# 语音 Voice

> 来源：https://mooncellwiki.github.io/prts-design/arknights/voice
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

同现网的语音记录（`{{:干员/语音记录}}` 的 VoiceTable）：一条一行——播放钮 + 标题行（游戏内解锁条件 / 语音编号）+ 台词。当前语种只在上方的切换条里标，条目不再重复（「方言」这类也没有靠谱的缩写）。列表外框 + 悬停行底色。

## 语种切换 [​](#语种切换)

`AkVoiceList` 给了 `languages`（`{ value, label, cv? }`，同 Naive 的 options）就在上方出一排语种（带 CV 名），`v-model` 是当前语种。`AkVoice` 的 `text` / `src` 写成按语种的对象（`{ cn, yue, jp, en, kr }`），跟着列表切换，缺某个语种时退回第一项。切语种只换台词和音频地址。

音频地址由调用方给（示例用的是现网 torappu.prts.wiki 的同源地址）。播放用原生 `Audio`，**全页同时只放一条**：点另一条先停掉正在放的，再点同一条停止；播放中的条目播放钮变成暂停、标题行出现跳动的声波。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-voice--languages)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import type { VoiceLanguage } from "../context";
import AkVoice from "../AkVoice.vue";
import AkVoiceList from "../AkVoiceList.vue";

const languages: VoiceLanguage[] = [
  { value: "cn", label: "中文-普通话", cv: "虫虫" },
  { value: "yue", label: "中文-方言", cv: "包少爷" },
  { value: "jp", label: "日文", cv: "石上静香" },
  { value: "en", label: "英文", cv: "Amy Lennox" },
  { value: "kr", label: "韩文", cv: "郑侑廷" },
];
const lang = ref("cn");

/** 现网同源音频（与干员页 / VoiceTable 同一套地址）：torappu.prts.wiki/assets/audio/<语种目录>/<charId>/<编号>.mp3 */
const DIR: Record<string, string> = {
  cn: "voice_cn/char_010_chen",
  yue: "voice_custom/char_010_chen_cn_topolect",
  jp: "voice/char_010_chen",
  en: "voice_en/char_010_chen",
  kr: "voice_kr/char_010_chen",
};
const audio = (code: string) =>
  Object.fromEntries(
    Object.entries(DIR).map(([k, d]) => [k, `https://torappu.prts.wiki/assets/audio/${d}/${code.toLowerCase()}.mp3`]),
  );

const voices = [
  { code: "CN_001", title: "任命助理", text: { cn: "博士，现在起由我担任你的护卫。", yue: "博士，接下来就由我担任你的护卫。", jp: "ドクター、これよりキミの護衛を担当させてもらう。", en: "I will be your bodyguard, Doctor.", kr: "박사, 지금부터 너의 호위를 담당하겠다." } },
  { code: "CN_002", title: "交谈1", text: { cn: "我一直觉得你们罗德岛很可疑，现在也一样。", yue: "我一向都觉得你们罗德岛很可疑，现在这一刻也是这么觉得。", jp: "ロドスの動向については、ずっと不審に感じているところがある。もちろん、今でもだ。", en: "I've always had my suspicions about Rhodes Island. Still do.", kr: "로도스 아일랜드의 동향은 계속 수상하다 여기고 있어. 물론 지금도 마찬가지다." } },
  { code: "CN_003", title: "交谈2", text: { cn: "你怎么还在盯着我看？你的工作呢？", yue: "你怎么还在看着我？你手头要做的的东西呢？", jp: "なぜこちらをじっと見ている？仕事はどうしたんだ？", en: "Why are you staring at me? Nothing better to do?", kr: "뭘 그렇게 뚫어져라 쳐다보고 있는 거지? 일할 시간 아닌가?" } },
  { code: "CN_005", title: "晋升后交谈1", unlock: "提升至精英阶段1以查看", text: { cn: "龙门的大街小巷我都走过。码头、招牌、坡道，那些风景印在我的心上。我永远都不会忘记。", yue: "龙门的每一条街道每一条巷子我都行过。码头、招牌、斜坡，那些风景已经全部进了我的心。我永远都会记得。", jp: "龍門の街じゅう、いたる所に足を運んだ。港、街中の看板や坂道……心に刻まれたこの街の風景を、私はいつまでも忘れることはないだろう。", en: "I know every nook and cranny in Lungmen. The docks, the neon signs, the winding paths, I can see them all in my mind, clear as day. I'll never forget them.", kr: "용문 거리를 여기저기 돌아다녔다. 항구, 길거리의 간판이나 언덕길…… 마음속 깊이 새겨진 이 도시의 풍경을, 난 영원히 잊지 못하겠지." } },
  { code: "CN_007", title: "信赖提升后交谈1", unlock: "提升信赖至40%以查看", text: { cn: "也许有一天我会得到那个人的认可，在那之前......", yue: "或许有一天我会得到那个人的认可，在那天来到之前......", jp: "いつかはあの人に認めてもらえるかもしれない。その時までは……。", en: "Maybe one day that man will take note of my efforts, but until then…", kr: "언젠가는 그 사람에게 인정받을지도 몰라. 그때까지는……" } },
  { code: "CN_010", title: "闲置", text: { cn: "博士？睡着了？哼，真没紧张感。", yue: "博士？睡着了？哼，一点紧张感也冇。", jp: "……ドクター？眠っているのか？ふん、緊張感のないやつだ。", en: "Doctor? You fell asleep? Hmph. No nerves to keep you awake, then.", kr: "……박사? 자고 있는 건가? 흥, 긴장감 없는 녀석이군." } },
  { code: "CN_025", title: "作战中1", text: { cn: "斩！", yue: "斩！", jp: "斬！", en: "Slash!", kr: "참!" } },
  { code: "CN_034", title: "戳一下", text: { cn: "喂！干什么呢！", yue: "喂！做咩啊！", jp: "おい！何をする！", en: "Hey! What are you doing!", kr: "어이! 뭐하는 거야!" } },
];
</script>

