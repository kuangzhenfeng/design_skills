---
name: ultra-design
description: 当需要构建高质量、极具设计感的前端界面（网页、落地页、Dashboard、组件、可视化工具、应用 UI 等）时使用。输出 **Liquid Glass（液态玻璃）** 设计语言 — 半透明玻璃材质、动态边缘高光、镜面反射、流动氛围背景。**承诺一套完整的 Motion 动效体系**：加载（转圈/骨架流光/进度流光/按钮 loading/数字滚动）、状态（心跳脉冲/呼吸/在线光晕/连接流光）、入场（错落浮现/模糊聚焦/缩放弹出/翻转）、滚动（滚动显现/视差/进度条）、指针（磁吸/3D 倾斜/涟漪/光泽跟随）、过渡（FLIP/layoutId 滑动/折叠展开/AnimatePresence/圆形揭露换肤）、灵动点缀（边缘流光/光泽划过/脉冲环/悬停浮起/抖动/光标闪烁/图标弹跳/下划线生长/徽章弹现/光晕脉冲）。默认技术栈 TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion，输出生产级、可直接运行的 TSX 代码。视觉质感优先：玻璃质感、氛围流动、阴影过渡一律完整保留，只保留温和的合成层性能建议，不为性能删减任何视觉与动效。默认亮色 + 薄荷绿（#00D4AA）主题，内置亮/暗双主题切换与多主题色切换（两个维度正交、任意组合）。告别通用 AI 审美。
---

# Ultra Design — Liquid Glass 设计系统（Motion 驱动）

## 概述

Ultra Design 是一套基于 **Liquid Glass（液态玻璃）** 设计语言的前端规范，技术栈默认 **TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion**。

核心理念：**界面如同真实的液态玻璃 — 透、亮、有重量、有动感**。动效是必需品而非装饰：加载有转圈、状态有心跳、入场有浮现、滚动有视差、指针有跟随、过渡有流动、交互有灵动反馈。**全部动效通过 Motion 实现，一个都不为"性能"删掉** —— 流畅靠「让动效走合成层（transform/opacity/filter）」与「滚动/指针监听节流」这类温和手段保证，而非牺牲视觉质感。

### 设计三原则

1. **材质真实**：玻璃必须真的"透"——能看见背景的色彩流动穿过表面；边缘有镜面高光，悬停时浮起、光泽划过、阴影平滑加深。按钮、输入框、卡片同等享有玻璃质感，**不为了性能给细碎元素降级**。
2. **动效是生命力**：七大维度（加载/状态/入场/滚动/指针/过渡/灵动）一律完整实现。流畅不靠删减动效，而靠合成层属性 + Motion 的高性能调度。
3. **视觉质感优先**：氛围色斑可以缓慢流动、hover 阴影可以平滑过渡、所有玻璃元素都可以有 `backdrop-filter`——这些是"看起来高级"的来源，**一律保留**。性能只做温和优化（优先合成层、节流监听、合理 `will-change`），不设牺牲视觉的红线。

---

## 技术栈约定

- **Next.js 14+（App Router）**：动效组件用 `'use client'` 标注；主题变量放在 `globals.css`。
- **Tailwind CSS v3/v4**：用 CSS 变量承载 token，通过 `theme.extend` 或 `@theme` 暴露为工具类（`bg-glass-tint`、`text-primary` 等）。组件里**只引用语义 token**，不写死色值。
- **shadcn/ui + Radix**：交互基座与无障碍（`Dialog`/`DropdownMenu`/`Tabs`/`Tooltip`/`Popover` 等自带焦点管理、键盘可达、ARIA）。在 shadcn 组件基础上叠加玻璃材质 + Motion 动效，不重复造轮子。
- **Motion（`motion/react`，原 Framer Motion）**：全部动效的唯一实现。入场用 `whileInView`、滚动用 `useScroll`/`useTransform`、过渡用 `AnimatePresence` + `layout`/`layoutId`、数字用 `useMotionValue` + `animate`、指针跟随用 `useMotionValue` + `useSpring`。
- **lucide-react**：统一图标体系，不混用多套图标库、不用 emoji 做结构性图标。

---

## Liquid Glass 材质原理

为什么看起来像玻璃？三要素缺一不可：

| 要素 | 作用 | 实现 |
|------|------|------|
| **透射（Transmission）** | 背景色彩穿过玻璃，被柔化、提亮饱和度 | `backdrop-filter: blur() saturate()` |
| **边缘高光（Specular Edge）** | 玻璃边缘折射聚集的光线，形成发光边 | `inset` 白色阴影 + 半透明白色描边 + 伪元素镜面高光 |
| **镜面反射（Reflection）** | 顶面反射环境光，形成立体厚度感 | 顶部内高光 + 渐变背景 + 多层投影 |

**关键认知**：没有背景的玻璃等于没有玻璃。Liquid Glass 必须放在有色彩流动、有明暗变化的背景之上。因此**背景氛围层**与**玻璃层**永远成对出现。

---

## 双维度主题系统

主题由**两个正交维度**组合决定，互不耦合：

| 维度 | 属性 | 取值 | 决定 |
|------|------|------|------|
| **明暗** | `data-theme` | `light`（默认）/ `dark` | 底色、玻璃 tint、文本、高光强度、模糊度、投影 |
| **主题色** | `data-accent` | `mint`（默认，#00D4AA）/ `azure` / `rose` / `violet` / `emerald` / `amber` / `indigo` | accent 强调色 + 4 个氛围色斑（整体换肤） |

两个属性都挂在 `<html>` 上，如 `<html data-theme="dark" data-accent="rose">` = 夜晚的玫瑰玻璃。

### 架构：三段式变量分离

