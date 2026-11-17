# 头像 Avatar

> 来源：https://mooncellwiki.github.io/prts-design/components/avatar
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

直角 1px 边的方块头像，图片铺满；没有图时显示文字（缩写、「+9」，Bender 粗体，字号随尺寸）。写法同 Naive UI 的 `n-avatar` / `n-avatar-group`。干员列表行里带稀有度色条的那种见干员行组件，这里是通用头像。

## 尺寸 · 圆形 · 状态点 · 文字 

`xs` 24 · `sm` 32 · 默认 40 · `lg` 56 · `xl` 80。系统默认直角，`round` 只给用户头像这类确需圆形的场合；`status` 右下角一枚绿色状态点（纯视觉，含义要在旁边文字里说出来）。图片加载失败时退回默认插槽的文字（同 Naive 的 fallback）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-avatar--sizes)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkAvatar from "../AkAvatar.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkAvatar size="xs" :src="asset('avatar/char_002_amiya_1p.png')" alt="阿米娅" />
    <AkAvatar size="sm" :src="asset('avatar/char_003_kalts_2.png')" alt="凯尔希" />
    <AkAvatar :src="asset('avatar/char_010_chen_2.png')" alt="陈" status />
    <AkAvatar size="lg" :src="asset('avatar/char_172_svrash_2.png')" alt="银灰" />
    <AkAvatar size="xl" round :src="asset('avatar/char_103_angel_2.png')" alt="能天使" />
    <AkAvatar>DR</AkAvatar>
    <AkAvatar size="lg">PRTS</AkAvatar>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 头像组 

后一枚压前一枚 8px。给 `options`（`{ src, alt }[]`）由组件画，`max` 把其余收成一枚「+N」；也可以直接在默认插槽里放 `AkAvatar`。组上的 `size` 给组内头像统一尺寸（经 provide，同 `AkButtonGroup`）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-avatar--group)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkAvatar from "../AkAvatar.vue";
import AkAvatarGroup from "../AkAvatarGroup.vue";

/** 第一小队（12 人），画 3 枚、其余收成 +9 */
const squad = [
  { src: asset("avatar/char_102_texas_2.png"), alt: "德克萨斯" },
  { src: asset("avatar/char_112_siege_2.png"), alt: "推进之王" },
  { src: asset("avatar/char_148_nearl_2.png"), alt: "临光" },
  { src: asset("avatar/char_010_chen_2.png"), alt: "陈" },
  { src: asset("avatar/char_172_svrash_2.png"), alt: "银灰" },
  { src: asset("avatar/char_103_angel_2.png"), alt: "能天使" },
  { src: asset("avatar/char_2014_nian_2.png"), alt: "年" },
  { src: asset("avatar/char_2025_shu_2.png"), alt: "黍" },
  { src: asset("avatar/char_147_shining_2.png"), alt: "闪灵" },
  { src: asset("avatar/char_134_ifrit_2.png"), alt: "伊芙利特" },
  { src: asset("avatar/char_180_amgoat_2.png"), alt: "艾雅法拉" },
  { src: asset("avatar/char_017_huang_2.png"), alt: "煌" },
];
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
    <AkAvatarGroup :options="squad" :max="3" size="sm" label="第一小队" />
    <AkAvatarGroup :options="squad.slice(0, 5)" label="助战干员" />
    <AkAvatarGroup size="lg">
      <AkAvatar :src="asset('avatar/char_002_amiya_1p.png')" alt="阿米娅" />
      <AkAvatar :src="asset('avatar/char_003_kalts_2.png')" alt="凯尔希" />
      <AkAvatar>DR</AkAvatar>
    </AkAvatarGroup>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 头像旁边已经写了名字时，`alt` 留空（默认），读屏不重复念。
- 头像单独出现（头像组、只有头像的作者栏）时每枚都写 `alt`（人名），头像组再给 `label`（「第一小队」）作组名。

## Vue API 

### AkAvatar 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `src` | `string` | — | 图片地址（干员头像等）；不写或加载失败时显示默认插槽的文字 |
| `alt` | `string` | `""` | 图片替代文本：旁边已经写了名字时留空（默认），头像单独出现时写人名 |
| `size` | `"md"``"sm"``"lg"``"xl"``"xs"` | — | 尺寸：xs 24 · sm 32 · md 40 · lg 56 · xl 80（px）；不写时跟 AkAvatarGroup，再不然是 md |
| `round` | `boolean` | `false` | 圆形（系统默认直角，只给用户头像这类确需时用） |
| `status` | `boolean` | `false` | 右下角绿色状态点（在线 / 可用）：纯视觉，含义要在旁边的文字里说出来 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 没有图片时显示的文字（缩写、「+9」） |

### AkAvatarGroup 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `options` | `AvatarOption[]` | — | 头像列表（同 Naive 的 options）；不写就用默认插槽里的 AkAvatar |
| `max` | `number` | — | 最多画几枚，其余收成一枚「+N」（只对 options 生效） |
| `size` | `"md"``"sm"``"lg"``"xl"``"xs"` | — | 组内头像的尺寸（头像自己写了 size 以头像为准） |
| `label` | `string` | — | 读屏用的组名（「参与干员」） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkAvatar（不用 options 时） |

## CSS 实现 

尺寸由私有变量 `--_s` 定（字号是它的 40%），页眉用户菜单、干员行这类场合在自己的规则里改 `--_s`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
