# 技能全等级表 SkillSheet

> 来源：https://mooncellwiki.github.io/prts-design/arknights/skill-sheet
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

**干员页正文的技能用这个**：同现网 prts.wiki，一张表列完 1–7 级 + 专精 Ⅰ–Ⅲ，初始 / 消耗 / 持续按列对齐、上下直接对比，不做「选一个等级看一份」的切换器。表头是一张不写数值的宽[技能卡](/prts-design/arknights/skill)（名称行居中对齐图标），列头直接用 [SP 芯片](/prts-design/arknights/sp)当图例。

另一种对比法是[参数矩阵](/prts-design/arknights/skill-matrix)：描述只写一遍、等级横着放。长描述、参数多于三四个时用全等级表；短描述 / 手机优先时用矩阵。

## 全等级表 

`levels` 每项是一级（第 8–10 项 = 专精 Ⅰ–Ⅲ，三星及以下只有 7 项）：`description` 是游戏原始标记，`vars` 是这一级的 blackboard——直接把 `skill_table` 的描述模板和各级 blackboard 传进来，由 [AkRichText](/prts-design/arknights/rich-text) 填数、上色；`init` / `cost` / `duration` 是三列数值，空着的格显示「—」。`highlight` 高亮一行（当前等级），`terms` 给描述里的术语（晕眩）悬停说明。表头卡放在 `#header` 插槽。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skillsheet--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkSkill from "../../Skill/AkSkill.vue";
import AkSkillSheet, { type SkillSheetLevel } from "../AkSkillSheet.vue";

/** 陈 · 鞘击：描述模板（skill_table 原样）+ 每级的 blackboard / 初始 / 消耗 */
const template =
  "下次攻击使用刀鞘砸向敌人，造成相当于攻击力<@ba.vup>{atk_scale:0%}</>的物理伤害，令命中目标<$ba.stun>晕眩</><@ba.vup>{stun}</>秒";
// [atk_scale, stun, 初始, 消耗]：1–7 级 + 专精 Ⅰ–Ⅲ
const data = [
  [2, 1, 0, 7], [2.1, 1, 0, 7], [2.2, 1, 0, 7], [2.3, 1.25, 0, 6], [2.4, 1.25, 0, 6],
  [2.5, 1.25, 0, 6], [2.6, 1.5, 0, 5], [2.8, 1.5, 0, 5], [3, 1.5, 0, 5], [3.2, 1.5, 0, 4],
];
const levels: SkillSheetLevel[] = data.map(([atk_scale, stun, init, cost]) => ({ description: template, vars: { atk_scale, stun }, init, cost }));

const terms = { "ba.stun": "晕眩：无法移动、阻挡、攻击及使用技能" };
const masteryIcons = [1, <span style="--shiki-light:#005CC5;--shiki-dark:#79B8
```
