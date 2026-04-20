# 设计系统 · 配色 / 排版 / 动画 / 关键技巧

这套设计系统在所有 playground 和最终 deck 里都已固化。新 deck 不要重新发明，照搬即可。

## CSS Tokens（必须照搬）

```css
:root {
  /* 品牌（用户可覆盖） */
  --brand: #1E40FF;
  --brand-soft: #EEF1FF;       /* 标题 highlighter 黄底替代色 */
  --brand-faint: #F5F7FF;      /* 引言 box / 表头底 */

  /* 火苗渐变（默认 logo） */
  --flame-1: #FFA92E;
  --flame-2: #EF4D2A;
  --flame-3: #C42138;

  /* 中性 */
  --ink: #0A0A0A;
  --ink-2: #3A3A3C;
  --muted: #8E8E93;
  --muted-2: #C7C7CC;
  --line: #E5E5EA;
  --line-2: #F0F0F3;
  --bg: #F5F5F7;

  /* 代码块（暗色） */
  --code-bg: #1a1d24;
  --code-bg-2: #21252e;
  --code-fg: #d4d8e0;
  --code-line: #6b7280;
  --code-com: #5c6577;
  --code-num: #f78c6c;
  --code-str: #c3e88d;

  /* 文件角色 */
  --warn: #b45309;   /* source.md 只读 */
  --done: #047857;   /* final.md 成品 */
}
```

**用户覆盖品牌色时**：只改 `--brand`、`--brand-soft`、`--brand-faint`、火苗渐变。其他不动。

## 字体栈

```css
font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif;
```

代码块用：
```css
font-family: ui-monospace, SFMono-Regular, "JetBrains Mono", Menlo, monospace;
```

**禁止引入外部字体**（不要 Google Fonts、不要 @font-face）。

## 单位约定

- **slide-frame 用 `aspect-ratio: 16/9` + `container-type: inline-size`**——这让 slide 内任何元素都能用 cqw 单位响应 slide 大小
- **slide 内所有尺寸用 cqw**（container query width，1cqw = slide 宽的 1%）
  - 标题大小：6.4cqw（divider）/ 4.4cqw（内容页 h1）/ 3.6cqw（内容页 smaller）/ 3.2cqw（tiny）
  - 正文：1.55cqw
  - 副标题：1.7cqw
  - 内边距：5cqw（左右）/ 8cqw（上下，要给 chrome 留位置）
- **chrome（顶部进度条 + 面包屑）也用 cqw**，跟着 slide 缩放

cqw 让 slide 在不同屏幕（4K、1080p、笔记本）都自动缩放，文字始终保持 slide 内的相对比例。

## 动画原语

```css
.fade-up {
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.85s cubic-bezier(0.2, 0.65, 0.2, 1),
              transform 0.85s cubic-bezier(0.2, 0.65, 0.2, 1);
}
.slide.active.playing .fade-up { opacity: 1; transform: translateY(0); }

.fade-in {
  opacity: 0;
  transition: opacity 0.85s cubic-bezier(0.2, 0.65, 0.2, 1);
}
.slide.active.playing .fade-in { opacity: 1; }

.d1 { transition-delay: 0.10s; } .d2 { transition-delay: 0.30s; }
.d3 { transition-delay: 0.50s; } .d4 { transition-delay: 0.70s; }
.d5 { transition-delay: 0.90s; } .d6 { transition-delay: 1.10s; }
.d7 { transition-delay: 1.30s; } .d8 { transition-delay: 1.50s; }
```

**绝对禁用的动画**：弹跳（bounce）、旋转（rotate）、3D 翻转、大位移（>30px）、抖动、闪烁、脉冲发光。这些都属于"戏剧动画"，破坏克制 Keynote 风。

**唯一允许的"非克制"动画**：
- 流程图箭头亮起后的"流光"效果（`@keyframes flow-light`，1.4s linear infinite，仅作 UI 反馈）
- 标题局部背景的"荧光笔"扫过（淡淡的 `--brand-soft` 底色，不闪不动）

## 重播动画 reset 模式（关键技巧）

只移除 `.playing` 然后再加一次，**不会重新触发动画**——浏览器会把它合并成连续过渡。必须用：

```css
.no-anim, .no-anim *, .no-anim *::before, .no-anim *::after {
  transition: none !important;
}
```

```js
function replay(slideEl) {
  slideEl.classList.add('no-anim');         // 1. 禁用所有 transition
  slideEl.classList.remove('playing');      // 2. 把元素瞬间拉回初始状态
  void slideEl.offsetWidth;                 // 3. 强制 reflow
  requestAnimationFrame(() => {
    slideEl.classList.remove('no-anim');    // 4. 下一帧恢复 transition
    requestAnimationFrame(() => {
      slideEl.classList.add('playing');     // 5. 再下一帧触发动画
    });
  });
}
```

**双 requestAnimationFrame 不能省**——单层不够，浏览器会把"恢复 transition"和"加 playing"合并到同一帧。

## 步进 Reveal 模式（用于三条规则、三个动作等）

```html
<div class="slide stepped" data-steps="3">
  <div class="step" data-step="1">规则 1...</div>
  <div class="step" data-step="2">规则 2...</div>
  <div class="step" data-step="3">规则 3...</div>
</div>
```