1. **明暗基础层**（`data-theme`）：底色、玻璃透明度、文本、高光、模糊、投影。它**只做引用切换**：`--accent: var(--accent-l)`（亮）或 `var(--accent-d)`（暗）。
2. **色相层**（`data-accent`）：每个主题色提供一组**明/暗变体** + RGB 三通道 `--accent-rgb`。
3. **组件层**：只引用最终变量 `--accent`、`--aura-N`、`--accent-rgb`，对切换无感。

`--accent-rgb` 把强调色拆成三通道（如 `0, 212, 170`），让派生色用 `rgba(var(--accent-rgb), 0.35)` 动态生成——切主题色时投影、聚焦环、软填充也整体换肤。

---

## CSS 变量定义（写入 `globals.css`）

```css
/* ============ 明暗基础层：只做引用切换 ============ */
:root,
:root[data-theme="light"] {
  --glass-bg-base: #F5F5F7;
  --glass-tint: rgba(255, 255, 255, 0.55);
  --glass-tint-strong: rgba(255, 255, 255, 0.72);
  --glass-tint-soft: rgba(255, 255, 255, 0.35);
  --glass-highlight: rgba(255, 255, 255, 0.85);
  --glass-border: rgba(255, 255, 255, 0.6);
  --glass-blur: 24px;
  --glass-saturate: 180%;

  --text-primary: #1D1D1F;
  --text-secondary: #515154;
  --text-tertiary: rgba(60, 60, 67, 0.6);
  --text-on-glass: #1D1D1F;

  --shadow-glass:
    0 1px 1px rgba(255, 255, 255, 0.9) inset,
    0 -1px 1px rgba(255, 255, 255, 0.35) inset,
    0 12px 40px rgba(0, 0, 0, 0.08),
    0 4px 12px rgba(0, 0, 0, 0.04);

  --accent: var(--accent-l);
  --accent-rgb: var(--accent-l-rgb);
  --aura-1: var(--aura-1-l);
  --aura-2: var(--aura-2-l);
  --aura-3: var(--aura-3-l);
  --aura-4: var(--aura-4-l);
}

:root[data-theme="dark"] {
  --glass-bg-base: #0A0A0C;
  --glass-tint: rgba(40, 40, 48, 0.55);
  --glass-tint-strong: rgba(48, 48, 56, 0.72);
  --glass-tint-soft: rgba(30, 30, 36, 0.35);
  --glass-highlight: rgba(255, 255, 255, 0.18);
  --glass-border: rgba(255, 255, 255, 0.12);
  --glass-blur: 28px;
  --glass-saturate: 160%;

  --text-primary: #F5F5F7;
  --text-secondary: rgba(235, 235, 245, 0.7);
  --text-tertiary: rgba(235, 235, 245, 0.4);
  --text-on-glass: #F5F5F7;

  --shadow-glass:
    0 1px 1px rgba(255, 255, 255, 0.12) inset,
    0 -1px 1px rgba(0, 0, 0, 0.4) inset,
    0 16px 50px rgba(0, 0, 0, 0.55),
    0 4px 14px rgba(0, 0, 0, 0.35);

  --accent: var(--accent-d);
  --accent-rgb: var(--accent-d-rgb);
  --aura-1: var(--aura-1-d);
  --aura-2: var(--aura-2-d);
  --aura-3: var(--aura-3-d);
  --aura-4: var(--aura-4-d);
}

/* 派生色：基于 RGB 通道，随主题色整体换肤 */
:root {
  --accent-soft: rgba(var(--accent-rgb), 0.12);
  --accent-glow: rgba(var(--accent-rgb), 0.35);
}

/* ============ 色相层：每个主题色提供明/暗变体 ============ */
:root,
:root[data-accent="mint"] {
  --accent-l: #00D4AA; --accent-l-rgb: 0, 212, 170;
  --accent-d: #2EE8C0; --accent-d-rgb: 46, 232, 192;
  --aura-1-l: #B5F0E0; --aura-1-d: #2D6B5A;
  --aura-2-l: #A0E8E0; --aura-2-d: #2D6B6B;
  --aura-3-l: #C8F5E8; --aura-3-d: #3D6B5A;
  --aura-4-l: #9FE8C8; --aura-4-d: #2D5A4E;
}
:root[data-accent="azure"] {
  --accent-l: #0071E3; --accent-l-rgb: 0, 113, 227;
  --accent-d: #0A84FF; --accent-d-rgb: 10, 132, 255;
  --aura-1-l: #FFB7C5; --aura-1-d: #6B4F8E;
  --aura-2-l: #A0D8EF; --aura-2-d: #2E5A88;
  --aura-3-l: #C8A2E6; --aura-3-d: #884A6B;
  --aura-4-l: #B5EAD7; --aura-4-d: #2F6E5A;
}
:root[data-accent="rose"] {
  --accent-l: #FF2D55; --accent-l-rgb: 255, 45, 85;
  --accent-d: #FF375F; --accent-d-rgb: 255, 55, 95;
  --aura-1-l: #FFD1DC; --aura-1-d: #8E2D4A;
  --aura-2-l: #FFCBA4; --aura-2-d: #8E4A3D;
  --aura-3-l: #F7C8C0; --aura-3-d: #7A3D5A;
  --aura-4-l: #E6C5E8; --aura-4-d: #6B3D6B;
}
:root[data-accent="violet"] {
  --accent-l: #AF52DE; --accent-l-rgb: 175, 82, 222;
  --accent-d: #BF5AF2; --accent-d-rgb: 191, 90, 242;
  --aura-1-l: #D9C2F0; --aura-1-d: #5A3D8E;
  --aura-2-l: #E0B0FF; --aura-2-d: #4A3D8E;
  --aura-3-l: #B4C5F0; --aura-3-d: #3D4A8E;
  --aura-4-l: #F0C2D9; --aura-4-d: #5A3D7A;
}
:root[data-accent="emerald"] {
  --accent-l: #34C759; --accent-l-rgb: 52, 199, 89;
  --accent-d: #30D158; --accent-d-rgb: 48, 209, 88;
  --aura-1-l: #B5EAD7; --aura-1-d: #2D6B4A;
  --aura-2-l: #C5E8B5; --aura-2-d: #2D6B6B;
  --aura-3-l: #B5DCDC; --aura-3-d: #3D6B3D;
  --aura-4-l: #DCEAB5; --aura-4-d: #2D5A5A;
}
:root[data-accent="amber"] {
  --accent-l: #FF9500; --accent-l-rgb: 255, 149, 0;
  --accent-d: #FF9F0A; --accent-d-rgb: 255, 159, 10;
  --aura-1-l: #FFD9A0; --aura-1-d: #8E5A2D;
  --aura-2-l: #FFB88C; --aura-2-d: #8E6A3D;
  --aura-3-l: #FFE8A0; --aura-3-d: #7A5A2D;
  --aura-4-l: #FFCBA4; --aura-4-d: #7A4A3D;
}
:root[data-accent="indigo"] {
  --accent-l: #5856D6; --accent-l-rgb: 88, 86, 214;
  --accent-d: #5E5CE6; --accent-d-rgb: 94, 92, 230;
  --aura-1-l: #C5C2F0; --aura-1-d: #3D3D8E;
  --aura-2-l: #A0C5EF; --aura-2-d: #4A3D8E;
  --aura-3-l: #D9C2F0; --aura-3-d: #2E3D8E;
  --aura-4-l: #B5DCEF; --aura-4-d: #3D3D7A;
}

/* 主题切换：过渡挂 :root 即可（一次性离散操作），柔和流动 */
:root { transition: background-color 0.5s ease, color 0.5s ease; }
```

