# 模组 Module

> 来源：https://mooncellwiki.github.io/prts-design/arknights/module
>
> PRTS Design 文档自动归档，供 AI 学习复刻 UI 与美术设计。

---

同现网 `{{模组}}`（`.equiptemplate`），一张卡装完：型号（类型图标 + SWO-X）+ 名称 + 说明 tooltip + 基础信息（故事）+ 三阶段属性 / 特性追加 · 天赋更新 + 解锁任务 + 解锁需求与材料。`color`（= 模板参数「类型颜色」，游戏模组类型底色）决定型号块与阶段条的颜色：`red` / `blue` / `green` / `yellow` / `purple`，原型证章不写 = 灰。

写法参考 Naive UI 的 `n-card`（`#header-extra`）：头部走 props，故事是默认插槽；三张表的行放在具名插槽里——`#stages` 放 `AkModuleStage`、`#unlock` 放 `AkModuleUnlock`，表头由卡片画。解锁任务是纯文本时用 `tasks`，要链接关卡号时用 `#tasks` 插槽写 `<li>`。

## 完整卡片 

`AkModuleStage` 的 `stats` 按写的顺序排、数值绿字，`kicker` 是反色小标（特性追加 / 天赋更新），描述写在默认插槽里；`AkModuleUnlock` 的 `#requirement` 放信赖 / 精英阶段 / 任务标签，默认插槽放材料（[道具](/prts-design/arknights/item)）。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-module--full)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkItem from "../../Item/AkItem.vue";
import AkItemList from "../../Item/AkItemList.vue";
import AkTag from "../../Tag/AkTag.vue";
import AkModule from "../AkModule.vue";
import AkModuleStage from "../AkModuleStage.vue";
import AkModuleUnlock from "../AkModuleUnlock.vue";

const f = (id: string) => asset(`item/framed/${id}.png`);

const story = [
  "七月七，晴。生辰，她激动地想要为魏彦吾展示拔刀，却找不到魏彦吾的踪影。等了三天，没有等到。",
  "五月十三，多云。以泪锋斩断了庭中的三十年老树，砸坏了屋顶。魏彦吾没有出现，只是差人植了一棵新的。她不再期待魏彦吾的认可。",
  "一月一，大雨。贺年，倾盆大雨，去贫民区的途中路见不平，愤而出手，以一敌十五。生死攸关之际，以随身佩剑递出奔夜，伤七人，退八人。闹到了上面，魏彦吾难得出现，看了剑痕后，只看她一眼就走了。她忘了自己当时为何出手，却永远不会忘记那一眼中的失望。",
  "十二月二十五，晴。从近卫学院返乡路上见义勇为，浑然无惧，扬眉之剑，当放则放。她意气风发，以为自己可以荡平天下不平事。",
  "七月三十，多云。以昏迷五天为代价，递出绝影，最终斩了重大通缉犯，龙门陈晖洁，崭露头角。醒来之后，赤霄放在她的床头，魏彦吾没有来看过她。",
  "八月十三，阴。追捕感染者罪犯途中，欲以赤霄出手，赤霄纹丝未动。而后，生死攸关之际，福至心灵，赤霄拔刀，斩塌了半个地下车库。她去找魏彦吾想知道赤霄究竟为何，魏彦吾没有见她。",
  "九月三十，晴。赤霄二度出鞘，斩开一扇金库大门，救出其中人质。她开始理解，赤霄乃意志之剑。她以为她一直以来做得够多，其实是她做得太少。她必须更快，她必须更有力。",
  "七月七，多云。生辰，十几年来，魏彦吾第一次提出要看一看她的剑，她凝聚一路所学，以未完成的云裂作为答卷，魏彦吾不置可否。",
  "一月十一，晴。与罗德岛一同离开龙门。之后一路，赤霄鲜少出鞘。",
];

const unlock = [
  { stage: 1, trust: "0%", items: [["mod_unlock_token", "模组数据块", 4], ["30135", "D32钢", 2], ["4001", "龙门币", "8万"]] },
  { stage: 2, trust: "50%", items: [["mod_unlock_token", "模组数据块", 4], ["mod_update_token_1", "数据增补条", 60], ["30145", "晶体电子单元", 3], ["4001", "龙门币", "10万"]] },
  { stage: 3, trust: "100%", items: [["mod_unlock_token", "模组数据块", 4], ["mod_update_token_2", "数据增补仪", 20], ["30115", "聚合剂", 4], ["4001", "龙门币", "12万"]] },
] as const;
</script>

