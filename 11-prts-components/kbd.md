# 键帽 Kbd

> 来源：https://mooncellwiki.github.io/prts-design/components/kbd
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

快捷键提示：直角 1px 边、底边略重一点（`inset 0 -1px`）、等宽 10px 粗体，18px 高。用在面板页脚的键位说明、触发器右侧、正文里「按 `/` 搜索」这类行内说明。组合键写成几枚键帽，中间的 `+` 写在外面。

## 快捷键提示 [​](#快捷键提示)

[Storybook ↗](/prts-design/storybook/?path=/story/components-kbd--basic)

vue

```
<script setup lang="ts">
import AkKbd from "../AkKbd.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3 ak-fs-sm">
    <span>按 <AkKbd>/</AkKbd> 或 <AkKbd>Ctrl</AkKbd> + <AkKbd>K</AkKbd> 打开搜索</span>
    <span class="ak-flex ak-wrap ak-gap-4 ak-fg-muted ak-fs-xs">
      <span><AkKbd>↑</AkKbd> <AkKbd>↓</AkKbd> 选择</span>
      <span><AkKbd>↵</AkKbd> 打开</span>
      <span><AkKbd>⇧</AkKbd> <AkKbd>↵</AkKbd> 全文搜索</span>
      <span><AkKbd>Esc</AkKbd> 关闭</span>
    </span>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

正文里编辑直接写的 `<kbd>`（没有 class）由 [MediaWiki 内容样式](/prts-design/content/typography) 换肤，长得稍大、跟随正文字号；`.ak-kbd` 是界面里用的固定小号版。页眉 / 搜索面板里的键帽另有深色底的覆盖（皮肤骨架负责）。

## Vue API [​](#vue-api)

`AkKbd` 只有默认插槽（键名：`Ctrl`、`K`、`Esc`、`↵`、`/` …），输出 `<kbd class="ak-kbd">`。

## CSS 实现 [​](#css-实现)

html

```
<kbd class="ak-kbd">Ctrl</kbd> + <kbd class="ak-kbd">K</kbd>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
