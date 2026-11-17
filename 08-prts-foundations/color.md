# 色彩

> 来源：https://mooncellwiki.github.io/prts-design/foundations/color
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

# 色彩 [​](#色彩)

令牌分三层：**原始色板** `--ak-{hue}-{step}`（每个颜色标明出处）→ **语义令牌** `--ak-{role}`（随主题变化，组件只用这一层）→ **Codex 桥接**（MediaWiki 核心 / 扩展 UI 自动跟随）。

```
Primitive  --ak-gray-800 / --ak-cyan-500 / --ak-rarity-6 …          有出处的原始色
Semantic   --ak-bg-surface / --ak-fg-muted / --ak-accent / --ak-link …  随主题变化
Bridge     --background-color-base / --color-progressive …           Codex / MW 令牌映射
```

命名一律 `--ak-{group}-{name}`。源文件是 `packages/tokens/src/` 下的 JSON5（W3C DTCG 格式）；`pnpm tokens`（`packages/tokens/build.ts`，Style Dictionary）生成 `packages/css/src/tokens.css`（CSS 自定义属性；同一份复制到 `packages/tokens/tokens.css`，随 `@mooncellwiki/prts-design-tokens` 发布）、`packages/css/src/bridge-codex.css`（Codex 桥接，见下）与 `packages/tokens/tokens.json`（机器可读，下面的表都读它）——这些都是生成物，改令牌改 JSON5，CI 会检查生成物是否最新。

点色块复制 `var(--…)`。

## 原始色板 [​](#原始色板)

组件不直接用原始色；需要时经由语义令牌引用。每个颜色都有出处，不用「看起来像」：

| 令牌 | 出处 | 用途 |
| --- | --- | --- |
| gray-50 `#F5F5F5` | 游戏亮色面板 `left_bkg` | 亮色画布 |
| gray-500 / 600 / 700 | 游戏 `btn_done` / `max_bg` / `btn_account_center` |  |
| gray-800 `#313131` | **游戏标准按钮** `btn_off` / `black_btn` | 默认按钮 |
| gray-200 / 400 / 850 / 900 | 官网 | 官网灰阶、面板 |
| gray-950 `#181818` / 1000 `#000` | 游戏深底 / 官网页面底 |  |
| **cyan-500** `#18D1FF` | 官网 CSS（42 处） | 暗色主题主强调 |
| cyan-400 / 300 / 200 | 游戏 `select_round` / 官网 | 暗色链接、悬停 |
| **blue-500** `#0098DC` | 游戏 `selected_back`、`bkg_openserver`、`<ba.vup>` | 亮色主题主强调；富文本增益 |
| blue-400 / 600 | `<ba.kw>` / 游戏 `toggle_on` | 关键词 / 开关开启 |
| **yellow-500** `#FFD800` | 游戏 `go_to_shop`、`image_exp_circle`；稀有度星 `#FFDE00` | 次强调、稀有度、提示 |
| yellow-600 `#FFC90E` | `<ga.subtitle>` | 卡池副标题 |
| lime-400 / 500 / 600 | 游戏 SP cost 底 / 官网 | SP、自动回复 |
| orange-400 / 500 / 600 | 六星色 / `<ba.rem>` / 游戏任务追踪 | 六星、提醒 |
| red-400 / 500 / 600 | `<ba.vdown>` / `<ba.enemy>` / 游戏 NEW | 减益、敌方、NEW |
| red-700 / 800 / 900 | BREAKING NEWS / 确认按钮 / 专精三角 | 危险横幅、危险动作、专精 |
| green-500 `#2FAC78` | `<ba.gild>` | 镀层 / 成功 |
| purple-400 `#BF96ED` | 四星色 |  |

### 中性色 [​](#中性色)

取自游戏内 UI（标准按钮 `#313131`、亮色面板 `#F5F5F5`）与官网（`#1D1F20`、`#8D8D8D`、`#D2D2D2`）。

### 品牌：青 / 蓝 [​](#品牌-青-蓝)

### 黄 · 绿 · 橙 · 红 · 紫 [​](#黄-·-绿-·-橙-·-红-·-紫)

### 稀有度（官方色阶） [​](#稀有度-官方色阶)