<template>
  <AkModule
    name="罗德岛制式剑"
    :src="asset('module/uniequip_002_chen.png')"
    type-name="SWO-X"
    :type-icon="asset('module/type_swo-x.png')"
    color="red"
    hint="调整效果含特性调整与天赋调整。所有效果调整先于潜能提升生效，此处只显示潜能提升前的调整效果，实际变化请参照游戏内表述。"
  >
    <p>
      <template v-for="(line, i) in story" :key="i"><br v-if="i" />{{ line }}</template>
    </p>
    <template #stages>
      <AkModuleStage :stage="1" :stats="{ 攻击: '+50', 攻击速度: '+5' }" kicker="特性追加">技能期间造成的伤害提升<span class="ak-rt-vup">10%</span></AkModuleStage>
      <AkModuleStage :stage="2" :stats="{ 攻击: '+65', 攻击速度: '+6' }" kicker="天赋更新">天赋【呵斥】更新：在场时每<span class="ak-rt-vup">3</span>秒回复全场友方角色1点攻击/受击技力</AkModuleStage>
      <AkModuleStage :stage="3" :stats="{ 攻击: '+80', 攻击速度: '+7' }" kicker="天赋更新">天赋【呵斥】更新：在场时每3秒回复全场友方角色1点攻击/受击技力，<span class="ak-rt-rem">自身额外回复1点技力</span></AkModuleStage>
    </template>
    <template #tasks>
      <li>完成5次战斗；必须编入非助战陈并上场，且每次战斗至少释放一次赤霄·拔刀或赤霄·绝影</li>
      <li>3星通关主题曲 <a class="ak-stage-code" href="#">5-8</a>；必须编入非助战陈并上场，且使用陈至少歼灭16名敌人</li>
    </template>
    <template #unlock>
      <AkModuleUnlock v-for="u in unlock" :key="u.stage" :stage="u.stage">
        <template #requirement>
          <span class="ak-trust">{{ u.trust }}</span>
          <template v-if="u.stage === 1">
            <span class="ak-elite"><img :src="asset('elite/elite_2.png')" alt="" />精英二 Lv60</span>
            <AkTag size="sm" variant="outline" title="完成本模组所有模组解锁任务">✓ 全部解锁任务</AkTag>
          </template>
        </template>
        <AkItemList>
          <AkItem v-for="[id, name, count] in u.items" :key="id" :src="f(id)" :name="name" :count="count" size="sm" />
        </AkItemList>
      </AkModuleUnlock>
    </template>
  </AkModule>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 故事折叠 

故事默认定高 5 行、框内可滚动（同现网 ：不展开也读得到全文），「全文阅读」去掉定高撑开、「收起全文」收回。展开态是组件自己的状态，也可以用 `v-model` 接出来（外面放「全部展开」之类的开关）。无论展开与否，全文都在 DOM 里，可被搜索。

CSS 版的开关靠 `data-toggle-class` 约定：勾选「全文阅读」时皮肤脚本（`skin.js`，预览里是 `preview.js`）给 `data-toggle-target=".ak-module__main"` 切 `.is-open`；无 JS 时复选框照样在，只是故事停在定高框里（仍可滚动）。Vue 版自带状态。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-module--collapse)

vue