> 默认根节点 `<html data-theme="light" data-accent="mint">`。暗色不是反转，而是"夜晚的玻璃"——高光含蓄、背景深邃、强调色更亮。主题色不是换皮，而是换"玻璃透出的色族"。

---

## 字体系统

现代通用字体栈，跨平台一致、几何感强、数字锐利。在 `layout.tsx` 用 `next/font` 加载（推荐 `Geist`、`Inter` 等现代字体），或直接用系统栈兜底：

```css
:root {
  --font-display: 'Geist', 'Inter', system-ui, -apple-system, 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  --font-text: 'Geist', 'Inter', system-ui, -apple-system, 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  --font-mono: 'JetBrains Mono', 'Geist Mono', ui-monospace, 'SF Mono', Menlo, monospace;
}
```

| 用途 | 字号 | 字重 | 备注 |
|------|------|------|------|
| 巨型展示标题 | 56–80px | 700 | `letter-spacing: -0.02em` 收紧 |
| 页面大标题 | 32–44px | 600 | `letter-spacing: -0.01em` |
| 正文 | 16–17px | 400 | 行高 1.47 |
| 辅助说明 | 13–14px | 400 | `--text-tertiary` |
| 数字/指标 | 28–48px | 600 | `font-variant-numeric: tabular-nums`（数字滚动/刷新不抖） |

推荐用 `next/font` 加载 **Geist** 或 **Inter** 作为主字体（几何无衬线、可变字重、现代感强），中文回退到系统中文字体（PingFang SC / Microsoft YaHei）；数据与代码用 **JetBrains Mono**。

---

## 玻璃材质配方（核心）

**任何玻璃元素都包含：透射模糊 + 边缘高光 + 镜面投影** 三件套。按钮、输入框、卡片同等享有完整玻璃质感——这是高级感的来源，不要为了性能给任何元素降级。

```css
.glass {
  background: var(--glass-tint);
  backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
  -webkit-backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
  border: 1px solid var(--glass-border);
  box-shadow: var(--shadow-glass);
  border-radius: 24px;
}
.glass-strong {
  background: var(--glass-tint-strong);
  backdrop-filter: blur(36px) saturate(200%);
  -webkit-backdrop-filter: blur(36px) saturate(200%);
  box-shadow: var(--shadow-glass), 0 1px 0 rgba(255,255,255,0.5) inset;
}
.glass-soft {
  background: var(--glass-tint-soft);
  backdrop-filter: blur(16px) saturate(140%);
  -webkit-backdrop-filter: blur(16px) saturate(140%);
  border: 1px solid var(--glass-border);
}
@supports not (backdrop-filter: blur(1px)) {
  .glass, .glass-strong { background: var(--glass-tint-strong); }
}

/* 顶部镜面高光（specular sheen）：区分"真玻璃"与"毛玻璃"的关键 */
.glass-sheen { position: relative; }
.glass-sheen::before {
  content: '';
  position: absolute; inset: 0;
  border-radius: inherit; padding: 1px;
  background: linear-gradient(180deg, var(--glass-highlight) 0%, transparent 40%);
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor; mask-composite: exclude;
  pointer-events: none;
}
```

---

## 背景氛围层（玻璃的舞台，缓慢流动）

玻璃无背景则无效。每个页面先铺一层**流动的 mesh 色斑**，玻璃才显形。色斑取自 `--aura-1~4`，切换主题色时整体换肤。**氛围色斑允许缓慢漂移**——这是"活"的来源，缓慢周期（20s+）的 transform 位移是合成层安全且视觉极有价值，不要砍掉。

```css
.aura { position: fixed; inset: 0; z-index: 0; overflow: hidden; pointer-events: none; }
.aura::before, .aura::after, .aura .blob {
  content: ''; position: absolute;
  width: 60vmax; height: 60vmax; border-radius: 50%;
  filter: blur(80px); opacity: 0.55;
}
/* 每个色斑用不同的缓慢漂移 keyframes，transform 走合成层，可常驻 */
.aura::before {
  background: radial-gradient(circle, var(--aura-1), transparent 70%);
  top: -20%; left: -10%;
  animation: drift-a 24s var(--ease-smooth, ease-in-out) infinite alternate;
}
.aura::after {
  background: radial-gradient(circle, var(--aura-2), transparent 70%);
  bottom: -25%; right: -15%;
  animation: drift-b 28s var(--ease-smooth, ease-in-out) infinite alternate;
}
/* blob-3 / blob-4 同理，分别用 --aura-3 / --aura-4，给不同方向与周期 */
@keyframes drift-a { to { transform: translate(6vmax, 4vmax) scale(1.1); } }
@keyframes drift-b { to { transform: translate(-5vmax, -3vmax) scale(1.05); } }
```

