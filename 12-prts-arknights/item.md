# 道具 Item

> 来源：https://mooncellwiki.github.io/prts-design/arknights/item
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

**游戏道具图标原样，图就用现网拼好的**：`<img>` 直接放 prts.wiki `文件:道具_带框_<名>.png`（游戏底图 sprite\_item\_r\* + 图标合成，183×183，每个道具都有；现网 `Template:道具图标` 输出的就是它），占满框，皮肤不自己叠框。数量是骑在框沿上的描边数字（同现网 `.prts-item-quantity-label`，晕色跟主题）。

这是整套系统里除单选之外唯一的圆：它不是 UI 盒子，而是游戏素材本身。

## 合成图（默认） [​](#合成图-默认)

`insufficient` 数量不足（红字）。悬停提示默认是道具名，`tip` 可改写（如「固源岩 ×12」），`tip=false` 关掉。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-item--framed)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkItem from "../AkItem.vue";
import AkItemList from "../AkItemList.vue";

const f = (id: string) => asset(`item/framed/${id}.png`);
</script>

<template>
  <AkItemList>
    <AkItem :src="f('30011')" name="源岩" :count="6" />
    <AkItem :src="f('30012')" name="固源岩" :count="12" />
    <AkItem :src="f('30013')" name="固源岩组" :count="5" />
    <AkItem :src="f('30014')" name="提纯源岩" :count="3" />
    <AkItem :src="f('30135')" name="D32钢" :count="2" insufficient />
    <AkItem :src="f('4002')" name="至纯源石" :count="1" />
    <AkItem :src="f('4001')" name="龙门币" count="30K" />
    <AkItem :src="f('3223')" name="近卫双芯片" :count="4" />
    <AkItem :src="f('mod_unlock_token')" name="模组数据块" :count="4" />
  </AkItemList>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 · 状态 · 行内 [​](#尺寸-·-状态-·-行内)

`sm` 40 · 默认 56 · `lg` 76；`AkItemList` 的 `size` 给整排定默认尺寸（道具自己写了 `size` 以自己为准，同按钮组；[材料表](/prts-design/arknights/materials)的每行就是这样给成 `sm`）；`disabled` 去色；`inline` 是放进正文的 22px 小图 + 文字：文字默认是「×`count`」（也可以写在默认插槽里），`href` / `tip` / `insufficient` 照常生效，`size` / `avatar` 不适用。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-item--sizes)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkItem from "../AkItem.vue";
import AkItemList from "../AkItemList.vue";

const f = (id: string) => asset(`item/framed/${id}.png`);
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <AkItemList>
      <AkItem :src="f('3302')" name="技巧概要·卷2" :count="8" size="sm" />
      <AkItem :src="f('3303')" name="技巧概要·卷3" :count="8" />
      <AkItem :src="f('2004')" name="高级作战记录" count="99+" size="lg" />
      <AkItem :src="f('30104')" name="RMA70-24" :count="0" disabled />
      <AkItem :src="f('4001')" name="龙门币" :count="30000" inline />
      <AkItem :src="f('30135')" name="D32钢" :count="2" insufficient inline />
    </AkItemList>
    <!-- 整排定尺寸：AkItemList 的 size 给里面没写 size 的道具；道具自己写了以自己为准（同 AkButtonGroup） -->
    <AkItemList size="sm">
      <AkItem :src="f('30011')" name="源岩" :count="6" />
      <AkItem :src="f('30012')" name="固源岩" :count="12" />
      <AkItem :src="f('30013')" name="固源岩组" :count="5" />
      <AkItem :src="f('30014')" name="提纯源岩" :count="3" size="md" />
    </AkItemList>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 裸图标 [​](#裸图标)

手里只有 torappu 的透明图标（解包数据 / 搜索面板）时：`bare` 让 CSS 按 `rarity` 1–6 叠游戏的稀有度底框（白 / 绿 / 蓝 / 紫 / 金 / 特殊，= 现网 `文件:道具_背景_N.png`），图标占 94%。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-item--bare)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkItem from "../AkItem.vue";
import AkItemList from "../AkItemList.vue";

