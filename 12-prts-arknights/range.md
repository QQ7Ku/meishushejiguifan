# 攻击范围 Range

> 来源：https://mooncellwiki.github.io/prts-design/arknights/range
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

同现网 `Widget:Range/*` 的画法：自身格 = 实心蓝方块，可攻击格 = 灰色 2px 空心框，不在范围内的格不画；格 22px + 间隙 4px（= 现网 26px 格距）。只铺范围的外接矩形——`1-1` 就是两格，不补空格撑成 3×3。

## 范围形状 [​](#范围形状)

`grids` 直接传游戏 `range_table` 的 `grids`（`{ row, col }`，相对自身，`col` 向前为正），也可以写成 `[row, col]`；自身 (0, 0) 写不写都行。组件算出外接矩形，逐格输出 `i.self` / `i.on` / 空 `<i>`，列数写进 `--cols`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-range--shapes)

vue

```
<script setup lang="ts">
import AkRange, { type RangeGrid } from "../AkRange.vue";

/** 行 r0..r1 × 列 c0..c1 整块 */
const block = (r0: number, r1: number, c0: number, c1: number) => {
  const g: [number, number][] = [];
  for (let r = r0; r <= r1; r++) for (let c = c0; c <= c1; c++) g.push([r, c]);
  return g;
};

const ranges: { title: string; grids: RangeGrid[] }[] = [
  // 陈：正前方一格（= range_table 的 grids 原样）
  { title: "近战 · 1-1", grids: [{ row: 0, col: 0 }, { row: 0, col: 1 }] },
  { title: "远程 · 3-3", grids: block(-1, 1, 0, 3) },
  { title: "阵法 · x-4", grids: block(-1, 1, -1, 1) },
  // 外接矩形里不在范围内的格不画
  { title: "空格不画", grids: [[-2, 0], [-1, -1], [-1, 0], [-1, 1], [0, -2], [0, -1], [0, 1], [0, 2], [1, -1], [1, 0], [1, 1], [2, 0]] },
];
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-start">
    <div v-for="r in ranges" :key="r.title">
      <div class="ak-overline ak-mb-2">{{ r.title }}</div>
      <AkRange :grids="r.grids" />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 [​](#尺寸)

`sm` 14px（技能卡侧槽 / 表格里）· 默认 22px · `lg` 28px。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-range--sizes)

vue

```
<script setup lang="ts">
import AkRange from "../AkRange.vue";

const sniper: [number, number][] = [];
for (let r = -1; r <= 1; r++) for (let c = 0; c <= 3; c++) sniper.push([r, c]);
</script>

<template>
  <div class="ak-flex ak-gap-6 ak-items-end">
    <AkRange :grids="sniper" size="sm" />
    <AkRange :grids="sniper" />
    <AkRange :grids="sniper" size="lg" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

整块是 `role="img"`，默认读作「攻击范围：自身之外 N 格」；有更贴切的说法（「正前方一格」）时用 `label` 改写。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `grids`必填 | `readonly RangeGrid[]` | — | 可攻击的格子（相对自身）：{ row, col }（直接传 range\_table 的 grids）或 [row, col]；col 向前为正，自身 (0, 0) 写不写都行 |
| `size` | `"md"``"sm"``"lg"` | `"md"` | 尺寸：sm 14 · md 22 · lg 28（px 格边）；sm 给技能卡侧槽 / 表格 |
| `label` | `string` | — | 可访问名（aria-label）；默认「攻击范围：自身之外 N 格」 |

## CSS 实现 [​](#css-实现)

颜色两个钩子：`--ak-range-self`（自身格）/ `--ak-range-cell`（攻击格描边）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
