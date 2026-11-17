# 标签页 Tabs

> 来源：https://mooncellwiki.github.io/prts-design/components/tabs
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

同一位置切换几组平级内容。正文里编辑写的 `<tabber>`（TabberNeue）由皮肤单独换肤（见 [MediaWiki 内容样式 · TabberNeue](/prts-design/content/tabber)），这里是模板 / 小部件用的版本。

## 变体 

下划线（默认，选中项青色下划线 + 左上角标）· 胶囊底 · 游戏内块状（选中项黑白反转）。

写法同 Naive UI 的 `n-tabs`：`AkTabs` 里放若干 `AkTabPane`（页签 + 面板，`name` 是 `v-model` 的值，`tab` 是页签文字）；只要一排页签当筛选、内容在别处时，改放 `AkTab`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tabs--variants)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkTab from "../AkTab.vue";
import AkTabPane from "../AkTabPane.vue";
import AkTabs from "../AkTabs.vue";

const tab = ref("op");
const scope = ref("all");
const server = ref("cn");
</script>

<template>
  <div class="ak-flex-col ak-gap-6">
    <AkTabs v-model="tab" label="干员资料">
      <AkTabPane name="op" tab="干员">下划线标签页（默认）：选中项青色下划线 + 左上角标。</AkTabPane>
      <AkTabPane name="skill" tab="技能">技能面板。</AkTabPane>
      <AkTabPane name="module" tab="模组">模组面板。</AkTabPane>
      <AkTabPane name="archive" tab="档案" disabled>档案面板。</AkTabPane>
    </AkTabs>
    <div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
      <AkTabs v-model="scope" variant="pill" label="范围">
        <AkTab name="all" tab="全部" />
        <AkTab name="main" tab="主线" />
        <AkTab name="event" tab="活动" />
      </AkTabs>
      <AkTabs v-model="server" variant="block" label="服务器">
        <AkTab name="cn">国服</AkTab>
        <AkTab name="bili">B 服</AkTab>
        <AkTab name="global">国际服</AkTab>
      </AkTabs>
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 竖排 

`placement="left"`：干员档案那种，页签竖排在左、面板在右，上下方向键切换。页签要第二行小字（解锁条件）时用 `AkTabPane` 的 `#tab` 插槽，小字放 `<small>`。

面板默认只渲染当前页（`display-directive="if"`，同 Naive）；`show` 全部渲染、非当前页 `hidden`；`show:lazy` 第一次切到时才渲染，之后保留——档案、语音这类切来切去的长内容用它。

[Storybook ↗](/prts-design/storybook/?path=/story/components-tabs--vertical)

vue

```
<script setup lang="ts">
import AkTabPane from "../AkTabPane.vue";
import AkTabs from "../AkTabs.vue";

const files = [
  { name: "basic", title: "基础档案", unlock: "初始开放", text: "【代号】陈　【性别】女　【战斗经验】四年" },
  { name: "health", title: "综合体检测试", unlock: "初始开放", text: "物理强度：优良　战场机动：标准　战斗技巧：卓越" },
  { name: "clinic", title: "临床诊断分析", unlock: "信赖 25%", text: "【应龙门近卫局要求，不予公开】" },
  { name: "story1", title: "档案资料一", unlock: "信赖 50%", text: "陈从来不在人前使用源石技艺……" },
  { name: "promo", title: "晋升记录", unlock: "精英二", text: "她一定会回来。" },
];
</script>

<template>
  <AkTabs placement="left" label="人员档案">
    <AkTabPane v-for="f in files" :key="f.name" :name="f.name" display-directive="show:lazy">
      <template #tab>{{ f.title }}<small>{{ f.unlock }}</small></template>
      {{ f.text }}
    </AkTabPane>
  </AkTabs>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘 

| 键 | 行为 |
| --- | --- |
| `←` / `→`（竖排为 `↑` / `↓`） | 移到上一个 / 下一个页签并选中 |
| `Home` / `End` | 第一个 / 最后一个 |
| `Tab` | 从页签组进入当前面板 |

只有当前页签在 Tab 顺序里（roving tabindex），符合 [WAI-ARIA Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) 的自动激活模式。

## Vue API 

### AkTabs 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `variant` | `"underline"``"pill"``"block"` | `"underline"` | 外观：underline 下划线（默认）· pill 胶囊底 · block 游戏内块状（选中黑白反转） |
| `placement` | `"top"``"left"` | `"top"` | 页签位置：top 在上（默认）· left 竖排在左、面板在右（干员档案），上下方向键切换 |
| `label` | `string` | — | 读屏用的页签组名 |
| `modelValue` | `Name` | — | 当前页签的 name；不绑定时默认第一个 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: Name | undefined]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | AkTabPane（页签 + 面板）或 AkTab（只有页签）若干 |

### AkTabPane 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string | number` | — | 标识，= AkTabs 的 v-model 值 |
| `tab` | `string` | — | 页签文字；要第二行小字（「信赖 25%」）或图标时改用 #tab 插槽 |
| `disabled` | `boolean` | `false` | 禁用：页签不可选，方向键跳过 |
| `displayDirective` | `"if"``"show"``"show:lazy"` | — | 面板渲染方式（同 Naive）：if 只渲染当前页（默认）· show 全部渲染、非当前页 hidden · show:lazy 第一次切到时渲染，之后保留 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 面板内容 |
| `tab` | 页签内容（代替 tab） |

### AkTab 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string | number` | — | 标识，= AkTabs 的 v-model 值 |
| `tab` | `string` | — | 页签文字（也可以写在默认插槽里） |
| `disabled` | `boolean` | `false` | 禁用：页签不可选，方向键跳过 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 页签内容（代替 tab） |

## CSS 实现 

CSS 实现只负责外观；切换由皮肤脚本（`data-tabs` 分组）或 Vue 组件负责。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
