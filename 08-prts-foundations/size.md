# 尺寸 / 动效 / 层级

> 来源：https://mooncellwiki.github.io/prts-design/foundations/size
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

# 尺寸 / 动效 / 层级 

## 间距 

4px 栅格。

| 令牌 | 值 | 样张 | 说明 |
| --- | --- | --- | --- |
| `--ak-space-1` | `4px` |  |  |
| `--ak-space-2` | `8px` |  |  |
| `--ak-space-3` | `12px` |  |  |
| `--ak-space-4` | `16px` |  |  |
| `--ak-space-5` | `20px` |  |  |
| `--ak-space-6` | `24px` |  |  |
| `--ak-space-8` | `32px` |  |  |
| `--ak-space-10` | `40px` |  |  |
| `--ak-space-12` | `48px` |  |  |
| `--ak-space-16` | `64px` |  |  |
| `--ak-space-20` | `80px` |  |  |
| `--ak-space-24` | `96px` |  |  |

这套阶梯在所有视口下都不变：它同时管按钮内边距、列表缩进和点按面积，手机上整档缩小会连触控区一起缩。手机上要收紧的只是版面这一层，交给语义令牌：`--ak-gutter`（两侧留白 / 栏间距，手机 16）、`--ak-header-h`（页眉高，手机 52）、`--ak-space-section`（大区块之间的竖向间隔，用于带色条的章节标题和正文 `hr`；宽屏 32，手机 24）。窄屏值见下面[布局](#布局)表的「手机」一列。

## 形状 

方舟 = 直角；输入框允许 2px，是唯一的例外。色条 + 细框的盒子用 `border-image` 把色条和 1px 框直角拼接——不同宽度的 border 会被浏览器在角上斜接出一道小斜边，系统里不出现 45° 斜边。

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-radius` | `0px` |  |
| `--ak-radius-sm` | `2px` |  |
| `--ak-radius-pill` | `999px` |  |
| `--ak-bar-w` | `4px` | 标题左侧色条宽度 |
| `--ak-bar-w-lg` | `8px` |  |

## 布局 

断点与 Codex 一致：320 / 640 / 1120 / 1680；另有 1400 作为目录导轨的断点。侧栏与目录导轨等宽（200px），正文夹在正中；≤1400 目录导轨收起后侧栏收到 176px（覆盖写在 `:root` 上，页眉网格与布局共用）。各断点下皮肤骨架的形态见[皮肤骨架 · 响应式](/prts-design/chrome/responsive)。

| 令牌 | 值 | 手机 | 说明 |
| --- | --- | --- | --- |
| `--ak-header-h` | `56px` | `52px` | ≤639 为 52（functional/compact） |
| `--ak-sidebar-w` | `200px` | 同左 | 与 toc-w 等宽：正文夹在两条导轨正中；≤1400 目录导轨收起后收到 176 |
| `--ak-toc-w` | `200px` | 同左 |  |
| `--ak-content-max` | `1240px` | 同左 | 阅读区最大宽度；表格密集页可加 .ak-wide |
| `--ak-content-narrow` | `760px` | 同左 |  |
| `--ak-gutter` | `24px` | `16px` | 版面两侧留白 / 栏间距；≤639 换成 gutter-sm |
| `--ak-gutter-sm` | `16px` | 同左 |  |
| `--ak-space-section` | `var(--ak-space-8)` | `24px` | 大区块之间的竖向间隔：带色条的章节标题 .ak-heading 的上边距、正文 hr 的上下；≤639 收到 space-6（24） |

## 图标尺寸 · 装饰尺寸 

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-icon-xs` | `14px` |  |
| `--ak-icon-sm` | `18px` |  |
| `--ak-icon-md` | `24px` |  |
| `--ak-icon-lg` | `32px` |  |
| `--ak-icon-xl` | `48px` |  |

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-stripe-size` | `8px` |  |
| `--ak-halftone-size` | `6px` |  |
| `--ak-grid-size` | `32px` |  |

## 动效 

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-dur-instant` | `80ms` |  |
| `--ak-dur-fast` | `150ms` |  |
| `--ak-dur-normal` | `250ms` |  |
| `--ak-dur-slow` | `400ms` |  |

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-ease` | `cubic-bezier(0.2, 0.8, 0.2, 1)` |  |
| `--ak-ease-in` | `cubic-bezier(0.4, 0, 1, 1)` |  |
| `--ak-ease-out` | `cubic-bezier(0, 0, 0.2, 1)` |  |
| `--ak-ease-sharp` | `cubic-bezier(0.4, 0, 0.2, 1)` |  |

`prefers-reduced-motion: reduce` 下所有动画与过渡缩到 0.01ms（`base/root.css`，见[可访问性 · 用户偏好](/prts-design/foundations/accessibility#用户偏好)）。

加载指示是游戏内 loading 的菱形涟漪（[加载 Spinner](/prts-design/components/spinner)，菱形母题见[设计理念](/prts-design/foundations/principles#菱形-源石)）；按钮的加载态只有 36px 高、涟漪放不开，退回 14px 圆弧转圈。

## 阴影 

| 令牌 | 档案（亮） | 终端（暗） | 说明 |
| --- | --- | --- | --- |
| `--ak-shadow-sm` | `0 1px 2px rgba(0,0,0,.08)` | `0 1px 2px rgba(0,0,0,.4)` |  |
| `--ak-shadow-md` | `0 4px 12px rgba(0,0,0,.10), 0 1px 3px rgba(0,0,0,.06)` | `0 4px 14px rgba(0,0,0,.5), 0 1px 3px rgba(0,0,0,.4)` |  |
| `--ak-shadow-lg` | `0 12px 32px rgba(0,0,0,.14), 0 2px 6px rgba(0,0,0,.08)` | `0 16px 40px rgba(0,0,0,.6), 0 2px 6px rgba(0,0,0,.4)` |  |
| `--ak-shadow-accent` | `0 0 0 3px var(--ak-accent-subtle)` | `0 0 0 3px var(--ak-accent-subtle)` |  |

## 层级 

| 令牌 | 值 | 说明 |
| --- | --- | --- |
| `--ak-z-dropdown` | `100` |  |
| `--ak-z-sticky` | `200` |  |
| `--ak-z-header` | `1000` |  |
| `--ak-z-overlay` | `1500` |  |
| `--ak-z-modal` | `2000` |  |
| `--ak-z-toast` | `3000` |  |
| `--ak-z-tooltip` | `4000` |  |
