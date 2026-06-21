---
name: ultra-design-pro
description: 当需要构建高质量、极具设计感、纯 2D 液态玻璃风格的前端界面（Dashboard、落地页、SaaS 控制台、可视化看板、品牌门户等）时使用。技术栈：TypeScript + React + Next.js (App Router) + Tailwind CSS + shadcn/ui + Radix + Motion (motion/react + framer-motion) + Zustand。三大取向 —— ① 液态玻璃（首要准则）：所有面板/卡片/弹窗用 `.glass` / `.glass-strong` DOM 玻璃 = backdrop-filter blur+saturate 磨砂折射 + 半透底色 + 菲涅尔顶部高光(sheen) + 边缘色散(rim) + 浮起投影，纯 CSS 实现真液态质感，零 3D 库。② 流星夜空 + 星尘交互背景：body 暗色夜空渐变兜底，Canvas 烘焙静态星空底(280 星点不每帧重绘)，DOM/CSS 流星群以 keyframes 沿自身轴线斜飞(头核+渐隐拖尾+glow，同时可见≤6 条、偶发优雅、速度舒缓 3~6s、随主题色换色)；鼠标移动 spawn 星尘/闪沙拖尾(粒子池+rAF)、点击径向爆裂闪光沙，全程 pointer-events:none 顶层不挡交互。③ 可调玻璃透明度（独家）：单一 CSS 变量 `--glass-alpha`(0~1.4)经 Zustand 滑块同时驱动 8+ 层 —— 背景色/blur/饱和度/边框/高光/色散/投影全联动，clamp 非零下限保证"任何时候都是液态玻璃、只调浓重度"，最低档真透出后方流星星空。视觉优先 + 主流机流畅（DPR≤2、粒子池封顶、合成层动画、流星可见封顶、Canvas 烘焙）。内置亮/暗双主题 + 7 主题色（默认暗色——夜空流星暗色最出彩；亮色流星=主题色偏灰 color-mix，绝不用白流星）。生成生产级、可直接运行的 TSX/CSS 代码，告别平庸毛玻璃。
---

# Ultra Design Pro — 流星夜空液态玻璃设计系统（纯 2D · 可调透明度 · 星尘交互）

## 概述

本 skill 用于构建**纯 2D、视觉优先**的高质感前端界面。界面是**浮在流星夜空前的液态玻璃面板群**：所有 UI 控件（导航、卡片、按钮、输入框、弹窗）用 DOM `backdrop-filter` 玻璃实现，带磨砂折射 + 菲涅尔高光 + 边缘色散 + 浮起投影；背景是暗色夜空里的偶发流星群 + 烘焙星空底，鼠标移动拖出星尘、点击迸发闪光沙。一切由用户滑块控制的「玻璃浓重度」统一调节，0~1.4 平滑联动所有层。

**首要准则：液态玻璃 + 流星夜空 + 可调透明度，三者缺一不可。** 玻璃要"液态"——不是普通毛玻璃，而是有折射质感（blur+saturate 磨砂、菲涅尔 sheen 高光、色散 rim 棱线、浮起投影）的半透面板；背景要"有生命"——夜空里偶发流星划过 + 静态星点垫底，绝不做死板的纯色底；透明度要"可控"——单一变量驱动 8+ 层，最低档透出星空、最高档浓重磨砂，中间全程平滑。

### 设计五原则

1. **液态玻璃（首要）**：UI 控件本身就是 DOM 玻璃（`.glass`/`.glass-strong`），带 blur+saturate 磨砂折射、菲涅尔顶部 sheen、边缘色散 rim、浮起投影。质感来自这四层叠加，不是单一 backdrop-filter。
2. **背景流星夜空**：后景是 DOM/CSS 流星群 + Canvas 烘焙星空底（都在 3D 场景之外，纯 2D 层）。流星 = 细长发光线段，linear-gradient 头亮尾隐拖尾 + box-shadow glow，沿自身轴线（旋转角 = 飞行方向）斜飞，同时可见 ≤6 条、偶发优雅、速度舒缓。
3. **可调玻璃透明度**：单一 CSS 变量 `--glass-alpha`(0~1.4) 驱动所有玻璃层。clamp 给每层非零下限——任何时候都是玻璃（非空气），只调浓重度。最低档透出后方流星，最高档浓重磨砂。
4. **星尘交互反馈**：鼠标移动 spawn 星尘/闪沙拖尾，点击径向爆裂闪光沙。粒子池 + 单 rAF loop，pointer-events:none 顶层不挡交互。
5. **视觉优先 + 流畅**：DPR≤2、粒子池封顶、流星可见封顶≤6、Canvas 星点烘焙不每帧重绘、全 transform/opacity 合成层动画。流畅靠工程手段而非删视觉。

