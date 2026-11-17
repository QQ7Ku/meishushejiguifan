# 等级 Level

> 来源：https://mooncellwiki.github.io/prts-design/arknights/level
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

等级读数「LV 90」：横排，LV 小标坐在数字基线上；数字等宽（`tabular-nums`），E0 / E1 / E2 切换 50 → 80 → 90 时宽度不跳。

## 读数 · 灰块 

`variant="badge"` 是主题灰块（`surface-3` + 正文色，24px 高），与[阶段选择器](/prts-design/arknights/phase-tabs)、开关、[信赖](/prts-design/arknights/trust)在控制行里同一条线。它是**读数**不是按钮，所以不反白——反白在本系统里是「选中 / 可点」（阶段选择器的当前项、`contrast` 按钮），读数块挨着 E2 再反白就像又一个选中项。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-level--variants)

vue

```
<script setup lang="ts">
import AkLevel from "../AkLevel.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
    <AkLevel :value="90" />
    <AkLevel :value="90" variant="badge" />
    <AkLevel :value="1" variant="badge" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value`必填 | `number` | — | 等级 |
| `variant` | `"default"``"badge"` | `"default"` | 外观：default 纯读数 · badge 主题灰块（24px 高，与阶段选择器 / 开关 / 信赖同一条线；是读数不是按钮，不反白） |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
