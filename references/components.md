# 组件 · 4 种可复用 slide 内部件

这 4 个组件嵌在 `.slide.content` 内部，不是 slide 本身。按需挑用——不是每张页都要带组件。CSS 全部已写在 `assets/final-deck-example.html`，装配时照抄 HTML 骨架即可。

| 组件 | 类 | 典型用途 |
|---|---|---|
| 1. Tree | `.tree-box .tree` | 展示项目目录结构 |
| 2. Flow | `.flow .flow-row` | 节点 → 箭头的流水线图 |
| 3. Compare | `.compare-stage` | "没有 X 的样子 vs 有了 X 的样子" 两态切换 |
| 4. Code | `.code-stage .code-window` | 终端窗口风代码块 |

---

## 1. Tree（目录树）

**用途**：讲项目文件结构、vault 布局、部署后的目录。用等宽字体 + 色彩按角色分。

```html
<div class="slide content" data-section="4" data-time="28">
  <div class="eyebrow fade-up d1">回头看 · 完整结构</div>
  <h1 class="fade-up d2 smaller">现在每个文件你都已经认识了</h1>
  <div class="tree-box">
    <div class="tree fade-in d2"><span class="folder">articles/抓住 AI 时代的红利/</span>
<span class="branch">├── </span><span class="folder">draft/</span>
<span class="branch">│   ├── </span><span class="file-source">source.md</span><span class="comment warn">          ← 存档点（不要改）</span>
<span class="branch">│   └── </span><span class="file-working">working.md</span><span class="comment write">         ← 工作台（你在这里改）</span>
<span class="branch">└── </span><span class="folder">final/</span>
<span class="branch">    ├── </span><span class="file-final">final.md</span><span class="comment done">           ← 正式稿（定稿后才出现）</span>
<span class="branch">    └── </span><span class="file-cover">cover-prompt.md</span><span class="comment done">    ← 封面提示词</span></div>
  </div>
</div>
```

**文件角色配色**（用 `--warn` / `--brand` / `--done` 三色区分）：

| class | 颜色 | 语义 |
|---|---|---|
| `.folder` | `--brand`（蓝） | 文件夹 |
| `.file-source` | `--warn`（橘） | 只读存档 |
| `.file-working` | `--brand`（蓝） | 可写工作台 |
| `.file-final`/`.file-cover` | `--done`（绿） | 成品 |
| `.branch` | `--muted-2`（灰） | 树枝符号 ├── │ └── |
| `.comment` | `--muted`（浅灰） | 行内注释；可叠 `.warn`/`.write`/`.done` 染色 |

**排版要点**：
- `.tree` 用 `white-space: pre`，所以缩进靠**真实空格**，不是 CSS margin
- 树枝符号（├── │ └──）用全角 + 半角混合保证对齐，跟 Unix `tree` 命令输出一致
- 整块上 `fade-in d2`（不是 fade-up），避免整树跳动

**反模式**：
- ❌ 用 `<ul>` / `<li>` 代替 `<span class="branch">`——会失去等宽排版
- ❌ 在 tree 里用 `<br>`——直接换行符，pre 会保留
- ❌ 树深度超过 4 层——学员一眼看不过来，拆成两页

---

## 2. Flow（流程图）

**用途**：展示单向流程（"导入 → 修改 → 定稿"、"想法 → 补充要点 → 导入"）。支持逐节点亮起。

```html
<div class="slide content" data-section="6" data-time="5">
  <div class="eyebrow fade-up d1">就这三步</div>
  <h1 class="fade-up d2 smaller">导入 → 修改 → 定稿</h1>
  <div class="flow">
    <div>
      <div class="flow-row">
        <div class="flow-node" data-lit-step="1">
          <div class="name">导入</div>
          <div class="desc">扩写 → draft/</div>
        </div>
        <div class="flow-arrow" data-lit-step="2"></div>
        <div class="flow-node" data-lit-step="3">
          <div class="name">修改</div>
          <div class="desc">working.md</div>
        </div>
        <div class="flow-arrow" data-lit-step="4"></div>
        <div class="flow-node" data-lit-step="5">
          <div class="name">定稿</div>
          <div class="desc">final/ (含封面)</div>
        </div>
      </div>
    </div>
  </div>
</div>
```