---

## 技术栈约定

| 依赖 | 用途 | 必需 |
|------|------|------|
| TypeScript + React 18 + Next.js (App Router) | 工程基线，'use client' | ✅ 七项缺一不可 |
| Tailwind CSS | 原子类 + `@layer components` 玻璃类 | ✅ |
| shadcn/ui + Radix | 弹窗/下拉/Popover/Tooltip 等 | ✅ |
| Motion (motion/react + framer-motion) | 动效（useMotionValue/useSpring/AnimatePresence/motion.div） | ✅ |
| Zustand | 主题 + 玻璃透明度状态桥接 | ✅ |

> **铁律：纯 2D，绝不引入 Three.js / R3F / Drei / postprocessing / troika。** 液态玻璃、流星、星空、星尘全部用 DOM + CSS + Canvas 2D 实现。体积小、零 3D 依赖、主流机流畅。

---

## 双维度主题系统（明暗 × 7 色，默认暗色）

### CSS 变量（`src/app/globals.css`）

```css
@layer base {
  :root {
    /* accent base (mint default) */
    --accent: #00D4AA; --accent-rgb: 0, 212, 170; --accent-hsl: 168 100% 42%;
    --accent-soft: rgba(0, 212, 170, 0.14);

    /* night sky (background) */
    --sky-bg: #05070F; --sky-bg-2: #0B1426;
    --star-color: rgba(255, 255, 255, 0.72);
    /* 暗色流星 = 白核 + 蓝白辉光（夜空最出彩） */
    --meteor-color: #FFFFFF;
    --meteor-glow: rgba(186, 230, 253, 0.85);

    /* ---- glass tokens（详见下一章）---- */
    --glass-alpha: 1;
    /* ... --glass-bg / blur / saturate / border / sheen / rim ... */

    --text-primary: #F1F6FF; --text-secondary: #9AA7C7; --text-tertiary: #6B779A;
    --ease-liquid: cubic-bezier(0.22, 1, 0.36, 1);
    --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
    --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
  }

  [data-theme='light'] {
    --sky-bg: #E8EEF7; --sky-bg-2: #DCE5F3;
    /* 亮色流星 = 主题色偏灰（color-mix），绝不用白色 */
    --meteor-color: color-mix(in srgb, var(--accent) 45%, #8a8a99);
    --meteor-glow: color-mix(in srgb, var(--accent) 35%, transparent);
    /* ... 亮色玻璃 token（更透的白色基底） ... */
  }

  /* 7 accent palettes */
  [data-accent='mint']    { --accent:#00D4AA; --accent-rgb:0,212,170; ... }
  [data-accent='azure']   { --accent:#38BDF8; --accent-rgb:56,189,248; ... }
  [data-accent='rose']    { --accent:#FB7185; --accent-rgb:251,113,133; ... }
  [data-accent='violet']  { --accent:#A78BFA; --accent-rgb:167,139,250; ... }
  [data-accent='emerald'] { --accent:#34D399; --accent-rgb:52,211,153; ... }
  [data-accent='amber']   { --accent:#FBBF24; --accent-rgb:251,191,36; ... }
  [data-accent='indigo']  { --accent:#818CF8; --accent-rgb:129,140,248; ... }
}
```

### 主题桥接：Zustand store

```ts
// src/store/theme.ts
export const useThemeStore = create<ThemeState>((set, get) => ({
  mode: 'dark', accent: 'mint', glassAlpha: 1,
  setMode: (mode) => { set({mode}); document.documentElement.setAttribute('data-theme', mode); localStorage.setItem('aurora-theme', mode) },
  setAccent: (accent) => { set({accent}); document.documentElement.setAttribute('data-accent', accent); localStorage.setItem('aurora-accent', accent) },
  setGlassAlpha: (n) => { /* 见下章 */ },
  toggleMode: () => get().setMode(get().mode === 'dark' ? 'light' : 'dark'),
}))
```

