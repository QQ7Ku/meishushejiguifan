# 计数徽标 Badge

> 来源：https://mooncellwiki.github.io/prts-design/components/badge
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

未读计数 / 未读点。**默认黄**（次强调「提示」），不走红：红只留给危险与 NEW / BREAKING，未读数不是高危信息；Echo 的 alerts 类（回退 / 权限变更）才用 `danger`。NEW 角标是[标签](/prts-design/components/tag)的 `variant="new"`，不是徽标。

数字是正文字体 700 12px + 等宽数字（不用 Bender：11–12px 下它比思源黑体细窄，黑字落在黄圆里读不清）；高 18px，单个数字是正圆、两位以上才拉成胶囊。

## 颜色 · 封顶 · 圆点 

写法同 Naive UI 的 `n-badge`：`value` 数字或短串，`max` 封顶显示「99+」，`dot` 只出一个点；`value` 为 0 时默认不显示，`show-zero` 让它显示。

[Storybook ↗](/prts-design/storybook/?path=/story/components-badge--variants)

vue

```
<script setup lang="ts">
import AkBadge from "../AkBadge.vue";
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-3 ak-items-center">
    <AkBadge :value="3" />
    <AkBadge :value="12" />
    <AkBadge :value="120" :max="99" label="超过 99 条未读" />
    <AkBadge :value="3" variant="accent" />
    <AkBadge :value="2" variant="danger" label="2 条回退提醒" />
    <AkBadge dot label="有新消息" />
    <AkBadge :value="0" show-zero />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 骑在内容上 

默认插槽写了内容（图标按钮、头像）时，徽标中心压在它的右上角（外壳 `.ak-badge-wrap` 只负责定位）；`show` 为 `false` 或计数归零时只剩内容。页眉铃铛那枚是骨架自己定位的，不走这里。

[Storybook ↗](/prts-design/storybook/?path=/story/components-badge--wrap)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkAvatar from "../../Avatar/AkAvatar.vue";
import AkButton from "../../Button/AkButton.vue";
import AkBadge from "../AkBadge.vue";

const unread = ref(3);
</script>

<template>
  <div class="ak-flex ak-wrap ak-gap-6 ak-items-center">
    <AkBadge :value="unread" :label="`${unread} 条未读通知`">
      <AkButton icon="bell" label="通知" variant="ghost" />
    </AkBadge>
    <AkBadge dot label="有新的讨论">
      <AkButton icon="talk" label="讨论" variant="ghost" />
    </AkBadge>
    <AkBadge :value="12" label="12 项监视列表更新">
      <AkAvatar size="lg" :src="asset('avatar/char_002_amiya_1p.png')" alt="阿米娅" />
    </AkBadge>
    <AkBadge :value="128" :max="99" variant="danger" label="超过 99 条回退提醒">
      <AkAvatar>DR</AkAvatar>
    </AkBadge>
    <AkButton size="sm" @click="unread = unread ? 0 : 3">{{ unread ? "全部已读" : "来一条新通知" }}</AkButton>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

光一个「3」读屏听不出是什么。写 `label`（「3 条未读通知」）时，数字对读屏隐藏、改念这句（`.ak-sr-only`）；**圆点徽标一定要写 `label`**，否则读屏什么也听不到。徽标本身不可聚焦，要能点的是它包住的那个按钮。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `value` | `string | number` | — | 数字或短串；数字为 0 时默认不显示（见 showZero） |
| `max` | `number` | — | 封顶：value 按数字大于它时显示「max+」（99+） |
| `dot` | `boolean` | `false` | 圆点：不显示数字，只提示「有新的」 |
| `show` | `boolean` | `true` | 是否显示徽标（同 Naive）；false 时只剩包住的内容 |
| `showZero` | `boolean` | `false` | value 为 0 时也显示 |
| `variant` | `"default"``"accent"``"danger"` | `"default"` | 颜色：default 黄（未读计数 / 点）· accent 主色 · danger 红（只给回退 / 权限变更这类 alerts） |
| `label` | `string` | — | 读屏文本（「3 条未读通知」）：写了就把数字对读屏隐藏、改念这句；圆点徽标一定要写 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 被标记的内容（头像、图标按钮…）：徽标骑在它右上角；不写就是独立的行内徽标 |

## CSS 实现 

行内徽标：`<span class="ak-badge">12</span>`；骑在内容上：`<span class="ak-badge-wrap"><button …>…</button><span class="ak-badge">3</span></span>`。页眉的 Echo 通知就是它：notices 默认黄，alerts 加 `.ak-badge--danger`。

18px 高、`border-box`（`min-width` = 高，所以一位数是正圆）；`padding-bottom: 1px` 补思源黑体上下伸不对称带来的约 0.7px 下沉，数字才坐在正中。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
