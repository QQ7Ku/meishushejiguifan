# 键值表 Kv

> 来源：https://mooncellwiki.github.io/prts-design/arknights/kv
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

信息栏 / 档案里的键值对，输出 `<dl>`。`dt` / `dd` 垂直居中（行高由高的一侧决定时另一侧居中，同现网 infobox 的 th / td）；数字走正文字体（表格硬规则，`.ak-code-id` / `.ak-trust` 落进 `dd` 时由表格数字规则兜底）。

写法同 Naive UI 的 `n-descriptions`：`AkKv` 里放若干 `AkKvItem`，`term` 是键、默认插槽是值；键要带悬停解释 / 图标时用 `#term` 插槽。Naive 的键叫 `label`，这里叫 `term`（= `<dt>`）——`label` 全库只作可访问名。

没有做成 `items` 数组：值常常是链接、标签、图标、嵌套列表，插槽写起来比往数组里塞渲染函数直观；子项自己输出 `dt` + `dd`，只有内联版要多包一层 `div`，这点由父组件 provide 下去，父组件不用读子项。

## 带框（信息栏） 

`bordered`：外框 + 键列底色、居中，同现网 infobox 的 th。干员页「特性」节。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-kv--boxed)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkKv from "../AkKv.vue";
import AkKvItem from "../AkKvItem.vue";
</script>

<template>
  <!-- 干员页「特性」：带框的信息栏，键列居中 -->
  <AkKv bordered>
    <AkKvItem term="分支">
      <span class="ak-prof-label">
        <span class="ak-subprof"><img :src="asset('subprofession/sword.png')" alt="" /></span>
        <a href="#">剑豪</a> <span class="ak-en">Swordmaster</span>
      </span>
    </AkKvItem>
    <AkKvItem term="描述">普通攻击连续造成<span class="ak-rt-kw">两次</span>伤害</AkKvItem>
    <AkKvItem term="分支信息">
      <ul>
        <li>可以且优先攻击自身阻挡的单位（即使目标处于攻击范围外或为飞行单位）。</li>
      </ul>
    </AkKvItem>
  </AkKv>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 不带框（档案） 

默认：只有横线，键列左对齐。人员档案的「基础档案」。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-kv--plain)

vue

```
<script setup lang="ts">
import AkKv from "../AkKv.vue";
import AkKvItem from "../AkKvItem.vue";

/** 人员档案「基础档案」 */
const profile = [
  ["代号", "陈"],
  ["性别", "女"],
  ["战斗经验", "四年"],
  ["出身地", "龙门"],
  ["生日", "7月7日"],
  ["种族", "龙"],
  ["身高", "168cm"],
  ["矿石病感染情况", "未公开"],
] as const;
</script>

<template>
  <AkKv>
    <AkKvItem v-for="[k, v] in profile" :key="k" :term="k">{{ v }}</AkKvItem>
  </AkKv>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 内联 

`inline`：两三对短值一行放下（`dl > div > dt + dd` 分组，放不下换行），不画格子；键靠灰色和值分开，不加冒号。所属势力 / 隐藏势力、获得方式 / 上线时间这种量级的内容，竖着一行一格是空占地方。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-kv--inline)

vue

```
<script setup lang="ts">
import AkTag from "../../Tag/AkTag.vue";
import AkKv from "../AkKv.vue";
import AkKvItem from "../AkKvItem.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <!-- 干员页「获得方式」 -->
    <AkKv inline>
      <AkKvItem term="获得方式"><AkTag>公开招募</AkTag> <AkTag>中坚寻访</AkTag></AkKvItem>
      <AkKvItem term="上线时间">2019年7月9日 16:00</AkKvItem>
    </AkKv>
    <!-- 干员页「属性」节的表脚：键带悬停解释时用 #term 插槽 -->
    <AkKv inline>
      <AkKvItem term="所属势力">
        <a href="#">炎-龙门</a> <span class="ak-fg-subtle">›</span> <a href="#">龙门近卫局</a>
      </AkKvItem>
      <AkKvItem>
        <template #term><span class="ak-term" data-ak-tip="仅在战斗中所使用的数据" tabindex="0">隐藏势力</span></template>
        <i class="ak-fg-muted">无</i>
      </AkKvItem>
    </AkKv>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkKv 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `bordered` | `boolean` | `false` | 带框（信息栏）：外框 + 键列底色、居中，同现网 infobox 的 th；不带框的是档案里那种只有横线的版本 |
| `inline` | `boolean` | `false` | 内联：两三对短值一行放下、放不下换行，不画格子（所属势力 / 隐藏势力、获得方式 / 上线时间）；与 bordered 同时写时以内联为准 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkKvItem |

### AkKvItem 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `term` | `string` | — | 键（dt）文字；要带悬停解释 / 图标时改用 #term 插槽 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 值（dd）：文字、链接、标签、列表都行 |
| `term` | 键（代替 term） |

## CSS 实现 

`dt` / `dd` 都显式 `margin: 0`：正文的 `.mw-parser-output dt { margin-top }` / `dd { margin }` 同优先级，靠顺序压掉。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
