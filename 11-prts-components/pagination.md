# 分页 Pagination

> 来源：https://mooncellwiki.github.io/prts-design/components/pagination
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

`‹ 1 2 3 4 5 … 24 ›`：首页、尾页常驻，当前页居中，两边放不下的收成省略号；当前页主色实底，到头的 ‹ / › 淡出。页码用等宽数字，翻页时宽度不跳。

## 基本 

写法同 Naive UI 的 `n-pagination`：`v-model` 是当前页（从 1 数），`page-count` 给总页数。

[Storybook ↗](/prts-design/storybook/?path=/story/components-pagination--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkPagination from "../AkPagination.vue";

const page = ref(1);
const middle = ref(12);
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkPagination v-model="page" :page-count="24" />
      <span class="ak-fs-sm ak-fg-muted">第 {{ page }} / 24 页</span>
    </div>
    <AkPagination v-model="middle" :page-count="24" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 条数 · 页码格数 · 禁用 

也可以给总条数 `item-count` + 每页条数 `page-size`，页数自己算。`page-slot` 是最多显示几格（含首尾页和省略号，同 Naive，最少 5）：每格约 50px 宽，默认 7 格约 450px，手机上放不下时调到 5。省略号只是提示，不可点。

[Storybook ↗](/prts-design/storybook/?path=/story/components-pagination--options)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkPagination from "../AkPagination.vue";

const a = ref(1);
const b = ref(8);
const c = ref(3);
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkPagination v-model="a" :item-count="236" :page-size="20" />
      <span class="ak-fs-sm ak-fg-muted">共 236 条，每页 20 条</span>
    </div>
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkPagination v-model="b" :page-count="24" :page-slot="5" />
      <span class="ak-fs-sm ak-fg-muted">page-slot = 5（窄处）</span>
    </div>
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkPagination v-model="c" :page-count="5" disabled />
      <span class="ak-fs-sm ak-fg-muted">禁用</span>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 链接形态 

默认页码是页内切换的 `<button>`，只更新 `v-model`。给 `page-href`（页码 → 地址）就渲染成 `<a href>`，点了照常跳转——MW 的列表页（`?offset=`）、能直接打开 / 分享的地址用它。

[Storybook ↗](/prts-design/storybook/?path=/story/components-pagination--links)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkPagination from "../AkPagination.vue";

const page = ref(3);
/** MW 的列表页按 offset 翻页；示例里用 # 地址，点了不会真的跳走 */
const href = (n: number) => `#offset=${(n - 1) * 50}`;
</script>

<template>
  <AkPagination v-model="page" :page-count="9" :page-href="href" label="页面列表分页" />
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 外层 `<nav aria-label="分页">` 地标；一页有两组分页时用 `label` 区分。
- 当前页 `aria-current="page"`；页码的读屏名是「第 N 页」，‹ / › 是「上一页」「下一页」。
- 到头的 ‹ / ›：按钮用 `disabled`，链接去掉 `href` 并标 `aria-disabled`。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `pageCount` | `number` | — | 总页数；不写时由 itemCount ÷ pageSize 算出 |
| `itemCount` | `number` | — | 总条数（配合 pageSize 算页数） |
| `pageSize` | `number` | `10` | 每页条数（只在用 itemCount 算页数时有用） |
| `pageSlot` | `number` | `7` | 最多显示几格页码（含首页、尾页和省略号，同 Naive 的 page-slot，最少 5）；每格约 50px，窄处调小 |
| `disabled` | `boolean` | `false` | 整组禁用 |
| `pageHref` | `((page: number) => string)` | — | 页码链接：给了就渲染成 <a href="pageHref(n)">，点击照常跳转；不给就是 <button>，只更新 v-model |
| `label` | `string` | `"分页"` | 导航地标的可访问名（nav 的 aria-label） |
| `modelValue` | `number` | `1` | 当前页（从 1 数） |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: number]` |  |

| 插槽 | 说明 |
| --- | --- |
| `prev` | 「上一页」按钮的内容（默认 ‹） |
| `next` | 「下一页」按钮的内容（默认 ›） |

## CSS 实现 

模板输出链接（与 Vue 版的链接形态结构相同）；当前页 `.is-active`、到头的箭头 `.is-disabled`，不是链接的格子用 `.ak-pagination__item`。

html

```
<nav class="ak-pagination" aria-label="分页">
  <a class="is-disabled" aria-disabled="true" aria-label="上一页">‹</a>
  <a href="#" class="is-active" aria-current="page">1</a>
  <a href="#">2</a>
  <a href="#">3</a>
  <span class="ak-pagination__ellipsis">…</span>
  <a href="#">24</a>
  <a href="#" aria-label="下一页">›</a>
</nav>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
