# 文字提示 Tooltip

> 来源：https://mooncellwiki.github.io/prts-design/components/tooltip
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

悬停或键盘聚焦时浮出的一句解释：术语（「晕眩」「无敌」）、缩写、图标按钮的名字、技力回复方式。反转底（暗主题白底黑字、亮主题黑底白字）、直角、12px 字。

## 两种实现 

|  | 纯 CSS：`data-ak-tip` | Vue：`AkTooltip` |
| --- | --- | --- |
| 写法 | 任意元素加 `data-ak-tip="文字"`（气泡是 `::after`） | `#trigger` 放触发元素，默认插槽放内容 |
| 内容 | 属性里的一句字符串 | 任意内容（加粗、换行、链接） |
| 方向 | 只在上方（贴着视口左 / 右缘时皮肤脚本左右平移） | `placement`：上 / 下 / 左 / 右，上下可左 / 右缘对齐；贴边放不下时自动换边 / 平移 |
| 触发 | 悬停 + 触发元素自己 `:focus-visible`（不可聚焦的元素由皮肤脚本补 `tabindex="0"`） | 悬停 + 聚焦（默认）/ 只聚焦 / 点击 / 手动（`v-model`） |
| 读屏 | 皮肤脚本补 `aria-describedby`，指向页面里一个隐藏的描述节点（提示与元素自身名称相同时跳过，如道具图 `alt` = 道具名）；无 JS 时不关联 | 触发元素 `aria-describedby` 指向 `role="tooltip"`；`Esc` 收起 |
| 用在 | MW 模板、Lua 输出、不需要 JS 的地方；`AkItem` 的 `tip` 也是它 | 小部件里需要上面任何一项时 |

两者外观相同（`.ak-tooltip` 与 `[data-ak-tip]::after` 用同一组令牌）。一两句话的长解释：纯 CSS 版加 `.ak-tip--wide`（允许折行、最宽 280）；Vue 版自动在 280px 处折行。

html

```
<p>属性表头：<span class="ak-term" data-ak-tip="不包括信赖及潜能加成">精英0 1级</span> · 技力：<span class="ak-tag ak-tag--outline" data-ak-tip="每次攻击回复1点技力">攻击回复</span></p>
<p>技能生效期间，持有效果：<span class="ak-term ak-tip--wide" data-ak-tip="无敌：无法被不同阵营选中（属于无法选择类效果）；受到的伤害与元素值变为 0；无法触发任何单位未绑定选择器的能力">无敌</span>、<span class="ak-term ak-tip--wide" data-ak-tip="晕眩免疫：使自身的晕眩失效，但不会清除相关 Buff">晕眩免疫</span></p>
```

给开关、复选框这类「外层 `<label>` + 原生控件」的组件加提示，`data-ak-tip` 直接写在组件上：`AkSwitch` / `AkCheckbox` / `AkRadio` / `AkInputNumber` / `AkSearch` 把它落到外层（原生 `<input>` 上画不出 `::after`），悬停名称文字也出提示，键盘聚焦到里面的控件时由 `:has(:focus-visible)` 显示。**不要为了提示再包一层 `<span data-ak-tip>`**：行内的 span 里控件按基线排，在 `align-items: center` 的一行里会比旁边没包的同类高几像素。

html

```
<div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
  <label class="ak-switch ak-switch--sm"><input type="checkbox" role="switch" checked>循环</label>
  <label class="ak-switch ak-switch--sm" data-ak-tip="一招的几段接着播：Begin → Loop → End"><input type="checkbox" role="switch">连播</label>
</div>
```

纯 CSS 版的气泡闲置时收成 0 宽（`max-width: 0` + `overflow: hidden`），不只是透明——透明的气泡仍占布局，靠近右缘的长提示会把手机页面撑出横向滚动、整页跟着缩小（干员页 130 枚提示就是这么发现的，见[贡献一个组件 · 命名与约定](/prts-design/guide/contributing#命名与约定)）。

显示出来的气泡也不伸出视口：连内边距最宽到视口宽减 16px，再长就折行；贴着视口左 / 右缘时，皮肤脚本在悬停 / 聚焦时量出气泡宽度，把它整条挪回视口里、两边各留 8px（写在触发元素的 `--ak-tip-dx` 上），小部件后来渲染的提示也算（prts-widgets 干员模型最右的「GIF 2x」原来伸出去约 80px，手机上一点整页就缩小）。站外没有这段脚本，只有宽度上限——贴边的触发元素改用 `AkTooltip`（自己会避让，见下文「方向」）。

## 基本用法 

写法同 Naive UI 的 `n-tooltip`。触发元素取 `#trigger` 里的第一个元素 / 组件；是 `<span class="ak-term">` 这种不可聚焦的元素时自动补 `tabindex="0"`，键盘用户也能看到。`trigger="click"` 改成点击开合（点外面 / `Esc` 收起）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tooltip--basic)

