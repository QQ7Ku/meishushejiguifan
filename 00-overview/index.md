# 首页 · 设计规范总览

> 来源：https://icaruszezen.github.io/endfield-ui/design/
>
> 本文档由 endfield-ui 设计规范站点自动归档，供 AI 学习复刻 UI 与美术设计使用。

---

DESIGN

// ENDFIELD-UI　设计规范　非官方

# 《明日方舟：终末地》风格设计指导文档

**工业编辑风**：把界面当成一份印出来的技术文档——纸白或烟黑的底、墨黑的字、一处高饱和的信号色，再叠上一层克制的测绘与印刷痕迹。

它不是“深色赛博朋克”，也不等于“黑底黄字加切角”。这里把官网、游戏界面和宣传物料的视觉语言拆成能照着做的规范，每条都标了依据。

[从总纲读起](/endfield-ui/design/overview/)[组件预览](https://icaruszezen.github.io/endfield-ui/)

// DOCS

39篇文档

// FIGURES

35张原创图解

// EVIDENCE

4档来源等级

Surfaces

## 三个载体，三种气质

同一套语言在不同载体上的侧重不同。做设计前先确认自己在做哪一种。

- [官网实测纸白为主，几乎全是直角，只有一种信号黄；大图大字，信息密度低。](/endfield-ui/design/surfaces/website/)
- [游戏界面社区亮暗并存，黄色之外的角色色各司其职；切角、圆弧、胶囊按语义混用。](/endfield-ui/design/surfaces/game-ui/)
- [宣传物料观察社区版本、角色、地区的主题色可以整页接管；结构跟着体裁走。](/endfield-ui/design/surfaces/promotional/)

Principles

## 六条核心原则

这六条在三个载体上都成立，是判断“像不像”的依据。

1. 01

   ### [纸与墨是主体，颜色是信号](/endfield-ui/design/foundations/color/)

   大面积只用中性色，信号黄只出现在需要被看见的那一处。
2. 02

   ### [主角偏心，信息靠边](/endfield-ui/design/foundations/layout/)

   主角占据偏离中心的大块面积，导航、标签、说明停靠在边缘。
3. 03

   ### [形状有含义](/endfield-ui/design/foundations/shape/)

   直角与切角是机械与行动，圆与弧是人与仪表，胶囊是筛选与短状态。
4. 04

   ### [两种文字，两个层次](/endfield-ui/design/foundations/typography/)

   中文黑体承担阅读，英文承担结构：分节名、编号、微标签。
5. 05

   ### [纹理有预算](/endfield-ui/design/foundations/texture/)

   斜纹、网格、点阵都是可选的，一屏选一到两种。
6. 06

   ### [动得短，动得硬](/endfield-ui/design/foundations/motion/)

   过渡以 0.2 – 0.3 秒为主，用位移、擦入和明暗反转，不弹跳。

Index

## 全部文档

// OVERVIEW

### 总纲

- [设计总纲定位、三个载体、六条原则、常见误区](/endfield-ui/design/overview/)

// FOUNDATIONS

### 基础

单看一个维度。

- [色彩中性阶、信号黄、角色色、亮暗主题、对比度](/endfield-ui/design/foundations/color/)
- [字体四种字体角色、字阶、中英搭配](/endfield-ui/design/foundations/typography/)
- [形状四类形状的语义、圆角、线、阴影](/endfield-ui/design/foundations/shape/)
- [布局与空间偏心构图、页面骨架、尺寸体系、断点](/endfield-ui/design/foundations/layout/)
- [动效时长与缓动、交互反馈的套路、标志性动画](/endfield-ui/design/foundations/motion/)
- [纹理与装饰纹理清单、强度、预算](/endfield-ui/design/foundations/texture/)
- [图标网格与画法、状态](/endfield-ui/design/foundations/iconography/)
- [图像人物图、等距线稿、点云、主视觉接管](/endfield-ui/design/foundations/imagery/)

// ELEMENTS

### 母题

单个标志性元素怎么画、什么时候不用。

- [切角与斜楔](/endfield-ui/design/elements/corner-and-wedge/)
- [括号与标记](/endfield-ui/design/elements/brackets-and-markers/)
- [斜纹与色条](/endfield-ui/design/elements/stripes-and-strips/)
- [分节标题](/endfield-ui/design/elements/section-title/)
- [镂空字与微文字](/endfield-ui/design/elements/ghost-and-micro-text/)
- [网格、等高线、点阵](/endfield-ui/design/elements/grid-contour-dots/)
- [测绘叠层](/endfield-ui/design/elements/hud-overlays/)

// SURFACES

### 载体

三种载体各自的整体面貌；官网的云游戏页单独一篇。

- [官网证据等级：实测](/endfield-ui/design/surfaces/website/)
- [云游戏页证据等级：实测](/endfield-ui/design/surfaces/cloud/)
- [游戏界面证据等级：社区](/endfield-ui/design/surfaces/game-ui/)
- [宣传物料证据等级：观察 + 社区](/endfield-ui/design/surfaces/promotional/)

// COMPONENTS

### 组件

落到具体控件。

- [组件规范总览通用约定、清单与实现顺序](/endfield-ui/design/components/)
- [按钮](/endfield-ui/design/components/button/)
- [导航](/endfield-ui/design/components/navigation/)
- [卡片](/endfield-ui/design/components/card/)
- [表单](/endfield-ui/design/components/form/)
- [反馈](/endfield-ui/design/components/feedback/)
- [浮层](/endfield-ui/design/components/overlay/)
- [数据展示](/endfield-ui/design/components/data-display/)

// GAME

### Game 实机风格

照游戏内界面复刻的一组控件：独立入口，带官方图片，和上面的规范是两套东西。

- [总览、风险与处理和主库的区别、怎么引入、带了哪些官方图片、风险和我们的处理](/endfield-ui/design/game/)
- [动画弹窗与圆角下拉](/endfield-ui/design/game/dialog-and-select/)
- [圆角控件按钮、图标按钮、分段选择、文字开关](/endfield-ui/design/game/rounded-controls/)
- [确认提示紧凑卡片与通栏面板](/endfield-ui/design/game/confirmation-alerts/)
- [物品卡片稀有度、亮暗背景、库存和分层图标](/endfield-ui/design/game/item-cards/)
- [物品悬浮卡详情、获取途径与网格悬浮预览](/endfield-ui/design/game/item-hover-cards/)
- [官网开屏页主入口的开屏页填上官方的标志、底图和微标](/endfield-ui/design/game/splash-screen/)

// REFERENCES

### 依据

- [官网实测数据官网首页的全部原始数值](/endfield-ui/design/references/measurements/)
- [云游戏页实测数据云游戏页（/cloud）和页面里的鹰角账号弹窗的原始数值](/endfield-ui/design/references/measurements-cloud/)
- [官方来源索引来源等级的定义、官方渠道入口](/endfield-ui/design/references/official-sources/)
- [开源项目调研GitHub 上的相关项目与可借鉴之处](/endfield-ui/design/references/open-source-projects/)

Evidence

## 怎么读这些文档

每条规范都标了依据。“实测”和“观察”是事实，“社区”和“推断”是可以被推翻的判断；游戏界面的部分以“社区”为主。

实测
:   从官网样式中直接读到的数值

观察
:   在官方公开页面上看到的现象

社区
:   开源项目作者的取值或观察，这里没有独立验证

推断
:   这套文档自己的设计建议，可以被推翻

非官方项目

这是爱好者做的设计规范，与鹰角网络（Hypergryph）没有隶属或授权关系，也不代表官方立场。站内的图解都是原创示意，不包含官方截图、立绘、标志与字体。[版权与商标声明](https://github.com/icaruszezen/endfield-ui/blob/main/NOTICE.md)
