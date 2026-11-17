# 轻提示 Toast

> 来源：https://mooncellwiki.github.io/prts-design/components/toast
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

操作之后在视口右下角弹出的一句反馈——「页面已加入监视列表」「保存失败：会话过期」——几秒后自己消失，底部 2px 进度条走完即关。白底卡片、左侧 4px 等级色条、投影，从下方滑入；多条从下往上叠。与 [消息 Message](/prts-design/components/message) 同族：Message 留在页面里，Toast 是浮层、会消失。

**只放无关紧要、错过也不要紧的反馈**：需要读者处理的错误（表单校验、权限不足）用 Message 或对话框。

## 用法 [​](#用法)

同 Naive UI 的 `useMessage()` + `n-message-provider`：应用 / 小部件的最外层包一个 `AkToastProvider`，里面任意组件 `useToast()` 拿到 API：

vue

```
<!-- App.vue -->
<AkToastProvider>
  <RouterView />
</AkToastProvider>
```

ts

```
// 任意子组件
import { useToast } from "@mooncellwiki/prts-design-vue";

const toast = useToast();
toast.success("页面已加入监视列表", { title: "完成" });
toast.error("保存失败：会话过期");
```

`info`（主色）· `success` · `warning` · `error`（红，= `.ak-toast--danger`）四个方法，也可以 `create(内容, { variant })`；都返回 `{ destroy() }`。`destroyAll()` 全部关掉。

[Storybook ↗](/prts-design/storybook/?path=/story/components-toast--variants)

vue

```
<script setup lang="ts">
import { defineComponent, h } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkToastProvider from "../AkToastProvider.vue";
import { useToast } from "../useToast";

/** 实际项目里这是 AkToastProvider 里面的任意子组件：useToast() 要在 provider 的后代里调用 */
const Actions = defineComponent(() => {
  const toast = useToast();
  const button = (text: string, onClick: () => void) => h(AkButton, { onClick }, () => text);
  return () =>
    h("div", { class: "ak-flex ak-wrap ak-gap-3" }, [
      button("信息", () => toast.info("本页数据取自游戏版本 2.7.61")),
      button("成功", () => toast.success("页面已加入监视列表", { title: "完成" })),
      button("警告", () => toast.warning("本页含未实装内容，数据可能变动")),
      button("失败", () => toast.error("保存失败：会话过期")),
    ]);
});
</script>

<template>
  <div>
    <AkToastProvider>
      <Actions />
    </AkToastProvider>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 选项 [​](#选项)

单条选项：`title` 标题行 · `duration` 停留毫秒（`0` 常驻）· `closable` ✕ 按钮 · `onClose` 关掉时回调；正文可以是函数（返回 vnode，放链接 / 加粗）。不写的跟 `AkToastProvider` 的 `duration`（默认 5000）/ `closable`（默认有）。`max` 限制同时显示的条数，多了先关最早的。也可以拿 provider 的 `ref` 直接调——方法和 `useToast()` 一样。

[Storybook ↗](/prts-design/storybook/?path=/story/components-toast--options)

vue

```
<script setup lang="ts">
import { h, useTemplateRef } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkToastProvider from "../AkToastProvider.vue";

// 也可以拿 provider 的 ref 直接调，方法和 useToast() 一样
const toaster = useTemplateRef("toaster");

const sticky = () => toaster.value?.warning("编辑冲突：另一位编辑者刚刚保存了本页", { title: "请先合并", duration: 0 });
const quick = () => toaster.value?.success("已复制 --ak-accent", { duration: 1500, closable: false });
const rich = () =>
  toaster.value?.info(() => ["已提交到 ", h("a", { href: "#" }, "Special:最近更改"), "，刷新后可见"], { duration: 8000 });
</script>

<template>
  <div>
    <AkToastProvider ref="toaster" :max="3">
      <div class="ak-flex ak-wrap ak-gap-3">
        <AkButton @click="sticky">常驻（duration: 0）</AkButton>
        <AkButton @click="quick">1.5 秒、不带 ✕</AkButton>
        <AkButton @click="rich">带链接的正文</AkButton>
        <AkButton variant="ghost" @click="toaster?.destroyAll()">全部关掉</AkButton>
      </div>
    </AkToastProvider>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- 提示栈是一个 `role="region"`（名字「通知」，`label` 可改）+ `aria-live="polite"` 的区域，一直在页面上：新弹出的提示读屏会念，但不打断当前朗读。
- **悬停或键盘聚焦在某条上时暂停它的倒计时**（进度条一起停，`keep-alive-on-hover`，默认开），移开后接着走——给读得慢的人留时间（WCAG 2.2.1）。
- 常驻的（`duration: 0`）一定要留 ✕。

## Vue API [​](#vue-api)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `duration` | `number` | `5000` | 默认停留时长（ms），0 = 不自动消失；单条可以在选项里改 |
| `closable` | `boolean` | `true` | 默认显示 ✕ 关闭按钮 |
| `keepAliveOnHover` | `boolean` | `true` | 鼠标悬停 / 键盘聚焦在某条上时暂停它的倒计时（同 Naive 的 keep-alive-on-hover） |
| `max` | `number` | — | 最多同时显示几条，多了先关最早的 |
| `to` | `string | HTMLElement` | — | 把提示栈传送到别处（Teleport 的 to，如 "body"）；不写就渲染在原地——它是 fixed 定位，一般用不着 |
| `label` | `string` | `"通知"` | 读屏里提示栈的名字 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 应用内容：里面的组件可以 useToast() |

`useToast()` 返回 `ToastApi`：`create / info / success / warning / error(内容, 选项?) => { destroy }` 与 `destroyAll()`；类型 `ToastApi` / `ToastOptions` / `ToastVariant` 从 `@mooncellwiki/prts-design-vue` 导出。

## CSS 实现 [​](#css-实现)

`div.ak-toasts`（fixed 右下）> `div.ak-toast(.ak-toast--success …)` > `div.ak-toast__body( .ak-toast__title + 正文 )` + 关闭按钮（借 `.ak-message__close`）+ `i.ak-toast__progress`。进度条时长读 `--_dur`（默认 5s），`.is-paused` 暂停。无 Vue 的页面由 Gadget 按这个结构插（预览站 `preview.js` 的 `akdsToast()` 就是）。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
