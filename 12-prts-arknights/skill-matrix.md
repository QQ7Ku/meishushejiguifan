# 技能参数矩阵 SkillMatrix

> 来源：https://mooncellwiki.github.io/prts-design/arknights/skill-matrix
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

[全等级表](/prts-design/arknights/skill-sheet)的另一种对比法（二选一，看版面）：等级横着放、参数竖着放（游戏内技能详情 / 各类工具箱的写法）。描述只写一遍，变量位写成区间（200%→320%）；每个参数一行、每级一列，**较上一级有变化的格子才亮**、没变的淡掉——一眼看出哪一级涨了什么、涨了多少，十句重复的话不用读十遍，手机上也不用横向滚。

## 参数矩阵 

`description` 是描述模板（游戏原始标记），里面的 `{key}` 占位符是变量位；`rows` 是参数行，`key` 对上占位符的那几行驱动描述里的变量位，`sp`（`init` / `cost` / `duration`）的行用 SP 芯片当参数名。`values` 是各级已经写好的字（「200%」）。有没有变化（`td.is-up`）、7 级与专精之间的分隔由组件算。

**悬停某一列 → 整列高亮，描述里的变量位换成该级的值；离开还原区间。点列里任一格钉住这一列，再点取消。**

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skillmatrix--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkSkill from "../../Skill/AkSkill.vue";
import AkSkillMatrix, { type SkillMatrixRow } from "../AkSkillMatrix.vue";

/** 陈 · 鞘击：描述只写一遍，{atk_scale} / {stun} 两个变量位各对应矩阵的一行 */
const description =
  "下次攻击使用刀鞘砸向敌人，造成相当于攻击力<@ba.vup>{atk_scale}</>的物理伤害，令命中目标<$ba.stun>晕眩</><@ba.vup>{stun}</>秒";
const rows: SkillMatrixRow[] = [
  { key: "atk_scale", label: "伤害倍率", values: ["200%", "210%", "220%", "230%", "240%", "250%", "260%", "280%", "300%", "320%"] },
  { key: "stun", label: "晕眩（秒）", values: [1, 1, 1, 1.25, 1.25, 1.25, 1.5, 1.5, 1.5, 1.5] },
  { label: "初始", sp: "init", values: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0] },
  { label: "消耗", sp: "cost", values: [7, 7, 7, 6, 6, 6, 5, 5, 5, 4] },
];

const terms = { "ba.stun": "晕眩：无法移动、阻挡、攻击及使用技能" };
const masteryIcons = [1, 2, 3].map(n => asset(`specialized/specialized_tiny_${n}.png`));
</script>

<template>
  <AkSkillMatrix :rows="rows" :description="description" :mastery-icons="masteryIcons" :terms="terms" label="鞘击 · 各等级参数">
    <template #header>
      <AkSkill
        name="鞘击"
        :icon="asset('skill/chen_1.png')"
        sp-type="attack"
        trigger="auto"
        unlock="技能1 · 精英零开放"
        :unlock-icon="asset('elite/elite_0.png')"
        :heading-level="3"
      />
    </template>
  </AkSkillMatrix>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 配等级选择器 

钉住的列就是 `v-model`（等级 1–10，没钉住是 `undefined`），可以和[技能等级选择](/prts-design/arknights/skill-levels)绑同一个值：点选择器 = 钉住那一列，点矩阵的列 = 选择器跟着变。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skillmatrix--linked)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkSkill from "../../Skill/AkSkill.vue";
import AkSkillLevels from "../../SkillLevels/AkSkillLevels.vue";
import AkSkillMatrix, { type SkillMatrixRow } from "../AkSkillMatrix.vue";

/** 陈 · 赤霄·绝影：等级选择器与矩阵绑同一个 v-model——点选择器 = 钉住那一列，点矩阵的列 = 选择器跟着变 */
const level = ref<number | undefined>(7);

const description =
  "向周围寻找最近的敌方目标，对其发动<@ba.vup>10</>次连续斩击，每次造成相当于攻击力<@ba.vup>{atk_scale}</>的物理伤害，并在最后一击时使目标<$ba.stun>晕眩</><@ba.vup>{stun}</>秒";
