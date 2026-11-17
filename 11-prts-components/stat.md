# 统计数字 Stat

> 来源：https://mooncellwiki.github.io/prts-design/components/stat
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

一个关键数字：小号大写的名称（overline）+ 大号 Bender 等宽数字 + 可选的升降变化量。顶边 2px 主色条的小卡片（最窄 120），一排放进 `AkStatRow` 自动铺满、放不下换行；`inline` 是一行「名称 数值」，放进正文 / 表格。

## 一排统计 · 变化量 · 单位 [​](#一排统计-·-变化量-·-单位)

写法同 Naive UI 的 `n-statistic`：`label` 名称、`value` 数值（原样显示，千分位由调用方先排好），`#prefix` / `#suffix` 插槽放数值前后的东西——`#suffix` 渲染成半号小字（`2.3k` 的 k）。`delta` 是变化量，颜色取游戏富文本的升 / 降色，方向默认按开头的 `+` / `-` 判断，`trend` 可显式指定。

[Storybook ↗](/prts-design/storybook/?path=/story/components-stat--basic)

vue

```
<script setup lang="ts">
import AkStat from "../AkStat.vue";
import AkStatRow from "../AkStatRow.vue";
</script>

<template>
  <AkStatRow>
    <AkStat label="Operators" :value="356" delta="+4 本月" />
    <AkStat label="Stages" value="1,204" />
    <AkStat label="Edits / 24h" value="2.3" delta="-12%">
      <template #suffix>k</template>
    </AkStat>
  </AkStatRow>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 行内 [​](#行内)

[Storybook ↗](/prts-design/storybook/?path=/story/components-stat--inline)

vue

```
<script setup lang="ts">
import AkStat from "../AkStat.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-baseline">
    <AkStat inline label="干员" :value="356" />
    <AkStat inline label="关卡" value="1,204" />
    <AkStat inline label="今日编辑" value="2.3" delta="+8%">
      <template #suffix>k</template>
    </AkStat>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

### AkStat [​](#akstat)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `label` | `string` | — | 名称（小号大写 overline：Operators / Edits / 24h）；要带格式时用 #label 插槽 |
| `value` | `string | number` | — | 数值，原样显示（千分位等格式由调用方先排好：「1,204」）；也可以写在默认插槽里 |
| `delta` | `string` | — | 变化量（「+4 本月」「-12%」），显示在数值下面 |
| `trend` | `"up"``"down"` | — | 变化方向（颜色取游戏富文本的升 / 降色）；不写时按 delta 开头的 + / - 判断 |
| `inline` | `boolean` | `false` | 行内：一行「名称 数值」，不要卡片框（放进正文 / 表格里） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 数值（代替 value） |
| `label` | 名称（代替 label） |
| `prefix` | 数值前面的内容（货币符号、图标） |
| `suffix` | 数值后面的小字单位（渲染成 <small>：2.3<small>k</small>） |

### AkStatRow [​](#akstatrow)

一排统计卡片（`div.ak-stat-row`）：`repeat(auto-fit, minmax(120px, 1fr))`，间距 12。只有默认插槽。

## CSS 实现 [​](#css-实现)

`div.ak-stat > span.ak-stat__label + span.ak-stat__value( 数字 + small 单位 ) + span.ak-stat__delta(--up | --down)`。`.ak-stat` 自带 `box-sizing: border-box`，窄屏不会撑破容器。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
