# 筛选芯片 Chip

> 来源：https://mooncellwiki.github.io/prts-design/components/chip
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

可交互的开关按钮：干员一览的筛选栏、干员页语音记录的语种切换。与[标签](/prts-design/components/tag)的分工：标签是只读的分类 / 状态标记，芯片是能点的筛选条件。

写法同 Naive UI 的 `n-tag checkable`：`v-model` 是否选中（Naive 的 `checked`），点击切换。渲染成 `<button type="button" aria-pressed>`，选中时主色实底 + 左上角三角角标（与标签页、卡片的选中角标同一枚），不占行内宽度。

## 筛选（多选） 

每枚芯片各自一个布尔值。一组芯片放进带 `role="group"` 与 `aria-label` 的容器（「职业」「稀有度」），读屏念「职业 分组 · 近卫 切换按钮 已按下」。

[Storybook ↗](/prts-design/storybook/?path=/story/components-chip--filter)

vue

```
<script setup lang="ts">
import { reactive } from "vue";

import AkChip from "../AkChip.vue";

const profession = reactive({ guard: true, sniper: false, caster: false });
const rarity = reactive({ six: true, five: false });
const recruit = reactive({ yes: false, favorite: false });
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center" role="group" aria-label="职业">
      <span class="ak-overline">职业</span>
      <AkChip v-model="profession.guard">近卫</AkChip>
      <AkChip v-model="profession.sniper">狙击</AkChip>
      <AkChip v-model="profession.caster">术师</AkChip>
    </div>
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center" role="group" aria-label="稀有度">
      <span class="ak-overline">稀有度</span>
      <AkChip v-model="rarity.six">六星</AkChip>
      <AkChip v-model="rarity.five">五星</AkChip>
    </div>
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center" role="group" aria-label="其他">
      <span class="ak-overline">其他</span>
      <AkChip v-model="recruit.yes">可公招</AkChip>
      <AkChip v-model="recruit.favorite" icon="star">已收藏</AkChip>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 单选（语音语种） 

一组里只能选一个时：各自 `:model-value="lang === key"` + `@update:model-value="lang = key"`，再点已选中的那枚不会取消。干员页语音记录的语种切换就是这样。

[Storybook ↗](/prts-design/storybook/?path=/story/components-chip--single)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkChip from "../AkChip.vue";

const langs = [
  { key: "cn", name: "中文-普通话" },
  { key: "yue", name: "中文-方言" },
  { key: "jp", name: "日文" },
  { key: "en", name: "英文" },
  { key: "kr", name: "韩文" },
];
const lang = ref("cn");
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-2" role="group" aria-label="语音语种">
      <AkChip v-for="l in langs" :key="l.key" :model-value="lang === l.key" icon="mic" @update:model-value="lang = l.key">
        {{ l.name }}
      </AkChip>
    </div>
    <p class="ak-fs-sm ak-fg-muted ak-m-0">当前：{{ langs.find(l => l.key === lang)?.name }}（再点一次已选中的不会取消）</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 芯片是原生 `<button type="button">`：`Tab` 聚焦，`Enter` / `Space` 切换。
- 状态用 `aria-pressed`（[WAI-ARIA Button](https://www.w3.org/WAI/ARIA/apg/patterns/button/) 的切换按钮），不用 `aria-selected` / `aria-checked`。
- 选中不只靠颜色：实底反转、角标、字重 600 三处同时变。
- `AkChip` 带 `data-no-toggle`：皮肤脚本会在 document 上替模板输出的纯 CSS 芯片翻 `is-active` / `aria-pressed`，状态归 Vue 管的芯片要退出这层委托，否则两边各翻一次。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `icon` | `IconName` | — | 文字前的图标（芯片自带 6px 间距） |
| `modelValue` | `boolean` | `false` | 是否选中（= Naive 的 checked）；点击切换 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 芯片文字 |

## CSS 实现 

模板里写 `<button type="button" class="ak-chip" aria-pressed="false">近卫</button>`；`aria-pressed="true"` 与 `.is-active` 两种写法都认。切换由皮肤脚本（document 级委托）负责，不想被它接管的容器标 `data-no-toggle`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
