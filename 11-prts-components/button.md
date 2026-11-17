# 按钮 Button

> 来源：https://mooncellwiki.github.io/prts-design/components/button/
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

## 变体 

`primary` 主色实底，一处只放一枚；`contrast` 是游戏内 `btn_on` 的黑白反转，给「开始行动」这类强动作；`danger` 叠斜纹表示不可逆；`link` 用于行内次要动作。

[Storybook ↗](/prts-design/storybook/?path=/story/components-button--variants)

vue

```
<script setup lang="ts">
import AkButton from "../AkButton.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkButton variant="primary">主要动作</AkButton>
    <AkButton variant="contrast">开始行动</AkButton>
    <AkButton>默认</AkButton>
    <AkButton variant="outline">描边</AkButton>
    <AkButton variant="ghost">幽灵</AkButton>
    <AkButton variant="danger" stripes>危险</AkButton>
    <AkButton variant="link">链接按钮</AkButton>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 

高 24 / 30 / 36 / 44 / 56。默认 36 与输入框、裸控件同一刻度，一行里混排齐平。

[Storybook ↗](/prts-design/storybook/?path=/story/components-button--sizes)

vue

```
<script setup lang="ts">
import AkButton from "../AkButton.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkButton size="xs">XS</AkButton>
    <AkButton size="sm">SM</AkButton>
    <AkButton>MD</AkButton>
    <AkButton size="lg">LG</AkButton>
    <AkButton size="xl" variant="primary">XL 主动作</AkButton>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 图标 

写法同 Naive UI 的 `n-button`：`icon` 放图标，`icon-placement="right"` 放到文字后面；不写文字就是方形图标按钮，这时 `label` 必填（同时作 `aria-label` 与 `title`）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-button--icons)

vue

```
<script setup lang="ts">
import AkButton from "../AkButton.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkButton icon="edit" variant="primary">编辑</AkButton>
    <AkButton icon="history">查看历史</AkButton>
    <AkButton icon="arrow-ne" icon-placement="right" variant="ghost">前往现网</AkButton>
    <AkButton icon="search" label="搜索" />
    <AkButton icon="more" label="更多" variant="ghost" />
    <AkButton icon="x" label="关闭" size="sm" variant="ghost" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 状态 

禁用、加载中（文字隐去、居中一枚圆弧转圈、`aria-busy`）、链接形态（有 `href` 时渲染成 `<a>`，禁用时用 `aria-disabled` 并去掉 `href`）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-button--states)

vue

```
<script setup lang="ts">
import AkButton from "../AkButton.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkButton disabled>禁用</AkButton>
    <AkButton variant="primary" loading>保存中</AkButton>
    <AkButton href="#">链接形态（&lt;a&gt;）</AkButton>
    <AkButton href="#" disabled>禁用的链接</AkButton>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 按钮组 

一排贴在一起的按钮（同 Naive 的 `n-button-group`，纯容器）：相邻边框重叠，`size` 给组内按钮统一尺寸。多选一的分段控件用[单选按钮组](/prts-design/components/radio)（`AkRadioGroup` + `AkRadioButton`，同 Naive 的 `n-radio-button`），精英阶段切换用[阶段选择器](/prts-design/arknights/phase-tabs)，不用按钮组。

[Storybook ↗](/prts-design/storybook/?path=/story/components-button--group)

vue

```
<script setup lang="ts">
import AkButton from "../AkButton.vue";
import AkButtonGroup from "../AkButtonGroup.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-center">
    <AkButtonGroup label="页面操作">
      <AkButton icon="edit">编辑</AkButton>
      <AkButton icon="history">历史</AkButton>
      <AkButton icon="star" label="监视" />
    </AkButtonGroup>
    <AkButtonGroup label="导出" size="sm">
      <AkButton>JSON</AkButton>
      <AkButton>CSV</AkButton>
      <AkButton icon="print" label="打印" />
    </AkButtonGroup>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkButton 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"default"``"outline"``"danger"``"link"``"primary"``"contrast"``"ghost"` | `"default"` | 外观：primary = 主色实底（一处一枚）· contrast = 黑白反转（游戏内 btn\_on）· outline 描边 · ghost 幽灵 · danger 危险 · link 链接样式 |
| `size` | `"md"``"sm"``"lg"``"xl"``"xs"` | — | 尺寸：xs 24 · sm 30 · md 36 · lg 44 · xl 56（px 高）；不写时跟 AkButtonGroup，再不然是 md |
| `icon` | `IconName` | — | 图标；没有文字（不写默认插槽）时是方形的图标按钮，这时 label 必填 |
| `iconPlacement` | `"left"``"right"` | `"left"` | 图标在文字左边还是右边（同 Naive 的 icon-placement） |
| `label` | `string` | — | 可访问名（aria-label；图标按钮同时作 title）——只有图标的按钮必填，有文字的一般不写 |
| `block` | `boolean` | `false` | 占满容器宽度 |
| `pill` | `boolean` | `false` | 胶囊圆角（系统默认直角，只在确需时用） |
| `stripes` | `boolean` | `false` | 危险按钮叠斜纹（只配 variant="danger"） |
| `loading` | `boolean` | `false` | 加载中：文字隐去，居中转圈，不可点 |
| `disabled` | `boolean` | `false` |  |
| `href` | `string` | — | 有 href 时渲染成 <a>（禁用时 aria-disabled） |
| `type` | `"button"``"submit"``"reset"` | `"button"` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 按钮文字；不写就是图标按钮 |

### AkButtonGroup 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `size` | `"md"``"sm"``"lg"``"xl"``"xs"` | — | 组内按钮的默认尺寸（按钮自己写了 size 以按钮为准） |
| `label` | `string` | — | 读屏用的组名：一组有意义的动作时写 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkButton |

## CSS 实现 

模板 / Lua 输出 `<button class="ak-btn ak-btn--primary">`（或 `<a class="ak-btn">`）即可；裸 `<button>`（Widget 直接吐出的、没有 class 的）由皮肤兜底成同样 36px 高的默认外观。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
