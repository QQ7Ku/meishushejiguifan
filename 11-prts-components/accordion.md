# 折叠面板 Accordion

> 来源：https://mooncellwiki.github.io/prts-design/components/accordion
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

原生 `<details>` 折叠块：标题行左侧主色 `+` / `−`，展开后标题行加底色和分隔线。常见问题、档案、默认收起的长表格。正文里编辑手写的 `mw-collapsible` 是另一回事，这里是模板 / 小部件用的。

## 基本 [​](#基本)

Vue 版照 Naive UI 叫 `AkCollapse` + `AkCollapseItem`（同 `n-collapse`）：`v-model` 是展开项 `name` 的数组；标题用 `title` 或 `#header` 插槽，展开内容写在默认插槽里（正文排版照常生效）。

[Storybook ↗](/prts-design/storybook/?path=/story/components-accordion--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkCollapse from "../AkCollapse.vue";
import AkCollapseItem from "../AkCollapseItem.vue";

const expanded = ref(["akds"]);
</script>

<template>
  <div>
    <AkCollapse v-model="expanded">
      <AkCollapseItem name="akds" title="什么是 PRTS Design？">
        <p>明日方舟网页设计系统，为 prts.wiki 皮肤设计。</p>
      </AkCollapseItem>
      <AkCollapseItem name="codex" title="为什么不用 Codex 默认外观？">
        <p>Codex 令牌被桥接到 <code>--ak-*</code>，核心 / 扩展 UI 自动跟随；但视觉语言完全按明日方舟本体重建，而不是套一层配色。</p>
      </AkCollapseItem>
    </AkCollapse>
    <p class="ak-fs-sm ak-fg-muted ak-mb-0">v-model = {{ JSON.stringify(expanded) }}</p>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 手风琴 [​](#手风琴)

`accordion`：同一时间只展开一项，展开一项时收起其他。

[Storybook ↗](/prts-design/storybook/?path=/story/components-accordion--accordion)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkTag from "../../Tag/AkTag.vue";
import AkCollapse from "../AkCollapse.vue";
import AkCollapseItem from "../AkCollapseItem.vue";

const files = [
  { name: "basic", title: "基础档案", unlock: "初始开放", text: "【代号】陈　【性别】女　【战斗经验】四年" },
  { name: "health", title: "综合体检测试", unlock: "初始开放", text: "物理强度：优良　战场机动：标准　战斗技巧：卓越" },
  { name: "clinic", title: "临床诊断分析", unlock: "信赖 25%", text: "【应龙门近卫局要求，不予公开】" },
  { name: "story1", title: "档案资料一", unlock: "信赖 50%", text: "陈从来不在人前使用源石技艺……" },
];
const open = ref(["basic"]);
</script>

<template>
  <AkCollapse v-model="open" accordion>
    <AkCollapseItem v-for="f in files" :key="f.name" :name="f.name">
      <template #header>
        {{ f.title }} <AkTag size="sm" variant="outline">{{ f.unlock }}</AkTag>
      </template>
      <p>{{ f.text }}</p>
    </AkCollapseItem>
  </AkCollapse>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘与可访问性 [​](#键盘与可访问性)

每项就是原生 `<details>` + `<summary>`：按钮语义、展开状态、Enter / 空格开合都是浏览器给的，不另加 `aria-expanded`；页内查找（Ctrl+F）命中收起的内容时浏览器会自动展开，`v-model` 跟着同步。`<summary>` 带 `aria-controls` 指向内容区。另外补了 [WAI-ARIA Accordion](https://www.w3.org/WAI/ARIA/apg/patterns/accordion/) 的可选键盘支持：

| 键 | 行为 |
| --- | --- |
| `Enter` / `空格` | 展开 / 收起当前项 |
| `↓` / `↑` | 移到下一项 / 上一项的标题（首尾循环） |
| `Home` / `End` | 第一项 / 最后一项的标题 |

## Vue API [​](#vue-api)

### AkCollapse [​](#akcollapse)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `accordion` | `boolean` | `false` | 手风琴：同一时间只展开一项（展开一项时收起其他） |
| `modelValue` | `CollapseName[]` | `[]` | 展开项的 name 数组 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: CollapseName[]]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkCollapseItem |

### AkCollapseItem [​](#akcollapseitem)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name` | `CollapseName` | — | 标识，= AkCollapse 的 v-model 数组里的值；不写时自动生成（只能靠点击开合） |
| `title` | `string` | — | 标题；要放图标 / 标签时改用 #header 插槽 |

| 插槽 | 说明 |
| --- | --- |
| `header` | 标题（代替 title） |
| `default` | 展开后的内容（正文排版照常生效） |

## CSS 实现 [​](#css-实现)

模板直接输出 `<details>`，不需要脚本；要默认展开写 `open`。

html

```
<details class="ak-details" open>
  <summary>什么是 PRTS Design？</summary>
  <div class="ak-details__body">明日方舟网页设计系统，为 prts.wiki 皮肤设计。</div>
</details>
<details class="ak-details">
  <summary>为什么不用 Codex 默认外观？</summary>
  <div class="ak-details__body">Codex 令牌被桥接到 <code>--ak-*</code>，核心 / 扩展 UI 自动跟随；但视觉语言完全按明日方舟本体重建，而不是套一层配色。</div>
</details>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
