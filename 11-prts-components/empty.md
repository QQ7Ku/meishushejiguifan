# 空状态 Empty

> 来源：https://mooncellwiki.github.io/prts-design/components/empty
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

没有数据、筛选没有结果、页面不存在：虚线框、表面 2 底，居中一枚 48px 半透明线稿图标 + 粗体标题 + 小字说明 + 可选的操作。写法同 Naive UI 的 `n-empty`：`description`（或默认插槽）、`#icon`、`#extra`。

## 基础 · 操作 

标题默认「暂无数据」；`icon` 换图标（`false` 不要图标）；说明里要放链接 / 加粗时写在默认插槽；`#extra` 放「清除筛选」这类让用户走出空状态的操作。

[Storybook ↗](/prts-design/storybook/?path=/story/components-empty--basic)

vue

```
<script setup lang="ts">
import AkButton from "../../Button/AkButton.vue";
import AkEmpty from "../AkEmpty.vue";
</script>

<template>
  <div class="ak-grid ak-grid-2 ak-gap-4">
    <AkEmpty description="尝试更换筛选条件" />
    <AkEmpty title="没有符合条件的干员" icon="search">
      「近卫 · 六星 · 可公招」没有结果。
      <template #extra>
        <AkButton size="sm" variant="outline">清除筛选</AkButton>
        <AkButton size="sm" variant="ghost" href="#">干员一览</AkButton>
      </template>
    </AkEmpty>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 状态码 

`code` 在图标的位置放一个 Novecento 大号状态码（404 / 403），描边色、不抢标题。

[Storybook ↗](/prts-design/storybook/?path=/story/components-empty--code)

vue

```
<script setup lang="ts">
import AkButton from "../../Button/AkButton.vue";
import AkEmpty from "../AkEmpty.vue";
</script>

<template>
  <AkEmpty code="404" title="页面不存在">
    PRTS 里还没有「<b>陈（假日威龙）/语音记录</b>」这个页面。
    <template #extra>
      <AkButton size="sm" variant="primary" icon="edit">创建此页面</AkButton>
      <AkButton size="sm" icon="search">搜索站内</AkButton>
    </template>
  </AkEmpty>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | `"暂无数据"` | 标题（粗体一行）；传空串不显示 |
| `description` | `string` | — | 说明文字（小字，标题下一行）；要放链接时改用默认插槽 |
| `code` | `string` | — | 大号状态码（404 / 403）：放在图标的位置，写了就不显示图标 |
| `icon` | `false | IconName` | `"empty"` | 图标（48px 半透明线稿）；false 不显示 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 说明（代替 description） |
| `icon` | 自定义图标（代替 icon）；自己加 class="ak-empty\_\_icon" 得到 48px 半透明 |
| `extra` | 说明下方的操作（按钮 / 链接） |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