```tsx
{/* 内容浮在 aura 之上 */}
<div className="aura"><div className="blob" /><div className="blob" /></div>
<main className="relative z-10">{/* 玻璃卡片自然透出底层流动色斑 */}</main>
```

> 温和建议：色斑数量 3–4 个、周期 ≥ 20s、只动 `transform`/`opacity` 即可常驻不掉帧。如需进一步降负，可对色斑层加 `will-change: transform`。

---

## 缓动曲线库（Motion 与 CSS 共用）

告别 `linear`/`ease`/`ease-in-out`。每类动效匹配对应物理曲线。在 Motion 里用数组形式 `[0.16, 1, 0.3, 1]`，在 CSS 里用 `cubic-bezier()`：

```css
:root {
  --ease-liquid:   cubic-bezier(0.2, 0.8, 0.2, 1);  /* 默认液态位移 */
  --ease-smooth:   cubic-bezier(0.4, 0, 0.2, 1);    /* 颜色/透明度过渡 */
  --ease-spring:   cubic-bezier(0.34, 1.56, 0.64, 1); /* 回弹（按钮/浮现/磁吸归位） */
  --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);   /* 入场"落定" */
  --ease-snap:     cubic-bezier(0.4, 0, 0.2, 1);    /* 开关/tab 吸附 */
  --ease-theme:    cubic-bezier(0.65, 0, 0.35, 1);  /* 主题换肤柔和流动 */
}
```

| 曲线 | 适用 |
|------|------|
| `--ease-liquid` | 默认位移、缩放基础态 |
| `--ease-spring` | 回弹交互（按钮、卡片浮现、磁吸归位） |
| `--ease-out-expo` | 入场落定（从模糊/远处急停） |
| `--ease-smooth` | 颜色、透明度等无位移过渡 |
| `--ease-theme` | 主题色/明暗换肤 |

---

## Motion 动效系统（七大维度，全部实现）

> **铁律**：所有动效作用于 `transform` / `opacity` / `filter`（合成层）。`width`/`height`/`top`/`left`/`margin` 不入动画；阴影变化可用 Motion 平滑过渡（Motion 自动优化为合成层，少量元素完全没问题）。pointermove/scroll 驱动的跟随一律用 `useMotionValue` + `useSpring`，Motion 内部已 rAF 节流，无需手写。

### ① 入场：错落浮现 + 模糊聚焦

首屏是高光时刻，**错落入场 + 模糊聚焦 + 多轴运动**，而非整齐淡入。用 `whileInView` + `viewport={{ once: true }}`。

```tsx
'use client'
import { motion, type Variants } from 'motion/react'

// 容器编排子项错落；--i 由父级 stagger 控制
const container: Variants = {
  hidden: {},
  show: { transition: { staggerChildren: 0.08, delayChildren: 0.1 } },
}
const rise: Variants = {
  hidden: { opacity: 0, y: 24, scale: 0.98, filter: 'blur(8px)' },
  show: { opacity: 1, y: 0, scale: 1, filter: 'blur(0)',
    transition: { duration: 0.8, ease: [0.16, 1, 0.3, 1] } },
}

export function Hero() {
  return (
    <motion.div variants={container} initial="hidden" whileInView="show" viewport={{ once: true }}>
      <motion.h1 variants={rise} className="text-6xl font-bold tracking-tight">主标题（焦点先行）</motion.h1>
      <motion.p variants={rise} className="text-text-secondary">副内容逐级延后跟上</motion.p>
    </motion.div>
  )
}
```

变体：`popIn`（`scale: 0.85→1`，回弹）、`slideInLeft`（`x: -40→0`）、`blurIn`（`filter: blur(20px)→0` + `scale: 1.05→1`）。首屏主标题最先入场（`delayChildren` 小），形成"标题落下 → 内容跟上"的焦点引导。

### ② 滚动：显现 + 视差 + 进度条

```tsx
'use client'
import { motion, useScroll, useTransform, useSpring } from 'motion/react'

// (1) 滚动显现：进入视口才触发
export function Reveal({ children }: { children: React.ReactNode }) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 40 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: '0px 0px -10% 0px' }}
      transition={{ duration: 0.8, ease: [0.16, 1, 0.3, 1] }}
    >{children}</motion.div>
  )
}

// (2) 视差：背景/装饰层随滚动以不同速率移动
export function Parallax() {
  const { scrollY } = useScroll()
  const y = useTransform(scrollY, [0, 600], [0, 120]) // 0→120px，柔顺
  return <motion.div style={{ y }} className="aura" />
}

// (3) 滚动进度条：顶部强调色细线随滚动填充
export function ScrollProgress() {
  const { scrollYProgress } = useScroll()
  const scaleX = useSpring(scrollYProgress, { stiffness: 120, damping: 30 })
  return (
    <motion.div
      style={{ scaleX, transformOrigin: 'left' }}
      className="fixed top-0 left-0 h-[3px] w-full z-[200] bg-[var(--accent)]"
    />
  )
}
```

### ③ 指针：磁吸 + 3D 倾斜 + 涟漪 + 光泽跟随

最强感知来自界面跟随指针实时形变。`useMotionValue` + `useSpring` 天然 rAF 节流，平滑且不掉帧。

