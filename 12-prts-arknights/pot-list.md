# 潜能提升一览 Pot List

> 来源：https://mooncellwiki.github.io/prts-design/arknights/pot-list
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

干员页「潜能提升」一节，同现网 `{{潜能提升}}` 的五格表：潜能 2–6 各一格 = 潜能图标 + 小标 + 效果，格子按宽度自动折行（每格最窄 150px），1px 格线。

## 基本 

`AkPotList` 里放若干 `AkPot`：`level` 是潜能等级（给出小标「潜能 N」），效果写在默认插槽里（可带富文本 `.ak-rt-vup`）。`AkPotList` 的 `value` 是当前潜能——属性计算器选了潜能后传进来，等级不超过它的格子点亮（顶部青条 + 青色小标，`.is-on`）；单独一格要强制点亮 / 熄灭时写 `AkPot` 的 `active`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-pot-list--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkPot from "../AkPot.vue";
import AkPotList from "../AkPotList.vue";

const icon = (n: number) => asset(`potential/potential_${n - 1}.png`);
</script>

<template>
  <!-- 陈的潜能提升（现网 {{潜能提升}}）；属性计算器选了潜能 4：2–4 点亮 -->
  <AkPotList :value="4">
    <AkPot :level="2" :src="icon(2)">部署费用<span class="ak-rt-vup">-1</span></AkPot>
    <AkPot :level="3" :src="icon(3)">再部署时间<span class="ak-rt-vup">-4秒</span></AkPot>
    <AkPot :level="4" :src="icon(4)">攻击力<span class="ak-rt-vup">+23</span></AkPot>
    <AkPot :level="5" :src="icon(5)">第二天赋效果增强</AkPot>
    <AkPot :level="6" :src="icon(6)">部署费用<span class="ak-rt-vup">-1</span></AkPot>
  </AkPotList>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

整张表是一个列表（`role="list"` / `listitem`）。小标已经写了「潜能 N」，图标 `alt` 为空；点亮的格子另有一段读屏文本「（已生效）」，不只靠颜色。

## Vue API 

### AkPotList 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value` | `number` | — | 当前潜能 1–6：潜能等级不超过它的格子点亮（顶部青条 + 青色小标）；不写都不亮 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkPot |

### AkPot 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `level`必填 | `2``3``4``5``6` | — | 潜能等级 2–6：给出小标「潜能 N」，并与 AkPotList 的 value 比较决定是否点亮 |
| `src`必填 | `string` | — | 潜能图标地址（potential/potential\_{N−1}.png） |
| `title` | `string` | — | 小标：默认「潜能 N」 |
| `active` | `boolean` | `false` | 点亮（已生效）：不写时按 AkPotList 的 value 算 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 效果：「部署费用<span class="ak-rt-vup">-1</span>」这种，可带富文本 |

## CSS 实现 

图标是[潜能](/prts-design/arknights/potential)组件的 `.ak-potential`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