```
<script setup lang="ts">
import { ref } from "vue";

import { asset } from "../../../demo/asset";
import AkButton from "../../Button/AkButton.vue";
import AkModule from "../AkModule.vue";
import AkModuleStage from "../AkModuleStage.vue";

/** 展开态用 v-model 接出来：外面的按钮也能展开 / 收起 */
const open = ref(false);

const story = [
  "大地的伤痕自天际线奔袭而来，精准地击中一片维多利亚式建筑群，楼宇如同遭遇旋风袭击的树林，围绕着同一个点以放射状倒塌。在这片帝国的废墟之中，两个身影正翻过一座倒下的石碑，试图寻找高点。",
  "“这里荒废多久了？”陈站在废墟上远眺，身后的石碑上那些曾经象征着荣誉的名字都被涂抹殆尽。",
  "“这片校舍自从战争爆发那会儿就弃用了，还没完成学业的学弟学妹们被分流到了新的校区，完成了剩下的课程......陈陈，前面是我们以前的老训练场，你还记得吗？”风笛的声音从低处传来。",
  "“我记得特别清楚，感觉自己当学生的时候总是错过一些好事。一到毕业路也修好了，训练场也修好了。”训练场被冲击波挤成了一片片翘起的地块，陈捡起了一块石头，丢进地块夹缝中冒出的灌木丛，躲在其中的羽兽尖叫着扑腾向远处。",
  "“那时候已经足够好啦。”风笛的声音自远而近，停留在了陈的耳畔。",
  "“也是。那可能是我最单纯的一段时间了，不用去思考一些麻烦的问题，只需要专心训练和学习。”",
  "“这就是当时的你每天疯狂训练，全常规科目拿A的动力？”风笛将双手搭在陈的肩上，轻轻地摇晃着。",
  "“回去以后，我发现有太多的事无法用刀剑解决，有些时候会觉得自己过于渺小，即便努力工作，也无法改变很多事——消灭敌人有的时候并不能解决问题。”陈摇了摇头，好像要把过去的庞杂思绪都从脑海中丢出去。",
  "两人走下高坡，记忆中的花圃还在原地。曾经高悬的近卫学校标志一半深深插入泥土，一半斜靠在断裂的石柱上。夕阳西下，阳光倾泻在已有斑驳锈迹的标志上，仅剩的金属光泽诉说着这里曾经的荣光。",
  "“一切都变了，维多利亚不也一样吗？”陈盯着校徽，喃喃自语道。",
  "“还是有人没有变的。知道你要来，我前几天特意去拜访了泰勒教官。当时你走得太匆忙，留在宿舍里的文件啥的都没带走——她这些年一直保管着你这些东西。这是你的学业通过证明。哦对了，还有这个！”风笛从随身的包里掏出了一叠证件，还有一把木剑。",
  "“这是你当年的训练用剑，按道理是要收回的，但是你的成绩太好了，教官本来打算给你个惊喜，想把这把剑送给你......”",
  "“代我向她表示感谢。那会儿我走得太快了......”",
  "“至少你现在回来了。这些年，我们很多同学再也没有回来。”",
  "两人沉默了一会儿。",
  "“十年后，你觉得你会在哪？”",
  "“谁知道呢，应该要回去吧，还有很多事要做。”陈向远方看去，那正是龙门的方向，“那时无论来这上学还是离开，都是他们安排好的。现在，我要自己走回去了。”",
];
</script>

<template>
  <div class="ak-flex-col ak-gap-3">
    <div class="ak-flex ak-gap-3 ak-items-center">
      <AkButton size="sm" @click="open = !open">{{ open ? "收起故事" : "展开故事" }}</AkButton>
      <span class="ak-fs-xs ak-fg-muted">v-model = {{ open }}</span>
    </div>
    <AkModule
      v-model="open"
      name="往昔时光"
      :src="asset('module/uniequip_003_chen.png')"
      type-name="SWO-Y"
      :type-icon="asset('module/type_swo-y.png')"
      color="red"
    >
      <p>
        <template v-for="(line, i) in story" :key="i"><br v-if="i" />{{ line }}</template>
      </p>
      <template #stages>
        <AkModuleStage :stage="1" :stats="{ 攻击: '+52', 防御: '+28' }" kicker="特性追加">攻击时无视敌人<span class="ak-rt-vup">70</span>点的防御力</AkModuleStage>
        <AkModuleStage :stage="3" :stats="{ 攻击: '+85', 防御: '+42' }" kicker="天赋更新">天赋【持刀格斗术】更新：攻击力<span class="ak-rt-vup">+15%</span>，防御力<span class="ak-rt-vup">+15%</span>，物理闪避<span class="ak-rt-vup">+18%</span></AkModuleStage>
      </template>
    </AkModule>
  </div>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 原型证章 

`:collapsible="false"`：短文全文展开、不出开关；不写 `color`（灰条）；`#header-extra` 放说明标签。

