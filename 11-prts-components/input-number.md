# 数字输入 InputNumber

> 来源：https://mooncellwiki.github.io/prts-design/components/input-number
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

常显 − / + 的步进器（同 Naive UI 的 `n-input-number`）：等级、信赖、潜能、材料数量这类有上下限的整数。裸 `<input type="number">` 的原生 ▲▼ 只在悬停 / 聚焦时出现（Chrome），要一直看得见按钮用它。输入框定宽 56px、数字等宽（改值不跳动）。

## 上下限 · 步长 

`v-model` 是数字（清空后是 `null`）。打字时是合法数字且在范围内就实时更新，否则等失焦 / 回车再落到范围内（超了收到上下限，不是数字退回原值）；到上下限时对应的按钮变灰。

[Storybook ↗](/prts-design/storybook/?path=/story/components-inputnumber--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkField from "../../Field/AkField.vue";
import AkInputNumber from "../AkInputNumber.vue";

const level = ref(90);
const trust = ref(100);
const potential = ref(1);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6">
    <AkField label="等级" feedback="精英二 1–90">
      <AkInputNumber v-model="level" :min="1" :max="90" />
    </AkField>
    <AkField label="信赖（%）" feedback="每步 10">
      <AkInputNumber v-model="trust" :min="0" :max="200" :step="10" />
    </AkField>
    <AkField label="潜能">
      <AkInputNumber v-model="potential" :min="1" :max="6" />
    </AkField>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 状态 

`:show-button="false"` 只留输入框；`readonly` 能看能复制不能改（按钮一并锁住）；`disabled`；`placeholder` 给「清空 = 不限」这类语义。

[Storybook ↗](/prts-design/storybook/?path=/story/components-inputnumber--states)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkInputNumber from "../AkInputNumber.vue";

const need = ref<number | null>(null);
const sanity = ref(135);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
    <AkInputNumber v-model="need" :min="0" placeholder="不限" label="需求数量" />
    <AkInputNumber v-model="sanity" :min="0" :max="135" :show-button="false" label="理智" />
    <AkInputNumber :model-value="3212" readonly label="库存（只读）" />
    <AkInputNumber :model-value="12" disabled label="数量（禁用）" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘 

输入框是 `role="spinbutton"`（`aria-valuenow` / `aria-valuemin` / `aria-valuemax`），按 [WAI-ARIA Spinbutton](https://www.w3.org/WAI/ARIA/apg/patterns/spinbutton/)：

| 键 | 行为 |
| --- | --- |
| `↑` / `↓` | 加 / 减一步 |
| `PageUp` / `PageDown` | 加 / 减十步 |
| `Home` / `End` | 到下限 / 上限（设了才接管，否则照常移动光标） |
| `Enter` | 确认输入 |

− / + 按钮不进 Tab 顺序（`tabindex="-1"`，键盘用方向键），但有读屏名「减少」/「增加」，触屏读屏照样能点。整数且下限 ≥ 0 时手机弹数字键盘（`inputmode="numeric"`），否则 `decimal`。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `min` | `number` | — | 最小值 |
| `max` | `number` | — | 最大值 |
| `step` | `number` | `1` | 步长：按钮 / 方向键每次加减多少 |
| `disabled` | `boolean` | `false` | 禁用 |
| `readonly` | `boolean` | `false` | 只读：值能看能复制，不能改 |
| `placeholder` | `string` | — | 清空时的占位提示 |
| `showButton` | `boolean` | `true` | 显示 − / + 按钮（同 Naive 的 show-button） |
| `label` | `string` | — | 可访问名（aria-label）：不在 AkField 里、旁边也没有 <label> 时必填 |
| `modelValue` | `number | null` | — | 当前值；输入框清空后是 null |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: number | null | undefined]` |  |

## CSS 实现 

html

```
<div class="ak-number"><button type="button" tabindex="-1" aria-label="减少">−</button><input value="12" role="spinbutton"><button type="button" tabindex="-1" aria-label="增加">+</button></div>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
