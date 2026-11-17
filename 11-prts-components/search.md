# 搜索框 Search

> 来源：https://mooncellwiki.github.io/prts-design/components/search
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

左侧放大镜 + `<input type="search" class="ak-input">` + 右侧快捷键键帽——同页眉里没有 JS 时的那个搜索表单（有 JS 时页眉换成[搜索面板](/prts-design/chrome/search)的触发器）。给页内筛选、数据页的搜索栏；**站点级搜索外面包 `<form role="search">`**，页内筛选直接用。

## 快捷键 · 回车搜索 

`shortcut` 是单个按键（常用 `/`）：右侧显示键帽（有字时隐去），在页面别处按下就聚焦到输入框——正在别的输入框里打字时不抢，输入框同时带 `aria-keyshortcuts`。回车触发 `search` 事件（输入法选字时的回车不算）。`v-model` 是搜索词。

[Storybook ↗](/prts-design/storybook/?path=/story/components-search--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkKbd from "../../Kbd/AkKbd.vue";
import AkSearch from "../AkSearch.vue";

const q = ref("");
const last = ref("");
</script>

<template>
  <form role="search" class="ak-flex-col ak-gap-2 ak-max-narrow" @submit.prevent>
    <AkSearch v-model="q" placeholder="搜索 PRTS…" shortcut="/" @search="last = $event" />
    <span class="ak-fs-xs ak-fg-muted">在示例里别处按 <AkKbd>/</AkKbd> 聚焦；回车触发 search：{{ last || "—" }}</span>
  </form>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 · 在字段里 

高 30 / 36 / 44，与输入框同一刻度。不在带标签的[表单字段](/prts-design/components/field)里时，读屏名默认是「搜索」，`label` 可改。

[Storybook ↗](/prts-design/storybook/?path=/story/components-search--sizes)

vue

```
<script setup lang="ts">
import AkField from "../../Field/AkField.vue";
import AkSearch from "../AkSearch.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3 ak-max-narrow">
    <AkSearch size="sm" placeholder="筛选干员：名称 / 代号 / 拼音" label="筛选干员" />
    <AkSearch placeholder="搜索道具" label="搜索道具" />
    <AkSearch size="lg" placeholder="搜索关卡，如 1-7、CE-6" label="搜索关卡" />
    <AkField label="敌人" feedback="支持中文名与编号（如 B1）">
      <AkSearch placeholder="搜索敌人" />
    </AkField>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `placeholder` | `string` | — | 占位提示 |
| `size` | `"md"``"sm"``"lg"` | `"md"` | 尺寸：sm 30 · md 36 · lg 44（px 高） |
| `disabled` | `boolean` | `false` | 禁用 |
| `label` | `string` | — | 可访问名（aria-label）；在带标签的 AkField 里时由标签给，否则默认「搜索」 |
| `shortcut` | `string` | — | 聚焦快捷键（单个按键，如 "/"）：右侧显示键帽（有字时隐去），在页面上别处按下它就聚焦到这里（正在别的输入框里打字时不抢） |
| `modelValue` | `string` | — | 搜索词 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: string | undefined]` |  |
| `search` | `[query: string]` | 在输入框里按了回车 |

## CSS 实现 

html

```
<form class="ak-search" role="search">
  <svg class="ak-search__icon"><use href="#i-search"/></svg>
  <input class="ak-input" type="search" placeholder="搜索 PRTS…" aria-label="搜索">
  <span class="ak-search__kbd">/</span>
</form>
```

键帽 `.ak-search__kbd` 是[键帽](/prts-design/components/kbd) `.ak-kbd` 的绝对定位版。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
