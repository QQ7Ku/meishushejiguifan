# 下拉选择 Select

> 来源：https://mooncellwiki.github.io/prts-design/components/select
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

原生 `<select>` 换肤（与裸 `<select>` 同一枚 ▾，`--ak-select-arrow`）：弹出的列表、键盘、手机上的滚轮选择器、读屏都是浏览器自己的，所以只画关着时的那个框。选项照 Naive UI 的 `n-select` 用 `options` 数组给，`v-model` 是选中项的 `value`（数字也行）。

## 基本 [​](#基本)

`placeholder` 是一个选不到的空选项：没选（`v-model` 为 `null` / `undefined`）时显示它。

[Storybook ↗](/prts-design/storybook/?path=/story/components-select--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkSelect from "../AkSelect.vue";

const professions = [
  { label: "先锋", value: "PIONEER" },
  { label: "近卫", value: "WARRIOR" },
  { label: "重装", value: "TANK" },
  { label: "狙击", value: "SNIPER" },
  { label: "术师", value: "CASTER" },
  { label: "医疗", value: "MEDIC" },
  { label: "辅助", value: "SUPPORT" },
  { label: "特种", value: "SPECIAL" },
];
const potentials = [1, 2, 3, 4, 5, 6].map(n => ({ label: `潜能 ${n}`, value: n }));
const modules = [
  { label: "陈证章（原型）", value: "" },
  { label: "罗德岛制式剑 · SWO-X 3 级", value: "x" },
  { label: "往昔时光 · SWO-Y 3 级", value: "y" },
];

const profession = ref<string | null>(null);
const potential = ref(1);
const mod = ref("x");
</script>

<template>
  <div class="ak-grid-3 ak-gap-4">
    <AkSelect v-model="profession" :options="professions" placeholder="选择职业" label="职业" />
    <AkSelect v-model="potential" :options="potentials" label="潜能" />
    <AkSelect v-model="mod" :options="modules" label="模组" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 分组 · 禁用项 [​](#分组-·-禁用项)

`{ type: "group", label, children }` 渲染成 `<optgroup>`；选项写 `disabled: true` 选不到。

[Storybook ↗](/prts-design/storybook/?path=/story/components-select--groups)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkSelect from "../AkSelect.vue";

const support = [
  {
    type: "group" as const,
    label: "近卫",
    children: [
      { label: "陈", value: "char_010_chen" },
      { label: "银灰", value: "char_172_svrash" },
      { label: "史尔特尔", value: "char_350_surtr" },
    ],
  },
  {
    type: "group" as const,
    label: "术师",
    children: [
      { label: "艾雅法拉", value: "char_180_amgoat" },
      { label: "伊芙利特", value: "char_134_ifrit" },
    ],
  },
  {
    type: "group" as const,
    label: "医疗",
    children: [
      { label: "闪灵", value: "char_147_shining" },
      { label: "夜莺（已在编队）", value: "char_179_cgbird", disabled: true },
    ],
  },
];

const pick = ref<string | null>(null);
</script>

<template>
  <AkSelect v-model="pick" :options="support" placeholder="选择助战干员" label="助战干员" class="ak-max-narrow" />
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 · 状态 [​](#尺寸-·-状态)

高 30 / 36 / 44，与输入框同一刻度。禁用、`status="error"`（红边 + `aria-invalid`）、`success`（绿边）；放在[表单字段](/prts-design/components/field)里时跟字段的 `validation-status`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-select--states)

vue

```
<script setup lang="ts">
import AkSelect from "../AkSelect.vue";

const servers = [
  { label: "国服", value: "cn" },
  { label: "B 服", value: "bili" },
  { label: "国际服", value: "global" },
];
</script>

<template>
  <div class="ak-grid-3 ak-gap-4 ak-items-center">
    <AkSelect size="sm" :options="servers" model-value="cn" label="服务器（小）" />
    <AkSelect :options="servers" model-value="cn" label="服务器" />
    <AkSelect size="lg" :options="servers" model-value="cn" label="服务器（大）" />
    <AkSelect :options="servers" model-value="bili" disabled label="服务器（禁用）" />
    <AkSelect :options="servers" placeholder="未选择" status="error" label="服务器（出错）" />
    <AkSelect :options="servers" model-value="global" status="success" label="服务器（通过）" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 什么时候不用它 [​](#什么时候不用它)

- 选项 ≤ 5 个、又想一眼看全：用[单选](/prts-design/components/radio)；立即生效的视图切换用单选按钮组。
- 要搜索 / 多选 / 自定义选项外观：原生 `<select>` 做不到，需要下拉菜单类组件（`.ak-dropdown`）另做。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `options`必填 | `(SelectOption | SelectGroupOption)[]` | — | 选项（同 Naive 的 options）：{ label, value, disabled }；{ type: "group", label, children } 是一组（<optgroup>） |
| `placeholder` | `string` | — | 没选时显示的提示（一个选不到的空选项）；不写则默认选中第一项的样子 |
| `size` | `"md"``"sm"``"lg"` | `"md"` | 尺寸：sm 30 · md 36 · lg 44（px 高），与输入框同一刻度 |
| `status` | `"success"``"error"` | — | 校验状态：error 红边（aria-invalid）· success 绿边；不写时跟 AkField 的 validation-status |
| `disabled` | `boolean` | `false` | 禁用 |
| `label` | `string` | — | 可访问名（aria-label）：不在 AkField 里、旁边也没有 <label> 时必填 |
| `modelValue` | `SelectValue | null` | — | 选中项的 value；没选是 null / undefined |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: SelectValue | null | undefined]` |  |

## CSS 实现 [​](#css-实现)

`<select class="ak-select">`；尺寸 `.ak-select--sm` / `--lg`；状态 `:disabled` `[aria-invalid="true"]` / `:user-invalid` `.is-valid`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
