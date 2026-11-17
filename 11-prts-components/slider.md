# 滑杆 Slider

> 来源：https://mooncellwiki.github.io/prts-design/components/slider
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

原生 `<input type="range">` 换肤（同 Naive UI 的 `n-slider`）：4px 轨道、16px 方形滑块（主色 + 表面色描边）。给等级、信赖这类在区间里拖、拖的时候就想看到结果的值；要精确输入用[数字输入](/prts-design/components/input-number)，两者常常并排。

## 基本 · 步长 · 禁用 [​](#基本-·-步长-·-禁用)

`v-model` 是数字，`min` / `max` / `step` 同原生。滑杆本身不显示数值，旁边放一个 `.ak-num`；`format-value` 给读屏念的文字（`aria-valuetext`，如「100%」）。禁用时半透明。

[Storybook ↗](/prts-design/storybook/?path=/story/components-slider--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkSlider from "../AkSlider.vue";

const level = ref(90);
const trust = ref(100);
const skill = ref(7);
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <div class="ak-flex ak-gap-3 ak-items-center">
      <span class="ak-overline ak-nowrap">等级</span>
      <AkSlider v-model="level" :min="1" :max="90" label="等级" />
      <span class="ak-num">{{ level }}</span>
    </div>
    <div class="ak-flex ak-gap-3 ak-items-center">
      <span class="ak-overline ak-nowrap">信赖</span>
      <AkSlider v-model="trust" :max="200" :step="10" label="信赖" :format-value="v => `${v}%`" />
      <span class="ak-num">{{ trust }}%</span>
    </div>
    <div class="ak-flex ak-gap-3 ak-items-center">
      <span class="ak-overline ak-nowrap">技能</span>
      <AkSlider v-model="skill" :min="1" :max="7" label="技能等级" disabled />
      <span class="ak-num">{{ skill }}</span>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

键盘与读屏都是原生的：`←` `→` `↑` `↓` 一步、`PageUp` / `PageDown` 大步、`Home` / `End` 到两端。键盘焦点是 2px 青色描边。旁边的「等级」文字不是 `<label>` 时写 `label`，或放在[表单字段](/prts-design/components/field)里。

CSS 没有刻度（Naive 的 `marks`）与拖动时的数值气泡，这里也不做；要刻度时在下面另排一行文字。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `min` | `number` | `0` | 最小值 |
| `max` | `number` | `100` | 最大值 |
| `step` | `number` | `1` | 步长 |
| `disabled` | `boolean` | `false` | 禁用 |
| `label` | `string` | — | 可访问名（aria-label）：不在 AkField 里、旁边也没有 <label> 时必填 |
| `formatValue` | `((value: number) => string)` | — | 读屏念的值（aria-valuetext），如 v => `信赖 ${v}%`；不写就念数字 |
| `modelValue` | `number` | — | 当前值 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: number | undefined]` |  |

## CSS 实现 [​](#css-实现)

html

```
<input class="ak-slider" type="range" min="1" max="90" value="90" aria-label="等级">
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
