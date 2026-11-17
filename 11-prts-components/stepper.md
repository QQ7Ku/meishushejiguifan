# 步骤条 Stepper

> 来源：https://mooncellwiki.github.io/prts-design/components/stepper
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

一排带序号的步骤 + 连线，展示走到了哪一步：精英化、技能专精、多步表单的进度。序号由 CSS 计数器生成；已完成 = 主色序号 + 主色连线，当前 = 主色序号 + 加粗，未到 = 灰。只有横排，只展示、不可点。

## 基本 [​](#基本)

写法同 Naive UI 的 `n-steps`：`AkSteps` 里放若干 `AkStep`，`current`（从 1 数）之前的步骤已完成、这一步是当前、之后未到。

[Storybook ↗](/prts-design/storybook/?path=/story/components-stepper--basic)

vue

```
<script setup lang="ts">
import AkStep from "../AkStep.vue";
import AkSteps from "../AkSteps.vue";
</script>

<template>
  <AkSteps :current="3" label="精英化进度">
    <AkStep title="精英零" />
    <AkStep title="精英一" />
    <AkStep title="精英二" />
    <AkStep title="满级" />
  </AkSteps>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 随 current 推进 [​](#随-current-推进)

`current` 超过步数 = 全部完成。某一步要单独指定状态时写 `AkStep` 的 `status`（优先于推算）。步骤名也可以写在 `AkStep` 的默认插槽里。

[Storybook ↗](/prts-design/storybook/?path=/story/components-stepper--progress)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkStep from "../AkStep.vue";
import AkSteps from "../AkSteps.vue";

const steps = ["技能等级 7", "专精一", "专精二", "专精三"];
const current = ref(2);
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <AkSteps :current="current" label="技能专精进度">
      <AkStep v-for="s in steps" :key="s">{{ s }}</AkStep>
    </AkSteps>
    <div class="ak-flex ak-gap-2 ak-items-center">
      <AkButton size="sm" :disabled="current <= 1" @click="current--">上一步</AkButton>
      <AkButton size="sm" variant="primary" :disabled="current > steps.length" @click="current++">下一步</AkButton>
      <span class="ak-fs-sm ak-fg-muted">current = {{ current }}</span>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- 输出 `<ol>` / `<li>`（有序列表，步骤有先后），`label` 作列表名（如「精英化进度」）。`<ol>` 上显式写 `role="list"`：Safari（VoiceOver）对去掉列表符号（`list-style: none`）的列表不报列表语义。
- 当前步骤 `aria-current="step"`；已完成的步骤带一段只给读屏的「（已完成）」（`.ak-sr-only`）——颜色之外的状态说明。

## Vue API [​](#vue-api)

### AkSteps [​](#aksteps)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `current` | `number` | — | 当前步骤（从 1 数，同 Naive）：之前的已完成、这一步是当前、之后未到；超过步数 = 全部完成；不写则各步按自己的 status |
| `label` | `string` | — | 读屏用的列表名（如「精英化进度」） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkStep |

### AkStep [​](#akstep)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | — | 步骤名（也可以写在默认插槽里） |
| `status` | `"done"``"wait"``"active"` | — | 状态：wait 未到 · active 当前（主色序号 + 加粗）· done 已完成（主色序号 + 主色连线）；不写时按 AkSteps 的 current 推算 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 步骤名（代替 title） |

## CSS 实现 [​](#css-实现)

结构 `ol.ak-stepper > li.ak-step`：`.ak-stepper` 自己去掉原生序号、缩进与外边距（序号由 CSS 计数器画），直接写在正文里也不吃正文有序列表的缩进、段距与主色序号。

html

```
<ol class="ak-stepper" role="list" aria-label="精英化进度">
  <li class="ak-step is-done">精英零</li>
  <li class="ak-step is-done">精英一</li>
  <li class="ak-step is-active" aria-current="step">精英二</li>
  <li class="ak-step">满级</li>
</ol>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