<template>
  <AkVoiceList v-model="lang" :languages="languages">
    <template #header-extra><span class="ak-fs-xs ak-fg-muted">灰标 = 游戏内解锁条件 · 切语种只换文本与音频</span></template>
    <AkVoice
      v-for="v in voices"
      :key="v.code"
      :title="v.title"
      :unlock="v.unlock"
      :text="v.text"
      :src="audio(v.code)"
    />
  </AkVoiceList>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 文本与语种分开选 [​](#文本与语种分开选)

现网语音记录里「语言」和「语种」是两回事：**文本**是台词的语言（中文 / 日文 / 繁体中文 / 中文-方言 …），可以多选同时看、也可以一个都不选；**语种**是音频的差分（日语 / 中文-普通话 / 中文-方言 …），必须选一个，带 CV 名。`AkVoiceList` 再给 `texts`（`{ value, label }`）就多出「文本」那排多选芯片，`v-model:shown-texts` 是选中的几种（不绑时默认第一个）；`languages` 只管语种（音频）。两排在 `.ak-voice-toolbar` 里排成一行，放不下时语种整组换到第二行。这时 `AkVoice` 的 `text` 按文本键给对象，选中的几种一种一行（`.ak-voice__text` 是 `pre-line`）；`src` 仍按语种给对象。prts-widgets 的 VoiceTable 就是这么接的。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-voice--split)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import type { VoiceLanguage, VoiceText } from "../context";
import AkVoice from "../AkVoice.vue";
import AkVoiceList from "../AkVoiceList.vue";

/** 文本（台词的语言）：多选，可以一个都不选；不绑 v-model:shown-texts 时默认第一个 */
const texts: VoiceText[] = [
  { value: "cn", label: "中文" },
  { value: "jp", label: "日文" },
  { value: "en", label: "英文" },
  { value: "kr", label: "韩文" },
  { value: "yue", label: "中文-方言" },
];
const shown = ref(["cn"]);

/** 语种（音频差分）：单选，带 CV 名；只换音频 */
const languages: VoiceLanguage[] = [
  { value: "jp", label: "日语", cv: "石上静香" },
  { value: "cn", label: "中文-普通话", cv: "虫虫" },
  { value: "yue", label: "中文-方言", cv: "包少爷" },
  { value: "kr", label: "韩语", cv: "郑侑廷" },
  { value: "en", label: "英语", cv: "Amy Lennox" },
];
const lang = ref("jp");
const DIR: Record<string, string> = {
  cn: "voice_cn/char_010_chen",
  yue: "voice_custom/char_010_chen_cn_topolect",
  jp: "voice/char_010_chen",
  en: "voice_en/char_010_chen",
  kr: "voice_kr/char_010_chen",
};
const audio = (code: string) =>
  Object.fromEntries(
    Object.entries(DIR).map(([k, d]) => [k, `https://torappu.prts.wiki/assets/audio/${d}/${code.toLowerCase()}.mp3`]),
  );

