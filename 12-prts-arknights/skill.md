# 技能卡 Skill

> 来源：https://mooncellwiki.github.io/prts-design/arknights/skill
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

一个技能的摘要卡：64px 图标（SP 类型色描边 + 底部色条，同游戏）、名称、[SP 类型 / 触发](/prts-design/arknights/sp)、消耗 / 初始 / 持续，下面一段描述。给列表、侧栏、干员卡悬停这类「一眼看一个等级」的场合；**干员页正文用[全等级表](/prts-design/arknights/skill-sheet)**（或[参数矩阵](/prts-design/arknights/skill-matrix)），不做「选一个等级看一份」的切换。

右栏撑满图标高度、两端对齐：名称行贴图标顶边，数值芯片行贴图标底边。

## 技能卡 [​](#技能卡)

`sp-type` 决定图标描边 / 底条色与 SP 标签（`auto` 绿 · `attack` 橙 · `hit` 黄 · `passive` 灰）；`trigger` 是手动 / 自动触发，被动技能不写。`cost` / `init` 画成芯片，`duration` 写成「持续 25s」（数字按秒），哪个不写哪个不显示。描述写在默认插槽里——游戏原始标记套一层 [AkRichText](/prts-design/arknights/rich-text)。`selected` 是游戏内的选中态（蓝框 + 左上角标）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skill--cards)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkRichText from "../../RichText/AkRichText.vue";
import AkSkill from "../AkSkill.vue";

/** 陈的三个技能，7 级数据 */
const terms = { "ba.stun": "晕眩：无法移动、阻挡、攻击及使用技能" };
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkSkill name="赤霄·绝影" :icon="asset('skill/chen_3.png')" sp-type="attack" trigger="manual" :cost="36" :init="10" duration="—" selected>
      <AkRichText
        text="向周围寻找最近的敌方目标，对其发动<@ba.vup>10</>次连续斩击，每次造成相当于攻击力<@ba.vup>260%</>的物理伤害，并在最后一击时使目标<$ba.stun>晕眩</><@ba.vup>3</>秒"
        :terms="terms"
      />
    </AkSkill>
    <AkSkill name="赤霄·拔刀" :icon="asset('skill/chen_2.png')" sp-type="attack" trigger="manual" :cost="25" :init="14">
      <AkRichText
        text="对前方范围内最多<@ba.vup>6</>名敌人造成相当于攻击力<@ba.vup>410%</>的<@ba.rem>物理</>和相当于攻击力<@ba.vup>410%</>的<@ba.rem>法术</>伤害"
      />
    </AkSkill>
    <AkSkill name="鞘击" :icon="asset('skill/chen_1.png')" sp-type="attack" trigger="auto" :cost="5" :init="0">
      <AkRichText text="下次攻击使用刀鞘砸向敌人，造成相当于攻击力<@ba.vup>260%</>的物理伤害，令命中目标<$ba.stun>晕眩</><@ba.vup>1.5</>秒" :terms="terms" />
    </AkSkill>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 未解锁 [​](#未解锁)

`locked`：图标灰化 + 锁。名称行末尾的附加内容（「未解锁」标签）用 `#header-extra` 插槽。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skill--states)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkTag from "../../Tag/AkTag.vue";
import AkSkill from "../AkSkill.vue";

/* 未解锁：图标灰化 + 锁；SP 类型色见图标描边与底部色条（绿 = 自动回复） */
</script>

<template>
  <AkSkill name="真银斩" :icon="asset('skill/svrash_3.png')" sp-type="auto" trigger="manual" :cost="40" :init="20" :duration="25" locked>
    <template #header-extra><AkTag variant="outline" size="sm">未解锁</AkTag></template>
    攻击范围扩大，攻击力提升，攻击时同时攻击范围内的多名敌人
  </AkSkill>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 右侧槽 [​](#右侧槽)

写了 `unlock`（开放条件，可带 `unlock-icon` 精英化图标）或 `#aside` 插槽（技能[范围](/prts-design/arknights/range)）就是三栏的宽卡 `.ak-skill--wide`；窄屏时右侧槽换到第二行。全等级表 / 参数矩阵的表头就是一张不写数值的宽卡。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skill--wide)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkRange from "../../Range/AkRange.vue";
import AkSkill from "../AkSkill.vue";

/** 赤霄·拔刀的技能范围（range_table 的 grids，相对自身） */
const range: [number, number][] = [[-1, 0], [-1, 1], [0, 1], [0, 2], [0, 3], [1, 0], [1, 1]];
/* 右侧槽：开放条件 + 技能范围（写了 unlock 或 #aside 就是三栏的宽卡） */
</script>

