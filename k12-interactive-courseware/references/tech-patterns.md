# 技术实现规范

> 主 skill `k12-interactive-courseware` 在阶段四（代码生成）前必读。
> 涉及外部库、JS 写法、iframe、无障碍、输出格式的**硬约束**见同目录 `engineering-constraints.md`，
> 本文件只讲怎么写得好看、写得稳。两者冲突时以 `engineering-constraints.md` 为准。

---

## 一、CSS 设计系统

### 1.1 基础品牌色（所有学科强制，不得更改）

```css
:root {
  --primary:    #3B8DBD;  /* 主蓝：数据、标签、选中态 */
  --secondary:  #E76F6F;  /* 珊瑚红：重点、关键对象 */
  --accent:     #F4A442;  /* 橙：主按钮、强调、流向 */
  --success:    #5CB85C;  /* 绿：正确/完成 */
  --dash:       #B0BEC5;  /* 浅灰虚线：负反馈、氢键、辅助关系 */
}
```

**图形内部的颜色一律走各学科 `subjects/*.md` 的「学科色规范」章节**（矢量箭头、CPK 原子配色、
生物分子配色等），本文件的品牌色只在学科色规范未覆盖处兜底。

### 1.2 中性色与页面尺度（UI 层）

| 项目 | 取值 |
|------|------|
| 页面背景 | `#f9fafb` |
| 卡片背景 | `#ffffff` |
| 主要文字 | `#1f2937` |
| 次要文字 | `#4b5563` |
| 边框 | `1px solid #e5e7eb` |
| 卡片圆角 | 16px |
| 控件圆角 | 10px |
| 轻阴影 | `0 1px 3px rgba(15, 23, 42, 0.08)` |
| 内容最大宽度 | 1120px |
| 页面留白 | 小屏 16px / 宽屏 24px |
| 可点击区域 | ≥44px × 44px |
| 常规动画 | 160ms ~ 320ms |

> 与旧课件系列的 `#F5F7FA / #2C3E50 / #6C757D / #E2E8F0` 属同一色阶，视觉互通；
> 新课件统一使用上表数值。

### 1.3 学科强调色（仅用于 UI 点缀，不用于图形）

用于标题下划线、学科标签、分区色条等页面装饰：

| 学科 | 主强调 | 次强调 |
|------|--------|--------|
| 数学 | `#2563eb` | `#7c3aed` |
| 物理 | `#1d4ed8` | `#0284c7` |
| 化学 | `#047857` | `#0e7490` |
| 生物 | `#15803d` | `#4d7c0f` |
| 语文 | `#b91c1c` | `#c2410c` |
| 历史 | `#78350f` | `#b45309` |
| 地理 | `#047857` | `#0369a1` |
| 综合 | `#334155` | `#4338ca` |

图形内部**不得**使用本表颜色，避免与学科语义色冲突。

### 1.4 完整样式块（可直接复制）

