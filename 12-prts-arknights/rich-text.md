# 游戏内富文本 RichText

> 来源：https://mooncellwiki.github.io/prts-design/arknights/rich-text
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

技能 / 天赋 / 关卡描述里的配色标记。游戏数据（`gamedata_const.richTextStyles`）里写成 `<@ba.vup>+50%</>`，页面上对应 `.ak-rt-*` 类：配色取游戏内色，亮色主题下校正到 AA 对比度。Lua / 模板把 `<@ba.vup>…</>` 转成 `<span class="ak-rt-vup">…</span>` 即可；Vue 版 `AkRichText` 直接吃游戏原始标记。

| 标记 | 类 | 含义 |
| --- | --- | --- |
| `<@ba.vup>` | `.ak-rt-vup` | 增益（数值） |
| `<@ba.vdown>` | `.ak-rt-vdown` | 减益 |
| `<@ba.rem>` | `.ak-rt-rem` | 提醒（伤害类型等） |
| `<@ba.kw>` | `.ak-rt-kw` | 关键词 |
| `<@ba.talpu>` | `.ak-rt-talpu` | 潜能加成（同增益色） |
| `<$ba.stun>` 等 | `.ak-rt-term` | 术语：虚线下划线 + 悬停说明 |
| `<@tu.imp>` | `.ak-rt-imp` | 重要提示 |
| `<@ba.enemy>` · `<@ba.gild>` · `<@ba.drop>` · `<@ba.acrem>` | `.ak-rt-enemy` 等 | 敌方 · 镀层等，同名游戏样式 |
| `mission.levelname` | `.ak-rt-level` | 关卡名 |
| `<@ba.pn>` | `.ak-rt-pn` | 斜体 |

## 游戏标记 [​](#游戏标记)

`text` 是原始标记：样式标签按名字认（命名空间不论，`cc.vup` 与 `ba.vup` 同色；认不得的样式只留文字），可以嵌套，没闭合的在末尾自动闭合；换行变 `<br>`。术语 `<$ba.xxx>` 的悬停说明来自 `terms`（`termDescriptionDict`，键是完整的 `ba.stun`）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-richtext--tags)

vue

```
<script setup lang="ts">
import AkRichText from "../AkRichText.vue";

/** gamedata_const.richTextStyles 里常用的几种：原始标记 → 渲染结果 */
const samples = [
  { tag: "<@ba.vup>", text: "攻击力<@ba.vup>+50%</>" },
  { tag: "<@ba.vdown>", text: "防御力<@ba.vdown>-30%</>" },
  { tag: "<@ba.rem>", text: "造成<@ba.rem>法术</>伤害" },
  { tag: "<@ba.kw>", text: "普通攻击连续造成<@ba.kw>两次</>伤害" },
  { tag: "<@ba.talpu>", text: "攻击力+6%<@ba.talpu>（+1%）</>" },
  { tag: "<$ba.stun>", text: "令目标<$ba.stun>晕眩</>1.5秒" },
  { tag: "<@tu.imp>", text: "<@tu.imp>注意：</>本关卡无法撤退" },
  { tag: "<@ba.enemy>", text: "<@ba.enemy>敌方</>单位" },
  { tag: "<@ba.gild>", text: "<@ba.gild>镀层</>" },
  { tag: "<@mission.levelname>", text: "通关<@mission.levelname>1-7</>" },
];
const terms = { "ba.stun": "晕眩：无法移动、阻挡、攻击及使用技能" };
</script>

<template>
  <table class="wikitable ak-compact">
    <thead>
      <tr><th>标签</th><th>原始标记</th><th>渲染</th></tr>
    </thead>
    <tbody>
      <tr v-for="s in samples" :key="s.tag">
        <td><code>{{ s.tag }}</code></td>
        <td><code>{{ s.text }}</code></td>
        <td><AkRichText :text="s.text" :terms="terms" /></td>
      </tr>
    </tbody>
  </table>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 占位符 · blackboard [​](#占位符-·-blackboard)

技能描述在游戏里是模板：`{atk_scale:0%}` 这类占位符由各级 blackboard 填。`vars` 给取值（键不分大小写）：`:0%` 乘 100 加 %、`:0.0` 保留一位小数，前缀 `-` 取负，没有格式就原样；没给取值的占位符原样留着。[全等级表](/prts-design/arknights/skill-sheet)就是把同一个模板配上每级的 `vars`；[参数矩阵](/prts-design/arknights/skill-matrix)用 `#var` 插槽把占位符画成可切换的变量位。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-richtext--variables)

vue

