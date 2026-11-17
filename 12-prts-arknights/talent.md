# 天赋卡 Talent

> 来源：https://mooncellwiki.github.io/prts-design/arknights/talent
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

一个天赋的摘要：名称 + 解锁条件（Bender 小字 + 精英化 / 潜能图标）+ 描述，左侧强调色条、浅底。给列表、侧栏这类只看最终效果的场合；**干员页正文用[天赋条件表](/prts-design/arknights/talent-table)**，一张表列完各阶段的效果。

## 天赋卡 [​](#天赋卡)

`requirements` 是跟在名字后面的解锁条件（`{ icon, text }`，图标地址由调用方给）；描述写在默认插槽里，游戏原始标记套一层 [AkRichText](/prts-design/arknights/rich-text)（潜能加成 `<@ba.talpu>` 是增益蓝）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-talent--basic)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkRichText from "../../RichText/AkRichText.vue";
import AkTalent from "../AkTalent.vue";

/** 陈的两个天赋（精英二满潜能时的效果） */
const e2 = { icon: asset("elite/elite_2.png"), text: "精英二 · 1级" };
</script>

<template>
  <div>
    <AkTalent name="呵斥" :requirements="[e2]">在场时每4秒回复全场友方角色1点攻击/受击技力</AkTalent>
    <AkTalent name="持刀格斗术" :requirements="[e2, { icon: asset('potential/potential_4.png'), text: '潜能5' }]">
      <AkRichText text="攻击力+6%<@ba.talpu>（+1%）</>，防御力+6%<@ba.talpu>（+1%）</>，物理闪避+13%<@ba.talpu>（+3%）</>" />
    </AkTalent>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 天赋名 |
| `requirements` | `TalentRequirement[]` | `[]` | 解锁条件，跟在名字后面（小号标签字） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 描述（游戏原始标记可以套 AkRichText） |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
