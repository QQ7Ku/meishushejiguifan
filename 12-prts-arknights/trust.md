# 信赖 Trust

> 来源：https://mooncellwiki.github.io/prts-design/arknights/trust
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

信赖读数：游戏的信赖图标 + 百分比（Bender 粗体）。图标走皮肤的素材接口（`base/skin-assets.css` 的 `--ak-trust-icon`，= prts.wiki 文件:图标\_信赖.png，白色线稿，亮色主题下随 `--ak-glyph-filter` 反相）；只加载组件层（`standalone.css`：站外页面、npm 包）时没有这个变量，`.ak-trust` 只有数字，不出图标也不占位——不画替代图形；要图标就在 `:root` 上设 `--ak-trust-icon`（指到 media.prts.wiki 的绝对地址）和 `--ak-trust-content: ""`。

## 信赖值 

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-trust--values)

vue

```
<script setup lang="ts">
import AkTrust from "../AkTrust.vue";
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkTrust :value="0" />
      <AkTrust :value="50" />
      <AkTrust :value="100" />
      <AkTrust :value="200" />
    </div>
    <!-- 模组 / 档案的解锁条件 -->
    <p class="ak-mb-0">解锁需求：<AkTrust :value="50" /></p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

图标是 `::before` 伪元素，读屏读不到：`AkTrust` 在数字前补一段 `.ak-sr-only` 的「信赖」（`label` 可改写，写空串去掉——比如表头已经写了「信赖」）。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value`必填 | `number` | — | 信赖（%）：0–200 |
| `label` | `string` | `"信赖"` | 读屏前缀：信赖图标是 ::before 伪元素、读屏读不到，默认补一段隐藏的「信赖」 |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