```css
* { margin: 0; padding: 0; box-sizing: border-box; }

:root {
  --primary: #3B8DBD; --secondary: #E76F6F; --accent: #F4A442;
  --success: #5CB85C; --dash: #B0BEC5;
  --bg-page: #f9fafb; --bg-card: #ffffff;
  --text-main: #1f2937; --text-sub: #4b5563; --border: #e5e7eb;
  --bg-formula: #E8F4FC; --bg-hint: #FFF8E7;
  --r-card: 16px; --r-ctrl: 10px;
  --shadow: 0 1px 3px rgba(15, 23, 42, 0.08);
  --hit: 44px;
}

html, body { width: 100%; max-width: 100%; overflow-x: hidden; }

body {
  font-family: "Microsoft YaHei", "PingFang SC", "Noto Sans SC", sans-serif;
  color: var(--text-main);
  background: var(--bg-page);
  font-size: 16px;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
  -webkit-touch-callout: none;
}

/* ---------- 布局：CSS Grid，正常文档流（iframe 友好） ---------- */
.layout {
  max-width: 1120px;
  margin: 0 auto;
  padding: 16px;
  display: grid;
  grid-template-columns: 1fr;
  gap: 16px;
}
@media (min-width: 768px) { .layout { padding: 24px; gap: 24px; } }
@media (min-width: 1024px) {
  .layout { grid-template-columns: minmax(0, 1fr) 320px; align-items: start; }
}

.stage {
  background: var(--bg-card);
  border: 1px solid var(--border);
  border-radius: var(--r-card);
  box-shadow: var(--shadow);
  padding: 16px;
  min-height: 420px;
}
/* 画布内的网格底纹（几何/坐标类场景用） */
.stage.grid-bg {
  background-color: var(--bg-card);
  background-image:
    linear-gradient(#eef1f4 1px, transparent 1px),
    linear-gradient(90deg, #eef1f4 1px, transparent 1px);
  background-size: 40px 40px;
}

.panel {
  background: var(--bg-card);
  border: 1px solid var(--border);
  border-radius: var(--r-card);
  box-shadow: var(--shadow);
  padding: 20px;
}
@media (min-width: 1024px) {
  .panel { position: sticky; top: 24px; max-height: calc(100vh - 48px); overflow-y: auto; }
}

/* ---------- 组件 ---------- */
.panel-title {
  font-size: 18px; font-weight: 700; margin-bottom: 16px;
  padding-bottom: 10px; border-bottom: 2px solid var(--bg-formula); text-align: center;
}

.metric-row { display: flex; gap: 12px; margin-bottom: 14px; }
.metric-card {
  flex: 1; background: var(--bg-page); border: 1px solid var(--border);
  border-radius: var(--r-ctrl); padding: 10px 8px; text-align: center;
}
.metric-card .label {
  font-size: 12px; color: var(--text-sub); margin-bottom: 4px;
  display: flex; align-items: center; justify-content: center; gap: 4px;
}
.metric-card .value { font-size: 24px; font-weight: 700; }
.metric-card.primary .value { color: var(--primary); }
.metric-card.secondary .value { color: var(--secondary); }
.metric-card.accent .value { color: var(--accent); }
.metric-dot { width: 8px; height: 8px; border-radius: 50%; display: inline-block; }
.metric-dot.primary { background: var(--primary); }
.metric-dot.secondary { background: var(--secondary); }

.formula-box {
  background: var(--bg-formula); border-radius: var(--r-ctrl);
  padding: 14px; margin-bottom: 14px; text-align: center; font-size: 15px; line-height: 1.6;
}
.formula-box .hl-primary { color: var(--primary); font-weight: 700; }
.formula-box .hl-secondary { color: var(--secondary); font-weight: 700; }

.control-group { margin-bottom: 16px; }
.control-group > .label {
  font-size: 13px; color: var(--text-sub); margin-bottom: 8px; display: block;
}

.segmented-control { display: flex; background: var(--bg-page); border-radius: var(--r-ctrl); padding: 4px; gap: 4px; }
.segmented-control button {
  flex: 1; min-height: var(--hit); padding: 8px 4px; border: none; border-radius: 8px;
  background: transparent; color: var(--text-sub); font-size: 13px; font-family: inherit;
  cursor: pointer; transition: background 0.2s, color 0.2s;
}
.segmented-control button.active {
  background: var(--primary); color: #fff; box-shadow: 0 2px 6px rgba(59, 141, 189, 0.3);
}

.slider {
  width: 100%; -webkit-appearance: none; appearance: none;
  height: 6px; border-radius: 3px; background: var(--border); outline: none;
}
.slider::-webkit-slider-thumb {
  -webkit-appearance: none; appearance: none;
  width: 28px; height: 28px; border-radius: 50%; background: var(--primary);
  cursor: pointer; border: 2px solid #fff; box-shadow: 0 2px 6px rgba(59, 141, 189, 0.4);
}
.slider::-moz-range-thumb {
  width: 24px; height: 24px; border-radius: 50%; border: 2px solid #fff;
  background: var(--primary); cursor: pointer;
}
.slider-value { float: right; font-size: 13px; color: var(--primary); font-weight: 600; }

.btn-row { display: flex; gap: 10px; margin-top: 8px; flex-wrap: wrap; }
.btn {
  flex: 1 1 120px; min-height: var(--hit); padding: 10px 12px;
  border: none; border-radius: var(--r-ctrl);
  font-size: 15px; font-family: inherit; cursor: pointer;
  transition: background 0.2s;
  display: inline-flex; align-items: center; justify-content: center; gap: 6px;
}
.btn-primary { background: var(--accent); color: #fff; box-shadow: 0 4px 12px rgba(244, 164, 66, 0.3); }
.btn-primary:hover { background: #E09400; }
.btn-secondary { background: var(--bg-page); color: var(--text-main); border: 1px solid var(--border); }
.btn-secondary:hover { background: #eff1f4; }
.btn:disabled { opacity: 0.45; cursor: not-allowed; }

.hint-text {
  margin-top: 14px; padding: 10px 12px; background: var(--bg-hint);
  border-radius: 8px; font-size: 12px; color: var(--text-sub); line-height: 1.5;
}
.hint-text::before { content: "💡"; margin-right: 6px; }

.step-card {
  background: var(--bg-page); border-left: 4px solid var(--primary);
  border-radius: 8px; padding: 12px 14px; margin-bottom: 12px; font-size: 14px; line-height: 1.7;
}

.feedback { margin-top: 12px; padding: 10px 12px; border-radius: 8px; font-size: 14px;
  text-align: center; font-weight: 600; opacity: 0; transition: opacity 0.2s; }
.feedback.show { opacity: 1; }
.feedback.correct { background: rgba(92, 184, 92, 0.15); color: var(--success); }
.feedback.wrong { background: rgba(231, 111, 111, 0.15); color: var(--secondary); }

#hint-overlay {
  position: fixed; left: 50%; top: 32px; transform: translateX(-50%);
  background: rgba(31, 41, 55, 0.9); color: #fff; padding: 10px 20px;
  border-radius: 24px; font-size: 15px; z-index: 999; pointer-events: none;
  transition: opacity 0.6s; opacity: 1;
}
#hint-overlay.hide { opacity: 0; }

/* ---------- 动画与无障碍 ---------- */
@media (prefers-reduced-motion: reduce) {
  * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
}
:focus-visible { outline: 3px solid var(--primary); outline-offset: 2px; }

/* ---------- 练习测评类布局 ---------- */
.quiz-card {
  max-width: 760px; margin: 0 auto; background: var(--bg-card);
  border-radius: var(--r-card); padding: 24px; box-shadow: var(--shadow);
}
.score-bar {
  display: flex; align-items: center; justify-content: space-between;
  gap: 12px; padding: 12px 0; font-size: 15px; flex-wrap: wrap;
}
```

