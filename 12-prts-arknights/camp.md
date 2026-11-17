# 阵营 Camp

> 来源：https://mooncellwiki.github.io/prts-design/arknights/camp
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

势力徽标 + 名字。徽标是游戏 `spritepack/ui_camp_logo` 里的白色线稿，亮色主题下由 CSS 反相成黑色（走 `--ak-glyph-filter` 令牌），所以同一张图两套主题都能用。常见用法是放在干员信息的键值表里，写在「阵营」那一格。

## 徽标 + 名字 [​](#徽标-名字)

徽标高 28px，名字跟在右边，字重和正文一样。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-camp--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkCamp from "../AkCamp.vue";

const camps = [
  ["rhodes", "罗德岛"],
  ["lgd", "龙门近卫局"],
  ["kjerag", "谢拉格"],
  ["penguin", "企鹅物流"],
  ["rhine", "莱茵生命"],
  ["laterano", "拉特兰"],
  ["siracusa", "叙拉古"],
] as const;
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
    <AkCamp v-for="[id, name] in camps" :key="id" :src="asset(`camp/${id}.png`)" :name="name" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 深底方块 · 大号 [​](#深底方块-·-大号)

`variant="box"` 是深底方块：徽标保持白色原色，不跟主题反相，两套主题下外观一致。`size="lg"` 徽标高 48px。`logo-only` 只显示徽标，名字作为图片的 `alt`，同时作悬停提示。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-camp--variants)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkCamp from "../AkCamp.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
    <AkCamp :src="asset('camp/victoria.png')" name="维多利亚" variant="box" />
    <AkCamp :src="asset('camp/kazimierz.png')" name="卡西米尔" variant="box" />
    <AkCamp :src="asset('camp/ursus.png')" name="乌萨斯" size="lg" logo-only />
    <AkCamp :src="asset('camp/lungmen.png')" name="龙门" size="lg" logo-only />
    <AkCamp :src="asset('camp/iberia.png')" name="伊比利亚" size="lg" variant="box" logo-only />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src`必填 | `string` | — | 势力徽标（游戏 spritepack/ui\_camp\_logo 的白色线稿，亮色下由 CSS 反相成黑） |
| `name`必填 | `string` | — | 势力名：显示在徽标右侧；logoOnly 时作图片 alt 和悬停提示 |
| `size` | `"md"``"lg"` | `"md"` | 徽标高度：md 28 · lg 48（px） |
| `variant` | `"default"``"box"` | `"default"` | 外观：default 线稿跟主题反相 · box 深底方块、原色白线稿（两套主题下都一样） |
| `logoOnly` | `boolean` | `false` | 只显示徽标（名字进 alt 与悬停提示） |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
