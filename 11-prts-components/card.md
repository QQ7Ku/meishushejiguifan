# 卡片 Card

> 来源：https://mooncellwiki.github.io/prts-design/components/card
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

一件可并列 / 可点的东西：1px 边框、直角、表面色，不投影（只有悬停时浮起）。与[面板](/prts-design/components/panel)的分工：卡片是一组里的一件（活动、章节、技能选项），面板是页面上一块有名字的区域（「今日信息」「新增关卡」）。

写法同 Naive UI 的 `n-card`：`title`（或 `#header`）、`#header-extra`、`#footer`、`#cover`；外观用 `variant`、色条用 `accent`。

## 封面 · 眉题 · 页脚 

`cover` 放一张 16:9 裁切的封面；`eyebrow` 是标题上方的灰色 Bender 小字（不上青：不可点的装饰小标签，青色会读成链接）；`#footer` 是表面 2 底、上面一道线的一栏。`horizontal` 横排：封面在左占 40%、正文在右。

[Storybook ↗](/prts-design/storybook/?path=/story/components-card--cover)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkButton from "../../Button/AkButton.vue";
import AkTag from "../../Tag/AkTag.vue";
import AkCard from "../AkCard.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <AkCard :cover="asset('ui/default_breaking_news.png')" eyebrow="Chapter 08" title="怒号光明" hoverable>
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">卡片：1px 边框、直角、悬停时青色描边与上移 2px。</p>
      <template #footer>
        <span class="ak-fs-xs ak-fg-muted ak-grow">2026-08-01</span>
        <AkButton size="sm" variant="outline" href="#">查看</AkButton>
      </template>
    </AkCard>
    <AkCard :cover="asset('mainpage/banner-is6.jpg')" eyebrow="Integrated Strategies #6" title="沉沦者的黑流树海" horizontal>
      <p class="ak-fs-sm ak-fg-2 ak-mb-2">横排：封面在左占 40%，正文在右。</p>
      <div class="ak-tags"><AkTag size="sm" variant="outline">常驻</AkTag><AkTag size="sm" variant="accent-soft">集成战略</AkTag></div>
    </AkCard>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 标题栏 · 选中 

默认标题 / 眉题直接排在正文顶部；`segmented`（同 Naive）让标题单独成一栏、下面一道分隔线——写了 `#header-extra`（标签、按钮）时自动成栏。`selected` 是选中态：主色 1px 内描边 + 左上角三角角标（源自游戏内技能选中框）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-card--header)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkTag from "../../Tag/AkTag.vue";
import AkCard from "../AkCard.vue";

const picked = ref<"s2" | "s3">("s3");
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <div class="ak-grid ak-grid-2 ak-gap-4">
      <AkCard title="赤霄·拔刀" :selected="picked === 's2'">
        <template #header-extra><AkTag v-if="picked === 's2'" variant="accent-soft" size="sm">selected</AkTag></template>
        <p class="ak-fs-sm ak-m-0">技能 2 · 攻击回复 · 手动触发</p>
        <template #footer><AkButton size="sm" :variant="picked === 's2' ? 'primary' : 'default'" @click="picked = 's2'">选为专精技能</AkButton></template>
      </AkCard>
      <AkCard title="赤霄·绝影" :selected="picked === 's3'">
        <template #header-extra><AkTag v-if="picked === 's3'" variant="accent-soft" size="sm">selected</AkTag></template>
        <p class="ak-fs-sm ak-m-0">技能 3 · 攻击回复 · 手动触发</p>
        <template #footer><AkButton size="sm" :variant="picked === 's3' ? 'primary' : 'default'" @click="picked = 's3'">选为专精技能</AkButton></template>
      </AkCard>
    </div>
    <AkCard title="数据版本" eyebrow="Gamedata" segmented title-tag="h3">
      <p class="ak-fs-sm ak-m-0">本页数据取自游戏版本 2.7.61。标题栏独立成一栏、下面一道分隔线（<code>segmented</code>）。</p>
    </AkCard>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 外观 · 色条 

`variant`：默认表面色 + 1px 边 · `flat` 无边、表面 2 底（卡里再套一层）· `inset` 凹陷底。`accent="top"` 顶部 3px / `accent="left"` 左侧 4px 主色条，与 1px 细框用 border-image 直角拼接、不斜接。

[Storybook ↗](/prts-design/storybook/?path=/story/components-card--variants)

vue

