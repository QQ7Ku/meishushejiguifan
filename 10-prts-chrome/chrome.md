# 皮肤骨架

> 来源：https://mooncellwiki.github.io/prts-design/chrome/
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

# 皮肤骨架 

L2 是正文之外的一切：页眉、头图、侧栏、页面头（标题与动作簇）、目录、正文白纸、分类栏的位置、页脚、搜索面板。它由皮肤的 `skin.mustache` 输出，编辑和模板都碰不到；样式在 `packages/css/src/chrome/`，排在所有组件之后加载——页眉里的 `.ak-btn` / `.ak-menu` / `.ak-fab` 靠同特指度后到覆盖。

骨架的形态由**视口宽度**决定（≥1400 目录在右侧导轨，<1400 目录收成正文右上角的一枚按钮 + 浮层（手机上入口在页眉里），<1120 侧栏变抽屉，≤639 手机、页眉全是图标；页眉在各宽度都只有一行），所以这一区的示例不是普通的示例块，而是一整页：按上方选定的视口宽度排版、再缩放进正文列。里面照常可以滚动、点击、切换，和真皮肤一致（交互脚本同预览页）；「示例活动主题」开关见[头图与主题接口](/prts-design/chrome/theming)。

视口示例活动主题

## DOM 约定 

与 `skin.mustache` 一一对应（完整的 mustache 数据映射见[入门 · skin.mustache 结构](/prts-design/guide/skin-template)）：

```
body.skin-arknights
  a.ak-skip                                  跳到内容
  header.ak-header                           页眉：黑色「终端」顶栏
    .ak-header__inner                        品牌 · 搜索 · 工具（外观 / 通知 / 用户菜单）· ⋮
    .ak-local-nav                            菜单（<1120）· 本页目录（仅 ≤639）：不另起一行，与 __inner 都 display:contents，并进 .ak-header 那一行
  div.ak-keyart > .ak-keyart__inner          头图（活动主题设了才出现；垫在版面背后，只露一小条）
  .ak-layout                                 两列：侧栏 + 主列
    aside.ak-sidebar#ak-sidebar              侧栏（门户 / #MenuSidebar 多层树）
    main.ak-main#content
      .ak-main__inner
        header.ak-page-header                页面头：面包屑 · 指示器 · h1 + 动作簇
        .ak-toc-dock > aside.ak-toc#ak-toc   目录（DOM 上属于页面，≥1400 抬进右侧导轨；<1400 dock 里多出目录按钮，浮层从它下面拉出）
        .ak-body#bodyContent                 正文白纸
          .mw-body-content                   ← L1 MediaWiki 内容样式从这里开始
          .ak-body-foot                      最后编辑 · 版权
        #catlinks                            分类栏
  footer.ak-footer                           页脚
  button.ak-fab                              回到顶部（≥1120）
```

| 部分 | 文件 | 页面 |
| --- | --- | --- |
| 外壳：`body.skin-arknights`、跳转链接、`.ak-sr-only` | `shell.css` | 本页 |
| 两列布局、目录导轨的让位 | `layout.css` | 本页 |
| 正文白纸、页脚信息行 | `body.css` | 本页 |
| 页眉（含「菜单」、手机上的「本页目录」）、外观开关 | `header.css` · `local-nav.css` · `theme-toggle.css` | [页眉](/prts-design/chrome/header) |
| 头图、画布底纹、活动主题示例 | `keyart.css` · `demo-theme.css` | [头图与主题接口](/prts-design/chrome/theming) |
| 侧栏、多层树、悬停飞出 | `sidebar.css` · `sidebar-tree.css` | [侧栏](/prts-design/chrome/sidebar) |
| 面包屑、标题、动作簇 | `page-header.css` | [页面头](/prts-design/chrome/page-header) |
| 目录导轨 / 按钮 + 浮层 | `toc.css` | [目录](/prts-design/chrome/toc) |
| 页脚、徽章 | `footer.css` | [页脚](/prts-design/chrome/footer) |
| 悬浮搜索面板 | `search-palette.css` | [搜索面板](/prts-design/chrome/search) |
| 特殊页面的正文内边距 | `special-pages.css` | 本页 |
| 全部断点 | `responsive.css`（最后加载，顺序敏感） | [响应式](/prts-design/chrome/responsive) |

`demo-theme.css` 不在 `chrome/index.css` 里、也不进 `skin.json`：它是一份示例 Gadget，只在预览 / 文档站里用。

## 布局 

`.ak-layout` 只有两列：侧栏 `--ak-sidebar-w` + 主列。目录导轨**不是第三列**，而是主列 `.ak-main` 的右内边距让出来的（`--ak-toc-w + --ak-gutter`）——这样 `aside.ak-toc` 在 DOM 上可以待在页面标题下面（属于页面而不是布局），窄屏收起时就地变成按钮下面的浮层，不需要第二份 DOM。整体最宽 1680，超宽屏两边留白；页眉、页脚用同一个 1680 容器，左右缘对齐。阅读列的宽度 `--ak-content-max` 只由「主列封顶 − 导轨内边距」得出，`.ak-main__inner` 上不另锁一道。

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

| 修饰 | 作用 |
| --- | --- |
| `.ak-layout--no-toc` | 页面没有目录：不让导轨，正文铺到阅读列 + 导轨之和（右缘与有目录的页面、页眉工具列对齐）。一览 / 工具页都是无标题的，要的就是这份宽度；不另收窄到阅读列——那样 >1512 的屏上右边会剩一条与导轨等宽的空白，左边又与侧栏贴齐，读起来像少了一栏 |
| `.ak-layout--wide` | 整体不限 1680、正文不限阅读列（大表、整页工具） |
| `.ak-layout--reading` | 居中阅读版式（目前没有页面用；启用时页眉要另给一套列宽） |

## 正文白纸 

`.ak-body` 是一张浅色「纸」（`--ak-bg-surface` + 1px 细框 + 24 / 32px 内边距），正文 `.mw-body-content` 放在里面；`.ak-body--flat` 去掉纸（首页这类自己排版的页面），各宽度都不垫边距——窄屏白纸的内边距与左右贴边的负外边距不落到它上面，正文和页眉一样只隔一个 gutter。纸底 `.ak-body-foot` 是 MW 的 `#footer-info`：最后编辑时间（等宽数字）· 版权声明。分类栏 `#catlinks` 在纸外、紧贴其下。特殊页面（`Special:` 命名空间、搜索页）内边距收到 20px。

## 外壳 

- `body.skin-arknights` 是纵向 flex，页脚 `margin-top: auto` 贴底——内容短的页面页脚也在视口底部。
- `.ak-skip`「跳到内容」：平时完全移出视口，键盘 Tab 到它才出现在左上角（主色实底）。
- `.ak-sr-only`：只给读屏器的文字（动作簇的当前项、只有图标的按钮名）。

## CSS 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
