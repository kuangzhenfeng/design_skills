---
name: ultra-design-pro
description: 当需要构建高质量、极具设计感、纯 2D 液态玻璃风格的前端界面（Dashboard、落地页、SaaS 控制台、可视化看板、品牌门户等）时使用。技术栈：TypeScript + React + Next.js (App Router) + Tailwind CSS + shadcn/ui + Radix + Motion (motion/react + framer-motion) + Zustand。五大取向 —— ① 液态玻璃（首要准则）：开启效果优先后，所有面板/卡片/弹窗用 `.glass` / `.glass-strong` DOM 玻璃 = backdrop-filter blur+saturate 折射 + 半透底色 + 菲涅尔顶部高光(sheen) + 边缘色散(rim) + 浮起投影，纯 CSS 实现真液态质感，零 3D 库。② 流星夜空 + 星尘交互背景：body 暗色夜空渐变兜底，Canvas 烘焙静态星空底(280 星点不每帧重绘)，DOM/CSS 流星群以 keyframes 沿自身轴线斜飞(头核+渐隐拖尾+glow，同时可见≤6 条、偶发优雅、速度舒缓 3~6s、随主题色换色)；鼠标移动 spawn 星尘/闪沙拖尾(粒子池+rAF)、点击径向爆裂闪光沙，全程 pointer-events:none 顶层不挡交互。③ 可调玻璃透明度（独家）：单一 CSS 变量 `--glass-alpha`(0~1.4)经 Zustand 滑块同时驱动背景色/饱和度/边框/高光/色散/投影全联动，clamp 非零下限保证"任何时候都是液态玻璃、只调浓重度"，最低档真透出后方流星星空。④ 效果优先开关（默认关）：**默认（关闭）= 纯 tint 玻璃——零 backdrop-filter、零卷积开销，玻璃退回半透底色 + 菲涅尔 sheen + 边缘 rim + 浮起投影，主流机/低端机绝对流畅、零掉帧**；Topbar 开启效果优先(`[data-effect="on"]`)后——blur/saturate 随 `--glass-alpha` 浮动(2~28px)、激活真折射，截图/演示最出彩但低端机可能掉帧。⑤ 减少动效开关（默认关）：**不读系统 `prefers-reduced-motion`**（各机偏好不一、跨机效果不可控），改由应用内显式开关(`<html data-reduce-motion="on">`)统一控制——开启后入场/循环/星尘/装饰流光降级或关闭、玻璃退回纯 tint，功能性反馈保留，所有机器效果一致。视觉优先 + 默认主流机流畅（默认纯 tint 零卷积、效果优先档 DPR≤2、粒子池封顶、合成层动画、流星可见封顶、Canvas 烘焙）。内置亮/暗双主题 + 7 主题色（默认暗色——夜空流星暗色最出彩；亮色流星=主题色偏灰 color-mix，绝不用白流星）。生成生产级、可直接运行的 TSX/CSS 代码，告别平庸毛玻璃。
---

# Ultra Design Pro — 流星夜空液态玻璃设计系统（纯 2D · 可调透明度 · 星尘交互 · 默认纯 tint / 效果优先完整液态玻璃）

## 概述

本 skill 用于构建**纯 2D、视觉优先**的高质感前端界面。界面是**浮在流星夜空前的液态玻璃面板群**：开启效果优先后，所有 UI 控件（导航、卡片、按钮、输入框、弹窗）用 DOM `backdrop-filter` 玻璃实现，带磨砂折射 + 菲涅尔高光 + 边缘色散 + 浮起投影；背景是暗色夜空里的偶发流星群 + 烘焙星空底，鼠标移动拖出星尘、点击迸发闪光沙。一切由用户滑块控制的「玻璃浓重度」统一调节，0~1.4 平滑联动所有层。

**首要准则：液态玻璃 + 流星夜空 + 可调透明度 + 效果优先开关 + 减少动效开关，五者缺一不可。** 玻璃要"液态"——不是普通毛玻璃，而是有折射质感（blur+saturate 折射、菲涅尔 sheen 高光、色散 rim 棱线、浮起投影）的半透面板；背景要"有生命"——夜空里偶发流星划过 + 静态星点垫底，绝不做死板的纯色底；透明度要"可控"——单一变量驱动所有层，最低档透出星空、最高档浓重磨砂，中间全程平滑；磨砂策略要"可切换"——**默认（关闭效果优先）= 纯 tint 玻璃（零 backdrop-filter、零卷积开销，主流机/低端机绝对流畅）**，效果优先开关开启后激活真折射（截图/演示最出彩）；动效要"可降级"——应用内「减少动效」开关（默认关，**不读系统 `prefers-reduced-motion`**）开启后压低运动类动效、关星尘、玻璃退回纯 tint，跨机器效果一致。

### 设计五原则

1. **液态玻璃（首要）**：UI 控件本身就是 DOM 玻璃（`.glass`/`.glass-strong`）。**默认（效果优先关）是纯 tint 玻璃**——半透底色 + 菲涅尔顶部 sheen + 边缘色散 rim + 浮起投影，零 backdrop-filter、零卷积开销；**效果优先开**则激活 `backdrop-filter: blur+saturate` 真折射，磨砂质感全开。质感来自这几层叠加，不是单一 backdrop-filter。
2. **背景流星夜空**：后景是 DOM/CSS 流星群 + Canvas 烘焙星空底（都在 3D 场景之外，纯 2D 层）。流星 = 细长发光线段，linear-gradient 头亮尾隐拖尾 + box-shadow glow，沿自身轴线（旋转角 = 飞行方向）斜飞，同时可见 ≤6 条、偶发优雅、速度舒缓。
3. **可调玻璃透明度**：单一 CSS 变量 `--glass-alpha`(0~1.4) 驱动所有玻璃层（背景色/saturate/边框/sheen/rim/投影）。clamp 给每层非零下限——任何时候都是玻璃（非空气），只调浓重度。最低档透出后方流星，最高档浓重磨砂。
4. **星尘交互反馈**：鼠标移动 spawn 星尘/闪沙拖尾，点击径向爆裂闪光沙。粒子池 + 单 rAF loop，pointer-events:none 顶层不挡交互。
5. **磨砂策略 + 动效均可切换（默认纯 tint / 动效完整）**：**默认（效果优先关）——纯 tint 玻璃，零 backdrop-filter、零卷积，主流机/低端机绝对流畅、零掉帧**；效果优先开关开启——blur/saturate 随浓重度浮动(2~28px)、激活真折射，演示/截图最出彩；「减少动效」开关（默认关，**不读系统 prefers-reduced-motion**）——开启后压低入场/循环动效、关星尘与装饰流光、玻璃退回纯 tint，功能性反馈保留。效果优先档 DPR≤2、粒子池封顶、流星可见封顶≤6、Canvas 星点烘焙不每帧重绘、全 transform/opacity 合成层动画。

