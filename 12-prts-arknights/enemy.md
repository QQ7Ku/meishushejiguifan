# 敌人 Enemy

> 来源：https://mooncellwiki.github.io/prts-design/arknights/enemy
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

敌人卡：左列是 64px 圆形头像（深底），右列从上到下是级别 · 编号、名字、威胁度菱形。精英加橙色左条，领袖（BOSS）加红色左条。用在关卡页的敌人列表里。

## 级别 

`variant` 对应游戏数据里的 `enemyLevel`：`normal` / `elite` / `boss`。级别字（NORMAL / ELITE / BOSS）会写在编号前面，不只靠色条区分，色弱读者和读屏器也能拿到这个信息。`level` 是点亮几枚菱形，共 `levelMax` 枚（默认 4）。

头像由调用方用 `src` 传入（游戏里是方图，这里裁成圆形）；不传时显示深色空圆。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-enemy--ranks)

vue

```
<script setup lang="ts">
import AkEnemy from "../AkEnemy.vue";
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <AkEnemy variant="boss" code="B003" name="碎骨" :level="4" />
    <AkEnemy variant="elite" code="E020" name="萨卡兹大剑手" :level="2" />
    <AkEnemy code="N001" name="源石虫" :level="1" />
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 可访问性 

- 头像的 `alt` 为空：名字就写在旁边，读屏器不需要再读一遍。
- 威胁度这排菱形是 `role="img"`，读屏名为「威胁度 2 / 4」，不会被当成四个空元素跳过。

## Vue API 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 敌人名 |
| `code` | `string` | — | 编号（B003 / E020 / N001），显示在级别后面：「BOSS · B003」 |
| `variant` | `"elite"``"normal"``"boss"` | `"normal"` | 级别（游戏 enemyLevel）：normal 普通 · elite 精英（橙色条）· boss 领袖（红色条）；级别字总会写出来，不只靠色条 |
| `src` | `string` | — | 头像地址（方图，裁成 64px 圆）；不写时是深色空圆 |
| `level` | `number` | — | 威胁度：点亮几枚菱形 |
| `levelMax` | `number` | `4` | 菱形总数 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 名字 / 威胁度下面追加的内容（属性、能力摘要） |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