**亮灯机制**：
- 每个 `.flow-node` / `.flow-arrow` 带 `data-lit-step="N"`
- JS 会在 slide active 后，按 N 递增 `setTimeout` 逐个加 `.lit` 类
- 节点从灰扁 → 蓝立（缩放 + 阴影 + 蓝底）
- 箭头从灰线 → 蓝线 + 流光动画（`@keyframes flow-light`，1.4s 循环）

**"未来"节点**（链路扩展提示）：
给未解锁节点加 `.future` 类 → 虚线边框 + 灰色。亮起后仍保持 `muted` 色，用来暗示"这一步以后会加，今天先不展开"：

```html
<div class="flow-node future" data-lit-step="7">
  <div class="name">发布</div>
  <div class="desc">下节课</div>
</div>
```

**legend**（可选图例）：
```html
<div class="flow-legend fade-up d5">
  <span><i class="swatch now"></i>今天打通</span>
  <span><i class="swatch future"></i>骨架之上可加</span>
</div>
```

**反模式**：
- ❌ 超过 5 个节点——视觉拥挤，考虑拆成两行或改成 steps 模板
- ❌ 双向箭头——工作流是单向的，双向会破坏"流水线"心智
- ❌ 节点里塞整段描述——`.desc` 只放文件名 / 目录 / 短标签

---

## 3. Compare（对比叙事）

**用途**：二元对比"没有 X 的样子 vs 有了 X 的样子"。按空格切换两态，视觉冲击比说理强 10 倍。

```html
<div class="slide content" data-section="3" data-time="5"
     data-toggle-compare="true"
     data-notes-strategy="先停在'惨状'让学员感受 5 秒，再按空格切到流水线。">
  <div class="eyebrow fade-up d1" id="cmp3-eyebrow">没有工作流的样子</div>
  <h1 class="fade-up d2 tiny" id="cmp3-title">同一件事，散装做是这样的</h1>

  <div class="compare-stage" id="cmp3-stage">
    <!-- 状态 1：散乱 -->
    <div class="compare-state bad">
      <div class="scatter fade-in d3">
        <div class="file f1"><span class="ico">📄</span>新建文档(3).md</div>
        <div class="file f2"><span class="ico">📄</span>草稿v2.md</div>
        <div class="file f8"><span class="ico">⚠️</span>哪个是最新的?.md</div>
        <!-- ... f1 ~ f11，位置由 .scatter .file.fN 的 left/top/rotate 决定 -->
      </div>
    </div>

    <!-- 状态 2：流水线 -->
    <div class="compare-state good">
      <div class="pipeline">
        <div class="pipe-node"><div class="name">想法池</div><div class="desc">idea/</div></div>
        <div class="arrow"></div>
        <div class="pipe-node"><div class="name">工作台</div><div class="desc">working.md</div></div>
        <div class="arrow"></div>
        <div class="pipe-node"><div class="name">成品</div><div class="desc">final.md</div></div>
      </div>
    </div>
  </div>
</div>
```

**切换机制**：
- slide 顶层加 `data-toggle-compare="true"` 告诉 JS：这张页按空格切换 `compare-stage` 的 `.is-good` 类
- 初始 `.bad` 可见，`.good` 透明缩小（scale 0.98）
- 加 `.is-good` 后 `.bad` 淡出 + 放大（scale 1.02）消失，`.good` 淡入 + 复位
- 过渡时长 0.7s

**文案联动**（可选）：切换时同步改 eyebrow + h1 文案，用 JS 读 `data-good-eyebrow` / `data-good-title`，实现"标题也跟着换"：

```html
<div class="slide content" ...
     data-good-eyebrow="有了工作流的样子"
     data-good-title="同一件事，流水线做是这样的">
```