<template>
  <AkSkill
    name="赤霄·拔刀"
    :icon="asset('skill/chen_2.png')"
    sp-type="attack"
    trigger="manual"
    :cost="25"
    :init="14"
    unlock="技能2 · 精英一开放"
    :unlock-icon="asset('elite/elite_1.png')"
  >
    <template #aside><AkRange :grids="range" size="sm" label="技能范围" /></template>
    对前方范围内最多 6 名敌人造成相当于攻击力 410% 的物理和法术伤害
  </AkSkill>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 别名 [​](#别名)

`alias` 在技能名后接一段灰色小字：`en` 英文名（Bender 大写，`.ak-en`）、`ja` 日文名，与技能名基线对齐——干员页全等级表的表头卡就是这样写的。要放别的内容（多一种语言、带链接）时用 `#alias` 插槽，内容进 `.ak-skill__alias`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-skill--alias)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkSkill from "../AkSkill.vue";

/** 陈的三个技能：干员页全等级表的表头卡——技能名后跟英 / 日名，右侧是开放条件 */
const skills = [
  { name: "鞘击", en: "Sheathed Strike", ja: "鞘打ち", trigger: "auto", unlock: "技能1 · 精英零开放" },
  { name: "赤霄·拔刀", en: "Chi Xiao · Unsheath", ja: "赤霄・抜刀", trigger: "manual", unlock: "技能2 · 精英一开放" },
  { name: "赤霄·绝影", en: "Chi Xiao · Shadowless", ja: "赤霄・絶影", trigger: "manual", unlock: "技能3 · 精英二开放" },
] as const;
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkSkill
      v-for="(s, i) in skills"
      :key="s.name"
      :name="s.name"
      :alias="{ en: s.en, ja: s.ja }"
      :icon="asset(`skill/chen_${i + 1}.png`)"
      sp-type="attack"
      :trigger="s.trigger"
      :unlock="s.unlock"
      :unlock-icon="asset(`elite/elite_${i}.png`)"
    />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 技能名 |
| `alias` | `{ en?: string | undefined; ja?: string | undefined; }` | — | 别名，跟在技能名后的灰色小字（干员页全等级表的表头）：en 英文名（Bender 大写）· ja 日文名；要放别的内容用 #alias 插槽 |
| `icon` | `string` | — | 技能图标地址（游戏 skill\_icon，64px 见方） |
| `spType` | `"auto"``"attack"``"hit"``"passive-recovery"``"passive"` | `"auto"` | 技力回复方式：决定图标描边 + 底条色与 SP 标签；auto 自动回复 · attack 攻击回复 · hit 受击回复 · passive 被动 |
| `trigger` | `"manual"``"auto"` | — | 触发方式：manual 手动触发 · auto 自动触发；不写不显示（被动技能） |
| `cost` | `string | number` | — | 技力消耗；不写不显示 |
| `init` | `string | number` | — | 初始技力；不写不显示 |
| `duration` | `string | number` | — | 持续时间：数字按秒写成「25s」，字符串原样（「—」）；不写不显示 |
| `unlock` | `string` | — | 开放条件（「技能2 · 精英一开放」），显示在右侧槽 |
| `unlockIcon` | `string` | — | 开放条件前的精英化图标地址 |
| `selected` | `boolean` | `false` | 选中（蓝框 + 左上角标，游戏内 selected\_back） |
| `locked` | `boolean` | `false` | 未解锁：图标灰化 + 锁 |
| `headingLevel` | `2``3``4``5``6` | `4` | 技能名的标题级别（干员页正文里是 3，列表 / 侧栏里一般是 4） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 技能描述（游戏原始标记可以套 AkRichText） |
| `alias` | 技能名后的别名（代替 alias），放进 .ak-skill\_\_alias |
| `header-extra` | 名称行末尾的附加内容（「未解锁」标签等） |
| `aside` | 右侧槽（开放条件之后）：技能范围等；写了就是三栏的宽卡（.ak-skill--wide） |

## CSS 实现 [​](#css-实现)

结构：`.ak-skill > .ak-skill__icon + div( .ak-skill__head( __name [+ __alias] + .ak-sp + .ak-sp-trigger ) + .ak-skill__stats ) [+ .ak-skill__aside] + .ak-skill__desc`；别名是 `span.ak-skill__alias > span.ak-en + span`（英文名 + 日文名）。技能名是标题元素（`heading-level`，默认 `h4`；干员页正文里是 `h3`），MW 章节标题的装饰由 `title-reset.css` 去掉。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
