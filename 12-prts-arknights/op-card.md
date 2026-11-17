# 干员卡 Op Card

> 来源：https://mooncellwiki.github.io/prts-design/arknights/op-card
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

游戏干员列表那张卡：方形头像 + 左上职业 / 右下星级 / 左下精英阶段三个角标（排法同现网 [微件:干员头像](https://prts.wiki/w/%E5%BE%AE%E4%BB%B6:%E5%B9%B2%E5%91%98%E5%A4%B4%E5%83%8F)）+ 名字；头像垫稀有度色的渐变底（[稀有度 · 头像底](/prts-design/arknights/rarity#头像底)），一排里混着几种星级时扫一眼底色就能分开，名字上方不再拉一道同色的线。三个角标都垫半透明黑底：黄星、白色图标压在往下褪成的浅灰 / 奶油色上，不垫看不清；连同右上角的额外角标，四个角标都贴角、不内缩（同现网）。悬停上浮 3px、描边变青。整张卡是一个链接（`href`），外层带 `ak-not-prose`，正文里不会被 `a:visited` 染色。

素材全部由调用方传：`avatar` 头像、`rarity-icon` 星级原图、`profession-icon` 职业图标、`elite-icon` 精英图标；`profession` / `elite` 给对应角标的 `alt`。

## 网格 [​](#网格)

`AkOpGrid`（`.ak-op-grid`）按宽度自动排列，每列最窄 112px，卡片宽度由网格定。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-op-card--grid)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import type { Rarity } from "../../Rarity/AkRarity.vue";
import AkOpCard from "../AkOpCard.vue";
import AkOpGrid from "../AkOpGrid.vue";

const PROF = { warrior: "近卫", medic: "医疗", sniper: "狙击", pioneer: "先锋", caster: "术师" } as const;

const ops: { id: string; name: string; sub: string; rarity: Rarity; prof: keyof typeof PROF; elite: 0 | 1 | 2 }[] = [
  { id: "char_010_chen_2", name: "陈", sub: "Ch'en · LM04", rarity: 6, prof: "warrior", elite: 2 },
  { id: "char_172_svrash_2", name: "银灰", sub: "SilverAsh · KJ01", rarity: 6, prof: "warrior", elite: 2 },
  { id: "char_003_kalts_2", name: "凯尔希", sub: "Kal'tsit · R001", rarity: 6, prof: "medic", elite: 2 },
  { id: "char_103_angel_2", name: "能天使", sub: "Exusiai · PL02", rarity: 6, prof: "sniper", elite: 2 },
  { id: "char_102_texas_2", name: "德克萨斯", sub: "Texas · PL03", rarity: 5, prof: "pioneer", elite: 2 },
  { id: "char_002_amiya_1p", name: "阿米娅", sub: "Amiya · R002", rarity: 5, prof: "caster", elite: 1 },
  { id: "char_151_myrtle_2", name: "桃金娘", sub: "Myrtle · PL33", rarity: 4, prof: "pioneer", elite: 2 },
  { id: "char_118_yuki_2", name: "白雪", sub: "ShiraYuki · SN12", rarity: 3, prof: "sniper", elite: 1 },
];
</script>

<template>
  <AkOpGrid>
    <AkOpCard
      v-for="op in ops"
      :key="op.id"
      href="#"
      :name="op.name"
      :sub="op.sub"
      :avatar="asset(`avatar/${op.id}.png`)"
      :rarity="op.rarity"
      :rarity-icon="asset(`rarity/rarity_yellow_${op.rarity - 1}.png`)"
      :profession="PROF[op.prof]"
      :profession-icon="asset(`profession/${op.prof}.png`)"
      :elite="op.elite"
      :elite-icon="asset(`elite/elite_${op.elite}.png`)"
    />
  </AkOpGrid>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 [​](#尺寸)

`sm` 88 · 默认 128 · `lg` 180（px 宽，不放网格时）。`sm` 的职业角标用头像同款小图标 `profession/icon_<职业>.png`（= 现网 `图标_职业_*.png`）；首页「亮点干员」就是一排 `sm` 卡，纯头像（`avatar-only`）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-op-card--sizes)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkOpCard from "../AkOpCard.vue";