const voices = [
  { code: "CN_001", title: "任命助理", text: { cn: "博士，现在起由我担任你的护卫。", yue: "博士，接下来就由我担任你的护卫。", jp: "ドクター、これよりキミの護衛を担当させてもらう。", en: "I will be your bodyguard, Doctor.", kr: "박사, 지금부터 너의 호위를 담당하겠다." } },
  { code: "CN_002", title: "交谈1", text: { cn: "我一直觉得你们罗德岛很可疑，现在也一样。", yue: "我一向都觉得你们罗德岛很可疑，现在这一刻也是这么觉得。", jp: "ロドスの動向については、ずっと不審に感じているところがある。もちろん、今でもだ。", en: "I've always had my suspicions about Rhodes Island. Still do.", kr: "로도스 아일랜드의 동향은 계속 수상하다 여기고 있어. 물론 지금도 마찬가지다." } },
  { code: "CN_005", title: "晋升后交谈1", unlock: "提升至精英阶段1以查看", text: { cn: "龙门的大街小巷我都走过。码头、招牌、坡道，那些风景印在我的心上。我永远都不会忘记。", yue: "龙门的每一条街道每一条巷子我都行过。码头、招牌、斜坡，那些风景已经全部进了我的心。我永远都会记得。", jp: "龍門の街じゅう、いたる所に足を運んだ。港、街中の看板や坂道……心に刻まれたこの街の風景を、私はいつまでも忘れることはないだろう。", en: "I know every nook and cranny in Lungmen. The docks, the neon signs, the winding paths, I can see them all in my mind, clear as day. I'll never forget them.", kr: "용문 거리를 여기저기 돌아다녔다. 항구, 길거리의 간판이나 언덕길…… 마음속 깊이 새겨진 이 도시의 풍경을, 난 영원히 잊지 못하겠지." } },
  { code: "CN_034", title: "戳一下", text: { cn: "喂！干什么呢！", yue: "喂！做咩啊！", jp: "おい！何をする！", en: "Hey! What are you doing!", kr: "어이! 뭐하는 거야!" } },
];
</script>

<template>
  <AkVoiceList v-model="lang" v-model:shown-texts="shown" :languages="languages" :texts="texts">
    <AkVoice v-for="v in voices" :key="v.code" :title="v.title" :unlock="v.unlock" :text="v.text" :src="audio(v.code)" />
  </AkVoiceList>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 单一语种 [​](#单一语种)

不给 `languages`：没有切换条，`text` / `src` 直接写串，`lang` 只用来标台词的 HTML lang。没有音频地址的条目播放钮压暗、禁用。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-voice--basic)

vue

```
<script setup lang="ts">
import AkVoice from "../AkVoice.vue";
import AkVoiceList from "../AkVoiceList.vue";

const src = (code: string) => `https://torappu.prts.wiki/assets/audio/voice_cn/char_010_chen/${code}.mp3`;
/** 下载给 wav 原文件；?filename= 是现网 VoiceTable 的约定，服务端据此写 Content-Disposition */
const wav = (code: string, name: string) =>
  `https://torappu.prts.wiki/assets/audio/voice_cn/char_010_chen/${code}.wav?filename=${encodeURIComponent(name)}.wav`;
</script>

