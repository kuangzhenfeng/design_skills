---
name: ultra-design
description: 当需要构建高质量、极具设计感的前端界面（网页、落地页、Dashboard、组件、可视化工具、应用 UI 等）时使用。输出 **Liquid Glass（液态玻璃）** 设计语言 — 半透明晶体面板材质、动态边缘高光、镜面反射、流动氛围背景。**性能铁律：氛围层零 `filter: blur()`（巨型色斑位图逐像素采样、最吃 GPU，氛围改单层 radial-gradient mesh）；玻璃层默认纯 tint（零 backdrop-filter、零卷积，主流机/低端机绝对流畅），由「效果优先」开关（默认关）开启后激活 `backdrop-filter: blur()+saturate()` 真折射；随小屏 / 「减少动效」开关同样降级为纯 tint（**不读系统 `prefers-reduced-motion`**——各机偏好不一、跨机效果不可控，改由应用内显式开关统一控制）；绝对禁止强 `blur(20px+)`。** 玻璃的"透"由半透 tint 承载，液态质感由菲涅尔折射高光（描边 + inset 白光 + sheen + 浮起投影）保证。**承诺一套完整的 Motion 动效体系**：加载（转圈/骨架流光/进度流光/按钮 loading/数字滚动）、状态（心跳脉冲/呼吸/在线光晕/连接流光）、入场（错落浮现/缩放聚焦/缩放弹出/翻转）、滚动（滚动显现/视差/进度条）、指针（磁吸/3D 倾斜/涟漪/光泽跟随）、过渡（FLIP/layoutId 滑动/折叠展开/AnimatePresence/圆形揭露换肤）、灵动点缀（边缘流光/光泽划过/脉冲环/悬停浮起/抖动/光标闪烁/图标弹跳/下划线生长/徽章弹现/光晕脉冲）。默认技术栈 TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion，输出生产级、可直接运行的 TSX 代码。视觉质感优先：玻璃质感、氛围流动、阴影过渡一律完整保留，只保留温和的合成层性能建议，不为性能删减任何视觉与动效。默认亮色 + 薄荷绿（#00D4AA）主题，内置亮/暗双主题切换与多主题色切换（两个维度正交、任意组合）。告别通用 AI 审美。
---

# Ultra Design — Liquid Glass 设计系统（Motion 驱动）
 
## 概述

Ultra Design 是一套基于 **Liquid Glass（液态玻璃）** 设计语言的前端规范，技术栈默认 **TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion**。

核心理念：**界面如同真实的液态玻璃 — 透、亮、有重量、有动感**。动效是必需品而非装饰：加载有转圈、状态有心跳、入场有浮现、滚动有视差、指针有跟随、过渡有流动、交互有灵动反馈。**全部动效通过 Motion 实现，一个都不为"性能"删掉** —— 流畅靠「让动效走合成层（transform/opacity）」与「滚动/指针监听节流」这类温和手段保证，而非牺牲视觉质感；**氛围层零 `filter: blur()`（巨型色斑位图采样、最昂贵）；玻璃层默认纯 tint（零 backdrop-filter、零卷积、主流机/低端机绝对流畅），由「效果优先」开关（默认关）开启后激活 `backdrop-filter: blur()+saturate()` 真折射；强 `blur()` 一律禁止**（详见材质章）。

### 设计三原则

1. **材质真实**：玻璃必须真的"透"——半透 tint 让氛围色彩隐约穿过来；边缘有镜面高光，悬停时浮起、光泽划过、阴影平滑加深。按钮、输入框、卡片同等享有晶体质感，**不为了性能给细碎元素降级**。
2. **动效是生命力**：七大维度（加载/状态/入场/滚动/指针/过渡/灵动）一律完整实现。流畅不靠删减动效，而靠合成层属性 + Motion 的高性能调度。
3. **视觉质感优先**：氛围色斑可以缓慢流动、hover 阴影可以平滑过渡、所有玻璃元素都享有半透晶体面板（tint + 折射高光 + sheen + 投影）——这些是"看起来高级"的来源，**一律保留**。性能只做温和优化（优先合成层、节流监听、合理 `will-change`，玻璃默认纯 tint、效果优先开激活折射、**氛围球零 `filter:blur()`**），不设牺牲视觉的红线。

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
| **透射（Transmission）** | 背景色彩透过半透 tint 隐约可见——是柔和氛围色而非清晰内容 | 半透 `--glass-tint`（默认）+ `backdrop-filter: blur()+saturate()`（效果优先开才激活） |
| **边缘高光（Specular Edge）** | 玻璃边缘折射聚集的光线，形成发光边 | `inset` 白色阴影 + 半透明白色描边 + 伪元素镜面高光 |
| **镜面反射（Reflection）** | 顶面反射环境光，形成立体厚度感 | 顶部内高光 + 渐变背景 + 多层投影 |

