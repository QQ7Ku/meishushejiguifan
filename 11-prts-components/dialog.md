# 对话框 Dialog

> 来源：https://mooncellwiki.github.io/prts-design/components/dialog
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

打断当前操作、要读者明确回应的模态窗口：确认精英化、删除确认、引用格式。白底直角、1px 边、投影；标题栏左侧 4px 主色条 + 细底线，底栏 `--ak-bg-surface-2` 底、按钮右对齐；遮罩是 `--ak-bg-overlay` + 2px 模糊。

**先想想是不是非用不可**：能就地完成的（撤销代替确认、行内展开代替弹窗）就别弹。

## 确认对话框 

写法同 Naive UI 的 `n-modal`（`preset="dialog"`）：`v-model` 是否打开，`title` 标题，`positive-text` / `negative-text` 在底栏放确认（黑白反转，游戏内 `btn_on`）/ 取消两枚按钮，点了分别触发 `positive-click` / `negative-click` 并关闭。

[Storybook ↗](/prts-design/storybook/?path=/story/components-dialog--basic)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkButton from "../../Button/AkButton.vue";
import AkItem from "../../Item/AkItem.vue";
import AkItemList from "../../Item/AkItemList.vue";
import AkDialog from "../AkDialog.vue";

const open = ref(false);
const result = ref("");
const materials = [
  { id: "3223", name: "近卫双芯片", count: 4 },
  { id: "30115", name: "聚合剂", count: 4 },
  { id: "30074", name: "白马醇", count: 6 },
  { id: "4001", name: "龙门币", count: "180K" },
];
</script>

<template>
  <div class="ak-flex ak-gap-3 ak-items-start">
    <AkButton variant="primary" @click="open = true">打开对话框</AkButton>
    <span v-if="result" class="ak-fs-sm ak-fg-muted ak-py-2">{{ result }}</span>
    <AkDialog
      v-model="open"
      title="确认精英化"
      positive-text="确认"
      negative-text="取消"
      @positive-click="result = '已精英化至精英二'"
      @negative-click="result = '已取消'"
    >
      将「陈」精英化至 <b>精英二</b>，消耗以下材料：
      <AkItemList class="ak-mt-2">
        <AkItem v-for="m in materials" :key="m.id" :src="asset(`item/framed/${m.id}.png`)" :name="m.name" :count="m.count" />
      </AkItemList>
    </AkDialog>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 尺寸 · 自定义底栏 · 不可随手关 

`size`：`sm` 420 · `md` 640（默认）· `lg` 900 · `full` 占满 96vw × 92vh。`#header` / `#footer` 插槽换掉标题与底栏按钮。不可逆的动作可以关掉遮罩与 `Esc`（`:mask-closable="false"` `:close-on-esc="false"`）并去掉 ✕（`:closable="false"`），逼读者明确点一个按钮——危险按钮配 `stripes`。

[Storybook ↗](/prts-design/storybook/?path=/story/components-dialog--options)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkDialog from "../AkDialog.vue";

const small = ref(false);
const large = ref(false);
const confirm = ref(false);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-start">
    <AkButton @click="small = true">小（sm）</AkButton>
    <AkButton @click="large = true">大（lg）</AkButton>
    <AkButton variant="danger" @click="confirm = true">删除页面…</AkButton>

    <AkDialog v-model="small" size="sm" title="保存失败">会话已过期，请重新登录后再保存。你的修改仍保留在编辑框里。</AkDialog>

    <AkDialog v-model="large" size="lg" title="引用此页">
      <p>请按所需格式引用本页（修订版本 #271828，最后编辑于 2026-09-27 12:00）：</p>
      <pre>PRTS. 陈 [DB/OL]. prts.wiki, 2026-09-27.</pre>
    </AkDialog>

    <!-- 不可逆的动作：不给遮罩 / Esc 关，必须明确点一个按钮；底栏用 #footer 自己放 -->
    <AkDialog v-model="confirm" size="sm" :closable="false" :mask-closable="false" :close-on-esc="false">
      <template #header>删除「干员异格任务」？</template>
      页面及其全部 42 个修订版本将被删除，只有管理员可以恢复。
      <template #footer>
        <AkButton @click="confirm = false">保留</AkButton>
        <AkButton variant="danger" stripes @click="confirm = false">删除</AkButton>
      </template>
    </AkDialog>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

用原生 `<dialog>` + `showModal()`，浏览器负责大部分：

- **焦点**：打开时焦点进入对话框（第一个可聚焦元素，通常是 ✕）；背后的页面整个 inert，`Tab` 只在对话框的控件之间走（走到头会经过浏览器地址栏再回来——原生 `<dialog>` 的行为，不把人困在页面里）；关闭后焦点**还给打开前的元素**（`AkDialog` 自己记着，被卸载时也还）。
- **Esc** 关闭（`close-on-esc` 可关）；点遮罩关闭（`mask-closable` 可关）——从框里拖选到框外松手不算点遮罩。
- **名字**：`title` 作 `aria-labelledby`；没有标题时写 `label`（`aria-label`）。
- 对话框里的提示、下拉菜单按 `Esc` 只收起自己，不会连对话框一起关。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title` | `string` | — | 标题（同时作对话框的可访问名）；要带格式时用 #header 插槽 |
| `label` | `string` | — | 可访问名：没有标题时写（aria-label） |
| `size` | `"md"``"sm"``"lg"``"full"` | `"md"` | 宽度：sm 420 · md 640（默认）· lg 900 · full 占满 96vw × 92vh |
| `closable` | `boolean` | `true` | 标题栏右侧的 ✕ 关闭按钮 |
| `maskClosable` | `boolean` | `true` | 点遮罩关闭（同 Naive 的 mask-closable） |
| `closeOnEsc` | `boolean` | `true` | 按 Esc 关闭（同 Naive 的 close-on-esc） |
| `positiveText` | `string` | — | 确认按钮文字：写了就在底栏放一枚黑白反转的确认按钮，点了触发 positive-click 并关闭 |
| `negativeText` | `string` | — | 取消按钮文字：写了就在底栏放一枚默认按钮，点了触发 negative-click 并关闭 |
| `modelValue` | `boolean` | `false` | 是否打开 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |
| `positive-click` | `[]` | 点了确认按钮（之后关闭） |
| `negative-click` | `[]` | 点了取消按钮（之后关闭） |

| 插槽 | 说明 |
| --- | --- |
| `default` | 正文 |
| `header` | 标题（代替 title） |
| `footer` | 底栏按钮（代替 positive-text / negative-text 那两枚），右对齐 |

## CSS 实现 

模板 / Gadget 输出 `dialog.ak-dialog(.ak-dialog--sm …)` > `.ak-dialog__head( .ak-dialog__title + 关闭按钮 )` + `.ak-dialog__body` + `.ak-dialog__foot`，脚本里 `showModal()` 打开（预览站的 `data-dialog-open="#id"` / `data-dialog-close` 就是这么做的）。同一个文件里还有抽屉 `.ak-drawer--left/--right`（`.is-open` 滑入）与全屏遮罩 `.ak-overlay`——皮肤的手机侧栏在用，暂无 Vue 版。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
