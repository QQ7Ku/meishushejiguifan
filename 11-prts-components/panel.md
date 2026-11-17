# 面板 Panel

> 来源：https://mooncellwiki.github.io/prts-design/components/panel
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

页面上一块有名字的区域（游戏内 announce panel）：标题栏是表面 2 底 + 左侧 4px 主色条，标题后可跟 Bender 大写英文；正文 16px 内距。首页的「今日信息」「新增关卡」「新增家具」都是它。与[卡片](/prts-design/components/card)的分工：卡片是一组里的一件，面板是一块区域——同一时刻的信息放进同一张面板，不拆成几个框。

## 基础 · 标题栏附加 [​](#基础-·-标题栏附加)

`#header-extra` 放在标题栏右侧（日期标签、按钮）。面板是页面大纲里的一节时用 `title-tag="h3"` 这类真正的标题元素。

[Storybook ↗](/prts-design/storybook/?path=/story/components-panel--basic)

vue

```
<script setup lang="ts">
import AkTag from "../../Tag/AkTag.vue";
import AkPanel from "../AkPanel.vue";

const stages = [
  { code: "TO-EX-1", name: "电影防沉迷" },
  { code: "TO-EX-2", name: "邮包流水线" },
  { code: "TO-EX-3", name: "地下捉迷藏" },
  { code: "TO-EX-4", name: "处处是一撮毛" },
];
</script>

<template>
  <div>
    <AkPanel title="今日信息" title-tag="h3">
      <template #header-extra><AkTag size="sm" variant="accent-soft">周五</AkTag></template>
      <p class="ak-fs-sm ak-m-0">面板：标题栏左侧色条 + 表面 2 底色。整块是一张面板——物资筹备、芯片搜索、时钟与周常倒计时都在里面，不各立一个框。</p>
    </AkPanel>
    <AkPanel title="新增关卡" title-tag="h3">
      <div class="ak-overline ak-mb-2">SideStory「直到大地变成一颗酸橙」</div>
      <ul class="ak-m-0">
        <li v-for="s in stages" :key="s.code"><a href="#">{{ s.code }} {{ s.name }}</a></li>
      </ul>
    </AkPanel>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 反转 · 可折叠 [​](#反转-·-可折叠)

`inverse` 标题栏黑白反转（亮色下黑底白字、暗色下白底黑字），给公告这类要跳出来的区域。`en` 是标题后的英文（灰、大写、字距），也只给这种单独一块的面板；并排的一组面板只写中文（见[字体排印 · 双语标题](/prts-design/foundations/typography#双语标题)）。`collapsible` 可折叠：点标题栏收起 / 展开，右端出现开合记号（收起 ＋ / 展开 −）；展开状态是 `v-model`（默认展开），外部也能控制。标题栏里的链接 / 按钮自己处理点击，不会顺带折叠。

[Storybook ↗](/prts-design/storybook/?path=/story/components-panel--collapsible)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkTag from "../../Tag/AkTag.vue";
import AkPanel from "../AkPanel.vue";

const open = ref(false);
/** 陈的潜能提升（gamedata 2.7.61） */
const pots = [
  { lv: 2, text: "部署费用 -1" },
  { lv: 3, text: "再部署时间 -4 秒" },
  { lv: 4, text: "攻击力 +23" },
  { lv: 5, text: "第二天赋效果增强" },
  { lv: 6, text: "部署费用 -1" },
];
</script>

<template>
  <div>
    <AkPanel title="公告" en="Notice" inverse collapsible>
      <p class="ak-fs-sm ak-m-0">反转标题栏 + 可折叠：点标题栏收起；聚焦标题后 Enter / Space 也行。</p>
    </AkPanel>
    <AkPanel v-model="open" title="潜能提升" collapsible title-tag="h3">
      <template #header-extra><AkTag size="sm" variant="outline">5 项</AkTag></template>
      <ul class="ak-m-0 ak-fs-sm">
        <li v-for="p in pots" :key="p.lv">潜能 {{ p.lv }}：{{ p.text }}</li>
      </ul>
    </AkPanel>
    <div class="ak-flex ak-gap-3 ak-items-center">
      <AkButton size="sm" @click="open = !open">{{ open ? "收起潜能提升" : "展开潜能提升" }}</AkButton>
      <span class="ak-fs-sm ak-fg-muted">v-model = {{ open }}</span>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘 [​](#键盘)

可折叠时标题文字是 `role="button"`（放在标题元素**里面**，`h3` 的大纲语义不丢，同 [WAI-ARIA Accordion](https://www.w3.org/WAI/ARIA/apg/patterns/accordion/)），带 `aria-expanded` 与指向正文的 `aria-controls`。

| 键 | 行为 |
| --- | --- |
| `Tab` | 聚焦标题 |
| `Enter` / `Space` | 收起 / 展开 |

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | — | 标题（也可用 #header 插槽） |
| `en` | `string` | — | 标题后的英文（Bender 大写灰字：「Notice」）；并排的一组面板不写 |
| `titleTag` | `"div"``"h2"``"h3"``"h4"``"h5"``"h6"` | `"div"` | 标题元素：默认 div；面板是页面大纲里的一节时用 h2–h6（首页「新增关卡」是 h3） |
| `inverse` | `boolean` | `false` | 标题栏反转（亮色下黑底白字、暗色下白底黑字，同 .ak-inverse）：公告这类要跳出来的区域 |
| `collapsible` | `boolean` | `false` | 可折叠：点标题栏收起 / 展开正文（右端出现开合记号：收起 ＋ / 展开 −）；需要有标题 |
| `modelValue` | `boolean` | `true` | 是否展开（collapsible 时有效）；默认展开 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 正文 |
| `header` | 标题内容（代替 title） |
| `header-extra` | 标题栏右侧（标签、日期、按钮） |

## CSS 实现 [​](#css-实现)

CSS 实现只负责外观：折叠状态是 `.is-collapsed`，模板输出的纯 CSS 面板由皮肤脚本（document 级委托，点 `.ak-panel--collapsible > .ak-panel__head` 就翻）切换。`AkPanel` 与这段脚本共存：点击派发完后再落 `v-model`，并把 class 压回与状态一致。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
