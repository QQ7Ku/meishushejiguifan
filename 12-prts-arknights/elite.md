# 精英化 Elite

> 来源：https://mooncellwiki.github.io/prts-design/arknights/elite
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

精英阶段图标 + 标签字（Bender 粗体小字）。图标是游戏 `elite_hub` 的白色原图，亮色主题下反相为黑；地址由调用方传（`elite_N.png`，大图 `elite_N_large.png`）。

要让读者**切换**精英阶段（属性计算器、攻击范围），用[阶段选择器](/prts-design/arknights/phase-tabs)；这里只是读数。

## 阶段 · 等级 · 大图标 

`phase` 给出默认文字「精英零 / 一 / 二」，`level` 接在后面（「精英二 Lv60」，模组 / 档案的解锁需求）。有文字时图标是装饰（`alt=""`）；`text=false` 只显示图标，这时阶段名（或 `label`）作图标的 `alt`。`size="lg"` 图标 28px 高，配大图用。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-elite--phases)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkElite from "../AkElite.vue";

const icon = (n: number) => asset(`elite/elite_${n}.png`);
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkElite :src="icon(0)" :phase="0" />
      <AkElite :src="icon(1)" :phase="1" />
      <AkElite :src="icon(2)" :phase="2" />
      <AkElite :src="icon(2)" :phase="2" :level="60" />
    </div>
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <!-- 大图标、只放图标：阶段名作 alt -->
      <AkElite :src="asset('elite/elite_0_large.png')" :phase="0" :text="false" size="lg" />
      <AkElite :src="asset('elite/elite_1_large.png')" :phase="1" :text="false" size="lg" />
      <AkElite :src="asset('elite/elite_2_large.png')" :phase="2" :text="false" size="lg" />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 改写文字 

`text` 改写整段文字：精英化材料表的行头（「提升至精英阶段 1 等级 1」）、专精材料表的行头（同一枚组件换专精小图标 `specialized_tiny_N.png`）。要带链接 / 加粗时写在默认插槽里。天赋表条件列的「· X模组 2级」写在组件外面。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-elite--text)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkElite from "../AkElite.vue";
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <!-- 精英化材料表的行头 -->
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkElite :src="asset('elite/elite_1.png')" text="提升至精英阶段 1 等级 1" />
      <AkElite :src="asset('elite/elite_2.png')" text="提升至精英阶段 2 等级 1" />
    </div>
    <!-- 专精材料表的行头：同一枚组件换专精小图标 -->
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <AkElite :src="asset('specialized/specialized_tiny_1.png')" text="专精 Ⅰ" />
      <AkElite :src="asset('specialized/specialized_tiny_2.png')" text="专精 Ⅱ" />
      <AkElite :src="asset('specialized/specialized_tiny_3.png')" text="专精 Ⅲ" />
    </div>
    <!-- 天赋表的条件列：阶段之后接模组条件（写在组件外） -->
    <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
      <span><AkElite :src="asset('elite/elite_2.png')" :phase="2" /> · X模组 2级</span>
      <span><AkElite :src="asset('elite/elite_2.png')" :phase="2" /> · Y模组 3级</span>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src`必填 | `string` | — | 图标地址：elite/elite\_N.png（白线稿，亮色主题下 CSS 反相为黑）；lg 用 elite\_N\_large.png |
| `phase` | `0``1``2` | — | 精英阶段 0–2：给出默认文字「精英零 / 一 / 二」 |
| `level` | `number` | — | 等级：接在阶段名后面，「精英二 Lv60」 |
| `text` | `string | false` | — | 文字：默认由 phase + level 拼出，可改写（「提升至精英阶段 1 等级 1」「专精 Ⅰ」）；false 只显示图标 |
| `size` | `"md"``"lg"` | `"md"` | 尺寸：md 图标 16px 高（默认，配文字）· lg 28px 高（一般只放图标） |
| `label` | `string` | — | 可访问名：只显示图标时作图标的 alt，默认是阶段名；有文字时图标是装饰 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字（代替 text），可以带链接 / 加粗 |

## CSS 实现 

放进表格单元格 / 键值表的 `dd` 时，数字走正文字体（`table-numerals.css` 的表格硬规则兜底）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