> **关键**：主题/主题色/玻璃透明度全部经 Zustand → `document.documentElement.setAttribute` / `style.setProperty` → CSS 变量响应。换肤是 CSS 变量天然响应，无需 JS 重渲染。`<html>` 预置 inline 脚本在首帧前设好 `data-theme`/`data-accent`，防闪烁。

### 亮色流星铁律

> **亮色主题下流星绝不可能是白色**（白流星在浅色天空里刺眼又廉价）。必须 `color-mix(in srgb, var(--accent) 45%, #8a8a99)` —— 随主题色切换的"主题色偏灰"。拖尾渐变与头核都走 `--meteor-color` 变量，改一个变量整体换色。

---

## 液态玻璃材质原理（核心）

### 为什么不是普通毛玻璃

普通毛玻璃 = `backdrop-filter: blur()` 一层了事 → 平、死板、无质感。

**液态玻璃 = 四层叠加**：

| 层 | CSS | 作用 |
|----|-----|------|
| ① 磨砂折射 | `backdrop-filter: blur() saturate()` | 模糊 + 提饱和，玻璃磨砂感 |
| ② 半透底色 | `background: rgb(... / alpha)` | 玻璃本身的色调底，半透才见后方 |
| ③ 菲涅尔顶部高光 | `::before` sheen 渐变 | 顶部斜向白色光泽带，玻璃"亮"的灵魂 |
| ④ 边缘色散 | `::after` screen 混合渐变 | accent 染色 + 天蓝双色棱线，菲涅尔折射边缘 |
| ⑤ 浮起投影 | `box-shadow: inset白 + drop黑` | inset 白光 = 顶边高光；drop 黑 = 玻璃浮起 |

```css
@layer components {
  .glass {
    position: relative;
    background: var(--glass-bg);
    -webkit-backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
    backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
    border: 1px solid var(--glass-border);
    box-shadow: var(--shadow-inner-glass), var(--shadow-glass);
    overflow: hidden;
  }
  /* 边缘菲涅尔色散 = 液态玻璃折射灵魂：顶部高光带 + 对角 accent 色散 + 天蓝折射棱 */
  .glass::after {
    content: ''; position: absolute; inset: 0; border-radius: inherit;
    background:
      linear-gradient(180deg, rgba(255,255,255,0.14) 0%, transparent 28%),
      linear-gradient(115deg, transparent 0%, rgba(var(--accent-rgb),0.12) 26%, rgba(56,189,248,0.1) 52%, transparent 78%);
    mix-blend-mode: screen; pointer-events: none;
    opacity: var(--glass-rim-alpha);
  }
  /* 顶部 sheen = 玻璃表面镜面光泽带 */
  .glass::before {
    content: ''; position: absolute; inset: 0; border-radius: inherit;
    background: var(--glass-sheen); pointer-events: none;
    opacity: var(--glass-sheen-alpha);
  }
}
```

---

## 可调玻璃透明度（核心 · 独家机制）

### 铁律：单一变量驱动所有层，clamp 非零下限

> **关键认知**：液态玻璃的"通透" ≠ "让玻璃消失"。如果只降背景色 alpha，blur 仍 22px → 后方被糊成色块，看起来还是不透明。必须让 `--glass-alpha` **同时驱动 8+ 层**，且每层用 `clamp(下限, calc(下限 + 斜率 × α), 上限)` 保留**非零下限**——任何浓重度都是玻璃，只调浓淡。

| α（浓重度） | 含义 | 背景 alpha | blur | saturate | sheen | rim |
|-------------|------|-----------|------|----------|-------|-----|
| **0** | 最通透 | 0.05 | 3px | 1.1 | 0.35 | 0.45 |
| 0.5 | 通透液态 | 0.15 | 11.5px | 1.35 | — | — |
| **1**（默认） | 基准液态玻璃 | 0.25 | 20px | 1.6 | 0.62 | 0.78 |
| 1.4 | 最浓磨砂 | 0.33 | 27px | 1.8 | 0.85 | 1.0 |

### 完整 token（`globals.css` `:root`，暗色）