```tsx
'use client'
import { motion, useMotionValue, useSpring, useTransform } from 'motion/react'

// (1) 磁吸：按钮被指针吸引，松手弹簧归位
export function Magnetic({ children, strength = 0.3 }: { children: React.ReactNode; strength?: number }) {
  const x = useMotionValue(0); const y = useMotionValue(0)
  const sx = useSpring(x, { stiffness: 200, damping: 15 })
  const sy = useSpring(y, { stiffness: 200, damping: 15 })
  return (
    <motion.div style={{ x: sx, y: sy }}
      onPointerMove={(e) => {
        const r = e.currentTarget.getBoundingClientRect()
        x.set((e.clientX - r.left - r.width / 2) * strength)
        y.set((e.clientY - r.top - r.height / 2) * strength)
      }}
      onPointerLeave={() => { x.set(0); y.set(0) }}>
      {children}
    </motion.div>
  )
}

// (2) 3D 倾斜：卡片随指针做 perspective 倾斜
export function Tilt({ children }: { children: React.ReactNode }) {
  const rx = useMotionValue(0); const ry = useMotionValue(0)
  const srx = useSpring(rx, { stiffness: 200, damping: 20 })
  const sry = useSpring(ry, { stiffness: 200, damping: 20 })
  return (
    <motion.div style={{ rotateX: srx, rotateY: sry, transformStyle: 'preserve-3d', transformPerspective: 800 }}
      onPointerMove={(e) => {
        const r = e.currentTarget.getBoundingClientRect()
        const px = e.clientX - r.left / r.width - 0.5
        const py = e.clientY - r.top / r.height - 0.5
        ry.set(px * 10); rx.set(-py * 10)
      }}
      onPointerLeave={() => { rx.set(0); ry.set(0) }}>
      {children}
    </motion.div>
  )
}

// (3) 光泽跟随：伪元素 radial-gradient 高光跟踪指针（CSS 变量驱动）
export function SheenFollow({ children }: { children: React.ReactNode }) {
  return (
    <div className="group relative overflow-hidden"
      onPointerMove={(e) => {
        const r = e.currentTarget.getBoundingClientRect()
        e.currentTarget.style.setProperty('--mx', `${e.clientX - r.left}px`)
        e.currentTarget.style.setProperty('--my', `${e.clientY - r.top}px`)
      }}>
      <div className="pointer-events-none absolute inset-0 opacity-0 transition-opacity duration-300
        [background:radial-gradient(220px_circle_at_var(--mx,50%)_var(--my,50%),var(--glass-highlight),transparent_60%)]
        group-hover:opacity-50" />
      {children}
    </div>
  )
}
```

涟漪点击：用 shadcn 的按钮 + 一个 Motion `span` 从触点 `scale(0→40)` + `opacity` 淡出即可（`key` 用时间戳保证重播）。

### ④ 状态：心跳脉冲 / 呼吸 / 在线光晕 / 连接流光

实时状态感知，全部用 Motion `animate` 无限循环，作用于 `opacity`/`scale`/`filter`。

```tsx
'use client'
import { motion } from 'motion/react'

// 心跳脉冲（连接中）
export const Pulse = () => (
  <motion.span className="inline-block h-2.5 w-2.5 rounded-full bg-[var(--accent)]"
    animate={{ opacity: [1, 0.4, 1], scale: [1, 0.85, 1] }}
    transition={{ duration: 1.8, repeat: Infinity, ease: 'easeInOut' }} />
)
// 在线呼吸 + 光晕
export const OnlineGlow = () => (
  <span className="relative inline-block h-2.5 w-2.5">
    <span className="absolute inset-0 rounded-full bg-emerald-500" />
    <motion.span className="absolute inset-0 rounded-full border-2 border-emerald-500"
      animate={{ scale: [0.8, 2.4], opacity: [0.7, 0] }}
      transition={{ duration: 2, repeat: Infinity, ease: [0.16, 1, 0.3, 1] }} />
  </span>
)
// 连接流光：强调色条沿连接线流动
export const ConnectStream = () => (
  <div className="relative h-0.5 w-full overflow-hidden bg-[var(--glass-border)]">
    <motion.div className="absolute inset-y-0 w-full"
      style={{ background: 'linear-gradient(90deg, transparent, var(--accent), transparent)' }}
      animate={{ x: ['-100%', '100%'] }}
      transition={{ duration: 1.2, repeat: Infinity, ease: 'easeInOut' }} />
  </div>
)
```

状态选型：连接中 = pulse；在线 = breathe + glow；加载 = dots/spinner；错误 = shake（`animate={{ x: [0,-5,5,-4,4,0] }}`）+ danger 色瞬时切；离线 = 静态灰点。每个状态都要有视觉反馈。

### ⑤ 加载：转圈 / 骨架流光 / 进度流光 / 按钮 loading / 数字滚动

用户等待时必须有"正在工作"信号，按场景选变体，不止一种 spinner。

```tsx
'use client'
import { motion, useMotionValue, animate, useInView } from 'motion/react'
import { useEffect, useRef } from 'react'

// 转圈（纯 rotate，合成层，无限循环安全）
export const Spinner = () => (
  <motion.span className="block h-7 w-7 rounded-full border-[2.5px] border-[var(--glass-border)] border-t-[var(--accent)]"
    animate={{ rotate: 360 }} transition={{ duration: 0.7, repeat: Infinity, ease: 'linear' }} />
)
// 三点跳动
export const LoaderDots = () => (
  <div className="flex gap-1.5">
    {[0, 1, 2].map((i) => (
      <motion.span key={i} className="h-2 w-2 rounded-full bg-[var(--accent)]"
        animate={{ y: [0, -8, 0], opacity: [0.4, 1, 0.4] }}
        transition={{ duration: 1.2, repeat: Infinity, delay: i * 0.15, ease: 'easeInOut' }} />
    ))}
  </div>
)
// 数字滚动（tabular-nums 不抖）
export function CountUp({ to, duration = 1.6 }: { to: number; duration?: number }) {
  const ref = useRef<HTMLSpanElement>(null)
  const inView = useInView(ref, { once: true })
  const mv = useMotionValue(0)
  useEffect(() => {
    if (!inView) return
    const controls = animate(mv, to, { duration, ease: [0.16, 1, 0.3, 1] })
    const unsub = mv.on('change', (v) => { if (ref.current) ref.current.textContent = Math.round(v).toLocaleString() })
    return () => { controls.stop(); unsub() }
  }, [inView, to, duration, mv])
  return <span ref={ref} className="tabular-nums">0</span>
}
```