**布局纪律**：用 CSS Grid + Flexbox + 媒体查询，正常文档流。320px 宽度不得横向滚动。
不依赖 hover 传达关键信息（触屏无 hover）。动画只改 `transform` 与 `opacity`。

---

## 二、HTML 文件骨架

```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>课题名 · 互动课件</title>
  <!-- 白名单资源按需引入，见 engineering-constraints.md -->
  <style>/* 上述设计系统 + 本课专属样式 */</style>
</head>
<body>
  <!-- 顶部设计说明注释，格式见 engineering-constraints.md 第 8 节 -->

  <div class="layout">
    <main class="stage" id="stage">
      <svg id="board" width="100%" height="100%" role="img" aria-label="简述画面内容">
        <title>简述画面内容</title>
      </svg>
    </main>
    <aside class="panel">
      <div class="panel-title">课题名</div>
      <!-- 指标卡 / 公式卡 / 控件 / 按钮 / 提示 -->
    </aside>
  </div>

  <div id="hint-overlay">拖动滑块，观察图形变化</div>
  <noscript>本页交互需要启用 JavaScript；核心知识与题目仍可静态阅读。</noscript>

  <script>
    function safeInit(name, initializer) {
      try { initializer(); } catch (error) { console.error(`${name} 初始化失败`, error); }
    }
    document.addEventListener('DOMContentLoaded', () => {
      safeInit('概念演示', initConceptDemo);
      safeInit('互动练习', initPractice);
      safeInit('页面导航', initNavigation);
    });
    // 各 initXxx 函数定义
  </script>
</body>
</html>
```

