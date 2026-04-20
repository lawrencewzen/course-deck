# Slide 模板 · 6 类骨架

每种 slide 一个模板。装配时按讲义内容匹配模板，照抄 HTML 骨架，只换文本 / data 属性。CSS 都在 `assets/final-deck-example.html` 里已经写好——不要在装配时重写样式，直接继承。

| 模板 | HTML 类 | 什么时候用 |
|---|---|---|
| 1. Cover | `.slide.cover` | 全 deck 只有 1 张（开头）+ 1 张 End |
| 2. Divider | `.slide.divider` | 每节开头，数量 = 节数 |
| 3. Break | `.slide.break` | 中场 / 小休息，用于 2.5h 及以上课 |
| 4. Concept | `.slide.content` | 单个概念解释：eyebrow + h1 + 2-3 段 body |
| 5. Steps | `.slide.content.steps` | 多条要点并列：3 条规则、6 步流程、5 个坑 |
| 6. Table | `.slide.content.table-page` | 对照表：命令表、对比表、扩展表 |

---

## 1. Cover

**约定**：开头 1 张 + 结束 1 张（`.slide.cover`，data-no-chrome="true" 隐藏面包屑）。

```html
<div class="slide cover" data-section="0" data-section-name="" data-time="0"
     data-notes-strategy="开场前 30 秒：给学员时间坐定，环视一圈。"
     data-notes-speak="今天这节课结束后...">
  <div class="top fade-up d1">
    <svg class="logo-mini"><use href="#logo-flame"/></svg>
    <span class="brand">AI Spark</span>
    <span class="sep">·</span>
    <span class="meta">第 04 节 / 2026.04.25</span>
  </div>
  <div class="center">
    <div class="eyebrow fade-up d2">From Idea To Final</div>
    <h1>
      <div class="fade-up d3">全链路 AI</div>
      <div class="fade-up d4">创作<span class="accent">工作流</span></div>
    </h1>
    <div class="subtitle fade-up d5">从一个想法，到一篇可以发布的成品</div>
  </div>
  <div class="footer fade-up d6">
    <span class="slogan">始于火花 · 成于实战</span>
    <span>2.5h · 单人讲完 · 讲师名</span>
  </div>
</div>
```

- `<span class="accent">` 会自动加品牌色下划线动画（`::after scaleX`）
- End 页把 `.eyebrow` 改成 `End · Q & A`，`<h1>` 改成收尾文案，其余结构不变

---

## 2. Divider

**约定**：每节开头 1 张，`data-section` 递增，`.tick.current` 位置随节号移。

```html
<div class="slide divider" data-section="4" data-section-name="回放刚才的演示" data-time="28"
     data-notes-strategy="回到演示，一步一步拆解。每步只引入一个新概念。"
     data-notes-speak="接下来我们倒回去看，刚才到底发生了什么。">
  <div class="center">
    <div class="num fade-up d2"><span class="hash">§</span>4</div>
    <div>
      <div class="label fade-up d3">Section · Replay</div>
      <h1 class="fade-up d4">回放刚才的演示</h1>
      <div class="bridge fade-up d5">
        <span class="quote-mark">「</span>接下来我们倒回去看，刚才到底发生了什么<span class="quote-mark">」</span>
      </div>
      <div class="meta-row fade-up d6">
        <span class="pill">⏱ 28 min</span>
        <span>6 步拆解 · 每步 4-5 分钟讲一个概念</span>
      </div>
    </div>
  </div>
  <div class="progress fade-in d6">
    <div class="ticks">
      <!-- 前 3 节已过 → .done；当前节 → .current；后 5 节 → 空 -->
      <span class="tick done"></span><span class="tick done"></span><span class="tick done"></span>
      <span class="tick current"></span>
      <span class="tick"></span><span class="tick"></span><span class="tick"></span>
      <span class="tick"></span><span class="tick"></span>
    </div>
    <span class="label-r">第 4 / 9 节</span>
  </div>
</div>
```

- `.bridge` 是"承上启下原句"——从讲义里 `data-notes-speak` 首句提炼（用引号包住）
- `.meta-row .pill` 是时长徽章；后半段描述从讲义"讲解策略"摘一句
- `.ticks` 里的 `.done / .current / (空)` 按当前节号排。每个 divider 都要更新

**省略 bridge 的减版**：如果讲义没给原句，只留 `<h1>`、`<label>`、`<meta-row>`，留空 `.bridge` div 即可（playground 02b 的 A 版）。

---

## 3. Break

**约定**：休息页，用 `data-no-chrome="true"` 隐藏面包屑。整页居中。

```html
<div class="slide break" data-section="0" data-section-name="中场休息" data-time="10"
     data-no-chrome="true"
     data-notes-speak="前半段讲完了。10 分钟后回来动手搭。">
  <div class="icon fade-up d1">☕</div>
  <div class="label fade-up d2">中场休息</div>
  <h1 class="fade-up d3">喝水 · 走动 · 问个小问题</h1>
  <div class="duration fade-up d4">10 min</div>
  <div class="hint fade-up d5">前半段讲完了——你已经看了演示、知道了文件结构...后半段你会真的在自己电脑上搭一个出来。</div>
</div>
```

