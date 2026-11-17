# 消息 Message

> 来源：https://mooncellwiki.github.io/prts-design/components/message
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

界面 / 系统给读者的反馈：「数据来自哪个版本」「已同步」「本页含未实装内容」「删除不可撤销」。左侧 4px 等级色条 + 等级色淡底，标题行用等级色；与 [轻提示 Toast](/prts-design/components/toast) 同族（Toast 是会自己消失的浮层版）。

与 [正文提示框 Cbox](/prts-design/components/cbox) 的分工：Cbox 是**编辑写在正文里的内容**（= 现网 `Template:Cbox2`）；Message 是**界面**说的话——横幅、可关闭、操作结果。

## 变体 [​](#变体)

写法同 Naive UI 的 `n-alert`：`variant` 定等级色（info 默认 · success · warning · danger · neutral · accent），`title` 是加粗的标题行，图标按等级自动选，`icon` 可换、`show-icon` 可关。危险提示可叠 `stripes` 斜纹。

[Storybook ↗](/prts-design/storybook/?path=/story/components-message--variants)

vue

```
<script setup lang="ts">
import AkMessage from "../AkMessage.vue";
</script>

<template>
  <div>
    <AkMessage title="信息">数据来自游戏版本 2.7.61。</AkMessage>
    <AkMessage variant="success">已同步 Cargo 表。</AkMessage>
    <AkMessage variant="warning">本页含未实装内容。</AkMessage>
    <AkMessage variant="danger" title="危险区域" stripes>删除操作不可撤销。</AkMessage>
    <AkMessage variant="accent">新版皮肤试运行中：界面问题请到<a href="#">反馈页</a>留言。</AkMessage>
    <AkMessage variant="neutral" :show-icon="false" class="ak-fs-xs">
      这是<b>干员页整页样例</b>（信息结构 1:1 取自 prts.wiki 现网「陈」页面的 19 个章节，数据取自 gamedata 2.7.61）。
    </AkMessage>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可关闭 [​](#可关闭)

`closable` 在右侧放一枚 ✕：点了触发 `close` 并隐藏自己。不绑定 `v-model` 时关了就没了；绑定了可以再打开。标题要带链接时用 `#header` 插槽。

[Storybook ↗](/prts-design/storybook/?path=/story/components-message--closable)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkMessage from "../AkMessage.vue";

const show = ref(true);
</script>

<template>
  <div>
    <AkMessage v-model="show" title="数据版本" closable>本页数据取自游戏版本 2.7.61，与现网 wikitext 同步。</AkMessage>
    <AkButton v-if="!show" size="sm" @click="show = true">重新显示</AkButton>
    <AkMessage variant="warning" closable>
      <template #header>存在<a href="#">待合并的编辑</a></template>
      另一位编辑者在你打开本页之后保存过修改。
    </AkMessage>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 横幅 [​](#横幅)

`banner`：顶边 3px 色条、文字居中，给站点公告 / 页顶通知；危险横幅配 `stripes`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-message--banner)

vue

```
<script setup lang="ts">
import AkMessage from "../AkMessage.vue";
</script>

<template>
  <div>
    <AkMessage variant="accent" banner :show-icon="false">
      网页活动<b>「罗德岛录像带」</b>进行中 · 08.20 10:00 — 08.30 03:59
    </AkMessage>
    <AkMessage variant="danger" banner stripes>数据库维护中：09-28 04:00 – 06:00 暂停编辑，页面只读。</AkMessage>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- `warning` / `danger` 输出 `role="alert"`，其余 `role="status"`：动态插入（提交后出现的结果）时读屏会念，前者立即、后者礼貌播报。页面一加载就在的提示不会被重复念。
- 图标是装饰（`aria-hidden`），等级靠文字表达——不要只写一个图标或只靠颜色区分「成功 / 失败」。
- 关闭按钮读屏名「关闭」，有焦点环（`message.css` 补回了被 `all: unset` 清掉的 `:focus-visible` 描边）。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"info"``"success"``"warning"``"accent"``"danger"``"neutral"` | `"info"` | 外观（等级色）：info 蓝（默认）· success 绿 · warning 黄 · danger 红 · neutral 灰 · accent 主色 |
| `title` | `string` | — | 加粗的标题行（等级色）；要带链接 / 格式时用 #header 插槽 |
| `icon` | `IconName` | — | 图标；默认按 variant：success → check，warning / danger → warn，其余 → info |
| `showIcon` | `boolean` | `true` | 显示图标（同 Naive 的 show-icon） |
| `closable` | `boolean` | `false` | 右侧 ✕ 关闭按钮：点了隐藏自己（v-model 变 false）并触发 close |
| `banner` | `boolean` | `false` | 横幅：顶边 3px 色条、文字居中——站点公告 / 页顶通知 |
| `stripes` | `boolean` | `false` | 叠斜纹（危险横幅，配 variant="danger"） |
| `modelValue` | `boolean` | `true` | 是否显示；不绑定时自己管（关掉就没了），绑定了可以再打开 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |
| `close` | `[]` | 点了关闭按钮（之后自己隐藏） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 正文 |
| `header` | 标题行（代替 title） |
| `icon` | 图标（代替 icon；放进 20px 的图标位，颜色跟等级色） |

## CSS 实现 [​](#css-实现)

模板 / Lua 输出 `div.ak-message(.ak-message--success …)` > `svg.ak-message__icon` + `div.ak-message__body`（里面可有 `div.ak-message__title`）+ 可选 `button.ak-message__close`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
