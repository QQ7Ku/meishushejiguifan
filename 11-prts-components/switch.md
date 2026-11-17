# 开关 Switch

> 来源：https://mooncellwiki.github.io/prts-design/components/switch
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

方形开关（同 Naive UI 的 `n-switch`）：40 × 22 的直角轨道，开 = 游戏内 `toggle_on` 蓝 `#0075A9`、白色方块滑到右边。给**立即生效**的二元设置（显示潜能加成、信赖满）；要提交后才生效的选项用[复选框](/prts-design/components/checkbox)。

## 基本 

`v-model` 是布尔值，默认插槽是开关的名称。`size="sm"` 是 32 × 18（标题栏旁、表头里，= 首页「演示：特别开放周」那枚）。禁用时轨道半透明、文字降到禁用色。

[Storybook ↗](/prts-design/storybook/?path=/story/components-switch--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkSwitch from "../AkSwitch.vue";

const potential = ref(true);
const trust = ref(false);
const demoWeek = ref(false);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
    <AkSwitch v-model="potential">显示潜能加成</AkSwitch>
    <AkSwitch v-model="trust">信赖满</AkSwitch>
    <AkSwitch v-model="demoWeek" size="sm">演示：特别开放周</AkSwitch>
    <AkSwitch :model-value="true" disabled>已开启（禁用）</AkSwitch>
    <AkSwitch disabled>已关闭（禁用）</AkSwitch>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 状态文字 

`#checked` / `#unchecked`（同 Naive）是名称后面、随开关变化的状态文字。**名称不随状态变**（它是可访问名），状态文字只给眼睛看（`aria-hidden`）——读屏已经会念「开 / 关」。没有名称文字时写 `label`，或放在[表单字段](/prts-design/components/field)里用字段的标签。

[Storybook ↗](/prts-design/storybook/?path=/story/components-switch--state-text)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkSwitch from "../AkSwitch.vue";

const potential = ref(false);
const calc = ref(true);
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkSwitch v-model="potential">
      潜能加成
      <template #checked>按满潜能计算</template>
      <template #unchecked>按 1 潜能计算</template>
    </AkSwitch>
    <AkSwitch v-model="calc" label="算法标记">
      <template #checked>显示加算 / 乘算标记</template>
      <template #unchecked>不显示</template>
    </AkSwitch>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 原生 `<input type="checkbox" role="switch">` 包在 `<label>` 里：`Space` 切换，读屏念「名称，开关，开」。
- 焦点只在键盘操作时亮：轨道外一圈淡青环。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `disabled` | `boolean` | `false` | 禁用 |
| `size` | `"md"``"sm"` | `"md"` | 尺寸：sm 32 × 18（标题栏旁、表头里）· md 40 × 22 |
| `label` | `string` | — | 可访问名（aria-label）：没有默认插槽的文字、也不在带标签的 AkField 里时必填 |
| `modelValue` | `boolean` | `false` | 是否打开 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 开关的名称（「显示潜能加成」）——就是它的可访问名，开 / 关时不变 |
| `checked` | 打开时名称后面的状态文字（同 Naive 的 #checked）；只给眼睛看，读屏已经念「开」 |
| `unchecked` | 关闭时名称后面的状态文字（同 Naive 的 #unchecked） |

## CSS 实现 

html

```
<label class="ak-switch"><input type="checkbox" role="switch" checked>显示潜能加成</label>
<label class="ak-switch ak-switch--sm"><input type="checkbox" role="switch"><span>演示：特别开放周</span></label>
```

轨道与滑块尺寸由私有变量 `--_w` / `--_h` 定，`--sm` 就是改了这两个。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