```
<script setup lang="ts">
import AkRichText from "../AkRichText.vue";

/** 陈 · 鞘击：技能描述模板（skill_table 原样）+ 各级的 blackboard */
const template =
  "下次攻击使用刀鞘砸向敌人，造成相当于攻击力<@ba.vup>{atk_scale:0%}</>的物理伤害，令命中目标<$ba.stun>晕眩</><@ba.vup>{stun}</>秒";
const terms = { "ba.stun": "晕眩：无法移动、阻挡、攻击及使用技能" };
</script>

<template>
  <div class="ak-flex-col ak-gap-2">
    <p class="ak-m-0"><b>1 级</b>　<AkRichText :text="template" :vars="{ atk_scale: 2, stun: 1 }" :terms="terms" /></p>
    <p class="ak-m-0"><b>7 级</b>　<AkRichText :text="template" :vars="{ atk_scale: 2.6, stun: 1.5 }" :terms="terms" /></p>
    <p class="ak-m-0"><b>专精 Ⅲ</b>　<AkRichText :text="template" :vars="{ atk_scale: 3.2, stun: 1.5 }" :terms="terms" /></p>
    <p class="ak-m-0 ak-fg-muted ak-fs-sm">没给取值的占位符原样留着：<AkRichText :text="template" :terms="terms" /></p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 只包一层 [​](#只包一层)

手写正文时不必拼游戏标记：不写 `text`，用 `variant` 包住默认插槽；术语（`variant="term"`）的说明写在 `tip`。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-richtext--wrap)

vue

```
<script setup lang="ts">
/* 手写正文时不必拼游戏标记：variant 包一层 */
import AkRichText from "../AkRichText.vue";

const tip = "晕眩：无法移动、阻挡、攻击及使用技能";
</script>

<template>
  <ul class="ak-m-0">
    <li>进驻会客室时，线索搜集速度提升<AkRichText variant="vup">25%</AkRichText></li>
    <li>攻击范围内的敌人防御力<AkRichText variant="vdown">-30%</AkRichText></li>
    <li>造成相当于攻击力 410% 的<AkRichText variant="rem">物理</AkRichText>和<AkRichText variant="rem">法术</AkRichText>伤害</li>
    <li>令命中目标<AkRichText variant="term" :tip="tip">晕眩</AkRichText>1.5秒</li>
    <li><AkRichText variant="imp">注意：</AkRichText>本关卡无法撤退</li>
  </ul>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 数值变化（CSS） [​](#数值变化-css)

纯 CSS 的小件，没有 Vue 版：`.ak-delta-up` / `.ak-delta-down` 在数值后面跟一枚 ▲ / ▼（增益 / 减益色），用于版本对比、模组前后对比。

html

```
<p>攻击力 <b class="ak-delta-up">610</b>　防御力 <b class="ak-delta-down">352</b>　再部署 <b class="ak-delta-down">70s</b></p>
```

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `text` | `string` | — | 游戏原始标记：<@ba.vup>…</> 样式 · <$ba.stun>…</> 术语 · {atk\_scale:0%} 占位符 · 换行；不写就渲染默认插槽 |
| `vars` | `Record<string, string | number>` | — | 占位符的取值（技能等级的 blackboard，如 { atk\_scale: 2.6 }）；键不分大小写，没给的占位符原样留着 |
| `terms` | `Record<string, string>` | — | 术语说明（termDescriptionDict，如 { "ba.stun": "晕眩：…" }），作 <$…> 的悬停提示 |
| `variant` | `"level"``"vup"``"vdown"``"rem"``"kw"``"talpu"``"term"``"imp"``"enemy"``"gild"``"drop"``"acrem"``"pn"` | — | 只包一层、不解析时的样式：vup 增益 · vdown 减益 · rem 提醒 · kw 关键词 · talpu 潜能加成 · term 术语 · imp 重要 · enemy 敌方 · gild 镀层 · level 关卡名 · pn 斜体 … |
| `tip` | `string` | — | 悬停提示（data-ak-tip）；variant="term" 时写术语说明 |
| `tag` | `string` | `"span"` | 外层元素 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 不写 text 时的内容（用 variant 包一层） |
| `var` | 占位符怎么画（技能参数矩阵用它把变量位画成 .ak-var）；不写就按 vars 填数 |

## CSS 实现 [​](#css-实现)

每个 `.ak-rt-*` 都有同名别名 `.ba-*`（`.ba-vup`，直接对应游戏标签名；`<@tu.imp>` 是 `.tu-imp`），以及语义别名 `.ak-buff` = 增益、`.ak-debuff` = 减益。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