vue

```
<script setup lang="ts">
import AkButton from "../../Button/AkButton.vue";
import AkTooltip from "../AkTooltip.vue";

const effects = [
  { name: "无敌", tip: "无敌：无法被不同阵营选中（属于无法选择类效果）；受到的伤害与元素值变为 0；无法触发任何单位未绑定选择器的能力" },
  { name: "不可阻挡", tip: "不可阻挡：无法阻挡 / 被阻挡，自动解除阻挡" },
  { name: "晕眩免疫", tip: "晕眩免疫：使自身的晕眩失效，但不会清除相关 Buff" },
];
</script>

<template>
  <div class="ak-flex-col ak-gap-4 ak-p-4">
    <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
      <AkTooltip>
        <template #trigger><AkButton variant="outline">攻击回复</AkButton></template>
        每次攻击回复1点技力
      </AkTooltip>
      <AkTooltip>
        <template #trigger><AkButton icon="star" variant="ghost">监视</AkButton></template>
        将本页面加入您的监视列表
      </AkTooltip>
      <AkTooltip trigger="click">
        <template #trigger><AkButton variant="ghost">点击显示</AkButton></template>
        再点一次、点外面或按 Esc 收起
      </AkTooltip>
    </div>
    <!-- 术语：.ak-term 虚线下划线；span 不可聚焦，AkTooltip 自动补 tabindex="0" -->
    <p class="ak-m-0">
      技能生效期间，持有效果：<template v-for="(e, i) in effects" :key="e.name">{{ i ? "、" : "" }}<AkTooltip>
          <template #trigger><span class="ak-term">{{ e.name }}</span></template>
          {{ e.tip }}
        </AkTooltip></template>
    </p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 方向 

`placement`：`top`（默认）· `bottom` · `left` · `right`，间距 6px，同纯 CSS 版；`top-start` / `bottom-end` 这类是与触发元素左 / 右缘对齐（同 Naive）。`placement` 是首选方向，贴着视口边缘放不下时**自动避让**（同 Naive 的 flip + shift，不引定位库）：这一边放不下、对边放得下就换过去（上 ↔ 下、左 ↔ 右；左右都放不下改到上 / 下），上下方向的再左右平移进视口、两边各留 8px；气泡最宽到视口宽减 16px。打开时、气泡 / 窗口尺寸变了时重算，滚动时不跟（打开着的不来回翻）。下面用 `v-model` 让四个提示常显。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tooltip--placements)

vue

```
<script setup lang="ts">
import AkButton from "../../Button/AkButton.vue";
import AkTooltip from "../AkTooltip.vue";

// 这里用 v-model 让四个提示常显，只为看方向；平时不绑定，悬停 / 聚焦时才出现
</script>

<template>
  <div class="ak-flex ak-wrap ak-justify-center ak-items-center ak-gap-8 ak-p-6">
    <AkTooltip placement="left" trigger="manual" :model-value="true">
      <template #trigger><AkButton size="sm">left</AkButton></template>
      左侧
    </AkTooltip>
    <AkTooltip trigger="manual" :model-value="true">
      <template #trigger><AkButton size="sm">top</AkButton></template>
      上方（默认）
    </AkTooltip>
    <AkTooltip placement="bottom" trigger="manual" :model-value="true">
      <template #trigger><AkButton size="sm">bottom</AkButton></template>
      下方
    </AkTooltip>
    <AkTooltip placement="right" trigger="manual" :model-value="true">
      <template #trigger><AkButton size="sm">right</AkButton></template>
      右侧
    </AkTooltip>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 弹出卡片 Popover 

`AkPopover`（同 Naive 的 `n-popover`）：白底卡片、顶边 2px 主色条、最宽 360，能放标题（`title` / `#header`）、段落、链接、道具。**默认点击开合**——里面常有可点的东西，悬停弹出的卡片键盘用户够不着；只是富文本解释时可以 `trigger="hover"`。点外面 / `Esc` 收起，焦点在卡片里时还给触发元素。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tooltip--popover)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkButton from "../../Button/AkButton.vue";
import AkItem from "../../Item/AkItem.vue";
import AkItemList from "../../Item/AkItemList.vue";
import AkPopover from "../AkPopover.vue";

