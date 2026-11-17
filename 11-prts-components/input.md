# 输入框 Input

> 来源：https://mooncellwiki.github.io/prts-design/components/input
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

单行 / 多行文字输入（同 Naive UI 的 `n-input`）。`v-model` 是文字；`type="textarea"` 换成多行（`<textarea class="ak-textarea">`，最小高 90px、只能竖向拉）。其余属性（`name`、`autocomplete`、`@focus` / `@blur` / `@keydown` …）都落到原生 `<input>` / `<textarea>` 上。数字用[数字输入](/prts-design/components/input-number)，搜索用[搜索框](/prts-design/components/search)。

## 单行 · 多行 

不在[表单字段](/prts-design/components/field)里、旁边也没有 `<label>` 时写 `label`（作 `aria-label`）；占位提示只做提示，不承载必填等信息。

[Storybook ↗](/prts-design/storybook/?path=/story/components-input--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkInput from "../AkInput.vue";

const name = ref("");
const note = ref("");
</script>

<template>
  <div class="ak-grid-2 ak-gap-6">
    <AkInput v-model="name" label="干员名称" placeholder="例如：陈" />
    <AkInput v-model="note" type="textarea" label="备注" placeholder="部署位置、技能专精顺序…" :maxlength="200" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 

高 30 / 36 / 44，与按钮同一刻度：`sm` 给表格里、工具栏，默认 36，`lg` 给独立的大输入。

[Storybook ↗](/prts-design/storybook/?path=/story/components-input--sizes)

vue

```
<script setup lang="ts">
import AkInput from "../AkInput.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkInput size="sm" label="小" placeholder="sm · 30px：表格里、工具栏" />
    <AkInput label="默认" placeholder="md · 36px（默认），与按钮同高" />
    <AkInput size="lg" label="大" placeholder="lg · 44px：独立的大输入" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 状态 

禁用 = surface-3 底 + 禁用色；**只读 = 下沉底**，仍可选中复制——计算器里「只显示不编辑」的结果用 `readonly`，不要用 `disabled`。`status="error"` 红边（`aria-invalid`）、`success` 绿边；放在字段里时跟字段的 `validation-status`。输入框不做悬停态，焦点是青边 + 3px 淡青环（鼠标点进去也亮）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-input--states)

vue

```
<script setup lang="ts">
import AkInput from "../AkInput.vue";
</script>

<template>
  <div class="ak-grid-2 ak-gap-4">
    <AkInput label="禁用" model-value="不可编辑" disabled />
    <AkInput label="只读" model-value="2880" readonly />
    <AkInput label="出错" model-value="???" status="error" />
    <AkInput label="通过" model-value="SilverAsh" status="success" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 前后缀 · 输入组 

`#prefix` / `#suffix` 是贴在输入框两边的灰底块（`.ak-input-group__addon`：路径前缀、单位）。要和按钮拼成一条，外面包 `AkInputGroup`（同 Naive 的 `n-input-group`）：相邻边重叠、中间不留圆角，`size` 同时给组内的输入框和按钮。

[Storybook ↗](/prts-design/storybook/?path=/story/components-input--group)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkInput from "../AkInput.vue";
import AkInputGroup from "../AkInputGroup.vue";

const page = ref("陈");
const trust = ref("200");
</script>

<template>
  <div class="ak-flex-col ak-gap-4">
    <AkInputGroup>
      <AkInput v-model="page" label="页面名">
        <template #prefix>wiki/</template>
      </AkInput>
      <AkButton variant="primary">GO</AkButton>
    </AkInputGroup>
    <AkInput v-model="trust" label="信赖">
      <template #prefix>信赖</template>
      <template #suffix>%</template>
    </AkInput>
    <AkInputGroup size="sm">
      <AkInput label="筛选干员" placeholder="干员名 / 代号" />
      <AkButton icon="search" label="搜索" />
    </AkInputGroup>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkInput 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `type` | `"text"``"textarea"``"password"``"email"``"url"``"tel"` | `"text"` | 类型：textarea 渲染多行 <textarea class="ak-textarea">（同 Naive）；其余是 <input> 的 type。数字用 AkInputNumber，搜索用 AkSearch |
| `placeholder` | `string` | — | 占位提示（只做提示，不承载必填等信息） |
| `size` | `"md"``"sm"``"lg"` | — | 尺寸：sm 30 · md 36 · lg 44（px 高），与按钮同一刻度；多行输入不适用。不写时跟 AkInputGroup，再不然是 md |
| `status` | `"success"``"error"` | — | 校验状态：error 红边（aria-invalid）· success 绿边；不写时跟 AkField 的 validation-status |
| `disabled` | `boolean` | `false` | 禁用 |
| `readonly` | `boolean` | `false` | 只读：下沉底色，仍可选中复制——计算器「只显示不编辑」的结果用它，不要用 disabled |
| `maxlength` | `number` | — | 最多字数（原生 maxlength） |
| `rows` | `number` | — | 多行输入的可见行数（原生 rows）；不写时由 CSS 的最小高 90px 定 |
| `label` | `string` | — | 可访问名（aria-label）：不在 AkField 里、旁边也没有 <label> 时必填 |
| `modelValue` | `string` | — | 输入的文字 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: string | undefined]` |  |

| 插槽 | 说明 |
| --- | --- |
| `prefix` | 前缀（输入框左边的 .ak-input-group\_\_addon，如「wiki/」）；多行输入不适用 |
| `suffix` | 后缀（输入框右边的 .ak-input-group\_\_addon，如单位「%」）；多行输入不适用 |

### AkInputGroup 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `size` | `"md"``"sm"``"lg"` | — | 组内输入框与按钮的默认尺寸（各自写了 size 以各自为准） |

| 插槽 | 说明 |
| --- | --- |
| `default` | AkInput（可带 #prefix / #suffix）与 AkButton |

## CSS 实现 

`<input class="ak-input">` / `<textarea class="ak-textarea">`；尺寸 `.ak-input--sm` / `--lg`；状态 `[readonly]` `:disabled` `.is-invalid` / `[aria-invalid="true"]` / `:user-invalid` `.is-valid`。输入组 `.ak-input-group > .ak-input-group__addon + .ak-input + .ak-btn`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