- 图标允许 emoji：☕（中场）/ 🚶（走动小休）/ 🌙（晚场复盘）——这是**唯一允许 emoji 装饰**的 slide 类型
- 背景底色 `var(--brand-faint)` 淡蓝，跟内容页白底区别开，学员一眼知道是休息

---

## 4. Concept（内容页 · 单概念）

**约定**：一张页讲一个概念。eyebrow 定位 + h1 主张 + body 2-3 段 + 可选 quote 收束。

```html
<div class="slide content" data-section="4" data-section-name="回放刚才的演示" data-time="28"
     data-notes-speak="一句话太单薄，AI 拿到后容易跑偏...">
  <div class="eyebrow fade-up d1">第二步 · 让想法长大</div>
  <h1 class="fade-up d2">我补充了几个<span class="accent">要点</span></h1>
  <div class="body">
    <p class="fade-up d3">一句话太单薄，AI 拿到后容易跑偏。所以我先跟 Claudian 多聊了几句——补了几个要点。</p>
    <p class="fade-up d4"><strong>注意</strong>：这些补充都会自动追加到 <strong>idea/</strong> 里那条原始笔记下面。</p>
  </div>
  <div class="quote fade-up d5">
    这一步就像做菜前先想好菜谱——你不告诉厨师要放什么料，他只能乱炒。
  </div>
</div>
```

- `<span class="accent">` → 标题局部加 `--brand-soft` 淡黄底（荧光笔扫过效果）
- `<strong>` → 自动加 `--brand` 底部实线下划线（强调词）
- `<span class="key">` → 文字本身变品牌色加粗（关键词）
- `.quote` → 左侧品牌色竖线 + `--brand-faint` 底，用于收束本页观点

**h1 大小调节**：
- `h1` 默认 4.4cqw
- `h1.smaller` 3.6cqw — 长标题用
- `h1.tiny` 3.2cqw — 对比叙事页（title 要给对比区让位）

---

## 5. Steps（内容页 · 多条要点）

两种形态：**一次全出**（fade-up + 递增 delay）和 **按空格步进**（`.stepped` + `data-steps`）。

### 5a · 一次全出（compact 版）

用于"现场演示 6 步概览"这种不需要讲师停顿的场景。

```html
<div class="slide content steps" data-section="2" data-section-name="先看效果" data-time="12"
     data-notes-speak="全程只跟 Claudian 说话。">
  <div class="eyebrow fade-up d1">现场演示</div>
  <h1 class="fade-up d2 smaller">6 步从一句想法到一篇定稿</h1>
  <div class="items compact">
    <div class="item fade-up d3">
      <div class="num">01</div>
      <div class="text">
        <div class="head">说一句想法</div>
        <div class="desc">"我有个想法：AI 时代，普通人怎么抓住这波红利"</div>
      </div>
    </div>
    <div class="item fade-up d4">...</div>
    <!-- d3 ~ d8 递增 -->
  </div>
</div>
```

### 5b · 按空格步进（stepped）

用于"三条规则 / 三个动作"这种讲师要停顿消化的页。

```html
<div class="slide content steps stepped" data-section="5" data-section-name="三条规则" data-time="5"
     data-steps="3"
     data-notes-strategy="按空格一条一条出。每条停顿 30 秒，让学员消化。">
  <div class="eyebrow fade-up d1">三条规则</div>
  <h1 class="fade-up d2 smaller">工作流靠三条规则跑起来</h1>
  <div class="items">
    <div class="item step" data-step="1">
      <div class="num">01</div>
      <div class="text">
        <div class="head">一篇文章一个文件夹</div>
        <div class="desc">所有文件都在 <code>articles/文章名/</code> 下面...</div>
      </div>
    </div>
    <div class="item step" data-step="2">...</div>
    <div class="item step" data-step="3">...</div>
  </div>
</div>
```

- 顶层要加 `.stepped` + `data-steps="N"`
- 每个 item 用 `.step` + `data-step="N"`（不是 fade-up，步进由 JS 加 `.revealed` 控制）
- eyebrow 和 h1 仍用 fade-up，它们一上来就出现
- 不要同时有 `.compact`（步进时每条要够大让讲师停讲）

---

## 6. Table（内容页 · 对照表）

**约定**：`.table-page` 子类，表头用品牌色底，首列等宽字体。

```html
<div class="slide content table-page" data-section="7" data-section-name="动手搭工作流" data-time="20"
     data-notes-speak="提示词会创建这 4 样东西。">
  <div class="eyebrow fade-up d1">部署提示词会创建什么</div>
  <h1 class="fade-up d2 smaller">4 样东西，1-2 分钟搭好</h1>
  <div class="sub fade-up d3">每个扩展都是加一个新动作，结构不变，能做的事变多。</div>
  <table class="fade-in d3">
    <thead>
      <tr>
        <th style="width: 32%;">创建什么</th>
        <th>干什么用的</th>
      </tr>
    </thead>
    <tbody>
      <tr><td class="col-name">articles/</td><td>放你的文章工作区，每篇文章一个子文件夹</td></tr>
      <tr><td class="col-name">idea/</td><td>想法池，随手记想法的地方</td></tr>
      <tr><td class="col-name">AGENTS.md</td><td>给 AI 看的规则手册</td></tr>
    </tbody>
  </table>
</div>
```

