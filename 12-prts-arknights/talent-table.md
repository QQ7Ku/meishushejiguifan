# 天赋条件表 TalentTable

> 来源：https://mooncellwiki.github.io/prts-design/arknights/talent-table
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

**干员页正文的天赋用这个**：同现网 prts.wiki，一张表列完各精英阶段 / 模组等级下的效果，一个天赋一张表（「第一天赋」「第二天赋」）。列表 / 侧栏里的摘要用[天赋卡](/prts-design/arknights/talent)。

## 条件表 

`rows` 每项是一个条件下的效果：`name` 天赋名（相邻几行同名合并成一格）、`condition` 条件、`description` 描述（游戏原始标记，由 [AkRichText](/prts-design/arknights/rich-text) 渲染）。条件格要放[精英化](/prts-design/arknights/elite)图标时用 `#condition` 插槽（`{ row, index }`）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-talenttable--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkElite from "../../Elite/AkElite.vue";
import AkTalentTable, { type TalentRow } from "../AkTalentTable.vue";

/** 陈 · 第一天赋：同一个天赋名的几行合并成一格；条件格用插槽放精英化图标 */
const rows: (TalentRow & { phase: 1 | 2; module?: string })[] = [
  { name: "呵斥", phase: 1, description: "在场时每5秒回复全场友方角色1点攻击/受击技力" },
  { name: "呵斥", phase: 2, description: "在场时每4秒回复全场友方角色1点攻击/受击技力" },
  { name: "呵斥", phase: 2, module: "X模组 2级", description: "在场时每3秒回复全场友方角色1点攻击/受击技力" },
  { name: "呵斥", phase: 2, module: "X模组 3级", description: "在场时每3秒回复全场友方角色1点攻击/受击技力，自身额外回复1点技力" },
];
</script>

<template>
  <AkTalentTable title="第一天赋" :rows="rows">
    <template #condition="{ index }">
      <AkElite :src="asset(`elite/elite_${rows[index].phase}.png`)" :phase="rows[index].phase" />
      <template v-if="rows[index].module"> · {{ rows[index].module }}</template>
    </template>
  </AkTalentTable>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 潜能 · 算法开关 

表头右侧两枚开关，都是组件自己的状态，也可以用 `v-model` 暴露出来：

- **潜能**（默认 `v-model`）：有任一行写了 `potential`（潜能加成后的描述，加成部分一般是 `<@ba.talpu>（+1%）</>`）就出现；打开后描述换成潜能版。`potential-rank` / `potential-icon` 是开关上的「潜能5」与图标。
- **算法**（`v-model:calc`）：`calc-toggle` 打开时出现。描述里每个加成项前用 `AkCalc` 标出它进公式的方式——绿 + 直接加算 · 蓝 + 直接乘算 · 橙 + 最终加算 · 橙 × 最终乘算（同现网的四枚小图标）；开关打开时标记与表尾图例一起出现，图例末尾可用 `#legend` 插槽加「详见」链接。

要插 `AkCalc` 就得自己画描述：`#description` 插槽（`{ row, index, potential }`）——有潜能版的行调两次，`potential` 告诉你这次画的是哪一版。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-talenttable--toggles)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkElite from "../../Elite/AkElite.vue";
import AkRichText from "../../RichText/AkRichText.vue";
import AkCalc, { type CalcKind } from "../AkCalc.vue";
import AkTalentTable, { type TalentRow } from "../AkTalentTable.vue";

/** 陈 · 第二天赋：潜能 5 时每项多一点；攻击力 / 防御力是直接乘算（算法开关打开时标出来） */
const potential = ref(false);
const calc = ref(false);

const items: { label: string; calc?: CalcKind; bonus: number }[] = [
  { label: "攻击力", calc: "mul", bonus: 1 },
  { label: "防御力", calc: "mul", bonus: 1 },
  { label: "物理闪避", bonus: 3 },
];
/** 精英二 / Y模组 2级 / Y模组 3级 下的三项数值 */
const stats = [[5, 5, 10], [11, 11, 15], [15, 15, 18]];
const modules = [undefined, "Y模组 2级", "Y模组 3级"];