```css
--glass-alpha: 1;
/* 背景底色：α=0→0.03, α=1→0.18, α=1.4→0.28 */
--glass-bg: rgb(18 26 46 / clamp(0.03, calc(0.03 + 0.15 * var(--glass-alpha)), 0.28));
--glass-strong: rgb(22 32 56 / clamp(0.05, calc(0.05 + 0.2 * var(--glass-alpha)), 0.4));
--glass-soft: rgb(18 26 46 / clamp(0.02, calc(0.02 + 0.1 * var(--glass-alpha)), 0.2));
/* 磨砂 blur：α=0→2px(几乎不糊), α=1→16px, α=1.4→24px */
--glass-blur: clamp(2px, calc(2px + 14px * var(--glass-alpha)), 24px);
--glass-blur-strong: clamp(3px, calc(3px + 17px * var(--glass-alpha)), 28px);
/* 折射饱和度：α=0→1.1, α=1→1.6 */
--glass-saturate: clamp(1.1, calc(1.1 + 0.5 * var(--glass-alpha)), 1.85);
/* 边框：α=0→0.04, α=1→0.1 */
--glass-border: rgb(255 255 255 / clamp(0.04, calc(0.04 + 0.06 * var(--glass-alpha)), 0.18));
--glass-border-strong: rgb(255 255 255 / clamp(0.07, calc(0.07 + 0.09 * var(--glass-alpha)), 0.26));
/* 折射光泽层：α=0→0.35, α=1→0.62（保留液态光泽，不盖过透明） */
--glass-sheen-alpha: clamp(0.35, calc(0.35 + 0.27 * var(--glass-alpha)), 0.85);
/* 边缘色散：α=0→0.45, α=1→0.78（始终有折射棱线） */
--glass-rim-alpha: clamp(0.45, calc(0.45 + 0.33 * var(--glass-alpha)), 1);
/* 投影：α=0 时大幅减弱(0.08)，让最通透玻璃几乎不压暗 */
--shadow-glass: 0 1px 0 rgb(255 255 255 / clamp(0.06, calc(0.06 + 0.08 * var(--glass-alpha)), 0.22)) inset,
                0 18px 50px -12px rgb(0 0 0 / clamp(0.08, calc(0.08 + 0.25 * var(--glass-alpha)), 0.55));
```

### Store + 滑块

```ts
// src/store/theme.ts
export const GLASS_ALPHA_MIN = 0, GLASS_ALPHA_MAX = 1.4, GLASS_ALPHA_DEFAULT = 1;

setGlassAlpha: (n) => {
  const clamped = Math.min(GLASS_ALPHA_MAX, Math.max(GLASS_ALPHA_MIN, n));
  set({ glassAlpha: clamped });
  document.documentElement.style.setProperty('--glass-alpha', String(clamped)); // 关键：直接设 CSS 变量
  localStorage.setItem('aurora-glass-alpha', String(clamped));
},
// hydrate 时从 localStorage 还原 + applyGlassAlpha
```

```tsx
// src/components/dashboard/Topbar.tsx —— 仿 accent picker 的 Popover
<Popover.Content className="glass-strong ...">
  <span>玻璃透明度 {Math.round(glassAlpha * 100)}%</span>
  <input type="range" min={0} max={1.4} step={0.05} value={glassAlpha}
    onChange={(e) => setGlassAlpha(parseFloat(e.target.value))} className="glass-range" />
  <button onClick={() => setGlassAlpha(1)}>重置</button>
</Popover.Content>
```

### 调参心法（踩过的坑）

> **❌ 错 1：只乘背景色 alpha**（`calc(0.42 * α)`）→ α=0 时 blur 仍 22px，后方糊成色块，"还是不透明"。
> **❌ 错 2：所有层都 ×α 且下限归零** → α=0 玻璃完全消失（变空气，不是玻璃），且滑条稍微一拉就极差。
> **❌ 错 3：下限设太高**（背景 0.06、blur 6px）→ 默认档背景到 0.47，是磨砂板不是玻璃，"默认不够通透"。
> **✅ 对**：每层 `clamp(非零下限, calc(下限 + 斜率×α), 上限)`。背景/blur 下限低（α=0 时背景 0.05、blur 3px → 真透出星空），折射层(saturate/sheen/rim)下限适中（始终保留液态光泽）。α=1 默认档背景 0.25 + blur 20px = 真液态玻璃。

