# 复选框 Checkbox

> 来源：https://mooncellwiki.github.io/prts-design/components/checkbox
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

18px 直角勾选框，选中 = 主色实底 + 对比色勾（同 Naive UI 的 `n-checkbox`）。长相由 [裸控件规则](/prts-design/content/forms) 给——裸 `<input type="checkbox">` 与这里是同一张脸，`.ak-check` 只负责「框 + 文字」的排布，点文字也能勾。

## 基本 

单独用时 `v-model` 是布尔值。禁用时框变灰、文字降到禁用色；`size="sm"` 是 16px 的框（表头里的开关，如天赋表的「潜能」）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-checkbox--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkCheckbox from "../AkCheckbox.vue";

const recruitable = ref(true);
const limited = ref(false);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
    <AkCheckbox v-model="recruitable">可公招</AkCheckbox>
    <AkCheckbox v-model="limited">限定</AkCheckbox>
    <AkCheckbox :model-value="true" disabled>已精二（禁用）</AkCheckbox>
    <AkCheckbox disabled>未实装（禁用）</AkCheckbox>
    <AkCheckbox size="sm" :model-value="true">sm · 16px</AkCheckbox>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 复选组 

`AkCheckboxGroup`（同 Naive 的 `n-checkbox-group`）的 `v-model` 是勾上的 `value` 数组，组里的 `AkCheckbox` 各写 `value`。`max` 选满后没勾的变成禁用——公开招募一次刷 5 个标签、最多选 3 个。组是 `role="group"`，放在[表单字段](/prts-design/components/field)里时字段的标签就是组名。

[Storybook ↗](/prts-design/storybook/?path=/story/components-checkbox--group)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkField from "../../Field/AkField.vue";
import AkCheckbox from "../AkCheckbox.vue";
import AkCheckboxGroup from "../AkCheckboxGroup.vue";

/** 公开招募：一次刷出 5 个标签，最多选 3 个 */
const tags = ["资深干员", "近战位", "输出", "防护", "削弱"];
const picked = ref(["近战位"]);
</script>

<template>
  <AkField label="公开招募标签" :feedback="`已选 ${picked.length} / 3`">
    <AkCheckboxGroup v-model="picked" :max="3">
      <AkCheckbox v-for="t in tags" :key="t" :value="t">{{ t }}</AkCheckbox>
    </AkCheckboxGroup>
  </AkField>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 半选 

「全选」框在部分子项勾上时写 `indeterminate`：框里一横，读屏念「部分选中」。它只管外观，点击照常切换 `v-model`——由你决定点了之后全选还是全不选。

[Storybook ↗](/prts-design/storybook/?path=/story/components-checkbox--indeterminate)

vue

```
<script setup lang="ts">
import { computed, ref } from "vue";

import AkCheckbox from "../AkCheckbox.vue";
import AkCheckboxGroup from "../AkCheckboxGroup.vue";

const professions = ["先锋", "近卫", "重装", "狙击", "术师", "医疗", "辅助", "特种"];
const shown = ref(["近卫", "狙击", "术师"]);

const all = computed({
  get: () => shown.value.length === professions.length,
  set: v => (shown.value = v ? [...professions] : []),
});
const some = computed(() => shown.value.length > 0 && !all.value);
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkCheckbox v-model="all" :indeterminate="some">全部职业</AkCheckbox>
    <AkCheckboxGroup v-model="shown" label="职业">
      <AkCheckbox v-for="p in professions" :key="p" :value="p">{{ p }}</AkCheckbox>
    </AkCheckboxGroup>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 原生 `<input type="checkbox">` 包在 `<label>` 里：`Space` 切换，点文字也能勾，读屏念「文字，复选框，已勾选」。
- 没有文字的勾选框（表头里只放一枚图标）要写 `label`，否则只念「复选框」；开发时漏写会在控制台警告。
- 焦点只在键盘操作时亮（`:focus-visible`）：青边 + 淡青环。

## Vue API 

### AkCheckbox 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value` | `CheckValue` | — | 在 AkCheckboxGroup 里的取值：勾上时进组的 v-model 数组 |
| `indeterminate` | `boolean` | `false` | 半选（一横）：「全选」框在部分子项勾上时用。只管外观与读屏的「部分选中」，点击照常切换 |
| `disabled` | `boolean` | `false` | 禁用 |
| `size` | `"md"``"sm"` | — | 尺寸：sm 16px 框（表头里的开关）· md 18px；不写时跟 AkCheckboxGroup |
| `label` | `string` | — | 可访问名（aria-label）：不写文字（默认插槽）时必填 |
| `modelValue` | `boolean` | `false` | 单独用时是否勾上；放在 AkCheckboxGroup 里时由组的 v-model 决定，不用绑 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 文字 |

### AkCheckboxGroup 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `disabled` | `boolean` | `false` | 整组禁用 |
| `max` | `number` | — | 最多勾几个：选满后没勾的变成禁用（公开招募最多选 3 个标签） |
| `min` | `number` | — | 最少勾几个：到了之后勾着的不能再取消 |
| `size` | `"md"``"sm"` | — | 组内勾选框的尺寸 |
| `vertical` | `boolean` | `false` | 竖排（默认横排、放不下换行） |
| `label` | `string` | — | 读屏用的组名（aria-label）；放在带标签的 AkField 里时不用写 |
| `modelValue` | `CheckValue[]` | `[]` | 勾上的 value 数组 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: CheckValue[]]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkCheckbox（各写 value） |

## CSS 实现 

html

```
<div class="ak-check-group" role="group" aria-label="获得方式">
  <label class="ak-check"><input type="checkbox" checked>公开招募</label>
  <label class="ak-check is-disabled"><input type="checkbox" disabled>限定寻访</label>
</div>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
