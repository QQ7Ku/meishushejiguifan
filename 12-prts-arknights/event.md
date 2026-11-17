# 活动 Event

> 来源：https://mooncellwiki.github.io/prts-design/arknights/event
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

活动卡和倒计时。活动卡从上到下是：21:9 横幅、强调色的类型行、标题、起止时间，时间下面可以放倒计时或按钮。倒计时由三枚灰块组成，每块是一个数字加一个单位；灰块和 `.ak-level--badge` 是同一种读数，不做成白块，否则看起来像一排按钮，也会压过标题。

## 活动卡 [​](#活动卡)

`status="live"` 在类型行前加一个闪烁的红点（进行中），`status="ended"` 让类型行转灰（已结束）。横幅地址由调用方用 `cover` 传入，按 21:9 裁切铺满；不传时不显示横幅，也可以用 `#cover` 插槽自己放。倒计时放在默认插槽里。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-event--cards)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkCountdown from "../AkCountdown.vue";
import AkEvent from "../AkEvent.vue";

/** 示例里的截止时刻取「现在 + 3 天 14 小时 27 分」，读数总是 03 / 14 / 27；实际用活动的结束时间（"2026-09-07T03:59:00+08:00"） */
const until = Date.now() + (3 * 24 + 14) * 36e5 + 27.5 * 6e4;
</script>

<template>
  <div class="ak-grid-2 ak-gap-3 ak-items-start">
    <AkEvent
      status="live"
      kind="限时活动 · 进行中"
      title="奇象巡展"
      time="2026.08.08 16:00 — 09.07 03:59"
      :cover="asset('mainpage/banner-qixiang.jpg')"
    >
      <AkCountdown class="ak-mt-2" :until="until" />
    </AkEvent>
    <AkEvent
      status="ended"
      kind="寻访 · 已结束"
      title="联合行动 23"
      time="2026.08.12 10:00 — 08.26 03:59"
      :cover="asset('mainpage/banner-gacha-joint23.jpg')"
    />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 倒计时 [​](#倒计时)

`AkCountdown` 只需要一个截止时刻 `until`，剩余时间按当前时间实时算。也可以像 Naive 的 `NCountdown` 那样给 `duration`（剩余毫秒），从挂载时开始倒数。`active=false` 暂停：`until` 模式下读数停住，恢复后跳回实际剩余；`duration` 模式下恢复后从停下的地方接着倒数。

读数规则同首页脚本：剩余不足一天时从「时」开始显示（00 时也照常显示），一直显示到 `precision`（默认到分，`sec` 到秒）。`parts` 限制最多显示几格，例如首页幻灯片用 2 格，显示成「03 days 14 hrs」，不足一天时是「14 hrs 27 mins」；数值为 1 时单位用单数（01 day / 01 hr / 01 min）。倒数到 0 时触发 `finish`，之后整个倒计时不再渲染。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-event--countdown)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import AkButton from "../../Button/AkButton.vue";
import AkCountdown from "../AkCountdown.vue";

const now = Date.now();
/** 示例截止时刻都相对「现在」取，读数固定 */
const event = now + (3 * 24 + 14) * 36e5 + 27.5 * 6e4;
const pool = now + 14 * 36e5 + 10.5 * 6e4;

/** 剿灭作战 & 周常任务刷新：下一个周一 04:00（服务器时间 UTC+8） */
function nextWeekly() {
  const cst = new Date(Date.now() + 8 * 36e5); // 挪到 UTC+8，下面一律用 UTC 读写
  const at = Date.UTC(cst.getUTCFullYear(), cst.getUTCMonth(), cst.getUTCDate(), 4);
  let add = (8 - cst.getUTCDay()) % 7;
  if (add === 0 && at <= cst.getTime()) add = 7;
  return at + add * 864e5 - 8 * 36e5;
}
const weekly = nextWeekly();

const active = ref(true);
const done = ref(false);
</script>

