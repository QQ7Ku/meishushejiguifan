# 骨架屏 Skeleton

> 来源：https://mooncellwiki.github.io/prts-design/components/skeleton
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

内容到之前先占住它的形状，加载完不跳版。底色在表面 2 与表面 3 之间扫光（1.4s 一轮）。写法同 Naive UI 的 `n-skeleton`：`text` / `circle` / `repeat` / `width` / `height`。

## 形状 · 尺寸 

不写形状时是 1em 高、占满一行的块，配 `height` 画图片 / 卡片封面的占位（`height="120"` = CSS 的 `.ak-skeleton--rect`）；`text` 是文字行（.9em 高、上下 .35em，一组里最后一行 60% 宽），配 `repeat` 画一段；`circle` / `square` 宽高相等、由 `width` 定（头像、道具）。数字按 px，字符串原样（`"60%"`）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-skeleton--shapes)

vue

```
<script setup lang="ts">
import AkSkeleton from "../AkSkeleton.vue";
</script>

<template>
  <div class="ak-grid ak-grid-2 ak-gap-6 ak-items-start">
    <div>
      <AkSkeleton :height="120" class="ak-mb-2" />
      <AkSkeleton text :repeat="3" />
    </div>
    <div class="ak-flex ak-gap-4 ak-items-center">
      <AkSkeleton circle :width="56" />
      <AkSkeleton square :width="56" />
      <AkSkeleton :width="120" :height="36" />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 拼成列表占位 

照真实内容的版式拼：头像位一个方块、名字一短行、副信息一长行。加载中的容器标 `aria-busy="true"`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-skeleton--layout)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkAvatar from "../../Avatar/AkAvatar.vue";
import AkButton from "../../Button/AkButton.vue";
import AkSkeleton from "../AkSkeleton.vue";

const loading = ref(true);
const ops = [
  { name: "陈", meta: "剑豪 · LM04", avatar: asset("avatar/char_010_chen_2.png") },
  { name: "银灰", meta: "领主 · KJ01", avatar: asset("avatar/char_172_svrash_2.png") },
  { name: "德克萨斯", meta: "尖兵 · PL03", avatar: asset("avatar/char_102_texas_2.png") },
];
</script>

<template>
  <div class="ak-flex-col ak-gap-4 ak-items-start">
    <AkButton size="sm" @click="loading = !loading">{{ loading ? "加载完成" : "重新加载" }}</AkButton>
    <div class="ak-flex-col ak-gap-3 ak-w-full" role="list" :aria-busy="loading" aria-label="干员列表">
      <div v-for="op in ops" :key="op.name" class="ak-flex ak-gap-3 ak-items-center" role="listitem">
        <template v-if="loading">
          <AkSkeleton square :width="40" />
          <div class="ak-grow">
            <AkSkeleton text :width="64" />
            <AkSkeleton text :width="112" />
          </div>
        </template>
        <template v-else>
          <AkAvatar :src="op.avatar" />
          <div class="ak-grow">
            <div class="ak-fw-700">{{ op.name }}</div>
            <div class="ak-fs-xs ak-fg-muted">{{ op.meta }}</div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

骨架对读屏隐藏（`aria-hidden`）——它只是形状，没有信息。「正在加载」由外层容器的 `aria-busy` 或一枚[加载](/prts-design/components/spinner)（`role="status"`）说。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `text` | `boolean` | `false` | 文字行：.9em 高、上下 .35em 间距；一组里最后一行 60% 宽 |
| `circle` | `boolean` | `false` | 圆形（头像占位）：宽高相等，由 width 定 |
| `square` | `boolean` | `false` | 方形（道具 / 头像占位）：宽高相等，由 width 定 |
| `repeat` | `number` | `1` | 重复几个（同 Naive）：配 text 画一段文字 |
| `width` | `string | number` | — | 宽度：数字按 px，字符串原样（"60%"）；不写时占满一行 |
| `height` | `string | number` | — | 高度：数字按 px；不写时 1em（text 时 .9em，圆 / 方形跟宽度） |

## CSS 实现 

`<span class="ak-skeleton ak-skeleton--text"></span>`；宽高不同的占位在模板里写 `style="width: …"`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