---

## 技术栈约定

| 依赖 | 用途 | 必需 |
|------|------|------|
| TypeScript + React 18 + Next.js (App Router) | 工程基线，'use client' | ✅ 七项缺一不可 |
| Tailwind CSS | 原子类 + `@layer components` 玻璃类 | ✅ |
| shadcn/ui + Radix | 弹窗/下拉/Popover/Tooltip 等 | ✅ |
| Motion (motion/react + framer-motion) | 动效（useMotionValue/useSpring/AnimatePresence/motion.div） | ✅ |
| Zustand | 主题 + 玻璃透明度 + 效果优先开关状态桥接 | ✅ |

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
  mode: 'dark', accent: 'mint', glassAlpha: 1, effectPriority: false, reduceMotion: false,
  setMode: (mode) => { set({mode}); document.documentElement.setAttribute('data-theme', mode); localStorage.setItem('aurora-theme', mode) },
  setAccent: (accent) => { set({accent}); document.documentElement.setAttribute('data-accent', accent); localStorage.setItem('aurora-accent', accent) },
  setGlassAlpha: (n) => { /* 见下章 */ },
  setEffectPriority: (on) => { set({effectPriority: on}); document.documentElement.setAttribute('data-effect', on ? 'on' : 'off'); localStorage.setItem('aurora-effect-priority', on ? 'on' : 'off') },
  toggleEffectPriority: () => get().setEffectPriority(!get().effectPriority),
  setReduceMotion: (on) => { set({reduceMotion: on}); document.documentElement.setAttribute('data-reduce-motion', on ? 'on' : 'off'); localStorage.setItem('aurora-reduce-motion', on ? 'on' : 'off') },
  toggleReduceMotion: () => get().setReduceMotion(!get().reduceMotion),
  toggleMode: () => get().setMode(get().mode === 'dark' ? 'light' : 'dark'),
}))
```

> **关键**：主题/主题色/玻璃透明度/效果优先/减少动效全部经 Zustand → `document.documentElement.setAttribute` / `style.setProperty` → CSS 变量响应。换肤是 CSS 变量天然响应，无需 JS 重渲染。`<html>` 预置 inline 脚本在首帧前设好 `data-theme`/`data-accent`/`data-effect`/`data-reduce-motion`，防闪烁。

> **铁律：不读系统 `prefers-reduced-motion`**——各机系统偏好设置千差万别、开关位置隐蔽，跨机器/跨用户看到的效果不可控，无法保证一致体验。减少动效统一改由应用内显式开关控制（`<html data-reduce-motion="on">`，默认关），用户在 Topbar 点开才降级，所有机器效果完全一致。详见「减少动效开关」章。

### 亮色流星铁律

> **亮色主题下流星绝不可能是白色**（白流星在浅色天空里刺眼又廉价）。必须 `color-mix(in srgb, var(--accent) 45%, #8a8a99)` —— 随主题色切换的"主题色偏灰"。拖尾渐变与头核都走 `--meteor-color` 变量，改一个变量整体换色。

---

## 液态玻璃材质原理（核心 · 默认纯 tint / 效果优先完整液态玻璃）

### 为什么不是普通毛玻璃

普通毛玻璃 = `backdrop-filter: blur()` 一层了事 → 平、死板、无质感。

**液态玻璃 = 多层叠加**（默认纯 tint 版零 backdrop-filter，效果优先开激活真折射）：

| 层 | CSS | 作用 | 默认(效果优先关) | 效果优先开 |
|----|-----|------|:-:|:-:|
| ① 折射 | `backdrop-filter: blur(α) saturate(α)` | 折射后方 + 提饱和，玻璃"折"的底 | ❌ 关（零卷积） | ✅ blur 2~28px 浮动 |
| ② 半透底色 | `background: rgb(... / alpha)` | 玻璃本身的色调底，半透才见后方 | ✅ | ✅ |
| ③ 菲涅尔高光（sheen + rim 合并） | `.glass::before` 两层渐变 | 顶部斜向白色光泽带 + 对角 accent 色散 + 天蓝双色棱线，玻璃"亮"的灵魂 | ✅ | ✅ |
| ④ 浮起投影 | `box-shadow: inset白 + drop黑` | inset 白光 = 顶边高光；drop 黑 = 玻璃浮起 | ✅ | ✅ |

> **铁律：默认（效果优先关）= 纯 tint 玻璃，零 backdrop-filter。** 折射层（①）在默认下完全关闭——`backdrop-filter: none`，零卷积采样、零 GPU 折射重算，主流机/低端机绝对流畅、零掉帧。此时玻璃的液态质感由 ② 半透 tint + ③ 菲涅尔 sheen/rim 高光 + ④ 浮起投影三件套撑起：半透底色让后方流星/图表隐约透出，菲涅尔高光给玻璃"亮"的灵魂，浮起投影给玻璃"重量"。开启效果优先（`[data-effect="on"]`）才激活 ① 折射层，blur/saturate 随 `--glass-alpha` 浮动，真折射质感全开。
>
> **为什么 ultra-design-pro 的 `::before` 能装下 sheen + rim**：本 skill 把 sheen（顶部高光）和 rim（边缘色散）合并到一个 `::before`（多重 `background` 叠加），结构精简。`::before` 一个伪元素装两个高光层完全够用（都是定位 `inset:0` 的渐变叠加），不损失视觉效果。

```css
@layer components {
  /* 默认（无 data-effect="on"）= 纯 tint 玻璃：零 backdrop-filter、零卷积开销。
     玻璃质感由 半透 tint + ::before(sheen+rim) + 浮起投影 三件套撑起，主流机/低端机绝对流畅。 */
  .glass {
    position: relative;
    background: var(--glass-bg);
    border: 1px solid var(--glass-border);
    box-shadow: var(--shadow-inner-glass), var(--shadow-glass);
    overflow: hidden;
  }
  /* ::before = sheen + rim 合并到一个伪元素：顶部斜向白色光泽带 + 对角 accent 色散 + 天蓝双色棱线 */
  .glass::before {
    content: ''; position: absolute; inset: 0; border-radius: inherit;
    background:
      var(--glass-sheen),
      linear-gradient(180deg, rgba(255,255,255,0.14) 0%, transparent 28%),
      linear-gradient(115deg, transparent 0%, rgba(var(--accent-rgb),0.12) 26%, rgba(56,189,248,0.1) 52%, transparent 78%);
    background-blend-mode: normal, normal, screen;
    mix-blend-mode: screen; pointer-events: none;
    opacity: var(--glass-rim-alpha);
  }
  /* 效果优先模式（data-effect="on"）：激活真折射——blur/saturate 随 --glass-alpha 浮动到大半径。
     默认（关）无 backdrop-filter，零卷积；开启后才挂上。 */
  :root[data-effect="on"] .glass {
    -webkit-backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
    backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
  }
}
```

> **默认纯 tint 的玻璃质感来源**：半透 `--glass-bg` 让后方流星/图表隐约透出（"透"）；`::before` 的 sheen/rim 给菲涅尔折射高光与边缘色散棱线（"亮"）；`box-shadow` 的 inset 白光 + drop 黑投影给浮起重量（"重"）。这三件套与 `backdrop-filter` 折射无关，所以默认零 backdrop-filter 仍是"液态玻璃"，只是没有"磨砂折射"——开启效果优先才补上这一层。

---

## 可调玻璃透明度（核心 · 独家机制）

### 铁律：单一变量驱动所有层，clamp 非零下限

> **关键认知**：液态玻璃的"通透" ≠ "让玻璃消失"。必须让 `--glass-alpha` **同时驱动背景色/边框/sheen/rim/投影**（效果优先开还额外驱动 blur/saturate），且每层用 `clamp(下限, calc(下限 + 斜率 × α), 上限)` 保留**非零下限**——任何浓重度都是玻璃，只调浓淡。
>
> **注意（默认下无折射层）**：默认（效果优先关）没有 `backdrop-filter`，blur/saturate 这两列只在效果优先开（`data-effect="on"`）时存在并随 α 浮动；默认下玻璃的"通透"完全由半透 tint 承载。

| α（浓重度） | 含义 | 背景 alpha | blur（效果优先开浮动 / 默认关=无折射） | saturate（同左） | sheen | rim |
|-------------|------|-----------|------|----------|-------|-----|
| **0** | 最通透 | 0.05 | 2px / **无** | 1.1 / **无** | 0.35 | 0.45 |
| 0.5 | 通透液态 | 0.15 | 11.5px / **无** | 1.35 / **无** | — | — |
| **1**（默认） | 基准液态玻璃 | 0.25 | 20px / **无** | 1.6 / **无** | 0.62 | 0.78 |
| 1.4 | 最浓磨砂 | 0.33 | 27px / **无** | 1.8 / **无** | 0.85 | 1.0 |

### 完整 token（`globals.css` `:root`，暗色）

```css
--glass-alpha: 1;
/* 背景底色：α=0→0.03, α=1→0.18, α=1.4→0.28 */
--glass-bg: rgb(18 26 46 / clamp(0.03, calc(0.03 + 0.15 * var(--glass-alpha)), 0.28));
--glass-strong: rgb(22 32 56 / clamp(0.05, calc(0.05 + 0.2 * var(--glass-alpha)), 0.4));
--glass-soft: rgb(18 26 46 / clamp(0.02, calc(0.02 + 0.1 * var(--glass-alpha)), 0.2));

/* ===== 默认（无 data-effect="on"）：纯 tint 玻璃，无 backdrop-filter、零卷积 =====
   玻璃的"通透"完全由半透 --glass-bg 承载，"液态质感"由 ::before sheen/rim + 浮起投影撑起。
   默认不定义 --glass-blur / --glass-saturate（玻璃类默认也不挂 backdrop-filter），
   这两个变量只在效果优先模式下被引用（见下文效果优先章覆写）。 */

/* ===== 效果优先模式（[data-effect="on"]）：blur/saturate 随 α 浮动到大半径，激活真折射 =====
   效果优先档：blur 在 2~24px、blur-strong 在 3~28px、saturate 在 1.1~1.85 随浓重度联动。
   浮动版本保留在此作效果优先覆写的参考（实际覆写见下文效果优先章）。 */
/* 浮动 blur 参考值（仅 data-effect="on" 生效）：
   --glass-blur: clamp(2px, calc(2px + 14px * var(--glass-alpha)), 24px);
   --glass-blur-strong: clamp(3px, calc(3px + 17px * var(--glass-alpha)), 28px);
   --glass-saturate: clamp(1.1, calc(1.1 + 0.5 * var(--glass-alpha)), 1.85); */

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

/* SheenSweep 流光专用高光色——主题自适应，【非】--glass-alpha 驱动，放 base 层随明暗切换。
   暗色 0.18 含蓄：深色流星夜空上写死亮白(0.6)会刺眼、喧宾夺主，必须压低；
   亮色 0.85 明亮：浅色背景上流光要够显眼才看得见。 */
--glass-highlight: rgba(255, 255, 255, 0.18);   /* 暗色 :root */
/* 亮色 [data-theme='light'] 下覆写为：
   --glass-highlight: rgba(255, 255, 255, 0.85);  /* 亮色流光要够显眼才看得见 */ */
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
  {/* 效果优先开关：默认关（纯 tint 玻璃，零卷积、主流机流畅），开启 → 激活 backdrop-filter 真折射（截图最出彩） */}
  <div className="flex items-center gap-2">
    <button role="switch" aria-checked={effectPriority} aria-label="效果优先（激活 backdrop-filter 折射）"
      onClick={() => toggleEffectPriority()} className={effectPriority ? 'effect-switch on' : 'effect-switch'}>
      <span className="effect-thumb" />
    </button>
    <span className="text-xs text-text-tertiary">{effectPriority ? '效果优先' : '性能优先'}</span>
  </div>
</Popover.Content>
```

### 调参心法（踩过的坑）

> **❌ 错 1：只乘背景色 alpha**（`calc(0.42 * α)`）→ α=0 时玻璃失去色调底，"还是不透明"。
> **❌ 错 2：所有层都 ×α 且下限归零** → α=0 玻璃完全消失（变空气，不是玻璃），且滑条稍微一拉就极差。
> **✅ 对**：每层 `clamp(非零下限, calc(下限 + 斜率×α), 上限)`。背景/blur 下限低，折射层(saturate/sheen/rim)下限适中（始终保留液态光泽）。
>
> **默认 vs 效果优先的分工**：默认（关）= 纯 tint，通透完全由半透 `--glass-bg` 承载、零 backdrop-filter；效果优先开 = 额外激活 blur/saturate 折射，随 α 浮动。两者都靠 `--glass-alpha` 驱动背景色/边框/sheen/rim/投影（浓重度）。

### 可调透明度自检子清单

- [ ] `--glass-alpha` 同时驱动背景色/边框/sheen/rim/投影（效果优先开还额外驱动 blur/saturate）？
- [ ] 每层用 `clamp(非零下限, ...)`，α=0 时仍是玻璃（非消失）？
- [ ] 默认（效果优先关）玻璃无 `backdrop-filter`，通透由半透 `--glass-bg` 承载？
- [ ] α=1 默认档背景 alpha ≤ 0.25（不是磨砂板）？
- [ ] 折射层(sheen/rim)下限 ≥ 0.3（始终有液态光泽）？
- [ ] 滑块经 store → `documentElement.style.setProperty('--glass-alpha', n)`，持久化到 localStorage？
- [ ] 滑块范围 0~1.4、step 0.05、显示百分比、有重置？

---

## 效果优先开关（折射开关 · 默认关）

### 两档策略对比

| 维度 | 默认（效果优先关 = 纯 tint 玻璃） | 效果优先（`data-effect="on"`） |
|------|---------------|------------------------------|
| `backdrop-filter` | **无**（零卷积） | blur+saturate 随 α 浮动 |
| blur 半径 | **无折射** | 随 α 浮动 **2~28px**（大半径磨砂） |
| saturate | **无折射** | 随 α 浮动 1.1~1.85 |
| 半透 tint + sheen + rim + 投影 | ✅ 全保留 | ✅ 全保留 |
| GPU 开销 | **极低**（零 backdrop 卷积） | 高（blur² × 面积，R² 项） |
| 适用 | **生产默认**、主流机、低端机、长开场景 | 截图、演示、出彩时刻、高端机 |
| `--glass-alpha` 驱动 | 背景色/边框/sheen/rim/投影（浓重度） | 同左 + blur/saturate（折射也联动） |

### 核心覆写（`globals.css`）

```css
@layer base {
  /* 效果优先模式（默认关）：开启后——
     激活 backdrop-filter，blur/saturate 随 --glass-alpha 浮动到大半径，真折射质感全开。
     放在 :root 之后，:root[data-effect="on"] 特异性更高。
     默认（无 data-effect="on"）不定义这两个变量、玻璃类也不挂 backdrop-filter = 纯 tint 零卷积。 */
  :root[data-effect="on"] {
    --glass-blur: clamp(2px, calc(2px + 14px * var(--glass-alpha)), 24px);
    --glass-blur-strong: clamp(3px, calc(3px + 17px * var(--glass-alpha)), 28px);
    --glass-saturate: clamp(1.1, calc(1.1 + 0.5 * var(--glass-alpha)), 1.85);
  }
}
/* 激活 backdrop-filter（见液态玻璃材质原理章的 :root[data-effect="on"] .glass 规则）：
   仅在 data-effect="on" 下挂 backdrop-filter；默认玻璃类无此属性，纯 tint 零卷积。 */
```

> **关键认知 · 默认与效果优先都仍是液态玻璃**：两档只差一个"折射层"（backdrop-filter），液态玻璃的其他要素（菲涅尔 sheen 高光、边缘 rim 色散、浮起投影、半透 tint）两档完全相同、都随 `--glass-alpha` 联动。所以无论开不开效果优先，玻璃始终是液态玻璃——默认是"零折射的液态玻璃"（通透由半透 tint 承载），效果优先开补上"折射层"（后方内容被 blur+saturate 柔化）。默认档零 backdrop-filter、零卷积开销，主流机/低端机绝对流畅；效果优先档激活真折射、截图最出彩，但低端机可能掉帧。

### Store + 开关

```ts
// src/store/theme.ts
// effectPriority 默认 false —— 默认（纯 tint 玻璃，零 backdrop-filter，主流机/低端机绝对流畅）
effectPriority: false,

setEffectPriority: (on) => {
  set({ effectPriority: on });
  // 关键：设 <html data-effect="on"> 触发 CSS 变量覆写 + backdrop-filter 挂载（换肤走 CSS 变量天然响应，无 JS 重渲染）
  document.documentElement.setAttribute('data-effect', on ? 'on' : 'off');
  localStorage.setItem('aurora-effect-priority', on ? 'on' : 'off');
},
toggleEffectPriority: () => get().setEffectPriority(!get().effectPriority),
// hydrate 时从 localStorage 还原 + document.documentElement.setAttribute('data-effect', ...)
```

### 效果优先自检子清单

- [ ] 默认**关**（`effectPriority: false`、`<html>` 默认无 `data-effect="on"`）= 纯 tint 玻璃（无 backdrop-filter、零卷积）？
- [ ] 开启后 `:root[data-effect="on"]` 定义 blur/saturate 随 α 浮动(2~24px / 1.1~1.85)，`.glass` 挂上 backdrop-filter 激活真折射？
- [ ] 两档液态玻璃要素（sheen/rim/投影/tint）相同，`--glass-alpha` 两档都驱动浓重度？
- [ ] 开关经 store → `documentElement.setAttribute('data-effect', ...)` + localStorage 持久化，有 `role="switch"` + `aria-checked`？
- [ ] Topbar 开关有清晰态标签（性能优先 / 效果优先）？

### 错 vs 对（折射开关）

```
❌ 错：默认（效果优先关）下还给 .glass 挂 backdrop-filter
       → 默认本该零卷积（纯 tint），挂上又退回高开销，丢掉默认的主流机流畅优势

✅ 对：默认 = 纯 tint 玻璃（零 backdrop-filter），通透由半透 tint 承载，主流机/低端机绝对流畅
       效果优先 = 激活 backdrop-filter（blur 浮动大半径），激活真折射，截图最出彩
       两档都是液态玻璃，只差折射层，按设备/场景切换
```

---

## 减少动效开关（默认关 · 不读系统偏好）

### 为什么不用 `prefers-reduced-motion`

`prefers-reduced-motion` 读的是操作系统级「减少动态效果」开关，存在三个不可控问题：

1. **跨机不一致**：不同 OS（macOS/Windows/iOS/Android）的开关位置、默认值、联动范围各异，部分老版本或定制 ROM 行为不同——同一份代码在不同机器上动效时有时无，演示与交付效果无法保证。
2. **用户无感**：系统级开关多数用户不知道、也不会设，等于降级几乎永远不触发；真需要降级的用户（晕动症、低性能机）反而够不到。
3. **与本系统诉求不对齐**：系统「减少动效」常同时关过渡、滚动惯性、窗口动画，与本系统「只压运动类动效、保留状态反馈」不对齐。

因此 ultra-design-pro **用应用内显式开关替代**：`<html data-reduce-motion="on">`，默认关，用户在 Topbar 点击切换，跨所有机器效果完全一致。

### Store + 开关（复用主题 store）

```ts
// src/store/theme.ts —— reduceMotion 默认 false
reduceMotion: false,
setReduceMotion: (on) => {
  set({ reduceMotion: on });
  // 设 <html data-reduce-motion="on"> 触发 CSS 降级 + 组件订阅
  document.documentElement.setAttribute('data-reduce-motion', on ? 'on' : 'off');
  localStorage.setItem('aurora-reduce-motion', on ? 'on' : 'off');
},
toggleReduceMotion: () => get().setReduceMotion(!get().reduceMotion),
// hydrate 时从 localStorage 还原 + setAttribute('data-reduce-motion', ...)
```

```tsx
// Topbar 里的开关（与效果优先开关并列）
<div className="flex items-center gap-2">
  <button role="switch" aria-checked={reduceMotion}
    aria-label="减少动效（压低入场、循环、星尘与装饰流光）"
    onClick={() => toggleReduceMotion()}
    className={reduceMotion ? 'reduce-switch on' : 'reduce-switch'}>
    <span className="reduce-thumb" />
  </button>
  <span className="text-xs text-text-tertiary">{reduceMotion ? '动效已减弱' : '动效完整'}</span>
</div>
```

### 降级行为（CSS + 运动类动效 + 星尘 + 流星 四侧）

**① CSS 侧（玻璃降级）**：`<html data-reduce-motion="on">` 下玻璃关 `backdrop-filter`、退回纯 tint（即使效果优先开着，reduce-motion 也强制降为纯 tint 玻璃）。

```css
@layer components {
  /* 减少动效强制纯 tint：即使效果优先开着，reduce-motion 也强制关掉 backdrop-filter（零折射、零卷积）。 */
  :root[data-reduce-motion="on"] .glass,
  :root[data-reduce-motion="on"] .glass-strong,
  :root[data-reduce-motion="on"] .glass-soft { -webkit-backdrop-filter: none; backdrop-filter: none; }
}
```

**② 运动类动效**：所有运动类组件读 `reduceMotion`（Zustand selector 或订阅 `data-reduce-motion` 属性），开启时入场/循环降为极轻或瞬时、视差/磁吸幅度归零、装饰性常驻流光（`SheenSweep`）直接 `return null`。**保留功能性反馈**：状态点（在线/告警脉冲）、聚焦环、加载指示仍有视觉反馈，只是幅度极小或瞬时落定。

```ts
// src/lib/motion.ts —— 订阅开关（非 useReducedMotion()，不读系统偏好）
export function useReduceMotion() {
  return useThemeStore((s) => s.reduceMotion);  // 或订阅 <html data-reduce-motion> 属性
}
```

**③ 星尘交互**：`reduceMotion` 为真时 `CursorMeteor` 不挂 `pointermove`/`pointerdown` 监听（零开销、零粒子）。

**④ 流星/氛围**：流星密度降到 2 条、`aura` 漂移与流星飞行可降为静态或极慢；背景仍是流星夜空（身份不丢），只是「不动」。

> **铁律**：组件里**不要用** Motion 的 `useReducedMotion()`（读系统 `prefers-reduced-motion`，跨机不一致）——一律读 Zustand `reduceMotion` 或订阅 `data-reduce-motion` 属性，与开关、CSS 降级同源。

### 减少动效自检子清单

- [ ] 提供「减少动效」开关，**默认关**（`reduceMotion: false`、`<html>` 默认无 `data-reduce-motion="on"`）？
- [ ] 开关经 store → `data-reduce-motion` 属性 + localStorage 持久化，有 `role="switch"` + `aria-checked` + 清晰态标签？
- [ ] **全篇不读系统 `prefers-reduced-motion`**（无 `@media (prefers-reduced-motion)`、无 `useReducedMotion()`）？
- [ ] 开启后：入场/循环/星尘/装饰流光降级或关闭，玻璃退回纯 tint，但功能性反馈（状态点/聚焦环/加载）保留？

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
| 减少（动效开关） | 2 |

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
- 尊重「减少动效」开关（开启时不挂星尘监听）——**不读系统 `prefers-reduced-motion`**，改用应用内开关（跨机一致，见「减少动效开关」章）
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
- [ ] 「减少动效」开关开启时不挂星尘监听（不读系统 prefers-reduced-motion）？

---

## 默认 Favicon（流星夜空玻璃方 · 消除 404 + 品牌一致）

Next.js App Router 默认无 favicon，`GET /favicon.ico` 必然 404（验收清单"无 console error"的常见扣分点）。工程**必须自带默认 favicon**，与流星夜空液态玻璃语言一致。

### 配方：深空夜底 + 流星划过 + 极光" A "（SVG，零外链）

`src/app/icon.svg`（Next.js App Router 自动注入 `<link rel="icon">`，零路由代码）：

```svg
<svg width="32" height="32" viewBox="0 0 32 32" fill="none" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="sky" x1="0" y1="0" x2="32" y2="32" gradientUnits="userSpaceOnUse">
      <stop stop-color="#05070F"/>
      <stop offset="1" stop-color="#0B1426"/>
    </linearGradient>
    <linearGradient id="meteor" x1="0" y1="0" x2="1" y2="1" gradientUnits="objectBoundingBox">
      <stop stop-color="#00D4AA" stop-opacity="0"/>
      <stop offset="1" stop-color="#BAE6FD"/>
    </linearGradient>
  </defs>
  <rect width="32" height="32" rx="8" fill="url(#sky)"/>
  <circle cx="24" cy="7" r="1.1" fill="#FFFFFF" opacity="0.9"/>
  <circle cx="6" cy="26" r="0.8" fill="#FFFFFF" opacity="0.7"/>
  <path d="M5 3 L13 11" stroke="url(#meteor)" stroke-width="1.6" stroke-linecap="round"/>
  <rect width="32" height="32" rx="8" fill="white" fill-opacity="0.06"/>
  <path d="M9.5 22 L16 9 L22.5 22 M12 17 H20"
        stroke="#00D4AA" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
</svg>
```

### 实现要点（铁律）

- **放 `src/app/icon.svg`**：Next.js 13.3+ App Router 自动注入图标 `<link>`，零配置。**不要**手写 `<link>`、不要放 `public/favicon.ico`（App Router 优先用 `app/icon.*`）。
- **纯 SVG，零外链**：内联 `linearGradient`，无字体/图片外部依赖（呼应"CSS 变量兜底无外部资源"铁律）。体积 < 1KB。
- **与设计语言同源**：深空夜底（`#05070F`→`#0B1426`，即 `--sky-bg`）+ 流星（accent 渐变拖尾）+ 星点 + 半透明白覆盖（`fill-opacity="0.06"`，呼应液态玻璃高光）+ 极光"A"（Aurora 首字母，accent 描边）。**favicon 是"缩小到 32px 的整页缩影"**。
- **随主题色换色（可选）**：默认 Mint 流星；若用户指定品牌色，把流星与"A"字母的 `#00D4AA`/`#BAE6FD` 换成该主题色及其辉光色。底色始终深空（夜空是 ultra-design-pro 的身份底）。
- **`apple-icon.png` 可选**：iOS 主屏需 PNG，可同 SVG 渲染成 `src/app/apple-icon.png`（180×180）；非必需。

### 验收

- [ ] `src/app/icon.svg` 存在，`GET /icon` 200、无 `/favicon.ico` 404？
- [ ] favicon 是深空夜底 + 流星，与流星夜空语言一致，非随机占位图？

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

> **装饰性常驻光效铁律**：`SheenSweep`（玻璃边缘流光）是**纯装饰**动效，**全页仅顶栏 1 处**（顶栏 `sticky` 贯穿全页、不承载密集数据，是整页"生命体征"锚点）。内容卡（主图/KPI/表格/告警/时间线）承载**必须静读**的数据，**严禁**装饰性常驻光效，只用状态类（在线/告警脉冲/异常呼吸）与触发类（hover/click 即现即灭）光效。流星夜空与星尘交互是 ultra-design-pro 的**身份级背景**（状态/氛围层，非前景内容卡），不受此约束——它们服务于"夜空有生命"的设定，是氛围而非与数据争抢注意力的装饰。

> **流光亮度铁律（深色夜空关键 · 含装饰常驻 + hover 触发两类）**：**任何**流光/sheen sweep——无论是顶栏装饰性常驻 `SheenSweep`，还是内容卡（KPI 卡/主图卡等）hover 触发的一过性流光——颜色**必须**用 `var(--glass-highlight)`（主题自适应：暗色 `rgba(255,255,255,0.18)` 含蓄 / 亮色 `rgba(255,255,255,0.85)` 明亮），**禁止**写死 `rgba(255,255,255,0.6)` **或** `rgba(var(--glass-sheen-rgb), 0.5)`（后者 = 暗色下 0.5 半透明白，同样过亮刺眼）。理由：ultra-design-pro 默认暗色 + 流星夜空背景，写死亮白流光（无论 `0.6` 还是 `0.5` 量级）在深色星空上**过亮刺眼、喧宾夺主**，压到 0.18 才含蓄优雅；切到亮色主题时同一变量自动抬到 0.85 显眼可见——一条变量管两种主题、管两类流光。`--glass-highlight` 放在 `@layer base` 的明/暗作用域里，**不参与** `--glass-alpha` 联动（流光亮度只随主题明暗，不随玻璃浓淡）。
>
> ⚠️ **反例（bug 复现）**：KPI 卡 hover 触发的 sheen sweep 写成 `linear-gradient(90deg, transparent, rgba(var(--glass-sheen-rgb), 0.5) 50%, transparent)` → 暗色下 0.5 半透明白在深色卡片上过亮，"卡片被选中时高光太亮"。正确：用 `var(--glass-highlight)`（暗色 0.18）。

```tsx
// src/components/motion/primitives.tsx —— 顶栏专用装饰性常驻流光（全页仅 1 处，内容卡禁用）
import { motion } from 'motion/react'
import { useReduceMotion } from '@/lib/motion'  // 订阅 <html data-reduce-motion>（非系统 prefers-reduced-motion）

export function SheenSweep({ className, duration = 5 }: { className?: string; duration?: number }) {
  const reduce = useReduceMotion();
  if (reduce) return null;   // 减少动效开关降级：关掉装饰流光
  return (
    <span aria-hidden className={`pointer-events-none absolute inset-y-0 left-0 w-2/5 ${className ?? ''}`}
      style={{
        // ★ 主题自适应高光：暗色 0.18 含蓄 / 亮色 0.85 明亮（var(--glass-highlight)），不写死亮白
        background: 'linear-gradient(90deg, transparent, var(--glass-highlight) 50%, transparent)',
        transform: 'skewX(-18deg)',
      }}>
      <motion.span className="block h-full w-full"
        style={{ background: 'linear-gradient(90deg, transparent, var(--glass-highlight) 50%, transparent)' }}
        animate={{ x: ['-120%', '320%'], opacity: [0, 0.7, 0.7, 0] }}
        transition={{ duration, repeat: Infinity, ease: 'easeInOut', times: [0, 0.2, 0.8, 1] }} />
    </span>
  );
}
```

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
- 「减少动效」开关（默认关）全局降级：入场/循环/星尘/装饰流光降级或关闭（不读系统 `prefers-reduced-motion`，跨机一致）

---

## 响应式

- 断点：桌面 ≥1280 / 平板 768–1279 / 移动 <768
- 流星密度三档(≤6/≤4/≤3)
- 默认（效果优先关）玻璃本就零 backdrop-filter，移动端无额外折射开销；效果优先开时小屏可自动降级回纯 tint（移动端 backdrop-filter 相对昂贵）

---

## 执行流程

1. 搭 `globals.css` 双维度 token（明暗 × 7 色 + 玻璃 alpha token + 流星/星空 token）——默认不定义 `--glass-blur`/`--glass-saturate`（纯 tint 玻璃），仅 `:root[data-effect="on"]` 覆写 blur/saturate 浮动
2. 写 `.glass` / `.glass-strong` 组件类（**默认纯 tint：半透底色 + `::before` 合并 sheen/rim + 浮起投影，无 backdrop-filter**；`:root[data-effect="on"] .glass` 挂 backdrop-filter 激活真折射；`--glass-alpha` 联动背景色/边框/sheen/rim/投影）
3. 写 Zustand store（mode/accent/glassAlpha/effectPriority/reduceMotion + setGlassAlpha 设 CSS 变量 + setEffectPriority 设 data-effect + setReduceMotion 设 data-reduce-motion + 持久化）
4. 搭背景层：StarfieldBackground(Canvas) + MeteorLayer(DOM) + CursorMeteor(星尘)
5. 搭前景 UI（Sidebar/Topbar/KpiCard/图表/表格），全用 `.glass`/`.glass-strong`
6. Topbar 加五个开关/面板：明暗切换 / 主题色 / 玻璃透明度滑块 / **效果优先开关**（默认关） / **减少动效开关**（默认关）
7. 8 模块全落地 + 真实数据
8. `next build` + `next start` 验证 HTTP 200、0 console error、滑块/流星/星尘/效果开关/减少动效开关交互正常

---

## 自检清单

**液态玻璃**
- [ ] `.glass` = **默认纯 tint**（半透底色 + `::before` 合并 sheen/rim + 浮起投影，**无 backdrop-filter**），`data-effect="on"` 时挂 `backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate))` 激活真折射？
- [ ] 默认玻璃**零 `backdrop-filter`**（零卷积开销），开启效果优先才激活折射？
- [ ] 绝无 Three.js/R3F/Drei/postprocessing/troika 依赖？

**可调透明度**
- [ ] `--glass-alpha` 驱动背景色/边框/sheen/rim/投影（效果优先开还额外驱动 blur/saturate），每层 clamp 非零下限？
- [ ] 默认（效果优先关）玻璃无 backdrop-filter，通透由半透 `--glass-bg` 承载？
- [ ] 滑块经 store 设 CSS 变量 + localStorage 持久化？

**效果优先开关（默认关）**
- [ ] Topbar 有效果优先开关，**默认关**（纯 tint 玻璃，零 backdrop-filter、主流机/低端机绝对流畅）？
- [ ] 开启后 `:root[data-effect="on"]` 定义 blur/saturate 随 α 浮动(2~24px / 1.1~1.85)，`.glass` 挂上 backdrop-filter 激活真折射？
- [ ] 两档液态玻璃要素（sheen/rim/投影/tint）相同，`--glass-alpha` 两档都驱动浓重度？
- [ ] 开关经 store → `data-effect` 属性 + localStorage 持久化，有 `role="switch"` + `aria-checked`？

**背景流星夜空**
- [ ] Canvas 烘焙星空 + DOM 流星群沿自身轴线飞(rotate+translateX 同变量)？
- [ ] 流星 ≤6 偶发、3~6s 舒缓、亮色主题色偏灰(非白)？

**星尘交互**
- [ ] 移动拖尾 + 点击爆裂，单 rAF + 粒子池复用，顶层 pointer-events:none？
- [ ] 「减少动效」开关开启时不挂星尘监听（不读系统 prefers-reduced-motion）？

**减少动效开关（默认关 · 不读系统偏好）**
- [ ] Topbar 有「减少动效」开关，**默认关**（`<html>` 默认无 `data-reduce-motion="on"`）？
- [ ] **全篇不读系统 `prefers-reduced-motion`**（无 `@media (prefers-reduced-motion)`、无 `useReducedMotion()`）？运动类动效一律订阅 `data-reduce-motion` 属性 / Zustand `reduceMotion`？
- [ ] 开关经 store → `data-reduce-motion` 属性 + localStorage 持久化，有 `role="switch"` + `aria-checked` + 清晰态标签（动效完整 / 动效已减弱）？
- [ ] 开启后：入场/循环/星尘/装饰流光（SheenSweep）降级或关闭，玻璃退回纯 tint（强制关 backdrop-filter），但**功能性反馈保留**（状态点/聚焦环/加载仍有视觉反馈）？

**主题系统**
- [ ] 双维度(明暗×7色)，Zustand → data-* 属性 → CSS 变量，默认暗色？

**装饰性常驻光效**
- [ ] `SheenSweep`（玻璃边缘流光）**全页仅顶栏 1 处**；内容卡（主图/KPI/表格/告警/时间线）无装饰性常驻光效，只有状态类/触发类？（流星夜空/星尘作为背景氛围层不受此约束）
- [ ] `SheenSweep` 流光颜色用 `var(--glass-highlight)`（暗 0.18 / 亮 0.85 自适应），**未写死** `rgba(255,255,255,0.6)`？（深色夜空流光过亮 = 扣分项）
- [ ] **所有流光/sheen sweep**（顶栏装饰常驻 + 内容卡 hover 触发）颜色都走 `var(--glass-highlight)`，**未写死** `rgba(var(--glass-sheen-rgb), 0.5)` 或 `rgba(255,255,255,0.6)`？（内容卡"被选中时高光太亮"是暗色下用 `--glass-sheen-rgb` 写死 0.5 的典型 bug）
- [ ] `--glass-highlight` 在 base 层明/暗作用域定义、**不参与** `--glass-alpha` 联动？

---

## 设计准则

1. **液态玻璃优先**：UI 控件全是 `.glass`，多层叠加出质感（半透 tint + sheen + rim + 投影，默认零折射；效果优先开补 backdrop-filter 折射），非单一 blur。
2. **一强多弱（装饰性常驻光效仅顶栏）**：`SheenSweep` 边缘流光全页仅放顶栏 1 处；内容卡承载必须静读的数据，只用状态类（在线/告警脉冲）与触发类（hover/click）光效；强感知来自编排节奏，不是堆砌。
3. **强调色点睛**：accent 只用在 CTA/选中态/数据高亮，不大面积铺。
4. **颜色走变量/store**：绝不在组件里写死色值，全读 CSS 变量或 Zustand。
5. **折射可切换（默认纯 tint / 效果优先完整液态玻璃）**：默认（效果优先关）= 纯 tint 玻璃（零 backdrop-filter、主流机/低端机绝对流畅）；效果优先开 = 激活 backdrop-filter 真折射（截图最出彩）；「减少动效」开关（默认关，**不读系统 prefers-reduced-motion**）压低运动类动效、关星尘、强制玻璃退纯 tint。流畅靠默认零折射而非削减视觉。
6. **绝不白屏**：CSS 变量兜底、无外部资源依赖（无字体/模型外链）。

---

## 常见误区

| 误区 | 正确做法 |
|------|----------|
| 默认（效果优先关）还给 `.glass` 挂 `backdrop-filter` | **默认必须纯 tint（零 backdrop-filter）**——挂上就退回高开销、丢掉主流机流畅优势；折射只在效果优先开时激活 |
| 普通 `backdrop-filter: blur()` 一层了事（效果优先开时） | 多层叠加（backdrop-filter 折射 + tint + sheen + rim + 投影）才是液态玻璃 |
| 只降背景 alpha 调透明度 | `--glass-alpha` 同时驱动背景色/边框/sheen/rim/投影（效果优先开还驱动 blur/saturate），且 clamp 非零下限 |
| 透明度下限归零(α=0 玻璃消失) | clamp 非零下限——任何时候都是玻璃，只调浓重度 |
| 透明度下限太高(默认像磨砂板) | α=1 背景应 ≤0.25 |
| `translate(vw,vh)` 移动流星 | `rotate(angle) translateX(distance)` 沿自身轴线飞 |
| 流星速度太快(1~2s) | 舒缓 3~6s，偶发优雅 |
| 亮色主题用白流星 | `color-mix(accent 45%, 灰)` 主题色偏灰 |
| 每帧重绘 Canvas 星点 | 烘焙到 offscreen，仅 resize 重绘 |
| 每粒子一个 timer | 单 rAF loop + 粒子池复用 |
| 引入 Three.js 做玻璃/流星 | 纯 2D DOM+CSS+Canvas，零 3D 依赖 |
| 星尘拖尾挡交互 | `pointer-events:none` + 顶层 z-index |
| 在内容卡（主图/KPI/表格）上放装饰性常驻光效（如 SheenSweep） | 装饰性常驻光效**全页仅顶栏 1 处**；内容卡承载必须静读的数据，只配状态类（在线/告警脉冲）与触发类（hover/click 即现即灭）；流星夜空/星尘作为背景氛围层不受此约束 |
| SheenSweep 流光写死亮白 `rgba(255,255,255,0.6)` | 用 `var(--glass-highlight)`（暗色 0.18 含蓄 / 亮色 0.85 明亮）随主题明暗自适应——深色流星夜空上亮白流光过亮刺眼 |
| 内容卡 hover 触发的 sheen sweep 写死 `rgba(var(--glass-sheen-rgb), 0.5)`（= 暗色 0.5 半透明白） | 同样改用 `var(--glass-highlight)`（暗色 0.18）。"卡片被选中/悬停时高光太亮"就是暗色下把白色 sheen 写死 0.4~0.5 的典型 bug——半透明白在深色玻璃上比顶栏流光更扎眼 |
| 用 `@media (prefers-reduced-motion)` / `useReducedMotion()` 做动效降级 | 各机系统偏好不一、跨机效果不可控——改用应用内「减少动效」开关（`<html data-reduce-motion="on">`，默认关），用户显式切换、所有机器效果一致 |
```