**状态管理**：状态集中在一个 `state` 对象，所有渲染由 `render()` 从 `state` 推导。
不要在各处直接改 DOM，否则多控件联动时状态会失控。

```javascript
const state = { a: 3, b: 4, mode: 'explore', step: 0 };
function render() { /* 只根据 state 重绘 */ }
function set(patch) { Object.assign(state, patch); render(); }
```

---

## 三、2D 实现（SVG 优先）

**SVG 优先**：图元 <300、需要文字标注、需要热区、需要清晰矢量边缘。
**Canvas 优先**：大量粒子、逐帧动画、手绘笔迹、像素处理。

### SVG 分层（标注/作图类课件必备）

```
从下到上：
├── L1 背景网格（CSS background 或 <pattern>）
├── L2 内容层（程序绘制，学生不可擦除）
├── L3 学生作图层（手绘线条、辅助线、批注）—— 可独立撤销/清空
└── L4 交互热区层（可拖拽点、按钮）
```

```html
<svg id="board" viewBox="0 0 1000 700" preserveAspectRatio="xMidYMid meet">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5"
            markerWidth="7" markerHeight="7" orient="auto-start-reverse">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#E76F6F"/>
    </marker>
    <pattern id="grid" width="40" height="40" patternUnits="userSpaceOnUse">
      <path d="M 40 0 L 0 0 0 40" fill="none" stroke="#eef1f4" stroke-width="1"/>
    </pattern>
  </defs>
  <rect width="1000" height="700" fill="url(#grid)"/>
  <g id="content-layer"></g>
  <g id="draw-layer"></g>
  <g id="hit-layer"></g>
</svg>
```

**带箭头有向线段**（物理矢量、生物流向、化学迁移）：

```html
<line x1="100" y1="200" x2="300" y2="120"
      stroke="#E76F6F" stroke-width="3" marker-end="url(#arrow)"/>
```

**屏幕坐标 → SVG 坐标**（viewBox 缩放时拖拽必备）：

```javascript
const svg = document.getElementById('board');
const pt = svg.createSVGPoint();
function toSVG(evt) {
  pt.x = evt.clientX; pt.y = evt.clientY;
  const m = svg.getScreenCTM();
  return m ? pt.matrixTransform(m.inverse()) : { x: 0, y: 0 };
}
```

### 拖拽（Pointer Events，鼠标与触摸统一）

```javascript
let dragEl = null;
function onDown(e) {
  dragEl = e.target.closest('[data-drag]');
  if (!dragEl) return;
  dragEl.setPointerCapture(e.pointerId);
}
function onMove(e) {
  if (!dragEl) return;
  const p = toSVG(e);
  const b = dragEl.dataset;
  const x = Math.min(Math.max(p.x, +b.minX), +b.maxX);
  const y = Math.min(Math.max(p.y, +b.minY), +b.maxY);
  set({ x, y });
}
function onUp(e) {
  if (dragEl && e.pointerId != null && dragEl.hasPointerCapture(e.pointerId)) {
    dragEl.releasePointerCapture(e.pointerId);
  }
  dragEl = null;
}
['pointerdown','pointermove','pointerup','pointercancel'].forEach(t =>
  svg.addEventListener(t, { pointerdown: onDown, pointermove: onMove,
                            pointerup: onUp, pointercancel: onUp }[t])
);
```

拖拽点在 `<g>` 内叠加一个透明 `r="22"` 的 `<circle>` 作热区（保证 ≥44px）。
**每个拖拽操作必须提供点击或键盘替代路径**（无障碍要求）。

### 吸附容错（小学段必备）

```javascript
function snap(p, targets, radius = 30) {
  for (const t of targets) {
    if (Math.hypot(p.x - t.x, p.y - t.y) < radius) return { x: t.x, y: t.y, snapped: true };
  }
  return p;
}
```

### 手绘笔迹（SVG path）