/** 描述串（没写 #description 插槽时直接由 AkRichText 渲染；这里要插 AkCalc，所以下面用插槽自己画） */
const rows: TalentRow[] = stats.map((s, i) => ({
  name: "持刀格斗术",
  condition: modules[i],
  description: items.map((it, k) => `${it.label}+${s[k]}%`).join("，"),
  potential: items.map((it, k) => `${it.label}+${s[k] + it.bonus}%<@ba.talpu>（+${it.bonus}%）</>`).join("，"),
}));
</script>

<template>
  <div class="ak-flex-col ak-gap-2">
    <AkTalentTable
      v-model="potential"
      v-model:calc="calc"
      title="第二天赋"
      :rows="rows"
      :potential-rank="5"
      :potential-icon="asset('potential/potential_4.png')"
      calc-toggle
    >
      <template #condition="{ row }">
        <AkElite :src="asset('elite/elite_2.png')" :phase="2" /><template v-if="row.condition"> · {{ row.condition }}</template>
      </template>
      <template #description="{ index, potential: pot }">
        <template v-for="(it, k) in items" :key="k">
          {{ k ? "，" : "" }}{{ it.label }}<AkCalc v-if="it.calc" :kind="it.calc" />+{{ stats[index][k] + (pot ? it.bonus : 0) }}%<AkRichText v-if="pot" variant="talpu">（+{{ it.bonus }}%）</AkRichText>
        </template>
      </template>
      <template #legend><span>详见 <a href="#">游戏数据基础 · 属性基本公式</a></span></template>
    </AkTalentTable>
    <span class="ak-fs-sm ak-fg-muted">潜能加成：{{ potential ? "开" : "关" }} · 算法：{{ calc ? "开" : "关" }}</span>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

预览页 / 皮肤里的纯 CSS 版靠 `input[data-toggle-class]` 约定：勾选时皮肤脚本（`skin.js` / `preview.js`，document 级委托）给最近的 `data-toggle-target`（默认 `table`）加 / 去类（`.is-pot` / `.is-calc`）；生产环境这几行也可以归 Gadget。Vue 版自带状态。

## 可访问性 

开关是带 `<label>` 的原生复选框（读作「潜能5加成」「算法」）。两版描述都在 DOM 里、由 CSS 切换显示，隐藏的那版 `display: none`，读屏也跳过。`AkCalc` 是 `role="img"`，读作「直接乘算」等（悬停同）。

## Vue API 

### AkTalentTable 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title`必填 | `string` | — | 第一列的列头（「第一天赋」「第二天赋」） |
| `rows`必填 | `TalentRow[]` | — | 各条件下的效果 |
| `potentialRank` | `number` | — | 潜能开关上写的潜能等级（「潜能5」）；有任一行写了 potential 才出现开关 |
| `potentialIcon` | `string` | — | 潜能开关上的潜能图标地址（游戏 potential\_N） |
| `calcToggle` | `boolean` | `false` | 表头显示「算法」开关——描述里用 AkCalc 标了加成项进公式的方式时打开；打开后表尾有图例行 |
| `terms` | `Record<string, string>` | — | 术语说明（termDescriptionDict），给描述里 <$ba.xxx> 的悬停提示 |
| `modelValue` | `boolean` | `false` | 潜能加成开关：打开时描述换成潜能版（表上 .is-pot） |
| `calc` | `boolean` | `false` | 算法标记开关：打开时每个加成项前露出 AkCalc、表尾出图例（表上 .is-calc） |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |
| `update:calc` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `condition` | 自己画条件格（放精英化图标等）：{ row, index } |
| `description` | 自己画描述（要标 AkCalc 时用）：potential = 这是潜能版还是基础版（有潜能版的行调两次） |
| `legend` | 图例行末尾的附加内容（「详见 属性基本公式」链接） |

### AkCalc 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `kind`必填 | `"add"``"mul"``"fadd"``"fmul"` | — | 进公式的方式：add 直接加算（绿 +）· mul 直接乘算（蓝 +）· fadd 最终加算（橙 +）· fmul 最终乘算（橙 ×） |

## CSS 实现 

结构：`table.ak-talent-table > thead( th.name th.cond th.desc > label.ak-check.ak-talent-table__toggle ) + tbody > tr( td.name[rowspan] td.cond td.desc( .ak-talent-table__base + .ak-talent-table__pot ) ) + tfoot > tr.ak-talent-table__legend`；表上 `.is-pot` / `.is-calc` 切换。窄屏时开关不再浮在右侧、换到「描述」下面。表格数字走正文字体（见[表格里的数字](/prts-design/arknights/skill-sheet#表格里的数字)）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
