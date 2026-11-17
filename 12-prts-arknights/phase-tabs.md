# 阶段选择器 Phase Tabs

> 来源：https://mooncellwiki.github.io/prts-design/arknights/phase-tabs
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

精英阶段 E0 / E1 / E2 的选择器：一排贴在一起的小块，当前项黑白反转（同游戏内 `btn_on`）。属性计算器、攻击范围这类「同一块数据按阶段重算」的地方用；`v-model` 是当前阶段 0–2。

## 基本 

图标由调用方按阶段传（`icons`，下标 = 阶段，`elite_N.png` 白线稿）：未选中项半透明，选中项落在反色块上时自动反相（`--ak-glyph-filter-inverse`，跟随系统的暗色也正确）。它和 [LV 灰块](/prts-design/arknights/level)、[信赖](/prts-design/arknights/trust)是同一高度，放在一行里齐平。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-phase-tabs--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkLevel from "../../Level/AkLevel.vue";
import AkTrust from "../../Trust/AkTrust.vue";
import AkPhaseTabs from "../AkPhaseTabs.vue";

const icons = [0, 1, 2].map(n => asset(`elite/elite_${n}.png`));
const phase = ref(2);

/** 陈：各阶段满级与满级攻击（现网 {{CharinfoV2}} 数据） */
const maxLv = [50, 80, 90];
const atk = [361, 469, 610];
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <!-- 属性计算器的控制行：阶段选择器、LV 灰块、信赖同一条线（24px） -->
    <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
      <AkPhaseTabs v-model="phase" :icons="icons" />
      <AkLevel :value="maxLv[phase]" variant="badge" />
      <AkTrust :value="100" />
    </div>
    <p class="ak-mb-0 ak-fs-sm ak-fg-muted">精英 {{ phase }} 满级攻击：{{ atk[phase] }}</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 低星干员 · 无图标 

`max` 是最高精英阶段：三星干员 1、一 / 二星 0，只画到这一阶（没有的阶段不画成禁用——读者不需要知道「这里本来可以有 E2」）。绑定的值超出 `max` 时按最高阶显示。不传 `icons` 只有文字。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-phase-tabs--max)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkPhaseTabs from "../AkPhaseTabs.vue";

const icons = [0, 1, 2].map(n => asset(`elite/elite_${n}.png`));
const a = ref(1);
const b = ref(0);
const c = ref(0);
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-3">
    <!-- 三星干员（白雪）：最高精英一 -->
    <div class="ak-flex ak-gap-3 ak-items-center">
      <AkPhaseTabs v-model="a" :icons="icons" :max="1" /><span class="ak-fs-sm ak-fg-muted">三星 · max=1</span>
    </div>
    <!-- 一 / 二星：只有精英零 -->
    <div class="ak-flex ak-gap-3 ak-items-center">
      <AkPhaseTabs v-model="b" :icons="icons" :max="0" /><span class="ak-fs-sm ak-fg-muted">二星 · max=0</span>
    </div>
    <!-- 不传图标：只有文字 -->
    <div class="ak-flex ak-gap-3 ak-items-center">
      <AkPhaseTabs v-model="c" /><span class="ak-fs-sm ak-fg-muted">无图标</span>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 语义：单选组，不是页签 

它不切换面板，只改一个值、下游数据跟着重算，所以按 [WAI-ARIA Radio Group](https://www.w3.org/WAI/ARIA/apg/patterns/radio/) 做：外层 `role="radiogroup"`（`label` 默认「精英阶段」），每项 `<button role="radio" aria-checked>`。要切换几块各自独立的内容时用[标签页](/prts-design/components/tabs)。

| 键 | 行为 |
| --- | --- |
| `Tab` | 进入 / 离开整组（只有当前阶段在 Tab 顺序里） |
| `→` / `↓` | 下一阶并选中（末尾回到第一阶） |
| `←` / `↑` | 上一阶并选中（开头回到最后一阶） |
| `Space` / `Enter` | 选中当前焦点项 |

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `max` | `0``1``2` | `2` | 最高精英阶段：只画到这一阶（三星干员 1，一 / 二星 0） |
| `icons` | `string[]` | `[]` | 各阶段图标地址，下标 = 阶段（elite/elite\_N.png，白线稿；选中项在反色块上自动反相）；不传只有文字 |
| `label` | `string` | `"精英阶段"` | 读屏用的组名 |
| `modelValue` | `number` | `0` | 当前精英阶段 0–2 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: number]` |  |

## CSS 实现 

CSS 只管外观；没有 Vue 的页面（MW 模板）由页面脚本切换 `.is-active`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
