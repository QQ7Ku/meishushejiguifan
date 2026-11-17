# 表单字段 Field

> 来源：https://mooncellwiki.github.io/prts-design/components/field
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

一个表单项 = 标签 + 控件 + 说明 / 校验文案（同 Naive UI 的 `n-form-item`）。表单组件（`.ak-input` `.ak-select` `.ak-check` …）与 Widget / Gadget 直接吐出的裸控件是**同一套规则**：36px 定高、2px 角、同一枚 ▾、同一张勾选框 / 圆形单选——裸控件的样子见 [MediaWiki 内容样式 · 表单控件](/prts-design/content/forms)。

各控件：[输入框](/prts-design/components/input) · [数字输入](/prts-design/components/input-number) · [下拉选择](/prts-design/components/select) · [复选框](/prts-design/components/checkbox) · [单选](/prts-design/components/radio) · [开关](/prts-design/components/switch) · [滑杆](/prts-design/components/slider) · [搜索框](/prts-design/components/search) · [键帽](/prts-design/components/kbd)。

## 标签 · 必填 · 说明 

`label` 渲染成 `<label for>`，指向字段里的第一个控件——控件的 id 由字段生成，不用自己写。`required` 在标签后出红色 `*`（读屏不念，控件自己带 `required`）。`feedback` 是标签下面那行小字，经 `aria-describedby` 关联到控件。