**关键认知**：没有背景的玻璃等于没有玻璃。Liquid Glass 必须放在有色彩流动、有明暗变化的背景之上。因此**背景氛围层**与**晶体玻璃层**永远成对出现。

> **为什么玻璃默认用「纯 tint」、由「效果优先」开关激活折射**：`backdrop-filter: blur()` 是对玻璃后方整块区域做空间卷积采样，开销随**玻璃面积 × 半径²**增长——面积翻倍线性涨、半径翻倍四倍涨，在 Dashboard 这类多玻璃页面里逐元素累积，会拖垮 GPU、低端机掉帧。本设计系统**默认纯 tint 玻璃——零 `backdrop-filter`、零卷积采样**，玻璃的"透"由半透 tint 承载、"液态质感"由菲涅尔折射高光（边缘描边 + inset 白光 + sheen + 浮起投影）保证，主流机/低端机绝对流畅、零掉帧；由 Topbar 的「效果优先」开关（默认关）开启后，才激活 `backdrop-filter: blur()+saturate()` 真折射，后方氛围被柔化、磨砂质感全开——这是截图/演示最出彩的档位。两档只差一个"折射层"，其余液态要素完全相同。

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

## 默认 Favicon（消除 404 + 品牌一致性）

Next.js App Router 默认无 favicon，浏览器请求 `/favicon.ico` 必然 404（验收清单"无 console error"的常见扣分点）。每个工程**必须自带一个默认 favicon**，且与设计语言一致——不是随便放个占位图。

### 配方：accent 色渐变方 + 极光" A "字母（SVG，零外链）

`src/app/icon.svg`（Next.js 自动作为站点图标，无需 `<link>` 声明，支持明暗主题切换）：

```svg
<svg width="32" height="32" viewBox="0 0 32 32" fill="none" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="g" x1="0" y1="0" x2="32" y2="32" gradientUnits="userSpaceOnUse">
      <stop stop-color="#00D4AA"/>
      <stop offset="1" stop-color="#38BDF8"/>
    </linearGradient>
  </defs>
  <rect width="32" height="32" rx="8" fill="url(#g)"/>
  <rect width="32" height="32" rx="8" fill="white" fill-opacity="0.16"/>
  <path d="M9.5 22 L16 9 L22.5 22 M12 17 H20"
        stroke="white" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
</svg>
```

> 同目录再加 `src/app/icon-dark.svg`（暗色 tab 适配：背景换成深色玻璃 `#0A0A0C`，字母用 accent 渐变）作为 `media` 备选，或直接用一套（亮色背景的渐变方在暗色浏览器 tab 上同样清晰，单文件即可）。

### 实现要点（铁律）

- **放 `src/app/icon.svg`**：Next.js 13.3+ App Router 自动注入 `<link rel="icon" href="/icon">`，**零配置、零路由代码**。不要手写 `<link>`、不要放 `public/favicon.ico`（App Router 优先用 `app/icon.*`）。
- **纯 SVG，不引外部资源**：`fill="url(#g)"` 内联渐变，无字体/图片外链。体积 < 1KB。
- **与主题色一致**：默认 Mint→Azure 渐变（`#00D4AA → #38BDF8`，即 Aurora 品牌）；若用户指定品牌色，把两个 `stop-color` 换成该主题色的明/暗变体（如 `--accent-l` / `--accent-d`）。
- **形态呼应设计语言**：圆角方（`rx="8"`，呼应卡片圆角）+ 半透明白覆盖（`fill-opacity="0.16"`，呼应玻璃高光）+ 极光"A"字母（Aurora 首字母，呼应品牌）。
- **`apple-icon.png` 可选**：iOS 主屏需要 PNG，可用同 SVG 渲染成 `src/app/apple-icon.png`（180×180）；非必需，SVG favicon 现代浏览器全支持。

### 验收

- [ ] `src/app/icon.svg` 存在，`GET /icon` 200、无 `/favicon.ico` 404？
- [ ] favicon 配色与默认主题色（Mint）一致，非随机占位图？

---

## 晶体玻璃材质配方（核心 · 默认纯 tint / 效果优先激活折射）

**性能铁律：本设计系统全篇禁止 `filter: blur()`（氛围球那类巨型位图逐像素采样），氛围层只能用单层 radial-gradient mesh。玻璃层**默认纯 tint——零 `backdrop-filter`、零卷积采样**，由「效果优先」开关（`<html data-effect="on">`，默认关）开启后激活 `backdrop-filter: blur()+saturate()` 真折射；并随小屏 / 「减少动效」开关同样降级为纯 tint（**不读系统 `prefers-reduced-motion`**——各机偏好不一、跨机效果不可控，统一改由应用内显式开关控制，详见「减少动效开关」章）。**绝对禁止强 `blur()`**（半径 ≥ 20px、大面积铺开），那才是这套语言里真正吃 GPU 的开销——半径翻倍开销四倍涨（R²），是 blur 开销模型里最大的黑洞。**

