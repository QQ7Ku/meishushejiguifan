# 关卡 Stage

> 来源：https://mooncellwiki.github.io/prts-design/arknights/stage
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

关卡卡片：编号用展示字大号放左列，右列是关卡名和一行元信息（理智、推荐等级）。给了 `href` 时整张卡是链接，悬停时描边变成强调色。关卡页页头、档案里「前往关卡页」、首页「近期新增」都用它。

## 关卡类型 [​](#关卡类型)

`variant` 给磨难（`hard`）/ EX（`ex`）/ 剧情（`story`）加 4px 左色条，和 1px 边框直角相接；普通关卡不加。编号下的小字写 `caption`（章节 / 类型）。元信息行里内置两项：`sanity` 理智（六边形 + 数字）、`level` 推荐等级（显示成「推荐 **LV.30**」），其余内容放进 `#meta` 插槽，追加在行末。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-stage--variants)

vue

```
<script setup lang="ts">
import AkStage from "../AkStage.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkStage href="#" code="1-7" caption="Main · Chapter 1" name="暴君" :sanity="6" level="LV.30" />
    <AkStage href="#" code="H6-4" caption="Hard" variant="hard" name="乌萨斯的孩子们" :sanity="21" level="E2 LV.40" />
    <AkStage href="#" code="TO-EX-8" caption="EX" variant="ex" name="有袋鼷兽奇遇">
      <template #meta><span>SideStory · 直到大地变成一颗酸橙</span></template>
    </AkStage>
    <AkStage href="#" code="RF-ST-1" caption="Story" variant="story" name="怒号光明">
      <template #meta><span>剧情关卡</span></template>
    </AkStage>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 紧凑 [​](#紧凑)

没有元信息（不写 `sanity` / `level` / `#meta`）时，关卡名直接作网格的第二列，可以单独省略号截断，适合首页「近期新增」这种按活动 / 章节分好组的格子。这种列表里关卡码已经说明了类型，就不再加色条，否则信息重复。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-stage--compact)

vue

```
<script setup lang="ts">
import AkStage from "../AkStage.vue";

/** 首页「近期新增」：SideStory「直到大地变成一颗酸橙」· 踏上归家长途 */
const stages = [
  ["TO-EX-1", "电影防沉迷"],
  ["TO-EX-2", "邮包流水线"],
  ["TO-EX-3", "地下捉迷藏"],
  ["TO-EX-4", "处处是一撮毛"],
  ["TO-EX-5", "“勒柄嘘混”"],
  ["TO-EX-6", "信使？术师！"],
  ["TO-EX-7", "卡特斯三个窝"],
  ["TO-EX-8", "有袋鼷兽奇遇"],
] as const;
</script>

<template>
  <div class="ak-grid-auto ak-gap-2">
    <AkStage v-for="[code, name] in stages" :key="code" href="#" :code="code" :name="name" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 行内关卡号 · 理智 [​](#行内关卡号-·-理智)

`AkStageCode` 是正文 / 解锁条件里的关卡号（黑白反转的展示字小块，`variant="hard"` 为红底），关卡号写在默认插槽里；`AkSanity` 是单独的理智消耗。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-stage--inline)

vue

```
<script setup lang="ts">
import AkSanity from "../AkSanity.vue";
import AkStageCode from "../AkStageCode.vue";
</script>

<template>
  <ul>
    <li>3星通关主题曲 <AkStageCode href="#">5-8</AkStageCode>；必须编入非助战陈并上场，且使用陈至少歼灭16名敌人</li>
    <li>情报资料一：通关主题曲 <AkStageCode href="#">2-2</AkStageCode> 解锁</li>
    <li>磨难险地 <AkStageCode variant="hard">H6-4</AkStageCode>「乌萨斯的孩子们」消耗 <AkSanity :value="21" /></li>
  </ul>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- 整块是链接的 `AkStage` / `AkStageCode` 会自动加 `ak-not-prose`，否则正文里的 `a:visited`（特指度 0,1,1）会把卡片文字 / 反白字染成已访问的链接色。
- 理智的六边形是 CSS 画的剪影，读屏器读不到。`AkSanity` 在数字前放了一段 sr-only 的「理智」，读出来是「理智 21」，不会只剩一个数字。

## Vue API [​](#vue-api)

### AkStage [​](#akstage)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `code`必填 | `string` | — | 关卡编号（1-7 / H6-4 / TO-EX-1），展示字大号 |
| `name` | `string` | — | 关卡名 |
| `caption` | `string` | — | 编号下的小字：章节 / 类型（Main · Chapter 1 / Hard / Story） |
| `variant` | `"default"``"hard"``"ex"``"story"` | `"default"` | 左侧类型色条：hard 磨难红 · ex 深红 · story 灰；default 无色条。按活动分好组的列表里关卡码已说明类型，不必再上色 |
| `sanity` | `string | number` | — | 理智消耗：元信息行里的六边形 + 数字 |
| `level` | `string` | — | 推荐等级（LV.30 / E2 LV.40），元信息行里显示为「推荐 LV.30」 |
| `href` | `string` | — | 链接到关卡页：整张卡是链接（悬停描边变强调色） |

| 插槽 | 说明 |
| --- | --- |
| `meta` | 元信息行末尾追加的项（剧情关卡 / 前往关卡页 »），每项一个元素，行内 flex 间距 10 |

### AkStageCode [​](#akstagecode)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"default"``"hard"` | — | hard = 红底（磨难 / 险地关卡号） |
| `href` | `string` | — | 链接到关卡页 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 关卡号（5-8 / H6-4） |

### AkSanity [​](#aksanity)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value`必填 | `string | number` | — | 消耗的理智 |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