[Storybook ↗](/prts-design/storybook/?path=/story/components-field--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkInput from "../../Input/AkInput.vue";
import AkSelect from "../../Select/AkSelect.vue";
import AkField from "../AkField.vue";

const name = ref("");
const profession = ref("WARRIOR");
const note = ref("");
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
</script>

<template>
  <div class="ak-max-narrow">
    <AkField label="干员名称" required feedback="支持中文名 / 代号 / 拼音">
      <AkInput v-model="name" placeholder="例如：陈" />
    </AkField>
    <AkField label="职业">
      <AkSelect v-model="profession" :options="professions" />
    </AkField>
    <AkField label="备注">
      <AkInput v-model="note" type="textarea" placeholder="…" />
    </AkField>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 校验状态 

`validation-status="error"`：字段加 `.is-invalid`、控件红边 + `aria-invalid`、文案变红字；`success`：输入框绿边。原生的 `:user-invalid`（`required` 空着、`maxlength` 超了……）也是红边，只在用户改过之后才判，不会一进页面满屏红。

[Storybook ↗](/prts-design/storybook/?path=/story/components-field--validation)

vue

```
<script setup lang="ts">
import { computed, ref } from "vue";

import AkInput from "../../Input/AkInput.vue";
import AkField from "../AkField.vue";

/** 关卡编号：主线 1-7、资源 CE-6、活动 SV-8 …… */
const stage = ref("1-7");
const valid = computed(() => /^[A-Z]{0,4}\d*-\d+$/.test(stage.value.trim()));
const status = computed(() => (!stage.value.trim() ? undefined : valid.value ? "success" : "error"));
const feedback = computed(() => (status.value === "error" ? "格式不正确：应为「章节-序号」，如 1-7、CE-6" : "如 1-7、CE-6、SV-8"));
</script>

<template>
  <div class="ak-grid-2 ak-gap-6">
    <AkField label="关卡编号" :validation-status="status" :feedback="feedback">
      <AkInput v-model="stage" />
    </AkField>
    <AkField label="出错的字段" validation-status="error" feedback="格式不正确">
      <AkInput model-value="???" />
    </AkField>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 各种控件 

一组复选框 / 单选没有单个可以 `for` 的控件：字段的标签改由组 `aria-labelledby` 指向（`role="group"` / `radiogroup`），标签不写 `for`。步进器、开关这类定宽控件在字段里不拉满一行。

[Storybook ↗](/prts-design/storybook/?path=/story/components-field--controls)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkCheckbox from "../../Checkbox/AkCheckbox.vue";
import AkCheckboxGroup from "../../Checkbox/AkCheckboxGroup.vue";
import AkInputNumber from "../../InputNumber/AkInputNumber.vue";
import AkRadio from "../../Radio/AkRadio.vue";
import AkRadioGroup from "../../Radio/AkRadioGroup.vue";
import AkSwitch from "../../Switch/AkSwitch.vue";
import AkField from "../AkField.vue";

const obtain = ref(["recruit"]);
const position = ref("melee");
const trust = ref(100);
const potential = ref(true);
</script>

<template>
  <div class="ak-grid-2 ak-gap-6">
    <AkField label="获得方式" feedback="可多选">
      <AkCheckboxGroup v-model="obtain">
        <AkCheckbox value="recruit">公开招募</AkCheckbox>
        <AkCheckbox value="standard">标准寻访</AkCheckbox>
        <AkCheckbox value="limited">限定寻访</AkCheckbox>
        <AkCheckbox value="event">活动获得</AkCheckbox>
      </AkCheckboxGroup>
    </AkField>
    <AkField label="部署位置" required>
      <AkRadioGroup v-model="position">
        <AkRadio value="melee">近战位</AkRadio>
        <AkRadio value="ranged">远程位</AkRadio>
      </AkRadioGroup>
    </AkField>
    <AkField label="信赖（%）">
      <AkInputNumber v-model="trust" :min="0" :max="200" :step="10" />
    </AkField>
    <AkField label="潜能加成">
      <AkSwitch v-model="potential">
        <template #checked>按满潜能计算</template>
        <template #unchecked>按 1 潜能计算</template>
      </AkSwitch>
    </AkField>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

| 关系 | 输出 |
| --- | --- |
| 标签 → 控件 | `<label for="…-control">` + 控件 `id`（AkInput / AkSelect / AkInputNumber / AkSwitch / AkSlider / AkSearch / 单个 AkCheckbox） |
| 标签 → 组 | 组 `aria-labelledby="…-label"`（AkCheckboxGroup / AkRadioGroup），标签不写 `for` |
| 说明 / 错误 → 控件 | `aria-describedby="…-help"` |
| 出错 | 控件 `aria-invalid="true"`；文字说清楚怎么改（「应为 1-7、CE-6 这样」），不要只写「格式错误」 |
| 必填 | 文本类控件 `required`、单选组 `aria-required`；标签里的 `*` `aria-hidden` |

字段里自己写的原生控件，用默认插槽的参数拿 id：`<AkField label="…" v-slot="{ id }"><input :id="id"></AkField>`。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `label` | `string` | — | 标签文字：渲染成 <label for>，指向字段里的第一个控件（= 控件的可访问名）；复选组 / 单选组改由组 aria-labelledby 指向它 |
| `required` | `boolean` | `false` | 必填：标签后出红色 \*（读屏不念，控件自己带 required） |
| `feedback` | `string` | — | 说明或校验文案（.ak-help），经 aria-describedby 关联到控件；validation-status="error" 时是红字 |
| `validationStatus` | `"success"``"error"` | — | 校验状态（同 Naive 的 validation-status）：error = 红边 + 红字文案 + aria-invalid · success = 绿边 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 控件；自己写的原生控件用插槽参数里的 id 对上标签 |
| `label` | 标签内容（代替 label，要放链接 / 图标时用） |
| `feedback` | 说明 / 校验文案（代替 feedback） |

## CSS 实现 

模板 / Lua 输出：

html

```
<div class="ak-field is-invalid">
  <label class="ak-label" for="stage">关卡编号 <span class="req">*</span></label>
  <input class="ak-input" id="stage" aria-describedby="stage-help" aria-invalid="true">
  <div class="ak-help ak-help--error" id="stage-help">格式不正确</div>
</div>
```

`.ak-check-group`（一组勾选 / 单选的横排容器）、各尺寸档、禁用态等也在同一个文件里。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
