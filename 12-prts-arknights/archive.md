# 档案类卡片 Archive

> 来源：https://mooncellwiki.github.io/prts-design/arknights/archive
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

干员密录 / 悖论模拟 / 未获得时档案——现网那几张「————xx」折叠 wikitable 的替身。与[档案](/prts-design/arknights/dossier)同一张脸，多了面板头（左青条 + 小标 + 标题 + 紧跟标题的解锁条件）和页脚。

写法参考 Naive UI 的 `n-card`：`title` + 默认插槽（正文）+ `#footer`（页脚）。解锁条件是纯文本时用 `unlock`，要放精英阶段 / 信赖 / 关卡号时用 `#unlock` 插槽；前面的小标默认「解锁条件」，用 `unlock-label` 改写，传空串不出（密录 / 悖论模拟的条件本身就是「精英化图标 Lv.1 · 信赖图标 50%」）。

## 未获得时档案 

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-archive--intel)

vue

```
<script setup lang="ts">
import AkArchive from "../AkArchive.vue";

/** 游戏文本原样：换行靠 <p> 的 pre-line 保留 */
const text = `龙门近卫局警司，特别督察组组长。
负责龙门近卫局特别督察组的日常指挥执行工作。
魏彦吾的最得力的下属之一。拥有整个近卫局中最精湛的剑技以及最严苛的态度。态度极度强硬，对人一视同仁。看起来一点都不变通，总是一副凶着脸的样子。`;
</script>

<template>
  <AkArchive kicker="未获得时档案" title="情报资料一">
    <template #unlock>通关主题曲 <a class="ak-stage-code" href="#">2-2</a></template>
    <p><b>【陈】</b><br />{{ text }}</p>
  </AkArchive>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 干员密录 · 悖论模拟 

页脚放阅读 / 关卡 / 首通奖励；「小标 + 内容」成对的包一层 `.ak-archive__group`，窄屏放不下时整组换行，小标不会单独留在上一行。`AkArchivePlay` 是页脚里那块黑底「阅读 / 播放」：有 `href` 时是链接（去密录页），没有时是按钮。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-archive--record)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkItem from "../../Item/AkItem.vue";
import AkItemList from "../../Item/AkItemList.vue";
import AkArchive from "../AkArchive.vue";
import AkArchivePlay from "../AkArchivePlay.vue";

const paradox = `无论什么时候，陈总是会站在队伍的前端。
一开始，她的同事们以为她是不自量力，她也确实为此付出过一些代价。但是当陈逐渐掌握运用赤霄之后，她的同事们开始主动为她留出场地。
一是，他们不想自己被出鞘的赤霄卷进去。二是，他们已经知道，如果陈说她做得到，她就会做到。这并不是说她无所不能，而是因为她决不放弃。`;
</script>

<template>
  <div class="ak-flex-col">
    <!-- 干员密录：解锁条件同现网——精英化图标 + Lv.1、信赖图标 + 50%，不加小标；页脚是「阅读密录」 -->
    <AkArchive title="一鼓作气" unlock-label="">
      <template #unlock>
        <span class="ak-elite" title="精英二 1 级"><img :src="asset('elite/upgrade_2.png')" alt="精英二" />Lv.1</span>
        <span class="ak-trust">50%</span>
      </template>
      <p><i>一鼓作气，再而衰。</i></p>
      <template #footer>
        <span class="ak-archive__group">
          <span class="ak-overline">Story</span>
          <AkArchivePlay href="#" title="陈/干员密录/1">阅读密录 · 一鼓作气</AkArchivePlay>
        </span>
        <span class="ak-fg-muted">蚀刻章 medal_story_chen_1 · 光荣之路</span>
      </template>
    </AkArchive>
    <!-- 悖论模拟：页脚是关卡 + 首通奖励；小标与内容包 .ak-archive__group，窄屏整组换行 -->
    <AkArchive title="永不后退" unlock-label="">
      <template #unlock>
        <span class="ak-elite" title="精英二 1 级"><img :src="asset('elite/upgrade_2.png')" alt="精英二" />Lv.1</span>
      </template>
      <p>{{ paradox }}</p>
      <p><b>&lt;活性源石&gt;</b> 部署于其上的我军和经过的敌军持续受到伤害，但攻击力和攻速大幅度提升</p>
      <template #footer>
        <span class="ak-archive__group">
          <span class="ak-overline">关卡</span>
          <AkArchivePlay href="#">悖论模拟 · 永不后退</AkArchivePlay>
        </span>
        <span class="ak-archive__group">
          <span class="ak-overline">首次通关奖励</span>
          <AkItemList>
            <AkItem :src="asset('item/framed/4003.png')" name="合成玉" :count="200" size="sm" />
          </AkItemList>
        </span>
      </template>
    </AkArchive>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkArchive 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title`必填 | `string` | — | 标题（h4） |
| `kicker` | `string` | — | 标题前的小标（密录 1 / 悖论模拟） |
| `unlock` | `string` | — | 解锁条件（纯文本）；要放精英 / 信赖 / 关卡号时改用 #unlock 插槽 |
| `unlockLabel` | `string` | `"解锁条件"` | 解锁条件前的小标；传空串不出（条件本身就是图标 + 数值时，如密录的「精英二 Lv.1 · 信赖 50%」） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 正文：若干 <p>（保留换行） |
| `unlock` | 解锁条件（代替 unlock） |
| `footer` | 页脚：AkArchivePlay「阅读密录」、关卡、首通奖励 … |

### AkArchivePlay 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `href` | `string` | — | 链接地址（陈/干员密录/1）；不写就渲染成 <button> |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字（阅读密录 · 一鼓作气） |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