来源 `gamedata/excel/sandbox_table.json → charRarityColorList`，不用「看起来像」的近似色。填充色 `--ak-rarity-N`；文字用 `--ak-rarity-N-text`——暗色下就是填充色，亮色下加深到白底可读（见下面[稀有度文字色](#稀有度文字色)）。

用法：容器写 `data-rarity="1–6"`，子元素用 `var(--ak-r)`（填充）/ `var(--ak-r-text)`（文字）；便捷类 `.ak-r-text` `.ak-r-bg` `.ak-r-border` `.ak-r-bar`（左侧色条）`.ak-r-chip` `.ak-r-swatch`。星级组件见[稀有度 Rarity](/prts-design/arknights/rarity)。

html

```
<div class="ak-flex-col ak-gap-3">
  <div class="ak-flex ak-gap-2 ak-wrap"><span data-rarity="1" class="ak-r-chip">★1 #BABABA</span><span data-rarity="2" class="ak-r-chip">★2 #D3DC35</span><span data-rarity="3" class="ak-r-chip">★3 #82C5F5</span><span data-rarity="4" class="ak-r-chip">★4 #BF96ED</span><span data-rarity="5" class="ak-r-chip">★5 #EFD691</span><span data-rarity="6" class="ak-r-chip">★6 #FF9433</span></div>
  <div class="ak-flex ak-gap-4 ak-wrap"><span data-rarity="6" class="ak-r-text ak-fw-700">六星文字色</span><span data-rarity="5" class="ak-r-text ak-fw-700">五星文字色</span><span data-rarity="4" class="ak-r-text ak-fw-700">四星文字色</span><span data-rarity="3" class="ak-r-text ak-fw-700">三星文字色</span><span data-rarity="2" class="ak-r-text ak-fw-700">二星文字色</span><span data-rarity="1" class="ak-r-text ak-fw-700">一星文字色</span></div>
</div>
```

### 技能 SP 回复类型 [​](#技能-sp-回复类型)

自动回复 / 攻击回复 / 受击回复 / 被动四种回复方式各一个色（游戏内 SP 条的颜色）；颜色不是唯一的信息载体，芯片上总有文字。触发方式不用彩色，用黑白反转：手动触发黑底白字、自动触发灰底。组件见[技能 SP](/prts-design/arknights/sp)。

html

```
<div class="ak-flex ak-gap-2 ak-wrap ak-items-center"><span class="ak-sp">自动回复</span><span class="ak-sp ak-sp--attack">攻击回复</span><span class="ak-sp ak-sp--hit">受击回复</span><span class="ak-sp ak-sp--passive-recovery">被动回复</span><span class="ak-sp ak-sp--passive">被动</span><span class="ak-sp-trigger">手动触发</span><span class="ak-sp-trigger ak-sp-trigger--auto">自动触发</span><span class="ak-sp-cost">25</span><span class="ak-sp-init">14</span></div>
```

## 语义令牌 [​](#语义令牌)

两套主题等价（终端 / 档案是双正典），同一个令牌在两列里各自的值。组件、模板、TemplateStyles 只用这一层。

### 背景 / 表面 [​](#背景-表面)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-bg-canvas` | `var(--ak-gray-50)` | `#0f0f10` |  |
| `--ak-bg-surface` | `var(--ak-gray-0)` | `#1a1b1c` |  |
| `--ak-bg-surface-2` | `var(--ak-gray-50)` | `#232425` |  |
| `--ak-bg-surface-3` | `var(--ak-gray-100)` | `#2d2e30` |  |
| `--ak-bg-inset` | `#eef0f1` | `#141516` |  |
| `--ak-bg-hover` | `rgba(0, 0, 0, 0.05)` | `rgba(255, 255, 255, 0.06)` |  |
| `--ak-bg-active` | `rgba(0, 0, 0, 0.09)` | `rgba(255, 255, 255, 0.11)` |  |
| `--ak-bg-inverse` | `var(--ak-gray-900)` | `var(--ak-gray-0)` |  |
| `--ak-bg-overlay` | `rgba(15, 15, 16, 0.6)` | `rgba(0, 0, 0, 0.7)` |  |

### 文字 [​](#文字)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-fg` | `var(--ak-gray-900)` | `#f0f0f0` |  |
| `--ak-fg-secondary` | `#454647` | `#c9c9c9` |  |
| `--ak-fg-muted` | `var(--ak-gray-500)` | `var(--ak-gray-400)` |  |
| `--ak-fg-subtle` | `var(--ak-gray-400)` | `#6f7071` |  |
| `--ak-fg-disabled` | `var(--ak-gray-300)` | `#4a4b4c` |  |
| `--ak-fg-inverse` | `var(--ak-gray-0)` | `var(--ak-gray-950)` |  |

### 强调色 [​](#强调色)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-accent` | `var(--ak-blue-500)` | `var(--ak-cyan-500)` | 亮色下用游戏内蓝 #0098DC，对比更稳 暗：暗色下用官网青 #18D1FF |
| `--ak-accent-hover` | `var(--ak-blue-600)` | `var(--ak-cyan-200)` |  |
| `--ak-accent-fg` | `#ffffff` | `#000000` |  |
| `--ak-accent-subtle` | `rgba(0, 152, 220, 0.10)` | `rgba(24, 209, 255, 0.10)` |  |
| `--ak-accent-muted` | `rgba(0, 152, 220, 0.28)` | `rgba(24, 209, 255, 0.30)` |  |
| `--ak-accent-2` | `var(--ak-yellow-500)` | `var(--ak-yellow-500)` | 次强调：黄 |
| `--ak-accent-2-fg` | `#1d1f20` | `#000000` |  |

### 链接 [​](#链接)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-link` | `#0072a8` | `var(--ak-cyan-300)` |  |
| `--ak-link-hover` | `#005a82` | `var(--ak-cyan-200)` |  |
| `--ak-link-visited` | `#2c6b88` | `#3f8ea4` | 已访问 = 链接色「褪一层」而不是换成紫：= #0072a8 × 55% + --ak-fg-muted #626262 × 45%（变灰变钝，白底 5.9:1）。 写死算好的实色，不用 color-mix——链接色是常量，没必要让正文链接吃一个 2023 年的特性；重算：round(l\*k + m\*(1-k))。 :visited 里的 alpha 会被浏览器丢掉（防历史嗅探），所以必须是实色；悬停回 --ak-link-hover 暗：已访问 = 青 #5ddcff × 62% + 画布 #0f0f10 × 38%（视觉上就是「62% 透明的链接色」，写成实色）；黑底 5:1、surface 4.6:1 |
| `--ak-link-new` | `#c82a36` | `#ff6b6b` | 红链 |
| `--ak-link-external` | `#0072a8` | `var(--ak-cyan-300)` |  |

已访问链接不换色相（不用紫），就是链接色「褪一层」：亮色 = 链接色 55% + `--ak-fg-muted` 45%，暗色 = 青 62% + 画布 38%；悬停回到完整的 `--ak-link-hover`。**写死算好的实色**：不用 `color-mix`（正文链接不该依赖较新的特性），也不能写半透明——浏览器为防历史嗅探会丢掉 `:visited` 颜色的 alpha。活动主题要改链接色，就连 `--ak-link-visited` 一起给。样张见[排版 · 链接与行内](/prts-design/content/typography#链接与行内)；对比度要求见[可访问性](/prts-design/foundations/accessibility#对比度)。

### 边框 / 焦点 / 选区 [​](#边框-焦点-选区)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-border` | `#d9dbdc` | `#2f3133` |  |
| `--ak-border-subtle` | `#e8eaeb` | `#262729` |  |
| `--ak-border-strong` | `#b1b1b1` | `#4a4c4f` |  |
| `--ak-border-accent` | `var(--ak-blue-500)` | `var(--ak-cyan-500)` |  |
| `--ak-focus` | `var(--ak-blue-500)` | `var(--ak-cyan-500)` |  |
| `--ak-selection` | `rgba(0, 152, 220, 0.25)` | `rgba(24, 209, 255, 0.28)` |  |
| `--ak-mark-bg` | `rgba(0, 152, 220, 0.14)` | `rgba(24, 209, 255, 0.20)` | <mark> 文字高亮：同 :target 引用 / 表格 is-hl / 面板当前项一套「淡青底」语言，比 --ak-accent-subtle 深一档、比 ::selection 浅一档（选中时仍看得出变化）； 不用黄——黄留给稀有度 / 警示条纹，且半透明黄叠在暗色面上会发闷成橄榄色；不用红——红链 / danger 已占满红的语义，红底高亮会像报错，红链落在里面也看不清 |

### 状态 [​](#状态)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-info` | `#0072a8` | `var(--ak-cyan-300)` |  |
| `--ak-info-bg` | `rgba(0, 152, 220, 0.10)` | `rgba(24, 209, 255, 0.10)` |  |
| `--ak-success` | `#1f8f5f` | `#4fd18f` |  |
| `--ak-success-bg` | `rgba(47, 172, 120, 0.12)` | `rgba(47, 172, 120, 0.14)` |  |
| `--ak-warning` | `#b86f00` | `#ffb340` |  |
| `--ak-warning-bg` | `rgba(244, 152, 0, 0.14)` | `rgba(244, 152, 0, 0.14)` |  |
| `--ak-danger` | `#c8102e` | `#ff5c5c` |  |
| `--ak-danger-bg` | `rgba(200, 42, 54, 0.10)` | `rgba(216, 60, 60, 0.14)` |  |

### 游戏内富文本 [​](#游戏内富文本)

来源 `gamedata_const.richTextStyles`：技能 / 天赋 / 模组描述里的 `<@ba.vup>…</>` 等标签，Lua / 模板转成 `<span class="ak-rt-*">`（也认别名 `.ba-vup` 这类）。暗色取游戏原色，亮色逐个加深到 AA 对比度。渲染与术语提示见[富文本 Rich text](/prts-design/arknights/rich-text)。

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-rt-vup` | `#007bb5` | `var(--ak-blue-500)` |  |
| `--ak-rt-vdown` | `#d0421c` | `var(--ak-red-400)` |  |
| `--ak-rt-rem` | `#b86f00` | `var(--ak-orange-500)` |  |
| `--ak-rt-kw` | `#0077b3` | `var(--ak-blue-400)` |  |
| `--ak-rt-imp` | `#d40000` | `#ff3b3b` |  |
| `--ak-rt-drop` | `#1d78b8` | `#36acff` |  |
| `--ak-rt-enemy` | `#c22f2f` | `var(--ak-red-500)` |  |
| `--ak-rt-gild` | `#1f8f5f` | `var(--ak-green-500)` |  |
| `--ak-rt-acrem` | `#727272` | `#8a8a8a` |  |
| `--ak-rt-level` | `#9a7b00` | `#ffde00` |  |

html

```
<table class="wikitable ak-compact" style="width:100%">
<tr><th>标签</th><th>类名</th><th>游戏内色</th><th>示例</th></tr>
<tr><td>&lt;@ba.vup&gt;</td><td>.ak-rt-vup</td><td>#0098DC</td><td>攻击力<span class="ak-rt-vup">+50%</span></td></tr>
<tr><td>&lt;@ba.vdown&gt;</td><td>.ak-rt-vdown</td><td>#FF6237</td><td>防御力<span class="ak-rt-vdown">-30%</span></td></tr>
<tr><td>&lt;@ba.rem&gt;</td><td>.ak-rt-rem</td><td>#F49800</td><td>造成<span class="ak-rt-rem">法术</span>伤害</td></tr>
<tr><td>&lt;@ba.kw&gt;</td><td>.ak-rt-kw</td><td>#00B0FF</td><td>普通攻击连续造成<span class="ak-rt-kw">两次</span>伤害</td></tr>
<tr><td>&lt;@ba.talpu&gt;</td><td>.ak-rt-talpu</td><td>#0098DC</td><td>攻击力+6%<span class="ak-rt-talpu">（+1%）</span></td></tr>
<tr><td>&lt;$ba.stun&gt;</td><td>.ak-rt-term</td><td>—</td><td>令目标<span class="ak-rt-term" data-ak-tip="晕眩：无法移动、攻击、使用技能">晕眩</span>1.5秒</td></tr>
<tr><td>&lt;@tu.imp&gt;</td><td>.ak-rt-imp</td><td>#FF0000</td><td><span class="ak-rt-imp">注意：</span>本关卡无法撤退</td></tr>
<tr><td>&lt;@ba.enemy&gt;</td><td>.ak-rt-enemy</td><td>#D83C3C</td><td><span class="ak-rt-enemy">敌方</span>单位</td></tr>
<tr><td>&lt;@ba.gild&gt;</td><td>.ak-rt-gild</td><td>#2FAC78</td><td><span class="ak-rt-gild">镀层</span></td></tr>
<tr><td>mission.levelname</td><td>.ak-rt-level</td><td>#FFDE00</td><td>通关<span class="ak-rt-level">1-7</span></td></tr>
</table>
```

### 稀有度文字色 [​](#稀有度文字色)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-rarity-1-text` | `#6f6f6f` | `var(--ak-rarity-1)` |  |
| `--ak-rarity-2-text` | `#7f8a00` | `var(--ak-rarity-2)` |  |
| `--ak-rarity-3-text` | `#1d78b8` | `var(--ak-rarity-3)` |  |
| `--ak-rarity-4-text` | `#7d4fc2` | `var(--ak-rarity-4)` |  |
| `--ak-rarity-5-text` | `#a87a00` | `var(--ak-rarity-5)` |  |
| `--ak-rarity-6-text` | `#d45f00` | `var(--ak-rarity-6)` |  |

### 装饰 [​](#装饰)

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-glyph-filter` | `invert(1)` | `none` | 游戏白色线稿图标在亮色下反相为黑 |
| `--ak-glyph-filter-inverse` | `none` | `invert(1)` | 同一套白线稿落在反色块（--ak-bg-inverse 底：.ak-phase-tabs 选中项那种）上时用——亮色下反色块是深底，白线稿不动；暗色下反色块是白底，要反相。组件走这个令牌，别按 :root[data-theme] 分支：跟随系统的暗色没有 data-theme，会漏 |
| `--ak-stripe-color` | `rgba(0,0,0,.08)` | `rgba(255,255,255,.07)` |  |
| `--ak-grid-color` | `rgba(0,0,0,.06)` | `rgba(255,255,255,.05)` |  |
| `--ak-halftone-color` | `rgba(0,0,0,.14)` | `rgba(255,255,255,.16)` |  |
| `--ak-scrollbar` | `#c4c6c8` | `#3b3d40` |  |

## 主题机制 [​](#主题机制)

两套主题等价，按 `<html>` 上的类切换——与 Vector 2022 / Minerva 的 `mw.user.clientPrefs` 同一机制（未登录也可用），核心与扩展对暗色的适配（Codex 令牌）因此可以复用：

```
html.skin-theme-clientpref-os      跟随系统（默认；@media prefers-color-scheme）
html.skin-theme-clientpref-day     档案模式（亮）
html.skin-theme-clientpref-night   终端模式（暗）
```

非 MW 环境（预览、Storybook、本站示例）用 `data-theme="light | dark"`。**局部主题**：亮 / 暗两块令牌同时挂在 `.ak-scope[data-theme="light | dark"]` 上（自定义属性在更近的祖先上声明就覆盖继承值），一个 widget 可以固定走终端 / 档案配色，与页面主题无关——Vue 里是 `<AkScope theme>`，见[作用域](/prts-design/components/scope)；主题无关块里引用了语义令牌的 `--ak-select-arrow` 在作用域上重新声明，高对比偏好也作用于作用域亮色。切换：`mw.user.clientPrefs.set('skin-theme', 'night')`；页眉里的外观开关就是做这件事（见[皮肤骨架 · 页眉](/prts-design/chrome/header#外观开关)）。

## 页眉 / 头图 / 画布的主题接口 [​](#页眉-头图-画布的主题接口)

皮肤的「框」在两套主题下都是黑的；活动主题（Gadget / Common.css）只覆盖这一组变量，不碰选择器。怎么用、示例主题见[皮肤骨架 · 头图与主题接口](/prts-design/chrome/theming)。

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-theme-accent` | `var(--ak-cyan-500)` | 活动主色：页眉标语 / 悬停、侧栏与目录分组条、页脚斜纹、窄屏卡片顶条 …（正文里的链接 / 选中仍是 --ak-accent；想一起换就连 --ak-accent 也覆盖） |
| `--ak-theme-accent-fg` | `#000` |  |
| `--ak-theme-accent-subtle` | `rgba(24, 209, 255, .14)` | 活动主色的淡底（页眉里的悬停 / 选中底、聚焦光圈）= --ak-theme-accent 的 14%；换主色时一起写——不用 color-mix() 推导，老浏览器也认 |
| `--ak-chrome-bg` | `rgba(8, 9, 10, .9)` | 页眉玻璃：半透明，头图 / 滚过来的正文在下面均匀透出。活动主题想让头图多透一点可调 alpha 到 .78–.85；别低于 .72（白色画面之下 #F2F2F2 仍 ≥7:1） |
| `--ak-chrome-backdrop` | `none` | 页眉玻璃底下的 backdrop-filter（毛玻璃），默认不开：玻璃 .9 不透明时模糊只透出一成，开不开截图逐像素平均只差 1.4/255，却是滚动时 GPU 上最重的一项——粘性页眉每帧都要把底下重糊一遍。玻璃调淡、确实要毛玻璃的活动主题再设 saturate(1.4) blur(10px) 之类 |
| `--ak-chrome-bg-solid` | `#0e0f10` | 不透明处：窄屏工具卡片、飞出菜单 |
| `--ak-chrome-fg` | `#f2f2f2` |  |
| `--ak-chrome-fg-2` | `rgba(255, 255, 255, .74)` |  |
| `--ak-chrome-fg-muted` | `rgba(255, 255, 255, .5)` |  |
| `--ak-chrome-line` | `rgba(255, 255, 255, .14)` | 页眉底线 / 分隔线 |
| `--ak-chrome-line-strong` | `rgba(255, 255, 255, .32)` |  |
| `--ak-chrome-hover` | `rgba(255, 255, 255, .08)` |  |
| `--ak-chrome-field` | `rgba(255, 255, 255, .16)` | 搜索触发器底（无 JS 时的真搜索框同）。比 -hover 亮一截、比 -line-strong 暗：触发器要在黑玻璃上一眼找得到（.07 时底 / 线 / 字都是「黑上一点白」，整条和页眉融成一块，用户反馈找不到搜索栏），又不能亮到像实底的卡片。只有这两处直接读它——页眉配色里的 --ak-bg-surface-2 另是白 .07，按钮 / 浮层里的控件不跟着变亮 |
| `--ak-chrome-image` | `none` | 顶栏角饰 / 底纹：url(left.png), url(right.png)（现网 PRTSheadleft 的活动徽章 / Garanheadright 的波纹那种）。画在玻璃之上、不会被压暗——只放深色低对比素材，照片请走 --ak-keyart-image |
| `--ak-chrome-image-position` | `right top` |  |
| `--ak-chrome-image-size` | `auto 100%` |  |
| `--ak-chrome-image-repeat` | `no-repeat` |  |
| `--ak-chrome-texture` | `.55` | 右侧半调网点强度 0–1（游戏顶栏 img\_back / 官网左缘网点），0 关闭 |
| `--ak-keyart-image` | `none` | 头图（Key Visual）：从页面顶端铺起、垫在页眉与版面背后的一幅通栏画；--ak-keyart-h 为 0 时只在页眉后面透 |
| `--ak-keyart-h` | `0px` | 页眉之下那段画的高度（画有多高，不是占多高——占位看 -reveal） |
| `--ak-keyart-reveal` | `72px` | 留在版面之上、不被压住的一段高度（不超过 --ak-keyart-h，没有头图时不占位）：默认留一小条，画先露一口气、版面不贴着页眉，各宽度相同（手机上也露）；0 = 完全垫在版面背后；= --ak-keyart-h 即一条横幅带。露出段会把侧栏导轨往下推这么多——还没吸顶时导轨限高跟着减掉这一截（sidebar-tree.js 写 --\_y），首屏底边也落在视口底边 |
| `--ak-keyart-veil` | `60%` | 垫在版面背后那一段蒙的画布色纱：顶端的浓度，往下渐浓到 100%。画面越花调得越高；别低于 50%（侧栏 / 标题压在画上要读得清） |
| `--ak-keyart-veil-filter` | `blur(8px)` | 纱底下先给画加的 filter（加在 ::before 里同一幅头图的副本上，不是 backdrop-filter）：细节先糊开、再被纱盖住，亮色主题下读起来是雾 / 景深而不是隔着磨砂膜；none 关闭。画面鲜艳的主题可以再加 saturate(.6) 之类 |
| `--ak-keyart-position` | `center 30%` | -position / -size 相对整块（页眉高 + -h）计算 |
| `--ak-keyart-size` | `cover` |  |
| `--ak-keyart-bg` | `transparent` | 画未铺满 / 未加载时的底色；默认透明，没有头图时页眉背后什么也不多画 |
| `--ak-keyart-fade` | `96px` | 底部渐隐进画布的高度，0 关闭（垫底段另有 -veil 的纱） |
| `--ak-canvas-image` | `none` | 页面画布底纹 / 底图（body 背景，叠在 --ak-bg-canvas 之上；现网 body 的 bkg 图位置） |
| `--ak-canvas-position` | `center top` |  |
| `--ak-canvas-size` | `auto` |  |
| `--ak-canvas-repeat` | `repeat` |  |
| `--ak-canvas-attachment` | `scroll` |  |

## Codex / MediaWiki 桥接 [​](#codex-mediawiki-桥接)

MediaWiki 核心与扩展（Codex 组件、mw-message-box、OOUI 的一部分）读这些变量；映射到 PRTS Design 语义令牌后，皮肤之外的 UI 自动换肤。它们单独生成在 `bridge-codex.css`（`skins.akds.base` 模块），只属于 Arknights 皮肤：加载到别的皮肤（Vector 2022 …）上会把宿主自己的 Codex 配色整体改掉，所以不在 `tokens.css`、也不进 npm 包的默认入口 `standalone.css`。

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--background-color-base` | `var(--ak-bg-surface)` |  |
| `--background-color-neutral` | `var(--ak-bg-surface-2)` |  |
| `--background-color-neutral-subtle` | `var(--ak-bg-canvas)` |  |
| `--background-color-interactive` | `var(--ak-bg-surface-2)` |  |
| `--background-color-interactive-subtle` | `var(--ak-bg-surface-2)` |  |
| `--background-color-interactive--hover` | `var(--ak-bg-hover)` |  |
| `--background-color-interactive--active` | `var(--ak-bg-active)` |  |
| `--background-color-disabled` | `var(--ak-bg-surface-3)` |  |
| `--background-color-disabled-subtle` | `var(--ak-bg-surface-2)` |  |
| `--background-color-inverted` | `var(--ak-bg-inverse)` |  |
| `--background-color-progressive` | `var(--ak-accent)` |  |
| `--background-color-progressive--hover` | `var(--ak-accent-hover)` |  |
| `--background-color-progressive--active` | `var(--ak-accent-hover)` |  |
| `--background-color-progressive-subtle` | `var(--ak-accent-subtle)` |  |
| `--background-color-destructive` | `var(--ak-danger)` |  |
| `--background-color-destructive-subtle` | `var(--ak-danger-bg)` |  |
| `--background-color-error-subtle` | `var(--ak-danger-bg)` |  |
| `--background-color-warning-subtle` | `var(--ak-warning-bg)` |  |
| `--background-color-success-subtle` | `var(--ak-success-bg)` |  |
| `--background-color-notice-subtle` | `var(--ak-info-bg)` |  |
| `--background-color-backdrop-light` | `var(--ak-bg-overlay)` |  |
| `--background-color-backdrop-dark` | `var(--ak-bg-overlay)` |  |
| `--color-base` | `var(--ak-fg)` |  |
| `--color-base--hover` | `var(--ak-fg)` |  |
| `--color-emphasized` | `var(--ak-fg)` |  |
| `--color-subtle` | `var(--ak-fg-muted)` |  |
| `--color-placeholder` | `var(--ak-fg-subtle)` |  |
| `--color-disabled` | `var(--ak-fg-disabled)` |  |
| `--color-inverted` | `var(--ak-fg-inverse)` |  |
| `--color-progressive` | `var(--ak-link)` |  |
| `--color-progressive--hover` | `var(--ak-link-hover)` |  |
| `--color-progressive--active` | `var(--ak-link-hover)` |  |
| `--color-visited` | `var(--ak-link-visited)` |  |
| `--color-destructive` | `var(--ak-danger)` |  |
| `--color-destructive--visited` | `var(--ak-link-new)` |  |
| `--color-error` | `var(--ak-danger)` |  |
| `--color-warning` | `var(--ak-warning)` |  |
| `--color-success` | `var(--ak-success)` |  |
| `--color-notice` | `var(--ak-fg-secondary)` |  |
| `--color-link-red` | `var(--ak-link-new)` |  |
| `--color-link-red--visited` | `var(--ak-link-new)` |  |
| `--color-icon-base` | `var(--ak-fg-secondary)` |  |
| `--border-color-base` | `var(--ak-border)` |  |
| `--border-color-subtle` | `var(--ak-border-subtle)` |  |
| `--border-color-muted` | `var(--ak-border-subtle)` |  |
| `--border-color-interactive` | `var(--ak-border-strong)` |  |
| `--border-color-disabled` | `var(--ak-border-subtle)` |  |
| `--border-color-progressive` | `var(--ak-accent)` |  |
| `--border-color-progressive--focus` | `var(--ak-focus)` |  |
| `--border-color-input-binary` | `var(--ak-border-strong)` | 复选框/单选框/开关：OOUI 用 6px 边框画选中态的圆点，不映射就是 Codex 默认的 #36c，和强调色对不上 |
| `--border-color-input-binary--checked` | `var(--ak-accent)` |  |
| `--border-color-input-binary--active` | `var(--ak-accent-hover)` |  |
| `--background-color-input-binary--checked` | `var(--ak-accent)` | Codex 复选框选中时的实底（CSS-only 的 .cdx-checkbox：ULS 语言设置、核心特殊页面…）；边框上面已映射，底色不映射就是 #36c，框青底蓝 |
| `--border-color-destructive` | `var(--ak-danger)` |  |
| `--border-color-error` | `var(--ak-danger)` |  |
| `--border-color-warning` | `var(--ak-warning)` |  |
| `--border-color-success` | `var(--ak-success)` |  |
| `--border-color-notice` | `var(--ak-border-strong)` |  |
| `--border-color-transparent` | `transparent` |  |
| `--border-radius-base` | `var(--ak-radius)` |  |
| `--border-radius-sharp` | `0` |  |
| `--border-radius-pill` | `var(--ak-radius-pill)` |  |
| `--box-shadow-drop-small` | `var(--ak-shadow-sm)` |  |
| `--box-shadow-drop-medium` | `var(--ak-shadow-md)` |  |
| `--box-shadow-drop-xx-large` | `var(--ak-shadow-lg)` |  |
| `--box-shadow-color-progressive--focus` | `var(--ak-focus)` |  |
| `--box-shadow-color-progressive--active` | `var(--ak-accent-hover)` |  |
| `--font-family-base` | `var(--ak-font-body)` |  |
| `--font-family-heading-main` | `var(--ak-font-body)` |  |
| `--font-family-monospace` | `var(--ak-font-mono)` |  |
| `--font-size-medium` | `var(--ak-fs-body)` |  |
| `--font-size-small` | `var(--ak-fs-sm)` |  |
| `--font-size-x-small` | `var(--ak-fs-xs)` |  |
| `--line-height-medium` | `var(--ak-lh-body)` |  |
| `--opacity-icon-base` | `0.87` |  |
| `--opacity-icon-subtle` | `0.67` |  |
| `--z-index-dropdown` | `var(--ak-z-dropdown)` |  |
| `--z-index-sticky` | `var(--ak-z-sticky)` |  |
| `--z-index-fixed` | `var(--ak-z-header)` |  |
| `--z-index-overlay` | `var(--ak-z-overlay)` |  |
| `--z-index-overlay-backdrop` | `var(--ak-z-overlay)` |  |
| `--z-index-toast-notification` | `var(--ak-z-toast)` |  |
| `--z-index-tooltip` | `var(--ak-z-tooltip)` |  |
