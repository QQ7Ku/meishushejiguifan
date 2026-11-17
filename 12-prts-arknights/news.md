# 新闻横幅 News

> 来源：https://mooncellwiki.github.io/prts-design/arknights/news
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

游戏主界面上那条 Breaking news 横幅：左边是矩形红块（不斜切），右边是反色底上的一行正文，超长时用省略号截断。正文里可以放链接和加粗。

这种横幅只装得下一条。如果同时有两三个活动要列（例如首页「进行中的网页活动」），应该改用列表，一条一行，不要把几条挤进一句话里轮流滚动。

## 横幅 [​](#横幅)

红块里的字默认是 Breaking News，可以用 `title` 或 `#title` 插槽改写。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-news--basic)

vue

```
<script setup lang="ts">
import AkNews from "../AkNews.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkNews>「怒号光明」复刻开启 · 新干员「维什戴尔」限时寻访 · <a href="#">查看活动一览</a></AkNews>
    <AkNews title="Notice">
      SideStory「直到大地变成一颗酸橙」已开放：新增关卡 TO-EX-1 至 TO-EX-8、TO-S-1 至 TO-S-4 与 TO-MO-1「大涌泉镇盛宴」，<b>关卡页、掉落与敌人资料已同步更新</b>
    </AkNews>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | `"Breaking News"` | 红块里的字（Bender 大写） |

| 插槽 | 说明 |
| --- | --- |
| `title` | 红块内容（代替 title） |
| `default` | 正文：一行，可含链接 / 加粗；超长省略号截断 |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
