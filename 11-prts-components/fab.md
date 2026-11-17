# 回到顶部 FAB

> 来源：https://mooncellwiki.github.io/prts-design/components/fab
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

右下角 44px 的反色方块（悬停变主色），滚过一屏淡入，点了平滑滚回顶部。

**整页那一枚由皮肤输出**（骨架末尾的 `button.ak-fab`，`skin.js` 滚过 600px 加 `.is-visible`），模板 / 小部件不用再放。它在侧栏还在的宽度（≥ 1120）出现：更窄时侧栏变抽屉，「回到顶部」改由目录浮层首项承担——右侧已经有一枚目录按钮（`.ak-toc-btn`，同款方块，见[目录](/prts-design/chrome/toc)），不再多浮一枚。1120–1400 目录浮层开着时它也让位。

## 外观 [​](#外观)

Vue 版照 Naive UI 叫 `AkBackTop`（同 `n-back-top`）。`v-model` 是是否显示；`:visibility-height="false"` 时不看滚动，显隐完全由 `v-model` 定。

[Storybook ↗](/prts-design/storybook/?path=/story/components-fab--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkBackTop from "../AkBackTop.vue";

const shown = ref(true);
</script>

<template>
  <div class="ak-relative ak-border ak-bg-surface ak-p-4">
    <p>不看滚动（<code>:visibility-height="false"</code>），显隐完全由 v-model 控制。</p>
    <AkButton size="sm" @click="shown = !shown">{{ shown ? "隐藏" : "显示" }}</AkButton>
    <p class="ak-fs-sm ak-fg-muted ak-mb-0">v-model = {{ shown }}</p>
    <AkBackTop v-model="shown" position="absolute" :visibility-height="false" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 滚动区域里的回到顶部 [​](#滚动区域里的回到顶部)

Vue 版主要给页面里某个自己滚动的区域用（长列表、搜索结果面板）：`position="absolute"` 贴最近的定位祖先右下，`listen-to` 指向滚动容器（元素或选择器），滚过 `visibility-height`（默认 600，同皮肤）出现，点了把**这个容器**滚回顶部。`absolute` 的不受 < 1120 收起的影响。回到顶部按钮要放在滚动容器**外面**（和它同在一个 `position: relative` 的框里），否则会跟着内容一起滚走。

[Storybook ↗](/prts-design/storybook/?path=/story/components-fab--scroll)

vue

```
<script setup lang="ts">
import { ref, useTemplateRef } from "vue";

import AkBackTop from "../AkBackTop.vue";

const chapters = ["黑暗时代·上", "黑暗时代·下", "异卵同生", "二次呼吸", "急性衰竭", "靶向药物", "局部坏死", "苦难摇篮", "怒号光明", "风暴瞭望", "破碎日冕", "淬火尘霾"];
const box = useTemplateRef<HTMLElement>("box");
const shown = ref(false);
</script>

<template>
  <div>
    <!-- 示例用的定高滚动区域（高度是示例脚手架，不属于组件） -->
    <div class="ak-relative ak-border">
      <div ref="box" class="ak-overflow-auto ak-p-4" style="height: 200px">
        <p>往下滚动这个区域，超过 120px 后右下角出现「回到顶部」。</p>
        <ul>
          <li v-for="(c, i) in chapters" :key="c">{{ i === 0 ? "序章" : `第 ${i} 章` }}　{{ c }}</li>
        </ul>
      </div>
      <AkBackTop v-model="shown" position="absolute" :listen-to="box" :visibility-height="120" />
    </div>
    <p class="ak-fs-sm ak-fg-muted ak-mt-2 ak-mb-0">v-model = {{ shown }}</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- 只有图标，`label`（默认「回到顶部」）同时作 `aria-label` 与 `title`。
- 隐藏时（`opacity: 0`）标 `inert`：不可点、不进 Tab 顺序、读屏读不到——不会在角落留一枚看不见的按钮。
- 系统开了「减少动态效果」时直接跳回顶部，不平滑滚动。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `label` | `string` | `"回到顶部"` | 可访问名（aria-label，同时作 title） |
| `icon` | `IconName` | `"up"` | 图标 |
| `visibilityHeight` | `number | false` | `600` | 滚动超过多少 px 出现（同 Naive 的 visibility-height）；false = 不看滚动，显隐完全由 v-model 定 |
| `listenTo` | `string | HTMLElement | null` | — | 监听、并滚回顶部的滚动容器（元素或选择器）；不写 = 整页。模板 ref 还没就绪（null）时先不监听 |
| `position` | `"fixed"``"absolute"` | `"fixed"` | 定位：fixed 贴视口右下（默认，整页一枚）· absolute 贴最近的定位祖先右下（配合 listen-to 给页面里的滚动区域） |
| `modelValue` | `boolean` | `false` | 是否显示；visibility-height 是数字时组件按滚动距离自己更新它 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

## CSS 实现 [​](#css-实现)

皮肤输出的结构（图标用 sprite）：

html

```
<button class="ak-fab" aria-label="回到顶部"><svg width="18" height="18"><use href="#i-up"/></svg></button>
```

显隐由脚本切 `.is-visible`。`.ak-fab--absolute` 是 Vue 版 `position="absolute"` 用的修饰类：贴定位祖先右下、任何宽度都显示。

html

```
<div class="ak-relative ak-border ak-bg-surface ak-p-4">
  <p class="ak-mb-0">右下角：<code>.ak-fab.ak-fab--absolute.is-visible</code></p>
  <p class="ak-mb-0 ak-fs-sm ak-fg-muted">整页那枚是 fixed，窄于 1120px 时被皮肤收起；</p>
  <p class="ak-mb-0 ak-fs-sm ak-fg-muted">这里用 absolute 看外观。</p>
  <button class="ak-fab ak-fab--absolute is-visible" aria-label="回到顶部"><svg width="18" height="18"><use href="#i-up"/></svg></button>
</div>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
