# 时间线 Timeline

> 来源：https://mooncellwiki.github.io/prts-design/components/timeline
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

左侧一条 2px 竖线 + 菱形节点的时间序列：版本记录、活动排期、剧情节点。节点空心 = 未到 / 普通，主色实心 = 已发生（`done`）或当前（`active`）。时间用 Bender 小字压在标题上方。

## 基本 

写法同 Naive UI 的 `n-timeline`：`AkTimeline` 里放若干 `AkTimelineItem`，`time` / `title` / `content` 三段文字，`status` 定节点。

[Storybook ↗](/prts-design/storybook/?path=/story/components-timeline--basic)

vue

```
<script setup lang="ts">
import AkTimeline from "../AkTimeline.vue";
import AkTimelineItem from "../AkTimelineItem.vue";
</script>

<template>
  <AkTimeline>
    <AkTimelineItem time="2019.04.30" title="公开测试开启" content="明日方舟正式上线。" status="done" />
    <AkTimelineItem time="2026.08.01" title="2.7.61 版本" content="当前数据版本。" status="active" />
    <AkTimelineItem time="TBA" title="下一版本" />
  </AkTimeline>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 标题插槽 

标题要带标签 / 链接时用 `#header` 插槽，说明写在默认插槽里（代替 `content`）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-timeline--slots)

vue

```
<script setup lang="ts">
import AkTag from "../../Tag/AkTag.vue";
import AkTimeline from "../AkTimeline.vue";
import AkTimelineItem from "../AkTimelineItem.vue";

const events = [
  { time: "2026.08.01 16:00 — 08.15 03:59", title: "怒号光明（复刻）", type: "Side Story", status: "done" },
  { time: "2026.08.08 16:00 — 09.07 03:59", title: "奇象巡展", type: "限时活动", status: "active" },
  { time: "2026.08.19 10:00 — 09.02 03:59", title: "中坚甄选 14", type: "寻访", status: "active" },
] as const;
</script>

<template>
  <AkTimeline>
    <AkTimelineItem v-for="e in events" :key="e.title" :time="e.time" :status="e.status">
      <template #header>
        {{ e.title }} <AkTag size="sm" :variant="e.status === 'active' ? 'accent-soft' : 'default'">{{ e.type }}</AkTag>
      </template>
    </AkTimelineItem>
    <AkTimelineItem time="TBA" title="下一期活动">敬请期待，往期见<a href="#">活动一览</a>。</AkTimelineItem>
  </AkTimeline>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkTimeline 

只有默认插槽（若干 `AkTimelineItem`），输出 `<ul class="ak-timeline">`。

### AkTimelineItem 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `time` | `string` | — | 时间（Bender 小字，在标题上方） |
| `title` | `string` | — | 标题 |
| `content` | `string` | — | 说明文字（也可以写在默认插槽里） |
| `status` | `"done"``"wait"``"active"` | `"wait"` | 节点状态：wait 空心菱形（默认）· active 当前 · done 已发生（后两者都是主色实心） |

| 插槽 | 说明 |
| --- | --- |
| `header` | 标题（代替 title） |
| `default` | 说明（代替 content） |

## CSS 实现 

`<ul class="ak-timeline">` 直接写在正文（`.mw-parser-output`）里就对，不用标 `ak-not-prose`：正文列表的缩进、段距与项目符号（`ul > li::before`）由时间线自己的规则压住（各带一个 `.mw-parser-output` 前缀的同义选择器），菱形节点不受影响；说明里的链接照常是正文链接。

html

```
<ul class="ak-timeline">
  <li class="is-done"><span class="ak-timeline__date">2019.04.30</span><div class="ak-timeline__title">公开测试开启</div><div class="ak-timeline__desc">明日方舟正式上线，见<a href="#">版本记录</a>。</div></li>
  <li class="is-active"><span class="ak-timeline__date">2026.08.01</span><div class="ak-timeline__title">2.7.61 版本</div><div class="ak-timeline__desc">当前数据版本。</div></li>
  <li><span class="ak-timeline__date">TBA</span><div class="ak-timeline__title">下一版本</div></li>
</ul>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
