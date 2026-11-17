# 数据表 Table

> 来源：https://mooncellwiki.github.io/prts-design/components/table
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

模板 / 小部件输出的数据表：1px 外框、表头 surface-2 底 + 小号大写 + 2px 粗底线、悬停行、数字列右对齐等宽。**编辑在正文里写的表格用 `.wikitable`**（皮肤另有一套，支持竖排行头），不用它。

## 基本 

Vue 版是 Naive UI `n-data-table` 的精简版 `AkDataTable`：`columns` 定义列（`key` 取 `row[key]`，`title` 表头，`num` 数字列），`data` 是行，`row-key` 给行标识。`striped` 斑马纹；`row-class-name` 给行加类名，`is-selected` 是淡主色底（当前阶段 / 选中行）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-table--basic)

vue

```
<script setup lang="ts">
import AkDataTable from "../AkDataTable.vue";
import type { DataTableColumn } from "../types";

interface Phase {
  phase: string;
  maxLevel: number;
  hp: number;
  atk: number;
  def: number;
}

const columns: DataTableColumn<Phase>[] = [
  { key: "phase", title: "阶段" },
  { key: "maxLevel", title: "等级上限" },
  { key: "hp", title: "生命", num: true },
  { key: "atk", title: "攻击", num: true },
  { key: "def", title: "防御", num: true },
];

/** 陈，各阶段满级（不含信赖 / 潜能加成） */
const data: Phase[] = [
  { phase: "精英零", maxLevel: 50, hp: 1684, atk: 361, def: 221 },
  { phase: "精英一", maxLevel: 80, hp: 2188, atk: 469, def: 288 },
  { phase: "精英二", maxLevel: 90, hp: 2880, atk: 610, def: 352 },
];
</script>

<template>
  <AkDataTable
    :columns="columns"
    :data="data"
    :row-key="r => r.phase"
    :row-class-name="r => (r.phase === '精英一' ? 'is-selected' : undefined)"
    striped
    label="陈 · 各阶段满级属性"
  />
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 排序 · 自定义单元格 

列写 `sorter: true`（按 `row[key]` 比较：数字比大小、文字按中文排序）或者给比较函数，表头就能点：升序 → 降序 → 原顺序。排序状态是 `v-model`（`{ key, order }`，`order` 取 `aria-sort` 的值 `ascending` / `descending`），当前排序列标 `aria-sort`，箭头由 CSS 画。

单元格要自定义时：SFC 里用 `#cell-<列 key>="{ row, index, value }"` 插槽；也可以给列写 `render(row, index)`（同 Naive）返回 vnode。插槽优先。

[Storybook ↗](/prts-design/storybook/?path=/story/components-table--sort)

vue

```
<script setup lang="ts">
import { h, ref } from "vue";

import AkDataTable from "../AkDataTable.vue";
import type { DataTableColumn, DataTableSort } from "../types";

interface Operator {
  name: string;
  branch: string;
  profession: string;
  rarity: number;
  hp: number;
  atk: number;
  def: number;
  faction: string;
}

const columns: DataTableColumn<Operator>[] = [
  { key: "name", title: "干员" },
  {
    key: "rarity",
    title: "稀有度",
    sorter: true,
    // render 函数：稀有度星标（.ak-rarity）
    render: r => h("span", { class: ["ak-rarity", `ak-rarity--r${r.rarity}`, "ak-rarity--sm"], role: "img", "aria-label": `${r.rarity} 星` }, Array.from({ length: r.rarity }, () => h("i"))),
  },
  { key: "profession", title: "职业" },
  { key: "hp", title: "生命", num: true, sorter: true },
  { key: "atk", title: "攻击", num: true, sorter: true },
  { key: "def", title: "防御", num: true, sorter: true },
  { key: "faction", title: "阵营" },
];

/** 精英二满级 */
const data: Operator[] = [
  { name: "陈", branch: "剑豪", profession: "近卫", rarity: 6, hp: 2880, atk: 610, def: 352, faction: "龙门近卫局" },
  { name: "银灰", branch: "领主", profession: "近卫", rarity: 6, hp: 2880, atk: 660, def: 352, faction: "喀兰贸易" },
  { name: "德克萨斯", branch: "尖兵", profession: "先锋", rarity: 5, hp: 2050, atk: 545, def: 371, faction: "企鹅物流" },
  { name: "桃金娘", branch: "执旗手", profession: "先锋", rarity: 4, hp: 1583, atk: 386, def: 223, faction: "—" },
];

const sort = ref<DataTableSort | null>({ key: "atk", order: "descending" });
</script>

<template>
  <div>
    <AkDataTable v-model="sort" :columns="columns" :data="data" :row-key="r => r.name" label="干员属性">
      <template #cell-name="{ row }">
        <a :href="`#${row.name}`">{{ row.name }}</a> <span class="ak-fs-xs ak-fg-muted">{{ row.branch }}</span>
      </template>
    </AkDataTable>
    <p class="ak-fs-sm ak-fg-muted ak-mt-2 ak-mb-0">v-model = {{ JSON.stringify(sort) }}</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 紧凑 · 空数据 