```
<script setup lang="ts">
import AkCard from "../AkCard.vue";
</script>

<template>
  <div class="ak-grid ak-grid-2 ak-gap-4">
    <AkCard title="默认" eyebrow="Default">
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">表面色 + 1px 边。</p>
    </AkCard>
    <AkCard title="平铺" eyebrow="Flat" variant="flat">
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">无边、表面 2 底：卡片里再套一层时用。</p>
    </AkCard>
    <AkCard title="凹陷" eyebrow="Inset" variant="inset">
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">凹陷底：数据块、代码示例的托盘。</p>
    </AkCard>
    <AkCard title="黑白为体，青为用" eyebrow="01 · Monochrome first" accent="top">
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">顶部 3px 色条：与 1px 细框用 border-image 直角拼接，不斜接。</p>
    </AkCard>
    <AkCard title="左色条卡片" accent="left">
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">用于公告、提示、天赋条目。</p>
    </AkCard>
    <AkCard title="悬停 + 选中" accent="left" hoverable selected>
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">选中态 = 青色 1px 内描边 + 左上角三角角标（源自游戏内技能选中框）。</p>
    </AkCard>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 卡片网格 · 整卡链接 

`AkCardGrid` 自动填充，每列至少 260px（窄屏一列）。整张卡是链接时写 `href`：渲染成 `<a>` 并带 `ak-not-prose`（正文的链接色、悬停下划线、`:visited` 褪色不进来），配 `hoverable` 给悬停反馈；这时卡里不要再放别的链接。

[Storybook ↗](/prts-design/storybook/?path=/story/components-card--grid)

vue

```
<script setup lang="ts">
import AkCard from "../AkCard.vue";
import AkCardGrid from "../AkCardGrid.vue";

const sections = [
  { eyebrow: "01 · Foundations", title: "基础", desc: "理念、色彩令牌（出处可追溯）、字体排印、装饰语言。" },
  { eyebrow: "02 · Chrome", title: "皮肤骨架 · 页眉与主题接口", desc: "黑色「终端」顶栏的构件、搜索面板、活动主题只改哪几个变量。" },
  { eyebrow: "03 · MediaWiki", title: "MediaWiki 内容样式", desc: ".mw-parser-output 里的一切：链接、列表、wikitable、图库、目录。" },
  { eyebrow: "04 · Components", title: "通用组件", desc: "按钮、标签、卡片面板、标签页、消息弹层、进度统计、表单。" },
  { eyebrow: "05 · Arknights", title: "方舟组件", desc: "稀有度职业、干员卡、道具材料、技能天赋、模组语音档案。" },
  { eyebrow: "06 · Pattern", title: "干员页整页样例（陈）", desc: "PRTS 最复杂的页面用这套系统搭一遍，数据取自 gamedata 2.7.61。" },
];
</script>

<template>
  <AkCardGrid>
    <AkCard v-for="s in sections" :key="s.eyebrow" :eyebrow="s.eyebrow" :title="s.title" accent="top" hoverable href="#">
      <p class="ak-fs-sm ak-fg-2 ak-mb-0">{{ s.desc }}</p>
    </AkCard>
  </AkCardGrid>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkCard 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | — | 标题（也可用 #header 插槽） |
| `eyebrow` | `string` | — | 标题上方的灰色小字（Bender 大写：「Side Story」「01 · Foundations」） |
| `titleTag` | `"div"``"h2"``"h3"``"h4"``"h5"``"h6"` | `"div"` | 标题元素：默认 div；卡片是页面大纲里的一节时用 h2–h6 |
| `segmented` | `boolean` | `false` | 标题独立成栏、下面一道分隔线（同 Naive 的 segmented）；写了 #header-extra 时自动成栏 |
| `cover` | `string` | — | 封面图地址：16:9 裁切铺满顶部；horizontal 时在左边占 40%。要自定义用 #cover |
| `variant` | `"default"``"flat"``"inset"` | `"default"` | 外观：default 表面色 + 1px 边 · flat 无边、表面 2 底 · inset 凹陷底 |
| `accent` | `"top"``"left"` | — | 主色色条：top 顶部 3px · left 左侧 4px（公告 / 提示 / 天赋条目） |
| `hoverable` | `boolean` | `false` | 悬停时主色描边 + 投影 + 上移 2px（同 Naive 的 hoverable）；整卡可点时用 |
| `selected` | `boolean` | `false` | 选中：主色内描边 + 左上角三角角标（源自游戏内技能选中框） |
| `horizontal` | `boolean` | `false` | 横排：封面在左、正文在右 |
| `href` | `string` | — | 整张卡是一个链接：渲染成 <a>（带 ak-not-prose，正文链接色 / 下划线不进来）；这时正文里别再放链接 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 正文 |
| `header` | 标题内容（代替 title） |
| `header-extra` | 标题栏右侧（标签、按钮）；写了它标题自动成栏 |
| `footer` | 页脚（日期 + 操作）：表面 2 底、上面一道线 |
| `cover` | 自定义封面（代替 cover）；图片自己加 class="ak-card\_\_media" |

### AkCardGrid 

卡片网格：只有默认插槽，放若干 `AkCard`。

## CSS 实现 

`.ak-card__title` 放在 `.ak-card__header` 里是标题栏，放在 `.ak-card__body` 顶部（前面可以有 `.ak-card__eyebrow`）是无分隔的标题。整块是链接的卡片在最外层标 `ak-not-prose`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
