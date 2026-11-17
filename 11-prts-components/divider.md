# 分隔线 Divider

> 来源：https://mooncellwiki.github.io/prts-design/components/divider
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

把一段内容和下一段分开。写法同 Naive UI 的 `n-divider`：默认插槽写字就是中间带字的分隔，`vertical` 是行内竖线。

## 变体 · 文字 

默认 1px 线（`<hr>`，上下 24px）；`accent` 左段 80px 主色 + 其余细线（2px 高，章节收尾）；`stripes` 6px 斜纹带（「以下为存档」这类警示性的分界）；默认插槽写字时是小号大写标签字、两侧线段（`role="separator"`）。带文字时 `variant` 不适用。

[Storybook ↗](/prts-design/storybook/?path=/story/components-divider--variants)

vue

```
<script setup lang="ts">
import AkDivider from "../AkDivider.vue";
</script>

<template>
  <div>
    <p class="ak-m-0">精英化材料：芯片 + 龙门币。</p>
    <AkDivider />
    <p class="ak-m-0">技能升级材料：1→7 通用，专精另计。</p>
    <AkDivider variant="accent" />
    <p class="ak-m-0">干员档案：信赖提升后解锁。</p>
    <AkDivider variant="stripes" />
    <p class="ak-m-0">以下内容已删除，仅作存档。</p>
    <AkDivider>Archive · 旧版数据</AkDivider>
    <p class="ak-m-0">2022 年 12 月 1 日之前的干员异格任务。</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 竖线 

`vertical`：行内 1px × 1em，左右 8px，隔开同一行里的几项（页面动作、干员的分支 · 编号 · 势力）。`role="separator"` + `aria-orientation="vertical"`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-divider--vertical)

vue

```
<script setup lang="ts">
import AkDivider from "../AkDivider.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <p class="ak-m-0 ak-fs-sm">
      <a href="#">编辑</a><AkDivider vertical /><a href="#">历史</a><AkDivider vertical /><a href="#">讨论</a><AkDivider vertical /><a href="#">监视</a>
    </p>
    <p class="ak-m-0 ak-fs-sm ak-fg-muted">剑豪<AkDivider vertical />LM04<AkDivider vertical />龙门近卫局</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"default"``"accent"``"stripes"` | `"default"` | 外观：default 1px 线 · accent 左段 80px 主色 + 其余 1 色线（2px 高）· stripes 6px 斜纹带；带文字 / 竖线时不适用 |
| `vertical` | `boolean` | `false` | 竖线：行内 1px × 1em，隔开同一行里的几项（「编辑 | 历史」） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 线中间的文字（同 Naive 的标题）；竖线时不适用 |

## CSS 实现 

横线用 `<hr class="ak-divider">`（自带分隔语义）；带文字的用 `<div class="ak-divider ak-divider--text" role="separator">`；竖线 `<span class="ak-divider ak-divider--vertical" role="separator" aria-orientation="vertical">`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