`compact` 收紧格内边距（4 / 8）。没有数据时整行显示 `#empty` 插槽（默认「暂无数据」）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-table--compact)

vue

```
<script setup lang="ts">
import AkDataTable from "../AkDataTable.vue";
import type { DataTableColumn } from "../types";

interface Stat {
  stat: string;
  e0l1: string;
  e0: string;
  e1: string;
  e2: string;
}

const columns: DataTableColumn<Stat>[] = [
  { key: "stat", title: "属性" },
  { key: "e0l1", title: "精英0 1级", num: true },
  { key: "e0", title: "精英0 满级", num: true },
  { key: "e1", title: "精英1 满级", num: true },
  { key: "e2", title: "精英2 满级", num: true },
];

/** 陈（不含信赖 / 潜能加成） */
const data: Stat[] = [
  { stat: "生命上限", e0l1: "1229", e0: "1684", e1: "2188", e2: "2880" },
  { stat: "攻击", e0l1: "249", e0: "361", e1: "469", e2: "610" },
  { stat: "防御", e0l1: "154", e0: "221", e1: "288", e2: "352" },
  { stat: "部署费用", e0l1: "19", e0: "19", e1: "21", e2: "23" },
  { stat: "攻击间隔", e0l1: "1.3s", e0: "1.3s", e1: "1.3s", e2: "1.3s" },
];
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <AkDataTable :columns="columns" :data="data" :row-key="r => r.stat" compact label="陈 · 属性" />
    <AkDataTable :columns="columns" :data="[]" compact label="空表">
      <template #empty>没有符合条件的数据</template>
    </AkDataTable>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 可排序列的表头里是一枚 `<button>`（[WAI-ARIA Sortable Table](https://www.w3.org/WAI/ARIA/apg/patterns/table/examples/sortable-table/)）：Tab 可达、Enter / 空格切换；长相就是表头文字本身。`aria-sort` 只标在当前排序的列上。
- 页面上没有标题说明这张表时，用 `label` 给表格一个可访问名。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `columns`必填 | `DataTableColumn<T>[]` | — | 列定义：key 取值字段 · title 表头 · num 数字列 · sorter 可排序 · render 自定义单元格 · className |
| `data`必填 | `T[]` | — | 行数据 |
| `rowKey` | `((row: T) => string | number)` | — | 行的唯一标识（同 Naive 的 row-key）；不写用原始下标 |
| `rowClassName` | `string | ((row: T, index: number) => string | undefined)` | — | 行的额外类名（同 Naive 的 row-class-name）：给当前行 / 选中行加 is-selected（淡主色底） |
| `striped` | `boolean` | `false` | 斑马纹 |
| `compact` | `boolean` | `false` | 紧凑：格内边距 4 / 8 |
| `label` | `string` | — | 表格的可访问名（aria-label）；上方已有标题说明这张表时可以不写 |
| `modelValue` | `DataTableSort | null` | `null` | 排序状态 { key, order: "ascending" | "descending" }，null = 原顺序；点可排序的表头依次切换 升序 → 降序 → 原顺序 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: DataTableSort | null]` |  |

| 插槽 | 说明 |
| --- | --- |
| `empty` | 没有数据时整行显示的内容（默认「暂无数据」） |

列定义 `DataTableColumn<T>`：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `key` | `string` | 列标识，也是取值字段（`row[key]`）；`#cell-<key>` 插槽按它找 |
| `title` | `string` | 表头文字 |
| `num` | `boolean` | 数字列：右对齐 + 等宽数字（`.num`） |
| `sorter` | `boolean | (a, b) => number` | 可排序：`true` 按 `row[key]` 比较，或给升序时的比较函数 |
| `render` | `(row, index) => VNodeChild` | 自定义单元格（同 Naive） |
| `className` | `string` | 这一列 `th` / `td` 的额外类名 |

## CSS 实现 

不需要排序时直接写 `<table class="ak-table">` 就行（Vue SFC 里也可以，不必用 `AkDataTable`）：`.ak-table--striped` 斑马纹、`.ak-table--compact` 紧凑、`th` / `td` 加 `.num` 是数字列、行加 `.is-selected` 高亮。`th[aria-sort="ascending|descending"]` 画 ↑ / ↓——模板输出静态排好序的表时照样可以标。

html

```
<table class="ak-table ak-table--striped">
  <thead>
    <tr><th aria-sort="descending">阶段</th><th>等级上限</th><th class="num">生命</th><th class="num">攻击</th><th class="num">防御</th></tr>
  </thead>
  <tbody>
    <tr><td>精英零</td><td>50</td><td class="num">1684</td><td class="num">361</td><td class="num">221</td></tr>
    <tr class="is-selected"><td>精英一</td><td>80</td><td class="num">2188</td><td class="num">469</td><td class="num">288</td></tr>
    <tr><td>精英二</td><td>90</td><td class="num">2880</td><td class="num">610</td><td class="num">352</td></tr>
  </tbody>
</table>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