const chen = {
  name: "陈",
  avatar: asset("avatar/char_010_chen_2.png"),
  rarity: 6,
  rarityIcon: asset("rarity/rarity_yellow_5.png"),
  profession: "近卫",
  elite: 2,
  eliteIcon: asset("elite/elite_2.png"),
} as const;
</script>

<template>
  <div class="ak-flex ak-flex-col ak-gap-4">
    <div class="ak-flex ak-wrap ak-gap-3 ak-items-end">
      <AkOpCard v-bind="chen" size="sm" :profession-icon="asset('profession/icon_warrior.png')" href="#" />
      <AkOpCard v-bind="chen" sub="Ch'en · LM04" :profession-icon="asset('profession/warrior.png')" href="#" />
      <AkOpCard v-bind="chen" sub="Ch'en · LM04" size="lg" :profession-icon="asset('profession/warrior.png')" href="#" />
    </div>
    <!-- 首页「亮点干员」：sm + 头像同款小职业图标，纯头像（avatar-only：不出名字栏，名字作 alt 与悬停提示） -->
    <div class="ak-flex ak-wrap ak-gap-2 ak-items-start">
      <AkOpCard
        name="海沫"
        :avatar="asset('mainpage/avatar/haimo.png')"
        :rarity="4"
        :rarity-icon="asset('rarity/rarity_yellow_3.png')"
        profession="近卫"
        :profession-icon="asset('profession/icon_warrior.png')"
        size="sm"
        avatar-only
        href="#"
      />
      <AkOpCard
        name="珊比"
        :avatar="asset('mainpage/avatar/shanbi.png')"
        :rarity="5"
        :rarity-icon="asset('rarity/rarity_yellow_4.png')"
        profession="重装"
        :profession-icon="asset('profession/icon_tank.png')"
        size="sm"
        avatar-only
        href="#"
      />
      <AkOpCard
        name="予愿安洁莉娜"
        :avatar="asset('mainpage/avatar/angelina-wish.png')"
        :rarity="5"
        :rarity-icon="asset('rarity/rarity_yellow_4.png')"
        profession="特种"
        :profession-icon="asset('profession/icon_special.png')"
        size="sm"
        avatar-only
        href="#"
      />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 色条 · CSS 星形 · 当前项 · 角标 [​](#色条-·-css-星形-·-当前项-·-角标)

- `variant="rail"`：头像底之外另加一条稀有度色左边条，与 1px 框直角拼接。
- 不传 `rarity-icon` 时右下角画 CSS 星形（[稀有度](/prts-design/arknights/rarity)组件，与原图等大）；不传职业 / 精英图标就不显示那个角标。
- `current`：异格一览里正在看的这位——青色描边 + `aria-current="page"`。
- `#badge` 插槽放右上角的额外角标（首页凭证兑换的「兑」、模组类型小图标）。
- `avatar-only`：纯头像，不出名字栏（首页「亮点干员」）——名字改作头像的 `alt` 与悬停提示 `title`，`sub` 不显示。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-op-card--variants)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkTag from "../../Tag/AkTag.vue";
import AkOpCard from "../AkOpCard.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-start">
    <!-- rail：头像底之外另加一条稀有度色左边条 -->
    <AkOpCard
      variant="rail"
      name="煌"
      sub="Blaze · 强攻手"
      :avatar="asset('avatar/char_017_huang_2.png')"
      :rarity="6"
      :rarity-icon="asset('rarity/rarity_yellow_5.png')"
      profession="近卫"
      :profession-icon="asset('profession/warrior.png')"
      href="#"
    />
    <!-- 不传星级原图：画 CSS 星形；不传职业 / 精英图标就不显示 -->
    <AkOpCard name="年" sub="Nian · 铁卫" :avatar="asset('avatar/char_2014_nian_2.png')" :rarity="6" href="#" />
    <!-- 异格一览：sm + current（青色描边、aria-current） -->
    <div class="ak-flex ak-gap-2">
      <AkOpCard
        current
        size="sm"
        name="陈"
        title="陈（原型）"
        :avatar="asset('avatar/char_010_chen_2.png')"
        :rarity="6"
        :rarity-icon="asset('rarity/rarity_yellow_5.png')"
        profession="近卫"
        :profession-icon="asset('profession/icon_warrior.png')"
        href="#"
      />
      <AkOpCard
        size="sm"
        name="假日威龙陈"
        title="假日威龙陈"
        :avatar="asset('avatar/char_1013_chen2_2.png')"
        :rarity="6"
        :rarity-icon="asset('rarity/rarity_yellow_5.png')"
        profession="狙击"
        :profession-icon="asset('profession/icon_sniper.png')"
        href="#"
      />
    </div>
    <!-- #badge：右上角角标（首页凭证兑换的「兑」） -->
    <AkOpCard
      size="sm"
      name="逻各斯"
      sub="黄票兑换"
      :avatar="asset('mainpage/avatar/logos.png')"
      :rarity="5"
      :rarity-icon="asset('rarity/rarity_yellow_4.png')"
      profession="术师"
      :profession-icon="asset('profession/icon_caster.png')"
      href="#"
    >
      <template #badge><AkTag variant="yellow" size="sm">兑</AkTag></template>
    </AkOpCard>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