**反模式**：
- ❌ 左右分栏 + 同时显示——视觉冲击被削弱，达不到"停留感受 → 切换反转"的效果
- ❌ 切换动画用旋转 / 翻转——违反克制原则，只能 opacity + scale
- ❌ `scatter` 里超过 12 个文件——太满，感受从"混乱"变"抽象"

---

## 4. Code（代码块）

**用途**：展示代码、提示词、配置片段。终端窗口风——深色底 + 红黄绿灯 + 行号。

```html
<div class="slide content" data-section="7" data-time="20">
  <div class="eyebrow fade-up d1">部署提示词 · 节选</div>
  <h1 class="fade-up d2 smaller">把这段完整复制，发给 Claudian</h1>

  <div class="code-stage">
    <div class="code-window fade-in d2">
      <div class="code-header">
        <div class="dots">
          <span class="dot r"></span>
          <span class="dot y"></span>
          <span class="dot g"></span>
        </div>
        <span class="filename">deploy-prompt.md</span>
      </div>
      <div class="code-body"><span class="ln">1</span><span class="heading"># 一键部署提示词</span>
<span class="ln">2</span>
<span class="ln">3</span>请帮我在当前 vault 搭一个极简的 AI 写作工作流。
<span class="ln">4</span>做<span class="num">三件事</span>，全部完成再回复我。
<span class="ln">5</span>
<span class="ln">6</span><span class="heading">## 一、建三个目录</span>
<span class="ln">7</span>
<span class="ln">8</span>- <span class="str">`articles/`</span><span class="com">（文章工作区）</span>
<span class="ln">9</span>- <span class="str">`idea/`</span><span class="com">（想法池）</span>
<span class="ln">10</span>
<span class="ln">11</span><span class="com"># …完整版见 assets/deploy-prompt.md</span></div>
    </div>
  </div>
</div>
```

**轻量语法色**（只 4 类，不要引 highlight.js）：

| class | 用途 | 颜色 token |
|---|---|---|
| `.ln` | 行号 | `--code-line`（暗灰） |
| `.com` | 注释 | `--code-com`（蓝灰） |
| `.str` | 字符串 / 路径 | `--code-str`（淡绿） |
| `.num` | 数字 / 数量 | `--code-num`（暖橙） |
| `.heading` | Markdown 标题 | 自定义 `#f8b886`（暖白） |

**排版要点**：
- `.code-body` 用 `white-space: pre`，行内直接换行（不要 `<br>`）
- 每行开头 `<span class="ln">N</span>` 固定宽度 `2cqw` 右对齐
- 整段 `fade-in`（不是 fade-up），代码块一次淡入，不要字字出现
- `max-width: 80%` 让窗口不占满，视觉更像"放在桌面上的终端"

**反模式**：
- ❌ 完整贴 100 行代码——改贴节选 + "完整版见 xxx" 的引用
- ❌ 用浅色底——违反设计系统（只有代码块允许深色窗口，内容页不能整页深色）
- ❌ 代码里放动画 / 步进——代码块是静态参考，不是互动元素
- ❌ 代码字号 < 0.9cqw——学员后排看不清，1.05cqw 是下限

---

## 组件嵌入位置

组件都放在 `.slide.content` 的 `.body` / `.tree-box` / `.compare-stage` / `.code-stage` / `.flow` 容器里。组件本身用 `flex: 1` 占满 slide 纵向剩余空间——所以一个 slide **只放一个组件**，不要混用。

如果需要"标题 + 组件 + 收束 quote"的三段结构，组件要瘦身（减少行数 / 节点数）给 quote 留位置。

---

## 动画延迟建议

| 元素 | 建议 delay class |
|---|---|
| eyebrow | d1 |
| h1 | d2 |
| 组件（tree/flow/compare/code）整块 | d2 或 d3 |
| 流程图节点首个 lit | d3（JS 控制 setTimeout） |
| 底部 quote / legend | d5 |

组件通常用 `fade-in`（纯淡入）而不是 `fade-up`（淡入 + 上移），避免整块滑动干扰阅读。