const materials = [
  { id: "3223", name: "近卫双芯片", count: 4 },
  { id: "30115", name: "聚合剂", count: 4 },
  { id: "30074", name: "白马醇", count: 6 },
  { id: "4001", name: "龙门币", count: "180K" },
];
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-4 ak-items-start">
    <AkPopover title="精英二 · 陈" placement="bottom-start">
      <template #trigger><AkButton variant="contrast">精英化材料</AkButton></template>
      <AkItemList class="ak-mt-2">
        <AkItem v-for="m in materials" :key="m.id" :src="asset(`item/framed/${m.id}.png`)" :name="m.name" :count="m.count" size="sm" />
      </AkItemList>
    </AkPopover>
    <p class="ak-m-0">
      所属势力：
      <AkPopover trigger="hover" title="龙门近卫局">
        <template #trigger><span class="ak-term">龙门近卫局</span></template>
        包含以下干员——陈、星熊、诗怀雅。<a href="#">查看势力页</a>
      </AkPopover>
    </p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 提示里只放**补充说明**：不要把完成任务必需的信息只写在提示里（触屏没有悬停）。
- 触发元素必须可聚焦；`AkTooltip` 对原生不可聚焦的元素自动补 `tabindex="0"`，组件（如 `AkButton`）假定自己渲染可聚焦元素。
- 悬停出现的提示可以移进去（离开后 `duration` 毫秒才消失），`Esc` 随时收起——满足 WCAG 1.4.13。
- 点击打开的 Popover 是非模态对话框：触发元素 `aria-haspopup="dialog"` + `aria-expanded` + `aria-controls`，卡片 `role="dialog"`、有标题时 `aria-labelledby` 指向标题；悬停打开的等同富文本提示（`aria-describedby`）。

## Vue API 

### AkTooltip 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `placement` | `"bottom"``"top"``"top-start"``"top-end"``"bottom-start"``"bottom-end"``"left"``"right"` | `"top"` | 出现在触发元素的哪一边：top（默认）· bottom · left · right；-start / -end 与触发元素左 / 右缘对齐。贴着视口边缘放不下时自动换到对边、左右平移进视口 |
| `trigger` | `"click"``"hover"``"focus"``"manual"` | `"hover"` | 触发方式：hover 悬停 + 键盘聚焦（默认）· focus 只看聚焦 · click 点击开合 · manual 只由 v-model 控制 |
| `delay` | `number` | `100` | 悬停多久后出现（ms） |
| `duration` | `number` | `100` | 移开多久后消失（ms）；这段时间里鼠标能移进提示里 |
| `disabled` | `boolean` | `false` | 禁用：不再出现 |
| `modelValue` | `boolean` | `false` | 是否显示；不绑定时由 trigger 决定 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `trigger` | 触发元素：取第一个元素 / 组件；只有文字时自动包一层 <span>，不可聚焦的元素自动补 tabindex="0" |
| `default` | 提示内容（短句；较长的解释会在 280px 处折行） |

### AkPopover 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | — | 标题（粗体一行）；也可以用 #header 插槽 |
| `placement` | `"bottom"``"top"``"top-start"``"top-end"``"bottom-start"``"bottom-end"``"left"``"right"` | `"bottom"` | 出现在触发元素的哪一边：bottom（默认）· top · left · right；-start / -end 与触发元素左 / 右缘对齐。贴着视口边缘放不下时自动换到对边、左右平移进视口 |
| `trigger` | `"click"``"hover"``"focus"``"manual"` | `"click"` | 触发方式：click 点击开合（默认）· hover 悬停 + 键盘聚焦 · focus 只看聚焦 · manual 只由 v-model 控制 |
| `delay` | `number` | `100` | hover 模式：悬停多久后出现（ms） |
| `duration` | `number` | `100` | hover 模式：移开多久后消失（ms）；这段时间里鼠标能移进卡片里 |
| `disabled` | `boolean` | `false` | 禁用：不再弹出 |
| `modelValue` | `boolean` | `false` | 是否显示；不绑定时由 trigger 决定 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `trigger` | 触发元素：取第一个元素 / 组件；只有文字时自动包一层 <span>，不可聚焦的元素自动补 tabindex="0" |
| `default` | 卡片正文 |
| `header` | 标题行（代替 title） |

## CSS 实现 

`.ak-term` 是术语的虚线下划线（悬停淡青底），`data-ak-tip` 与 `AkTooltip` 都可以配它。`.ak-tip-anchor` 与 `.ak-tooltip--*` / `.ak-popover--*` 方向修饰是给 Vue 版定位用的：外层相对定位，气泡贴在一边。外层是 `inline-flex`，大小就是触发元素的大小——包着开关、复选框、图片这类基线在底边的元素时，不会像 `inline-block` 那样在下面留出文字下沉的空间、把它顶高几像素；触发元素是术语（`.ak-term` / `.ak-rt-term`）时保持 `inline-block`，虚线下划线的位置不变。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