骨架屏流光：`.skeleton` 容器 + 一个 `::after` 伪元素用 `animate={{ x: ['-100%', '100%'] }}` 横扫（或 CSS `transform: translateX` keyframes）。进度流光同理，在填充条上叠一层白色高光带横扫；不定进度用整条强调色条左右滑动。按钮 loading：`disabled` + 内部 `<Spinner>` 缩小居中。

选型表：按钮操作 → 按钮 loading；连接/认证 → spinner / 双圈；列表/面板 → 骨架屏（多层）；轻量请求 → 三点跳动；上传下载 → 进度流光；未知进度 → 不定进度条。

### ⑥ 过渡：AnimatePresence / layoutId / 折叠展开 / 圆形揭露换肤

让变化"流动"而非跳变。

```tsx
'use client'
import { motion, AnimatePresence } from 'motion/react'

// Tab/路由切换：旧视图淡出、新视图 riseIn，mode="wait" 顺序播放
export function TabView({ tab }: { tab: string }) {
  return (
    <AnimatePresence mode="wait">
      <motion.div key={tab}
        initial={{ opacity: 0, y: 12 }} animate={{ opacity: 1, y: 0 }} exit={{ opacity: 0, y: -12 }}
        transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}>
        {tab === 'a' ? <PanelA /> : <PanelB />}
      </motion.div>
    </AnimatePresence>
  )
}

// layoutId 指示条：侧栏/Tab 当前项高亮条平滑滑动（FLIP 由 Motion 自动完成）
export function NavItem({ active, layoutId }: { active: boolean; layoutId: string }) {
  return (
    <button className="relative">
      {active && <motion.span layoutId={layoutId} className="absolute inset-0 rounded-full bg-[var(--accent-soft)]"
        transition={{ type: 'spring', stiffness: 400, damping: 32 }} />}
      <span className="relative">导航项</span>
    </button>
  )
}

// 折叠展开：grid-template-rows 0fr→1fr，无需测高（CSS 动画，不触发重排）
// 列表重排：父级加 <motion.div layout>，增删时 Motion 自动 FLIP 飞行
```

主题换肤圆形揭露：用 `document.startViewTransition` + `clip-path: circle()` 从点击点扩散（兜底：直接切）。氛围层换肤加 `.aura` 上 `transition: background 0.8s var(--ease-theme)`。

### ⑦ 灵动点缀（合成层微动效，叠加十几个也不掉帧）

这层让界面"灵"——全部 `transform`/`opacity`/`filter`，零重绘。给主结构配 2–3 个常驻（边缘流光、脉冲环、光标闪烁）+ 一批事件触发（光泽划过、涟漪、抖动、徽章弹现）。

```tsx
'use client'
import { motion } from 'motion/react'

// 玻璃边缘流光（顶栏/主卡常驻主角，周期 ≥5s）
export const SheenSweep = () => (
  <span className="pointer-events-none absolute inset-y-0 left-0 w-2/5"
    style={{ background: 'linear-gradient(90deg, transparent, var(--glass-highlight) 50%, transparent)',
             transform: 'skewX(-18deg)' }}
    animate={{ x: ['-120%', '320%'], opacity: [0, 0.8, 0.8, 0] }}
    transition={{ duration: 5, repeat: Infinity, ease: 'easeInOut', times: [0, 0.2, 0.8, 1] }} />
)
// 徽章弹现
export const BadgePop = (p: React.PropsWithChildren) => (
  <motion.span className="badge" initial={{ scale: 0, opacity: 0 }}
    animate={{ scale: [0, 1.25, 1], opacity: 1 }} transition={{ duration: 0.4, ease: [0.34, 1.56, 0.64, 1] }}>
    {p.children}
  </motion.span>
)
// 图标弹跳（点击/激活）
export const IconBounce = (p: React.PropsWithChildren<{ trigger: number }>) => (
  <motion.span animate={{ scale: [1, 1.3, 0.9, 1] }} key={p.trigger} transition={{ duration: 0.4 }}>
    {p.children}
  </motion.span>
)
```

其余灵动：光泽划过（hover 触发，`x: '-60%'→'120%'`）、脉冲环（`scale: [0.8, 2.4], opacity: [0.7, 0]`）、悬停浮起（`whileHover={{ y: -4 }}`，阴影可平滑加深）、抖动（错误，`x: [0,-5,5,-4,4,0]`）、光标闪烁（`opacity: [1,0]` step）、下划线生长（`scaleX: 0→1` + `transformOrigin: left`）、聚焦环呼吸（`scale/opacity` 微呼吸，`box-shadow` 静态）、光晕脉冲（伪元素 `opacity/scale` 呼吸）。

编排心法：一屏选 1 个常驻强动效（如顶栏 sheen-sweep）当主角，其余用触发型点缀；同源时序（共享曲线与时长基调）。

---

## 组件规范（shadcn/ui + 玻璃材质 + Motion）

在 shadcn 组件上叠加玻璃 token 与 Motion，不重造交互逻辑。所有可交互元素 5 态齐全（default/hover/active/focus-visible/disabled），`focus-visible` 有清晰 ring（`--ring` 用 `var(--accent-soft)` 或 2px outline + offset）。

