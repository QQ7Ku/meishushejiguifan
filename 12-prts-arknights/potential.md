# 潜能 / 专精 Potential

> 来源：https://mooncellwiki.github.io/prts-design/arknights/potential
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

潜能 1–6、专精 1–3 的游戏图标（`potential_hub` / `specialized_hub`）。图标自带颜色、不随主题反相，所以默认坐在一块深色底座上（两套主题一样）；放进已有深底或行内小图时用 `bare` 去掉底座。

## 潜能 

`value` 给出默认 `alt`「潜能 N」；旁边已经写了「潜能 N」时 `label=""` 当装饰（[潜能提升一览](/prts-design/arknights/pot-list)就是这样）。图标文件是 `potential_{N−1}.png`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-potential--potentials)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkPotential from "../AkPotential.vue";

const values = [1, 2, 3, 4, 5, 6] as const;
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkPotential v-for="n in values" :key="n" :src="asset(`potential/potential_${n - 1}.png`)" :value="n" />
    </div>
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkPotential v-for="n in values" :key="n" :src="asset(`potential/potential_${n - 1}.png`)" :value="n" bare />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 专精 

`AkSpecialization` = `.ak-spec`，同一套底座；`value` 1–3 给出默认 `alt`「专精 N」。技能等级选择器里的专精小图标见技能组件。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-potential--specialization)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkSpecialization from "../AkSpecialization.vue";

const values = [1, 2, 3] as const;
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
    <AkSpecialization v-for="n in values" :key="n" :src="asset(`specialized/specialized_${n}.png`)" :value="n" />
    <AkSpecialization v-for="n in values" :key="`b${n}`" :src="asset(`specialized/specialized_${n}.png`)" :value="n" bare />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkPotential 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src`必填 | `string` | — | 图标地址：potential/potential\_{N−1}.png（游戏图标自带颜色，不随主题反相） |
| `value` | `1``2``3``4``5``6` | — | 潜能 1–6：给出默认 alt「潜能 N」 |
| `label` | `string` | — | 可访问名（图标的 alt）：默认「潜能 N」；旁边已有同样的文字时写 "" 当装饰 |
| `bare` | `boolean` | `false` | 去掉深色底座（放进已有深底 / 表头开关这类行内位置） |

### AkSpecialization 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src`必填 | `string` | — | 图标地址：specialized/specialized\_N.png |
| `value` | `1``2``3` | — | 专精 1–3：给出默认 alt「专精 N」 |
| `label` | `string` | — | 可访问名（图标的 alt）：默认「专精 N」；旁边已有同样的文字时写 "" 当装饰 |
| `bare` | `boolean` | `false` | 去掉深色底座 |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
