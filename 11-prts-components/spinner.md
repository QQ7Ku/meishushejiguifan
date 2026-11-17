# 加载 Spinner

> 来源：https://mooncellwiki.github.io/prts-design/components/spinner
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

游戏内 loading 的**菱形涟漪**：中央常驻一枚空心菱形（中坚术师分支图标那枚），另一枚环从它身上冒出来、边扩边淡到没，1.2s 一枚。减弱动效时环不出，只留中央静止的菱形。按钮的加载态不用它（36px 高里放不开），是[按钮](/prts-design/components/button/)自己的圆弧转圈。

写法同 Naive UI 的 `n-spin`：`description`、`show`、默认插槽包住内容。

## 样子 · 说明 

`variant="bars"` 是三根竖条（更轻，放在行内）。`color` 换色（写到 CSS 约定的 `--_c`，只对菱形生效）。`description` 排在图形下方。

[Storybook ↗](/prts-design/storybook/?path=/story/components-spinner--variants)

vue

```
<script setup lang="ts">
import AkSpinner from "../AkSpinner.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-8 ak-items-center">
    <AkSpinner />
    <AkSpinner color="var(--ak-yellow-500)" label="正在同步" />
    <AkSpinner variant="bars" />
    <AkSpinner description="正在读取干员数据…" />
    <AkSpinner variant="bars" description="连接神经网络" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 包住内容 

默认插槽写了内容时，`show` 为真就把内容压淡、不可点，菱形 + 说明叠在正中；容器标 `aria-busy`。适合表格、卡片这类「旧内容还在、新数据在路上」的局部刷新；整块还没有内容时用[骨架屏](/prts-design/components/skeleton)。

[Storybook ↗](/prts-design/storybook/?path=/story/components-spinner--wrap)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkSpinner from "../AkSpinner.vue";

const loading = ref(true);
</script>

<template>
  <div class="ak-flex-col ak-gap-3 ak-items-start">
    <AkButton size="sm" @click="loading = !loading">{{ loading ? "加载完成" : "重新加载" }}</AkButton>
    <AkSpinner :show="loading" description="正在计算属性…" class="ak-w-full">
      <table class="wikitable ak-w-full ak-m-0">
        <thead>
          <tr><th>精英阶段</th><th>生命上限</th><th>攻击</th><th>防御</th><th>法术抗性</th></tr>
        </thead>
        <tbody>
          <tr><td>精英零 50 级</td><td>1684</td><td>361</td><td>221</td><td>0</td></tr>
          <tr><td>精英一 80 级</td><td>2188</td><td>469</td><td>288</td><td>0</td></tr>
          <tr><td>精英二 90 级</td><td>2880</td><td>610</td><td>352</td><td>0</td></tr>
        </tbody>
      </table>
    </AkSpinner>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 图形 `role="status"`，可访问名默认「加载中」（`label` 改写）；有说明时念说明。
- 包住内容时容器 `aria-busy="true"`，读屏等加载完再念变化。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"diamond"``"bars"` | `"diamond"` | 样子：diamond 菱形涟漪（游戏内 loading，默认）· bars 三根竖条（行内、更轻） |
| `description` | `string` | — | 说明文字，排在图形下方（同 Naive） |
| `show` | `boolean` | `true` | 包住内容时是否在加载（同 Naive）；不包内容时无效，要藏起来直接 v-if |
| `color` | `string` | — | 换色：写到 CSS 的 --\_c（只对 diamond 生效），如 "var(--ak-yellow-500)" |
| `label` | `string` | `"加载中"` | 读屏文本；有 description 时念 description |

| 插槽 | 说明 |
| --- | --- |
| `default` | 正在加载的内容：加载层叠在它上面 |
| `description` | 说明（代替 description） |

## CSS 实现 

大小由 `font-size` 定（默认 12px → 29px 见方），`--_c` 换色。`::before` 是中央常驻的空心菱形（.75em、2px 线）；`::after` 是涟漪环——同一枚菱形用 border 画，`width` / `height` 从刚好箍住中央那枚外沿扩到 1.65em（不用 `scale`，线宽恒 2px），`opacity` 另一条曲线淡到 0，各 1.2s。游戏里环出生时还带随机剪切，这个尺寸下施展不开，没做。菱形是源石母题，见[设计理念 · 菱形](/prts-design/foundations/principles#菱形-源石)。说明与加载层（`.ak-spin` / `.ak-spin-container`）是给 Vue 组件补的外壳，模板里一般直接放 `<span class="ak-spinner" role="status" aria-label="加载中"></span>`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