### 可调透明度自检子清单

- [ ] `--glass-alpha` 同时驱动背景色/blur/saturate/边框/sheen/rim/投影 共 8+ 层？
- [ ] 每层用 `clamp(非零下限, ...)`，α=0 时仍是玻璃（非消失）？
- [ ] α=1 默认档背景 alpha ≤ 0.25、blur ≤ 20px（不是磨砂板）？
- [ ] α=0 最低档 blur ≤ 5px（真透出后方流星星空）？
- [ ] 折射层(sheen/rim/saturate)下限 ≥ 0.3 / 1.1（始终有液态光泽）？
- [ ] 滑块经 store → `documentElement.style.setProperty('--glass-alpha', n)`，持久化到 localStorage？
- [ ] 滑块范围 0~1.4、step 0.05、显示百分比、有重置？

---

## 背景 2D 流星夜空（核心 · 退让不抢戏）

### 层叠架构（DOM 顺序）

```
body（夜空渐变兜底）
 └─ StarfieldBackground  (z:0, Canvas 烘焙静态星点)
 └─ MeteorLayer          (z:1, DOM/CSS 流星群)
 └─ [前景玻璃 UI]        (z:10+, .glass/.glass-strong)
```

### Canvas 静态星空底（烘焙不每帧重绘）

```tsx
// src/components/background/StarfieldBackground.tsx
// 280 个静态星点，useEffect 里一次性 ctx.fillRect 画到 offscreen canvas，
// 仅在 resize 时重绘。绝不每帧重绘（性能铁律）。
```

### DOM 流星群（沿自身轴线飞行）

> **关键认知 · 流星飞行方向**：流星 = 头亮尾隐的光迹，**拖尾方向 = 飞行方向的反向**。必须让 `--angle` 同时驱动【线的 rotate】和【translateX 方向】，保证头核永远在前、拖尾甩在身后。`transform: rotate(var(--angle)) translateX(var(--distance))` —— translateX 在 rotate 之后的局部坐标系内沿轴线移动。**绝不能 `translate(vw, vh)`，那会让旋转与位移脱节，变成横线平移。**

```tsx
// src/components/background/MeteorLayer.tsx
function buildMeteors(count) {
  return Array.from({ length: count }, (_, i) => ({
    angle: 20 + Math.random() * 20,        // 朝右下斜飞 20°~40°
    duration: 3.2 + Math.random() * 2.4,   // 3.2~5.6s 舒缓（不要太快）
    delay: i * (3.6 + Math.random() * 2.4) + Math.random() * 2.5,
    length: 120 + Math.random() * 120,     // 120~240px 拖尾
    distance: 55 + Math.random() * 20,     // 飞行距离
    tinted: Math.random() < 0.2,           // 20% 主题色染色
  }))
}
// <span className="meteor" style={{ '--angle': `${m.angle}deg`, '--distance': `${m.distance}vw`, animation: `meteor-fly ${m.duration}s linear ${m.delay}s infinite` }}>
//   <span className="meteor-head" />  {/* 头核 */}
// </span>
```

```css
.meteor {
  background-image: linear-gradient(to right,
    transparent 0%,
    color-mix(in srgb, var(--meteor-color) 5%, transparent) 30%,
    color-mix(in srgb, var(--meteor-color) 35%, transparent) 70%,
    color-mix(in srgb, var(--meteor-color) 95%, transparent) 96%,
    var(--meteor-color) 100%);
  box-shadow: 0 0 6px 1px var(--meteor-glow), 0 0 14px 3px color-mix(in srgb, var(--meteor-glow) 55%, transparent);
}
.meteor-head { /* 头核：右端 4px 亮点 + 辉光 */ }

@keyframes meteor-fly {
  0%   { transform: rotate(var(--angle)) translateX(0);    opacity: 0; }
  8%   { opacity: 1; }
  62%  { opacity: 1; }
  100% { transform: rotate(var(--angle)) translateX(var(--distance)); opacity: 0; }
}
```

### 响应式流星密度

| 屏幕 | 流星数 |
|------|--------|
| 桌面 ≥1280 | ≤6 |
| 平板 768–1279 | ≤4 |
| 移动 <768 | ≤3（省电 + 不抢小屏前景） |
| prefers-reduced-motion | 2 |

