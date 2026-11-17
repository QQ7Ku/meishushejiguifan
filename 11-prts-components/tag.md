# 标签 Tag

> 来源：https://mooncellwiki.github.io/prts-design/components/tag
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

## 变体 [​](#变体)

状态色（info / success / warning / danger）走「淡底 + 色字」，两套主题各自成立；实底只有强调青、次强调黄、反转，以及两枚红——`new`（NEW 角标）和 `danger-solid`（BREAKING）。**红只表示危险与 NEW / BREAKING**。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tag--variants)

vue

```
<script setup lang="ts">
import AkTag from "../AkTag.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
    <AkTag>默认</AkTag>
    <AkTag variant="outline">描边</AkTag>
    <AkTag variant="accent">强调</AkTag>
    <AkTag variant="accent-soft">浅强调</AkTag>
    <AkTag variant="yellow">黄</AkTag>
    <AkTag variant="info">信息</AkTag>
    <AkTag variant="success">成功</AkTag>
    <AkTag variant="warning">警告</AkTag>
    <AkTag variant="danger">危险</AkTag>
    <AkTag variant="danger-solid">BREAKING</AkTag>
    <AkTag variant="inverse">反转</AkTag>
    <AkTag variant="new">NEW</AkTag>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 · 标签字 · 圆点 · 可移除 [​](#尺寸-·-标签字-·-圆点-·-可移除)

`caps` 换成 Bender 大写加字距，给版本号 / 状态码这类拉丁短词；`removable` 出一个移除按钮，点击触发 `remove`，读屏名是「移除 + 标签文字」。一组标签放进 `.ak-tags`（换行 + 6px 间距）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tag--details)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkTag from "../AkTag.vue";

const filters = ref(["近卫", "六星", "可公招"]);
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkTag size="sm">SM</AkTag>
      <AkTag>MD</AkTag>
      <AkTag size="lg">LG</AkTag>
      <AkTag variant="accent-soft" caps>v0.1 draft</AkTag>
      <AkTag variant="outline" caps size="sm">gamedata 2.7.61</AkTag>
      <AkTag variant="success" dot>已同步</AkTag>
    </div>
    <div class="ak-tags">
      <AkTag v-for="f in filters" :key="f" variant="outline" removable @remove="filters = filters.filter(x => x !== f)">{{ f }}</AkTag>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"default"``"info"``"success"``"warning"``"outline"``"accent"``"accent-soft"``"yellow"``"danger"``"danger-solid"``"new"``"inverse"` | `"default"` | 外观：状态色走 info / success / warning / danger（淡底色字）；new = 红底 NEW 角标；danger-solid = BREAKING 红；yellow 次强调 |
| `size` | `"md"``"sm"``"lg"` | `"md"` | 尺寸：sm 18 · md 22 · lg 28（px 高） |
| `caps` | `boolean` | `false` | 标签字（Bender 大写 + 字距），给版本号 / 状态码这类拉丁短词 |
| `dot` | `boolean` | `false` | 前置小方点 |
| `removable` | `boolean` | `false` | 显示移除按钮，点击触发 remove（读屏名是「移除 + 标签文字」） |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `remove` | `[]` | 点了移除按钮 |

| 插槽 | 说明 |
| --- | --- |
| `default` |  |

## CSS 实现 [​](#css-实现)

计数徽标、可交互的筛选芯片是另外两个组件：[徽标 Badge](/prts-design/components/badge)、[芯片 Chip](/prts-design/components/chip)。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