```javascript
const drawLayer = document.getElementById('draw-layer');
let drawing = false, cur = null; const history = [];
drawLayer.addEventListener('pointerdown', e => {
  drawing = true;
  const p = toSVG(e);
  cur = document.createElementNS('http://www.w3.org/2000/svg', 'path');
  cur.setAttribute('d', `M ${p.x} ${p.y}`);
  cur.setAttribute('fill', 'none');
  cur.setAttribute('stroke', '#E76F6F');
  cur.setAttribute('stroke-width', '2');
  cur.setAttribute('stroke-linecap', 'round');
  drawLayer.appendChild(cur); history.push(cur);
});
drawLayer.addEventListener('pointermove', e => {
  if (!drawing || !cur) return;
  const p = toSVG(e);
  cur.setAttribute('d', cur.getAttribute('d') + ` L ${p.x} ${p.y}`);
});
window.addEventListener('pointerup', () => { drawing = false; });
// 撤销：const el = history.pop(); if (el) el.remove();
// 清空：drawLayer.textContent = '';
```

### Canvas 模板（高分屏不模糊）

```javascript
const cv = document.getElementById('cv'), ctx = cv.getContext('2d');
function resize() {
  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const r = cv.getBoundingClientRect();
  cv.width = Math.round(r.width * dpr);
  cv.height = Math.round(r.height * dpr);
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
}
window.addEventListener('resize', resize);
resize();
```

### 动画循环（可清理）

```javascript
let rafId = null, playing = false, t = 0, last = 0;
function loop(now) {
  rafId = requestAnimationFrame(loop);
  const dt = Math.min(now - last, 50);   // 限幅，切后台回来不跳帧
  last = now; t += dt;
  drawFrame(t);
}
function play() { if (playing) return; playing = true; last = performance.now(); rafId = requestAnimationFrame(loop); }
function pause() { playing = false; if (rafId) cancelAnimationFrame(rafId); rafId = null; }
function reset() { pause(); t = 0; drawFrame(0); }
```

---

## 四、3D 实现（Three.js，白名单版本）

**仅在内容本质是立体/空间结构时使用**。平面关系、流程图、文本结构一律 2D。

```html
<script type="importmap">
{
  "imports": {
    "three": "https://staticfile.sxw.cn/cdn/three%400.185.1/build/three.module.js",
    "three/addons/": "https://staticfile.sxw.cn/cdn/three%400.185.1/examples/jsm/"
  }
}
</script>
<script type="module">
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { CSS2DRenderer, CSS2DObject } from 'three/addons/renderers/CSS2DRenderer.js';

const container = document.getElementById('stage');
const scene = new THREE.Scene();
scene.background = new THREE.Color('#f9fafb');

const camera = new THREE.PerspectiveCamera(45, container.clientWidth / container.clientHeight, 0.1, 1000);
camera.position.set(4, 3, 6);

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.setSize(container.clientWidth, container.clientHeight);
container.appendChild(renderer.domElement);

const labelRenderer = new CSS2DRenderer();
labelRenderer.setSize(container.clientWidth, container.clientHeight);
Object.assign(labelRenderer.domElement.style,
  { position: 'absolute', top: '0', left: '0', pointerEvents: 'none' });
container.appendChild(labelRenderer.domElement);

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.08;
controls.touches = { ONE: THREE.TOUCH.ROTATE, TWO: THREE.TOUCH.DOLLY_PAN };

scene.add(new THREE.AmbientLight(0xffffff, 0.65));
const key = new THREE.DirectionalLight(0xffffff, 0.8); key.position.set(5, 8, 6); scene.add(key);
const fill = new THREE.DirectionalLight(0xffffff, 0.35); fill.position.set(-5, 3, -4); scene.add(fill);

function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
  labelRenderer.render(scene, camera);
}
animate();

window.addEventListener('resize', () => {
  camera.aspect = container.clientWidth / container.clientHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(container.clientWidth, container.clientHeight);
  labelRenderer.setSize(container.clientWidth, container.clientHeight);
});
</script>
```