[Storybook ↗](/prts-design/storybook/?path=/story/arknights-module--original)

vue

```
<script setup lang="ts">
import { asset } from "../../../demo/asset";
import AkTag from "../../Tag/AkTag.vue";
import AkModule from "../AkModule.vue";
</script>

<template>
  <!-- 原型证章：不写 color（灰条）、短文全文展开不出开关、没有阶段表 -->
  <AkModule name="陈证章" :src="asset('subprofession/sword.png')" type-name="Original" :collapsible="false">
    <template #header-extra>
      <AkTag size="sm" variant="outline">基础证章 · 无特殊效果 · STAGE MAX</AkTag>
    </template>
    <p>干员陈擅长于近身搏斗中对敌人造成多段杀伤<br />根据外勤部门决议<br />在外勤任务中划分为近卫干员，行使剑豪职责<br />特别颁发此证章<br />以兹证明</p>
  </AkModule>
</template>
```

html

上面是组件实际渲染出的结构——MW 模板 / Lua 照这个输出即可得到同样的外观。

## 键盘与可访问性 

- 「全文阅读」是 `<label>` 包着的原生复选框（`aria-controls` 指向故事），Tab 聚焦、Space 切换。复选框本身是 0×0 的隐形控件，焦点环画在整个开关上。
- 「说明 ⓘ」可聚焦，聚焦时显示解释（同悬停）。
- 阶段条是装饰（`aria-hidden`），读屏只读「STAGE n」。

## Vue API 

### AkModule 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `name`必填 | `string` | — | 模组名（h4） |
| `src` | `string` | — | 模组图地址（游戏 uniequip 图；原型证章放分支图标） |
| `typeName` | `string` | — | 型号文字：SWO-X / Original … |
| `typeIcon` | `string` | — | 型号图标地址（游戏模组类型小图标，放在型号文字前） |
| `color` | `"yellow"``"red"``"blue"``"green"``"purple"` | — | 型号色（= 模板参数「类型颜色」，游戏模组类型底色）：左侧粗色条 + 型号块 + 阶段条；不写 = 原型证章的灰 |
| `hint` | `string` | — | 「说明 ⓘ」的悬停解释（调整效果先于潜能生效 …）；不写不显示 |
| `collapsible` | `boolean` | `true` | 故事可折叠：默认定高 5 行可滚动 +「全文阅读」开关；false 时全文展开、不出开关（原型证章那种短文） |
| `tasks` | `string[]` | — | 模组解锁任务（纯文本，一条一行）；要链接关卡时改用 #tasks 插槽 |
| `modelValue` | `boolean` | `false` | 故事是否展开（「全文阅读」/「收起全文」） |

| 事件 | 参数 | 说明 |
| --- | --- | --- |
| `update:modelValue` | `[value: boolean]` |  |

| 插槽 | 说明 |
| --- | --- |
| `default` | 故事（基础信息）：若干 <p>，行内换行用 <br> |
| `header-extra` | 名称之后的附加内容（原型证章的说明标签等） |
| `stages` | 三阶段表的行：若干 AkModuleStage |
| `tasks` | 解锁任务：若干 <li>（排在 tasks 之后） |
| `unlock` | 解锁需求与材料表的行：若干 AkModuleUnlock |

### AkModuleStage 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `stage`必填 | `1``2``3` | — | 阶段 1–3：「STAGE n」+ 三段条点亮 n 段 |
| `stats` | `Record<string, string | number>` | — | 属性加成：{ 攻击: "+50", 攻击速度: "+5" }（按写的顺序排，数值绿字） |
| `kicker` | `string` | — | 变化类别的反色小标：特性追加 / 天赋更新 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 特性 / 天赋变化的描述（可带富文本） |

### AkModuleUnlock 

| 属性 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `stage`必填 | `1``2``3` | — | 阶段 1–3：「STAGE n」+ 三段条点亮 n 段 |

| 插槽 | 说明 |
| --- | --- |
| `default` | 材料消耗：一排 AkItem（AkItemList） |
| `requirement` | 解锁需求：信赖 / 精英阶段与等级 / 任务标签，横排换行 |

## CSS 实现 

| 块 | 元素 \_\_ | 修饰 -- |
| --- | --- | --- |