头像是装饰（`alt=""`）；三个角标的 `alt`（「六星」「近卫」「精英二」）和名字、小字一起组成链接的可访问名。`avatar-only` 时没有名字栏，头像就不再是装饰：名字写进头像的 `alt`，同一个名字也作 `title`（MediaWiki 的内链本来就带页面名作 `title`），悬停看得到是谁。异格卡这类只有名字的，`title`（透传到根元素）可以补一句「陈（原型）」。

## Vue API [​](#vue-api)

### AkOpCard [​](#akopcard)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 干员名 |
| `sub` | `string` | — | 名字下的小字（Bender 大写）：英文名 · 编号（Ch'en · LM04）/ 生日 / 获得方式 |
| `avatar`必填 | `string` | — | 头像地址（正方形，avatar/char\_xxx\_2.png） |
| `rarity`必填 | `1``2``3``4``5``6` | — | 稀有度 1–6：头像的稀有度色渐变底（rail 时另加左边条），并作右下角星级的 alt |
| `rarityIcon` | `string` | — | 右下角星级原图地址（rarity/rarity\_yellow\_{rarity−1}.png）；不传时画 CSS 星形 |
| `profession` | `string` | — | 职业名：左上角职业图标的 alt |
| `professionIcon` | `string` | — | 左上角职业图标地址：md / lg 用 profession/<职业>.png，sm 用头像同款小图标 profession/icon\_<职业>.png；不传不显示 |
| `elite` | `0``1``2` | — | 精英阶段 0–2：左下角精英图标的 alt |
| `eliteIcon` | `string` | — | 左下角精英图标地址（elite/elite\_N.png）；不传不显示 |
| `size` | `"md"``"sm"``"lg"` | `"md"` | 尺寸：sm 88 · md 128 · lg 180（px 宽）；放进 AkOpGrid 时宽度由网格定 |
| `variant` | `"default"``"rail"` | `"default"` | 外观：default 稀有度只看头像底 · rail 另加稀有度色左边条（与 1px 框直角拼接） |
| `href` | `string` | — | 链接到干员页；不写渲染成 <div> |
| `current` | `boolean` | `false` | 当前项（异格一览里正在看的这位）：青色描边 + aria-current="page" |
| `avatarOnly` | `boolean` | `false` | 纯头像（首页「亮点干员」）：不出名字栏，名字改作头像的 alt 与悬停提示（title，可用透传的 title 改写）；sub 不显示 |

| 插槽 | 说明 |
| --- | --- |
| `badge` | 头像右上角的角标（.ak-op-card\_\_badge：首页「兑」字标、模组类型小图标…） |

### AkOpGrid [​](#akopgrid)

干员卡网格，只有默认插槽。

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
