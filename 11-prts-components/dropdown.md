# 下拉菜单 Dropdown

> 来源：https://mooncellwiki.github.io/prts-design/components/dropdown
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

点一枚按钮拉下一列动作或链接：页面动作簇的「更多」、用户菜单、排序 / 语种切换。白底卡片、1px 边、投影；悬停 / 键盘移到的项是**左 2px 青条 + 淡底**，与侧栏当前项、搜索面板高亮行同一语言；危险项红字；组之间一条分隔线。

## 基本用法 [​](#基本用法)

写法同 Naive UI 的 `n-dropdown`：默认插槽放触发元素（一般是 `AkButton`），`options` 给菜单项，选中触发 `select(key, option)`，之后菜单收起、焦点回到触发按钮。菜单项 `{ key, label, icon?, disabled?, danger?, href? }`，`{ type: "divider" }` 是分隔线。

给了 `value` 就是单选菜单：当前项高亮（`.is-active`）、`role="menuitemradio"` + `aria-checked`，打开时焦点落在它上面。

[Storybook ↗](/prts-design/storybook/?path=/story/components-dropdown--basic)

vue

```
<script setup lang="ts">
import { computed, ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkDropdown from "../AkDropdown.vue";
import type { DropdownMixedOption, DropdownOption } from "../types";

const actions: DropdownMixedOption[] = [
  { key: "move", label: "移动", icon: "move" },
  { key: "protect", label: "保护", icon: "lock" },
  { key: "purge", label: "刷新", icon: "refresh", disabled: true },
  { type: "divider" },
  { key: "delete", label: "删除", icon: "trash", danger: true },
];
const last = ref("");

const langs: DropdownOption[] = [
  { key: "cn", label: "中文-普通话" },
  { key: "yue", label: "中文-方言" },
  { key: "jp", label: "日文" },
  { key: "en", label: "英文" },
  { key: "kr", label: "韩文" },
];
const lang = ref("jp");
const langLabel = computed(() => langs.find(l => l.key === lang.value)?.label);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-start">
    <AkDropdown :options="actions" @select="(_, o) => (last = o.label)">
      <AkButton>页面操作 ▾</AkButton>
    </AkDropdown>
    <!-- value：单选菜单，当前项高亮、打开时焦点落在它上面 -->
    <AkDropdown :options="langs" :value="lang" label="语音语种" @select="k => (lang = String(k))">
      <AkButton variant="ghost">语种：{{ langLabel }} ▾</AkButton>
    </AkDropdown>
    <span v-if="last" class="ak-fs-sm ak-fg-muted ak-py-2">选了「{{ last }}」</span>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 分组 · 链接项 [​](#分组-·-链接项)

`{ type: "group", label, children }` 是带小标题的一组，相邻两组之间自动一条线——对应 MW 门户（`#p-cactions` / `#p-tb`）塞进卡片的写法。有 `href` 的项渲染成 `<a>`（照常跳转，也触发 `select`）。触发按钮靠右时用 `placement="bottom-end"` 让菜单右对齐。

[Storybook ↗](/prts-design/storybook/?path=/story/components-dropdown--groups)

vue

