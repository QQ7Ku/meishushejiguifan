# 进度 Progress

> 来源：https://mooncellwiki.github.io/prts-design/components/progress
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

完成度 / 数量占比：信赖、理智、剿灭奖励、上传进度。直角细条（默认 8px，`--ak-bg-surface-3` 轨道 + 主色填充），可以带上方的标签行（左名称、右数值，Bender 小字）；另有环形。

## 线形 · 标签行 · 颜色 [​](#线形-·-标签行-·-颜色)

写法同 Naive UI 的 `n-progress`：`percentage` 0–100。写了 `label` 就在条上方出标签行，右边默认是「N%」，默认插槽可以改写成「200 / 200」。颜色 `variant`：`accent`（默认）· `yellow`（信赖 / 次强调）· `success` · `danger`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-progress--line)

vue

```
<script setup lang="ts">
import AkProgress from "../AkProgress.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkProgress label="信赖 TRUST" :percentage="100">200 / 200</AkProgress>
    <AkProgress label="理智 SANITY" variant="yellow" :percentage="62">84 / 135</AkProgress>
    <AkProgress label="本周剿灭 · 合成玉" variant="success" :percentage="67">1200 / 1800</AkProgress>
    <AkProgress label="素材缺口" variant="danger" :percentage="18" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 粗细 · 斜纹 · 不定 · 分段 [​](#粗细-·-斜纹-·-不定-·-分段)

`size`：`sm` 4 · `md` 8 · `lg` 14。`stripes` 叠斜纹；`indeterminate` 不知道进度时一段色块来回滑（不报数值）；`steps` 把条切成 N 格按百分比点亮（2 / 3 格 = `steps` 3 + `percentage` 67）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-progress--states)

vue

```
<script setup lang="ts">
import AkProgress from "../AkProgress.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <!-- 没有 label 时写 aria-label 给读屏 -->
    <AkProgress size="sm" :percentage="30" aria-label="sm" />
    <AkProgress :percentage="50" aria-label="md" />
    <AkProgress size="lg" stripes :percentage="40" aria-label="上传文件" />
    <AkProgress label="正在同步 Cargo 表" indeterminate :show-indicator="false" />
    <AkProgress label="关卡星级" :steps="3" :percentage="67">2 / 3</AkProgress>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 环形 [​](#环形)

`circle`：直径 56 的圆环（conic-gradient），中间是数值——这是系统里少有的圆，只给「一个百分比」这种自成一体的读数。

[Storybook ↗](/prts-design/storybook/?path=/story/components-progress--circle)

vue

```
<script setup lang="ts">
import AkProgress from "../AkProgress.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkProgress circle :percentage="75" label="图鉴收集" />
    <AkProgress circle variant="yellow" :percentage="30" label="信赖" />
    <AkProgress circle variant="success" :percentage="100" label="主线完成度" />
    <AkProgress circle variant="danger" :percentage="12" label="剩余理智">12</AkProgress>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- 输出 `role="progressbar"` + `aria-valuemin/max/now`（不定进度不报数值；分段额外给 `aria-valuetext`「2 / 3」）。
- 名字：线形有 `label` 时标签行的名称作 `aria-labelledby`；没有标签行时自己写 `aria-label`（透传到根元素）；环形的 `label` 直接作 `aria-label`。
- 颜色只是辅助：数值要写出来（标签行 / 环心）。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `percentage` | `number` | `0` | 百分比 0–100 |
| `variant` | `"success"``"accent"``"yellow"``"danger"` | `"accent"` | 颜色：accent 主色（默认）· yellow 黄（信赖 / 次强调）· success 绿 · danger 红 |
| `size` | `"md"``"sm"``"lg"` | `"md"` | 线形粗细：sm 4 · md 8（默认）· lg 14（px）；环形不适用 |
| `label` | `string` | — | 名称：线形显示在条上方标签行的左边（并作可访问名）；环形只作可访问名（aria-label） |
| `showIndicator` | `boolean` | `true` | 显示数值：默认「N%」，默认插槽可改写（「200 / 200」）；线形的数值在标签行右边，只在有 label 或插槽时出标签行 |
| `stripes` | `boolean` | `false` | 斜纹（线形） |
| `indeterminate` | `boolean` | `false` | 不定进度：一段色块来回滑，不报数值（线形） |
| `steps` | `number` | — | 分段：条切成 N 格，按百分比点亮（3 / 5 格 = steps 5 + percentage 60）（线形） |
| `circle` | `boolean` | `false` | 环形（= .ak-ring：直径 56，中间是数值）——信赖、完成度 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 数值文字（代替「N%」） |

## CSS 实现 [​](#css-实现)

线形 `div.ak-progress` 用 `--_v`（百分比，如 `62%`）定填充宽度；标签行 `div.ak-progress-label > span + span` 放在条前面。分段 `.ak-progress--segmented > i(.is-on)`。环形 `div.ak-ring > span`，`--_v` 是不带单位的数（`75`），颜色 `.ak-ring--yellow` 等（或直接给 `--_c`），直径 `--_size`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