玻璃的"透"与"液"由 **半透 tint（alpha 承载通透）+ 边缘高光（描边 + inset 白光 + sheen 伪元素）+ 浮起投影（多层 box-shadow）** 叠加实现（默认纯 tint，这三件套已足够"液态"）；效果优先开才补 **`backdrop-filter: blur()+saturate()`**（真折射层，柔化后方氛围）。

**任何晶体元素都包含：半透 tint 透射 + 边缘高光 + 镜面投影** 三件套（默认），效果优先开补折射层。按钮、输入框、卡片同等享有完整质感——这是高级感的来源，不要为了性能给任何元素降级。

```css
/* 默认（无 data-effect="on"）= 纯 tint 玻璃：零 backdrop-filter、零卷积开销。
   玻璃质感由 半透 tint + 边缘高光(描边+inset 白光) + sheen 伪元素 + 浮起投影 撑起，
   主流机/低端机绝对流畅。 */
.glass {
  /* 半透 tint 承载"透"——下方氛围色斑透过 tint 隐约可见（氛围感，非清晰内容）。
     默认不挂 backdrop-filter；效果优先开(data-effect="on")才激活 blur+saturate 折射。 */
  position: relative;
  background: var(--glass-tint);
  border: 1px solid var(--glass-border);
  box-shadow: var(--shadow-glass);
  border-radius: 24px;
}
.glass-strong {
  position: relative;
  background: var(--glass-tint-strong);
  box-shadow: var(--shadow-glass), 0 1px 0 rgba(255,255,255,0.5) inset;
}
.glass-soft {
  position: relative;
  background: var(--glass-tint-soft);
  border: 1px solid var(--glass-border);
}

/* 效果优先开关（默认关）：开启后激活真折射——blur+saturate 给玻璃"折"的底，
   柔化后方氛围色斑。默认(关)无 backdrop-filter，纯 tint 零卷积。 */
:root[data-effect="on"] .glass,
:root[data-effect="on"] .glass-strong,
:root[data-effect="on"] .glass-soft {
  -webkit-backdrop-filter: blur(8px) saturate(1.5);
  backdrop-filter: blur(8px) saturate(1.5);
}

/* ---- 降级：效果优先的折射在低端场景退回纯 tint，质感不丢 ---- */
/* 小屏（移动端 backdrop-filter 相对昂贵）→ 强制关掉折射，
   玻璃仍有 tint + 边缘高光 + 投影，退回纯 tint 玻璃。
   注：减少动效不在此媒体查询里读 prefers-reduced-motion（各机偏好不一、跨机
   效果不可控）——动效与玻璃降级统一由应用内「减少动效」开关（<html data-reduce-motion="on">）
   显式控制，见「减少动效开关」章。 */
@media (max-width: 768px) {
  .glass, .glass-strong, .glass-soft { -webkit-backdrop-filter: none; backdrop-filter: none; }
}
/* 减少动效开关（默认 off）：开启后强制纯 tint 降级（即使效果优先开着也关掉折射），跨机一致 */
:root[data-reduce-motion="on"] .glass,
:root[data-reduce-motion="on"] .glass-strong,
:root[data-reduce-motion="on"] .glass-soft { -webkit-backdrop-filter: none; backdrop-filter: none; }

/* 顶部镜面高光（specular sheen）：区分"真液态玻璃"与"死板半透明"的关键 */
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

> **`backdrop-filter` 用在哪、不用在哪**：
> - **用在**：所有 `.glass` / `.glass-strong` / `.glass-soft` 元素，但**仅当「效果优先」开关开启**（`data-effect="on"`）——给统一的"轻度折射"层，柔化后方氛围色斑（比纯 tint 更"液态"）。**默认关 = 纯 tint 零卷积**，主流机/低端机绝对流畅。
> - **不用在**：① `filter: blur()` 氛围球——那是巨型位图逐像素采样，全篇禁止；氛围层只用单层 radial-gradient mesh。② 强 `blur(20px+)`——blur 开销随面积×半径²暴涨，半径翻倍四倍涨，是这套语言里真正吃 GPU 的开销。③ 默认状态——默认零 backdrop-filter 是"主流机流畅"的保证。
>
> **为什么"纯 tint"仍是液态玻璃、且不破坏"透"**：液态感的灵魂是**菲涅尔折射——边缘聚集光线形成发光边、顶面反射环境光形成 sheen**，由 `inset` 白色阴影 + 白色描边 + `::before` sheen 提供，与 blur 半径无关。半透 tint 让氛围色斑透上来保证"透"，边缘高光 + sheen 保证"亮"，浮起投影保证"有重量"——这三件套默认即完整，零 backdrop-filter 仍是"液态玻璃"。效果优先开补上 `blur()+saturate()` 折射层，把透上来的氛围柔化成磨砂质感（截图最出彩）。氛围色斑本身已是大半径 `radial-gradient` 软过渡（见下章），玻璃层无需默认就做二次模糊。

---

## 背景氛围层（玻璃的舞台 · 零 filter）

玻璃无背景则无效。每个页面先铺一层**流动的 mesh 色斑**，玻璃才显形。色斑取自 `--aura-1~4`，切换主题色时整体换肤。

**性能铁律：氛围层用「单层 CSS 多重 radial-gradient mesh」+「整层 transform 漂移」，绝不给色斑加 `filter: blur()`。** mesh 的软过渡本身来自 `radial-gradient` 的 `transparent` 收尾，已经是大半径柔化——再叠 `filter: blur(80px)` 是在一张巨大位图上做高斯采样，是这套语言里最大的 GPU 开销，**砍掉它，视觉几乎无损（mesh 渐变本就柔和）**。

```css
/* 单层 mesh：4 个 radial-gradient 叠在 .aura 上，整层只占 1 个合成层 */
.aura {
  position: fixed; inset: 0; z-index: 0;
  overflow: hidden; pointer-events: none;
  opacity: 0.55;
  background:
    radial-gradient(38vmax 38vmax at 14% 12%, var(--aura-1), transparent 62%),
    radial-gradient(42vmax 42vmax at 86% 18%, var(--aura-3), transparent 64%),
    radial-gradient(40vmax 40vmax at 80% 84%, var(--aura-2), transparent 64%),
    radial-gradient(36vmax 36vmax at 16% 82%, var(--aura-4), transparent 62%);
  animation: aura-drift 26s var(--ease-smooth, ease-in-out) infinite alternate;
  will-change: transform;
}
@keyframes aura-drift {
  to { transform: translate(2.5vmax, 1.5vmax) scale(1.04); }
}
```

```tsx
{/* 整层 mesh 随 1 个 keyframe 缓慢漂移；内容浮在其上 */}
<div className="aura" />
<main className="relative z-10">{/* 晶体卡片透出底层流动色斑 */}</main>
```

> **对比：4 个 `filter: blur(80px)` 球 vs 1 层 mesh**。旧法是 4 个 60vmax 大球各自带 80px 模糊 + 各自的 drift 合成层 = 4 张巨幅模糊位图常驻 GPU；新法是 1 个元素用 `background` 画 4 个 radial-gradient + 整层 1 个 transform 漂移 = 1 个合成层、零逐像素模糊。**视觉都是"缓慢流动的彩色光晕"，开销差一个数量级。** 温和建议：mesh 用 3–4 个色斑、漂移周期 ≥ 20s、只动 `transform`/`opacity`，可常驻不掉帧。

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

> **铁律**：所有动效作用于 `transform` / `opacity`（合成层）。`width`/`height`/`top`/`left`/`margin` 不入动画；**`filter` / `backdrop-filter` 也不入动画**（blur/drop-shadow 动画逐帧按面积采样，开销与氛围球同级；玻璃的弱 `backdrop-filter` 是静态值，绝不拿来做动效）；阴影变化可用 Motion 平滑过渡（Motion 自动优化为合成层，少量元素完全没问题）。pointermove/scroll 驱动的跟随一律用 `useMotionValue` + `useSpring`，Motion 内部已 rAF 节流，无需手写。

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
  hidden: { opacity: 0, y: 24, scale: 0.96 },
  show: { opacity: 1, y: 0, scale: 1,
    transition: { duration: 0.7, ease: [0.16, 1, 0.3, 1] } },
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

变体：`popIn`（`scale: 0.85→1`，回弹）、`slideInLeft`（`x: -40→0`）、`scaleIn`（`scale: 1.05→1` + `opacity: 0→1`，落定感）。首屏主标题最先入场（`delayChildren` 小），形成"标题落下 → 内容跟上"的焦点引导。
> 注：入场动效**只动 transform/opacity**，不用 `filter: blur()` 焦点模糊（那是按元素面积采样的逐帧 blur，开销与背景球同级）。焦点感改用 `scale` 收紧 + `opacity` 渐显达成。

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

这层让界面"灵"——全部 `transform`/`opacity`/`filter`，零重绘。但要分清三类动效的**常驻归属**，不能见缝插针到处放：

- **装饰性常驻强动效（全页只 1 处，放顶栏）**：边缘流光（sheen-sweep）。顶栏 `sticky` 固定、贯穿全页、不承载密集数据，流光是整页的"生命体征"锚点。**全页仅此一处**装饰性常驻光效。
- **状态性常驻动效（按语义，允许多处）**：在线光晕、告警脉冲、连接流光等——它们承载"在线/异常/连接中"的**信息含义**，不是纯装饰，跟随状态元素出现、随状态消失。
- **事件触发动效（任意，hover/click 即现即灭）**：光泽划过、涟漪、抖动、徽章弹现——只在指针/状态变化时一闪，平时不可见。

内容区卡片（主图/KPI/表格/告警/时间线）承载**必须静下来读**的数据，只配**状态类**与**触发类**光效；**严禁**在内容卡上放装饰性常驻光效——它会和数据争夺注意力、拖慢读取节奏。

```tsx
'use client'
import { motion } from 'motion/react'