<template>
  <div class="ak-grid ak-gap-4">
    <div class="ak-flex ak-wrap ak-items-center ak-gap-3">
      <span class="ak-fs-sm ak-fg-muted">活动剩余</span>
      <AkCountdown :until="event" />
    </div>
    <div class="ak-flex ak-wrap ak-items-center ak-gap-3">
      <span class="ak-fs-sm ak-fg-muted">首页幻灯片（parts=2）</span>
      <AkCountdown :until="event" :parts="2" />
      <AkCountdown :until="pool" :parts="2" />
    </div>
    <div class="ak-flex ak-wrap ak-items-center ak-gap-3">
      <span class="ak-fs-sm ak-fg-muted">剿灭作战 &amp; 周常任务刷新 · 每周一 04:00</span>
      <AkCountdown :until="weekly" />
    </div>
    <div class="ak-flex ak-wrap ak-items-center ak-gap-3">
      <span class="ak-fs-sm ak-fg-muted">到秒 · duration 90 秒</span>
      <AkCountdown v-if="!done" :duration="90e3" precision="sec" :active="active" @finish="done = true" />
      <span v-else class="ak-fs-sm">已结束</span>
      <!-- 图标按钮：label 作读屏名与 title，随状态换成「暂停」/「继续」 -->
      <AkButton size="sm" :icon="active ? 'pause' : 'play'" :label="active ? '暂停' : '继续'" :disabled="done" @click="active = !active" />
    </div>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 [​](#可访问性)

- 倒计时是 `role="timer"`，它隐含 `aria-live="off"`，每分钟刷新时不会打断读屏器。
- 灰块上的单位是英文（DAYS / HRS / MINS），读屏名另给一份中文「剩余 3 天 14 小时 27 分」，灰块本身对读屏器隐藏。
- 只刷新到需要的精度：到分时下一次唤醒就排在读数变化的那一刻，不每秒重渲染。

## Vue API [​](#vue-api)

### AkEvent [​](#akevent)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title`必填 | `string` | — | 活动名 |
| `kind` | `string` | — | 标题上方的类型行（Side Story · 进行中 / 寻访 · 已结束），强调色、大写字距 |
| `time` | `string` | — | 起止时间（2026.08.08 16:00 — 09.07 03:59） |
| `cover` | `string` | — | 横幅图地址（21:9 裁切铺满）；不写且没有 #cover 插槽时不出横幅 |
| `status` | `"default"``"live"``"ended"` | `"default"` | 状态：live 进行中（类型行前闪烁红点）· ended 已结束（类型行转灰）· default 不标 |

| 插槽 | 说明 |
| --- | --- |
| `cover` | 横幅（代替 cover）：自己放 .ak-event\_\_banner 元素 |
| `title` | 标题（代替 title，如包一层链接） |
| `default` | 时间下面的内容：倒计时 AkCountdown、按钮 |

### AkCountdown [​](#akcountdown)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `until` | `string | number | Date` | — | 截止时刻（ISO 串 / 时间戳 / Date），按当前时间实时倒数——活动、卡池、周常刷新都用它 |
| `duration` | `number` | — | 或者给剩余毫秒数，从挂载时起倒数（同 Naive 的 NCountdown）；有 until 时忽略 |
| `active` | `boolean` | `true` | 是否走字；false 时停在当前读数（duration 模式下恢复后接着倒数） |
| `precision` | `"min"``"sec"` | `"min"` | 最小单位：min 到分（默认，每分钟刷新）· sec 到秒 |
| `parts` | `number` | — | 最多显示几格，从最大的单位数起：2 → 「03 days 14 hrs」/ 不足一天时「14 hrs 27 mins」（首页幻灯片）；不写则一直显示到 precision |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `finish` | `[]` | 倒数到 0（之后整个倒计时不再渲染） |

## CSS 实现 [​](#css-实现)

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
