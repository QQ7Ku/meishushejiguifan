# SP 标签 Sp

> 来源：https://mooncellwiki.github.io/prts-design/arknights/sp
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

技能的技力（SP）信息，三种小件：

- **SP 类型** `AkSp`：技力怎么回复，颜色同游戏——绿 = 自动回复、橙 = 攻击回复、黄 = 受击回复、紫 = 被动回复（仅在特殊条件下回复技力）、灰 = 被动；色值与白字照现网 `{{技能类型}}`（`#8EC31F` / `#FC793E` / `#F4AF09` / `#C289E3` / `#808080`），玩家认的就是这套；
- **触发方式** `AkSpTrigger`：手动触发、自动触发都是灰底白字（现网两者同为 `#808080`），靠文字区分；
- **数值芯片** `AkSpValue`：消耗（游戏 `skill_sp_cost_bkg` 的荧光绿闪电）、初始（▶）、持续（⏱）。三枚同高同内距，图形是单色 SVG mask（不用 emoji——彩色闪电字形基线不稳，两枚并排会错半像素）。

[技能卡](/prts-design/arknights/skill)、[全等级表](/prts-design/arknights/skill-sheet)、[参数矩阵](/prts-design/arknights/skill-matrix)都由它们拼成。

## SP 类型 · 触发 

默认文字就是游戏里的写法，也可以写在默认插槽里改写。悬停提示默认是游戏内的说明（「每次攻击回复1点技力」「技力达到需求并满足开启条件后，需要玩家手动开启的技能」），`tip` 改写、`tip=false` 关掉；被动没有技力，默认不给提示。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-sp--types)

vue

```
<script setup lang="ts">
import AkSp from "../AkSp.vue";
import AkSpTrigger from "../AkSpTrigger.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkSp />
      <AkSp sp-type="attack" />
      <AkSp sp-type="hit" />
      <AkSp sp-type="passive-recovery" />
      <AkSp sp-type="passive" />
    </div>
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkSpTrigger />
      <AkSpTrigger auto />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 数值芯片 

`value` 是数值；表头图例写字（「消耗」）时写在默认插槽里。悬停提示默认是这一项的解释（「消耗：该技能所需要消耗的技力数」）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-sp--values)

vue

```
<script setup lang="ts">
import AkSpValue from "../AkSpValue.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <!-- 陈 · 赤霄·绝影 7 级：消耗 36、初始 10；持续给真银斩（25 秒）作例 -->
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkSpValue kind="cost" :value="36" />
      <AkSpValue kind="init" :value="10" />
      <AkSpValue kind="duration" value="25s" />
    </div>
    <!-- 表头图例：芯片里写字 -->
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-center">
      <AkSpValue kind="init">初始</AkSpValue>
      <AkSpValue kind="cost">消耗</AkSpValue>
      <AkSpValue kind="duration">持续</AkSpValue>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

白字在绿 / 黄底上对比度不到 4.5:1——为了与游戏和现网一致保留了这套配色；每枚标签都有悬停说明（游戏内的原话），信息不单靠颜色。

芯片的图形是 CSS mask，读屏读不出来：写 `value` 时芯片里在数字前补一段 `.ak-sr-only` 的名字（读作「消耗36」）。一两句话的说明提示用可折行的宽气泡 `.ak-tip--wide`。

## Vue API 

### AkSp 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `spType` | `"auto"``"attack"``"hit"``"passive-recovery"``"passive"` | `"auto"` | 技力回复方式：auto 自动回复（绿）· attack 攻击回复（橙）· hit 受击回复（黄）· passive-recovery 被动回复（紫）· passive 被动（灰） |
| `tip` | `string | false` | — | 悬停提示（data-ak-tip）：默认是游戏内的说明（「每次攻击回复1点技力」），可改写；false 关掉 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字；默认「自动回复」「攻击回复」「受击回复」「被动回复」「被动」 |

### AkSpTrigger 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `auto` | `boolean` | `false` | 自动触发（灰底）；默认手动触发（黑白反转） |
| `tip` | `string | false` | — | 悬停提示（data-ak-tip）：默认是游戏内的说明，可改写；false 关掉 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字；默认「手动触发」/「自动触发」 |

### AkSpValue 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `kind`必填 | `"duration"``"cost"``"init"` | — | 哪一枚芯片：cost 消耗（荧光绿闪电）· init 初始（▶）· duration 持续（⏱） |
| `value` | `string | number` | — | 数值；也可以写在默认插槽里（表头图例写「消耗」「初始」这种字） |
| `tip` | `string | false` | — | 悬停提示（data-ak-tip）：默认是这一项的说明（「消耗：该技能所需要消耗的技力数」），可改写；false 关掉 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 芯片里的字（代替 value） |

## CSS 实现 

颜色令牌：`--ak-sp-auto` / `--ak-sp-attack` / `--ak-sp-hit` / `--ak-sp-passive`。芯片落进表格单元格时字体回正文（`table-numerals.css` 的表格硬规则，见[全等级表](/prts-design/arknights/skill-sheet#表格里的数字)）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
