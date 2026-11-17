# 大标题横幅 Hero

> 来源：https://mooncellwiki.github.io/prts-design/arknights/hero
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

官网风格的页头横幅：黑底，青色 overline，下面是展示字大号的拉丁标题（`subtitle` 作中文副标）、青色横条和说明文字，右侧是一块竖直的青色色面，叠了渐隐的网点。两套主题下都是黑底白字。

## 大标题横幅 [​](#大标题横幅)

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-hero--basic)

vue

```
<script setup lang="ts">
import AkHero from "../AkHero.vue";
</script>

<template>
  <AkHero
    eyebrow="PRTS.WIKI · NEW SKIN DESIGN SYSTEM"
    title="PRTS Design"
    subtitle="明日方舟网页设计系统"
    description="以明日方舟官网与游戏内 UI 为视觉母体，按令牌—组件—模式分层，为 prts.wiki 的 MediaWiki 皮肤定义色彩、字体、间距、装饰语言与组件库。所有颜色均可追溯至官网 CSS、游戏解包精灵或 gamedata。"
  />
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 带按钮 · 无色面 [​](#带按钮-·-无色面)

说明下面的内容（按钮等）放进默认插槽。`:side="false"` 去掉右侧色面，`:bar="false"` 去掉青色横条。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-hero--actions)

vue

```
<script setup lang="ts">
import AkButton from "../../Button/AkButton.vue";
import AkHero from "../AkHero.vue";
</script>

<template>
  <AkHero
    eyebrow="生息演算 · 七周年庆典"
    title="Reclamation Algorithm"
    subtitle="重启锚点"
    description="生息演算「重启锚点」随七周年庆典开放（2026.05.01 10:00）。"
    :side="false"
  >
    <div class="ak-flex ak-wrap ak-gap-2 ak-mt-4">
      <AkButton variant="primary" href="#">活动页面</AkButton>
      <AkButton href="#">生息演算</AkButton>
    </div>
  </AkHero>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 和首页轮播的关系 [​](#和首页轮播的关系)

[首页设计稿](/prts-design/patterns/home)顶部的活动轮播（暗色黑块 / 亮色白面板 = 幻灯片 + 左右箭头 + 指示条 / 进度 + 暂停）不是这个组件。它是首页自己的结构：类名是 `.mp-hero*`，样式写在页面的 TemplateStyles 里，只有 `MediaWiki:首页` 这一处用，所以没有进 `src/`，也就没有对应的 Vue 组件。幻灯片里用到的倒计时、标签、按钮都是现成的组件（[倒计时](/prts-design/arknights/event)、[标签](/prts-design/components/tag)、[按钮](/prts-design/components/button/)）。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title`必填 | `string` | — | 大标题（展示字、全大写），拉丁字母最好看：PRTS / RHODES ISLAND |
| `subtitle` | `string` | — | 标题下的中文副标（正文字体、半号） |
| `eyebrow` | `string` | — | 标题上方的青色小字（PRTS.WIKI · NEW SKIN DESIGN SYSTEM） |
| `description` | `string` | — | 说明文字（最宽 60 字，70% 白） |
| `bar` | `boolean` | `true` | 标题下的青色横条 |
| `side` | `boolean` | `true` | 右侧竖直青色色面 |

| 插槽 | 说明 |
| --- | --- |
| `title` | 标题（代替 title / subtitle）；中文副标放 <span class="cn"> |
| `description` | 说明（代替 description），放在 <p> 里——只放行内内容 |
| `default` | 说明下面的内容（按钮等） |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