const items = [
  { id: "30011", name: "源岩", count: 6 },
  { id: "30012", name: "固源岩", count: 12 },
  { id: "30013", name: "固源岩组", count: 5 },
  { id: "30014", name: "提纯源岩", count: 3 },
  { id: "30135", name: "D32钢", count: 2 },
  { id: "4002", name: "至纯源石", count: 1 },
] as const;
</script>

<template>
  <AkItemList>
    <AkItem
      v-for="(it, i) in items"
      :key="it.id"
      bare
      :rarity="(i + 1) as 1 | 2 | 3 | 4 | 5 | 6"
      :src="asset(`item/${it.id}.png`)"
      :name="it.name"
      :count="it.count"
    />
  </AkItemList>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API [​](#vue-api)

### AkItem [​](#akitem)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src`必填 | `string` | — | 图片地址：默认是现网拼好的「道具\_带框\_<名>.png」（底框 + 图标）；bare 时是裸图标 |
| `name`必填 | `string` | — | 道具名：作 alt，并默认作悬停提示 |
| `count` | `string | number` | — | 数量：数字或写好的串（"30K" / "99+"）；不写不显示。inline 时显示成图后的「×数量」 |
| `insufficient` | `boolean` | `false` | 数量不足（红字） |
| `size` | `"md"``"sm"``"lg"` | — | 尺寸：sm 40 · md 56 · lg 76（px）；不写时跟 AkItemList 的 size（材料表里是 sm），再不然是 md；inline 时固定 22 |
| `bare` | `boolean` | `false` | 裸图标：底框由 CSS 按 rarity 画（只有 torappu 透明图标、没有合成图时用） |
| `rarity` | `1``2``3``4``5``6` | — | bare 时的底框稀有度 1–6（白 / 绿 / 蓝 / 紫 / 金 / 特殊） |
| `disabled` | `boolean` | `false` | 去色（未获得 / 不可用） |
| `avatar` | `string` | — | 招聘合同那种：叠在底图空框里的干员头像；inline 时不显示 |
| `href` | `string` | — | 链接到道具页 |
| `tip` | `string | false` | — | 悬停提示（data-ak-tip）：默认是 name，可改写；false 关掉 |
| `inline` | `boolean` | `false` | 行内：22px 小图 + 文字，放在正文里（龙门币×30000）；文字默认是「×count」，可用默认插槽改写 |

| 插槽 | 说明 |
| --- | --- |
| `default` |  |

### AkItemList [​](#akitemlist)

一排道具：换行，间距 6 / 8。

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `size` | `"md"``"sm"``"lg"` | — | 里面道具的默认尺寸（道具自己写了 size 以道具为准，同 AkButtonGroup） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkItem |

## CSS 实现 [​](#css-实现)

结构 `.ak-item( .ak-item--sm / --lg / .is-disabled ) > img + .ak-item__count( .is-short )`；尺寸只改私有变量 `--_s`，框 / 图 / 数量字号 / 落点按比例走。数量是骑在框沿右下的粗体描边数字（现网 `.prts-item-quantity-label` 的做法：亮色黑字白晕、暗色白字黑晕，晕色取 `--ak-bg-surface`）。招聘合同 = 现网 `招聘合同_5.png` 底图（留着一个空方框）+ `img.ak-item__avatar` 把干员头像叠进方框（同现网 60px 框 / 30px 头像的落点）。早先自己画的「稀有度色方框 + 黑底数量角标」已经换掉，`--round` 随之移除；圆框是游戏素材本身（见[设计理念](/prts-design/foundations/principles)）。

材料表 `.ak-materials`（阶段 → 材料行）也在这里。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
