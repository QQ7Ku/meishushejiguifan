# 面包屑 Breadcrumb

> 来源：https://mooncellwiki.github.io/prts-design/components/breadcrumb
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

当前页在层级里的位置：`首页 › 干员一览 › 陈`。上级是链接（悬停变主色），最后一项是当前页——正文色、加粗、`aria-current="page"`。分隔符是 CSS 画的斜角（`li + li::before`），不是字符，也不能换。

## 基本 

写法同 Naive UI 的 `n-breadcrumb`：`AkBreadcrumb` 里放若干 `AkBreadcrumbItem`，有 `href` 的渲染成链接。没有哪一项写 `current` 时，**最后一项自动当作当前页**；当前页也要是链接时给它写 `href`，`aria-current` 会落在链接上。要用路由链接时把 `RouterLink` 整个写进默认插槽、不写 `href`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-breadcrumb--basic)

vue

```
<script setup lang="ts">
import AkBreadcrumb from "../AkBreadcrumb.vue";
import AkBreadcrumbItem from "../AkBreadcrumbItem.vue";

const branch = [
  { text: "干员", href: "#干员" },
  { text: "近卫", href: "#近卫" },
  { text: "剑豪", href: "#剑豪" },
];
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkBreadcrumb>
      <AkBreadcrumbItem href="#首页">首页</AkBreadcrumbItem>
      <AkBreadcrumbItem href="#干员一览">干员一览</AkBreadcrumbItem>
      <AkBreadcrumbItem>陈</AkBreadcrumbItem>
    </AkBreadcrumb>
    <AkBreadcrumb label="职业分支">
      <AkBreadcrumbItem v-for="b in branch" :key="b.text" :href="b.href">{{ b.text }}</AkBreadcrumbItem>
      <AkBreadcrumbItem>陈</AkBreadcrumbItem>
    </AkBreadcrumb>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 输出 `<nav aria-label="面包屑">` 地标 + 有序列表 `<ol>`（同 [WAI-ARIA Breadcrumb](https://www.w3.org/WAI/ARIA/apg/patterns/breadcrumb/)）；一页有两条面包屑时用 `label` 区分。
- 斜角分隔符是伪元素，读屏不念。

## Vue API 

### AkBreadcrumb 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `label` | `string` | `"面包屑"` | 导航地标的可访问名（nav 的 aria-label） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkBreadcrumbItem，从上级到当前页 |

### AkBreadcrumbItem 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `href` | `string` | — | 链接地址；不写就是纯文字（当前页一般不写） |
| `current` | `boolean` | `false` | 当前页：aria-current="page" + 加粗。一项都没写时 AkBreadcrumb 自动把最后一项当作当前页 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字；要用路由链接（RouterLink）时整个写在这里、不写 href |

## CSS 实现 

模板直接输出列表即可。放在正文（`.mw-parser-output`）里时外层要标 `ak-not-prose`，否则正文的列表缩进会进来（Vue 版已标在 `<nav>` 上）。

html

```
<nav class="ak-not-prose" aria-label="面包屑">
  <ol class="ak-breadcrumb">
    <li><a href="#">干员</a></li>
    <li><a href="#">近卫</a></li>
    <li><a href="#">剑豪</a></li>
    <li aria-current="page">陈</li>
  </ol>
</nav>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
