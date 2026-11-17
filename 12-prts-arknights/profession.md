# 职业 / 分支 Profession

> 来源：https://mooncellwiki.github.io/prts-design/arknights/profession
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

职业、分支图标都是 torappu 解包的游戏原图（`profession_large_hub` / `ui_sub_profession_icon_hub`）：白色图形，暗色主题原样、亮色主题由 CSS 反相为黑（`--ak-glyph-filter`），不用准备两套图。图片地址由调用方传。

## 八大职业 [​](#八大职业)

写了 `name` 就作图标的 `alt`，并默认作悬停提示（`tip` 可改写，`tip=false` 关掉）；不写 `name` 是装饰图标——旁边已经有文字时这么用。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-profession--professions)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkProfession from "../AkProfession.vue";

const professions = [
  { id: "pioneer", name: "先锋" },
  { id: "warrior", name: "近卫" },
  { id: "tank", name: "重装" },
  { id: "sniper", name: "狙击" },
  { id: "caster", name: "术师" },
  { id: "medic", name: "医疗" },
  { id: "support", name: "辅助" },
  { id: "special", name: "特种" },
];
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
    <AkProfession v-for="p in professions" :key="p.id" :src="asset(`profession/${p.id}.png`)" :name="p.name" size="lg" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 · 外观 [​](#尺寸-·-外观)

`sm` 22 · 默认 32 · `lg` 48 · `xl` 72（px 边长），图占 82%。`box` 深底白图，两套主题不变（同游戏内）；`outline` 加 1px 描边。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-profession--variants)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkProfession from "../AkProfession.vue";

const src = asset("profession/warrior.png");
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
      <AkProfession :src="src" name="近卫" size="sm" />
      <AkProfession :src="src" name="近卫" />
      <AkProfession :src="src" name="近卫" size="lg" />
      <AkProfession :src="src" name="近卫" size="xl" />
    </div>
    <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
      <AkProfession :src="src" name="近卫" size="lg" variant="box" />
      <AkProfession :src="src" name="近卫" size="lg" variant="outline" />
      <AkProfession :src="src" name="近卫" variant="box" />
      <AkProfession :src="src" name="近卫" variant="outline" />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 分支 · 带文字 [​](#分支-·-带文字)

`AkProfessionLabel` 输出 `.ak-prof-label`：图标 + 中文名 + Bender 英文小字（`en`），`href` 让名字成为链接——干员页「特性」的分支格就是它。`src` 默认画成分支图标（`AkProfession branch` = `.ak-subprof`，26px、图占 90%）；要职业图标等别的写法时放进 `#icon` 插槽。表格里只要一枚分支图标时直接用 `<AkProfession branch>`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-profession--branch)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkProfession from "../AkProfession.vue";
import AkProfessionLabel from "../AkProfessionLabel.vue";

const branches = [
  { id: "lord", name: "领主" },
  { id: "centurion", name: "强攻手" },
  { id: "charger", name: "冲锋手" },
  { id: "corecaster", name: "中坚术师" },
  { id: "physician", name: "医师" },
];
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkProfessionLabel :src="asset('subprofession/sword.png')" name="剑豪" en="Swordmaster" href="#" />
      <AkProfessionLabel v-for="b in branches" :key="b.id" :src="asset(`subprofession/${b.id}.png`)" :name="b.name" />
    </div>
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <!-- 职业名：图标换成职业图标 -->
      <AkProfessionLabel name="近卫" en="Guard">
        <template #icon><AkProfession :src="asset('profession/warrior.png')" size="sm" :tip="false" /></template>
      </AkProfessionLabel>
      <!-- 只要分支图标（表格里一列） -->
      <AkProfession :src="asset('subprofession/sword.png')" name="剑豪" branch />
      <AkProfession :src="asset('subprofession/musha.png')" name="武者" branch />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 职业色 [​](#职业色)

8 个职业各有一枚图表 / 筛选高亮用的颜色（非官方，社区常用配色降饱和）：容器写 `data-prof="pioneer | warrior | tank | sniper | caster | medic | support | special"` 得到 `--ak-p`，或直接用 `--ak-prof-<职业>`。

html

```
<div class="ak-flex ak-wrap ak-gap-2">
  <span data-prof="pioneer" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">先锋</span>
  <span data-prof="warrior" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">近卫</span>
  <span data-prof="tank" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">重装</span>
  <span data-prof="sniper" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">狙击</span>
  <span data-prof="caster" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">术师</span>
  <span data-prof="medic" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">医疗</span>
  <span data-prof="support" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">辅助</span>
  <span data-prof="special" class="ak-tag" style="background: var(--ak-p); color: #1d1f20">特种</span>
</div>
```

## Vue API [​](#vue-api)

### AkProfession [​](#akprofession)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src`必填 | `string` | — | 图标地址：职业 profession/<职业>.png、分支 subprofession/<分支>.png（torappu 白线稿，亮色主题下 CSS 反相为黑） |
| `name` | `string` | — | 职业 / 分支名：作图标的 alt，并默认作悬停提示；不写 = 装饰图标（旁边已经有文字） |
| `branch` | `boolean` | `false` | 分支图标（.ak-subprof，26px，图占 90%）：size / variant 不适用 |
| `size` | `"md"``"sm"``"lg"``"xl"` | `"md"` | 尺寸：sm 22 · md 32 · lg 48 · xl 72（px 边长） |
| `variant` | `"default"``"outline"``"box"` | `"default"` | 外观：default 透明底（线稿跟主题反相）· box 深底白线稿（两套主题不变，同游戏内）· outline 1px 描边 |
| `tip` | `string | false` | — | 悬停提示（data-ak-tip）：默认是 name，可改写；false 关掉 |

### AkProfessionLabel [​](#akprofessionlabel)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 职业 / 分支名 |
| `en` | `string` | — | 英文名（Bender 小字，.ak-en） |
| `src` | `string` | — | 图标地址：画成分支图标（AkProfession branch，装饰，不带提示）；要职业图标或别的写法用 #icon 插槽 |
| `href` | `string` | — | 名字链接到职业 / 分支页 |

| 插槽 | 说明 |
| --- | --- |
| `icon` | 图标（代替 src），如 <AkProfession size="sm" :src /> |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