// 玻璃边缘流光（装饰性常驻主角；全页仅放顶栏 1 处，内容卡禁用——见"编排心法"）
// 颜色用 var(--glass-highlight) 主题自适应（亮色 0.85 明亮 / 暗色 0.18 含蓄）——
// 切勿写死亮白 rgba(255,255,255,0.6)，暗色背景下会过亮刺眼（见下方"高光物理本质"）。
export const SheenSweep = ({ className }: { className?: string }) => (
  <span className={`pointer-events-none absolute inset-y-0 left-0 w-2/5 ${className ?? ''}`}
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

编排心法：**装饰性常驻光效全页仅 1 处，放顶栏**（`sticky`、不承载密集数据，是整页"生命体征"锚点）。内容卡（主图/KPI/表格等）承载必须静读的数据，**严禁装饰性常驻光效**，只用状态类（在线/告警脉冲）与触发类（hover/click 即现即灭）。其余触发型点缀（光泽划过、涟漪、徽章弹现）随意配；同源时序（共享曲线与时长基调）。

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

// 玻璃按钮（默认纯 tint，效果优先开享折射；hover 浮起 + 光泽划过 + active 回弹）
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
- **Modal**：shadcn `Dialog`（Radix 焦点陷阱/Esc）+ `.glass-strong` + `rounded-[28px]`，入场 `scale(0.92)+y(20px)→1`（spring）。遮罩 `.glass-soft` 或 `rgba(0,0,0,0.2)`（**遮罩不加 `blur(8px)`**：整屏遮罩模糊是大面积强 backdrop-filter、最贵的那档；用半透黑遮罩即可聚焦弹窗）。
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

切换面板：胶囊形玻璃面板内置明暗开关 + 7 个主题色色点 + **效果优先开关**（默认关）；色点点击 `setAccent`，激活项用 `aria-pressed="true"` + 外圈 `box-shadow: 0 0 0 3px var(--sw)` 高光环。可加圆形揭露换肤（`startViewTransition`）。主题色亲和性见下表，默认 Mint（薄荷绿 #00D4AA），除非用户指定品牌色或明确场景。

### 效果优先开关（默认关 · 折射开关）

玻璃默认纯 tint（零 backdrop-filter、主流机/低端机绝对流畅）；开启「效果优先」（`<html data-effect="on">`）后激活 `backdrop-filter: blur()+saturate()` 真折射，后方氛围被柔化、磨砂质感全开，截图/演示最出彩。两档只差折射层，其余液态要素（tint + 边缘高光 + sheen + 投影）完全相同。CSS 实现见「晶体玻璃材质配方」章（`:root[data-effect="on"] .glass` 挂 backdrop-filter）。

```tsx
// 切换面板里加一个开关（与明暗/主题色并列）
<button role="switch" aria-checked={effectPriority}
  aria-label="效果优先（激活 backdrop-filter 折射）"
  onClick={() => setEffectPriority(!effectPriority)}
  className={effectPriority ? 'effect-switch on' : 'effect-switch'}>
  <span className="effect-thumb" />
</button>
<span className="text-xs text-text-tertiary">{effectPriority ? '效果优先' : '性能优先'}</span>
```

```ts
// lib/theme.ts —— 效果优先开关（默认 off）
export function setEffectPriority(on: boolean) {
  document.documentElement.setAttribute('data-effect', on ? 'on' : 'off')
  localStorage.setItem('glass-effect-priority', on ? 'on' : 'off')
}
// hydrate 时从 localStorage 还原（首帧前 inline 脚本同步设好，防闪烁）
```

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
- **减少动效**：**不读系统 `prefers-reduced-motion`**（各机系统偏好设置千差万别、开关位置隐蔽，跨机器/跨用户看到的效果不可控，无法保证一致体验）。改为提供一个**应用内的「减少动效」开关**（默认关），用户显式开启才降级——效果在所有机器上完全一致。详见下「减少动效开关」章。

---

## 减少动效开关（默认关 · 不读系统偏好）

### 为什么不用 `prefers-reduced-motion`

`prefers-reduced-motion` 是读操作系统级「减少动态效果」开关的媒体查询，存在三个不可控问题：

1. **跨机不一致**：不同 OS（macOS/Windows/iOS/Android）的该开关位置、默认值、联动范围都不一样，部分老版本或定制 ROM 行为各异——同一份代码在不同机器上动效时有时无，演示与交付效果无法保证。
2. **用户无感**：系统级开关多数用户从不知道、也不会主动设置，等于这个降级几乎永远不触发；真需要降级的用户（晕动症、低性能机）反而够不到它。
3. **与其它开关耦合**：系统「减少动效」常同时关闭过渡、滚动惯性、窗口动画，与本设计系统的「只压运动类动效、保留状态反馈」诉求不对齐。

因此本系统**用应用内显式开关替代**：`<html data-reduce-motion="on">`，由用户在 UI 里点击切换，跨所有机器效果完全一致。

### 开关机制（Zustand + `<html>` 属性 + localStorage）

```ts
// lib/motion.ts —— 减少动效开关（默认 off）
export function setReduceMotion(on: boolean) {
  document.documentElement.setAttribute('data-reduce-motion', on ? 'on' : 'off')
  localStorage.setItem('glass-reduce-motion', on ? 'on' : 'off')
}
// hydrate 时从 localStorage 还原（首帧前 inline 脚本同步设好，防闪烁）
```

```tsx
// 切换面板里加一个开关（与明暗/主题色并列）
<button
  role="switch"
  aria-checked={reduceMotion}
  aria-label="减少动效（压低入场与循环动画幅度）"
  onClick={() => setReduceMotion(!reduceMotion)}
  className={reduceMotion ? 'reduce-switch on' : 'reduce-switch'}
>
  <span className="reduce-thumb" />
</button>
<span className="text-xs text-text-tertiary">{reduceMotion ? '动效已减弱' : '动效完整'}</span>
```

### 降级行为（CSS + Motion 双侧）

**CSS 侧**（玻璃降级，见材质章）：`<html data-reduce-motion="on">` 下玻璃强制关 `backdrop-filter`、退回纯 tint（即使效果优先开着也降为纯 tint，与移动端同款降级）。

**Motion 侧**（运动类动效降级）：所有运动类动效读 `data-reduce-motion` 属性，开启时把入场/循环降为极轻或瞬时，**保留功能性反馈不删除**（状态点、聚焦环、加载指示仍要有视觉反馈，只是幅度极小或瞬时落定）。

```tsx
'use client'
import { useEffect, useState } from 'motion/react' // 或自管订阅 data-reduce-motion 属性

// 读取开关（订阅 <html data-reduce-motion>，而非 useReducedMotion()）
function useReduceMotion() {
  const [on, setOn] = useState(false)
  useEffect(() => {
    const el = document.documentElement
    const sync = () => setOn(el.getAttribute('data-reduce-motion') === 'on')
    sync()
    const ob = new MutationObserver(sync)
    ob.observe(el, { attributes: true, attributeFilter: ['data-reduce-motion'] })
    return () => ob.disconnect()
  }, [])
  return on
}
// reduce 为真时：duration 调小（如 0.7→0.2）、repeat 循环降为单次、视差/磁吸幅度归零、
// 装饰性常驻流光（sheen-sweep）return null；状态类（pulse/glow）保留但幅度减半
```

> **铁律**：组件里**不要用** Motion 的 `useReducedMotion()`（它读的是系统 `prefers-reduced-motion`，跨机不一致）——一律用上面的 `useReduceMotion()` 订阅 `<html data-reduce-motion>` 属性，与开关、CSS 降级同源。

### 减少动效自检子清单

- [ ] 提供「减少动效」开关，**默认关**（`<html>` 默认无 `data-reduce-motion="on"`）？
- [ ] 开关经 `setReduceMotion` → `document.documentElement.setAttribute('data-reduce-motion', ...)` + localStorage 持久化，有 `role="switch"` + `aria-checked` + 清晰态标签？
- [ ] **全篇不读系统 `prefers-reduced-motion`**（无 `@media (prefers-reduced-motion)`、无 `useReducedMotion()`）？运动类动效一律订阅 `data-reduce-motion` 属性？
- [ ] 开启后：入场/循环动效降为极轻或瞬时、视差/磁吸幅度归零、装饰性常驻流光关闭，但**功能性反馈保留**（状态点、聚焦环、加载仍有视觉反馈）？
- [ ] 玻璃在 `data-reduce-motion="on"` 下退回纯 tint（强制关 backdrop-filter）？

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
3. **搭玻璃组件**：导航 `.glass-strong` + 胶囊；卡片 `.glass.glass-sheen`；按钮/输入框/标签取对应玻璃模板（同样享有晶体面板质感）。**强调色派生值用 `var(--accent)` / `rgba(var(--accent-rgb), α)`，禁止写死色值。**
4. **加主题切换面板**：明暗 + 主题色均可用户切换，绑定 `setTheme`/`setAccent` 并刷新激活态；并加「效果优先」开关（默认关，绑定 `setEffectPriority`，开启激活折射）与「减少动效」开关（默认关，绑定 `setReduceMotion`）。
5. **加生命（七大维度 Motion 动效）**：① 加载（按场景 6 种变体）② 状态（pulse/breathe/glow/stream/shake）③ 入场（`whileInView` 错落，焦点先行）④ 滚动（reveal/视差/进度条）⑤ 指针（magnetic/tilt/ripple/sheen-follow，用 `useSpring`）⑥ 过渡（`AnimatePresence`/`layoutId`/grid 折叠/圆形揭露）⑦ 灵动（sheen-sweep 常驻主角 + 触发型点缀）。每屏一强多弱，同源时序。**运动类动效一律订阅 `data-reduce-motion` 开关（不读系统 `prefers-reduced-motion`）。**
6. **自检**：见下清单。

---

## 自检清单

- [ ] 主结构玻璃元素有半透 tint（透射）+ 边缘高光（描边 + inset 白色阴影 + 镜面 sheen）+ 浮起投影，质感三要素齐全？
- [ ] **氛围层零 `filter: blur()`，玻璃层默认纯 tint（零 backdrop-filter、零卷积），由「效果优先」开关（默认关）开启激活 `backdrop-filter: blur()+saturate()` 真折射（强 `blur(20px+)` 与 `filter:blur()` 球一律禁止）？小屏 / 「减少动效」开关下玻璃降级为纯 tint（关 backdrop-filter）？
- [ ] 按钮/输入框/标签**同样享有晶体面板质感**，未为性能降级为纯 tint？
- [ ] 背景氛围层是**单层 radial-gradient mesh**（非 `filter:blur()` 球），色斑取 `--aura-1~4` 且**允许缓慢流动**？
- [ ] 字体栈使用 Geist / Inter + 中文系统字体，数字 `tabular-nums`？
- [ ] 明暗（`data-theme`）与主题色（`data-accent`）两个维度均可切换，默认 light + mint？
- [ ] 所有强调色派生值用 `var(--accent)` / `rgba(var(--accent-rgb), α)`，无写死色值？切换主题色时氛围/按钮/聚焦环整体换肤？
- [ ] 动效全面用 Motion，覆盖七大维度，一个不少？
- [ ] 动效作用于 transform/opacity；尺寸过渡用 `scale` 或 `grid-template-rows 0fr→1fr`；**绝不用 `filter:blur()` 做动效**？
- [ ] pointermove/scroll 跟随用 `useMotionValue`+`useSpring`（已节流），无裸事件直写样式？
- [ ] hover 浮起 + 阴影可平滑过渡；入场 `whileInView` + `viewport={{ once: true }}` 落定？
- [ ] 装饰性常驻光效（边缘流光 sheen-sweep）**全页仅顶栏 1 处**；内容卡（主图/KPI/表格/告警/时间线）无装饰性常驻光效，只有状态类/触发类？
- [ ] 技术栈 = TS + React + Next.js + Tailwind + shadcn/ui + Radix + Motion；交互元素 5 态齐全，`focus-visible` ring 清晰？
- [ ] **效果优先开关**（默认关）存在，默认纯 tint 零 backdrop-filter，开启激活折射；开关经 `setEffectPriority` → `data-effect` 属性 + localStorage 持久化，有 `role="switch"` + `aria-checked` + 清晰态标签（性能优先 / 效果优先）？
- [ ] 语义化标签 + ARIA + 键盘可达 + 对比度达标；「减少动效」开关（默认关）兜底，**不读系统 `prefers-reduced-motion`**？
- [ ] 三档响应式合理，移动端抽屉可用，无横向滚动？

---

## 设计准则

- **玻璃是主角，背景是配角**：背景色斑服务于玻璃显形，不喧宾夺主；强调色只点睛（主按钮、链接、焦点、关键数据），不可大面积铺色。
- **靠透明度与模糊分层**，而非浓重色块：`tint-strong → tint → tint-soft` 表达层级。
- **高光永远是白色系**（亮色 `rgba(255,255,255,0.85)`，暗色 `rgba(255,255,255,0.18)`），这是玻璃折射光线的物理本质。
- **动效一强多弱**：**装饰性常驻光效全页仅 1 处，放顶栏**（边缘流光，整页"生命体征"锚点）；内容卡（主图/KPI/表格/告警/时间线）承载必须静读的数据，**严禁装饰性常驻光效**，只用状态类（在线/告警脉冲）与触发类（hover/click 即现即灭）；强感知来自编排节奏，不是堆砌。
- **动效只动合成层**：transform/opacity 走合成器，可放心无限循环（spinner/脉冲/光晕/流光/连接流光）；width/height/margin 以及 `filter` 等重排/重采样属性不入动画。
- **视觉质感优先**：玻璃质感、氛围流动、hover 阴影过渡一律保留；性能只做温和优化（优先合成层、`useSpring` 节流、合理 `will-change`），不为性能删减视觉与动效。

---

## 常见误区

| 误区 | 正确做法 |
|------|----------|
| 只做一种加载动效（到处同一个 spinner） | 按场景 6 种变体：按钮 loading/转圈/双圈/骨架屏/三点跳动/进度流光/不定进度条 |
| 状态变化无视觉反馈 | pulse/breathe/online-glow/connect-stream/shake，每个状态对应一种 |
| 为性能给按钮/输入框砍掉晶体面板质感 | 半透 tint + 高光 + 投影是高级感来源，所有元素同等享有；流畅靠合成层 + `useSpring`，不靠降级 |
| 给氛围色斑加 `filter: blur(80px)`，或给玻璃/遮罩加强 `backdrop-filter: blur(≥20px)` | **氛围球是大半径逐像素采样、最吃 GPU**——氛围改单层 radial-gradient mesh（渐变本就柔和）。**玻璃层默认纯 tint（零 backdrop-filter），由「效果优先」开关开启激活 `backdrop-filter: blur()+saturate()`**（随小屏/「减少动效」开关降级回纯 tint）；要禁的是强 `blur(20px+)` 与全屏遮罩模糊，那才是面积×半径²暴涨的性能黑洞 |
| 用 `@media (prefers-reduced-motion)` / `useReducedMotion()` 做动效降级 | 各机系统偏好不一、跨机效果不可控——改用应用内「减少动效」开关（`<html data-reduce-motion="on">`），用户显式切换、所有机器效果一致 |
| 入场/聚焦用 `filter: blur()` 做焦点模糊 | blur 动画逐帧采样，开销与背景球同级；焦点感改用 `scale` 收紧 + `opacity` 渐显 |
| 砍掉氛围色斑漂移变成静态背景 | 缓慢漂移（≥20s，只动整层 transform）是"活"的来源，常驻安全，保留 |
| hover 阴影强制瞬时切换（不过渡） | 少量元素的阴影过渡可平滑（Motion 调度），立体感更自然 |
| 动画用了 width/height/margin（每帧重排）或 `filter:blur()`（每帧重采样） | 只动 transform/opacity；尺寸过渡用 `scale` 或 grid `0fr→1fr`；焦点用 scale |
| 裸 pointermove/scroll 直写样式（掉帧） | 用 Motion `useMotionValue`+`useSpring`，内部已 rAF 节流 |
| 把强调色写死成具体色值 | 用 `rgba(var(--accent-rgb), α)`，否则切主题色时投影/聚焦环不跟随 |
| 玻璃放在纯白/纯灰平铺背景 | 必须铺 `.aura` 色斑，玻璃透出色彩才有生命（效果优先开时折射的正是这层流动色斑；默认纯 tint 下半透 tint 也透出这层色斑） |
| 只用半透 tint 缺边缘高光 | 必须加白色描边 + inset 白色阴影 + 镜面 sheen，否则只是"塑料片"非"液态玻璃"（质感来自折射高光，与 blur 半径无关，故纯 tint 也是液态玻璃） |
| 默认（效果优先关）还给 `.glass` 挂 `backdrop-filter` | **默认必须纯 tint（零 backdrop-filter）**——挂上就退回高开销、丢掉主流机流畅优势；折射只在效果优先开时激活 |
| 暗色主题把高光做成强白 | 暗色高光 `rgba(255,255,255,0.18)`，含蓄反光才真实 |
| 用线性 ease 做动效 | 按物理特性选曲线：位移 `--ease-liquid`，回弹 `--ease-spring`，入场落定 `--ease-out-expo` |
| 全屏所有元素都上强动效 | 每屏一强多弱，只给焦点元素强动效，其余克制衬托 |
| 在内容卡（主图/KPI/表格）上放装饰性常驻光效（如 sheen-sweep） | 装饰性常驻光效**全页仅顶栏 1 处**；内容卡承载必须静读的数据，只配状态类（在线/告警脉冲）与触发类（hover/click 即现即灭）光效 |