```css
.step {
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.6s cubic-bezier(0.2, 0.65, 0.2, 1),
              transform 0.6s cubic-bezier(0.2, 0.65, 0.2, 1);
}
.step.revealed { opacity: 1; transform: translateY(0); }
```

```js
// 用户按空格 → 找出 data-step <= currentStage 的元素，加 .revealed
function next() {
  const active = currentSlide;
  if (active.classList.contains('stepped')) {
    const stage = +active.dataset.stage || 0;
    const max = +active.dataset.steps;
    if (stage < max) {
      active.dataset.stage = stage + 1;
      active.querySelectorAll('.step').forEach(s => {
        if (+s.dataset.step <= stage + 1) s.classList.add('revealed');
      });
      return; // 不翻页
    }
  }
  showSlide(currentIdx + 1);
}
```

## SVG Logo 复用模式

```html
<!-- 在 body 顶部定义一次 -->
<svg width="0" height="0" style="position: absolute;">
  <symbol id="logo-flame" viewBox="0 0 100 110">
    <linearGradient id="flameG" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#FFA92E"/>
      <stop offset=".55" stop-color="#EF4D2A"/>
      <stop offset="1" stop-color="#C42138"/>
    </linearGradient>
    <linearGradient id="dropG" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#FFF2E1"/>
      <stop offset="1" stop-color="#FCD9B8"/>
    </linearGradient>
    <path fill="url(#flameG)" stroke="white" stroke-width="6" stroke-linejoin="round"
          style="paint-order: stroke fill"
          d="M 62 6 C 56 14 50 20 48 24 L 38 12 L 36 28 C 22 38 14 54 14 72 C 14 94 30 104 50 104 C 70 104 86 94 86 72 C 86 48 76 26 62 6 Z"/>
    <path fill="url(#dropG)"
          d="M 50 56 C 38 68 36 82 42 92 C 46 100 54 100 58 92 C 64 82 62 68 50 56 Z"/>
  </symbol>
</svg>

<!-- 在任何地方 use -->
<svg class="logo-mini" style="width: 1.6cqw;"><use href="#logo-flame"/></svg>
```

**为什么用 `paint-order: stroke fill`**：让白色描边画在填充下面，得到"贴纸"效果（描边在边缘外侧）。

**必配的 CSS**（否则 Chromium 会把 SVG 高度默认成 150px，撑爆父容器）：

```css
.logo-mini { display: block; aspect-ratio: 100 / 110; height: auto; }
```

原因：SVG 元素在 CSS 只设 `width` 时，`height` 会 fallback 到 **150px 默认值**（HTML replaced element 规范）。flex 父级会被撑成 150px 高，整行布局塌陷。必须用 `aspect-ratio` 把高度绑回 viewBox 比例。

## Deck 容器 & Slide 切换

```css
body {
  background: #000;  /* letterbox */
  display: flex; align-items: center; justify-content: center;
  min-height: 100vh; overflow: hidden;
}

.deck {
  width: min(100vw, calc(100vh * 16 / 9));
  aspect-ratio: 16 / 9;
  background: white;
  position: relative; overflow: hidden;
  container-type: inline-size;
}

.slide {
  position: absolute; inset: 0;
  opacity: 0; pointer-events: none;
  transition: opacity 0.45s ease;
}
.slide.active { opacity: 1; pointer-events: auto; z-index: 1; }
```

切换时，**对新 active slide 调用 replay reset**，让入场动画重新播。

## Chrome（顶部进度条 + 面包屑 + 页码）

```html
<div class="deck-progress"><div class="fill" id="prog-fill"></div></div>
<div class="deck-top" id="deck-top">
  <div class="breadcrumb">
    <svg class="logo-mini"><use href="#logo-flame"/></svg>
    <span class="sec">§4</span>
    <span class="div">/</span>
    <span>回放刚才的演示</span>
  </div>
  <div class="pageno">页 <b>14</b> / 36</div>
</div>
```

切 slide 时 JS 更新 `prog-fill` 宽度（`(idx+1)/total*100%`）和 breadcrumb 文本（从 slide 的 data-section / data-section-name 读）。

封面 / 休息 / 结束页 chrome 应隐藏（`.deck-top.visible` 切换、根据 slide 的 `data-no-chrome`）。

## 演讲者备注

每张 slide 用 data 属性带备注：

```html
<div class="slide content"
     data-section="4"
     data-section-name="回放刚才的演示"
     data-time="28"
     data-notes-strategy="重点页！停下来，把'三件事'每件单独讲清。"
     data-notes-speak="这是最关键的一步。我说了一句'帮我导入工作流'，系统就自动做了三件事。">
```

按 `N` 弹出侧边栏面板显示 strategy + speak。

## 键盘控制

| 键 | 作用 |
|---|---|
| → / Space / Enter / PageDown | 下一步 / 下一页 |
| ← / PageUp | 上一步 / 上一页 |
| Home / End | 首/末页 |
| N | 演讲者备注 |
| T | 时间条（本节用时） |
| F | 全屏 |
| ? | 帮助面板 |
| Esc | 关闭面板 |

## 颜色用量自检

装配完检查：
1. 任何 slide 的总品牌色面积是否超过 10%？超过 → 改
2. 任何 slide 是否用了未在 token 列表里的颜色？是 → 改成 token
3. 任何 slide 是否有外部资源（字体、图片 CDN）？是 → 内联或删除
4. 任何 slide 是否有跳跃 / 旋转 / 大位移动画？是 → 改成 fade-up / fade-in