const rows: SkillMatrixRow[] = [
  { key: "atk_scale", label: "伤害倍率", values: ["200%", "210%", "220%", "230%", "240%", "250%", "260%", "280%", "300%", "320%"] },
  { key: "stun", label: "晕眩（秒）", values: [2, 2, 2, 2.5, 2.5, 2.5, 3, 3, 3, 4] },
  { label: "初始", sp: "init", values: [10, 10, 10, 10, 10, 10, 10, 13, 16, 20] },
  { label: "消耗", sp: "cost", values: [40, 40, 40, 38, 38, 38, 36, 34, 32, 30] },
];

const terms = { "ba.stun": "晕眩：无法移动、阻挡、攻击及使用技能" };
const masteryIcons = [1, 2, 3].map(n => asset(`specialized/specialized_tiny_${n}.png`));
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div><AkSkillLevels v-model="level" :mastery-icons="masteryIcons" label="赤霄·绝影 · 技能等级" /></div>
    <AkSkillMatrix v-model="level" :rows="rows" :description="description" :mastery-icons="masteryIcons" :terms="terms" label="赤霄·绝影 · 各等级参数">
      <template #header>
        <AkSkill name="赤霄·绝影" :icon="asset('skill/chen_3.png')" sp-type="attack" trigger="manual" :heading-level="3" />
      </template>
    </AkSkillMatrix>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘与可访问性 

列头的等级是按钮（`aria-pressed` = 钉住的列），一组只有一个 Tab 停点：

| 键 | 行为 |
| --- | --- |
| `Tab` | 进入列头（停在上次聚焦 / 钉住的列，否则第 1 列） |
| `←` / `→` | 移到上一列 / 下一列（聚焦即预览该列，同悬停） |
| `Home` / `End` | 第一列 / 最后一列 |
| `Enter` / `Space` | 钉住 / 取消这一列 |

矩阵本身是完整的数据表（`th scope`、`label` 作表格的可访问名），读屏用户直接读表即可；描述里变量位的切换只是视觉上的便利，不设 live region，免得每挪一列都播报一次。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `rows`必填 | `SkillMatrixRow[]` | — | 参数行 |
| `description` | `string` | — | 描述模板（游戏原始标记）：只写一遍，{key} 变量位画成 .ak-var——平时是区间（200%→320%），悬停 / 选中某一列时换成该级的值 |
| `masteryIcons` | `readonly string[]` | — | 专精 Ⅰ–Ⅲ 的图标地址（游戏 specialized\_tiny\_1–3）；不给时写 M1–M3 |
| `terms` | `Record<string, string>` | — | 术语说明（termDescriptionDict），给描述里 <$ba.xxx> 的悬停提示 |
| `label` | `string` | — | 表格的可访问名（如「鞘击 · 各等级参数」） |
| `modelValue` | `number` | — | 钉住的一列（等级 1–10，没有是 undefined）：点列里任一格 / 列头按钮钉住、再点取消；可以和 AkSkillLevels 绑同一个值 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: number | undefined]` |  |

| 插槽 | 说明 |
| --- | --- |
| `header` | 表头卡：一般放 <AkSkill>（名称 / SP 类型 / 开放条件 / 范围） |
| `footer` | 表下的注释（.ak-skill-sheet\_\_note，每条一个 <p>） |
| `description` | 自己画描述（代替 description）：level = 正在看的等级（没有是 undefined），value(key) = 该变量此刻该显示的字 |

## CSS 实现 

结构：`.ak-skill-sheet > .ak-skill--wide + .ak-skill-sheet__tpl`（描述 + `.ak-var[data-var]`）`+ table.ak-skill-matrix`（`col.label`；`thead th` 等级，`th.is-mastery` 专精、`.m-start` 专精 Ⅰ 那一列的左分隔；`tbody tr[data-var] > th` 参数名 + `td.is-up` / `td`；`.is-hl` 当前列）。表格数字走正文字体（见[表格里的数字](/prts-design/arknights/skill-sheet#表格里的数字)）。

纯 CSS 用法（模板 / Lua 输出）没有 JS 时就是一张静态矩阵；列头的 `<button>` 是 Vue 版加的（`all: unset` 去外观，焦点环补回）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