- 首列 `.col-name` 自动等宽字体 + 黑色 + 加粗，适合文件路径 / 命令名
- 可选 `.col-stars` 给右侧列用品牌色加粗（难度、星级）
- `.sub` 是可选副标题，放表格和 h1 之间
- 表头背景是 `--brand-faint`（浅蓝），不要改成深色

---

## Slide 顺序约定

按讲义 9 节组装，典型 ~36 页：

```
1.  Cover
2.  §1 Divider
3.  §1 内容页 ×1-2
4.  §2 Divider
5.  §2 内容页 ×2-3
...
9.  §4 Divider
10-15. §4 内容页 ×6（重点节拆成 6 步）
16. §5 Divider
17. §5 内容页（stepped 三条规则）
18. §6 Divider
19. §6 内容页（stepped 三个动作）
20. §6 内容页（flow 流程图）
21. ☕ Break
22. §7 Divider
...
27. 🚶 Break
28. §8 Divider
...
34. §9 内容页（课后作业）
35. End / Q&A
```

**规则**：
- 每节开头必有 Divider；不要省
- 重点节（如 §4）可以拆成 6-7 页，节奏是"每 4-5 分钟一页"
- 轻节（§1 开场、§9 收尾）1-2 页即可
- Break 的 `data-section="0"` 表示不纳入进度（chrome 隐藏）

---

## 缺失字段降级表（**装配时必读**）

当输入规范化后某些字段为空（尤其是 tutorial 模式），按下表降级，**不要瞎编**。配合 `references/source-schema.md` 第 3 节和第 7 节看。

### Cover

| 字段缺失 | 降级 |
|---|---|
| `subtitle` | 省略 `.subtitle` div（不要留空壳） |
| `duration_total` | footer 只写"单人讲完 · 讲师名"，不写时长 |
| `date` | `.top .meta` 只显示"第 N 节"或整体省略该 span |
| `subtitle` + `duration_total` 都缺 | Cover 只保留 `.eyebrow` + `<h1>` + footer slogan，视觉上更空但干净 |

### Divider

| 字段缺失 | 降级 |
|---|---|
| `bridge` | `.bridge` div 整个**省略**（不留空引号，避免出现空引号框） |
| `duration` | 去掉 `<span class="pill">⏱ N min</span>` |
| `duration` + 后半段描述都缺 | 整个 `.meta-row` 省略 |
| `speaker_strategy` | 元素上不写 `data-notes-strategy` attr |
| `speaker_speak` | 不写 `data-notes-speak` attr |

**注意**：`data-section` 和 `.ticks` 进度点**不能省**——进度条依赖它们；没 `number` 时按顺序自动编。

### Break

完全由 `section.is_break` 触发。**没有 break section 就不生成 break slide**，不要塞默认休息页。

### Concept / Steps / Table

| 字段缺失 | 降级 |
|---|---|
| `eyebrow` | 省略 `.eyebrow` div |
| `ContentBlock.heading` 但有 body | `<h1>` 用本节 `section.heading`（降一级当标题用），eyebrow 显示"正文" |
| `step_points` 为 false（或不存在） | **不加** `.stepped` class 和 `data-steps`，走一次全出版（`.compact`） |
| 表格数据列 `description` 列空值 | 该单元格留空字符串，不要写"—"或"暂无"占位 |

### Speaker Notes（所有 slide 通用）

| 字段缺失 | 降级 |
|---|---|
| 两个都没 | 不写任何 `data-notes-*` attr。JS 侧已兼容：N 键召唤出备注栏时显示"本页无备注" |
| 只有 speak 没有 strategy | 只写 `data-notes-speak` attr |
| 只有 strategy 没有 speak | 只写 `data-notes-strategy` attr |

### 整体时间条（底部 chrome）

| 情况 | 降级 |
|---|---|
| 所有 section 都有 `duration` | 时间条正常显示总时长 + 已过刻度 |
| 部分有部分没 | 把"有"的加总作为总时长，chrome 仍显示；Divider 侧按该节是否有 duration 决定 pill 出不出 |
| 所有 section 都没 `duration` | 在 `.deck` 根节点加 `data-no-timeline="true"`；CSS 隐藏 `.chrome .timeline` |

### 典型 tutorial 装配形态（全降级后）

一份只有 `##` 章节和正文的教程，装配结果应当是：

- ✅ Cover（标题取自 `# xxx`，无 subtitle，footer 只写"单人讲完"）
- ✅ 每章 Divider（只有 `<h1>` + `.ticks`，没有 pill、没有 bridge、没有演讲者备注）
- ✅ 章节内容页（concept / steps / table 正常出，视 body 类型而定）
- ❌ 没有 Break
- ❌ 底部没有时间条
- ❌ 按 N 键提示"本页无备注"

**这个形态是 skill 的合法输出，不要因为"看起来光秃秃"就去补假数据。**