**材质建议**：教学场景优先 `MeshStandardMaterial` + `transparent: true, opacity: 0.6~0.85`
配合 `EdgesGeometry` 描边，比纯实体更能看清内部结构与面之间的关系。

**常用几何体**：`BoxGeometry`、`SphereGeometry(r, 48, 32)`、`CylinderGeometry(rt, rb, h, 48)`、
`ConeGeometry`、`TorusGeometry`、`LatheGeometry`（旋转体）、`ExtrudeGeometry`（拉伸体）。

**3D 中的颜色**必须走学科色规范（见 `subjects/*.md` 第六节）；学科未覆盖时优先主蓝/珊瑚红/橙。

---

## 五、特殊能力

### 朗读（语文/英语，零依赖）

```javascript
function speak(text, lang = 'zh-CN', rate = 0.9) {
  if (!('speechSynthesis' in window)) return;
  speechSynthesis.cancel();
  const u = new SpeechSynthesisUtterance(text);
  u.lang = lang; u.rate = rate;
  speechSynthesis.speak(u);
}
```

用 `u.onboundary` 事件做逐句高亮同步。中文 `lang='zh-CN'`，英文 `lang='en-US'`。
**不得引入任何外部 TTS 或音频文件**。

### 音高与音效（音乐、反馈音）

浏览器禁止自动播放，**必须在用户手势后解锁**：

```javascript
let actx = null;
function audio() {
  if (!actx) actx = new (window.AudioContext || window.webkitAudioContext)();
  if (actx.state === 'suspended') actx.resume();
  return actx;
}
function tone(freq, dur = 0.4, type = 'sine') {
  const c = audio(), o = c.createOscillator(), g = c.createGain();
  o.type = type; o.frequency.value = freq;
  g.gain.setValueAtTime(0.0001, c.currentTime);
  g.gain.exponentialRampToValueAtTime(0.3, c.currentTime + 0.02);
  g.gain.exponentialRampToValueAtTime(0.0001, c.currentTime + dur);
  o.connect(g); g.connect(c.destination);
  o.start(); o.stop(c.currentTime + dur);
}
// 十二平均律：f = 440 * 2 ** ((midi - 69) / 12)
```

录音跟读需 `MediaRecorder`，仅在 HTTPS/localhost 可用；`file://` 打开会失败，需在页面给出提示与替代方案。

### 中文排版特殊需求

```css
/* 田字格 */
.tianzi { display: inline-grid; place-items: center; width: 64px; height: 64px;
  border: 2px solid var(--secondary); border-radius: 4px;
  background-image: linear-gradient(var(--border) 1px, transparent 1px),
                    linear-gradient(90deg, var(--border) 1px, transparent 1px);
  background-size: 32px 32px; background-position: center; font-size: 40px; }
/* 英语四线三格 */
.fourline { line-height: 3; background-image:
  repeating-linear-gradient(to bottom, transparent 0 23px,
    var(--primary) 23px 24px, transparent 24px 47px,
    var(--border) 47px 48px, transparent 48px 71px, var(--border) 71px 72px); }
/* 拼音 */
ruby { ruby-align: center; } rt { font-size: 0.55em; color: var(--text-sub); }
/* 化学式上下标 */
.sub { font-size: 0.7em; vertical-align: sub; }
.sup { font-size: 0.7em; vertical-align: super; }
/* 代码（信息科技） */
code, pre { font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace; }
```

---

## 六、性能

- **渲染节流**：滑块连续拖动用 `requestAnimationFrame` 合并渲染，不在 `input` 事件里同步重绘大场景
- **SVG 图元** ≤300 个；超出改 Canvas
- **Canvas / Three.js** 的 `devicePixelRatio` 上限取 2，避免 4K 屏卡顿
- **动画限幅**：`dt = Math.min(now - last, 50)`
- **静止时**暂停渲染循环（`controls.addEventListener('change', renderOnce)` 模式）
- **字体**只用系统字体栈，不加载 Web Font
- **动画**只改 `transform` / `opacity`，并遵循 `prefers-reduced-motion`
- **定时器和动画循环**必须能在重置/卸载时清理
