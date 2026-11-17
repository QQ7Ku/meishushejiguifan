# 属性面板 Attrs

> 来源：https://mooncellwiki.github.io/prts-design/arknights/attrs
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

干员属性读数，照游戏内 HUD 的读法：Bender 大号数字 + overline 小标（英文缩写 + 中文名）。等宽网格（每格最窄 140px，放不下换行），格间 1px 缝线。干员页「属性」节的属性计算器（现网 `Widget:PropertyCalc`）用它显示读数；按精英阶段 / 等级列的四档属性表仍是 wikitable。

写法：`AkAttrs` 里放若干 `AkAttr`，一格一个属性（思路同 Naive UI 的 `n-statistic`：小标签 + 数值 + 单位）。

## 属性面板 [​](#属性面板)

四维（生命 / 攻击 / 防御 / 法抗）加 `accent`（顶部 2px 主色线），部署类（费用 / 阻挡 / 攻击间隔 / 再部署）不加；单位写在 `unit`（数值后的小号灰字）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-attrs--panel)

vue

```
<script setup lang="ts">
import AkAttr from "../AkAttr.vue";
import AkAttrs from "../AkAttrs.vue";

/** 陈 · 精英二满级：四维（强调）+ 部署四项 */
const stats = [
  { name: "HP", zh: "生命上限", value: 2880, accent: true },
  { name: "ATK", zh: "攻击", value: 610, accent: true },
  { name: "DEF", zh: "防御", value: 352, accent: true },
  { name: "RES", zh: "法术抗性", value: 0, accent: true },
  { name: "COST", zh: "部署费用", value: 23 },
  { name: "BLOCK", zh: "阻挡数", value: 2 },
  { name: "INTERVAL", zh: "攻击间隔", value: 1.3, unit: "s" },
  { name: "REDEPLOY", zh: "再部署", value: 70, unit: "s" },
];
</script>

<template>
  <AkAttrs>
    <AkAttr v-for="s in stats" :key="s.name" v-bind="s" />
  </AkAttrs>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 紧凑 [​](#紧凑)

`compact`：名 / 值左右排开，数值降到正文字号——表格侧栏读数、人员档案里的「综合体检测试」（属性名直接写中文）。要上色的值写在默认插槽里。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-attrs--compact)

vue

```
<script setup lang="ts">
import AkAttr from "../AkAttr.vue";
import AkAttrs from "../AkAttrs.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <AkAttrs compact>
      <AkAttr name="HP" :value="2880" />
      <AkAttr name="ATK" :value="610" />
      <AkAttr name="DEF" :value="352" />
    </AkAttrs>
    <!-- 人员档案「综合体检测试」：中文属性名；要上色的值写在默认插槽里 -->
    <AkAttrs compact>
      <AkAttr name="物理强度" value="优良" />
      <AkAttr name="战场机动" value="标准" />
      <AkAttr name="生理耐受" value="优良" />
      <AkAttr name="战术规划" value="优良" />
      <AkAttr name="战斗技巧"><span class="ak-fg-accent">卓越</span></AkAttr>
      <AkAttr name="源石技艺适应性" value="标准" />
    </AkAttrs>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

### AkAttrs [​](#akattrs)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `compact` | `boolean` | `false` | 紧凑：名 / 值左右排开、数值缩到正文字号（档案里的综合体检、侧栏读数） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkAttr |

### AkAttr [​](#akattr)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 属性名：英文缩写（HP / ATK …，Bender 大写小字）或直接写中文（物理强度） |
| `zh` | `string` | — | 中文名：跟在英文缩写后，正文字体（生命上限） |
| `value` | `string | number` | — | 数值；要上色或放别的内容时改用默认插槽 |
| `unit` | `string` | — | 单位：数值后的小号灰字（s） |
| `accent` | `boolean` | `false` | 强调：顶部 2px 主色线（面板里的四维用，部署类属性不用） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 数值（代替 value） |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