```tsx
// 玻璃卡片（悬停浮起 + 阴影平滑加深，全部合成层/ Motion 调度）
<motion.div
  whileHover={{ y: -4 }}
  transition={{ duration: 0.35, ease: [0.16, 1, 0.3, 1] }}
  className="glass glass-sheen rounded-3xl p-7 transition-shadow duration-300 hover:shadow-[var(--shadow-glass),0_24px_60px_rgba(0,0,0,0.12)]"
>
  <h3 className="text-xl font-semibold">卡片标题</h3>
</motion.div>

// 玻璃按钮（同样享有 backdrop-filter；hover 浮起 + 光泽划过 + active 回弹）
<motion.button
  whileHover={{ scale: 1.04, y: -1 }} whileTap={{ scale: 0.97 }}
  transition={{ type: 'spring', stiffness: 400, damping: 22 }}
  className="btn-shine glass rounded-full px-6 py-2.5 font-medium"
>按钮</motion.button>

// 主按钮：实心强调色 + 玻璃覆膜，投影随主题色换肤
<button className="rounded-full px-6 py-2.5 font-medium text-white"
  style={{ background: 'var(--accent)', border: '1px solid rgba(255,255,255,0.25)',
           boxShadow: `0 1px 1px rgba(255,255,255,0.4) inset, 0 8px 24px var(--accent-glow)` }}>
  主按钮
</button>
```

- **导航栏**：`.glass-strong` + `sticky` + 胶囊形（`rounded-full`，宽度 `min(1120px, 92%)`），`z-index: 100`。
- **输入框**：`.glass` + `rounded-[14px]`，聚焦 `border-color: var(--accent)` + `box-shadow: 0 0 0 4px var(--accent-soft)`，可叠聚焦环呼吸。
- **Modal**：shadcn `Dialog`（Radix 焦点陷阱/Esc）+ `.glass-strong` + `rounded-[28px]`，入场 `scale(0.92)+y(20px)→1`（spring）。遮罩 `.glass-soft` 或 `rgba(0,0,0,0.2)` + `blur(8px)`。
- **标签/药丸**：`.glass-soft` + `rounded-full`。

> 圆角分级：卡片 24–28px，按钮/标签 9999px（胶囊），输入框 14px。透明度分层：主层 `--glass-tint-strong` → `--glass-tint` → `--glass-tint-soft`，靠透明度而非颜色建立层级。玻璃嵌玻璃最多两层。

---

## 主题切换（双维度，Next.js App Router）

主题状态用 `next-themes` 或自管，写入 `<html>` 属性 + `localStorage`，SSR 防闪烁用内联脚本（`suppressHydrationWarning`）。

```tsx
// lib/theme.ts —— 在客户端设置并持久化
export function setTheme(t: 'light' | 'dark') {
  document.documentElement.setAttribute('data-theme', t); localStorage.setItem('glass-theme', t)
}
export function setAccent(a: string) {
  document.documentElement.setAttribute('data-accent', a); localStorage.setItem('glass-accent', a)
}
```

切换面板：胶囊形玻璃面板内置明暗开关 + 7 个主题色色点；色点点击 `setAccent`，激活项用 `aria-pressed="true"` + 外圈 `box-shadow: 0 0 0 3px var(--sw)` 高光环。可加圆形揭露换肤（`startViewTransition`）。主题色亲和性见下表，默认 Mint（薄荷绿 #00D4AA），除非用户指定品牌色或明确场景。

| 主题色 | 气质 | 适用 |
|--------|------|------|
| Mint 薄荷绿 | 清新生机、轻盈科技 | 默认；科技、健康、数据看板 |
| Azure 蔚蓝 | 信任、科技通用 | 工具型应用、官网 |
| Rose 玫瑰 | 感性、时尚 | 美妆、生活方式、社交 |
| Violet 紫罗兰 | 创意、神秘 | 创意工具、AI 产品 |
| Emerald 翡翠 | 健康、数据 | 健康、环保、金融正向 |
| Amber 琥珀 | 温暖、活力 | 餐饮、电商促销 |
| Indigo 靛青 | 专业、稳重 | 金融、企业服务 |

---

## 无障碍

- **语义化**：`nav/header/main/aside/section/article/table`，不用 `div` 做导航/列表。
- **ARIA**：图标按钮 `aria-label`；通知 `role="status"`/`aria-live`；下拉/抽屉 `aria-expanded`/`aria-controls`（shadcn/Radix 已内置）。
- **键盘可达 + 焦点可见**：`:focus-visible` ring 全覆盖（`outline: 2px solid var(--accent); outline-offset: 2px`）。
- **对比度**：正文 ≥ 4.5:1，大字 ≥ 3:1；玻璃上文字注意模糊后仍达标。
- **`prefers-reduced-motion`**：用 Motion 的 `useReducedMotion()`，开启时把入场/循环降为极轻或瞬时（保留功能性反馈，不删除）。

```tsx
import { useReducedMotion } from 'motion/react'
const reduce = useReducedMotion()
// reduce 为真时：duration 调小、repeat 循环降为单次、视差/磁吸幅度归零
```

---

## 响应式

- 桌面 ≥1280：完整布局，侧栏常驻。
- 平板 768–1279：侧栏折叠为图标态，并排图表变堆叠。
- 移动 <768：侧栏折叠为抽屉（shadcn `Sheet` + 汉堡触发），KPI 单列，表格横向滚动或卡片化。
- 用 Tailwind 断点 `sm/md/lg/xl/2xl`，移动端用 `min-h-dvh` 而非 `100vh`，避免内容超出安全区。

---

## 执行流程

