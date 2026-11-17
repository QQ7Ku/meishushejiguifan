# 剧情对话 Dialogue

> 来源：https://mooncellwiki.github.io/prts-design/arknights/dialogue
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

剧情页的对话，输出 `<dl>`：说话人一列右对齐（主色粗体）、台词一列。`AkDialogue` 里放若干 `AkDialogueLine`：`speaker` 是说话人；不写就是旁白——说话人列写「旁白」，整句灰色斜体（`.narrator`）。

## 对话与旁白 

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-dialogue--basic)

vue

```
<script setup lang="ts">
import AkDialogue from "../AkDialogue.vue";
import AkDialogueLine from "../AkDialogueLine.vue";
</script>

<template>
  <AkDialogue>
    <AkDialogueLine speaker="陈">博士，现在起由我担任你的护卫。</AkDialogueLine>
    <AkDialogueLine>陈将剑鞘靠在墙边，环视了一圈办公室。</AkDialogueLine>
    <AkDialogueLine speaker="博士">……辛苦了。</AkDialogueLine>
  </AkDialogue>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 分段 · 剧透 

分段就是几个 `AkDialogue` 之间插小标题。剧透用[档案](/prts-design/arknights/dossier)里的 `AkRedacted` 涂黑，台词里、说话人里（`#speaker` 插槽）都能放。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-dialogue--segments)

vue

```
<script setup lang="ts">
import AkRedacted from "../../Dossier/AkRedacted.vue";
import AkDialogue from "../AkDialogue.vue";
import AkDialogueLine from "../AkDialogueLine.vue";

/** 人员档案「档案资料四 · 权限记录」：说话人身份是剧透，涂黑 */
const record = [
  "你的父亲已经和你断绝关系。他同样也和我再无瓜葛，我不会在乎，他也不会。",
  "你想离开这里？",
  "可以。不过在那之前，我要说些事情给你听。",
  "——其实我知道那个老人是谁。",
];
</script>

<template>
  <div class="ak-flex-col ak-gap-6">
    <section>
      <div class="ak-overline ak-mb-3">信赖提升后交谈</div>
      <AkDialogue>
        <AkDialogueLine speaker="陈">也许有一天我会得到<AkRedacted>那个人</AkRedacted>的认可，在那之前......</AkDialogueLine>
        <AkDialogueLine speaker="陈">我......我从没试过去毫无保留地相信一个人，即使是<AkRedacted>那个人</AkRedacted>也......</AkDialogueLine>
      </AkDialogue>
    </section>
    <section>
      <div class="ak-overline ak-mb-3">档案资料四 · 权限记录</div>
      <AkDialogue>
        <AkDialogueLine v-for="(line, i) in record" :key="i">
          <template #speaker><AkRedacted>魏彦吾</AkRedacted></template>
          {{ line }}
        </AkDialogueLine>
        <AkDialogueLine>......模拟结束......正在回到主界面......</AkDialogueLine>
      </AkDialogue>
    </section>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## Vue API 

### AkDialogue 

只有默认插槽：若干 `AkDialogueLine`。

### AkDialogueLine 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `speaker` | `string` | — | 说话人；不写（也没有 #speaker 插槽）就是旁白：说话人列写「旁白」，整句灰色斜体 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 台词 |
| `speaker` | 说话人（代替 speaker，比如身份要涂黑时放 AkRedacted） |

## CSS 实现 

`dt` 显式 `margin: 0`（同[键值表](/prts-design/arknights/kv)）：否则正文的 `.mw-parser-output dt { margin-top }` 漏进来，说话人比台词低 8px。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
