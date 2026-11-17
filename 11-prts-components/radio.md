# 单选 Radio

> 来源：https://mooncellwiki.github.io/prts-design/components/radio
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

几个里选一个（同 Naive UI 的 `n-radio-group` + `n-radio` / `n-radio-button`）。`AkRadioGroup` 的 `v-model` 是选中项的 `value`；里面放 `AkRadio` 是一排圆形单选，放 `AkRadioButton` 是连成一排的分段控件。

## 单选组 

**圆是单选的通用语义**，也是整套系统里 UI 控件唯一的圆（选中 = 主色实底 + 圆点）。长相与裸 `<input type="radio">` 同一套（[裸控件规则](/prts-design/content/forms)），组给它们同一个 `name`，方向键在组内移动并选中是浏览器自己的。`vertical` 竖排，`size="sm"` 16px。

[Storybook ↗](/prts-design/storybook/?path=/story/components-radio--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkRadio from "../AkRadio.vue";
import AkRadioGroup from "../AkRadioGroup.vue";

const position = ref("any");
const mod = ref("x");
</script>

<template>
  <div class="ak-flex-col ak-gap-6">
    <AkRadioGroup v-model="position" label="部署位置">
      <AkRadio value="any">不限</AkRadio>
      <AkRadio value="melee">近战位</AkRadio>
      <AkRadio value="ranged">远程位</AkRadio>
    </AkRadioGroup>
    <AkRadioGroup v-model="mod" label="模组" vertical size="sm">
      <AkRadio value="">陈证章（原型）</AkRadio>
      <AkRadio value="x">罗德岛制式剑 · SWO-X</AkRadio>
      <AkRadio value="y" disabled>往昔时光 · SWO-Y（未解锁）</AkRadio>
    </AkRadioGroup>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

单独用的 `AkRadio` 与原生写法一样：几个 `<AkRadio v-model="picked" value="…">` 绑同一个变量、写同一个 `name`。

## 单选按钮组 

`AkRadioButton` 渲染成 `.ak-btn-group` 里的 `.ak-btn`，选中 `.is-active`（主色实底）：视图切换（列表 / 网格 / 表格）、排序方式这类**立即生效**的多选一。写法同 [按钮](/prts-design/components/button/)：`icon` 放图标，不写文字就是方形图标按钮（这时 `label` 必填）；`size` 写在组上（30 / 36 / 44）。

它和按钮组长得一样，但语义不同：[按钮组](/prts-design/components/button/) 是一排各自独立的动作，单选按钮组是一个值。精英阶段切换另有专门的阶段选择器，不用它。

[Storybook ↗](/prts-design/storybook/?path=/story/components-radio--buttons)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkRadioButton from "../AkRadioButton.vue";
import AkRadioGroup from "../AkRadioGroup.vue";

const view = ref("grid");
const sort = ref("rarity");
const layout = ref("list");
</script>

<template>
  <div class="ak-flex-col ak-gap-4 ak-items-start">
    <AkRadioGroup v-model="view" label="视图">
      <AkRadioButton value="list" icon="list">列表</AkRadioButton>
      <AkRadioButton value="grid" icon="grid">网格</AkRadioButton>
      <AkRadioButton value="table" icon="table">表格</AkRadioButton>
    </AkRadioGroup>
    <AkRadioGroup v-model="sort" label="排序" size="sm">
      <AkRadioButton value="rarity">按稀有度</AkRadioButton>
      <AkRadioButton value="time">按实装时间</AkRadioButton>
      <AkRadioButton value="name">按名称</AkRadioButton>
      <AkRadioButton value="trust" disabled>按信赖</AkRadioButton>
    </AkRadioGroup>
    <AkRadioGroup v-model="layout" label="视图（图标）">
      <AkRadioButton value="list" icon="list" label="列表" />
      <AkRadioButton value="grid" icon="grid" label="网格" />
      <AkRadioButton value="table" icon="table" label="表格" />
    </AkRadioGroup>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘 

按 [WAI-ARIA Radio Group](https://www.w3.org/WAI/ARIA/apg/patterns/radio/)：

| 键 | 行为 |
| --- | --- |
| `Tab` | 进入组时落在选中项上（没有选中项时落在第一个可用项），再按一次离开整组 |
| `→` / `↓` | 下一个并选中（跳过禁用、到头回到第一个） |
| `←` / `↑` | 上一个并选中 |
| `Space` | 选中当前项 |

普通单选这些是浏览器对同名 `<input type="radio">` 的原生行为；单选按钮组由 `AkRadioGroup` 实现（`role="radiogroup"` + 每项 `role="radio"` `aria-checked`，roving tabindex：只有选中项 `tabindex="0"`）。

## Vue API 

### AkRadioGroup 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name` | `string` | — | 原生 name（普通单选靠它互斥）；不写自动生成 |
| `disabled` | `boolean` | `false` | 整组禁用 |
| `size` | `"md"``"sm"``"lg"` | — | 尺寸：单选按钮 sm 30 · md 36 · lg 44（px 高）；普通单选只有 sm（16px 圆）与 md |
| `vertical` | `boolean` | `false` | 竖排（只对普通单选；单选按钮总是连成一排） |
| `label` | `string` | — | 读屏用的组名（aria-label）；放在带标签的 AkField 里时不用写 |
| `modelValue` | `RadioValue` | — | 选中项的 value |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: RadioValue | undefined]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkRadio，或若干 AkRadioButton |

### AkRadio 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value`必填 | `RadioValue` | — | 这一项的值：选中时成为 v-model（或所在 AkRadioGroup 的 v-model）的值 |
| `disabled` | `boolean` | `false` | 禁用 |
| `size` | `"md"``"sm"` | — | 尺寸：sm 16px 圆 · md 18px；不写时跟 AkRadioGroup（lg 对单选圆点不起作用） |
| `name` | `string` | — | 原生 name：同名的单选互斥、方向键在其间移动；放在 AkRadioGroup 里时由组统一给 |
| `label` | `string` | — | 可访问名（aria-label）：不写文字（默认插槽）时必填 |
| `modelValue` | `RadioValue` | — | 单独用时 = 选中项的值（同原生 <input type="radio" v-model>）；放在 AkRadioGroup 里时由组的 v-model 决定，不用绑 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: RadioValue | undefined]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字 |

### AkRadioButton 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value`必填 | `RadioValue` | — | 这一项的值：选中时成为 AkRadioGroup 的 v-model |
| `disabled` | `boolean` | `false` | 禁用：不可选，方向键跳过 |
| `icon` | `IconName` | — | 图标；不写文字（默认插槽）时是方形的图标按钮，这时 label 必填 |
| `label` | `string` | — | 可访问名（aria-label；图标按钮同时作 title）——只有图标时必填 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字；不写就是图标按钮 |

## CSS 实现 

html

```
<div class="ak-check-group" role="radiogroup" aria-label="部署位置">
  <label class="ak-check"><input type="radio" name="pos" checked>近战位</label>
  <label class="ak-check"><input type="radio" name="pos">远程位</label>
</div>
<div class="ak-btn-group" role="radiogroup" aria-label="视图">
  <button type="button" class="ak-btn" role="radio" aria-checked="false" tabindex="-1">列表</button>
  <button type="button" class="ak-btn is-active" role="radio" aria-checked="true" tabindex="0">网格</button>
</div>
```

单选按钮组的 `.ak-btn-group` / `.ak-btn.is-active` 在 `components/button.css`；CSS 实现只有外观，方向键要皮肤脚本或 Vue 组件补。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