1. **铺氛围背景**：先写 `.aura` 色斑层与底色——玻璃显形的舞台。色斑取 `--aura-1~4`，允许缓慢漂移。
2. **注入主题变量**：完整复制 CSS 变量（明暗基础层 + 7 色相层 + 派生色）到 `globals.css`，根节点 `<html data-theme="light" data-accent="mint">`。
3. **搭玻璃组件**：导航 `.glass-strong` + 胶囊；卡片 `.glass.glass-sheen`；按钮/输入框/标签取对应玻璃模板（同等享有 backdrop-filter）。**强调色派生值用 `var(--accent)` / `rgba(var(--accent-rgb), α)`，禁止写死色值。**
4. **加主题切换面板**：明暗 + 主题色均可用户切换，绑定 `setTheme`/`setAccent` 并刷新激活态。
5. **加生命（七大维度 Motion 动效）**：① 加载（按场景 6 种变体）② 状态（pulse/breathe/glow/stream/shake）③ 入场（`whileInView` 错落，焦点先行）④ 滚动（reveal/视差/进度条）⑤ 指针（magnetic/tilt/ripple/sheen-follow，用 `useSpring`）⑥ 过渡（`AnimatePresence`/`layoutId`/grid 折叠/圆形揭露）⑦ 灵动（sheen-sweep 常驻主角 + 触发型点缀）。每屏一强多弱，同源时序。
6. **自检**：见下清单。

---

## 自检清单

- [ ] 主结构玻璃元素有 `backdrop-filter`（透射）+ 边缘高光（描边 + inset 白色阴影 + 镜面 sheen）？
- [ ] 按钮/输入框/标签**同样享有玻璃质感**，未为性能降级为纯 tint？
- [ ] 背景氛围层存在，色斑取 `--aura-1~4` 且**允许缓慢流动**？
- [ ] 字体栈使用 Geist / Inter + 中文系统字体，数字 `tabular-nums`？
- [ ] 明暗（`data-theme`）与主题色（`data-accent`）两个维度均可切换，默认 light + mint？
- [ ] 所有强调色派生值用 `var(--accent)` / `rgba(var(--accent-rgb), α)`，无写死色值？切换主题色时氛围/按钮/聚焦环整体换肤？
- [ ] 动效全面用 Motion，覆盖七大维度，一个不少？
- [ ] 动效作用于 transform/opacity/filter；尺寸过渡用 `scale` 或 `grid-template-rows 0fr→1fr`？
- [ ] pointermove/scroll 跟随用 `useMotionValue`+`useSpring`（已节流），无裸事件直写样式？
- [ ] hover 浮起 + 阴影可平滑过渡；入场 `whileInView` + `viewport={{ once: true }}` 落定？
- [ ] 技术栈 = TS + React + Next.js + Tailwind + shadcn/ui + Radix + Motion；交互元素 5 态齐全，`focus-visible` ring 清晰？
- [ ] 语义化标签 + ARIA + 键盘可达 + 对比度达标；`useReducedMotion()` 兜底？
- [ ] 三档响应式合理，移动端抽屉可用，无横向滚动？

---

## 设计准则

- **玻璃是主角，背景是配角**：背景色斑服务于玻璃显形，不喧宾夺主；强调色只点睛（主按钮、链接、焦点、关键数据），不可大面积铺色。
- **靠透明度与模糊分层**，而非浓重色块：`tint-strong → tint → tint-soft` 表达层级。
- **高光永远是白色系**（亮色 `rgba(255,255,255,0.85)`，暗色 `rgba(255,255,255,0.18)`），这是玻璃折射光线的物理本质。
- **动效一强多弱**：每屏一个常驻强动效（如顶栏边缘流光），其余触发型点缀；强感知来自编排节奏，不是堆砌。
- **动效只动合成层**：transform/opacity/filter 走合成器，可放心无限循环（spinner/脉冲/光晕/流光/连接流光）；width/height/margin 等重排属性不入动画。
- **视觉质感优先**：玻璃质感、氛围流动、hover 阴影过渡一律保留；性能只做温和优化（优先合成层、`useSpring` 节流、合理 `will-change`），不为性能删减视觉与动效。

---

## 常见误区

| 误区 | 正确做法 |
|------|----------|
| 只做一种加载动效（到处同一个 spinner） | 按场景 6 种变体：按钮 loading/转圈/双圈/骨架屏/三点跳动/进度流光/不定进度条 |
| 状态变化无视觉反馈 | pulse/breathe/online-glow/connect-stream/shake，每个状态对应一种 |
| 为性能给按钮/输入框砍掉 `backdrop-filter` | 玻璃质感是高级感来源，所有元素同等享有；流畅靠合成层 + `useSpring`，不靠降级 |
| 砍掉氛围色斑漂移变成静态背景 | 缓慢漂移（≥20s，只动 transform）是"活"的来源，常驻安全，保留 |
| hover 阴影强制瞬时切换（不过渡） | 少量元素的阴影过渡可平滑（Motion 调度），立体感更自然 |
| 动画用了 width/height/margin（每帧重排） | 只动 transform/opacity/filter；尺寸过渡用 `scale` 或 grid `0fr→1fr` |
| 裸 pointermove/scroll 直写样式（掉帧） | 用 Motion `useMotionValue`+`useSpring`，内部已 rAF 节流 |
| 把强调色写死成具体色值 | 用 `rgba(var(--accent-rgb), α)`，否则切主题色时投影/聚焦环不跟随 |
| 玻璃放在纯白/纯灰平铺背景 | 必须铺 `.aura` 色斑，玻璃透出色彩才有生命 |
| 只用 `backdrop-filter: blur()` 缺边缘高光 | 必须加白色描边 + inset 白色阴影 + 镜面 sheen，否则只是"毛玻璃"非"液态玻璃" |
| 暗色主题把高光做成强白 | 暗色高光 `rgba(255,255,255,0.18)`，含蓄反光才真实 |
| 忘记 `-webkit-backdrop-filter` | Safari 必须前缀，否则玻璃失效 |
| 用线性 ease 做动效 | 按物理特性选曲线：位移 `--ease-liquid`，回弹 `--ease-spring`，入场落定 `--ease-out-expo` |
| 全屏所有元素都上强动效 | 每屏一强多弱，只给焦点元素强动效，其余克制衬托 |
