# 干员条目 Op Row

> 来源：https://mooncellwiki.github.io/prts-design/arknights/op-row
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

列表 / 表格里一行一位干员：36px 头像（垫稀有度色的渐变底，见[稀有度 · 头像底](/prts-design/arknights/rarity#头像底)）+ 名字 + 第二行小字。整条是一个链接（`href`），外层带 `ak-not-prose`，不吃正文链接色。要卡片式的网格用[干员卡](/prts-design/arknights/op-card)。

## 条目 

`meta` 是第二行小字（分支 / 职业）；`stars` 在它前面画一排按稀有度色阶着色的小星（[稀有度](/prts-design/arknights/rarity) `variant="tier" size="sm"`）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-op-row--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkOpRow from "../AkOpRow.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
    <AkOpRow name="煌" :avatar="asset('avatar/char_017_huang_2.png')" :rarity="6" meta="强攻手" stars href="#" />
    <AkOpRow name="伊芙利特" :avatar="asset('avatar/char_134_ifrit_2.png')" :rarity="6" meta="阵法术师" href="#" />
    <AkOpRow name="年" :avatar="asset('avatar/char_2014_nian_2.png')" :rarity="6" meta="铁卫" href="#" />
    <AkOpRow name="德克萨斯" :avatar="asset('avatar/char_102_texas_2.png')" :rarity="5" meta="冲锋手" stars href="#" />
    <AkOpRow name="桃金娘" :avatar="asset('avatar/char_151_myrtle_2.png')" :rarity="4" meta="执旗手" stars href="#" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 表格里 · 自定义第二行 

`#meta` 插槽代替 `meta`，可以放[标签](/prts-design/components/tag)或链接。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-op-row--table)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkTag from "../../Tag/AkTag.vue";
import AkOpRow from "../AkOpRow.vue";
</script>

<template>
  <!-- 表格里：第一列放条目，#meta 插槽放标签 -->
  <table class="wikitable">
    <thead>
      <tr><th>干员</th><th>职业</th><th>获得方式</th></tr>
    </thead>
    <tbody>
      <tr>
        <td><AkOpRow name="陈" :avatar="asset('avatar/char_010_chen_2.png')" :rarity="6" meta="剑豪" stars href="#" /></td>
        <td>近卫</td>
        <td>中坚寻访</td>
      </tr>
      <tr>
        <td>
          <AkOpRow name="阿米娅" :avatar="asset('avatar/char_002_amiya_1p.png')" :rarity="5" href="#">
            <template #meta><AkTag size="sm" variant="accent-soft">主线</AkTag> 中坚术师</template>
          </AkOpRow>
        </td>
        <td>术师</td>
        <td>主线剧情</td>
      </tr>
    </tbody>
  </table>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 干员名 |
| `avatar`必填 | `string` | — | 头像地址（正方形，36px 显示） |
| `rarity` | `1``2``3``4``5``6` | — | 稀有度 1–6：头像的稀有度色渐变底；stars 时第二行的星也按它画 |
| `meta` | `string` | — | 第二行小字：分支 / 职业 / 获得方式 |
| `stars` | `boolean` | `false` | 在第二行前面画稀有度星（按稀有度色阶着色；要写 rarity） |
| `href` | `string` | — | 链接到干员页；不写渲染成 <span> |

| 插槽 | 说明 |
| --- | --- |
| `meta` | 第二行（代替 meta），可放标签 / 链接 |

## CSS 实现 

头像是通用组件 `.ak-avatar`（`components/avatar.css`），条目只改它的尺寸和左边条。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