### 流星自检子清单

- [ ] `transform: rotate(--angle) translateX(--distance)`（旋转与位移同变量，沿轴线飞）？
- [ ] 拖尾渐变 = `linear-gradient(to right, transparent → meteor-color)`（头亮尾隐）？
- [ ] 头核在右端（飞行方向前方）、带 glow？
- [ ] 同时可见 ≤6、delay 错开、duration 3~6s 舒缓？
- [ ] 颜色走 `--meteor-color` 变量，亮色主题是主题色偏灰（非白）？
- [ ] Canvas 星点烘焙不每帧重绘？

### 错 vs 对（流星方向）

```
❌ 错：translate(60vw, 40vh) rotate(angle)
[====>]      ← 旋转和位移脱节，横线平移，像扫描线

✅ 对：rotate(angle) translateX(distance)
   ╲
    ╲[====>]  ← 沿自身轴线飞，头核在前、拖尾在后，真流星
```

---

## 星尘/闪沙交互（核心 · 拖尾 + 点击爆裂）

### 拖尾：鼠标移动 spawn 星尘

监听 `pointermove`，每次采样 spawn 几颗"星尘"粒子：向四周散开 + 轻微上浮 + 淡出缩小。多颗串成拖尾。

### 点击：径向爆裂闪光沙

监听 `pointerdown`，在点击点从中心向 360° 飞溅 22 颗闪光沙。

### 实现铁律

- **单 rAF loop 批量更新**所有粒子（非每粒子一个 timer）
- **粒子池复用**：预建 220 颗 span，淡出回收，避免频繁增删 DOM
- `pointer-events: none` + `z-index: 9999`（顶层盖所有 UI，但不挡交互）
- 尊重 `prefers-reduced-motion`（不挂监听）
- 双色点缀：75% `--meteor-color` + 25% `--accent`；30~50% 是闪光沙（更亮更小更快淡）

```tsx
// src/components/background/CursorMeteor.tsx（粒子结构）
interface Particle {
  el: HTMLSpanElement; x, y, vx, vy: number;
  life: number; decay: number; size: number;
  hue: number;  // 0=meteor色, 1=accent色
  isSpark: boolean;  // 闪光沙：更亮更小
}
// 拖尾：沿移动方向向后散开 + 随机 + 轻微上浮(vy -= 0.15)
// 点击：i/BURST * 2π 径向飞溅，初速度更大(1.4~4)
// tick: p.vx *= 0.93 阻尼; p.vy -= 0.02 持续上浮; life -= decay; opacity/scale 随 life
```

### 星尘自检子清单

- [ ] 拖尾：`pointermove` spawn 3 颗，向四周散开 + 上浮？
- [ ] 点击：`pointerdown` 径向爆裂 22 颗？
- [ ] 单 rAF + 粒子池(220) 复用，无频繁增删 DOM？
- [ ] `pointer-events:none` + `z-index:9999`（顶层不挡交互）？
- [ ] 双色(--meteor-color / --accent) + 闪光沙点缀？
- [ ] prefers-reduced-motion 不挂监听？

---

## Motion 动效体系

全 DOM Motion（motion/react + framer-motion），无 3D。

| 维度 | 实现 |
|------|------|
| 入场 | `motion.div` initial/animate + stagger（卡片错落浮入） |
| 状态 | 点光晕/光圈 `animate-pulse-ring`、异常卡呼吸 |
| 交互 | hover 浮起(`whileHover={{ y: -4 }}`)、按钮按下 scale |
| 过渡 | 主题/色切换材质 lerp 感（CSS 变量天然过渡 + transition） |
| 弹窗 | Radix Popover/Dropdown + `AnimatePresence` |

```css
/* 全局 transition 走 ease 变量 */
transition: transform 0.3s var(--ease-spring), opacity 0.3s var(--ease-liquid);
```

---

## 性能（视觉优先下的工程优化）

- DPR 封顶 `[1, 2]`（Canvas/截图）
- 粒子池封顶 220，死粒子回收
- 流星同时可见 ≤6，Canvas 星点烘焙不每帧重绘
- 全 transform/opacity 合成层动画
- `prefers-reduced-motion` 全局降级