```
<script setup lang="ts">
import AkButton from "../../Button/AkButton.vue";
import AkDropdown from "../AkDropdown.vue";
import type { DropdownMixedOption } from "../types";

// = 页面动作簇的「更多」：两组 MW 门户（操作 #p-cactions · 工具 #p-tb），项是链接
const more: DropdownMixedOption[] = [
  {
    type: "group",
    label: "操作",
    children: [
      { key: "ca-move", label: "移动", icon: "move", href: "#" },
      { key: "ca-protect", label: "保护", icon: "lock", href: "#" },
      { key: "ca-delete", label: "删除", icon: "trash", href: "#" },
      { key: "ca-purge", label: "刷新", icon: "refresh", href: "#" },
    ],
  },
  {
    type: "group",
    label: "工具",
    children: [
      { key: "t-whatlinkshere", label: "链入页面", icon: "linkin", href: "#" },
      { key: "t-recentchangeslinked", label: "相关更改", icon: "list", href: "#" },
      { key: "t-permalink", label: "固定链接", icon: "link", href: "#" },
      { key: "t-info", label: "页面信息", icon: "info", href: "#" },
    ],
  },
];
</script>

<template>
  <!-- 触发按钮靠右，菜单用 bottom-end 右对齐 -->
  <div class="ak-flex ak-justify-end ak-items-start">
    <AkDropdown :options="more" placement="bottom-end">
      <AkButton icon="more" variant="ghost">更多</AkButton>
    </AkDropdown>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘 [​](#键盘)

按 [WAI-ARIA 菜单按钮](https://www.w3.org/WAI/ARIA/apg/patterns/menu-button/)：

| 焦点在 | 键 | 行为 |
| --- | --- | --- |
| 触发按钮 | `Enter` / `空格` / `↓` | 打开，焦点到第一项（单选菜单到当前项） |
| 触发按钮 | `↑` | 打开，焦点到最后一项 |
| 菜单 | `↓` / `↑` | 下一项 / 上一项（首尾循环；禁用项也停，但选不了） |
| 菜单 | `Home` / `End` | 第一项 / 最后一项 |
| 菜单 | 字母 / 汉字 | 跳到下一个以它开头的项 |
| 菜单 | `Enter` / `空格` | 选中（链接项跳转），收起，焦点回到按钮 |
| 菜单 | `Esc` | 收起，焦点回到按钮 |
| 菜单 | `Tab` | 收起，焦点照常移到后面的元素 |

鼠标点菜单外面也会收起。菜单是 `role="menu"`，名字默认取触发按钮的文字（`aria-labelledby`），`label` 可另给；组是 `role="group"` + `aria-label`（小标题只给眼睛看）。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `options`必填 | `DropdownMixedOption[]` | — | 菜单项：{ key, label, icon?, disabled?, danger?, href? }；{ type: "divider" } 分隔线；{ type: "group", label, children } 带小标题的一组 |
| `placement` | `"bottom-start"``"bottom-end"` | `"bottom-start"` | 菜单对齐：bottom-start 与触发元素左对齐（默认）· bottom-end 右对齐（靠页面右边的触发元素用） |
| `value` | `DropdownKey` | — | 当前选中项的 key：该项高亮，菜单变成单选菜单（menuitemradio + aria-checked），打开时落在它上面 |
| `label` | `string` | — | 读屏用的菜单名；不写时用触发元素的文字 |
| `disabled` | `boolean` | `false` | 禁用：触发元素点了不展开 |
| `modelValue` | `boolean` | `false` | 菜单是否展开 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |
| `select` | `[key: DropdownKey, option: DropdownOption]` | 选了一项（点击 / Enter / 空格）；之后菜单收起、焦点回到触发元素。有 href 的项照常跳转 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 触发元素：取第一个元素 / 组件，自动补 aria-haspopup / aria-expanded / aria-controls 与键盘；id 它自己写了就沿用，没写才补一个（菜单的 aria-labelledby 指向它） |

`options` 的类型 `DropdownMixedOption` / `DropdownOption` 从 `@mooncellwiki/prts-design-vue` 导出。

## CSS 实现 [​](#css-实现)

无 JS 的写法：`.ak-dropdown > details > summary + .ak-menu`（原生 `<details>` 开合，皮肤的用户菜单 / 「更多」就是这样）；有 JS 时在 `.ak-dropdown` 上切 `.is-open`（Vue 版就是这样）。菜单可以是 `ul.ak-menu > li > a`，也可以是 `div.ak-menu > nav.ak-menu__group( .ak-menu__label + ul )` 分组卡片，`.ak-menu__head` 是卡片抬头（用户名）；抬头本身是入口时写成 `a.ak-menu__head( .ak-avatar + .ak-menu__head-name )`，右端带箭头（皮肤的用户菜单：点抬头去用户页）。放在正文里时外层标 `ak-not-prose`，免得吃正文的列表符与链接色。

html

```
<div class="ak-dropdown ak-not-prose"><details><summary class="ak-btn">下拉菜单 ▾</summary><ul class="ak-menu"><li class="ak-menu__label">页面操作</li><li><a href="#">移动</a></li><li><a href="#">保护</a></li><li class="ak-menu__sep"></li><li><a href="#" class="ak-menu__item--danger">删除</a></li></ul></details></div>
<div style="height: 170px"></div>
```

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
