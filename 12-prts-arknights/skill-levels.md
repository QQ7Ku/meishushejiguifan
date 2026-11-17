# 技能等级选择 SkillLevels

> 来源：https://mooncellwiki.github.io/prts-design/arknights/skill-levels
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

1–7 级 + 专精 Ⅰ–Ⅲ 的一条单选。**只给需要「当前等级」的场合**——高亮[全等级表](/prts-design/arknights/skill-sheet)的一行、钉住[参数矩阵](/prts-design/arknights/skill-matrix)的一列、计算器里选等级；干员页正文照现网一张表列完所有等级，不用它来回切。

## 等级 1–7 + 专精 [​](#等级-1–7-专精)

`v-model` 是等级 1–10（8–10 = 专精 Ⅰ–Ⅲ）。专精格画游戏图标（`mastery-icons` 传 `specialized_tiny_1–3.png` 的地址），选中时蓝底；没给图标时写 M1–M3。三星及以下没有专精：`max="7"`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skilllevels--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkSkillLevels from "../AkSkillLevels.vue";

const level = ref(7);
const masteryIcons = [1, 2, 3].map(n => asset(`specialized/specialized_tiny_${n}.png`));

/** 三星及以下没有专精：max=7 */
const lowRarity = ref(4);
/** 没给专精图标时写 M1–M3 */
const noIcons = ref(9);
</script>

<template>
  <div class="ak-flex-col ak-gap-3 ak-items-start">
    <AkSkillLevels v-model="level" :mastery-icons="masteryIcons" />
    <span class="ak-fs-sm ak-fg-muted">当前：{{ level > 7 ? `专精 ${level - 7}` : `${level} 级` }}</span>
    <AkSkillLevels v-model="lowRarity" :max="7" />
    <AkSkillLevels v-model="noIcons" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 配全等级表 [​](#配全等级表)

选择器的 `v-model` 与全等级表的 `highlight` 绑同一个值：选哪一级，表里那一行就高亮（`tr.is-hl`）。和参数矩阵的联动见[参数矩阵](/prts-design/arknights/skill-matrix#配等级选择器)。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skilllevels--linked)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkSkill from "../../Skill/AkSkill.vue";
import AkSkillSheet, { type SkillSheetLevel } from "../../SkillSheet/AkSkillSheet.vue";
import AkSkillLevels from "../AkSkillLevels.vue";

/** 选中的等级 = 全等级表的高亮行（tr.is-hl） */
const level = ref(7);

/** 陈 · 赤霄·拔刀 */
const template =
  "对前方范围内最多<@ba.vup>{max_target}</>名敌人造成相当于攻击力<@ba.vup>{atk_scale:0%}</>的<@ba.rem>物理</>和相当于攻击力<@ba.vup>{atk_scale:0%}</>的<@ba.rem>法术</>伤害";
// [max_target, atk_scale, 初始, 消耗]：1–7 级 + 专精 Ⅰ–Ⅲ
const data = [
  [4, 3.3, 10, 27], [4, 3.4, 10, 27], [4, 3.5, 10, 27], [5, 3.7, 12, 26], [5, 3.8, 12, 26],
  [5, 3.9, 12, 26], [6, 4.1, 14, 25], [6, 4.4, 15, 23], [6, 4.7, 16, 21], [7, 5, 20, 20],
];
const levels: SkillSheetLevel[] = data.map(([max_target, atk_scale, init, cost]) => ({ description: template, vars: { max_target, atk_scale }, init, cost }));
const masteryIcons = [1, 2, 3].map(n => asset(`specialized/specialized_tiny_${n}.png`));
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div><AkSkillLevels v-model="level" :mastery-icons="masteryIcons" label="赤霄·拔刀 · 技能等级" /></div>
    <AkSkillSheet :levels="levels" :highlight="level" :mastery-icons="masteryIcons" label="赤霄·拔刀 · 各等级数据">
      <template #header><AkSkill name="赤霄·拔刀" :icon="asset('skill/chen_2.png')" sp-type="attack" trigger="manual" :heading-level="3" /></template>
    </AkSkillSheet>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘 [​](#键盘)

按 [WAI-ARIA Radio Group](https://www.w3.org/WAI/ARIA/apg/patterns/radio/)：整条是 `role="radiogroup"`（`label` 是组名，默认「技能等级」），每格 `role="radio"` + `aria-checked`；专精格的名字来自图标的 `alt`（「专精1」）。

| 键 | 行为 |
| --- | --- |
| `Tab` | 进入 / 离开（只停在选中的那一格） |
| `→` / `↓` · `←` / `↑` | 下一级 / 上一级并选中（首尾循环） |
| `Home` / `End` | 1 级 / 最高一级 |

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `max` | `number` | `10` | 最高等级：7 = 只有 1–7（三星及以下 / 未精二），10 = 再加专精 Ⅰ–Ⅲ |
| `masteryIcons` | `readonly string[]` | — | 专精 Ⅰ–Ⅲ 的图标地址（游戏 specialized\_tiny\_1–3）；不给时写 M1–M3 |
| `label` | `string` | `"技能等级"` | 读屏用的组名 |
| `disabled` | `boolean` | `false` | 整组禁用 |
| `modelValue` | `number` | — | 当前等级 1–10（8–10 = 专精 Ⅰ–Ⅲ）；不绑定时没有选中项 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: number | undefined]` |  |

## CSS 实现 [​](#css-实现)

按钮 `all: unset` 去掉了原生外观，焦点环由 `.ak-skill-levels > button:focus-visible` 补回（内缩 2px）；禁用同按钮（半透明、不可点）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