<template>
  <!-- 不给 languages：没有切换条，text / src 直接写串，lang 只用来标台词的 HTML lang；download 出下载钮；unlock 是标题下面一行的灰标 -->
  <AkVoiceList>
    <AkVoice
      title="任命助理"
      lang="cn"
      text="博士，现在起由我担任你的护卫。"
      :src="src('cn_001')"
      :download="wav('cn_001', '任命助理')"
      download-name="任命助理.wav"
    />
    <AkVoice
      title="晋升后交谈1"
      lang="cn"
      unlock="提升至精英阶段1以查看"
      text="龙门的大街小巷我都走过。码头、招牌、坡道，那些风景印在我的心上。我永远都不会忘记。"
      :src="src('cn_005')"
    />
    <AkVoice title="标题" text="明日方舟。" />
    <AkVoice
      title="新年祝福"
      lang="cn"
      unlock="游戏内仅在每年1月1日-1月4日显示"
      text="博士，关于上次的报告——博士也还在工作吗？嗯，倒是在我的意料之外，毕竟外面已经闹成这样，我以为打开门就能看到一屋子正在庆祝的干员。新年快乐，好了，这是打印出的事件报告——"
      :src="src('cn_038')"
    />
  </AkVoiceList>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 下载 [​](#下载)

`download` 给了地址（同 `src`，一个串或按语种的对象）就在播放钮旁出一枚同款的下载钮（黑方块 + 线稿图标 `#i-download`），是原生 `<a download>`；`download-name` 是存下来的文件名。模板里写 `<a class="ak-voice__download" href="…" download="…" aria-label="下载 任命助理"><svg class="ak-icon"><use href="#i-download"/></svg></a>`，紧跟在播放钮后面。现网 `/语音记录` 页给的是 torappu 的 wav 地址（`?filename=` 由服务端写进 Content-Disposition），干员页里嵌入的那份不给。

## 键盘与可访问性 [​](#键盘与可访问性)

- 播放钮是 `<button>`，读作「播放 + 标题」，`aria-pressed` 表示正在播放；下载是 `<a download>`，读作「下载 + 标题」。
- 语种切换是一组 `aria-pressed` 的切换按钮（`role="group"`，以「语种」小标为组名）。
- 台词带 HTML `lang`（cn → zh-Hans、yue → yue、jp → ja、en → en、kr → ko），读屏按语种发音。
- 语种切换条与播放钮都带 `data-no-toggle`：皮肤脚本会在 document 上替模板输出的纯 CSS 芯片 / 播放钮翻 `is-active` / `is-playing`（给[CSS 实现](#css-实现)的演示用），Vue 版的状态归自己管，要退出那层委托，否则两边各翻一次。自己用 `.ak-voice` 结构 + 脚本管状态时也照此标。

## Vue API [​](#vue-api)

### AkVoiceList [​](#akvoicelist)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `languages` | `VoiceLanguage[]` | `[]` | 语种选项 { value, label, cv? }（同 Naive 的 options）；不给就没有语种切换条 |
| `texts` | `VoiceText[]` | `[]` | 文本语言选项 { value, label }；不给就没有文本切换条，台词跟语种走 |
| `modelValue` | `string` | — | 当前语种（languages 里的 value）；不绑定时是第一个 |
| `shownTexts` | `string[]` | — | 选中的文本语言（texts 里的 value，可多个、可为空）；不绑定时默认第一个 |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: string | undefined]` |  |
| `update:shownTexts` | `[value: string[] | undefined]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 若干 AkVoice |
| `header-extra` | 语种切换条末尾的附注（「灰标 = 游戏内解锁条件」） |

### AkVoice [​](#akvoice)

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `title`必填 | `string` | — | 语音标题（任命助理 / 交谈1 …） |
| `text`必填 | `string | Record<string, string>` | — | 台词：一个串；或按语种的对象 { cn, jp, en, kr, yue }（取当前语种，缺了退回第一项）；列表给了 texts 时按文本键的对象（选中的几种一种一行） |
| `src` | `string | Record<string, string>` | — | 音频地址：一个串，或按语种的对象（同 text）；取不到时播放钮禁用 |
| `unlock` | `string` | — | 游戏内解锁 / 显示条件（提升至精英阶段1以查看 · 游戏内仅在每年1月1日-1月4日显示），标题下面单独一行的灰标 |
| `lang` | `string` | — | 语种键：不在 AkVoiceList 里时用它取 text / src、标台词的 HTML lang；在列表里时跟列表的 v-model |
| `download` | `string | Record<string, string>` | — | 下载地址（现网是 torappu 的 wav）：一个串，或按语种的对象（同 src）；给了就在播放钮旁出一枚同款下载钮 |
| `downloadName` | `string` | — | 下载存成的文件名（<a download> 的值，任命助理.wav）；不写由浏览器按地址定 |

## CSS 实现 [​](#css-实现)

切换条 `.ak-voice-langs`（原是干员页私有的 `.op-voice-langs`；几条并排时外面包 `.ak-voice-toolbar`）和禁用的播放钮 `.ak-voice__play:disabled` 随 Vue 版一起收进了 voice.css。皮肤脚本（document 级委托）会替模板输出的芯片翻 `is-active` / `aria-pressed`、替播放钮翻 `is-playing`（只是演示态，不会真的放音频）；状态自己管的容器标 `data-no-toggle`。

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