---

## 响应式

- 断点：桌面 ≥1280 / 平板 768–1279 / 移动 <768
- 流星密度三档(≤6/≤4/≤3)
- 玻璃 blur 在小屏略减（移动端 backdrop-filter 开销）

---

## 执行流程

1. 搭 `globals.css` 双维度 token（明暗 × 7 色 + 玻璃 alpha token + 流星/星空 token）
2. 写 `.glass` / `.glass-strong` 组件类（四层叠加 + alpha 联动）
3. 写 Zustand store（mode/accent/glassAlpha + setGlassAlpha 设 CSS 变量 + 持久化）
4. 搭背景层：StarfieldBackground(Canvas) + MeteorLayer(DOM) + CursorMeteor(星尘)
5. 搭前景 UI（Sidebar/Topbar/KpiCard/图表/表格），全用 `.glass`/`.glass-strong`
6. Topbar 加三个 Popover：明暗切换 / 主题色 / 玻璃透明度滑块
7. 8 模块全落地 + 真实数据
8. `next build` + `next start` 验证 HTTP 200、0 console error、滑块/流星/星尘交互正常

---

## 自检清单

**液态玻璃**
- [ ] `.glass` = backdrop-filter blur+saturate + 半透底 + `::before` sheen + `::after` rim + 浮起投影（四层叠加）？
- [ ] 绝无 Three.js/R3F/Drei/postprocessing/troika 依赖？

**可调透明度**
- [ ] `--glass-alpha` 驱动 8+ 层，每层 clamp 非零下限？
- [ ] α=1 默认背景 ≤0.25、blur ≤20px；α=0 时 blur ≤5px 真透出星空？
- [ ] 滑块经 store 设 CSS 变量 + localStorage 持久化？

**背景流星夜空**
- [ ] Canvas 烘焙星空 + DOM 流星群沿自身轴线飞(rotate+translateX 同变量)？
- [ ] 流星 ≤6 偶发、3~6s 舒缓、亮色主题色偏灰(非白)？

**星尘交互**
- [ ] 移动拖尾 + 点击爆裂，单 rAF + 粒子池复用，顶层 pointer-events:none？

**主题系统**
- [ ] 双维度(明暗×7色)，Zustand → data-* 属性 → CSS 变量，默认暗色？

---

## 设计准则

1. **液态玻璃优先**：UI 控件全是 `.glass`，四层叠加出质感，非单一 blur。
2. **一强多弱**：主面板用 `.glass`，弹窗/强调用 `.glass-strong`，其余弱化。
3. **强调色点睛**：accent 只用在 CTA/选中态/数据高亮，不大面积铺。
4. **颜色走变量/store**：绝不在组件里写死色值，全读 CSS 变量或 Zustand。
5. **视觉优先 + 流畅**：不删视觉换流畅，靠工程封顶。
6. **绝不白屏**：CSS 变量兜底、无外部资源依赖（无字体/模型外链）。

---

## 常见误区

| 误区 | 正确做法 |
|------|----------|
| 普通 `backdrop-filter: blur()` 一层了事 | 四层叠加（blur+saturate + sheen + rim + 投影）才是液态玻璃 |
| 只降背景 alpha 调透明度 | `--glass-alpha` 同时驱动 8+ 层，且 clamp 非零下限 |
| 透明度下限归零(α=0 玻璃消失) | clamp 非零下限——任何时候都是玻璃，只调浓重度 |
| 透明度下限太高(默认像磨砂板) | α=1 背景应 ≤0.25、blur ≤20px |
| `translate(vw,vh)` 移动流星 | `rotate(angle) translateX(distance)` 沿自身轴线飞 |
| 流星速度太快(1~2s) | 舒缓 3~6s，偶发优雅 |
| 亮色主题用白流星 | `color-mix(accent 45%, 灰)` 主题色偏灰 |
| 每帧重绘 Canvas 星点 | 烘焙到 offscreen，仅 resize 重绘 |
| 每粒子一个 timer | 单 rAF loop + 粒子池复用 |
| 引入 Three.js 做玻璃/流星 | 纯 2D DOM+CSS+Canvas，零 3D 依赖 |
| 星尘拖尾挡交互 | `pointer-events:none` + 顶层 z-index |
```
