---
name: ultra-design
description: 当需要构建高质量、极具设计感的前端界面（网页、落地页、Dashboard、组件、可视化工具、应用 UI、海报等）时使用。输出苹果 Liquid Glass（液态玻璃）设计语言 — 半透明玻璃材质、动态边缘高光、镜面反射。**承诺一套完整的动效体系**：加载动效（转圈/双圈/三点跳动/骨架屏多层流光/进度流光/按钮 loading）、状态动效（心跳脉冲/呼吸/在线光晕/连接流光）、入场动效（错落浮现/模糊聚焦/缩放弹出/侧向滑入/翻转）、滚动动效（滚动显现/视差/滚动进度条）、指针动效（磁吸/3D 倾斜/涟漪/光泽跟随）、过渡动效（FLIP 重排/折叠展开/tab 切换/圆形揭露换肤）、灵感动效（边缘流光/光泽划过/脉冲环/悬停浮起/数字滚动/涟漪/抖动/光标闪烁/图标弹跳/下划线生长/徽章弹现/聚焦环呼吸/光晕脉冲等约 18 种）。全部动效只动 transform/opacity/filter（合成层），因此完整实现也不掉帧——流畅靠「只动合成层 + 该砍则砍」，而非删减动效。该砍则砍只针对昂贵装饰（氛围色斑常驻漂移、box-shadow 动画、细碎元素多余 blur），绝不动任何功能性/体验性动效一刀切。集显上也能 60fps，无需任何运行时检测或降级方案。默认亮色 + 薄荷绿（#00D4AA）主题，内置亮/暗双主题切换与多主题色切换（两个维度正交、任意组合）。生成生产级、可直接运行的 HTML/CSS/JS 或 React 代码，告别通用 AI 审美。
---

# Ultra Design — Liquid Glass 设计系统

## 概述

Ultra Design 是一套基于苹果 **Liquid Glass（液态玻璃）** 设计语言的工业级前端规范。核心理念：**界面如同真实的液态玻璃 — 透、亮、有重量、有动感；它的动感来自一套「完整动效体系」，覆盖加载、状态、入场、滚动、指针、过渡、灵动点缀七大维度，一个不少。**

参考 `frontend-design` skill 的设计思维：**先锁定一个极致的美学方向，再以精度执行**。本 skill 锁定的方向只有一个 —— 高感知度的 Liquid Glass，且**动效是必需品而非装饰**：加载有转圈、状态有心跳、入场有浮现、滚动有视差、指针有跟随、过渡有流动、交互有灵动反馈。**这些动效都要完整实现，一个都不能为了"性能"而删掉**——流畅靠「让动效全部走合成层」与「昂贵装饰该砍则砍」来保证，而非靠删减动效。

### 设计三原则

1. **材质真实**：玻璃必须真的"透"——能看见背景的色彩流动穿过表面，而非纯色蒙层。
2. **光照生命**：玻璃永远不是死的——边缘有镜面高光，交互时光泽划过、悬停时浮起、状态点脉冲。
3. **完整动效，该砍则砍**：七大维度动效（加载/状态/入场/滚动/指针/过渡/灵动）一律完整实现；"该砍则砍"只针对昂贵且仅起装饰的开销（氛围色斑常驻漂移、box-shadow 动画、细碎元素多余 blur），**绝不对任何功能性/体验性动效一刀切**。

---

## 核心命题：完整动效体系 + 该砍则砍（最高优先级，凌驾一切美学）

**动效是必需品，不是装饰。** 一套把加载、状态、入场、滚动、指针、过渡、灵动全部砍光的设计，是死的、缺乏生命力的；但一套保留全部动效又乱用昂贵写法（背景动画、阴影动画、氛围常驻漂移）的设计，会在集显上卡顿。因此本 skill 的核心命题不是"要不要动效"，而是：

> **七大维度动效（加载/状态/入场/滚动/指针/过渡/灵动）一律完整实现，一个不删；流畅靠「让所有动效走合成层（transform/opacity/filter）」+「昂贵装饰该砍则砍」来保证。该砍则砍只砍装饰，绝不砍功能。**

### 七大维度动效体系（都必须完整实现）

| 维度 | 代表动效 | 必要性 | 实现要点 |
|------|---------|--------|---------|
| **① 加载** | spinner 转圈、双圈旋转、三点跳动、骨架屏多层流光、进度流光、按钮 loading、加载占位呼吸 | ✅ 必需（用户等待时的核心反馈，没有就是"干等"） | 全部 transform/opacity 合成层；spinner 用 rotate 无限循环，骨架屏用伪元素 translateX 流光 |
| **② 状态** | 心跳脉冲、呼吸缩放、在线光晕、连接流光、加载点跳动、状态点变色过渡 | ✅ 必需（实时状态感知：连接中/在线/离线/错误） | opacity + transform 循环；状态点变色用瞬时切换（不过渡重绘属性） |
| **③ 入场** | 错落浮现 riseIn、模糊聚焦 blurIn、缩放弹出 popIn、侧向滑入 slideInLeft、翻转 flipIn、数字 countUp | ✅ 必需（首屏高光时刻，第一印象） | transform + opacity 一次性，`forwards`/`both` 落定；用 `--i` 错落编排 |
| **④ 滚动** | 滚动显现 reveal、视差 parallax、滚动进度条 | ✅ 必需（让页面"活"在滚动中） | IntersectionObserver 触发；视差用 rAF 节流 + `{ passive: true }` |
| **⑤ 指针** | 磁吸 magnetic、3D 倾斜 tilt、涟漪 ripple、光泽跟随 sheen-follow | ✅ 必需（最强感知：界面"感知"到指针） | pointermove 驱动，**必须 rAF 节流**；限定焦点元素，不一屏全上 |
| **⑥ 过渡** | FLIP 列表重排、折叠展开（grid 0fr→1fr）、tab 切换、圆形揭露换肤（View Transition） | ✅ 必需（变化"流动"而非跳变） | FLIP 用 Element.animate；折叠用 grid-template-rows；换肤用 clip-path circle |
| **⑦ 灵动点缀** | 边缘流光 sheen-sweep、光泽划过 btn-shine、脉冲环 ring-pulse、悬停浮起 hover-lift、抖动 shake、光标闪烁 caret、图标弹跳 icon-bounce、下划线生长 underline-grow、徽章弹现 badge-pop、聚焦环呼吸 focus-ring、光晕脉冲 glow-pulse（约 18 种） | ✅ 必需（让界面"灵"，从编舞节奏中产生高级感） | 全部 transform/opacity/filter，近乎零开销；叠加十几个也不掉帧 |

> **关键认知**：`transform` 和 `opacity` 由合成器处理，**不触发主线程重排重绘**——可以无限循环（spinner、脉冲、骨架流光、边缘流光）而几乎不掉帧。**所以"完整动效"的代价可以趋近于零，前提是只动合成层属性。** 真正昂贵的只有两类：① 大半径 `blur` 的常驻动画（氛围色斑漂移）② 同屏过多的 `backdrop-filter`。**把这两类砍到只保留必需，其余七大维度动效全保留——这就是"又活又快"的全部秘密。**

### 为什么是「完整动效 + 该砍则砍」而非「删动效」？（核心洞察）

"删动效换流畅"是错误解法——它把界面变成死的。"完整动效 + 该砍则砍"才是正确解法——界面更活，且更流畅：

| 来源 | 错误解法（删动效） | ✅ 正确解法（完整动效 + 该砍则砍） |
|------|---------------|----------------------|
| **加载等待** | 不转圈、不显示骨架，干等 | ✅ 完整实现：spinner 转圈 + 骨架屏 shimmer + 进度流光 + 按钮 loading |
| **状态变化** | 不脉冲，静态色块 | ✅ 完整实现：心跳脉冲 + 呼吸 + 在线光晕 + 连接流光 |
| **入场** | 不入场，直接出现 | ✅ 完整实现：错落 riseIn + 焦点先行 + popIn/flipIn |
| **指针** | 不跟随，无反馈 | ✅ 完整实现：磁吸/倾斜/涟漪/光泽跟随（rAF 节流，限焦点） |
| **氛围色斑** | 删掉所有氛围动画 | ⚪ **该砍的是常驻漂移**，入场（aura-in）保留——流动感交给边缘流光 |
| **backdrop-filter** | 删掉所有玻璃模糊 | ⚪ **该砍的是细碎元素多余 blur**，主结构玻璃保留 |
| **box-shadow** | 删掉所有阴影 | ⚪ **该砍的是 box-shadow 动画**，静态阴影保留（瞬时切换） |

**结论**：砍掉的永远是"昂贵且仅起装饰"的东西，**七大维度功能性动效一个不删**。界面因此既"完整活"又"流畅快"。

### 完整动效的预算分配（一图看懂）

```
动效预算，按"完整性 vs 开销"分配：
┌─────────────────────────────────────────────────────────┐
│ ✅ 七大维度动效全部完整实现（transform/opacity/filter，合成层） │ ← 全保留（这才是"活"）
│    加载(spinner/骨架/进度流光) + 状态(脉冲/呼吸/光晕) +         │
│    入场(错落/弹出/翻转) + 滚动(reveal/视差/进度条) +            │
│    指针(磁吸/倾斜/涟漪/光泽) + 过渡(FLIP/折叠/换肤) +           │
│    灵动(边缘流光/光泽划过/光晕脉冲/图标弹跳…)                  │
│    —— 这些只动合成层，可无限循环（spinner/脉冲/shimmer/流光）不掉帧 │
├─────────────────────────────────────────────────────────┤
│ ✅ backdrop-filter（主结构层透射模糊，≤ 8 个）                 │ ← 保留（玻璃透射必需），
│                                                               │    但只给主结构，细碎元素纯 tint
├─────────────────────────────────────────────────────────┤
│ ⚪ 氛围色斑「常驻漂移」（大半径 blur + infinite）              │ ← 该砍（唯一）：只留入场
│ ⚪ box-shadow「动画/大面积过渡」                              │ ← 该砍（唯一）：改伪元素+opacity
│ ⚪ 细碎元素「多余 backdrop-filter」（按钮/标签/输入框）          │ ← 该砍（唯一）：改纯 tint
└─────────────────────────────────────────────────────────┘
   只有最底下三行是"该砍"的——其余全是"必须完整保留"的动效。
```

### 该砍清单（违反即砍，只砍装饰，不砍功能）

下列写法昂贵且仅起装饰，**一律砍掉/改写**——注意砍的是"写法"，不是"动效本身"：

1. **氛围色斑禁止 `infinite` 漂移**（这是唯一被砍的"氛围动效"）。`.aura` 色斑**只保留 `aura-in` 一次性错落入场**（`both` 落定后静止）。砍掉的是常驻漂移——4 个 60vmax 模糊圆跑 infinite 在 idle 时持续 ~180 swap/s。静止色斑 + backdrop-filter 已足够提供氛围，"流动感"交给顶栏/卡片的边缘流光灵感动效（sheen-sweep，近乎免费）。
2. **细碎元素砍掉多余 `backdrop-filter`**。按钮、标签、输入框、状态栏、表格行内元素——这些数量多/高频的元素改用纯半透明 `tint`（`rgba` 背景，无 blur）。`backdrop-filter` 只给主结构层（顶栏/侧边栏/主面板/模态/卡片）。同屏总预算 ≤ 8。
3. **禁止全局通配符 transition**。`* { transition: background-color/color/border-color/box-shadow ... }` 让每个 hover 元素进重绘过渡。主题/明暗过渡只挂 `:root`（一次性离散操作）；**高频密集元素**（导航项、列表行、按钮组）hover 只过渡 `transform`/`opacity`；**低频独立元素**（卡片、输入框聚焦）允许过渡 background/border（不会形成密集滑动风暴）。
4. **box-shadow 永不入动画**（这是唯一被砍的"阴影写法"）。`@keyframes` 和 `transition` 里**禁止**动 `box-shadow`（触发重绘）。需要阴影变化时，用伪元素承载阴影 + `opacity` 切换。hover 加深投影改"瞬时切换"（不过渡）。**注意：静态 box-shadow 本身保留**（玻璃投影必需），砍的只是"动"box-shadow。
5. **禁止动画布局属性**。`width`/`height`/`top`/`left`/`margin`/`padding` 永不进 `@keyframes`（触发 layout）。需要尺寸变化的过渡用 `transform: scale` 或 `grid-template-rows: 0fr→1fr`（后者是 grid 动画，不触发布局重排）。
6. **指针/滚动监听必须 `requestAnimationFrame` 节流 + `{ passive: true }`**。磁吸/倾斜/光斑/视差等 pointermove/scroll 驱动的动效保留，但一帧只合并应用一次。

> **该砍清单的边界**：只砍"昂贵的写法/装饰"（氛围漂移、阴影动画、多余 blur）。**凡是属于七大维度功能性的动效（加载转圈、状态脉冲、入场、滚动、指针跟随、过渡、灵动反馈），一个都不砍**——它们都走合成层，保留也不掉帧。

### 集显友好基线（一套方案，无运行时探测，动效完整）

本 skill **不探测 GPU、不读取 `prefers-reduced-motion`、不准备多档降级方案**。复杂度集中在"一套七大维度动效完整、且在集显上也流畅的写法"上：

- **七大维度动效完整实现**：加载（spinner/骨架/进度流光/按钮 loading）、状态（脉冲/呼吸/光晕/流光）、入场（错落/弹出/翻转）、滚动（reveal/视差/进度条）、指针（磁吸/倾斜/涟漪/光泽）、过渡（FLIP/折叠/换肤）、灵动（约 18 种）——一个不删，全部 transform/opacity/filter。
- **功能性循环全保留**：spinner 转圈、状态点脉冲、骨架屏 shimmer、进度流光、边缘流光、光晕呼吸——这些是用户等待/状态的刚需，且本就合成层安全，可无限循环。
- **氛围色斑只入场**：`aura-in` 错落入场后静止，砍掉常驻漂移，idle 合成归零。
- **backdrop-filter 只给主结构**：顶栏/侧边栏/主面板/模态/卡片 ≤ 8 个；细碎元素纯 tint。
- **box-shadow 永不入动画**：静态阴影保留，阴影变化用伪元素 + opacity；hover 投影瞬时切换。
- **动效只动 transform / opacity / filter**；hover 反馈分层（高频只过渡 transform/opacity，低频可过渡 background）。
- **指针/滚动监听 `requestAnimationFrame` 节流 + `{ passive: true }`**。

> **设计哲学**：七大维度动效完整保留，昂贵装饰砍到只留必需。基线无运行时检测、无降级分档——流畅由"只动合成层 + 该砍则砍"一揽子保证。对"减少动效"偏好用户，提供纯 CSS 的 `@media (prefers-reduced-motion: reduce)` 兜底（缩短时长而非删除），是渐进增强而非运行时检测。

---


## Liquid Glass 材质原理

为什么看起来像玻璃？三要素缺一不可：

| 要素 | 作用 | 技术实现 |
|------|------|----------|
| **透射（Transmission）** | 背景色彩穿过玻璃，被柔化、被提亮饱和度 | `backdrop-filter: blur() saturate()` |
| **边缘高光（Specular Edge）** | 玻璃边缘折射聚集的光线，形成发光边 | `inset` 白色阴影 + 半透明白色描边 |
| **镜面反射（Reflection）** | 顶面反射环境光，形成立体厚度感 | 顶部内高光 + 渐变背景 + 多层投影 |

**关键认知**：没有背景的玻璃等于没有玻璃。Liquid Glass 必须放在有色彩流动、有明暗变化的背景之上，材质才会"活"过来。因此**背景氛围层**与**玻璃层**永远成对出现。

---

## 双维度主题系统（核心架构）

主题由**两个正交维度**组合决定，互不耦合：

| 维度 | 属性 | 取值 | 决定 |
|------|------|------|------|
| **明暗（Brightness）** | `data-theme` | `light`（默认）/ `dark` | 底色、玻璃 tint、文本色、高光强度、模糊度、投影 |
| **主题色（Accent Hue）** | `data-accent` | `mint`（默认，薄荷绿 #00D4AA）/ `azure` / `rose` / `violet` / `emerald` / `amber` / `indigo` | accent 强调色 + 4 个配套氛围色斑（色斑随主题色整体换肤） |

两个属性都挂在根节点 `<html>` 上，组合如 `<html data-theme="dark" data-accent="rose">` = 夜晚的玫瑰玻璃。

### 架构分层原理（为什么这样设计）

采用**三段式变量分离**，让明暗与色相彻底解耦、任意组合都能成立：

1. **明暗基础层**（`data-theme`）：只管与色相无关的量——底色、玻璃透明度、文本、高光、模糊、投影。它**不定义颜色值，只做引用切换**：`--accent: var(--accent-l)`（亮）或 `var(--accent-d)`（暗），`--aura-1: var(--aura-1-l)` / `var(--aura-1-d)`。
2. **色相层**（`data-accent`）：每个主题色提供一组**明/暗变体**——`--accent-l/--accent-d` + 4 个 `--aura-N-l/--aura-N-d` + RGB 三通道 `--accent-l-rgb/--accent-d-rgb`。
3. **组件层**：只引用**最终生效变量** `--accent`、`--aura-N`、`--accent-rgb`，对明暗与色相的切换完全无感。

**`--accent-rgb` 的作用**：把强调色拆成 RGB 三通道（如 `0, 113, 227`），让 `--accent-soft`、按钮投影等可用 `rgba(var(--accent-rgb), 0.35)` 动态生成——切主题色时投影/聚焦环也整体换肤，不必为每个色相写死一套派生色。

> **这是工业级主题系统的精髓**：明暗与色相正交分离，组件只看最终变量。新增主题色只需在色相层加一个 `[data-accent="..."]` 块，组件代码零改动。

---

## CSS 变量定义（完整可复制）

```css
/* ============ 明暗基础层：只做引用切换 ============ */
:root,
:root[data-theme="light"] {
  /* 底色与玻璃 tint */
  --glass-bg-base: #F5F5F7;                       /* 苹果标志性浅灰底 */
  --glass-tint: rgba(255, 255, 255, 0.55);
  --glass-tint-strong: rgba(255, 255, 255, 0.72);
  --glass-tint-soft: rgba(255, 255, 255, 0.35);
  --glass-highlight: rgba(255, 255, 255, 0.85);   /* 边缘高光（亮色强反光） */
  --glass-border: rgba(255, 255, 255, 0.6);
  --glass-blur: 24px;
  --glass-saturate: 180%;

  /* 文本 */
  --text-primary: #1D1D1F;                        /* 苹果近黑 */
  --text-secondary: #515154;
  --text-tertiary: rgba(60, 60, 67, 0.6);
  --text-on-glass: #1D1D1F;

  /* 投影：内高光（白）+ 外柔影（黑） */
  --shadow-glass:
    0 1px 1px rgba(255, 255, 255, 0.9) inset,
    0 -1px 1px rgba(255, 255, 255, 0.35) inset,
    0 12px 40px rgba(0, 0, 0, 0.08),
    0 4px 12px rgba(0, 0, 0, 0.04);

  /* 明暗切换：取色相层的 light 变体 */
  --accent: var(--accent-l);
  --accent-rgb: var(--accent-l-rgb);
  --aura-1: var(--aura-1-l);
  --aura-2: var(--aura-2-l);
  --aura-3: var(--aura-3-l);
  --aura-4: var(--aura-4-l);
}

:root[data-theme="dark"] {
  --glass-bg-base: #0A0A0C;                       /* 深邃夜底 */
  --glass-tint: rgba(40, 40, 48, 0.55);
  --glass-tint-strong: rgba(48, 48, 56, 0.72);
  --glass-tint-soft: rgba(30, 30, 36, 0.35);
  --glass-highlight: rgba(255, 255, 255, 0.18);   /* 暗色下高光克制 */
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

  /* 明暗切换：取色相层的 dark 变体 */
  --accent: var(--accent-d);
  --accent-rgb: var(--accent-d-rgb);
  --aura-1: var(--aura-1-d);
  --aura-2: var(--aura-2-d);
  --aura-3: var(--aura-3-d);
  --aura-4: var(--aura-4-d);
}

/* 派生色：基于 RGB 通道动态生成，随主题色整体换肤 */
:root {
  --accent-soft: rgba(var(--accent-rgb), 0.12);
  --accent-glow: rgba(var(--accent-rgb), 0.35);   /* 主按钮投影色 */
}

/* ============ 色相层：每个主题色提供明/暗变体 ============ */

/* Mint 薄荷绿（默认）—— #00D4AA 主色，清新、生机、轻盈科技感 */
/* 注意：:root 裸选择器兜底，未设 data-accent 时也按薄荷绿渲染 */
:root,
:root[data-accent="mint"] {
  --accent-l: #00D4AA; --accent-l-rgb: 0, 212, 170;
  --accent-d: #2EE8C0; --accent-d-rgb: 46, 232, 192;
  --aura-1-l: #B5F0E0; --aura-1-d: #2D6B5A;   /* 薄荷淡绿 / 深薄荷 */
  --aura-2-l: #A0E8E0; --aura-2-d: #2D6B6B;   /* 湖水青 / 青绿暗 */
  --aura-3-l: #C8F5E8; --aura-3-d: #3D6B5A;   /* 淡薄荷 / 墨绿 */
  --aura-4-l: #9FE8C8; --aura-4-d: #2D5A4E;   /* 青翠 / 深翠 */
}

/* Azure 蔚蓝 —— 苹果经典蓝 */
:root[data-accent="azure"],
:root[data-accent="azure"][data-theme] {
  --accent-l: #0071E3; --accent-l-rgb: 0, 113, 227;
  --accent-d: #0A84FF; --accent-d-rgb: 10, 132, 255;
  --aura-1-l: #FFB7C5; --aura-1-d: #6B4F8E;   /* 樱花粉 / 紫 */
  --aura-2-l: #A0D8EF; --aura-2-d: #2E5A88;   /* 天空蓝 / 深蓝 */
  --aura-3-l: #C8A2E6; --aura-3-d: #884A6B;   /* 丁香紫 / 玫红 */
  --aura-4-l: #B5EAD7; --aura-4-d: #2F6E5A;   /* 薄荷绿 / 墨绿 */
}

/* Rose 玫瑰 —— 时尚、美妆、生活方式 */
:root[data-accent="rose"] {
  --accent-l: #FF2D55; --accent-l-rgb: 255, 45, 85;
  --accent-d: #FF375F; --accent-d-rgb: 255, 55, 95;
  --aura-1-l: #FFD1DC; --aura-1-d: #8E2D4A;   /* 樱花粉 / 酒红 */
  --aura-2-l: #FFCBA4; --aura-2-d: #8E4A3D;   /* 蜜桃 / 砖红 */
  --aura-3-l: #F7C8C0; --aura-3-d: #7A3D5A;   /* 玫瑰金 / 紫红 */
  --aura-4-l: #E6C5E8; --aura-4-d: #6B3D6B;   /* 淡紫 / 粉紫 */
}

/* Violet 紫罗兰 —— 创意、科技、神秘 */
:root[data-accent="violet"] {
  --accent-l: #AF52DE; --accent-l-rgb: 175, 82, 222;
  --accent-d: #BF5AF2; --accent-d-rgb: 191, 90, 242;
  --aura-1-l: #D9C2F0; --aura-1-d: #5A3D8E;   /* 兰花紫 / 深紫 */
  --aura-2-l: #E0B0FF; --aura-2-d: #4A3D8E;   /* 丁香 / 靛紫 */
  --aura-3-l: #B4C5F0; --aura-3-d: #3D4A8E;   /* 靛蓝 / 蓝 */
  --aura-4-l: #F0C2D9; --aura-4-d: #5A3D7A;   /* 樱花粉 / 紫红 */
}

/* Emerald 翡翠 —— 健康、数据、自然 */
:root[data-accent="emerald"] {
  --accent-l: #34C759; --accent-l-rgb: 52, 199, 89;
  --accent-d: #30D158; --accent-d-rgb: 48, 209, 88;
  --aura-1-l: #B5EAD7; --aura-1-d: #2D6B4A;   /* 薄荷 / 深绿 */
  --aura-2-l: #C5E8B5; --aura-2-d: #2D6B6B;   /* 青柠 / 青绿 */
  --aura-3-l: #B5DCDC; --aura-3-d: #3D6B3D;   /* 湖水蓝 / 墨绿 */
  --aura-4-l: #DCEAB5; --aura-4-d: #2D5A5A;   /* 嫩黄绿 / 深青 */
}

/* Amber 琥珀 —— 餐饮、暖色品牌、活力 */
:root[data-accent="amber"] {
  --accent-l: #FF9500; --accent-l-rgb: 255, 149, 0;
  --accent-d: #FF9F0A; --accent-d-rgb: 255, 159, 10;
  --aura-1-l: #FFD9A0; --aura-1-d: #8E5A2D;   /* 蜜糖 / 焦糖 */
  --aura-2-l: #FFB88C; --aura-2-d: #8E6A3D;   /* 落日橙 / 砖橙 */
  --aura-3-l: #FFE8A0; --aura-3-d: #7A5A2D;   /* 金黄 / 暗金 */
  --aura-4-l: #FFCBA4; --aura-4-d: #7A4A3D;   /* 桃 / 棕红 */
}

/* Indigo 靛青 —— 金融、企业、专业 */
:root[data-accent="indigo"] {
  --accent-l: #5856D6; --accent-l-rgb: 88, 86, 214;
  --accent-d: #5E5CE6; --accent-d-rgb: 94, 92, 230;
  --aura-1-l: #C5C2F0; --aura-1-d: #3D3D8E;   /* 薰衣草 / 靛蓝 */
  --aura-2-l: #A0C5EF; --aura-2-d: #4A3D8E;   /* 天蓝 / 深紫 */
  --aura-3-l: #D9C2F0; --aura-3-d: #2E3D8E;   /* 兰花紫 / 蓝 */
  --aura-4-l: #B5DCEF; --aura-4-d: #3D3D7A;   /* 冰蓝 / 蓝紫 */
}

/* 平滑切换主题：过渡只挂在 :root（一次性的离散操作），不挂 * 通配符。
   实测 * { transition: background-color/color/... } 会让鼠标滑过的每个元素都进入重绘过渡，
   在导航/按钮密集区形成 ~250 swap/s 重绘风暴。hover 反馈由各组件用 transform/opacity 单独定义。 */
:root { transition: background-color 0.5s ease, --accent 0.5s ease; }
```

### 主题切换逻辑（同时管两个维度）

```html
<!-- 默认：亮色 + 薄荷绿。根节点同时挂 data-theme 与 data-accent -->
<html data-theme="light" data-accent="mint">
```

```js
// 明暗切换：写入 localStorage，刷新后保持
function setTheme(theme) {
  document.documentElement.setAttribute('data-theme', theme);
  localStorage.setItem('glass-theme', theme);
}
// 主题色切换
function setAccent(accent) {
  document.documentElement.setAttribute('data-accent', accent);
  localStorage.setItem('glass-accent', accent);
}

// 初始化：明暗跟随系统偏好（否则存储值，默认 light）；
// 主题色取存储值，默认 mint（薄荷绿）
const savedTheme  = localStorage.getItem('glass-theme');
const savedAccent = localStorage.getItem('glass-accent') || 'mint';
const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
setTheme(savedTheme || (prefersDark ? 'dark' : 'light'));
setAccent(savedAccent);

// 可选：跟随系统明暗实时联动
window.matchMedia('(prefers-color-scheme: dark)')
  .addEventListener('change', e => {
    if (!localStorage.getItem('glass-theme')) setTheme(e.matches ? 'dark' : 'light');
  });
```

> **默认原则**：未指定时一律 `data-theme="light"` + `data-accent="mint"`（薄荷绿 `#00D4AA`）。暗色不是反转，而是"夜晚的玻璃"——高光更含蓄、背景更深邃、强调色更亮。主题色不是换皮，而是换"玻璃透出的色族"。

---

## 字体系统

苹果生态字体栈，中文优雅、英文几何、数字锐利：

```css
:root {
  --font-display: 'SF Pro Display', -apple-system, 'PingFang SC', 'Helvetica Neue', sans-serif;
  --font-text: 'SF Pro Text', -apple-system, 'PingFang SC', 'Helvetica Neue', sans-serif;
  --font-mono: 'SF Mono', 'JetBrains Mono', ui-monospace, monospace;
}
```

| 用途 | 字号 | 字重 | 备注 |
|------|------|------|------|
| 巨型展示标题 | 56–80px | 700（Semibold-Bold） | `letter-spacing: -0.02em` 收紧 |
| 页面大标题 | 32–44px | 600 | `letter-spacing: -0.01em` |
| 正文 | 16–17px | 400 | 苹果标准正文行高 1.47 |
| 辅助说明 | 13–14px | 400 | 用 `--text-tertiary` |
| 数字/指标 | 28–48px | 600 | `font-variant-numeric: tabular-nums` |

**禁止**使用 Inter / Roboto / Arial / system-ui 等通用字体——它们与 Liquid Glass 的精致感冲突。

---

## 玻璃材质配方（核心 CSS）

这是整个系统的灵魂。**任何玻璃元素都必须包含：透射模糊 + 边缘高光 + 镜面投影** 三件套。

```css
.glass {
  /* 透射：背景穿过来，被柔化并提升饱和度 */
  background: var(--glass-tint);
  backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));
  -webkit-backdrop-filter: blur(var(--glass-blur)) saturate(var(--glass-saturate));

  /* 边缘高光：半透明白色描边 */
  border: 1px solid var(--glass-border);

  /* 镜面反射 + 投影：内外多层 */
  box-shadow: var(--shadow-glass);

  border-radius: 24px;
}

/* 加重玻璃（导航栏、模态框等需要更强存在感） */
.glass-strong {
  background: var(--glass-tint-strong);
  backdrop-filter: blur(36px) saturate(200%);
  -webkit-backdrop-filter: blur(36px) saturate(200%);
  box-shadow: var(--shadow-glass), 0 1px 0 rgba(255,255,255,0.5) inset;
}

/* 轻盈玻璃（叠加层、次要面板） */
.glass-soft {
  background: var(--glass-tint-soft);
  backdrop-filter: blur(16px) saturate(140%);
  -webkit-backdrop-filter: blur(16px) saturate(140%);
  border: 1px solid var(--glass-border);
}

/* 降级：backdrop-filter 不支持时，用高透明度渐变兜底 */
@supports not (backdrop-filter: blur(1px)) {
  .glass { background: var(--glass-tint-strong); }
}
```

### 顶部镜面高光（specular sheen）

让玻璃顶部"反光"，强化厚度感——这是区分"真玻璃"与"毛玻璃"的关键：

```css
.glass-sheen {
  position: relative;
}
/* 顶部一道渐隐高光，模拟顶面反射 */
.glass-sheen::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: linear-gradient(
    180deg,
    var(--glass-highlight) 0%,
    transparent 40%
  );
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  pointer-events: none;
}
```

---

## 背景氛围层（玻璃的舞台，随主题色换肤）

玻璃无背景则无效。每个页面必须先铺一层 **流动的 mesh 色斑**，玻璃才显形。色斑取自 `--aura-1~4`，**切换主题色时色斑整体换肤**，玻璃透出的色彩随之改变——这是"感知度极高"的关键。

```css
body {
  background: var(--glass-bg-base);
  position: relative;
  min-height: 100vh;
}

/* 色斑层：四个超大柔光圆。
   ⚠️ 该砍则砍（灵动为魂）：色斑**只用一次性错落入场**（aura-in），入场后即静止，禁止任何 infinite 漂移。
   实测 4 个 60vmax 模糊圆跑 infinite drift 会在 idle 时持续 ~180 swap/s 驱动合成，集显机型风扇狂转——
   这是昂贵且仅起装饰的开销。静止色斑 + backdrop-filter 已足够提供氛围，"流动感"交给顶栏/卡片的
   灵感动效（sheen-sweep 边缘流光）承接，那些才是近乎免费的合成层微动。idle 合成归零。 */
.aura {
  position: fixed;
  inset: 0;
  z-index: 0;
  overflow: hidden;
  pointer-events: none;
}
.aura::before,
.aura::after {
  content: '';
  position: absolute;
  width: 60vmax;
  height: 60vmax;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.55;
}
.aura::before {
  background: radial-gradient(circle, var(--aura-1), transparent 70%);
  top: -20%;
  left: -10%;
  animation: aura-in 2.4s var(--ease-liquid) 0s both;   /* 入场后静止，无 drift */
}
.aura::after {
  background: radial-gradient(circle, var(--aura-2), transparent 70%);
  bottom: -25%;
  right: -15%;
  animation: aura-in 2.4s var(--ease-liquid) 0.18s both;   /* 入场后静止，无 drift */
}
/* 额外两个色斑用真实节点补充 */
.aura .blob-3 {
  position: absolute; width: 55vmax; height: 55vmax; border-radius: 50%;
  filter: blur(80px); opacity: 0.5;
  background: radial-gradient(circle, var(--aura-3), transparent 70%);
  top: 30%; right: 20%;
  animation: aura-in 2.4s var(--ease-liquid) 0.32s both;
}
.aura .blob-4 {
  position: absolute; width: 55vmax; height: 55vmax; border-radius: 50%;
  filter: blur(80px); opacity: 0.5;
  background: radial-gradient(circle, var(--aura-4), transparent 70%);
  bottom: 20%; left: 25%;
  animation: aura-in 2.4s var(--ease-liquid) 0.46s both;
}

/* 一次性入场：从偏移+缩小+透明 滑入到位后静止（both 保留终态）。无任何 infinite 后续。 */
@keyframes aura-in {
  from { transform: translate(-8vmax, -6vmax) scale(0.9); opacity: 0; }
  to   { transform: translate(0, 0) scale(1); }
}
```

HTML 骨架：

```html
<div class="aura"><div class="blob-3"></div><div class="blob-4"></div></div>
```

**所有内容用 `position: relative; z-index: 1;` 浮在 `.aura` 之上。** 内容的玻璃层会自然透出底层流动色斑。

---

## 组件库

### 玻璃卡片

```html
<div class="card glass glass-sheen">
  <h3>卡片标题</h3>
  <p>内容透出底层流动的色彩。</p>
</div>
```

```css
.card {
  padding: 28px 32px;
  color: var(--text-on-glass);
  /* 只过渡 transform（合成层）。box-shadow 禁止参与 transition/动画（触发重绘）；
     hover 加深投影改用「瞬时切换」。卡片浮起这一下本身就是灵感动效。 */
  transition: transform 0.4s var(--ease-out-expo);
}
.card:hover {
  transform: translateY(-4px);          /* 悬停浮起（合成层，零重绘） */
  box-shadow: var(--shadow-glass),
              0 24px 60px rgba(0, 0, 0, 0.12);   /* 瞬时切换，不过渡 */
}
```

### 悬浮导航栏 / 工具栏

吸附顶部、内容居中、半透明漂浮——这是 Liquid Glass 最经典的形态：

```html
<nav class="navbar glass-strong glass-sheen">
  <div class="brand">Logo</div>
  <div class="nav-links">...</div>
</nav>
```

```css
.navbar {
  position: sticky;
  top: 16px;
  margin: 0 auto;
  width: min(1120px, 92%);
  padding: 10px 24px;
  border-radius: 999px;     /* 胶囊形是苹果导航的灵魂 */
  display: flex;
  align-items: center;
  justify-content: space-between;
  z-index: 100;
}
```

### 玻璃按钮

按钮是**数量多、高频**的元素——按"该砍则砍"，**砍掉 `backdrop-filter`**，改纯半透明 tint。玻璃感留给主结构层；按钮的"活"交给灵感动效（hover 光泽划过 btn-shine + 悬停浮起）承接。

```css
.btn {
  padding: 11px 24px;
  border-radius: 980px;          /* 极圆胶囊 */
  font-weight: 500;
  color: var(--text-on-glass);
  background: var(--glass-tint);   /* 纯半透明 tint，无 backdrop-filter——按钮数量多，把模糊预算留给主结构 */
  border: 1px solid var(--glass-border);
  box-shadow: var(--shadow-glass);
  /* hover 过渡：transform 走合成层（丝滑、不重绘）；background 是低频的进入/离开，
     单个按钮不会形成密集滑动风暴，允许过渡以获得平滑变色。
     （密集按钮组/导航项才需限制为纯 transform）
     box-shadow 禁止参与过渡（重绘）——hover 加深投影瞬时切换。 */
  transition: transform 0.2s var(--ease-spring), background 0.25s ease;
}
.btn:hover { transform: scale(1.04) translateY(-1px); background: var(--glass-tint-strong); }
.btn:active { transform: scale(0.97); }   /* 按下回弹 */

/* 主按钮：实心强调色 + 玻璃覆膜。投影随主题色整体换肤 */
.btn-primary {
  background: var(--accent);
  color: #fff;
  border: 1px solid rgba(255, 255, 255, 0.25);
  box-shadow: 0 1px 1px rgba(255,255,255,0.4) inset,
              0 8px 24px var(--accent-glow);
}
```

> 按钮的"灵"在交互瞬间：`.btn` 加 `btn-shine`（hover 时光泽划过）+ hover 浮起，加载态内部转圈。这些全是合成层，零重绘。**不需要 backdrop-filter 也能让按钮充满质感。**

### 标签 / 药丸

```css
.chip {
  display: inline-flex;
  align-items: center;
  padding: 5px 14px;
  border-radius: 980px;
  font-size: 13px;
  color: var(--text-secondary);
  background: var(--glass-tint-soft);   /* 标签数量多，统一用纯 tint，把 blur 预算留给主结构 */
  border: 1px solid var(--glass-border);
}
```

### 输入框（聚焦环随主题色）

输入框是低频独立元素（用户逐个聚焦），但数量仍可能较多——按"该砍则砍"，**砍掉 `backdrop-filter`**，改纯 tint。聚焦时的"活"交给灵感动效（focus-ring 聚焦环呼吸）承接。

```css
.input {
  padding: 12px 18px;
  border-radius: 14px;
  color: var(--text-primary);
  background: var(--glass-tint);   /* 纯 tint，无 backdrop-filter */
  border: 1px solid var(--glass-border);
  box-shadow: 0 1px 1px rgba(255,255,255,0.5) inset;
  transition: border-color 0.3s, box-shadow 0.3s;
}
.input:focus {
  outline: none;
  border-color: var(--accent);
  box-shadow: 0 1px 1px rgba(255,255,255,0.5) inset,
              0 0 0 4px var(--accent-soft);
}
```

> 进阶：聚焦态可用 `focus-ring` 灵感动效（聚焦环 `scale` + `opacity` 呼吸），比静态 `box-shadow` 聚焦环更"活"，且仍是合成层。

### 浮层 / Modal

```css
.modal {
  background: var(--glass-tint-strong);
  backdrop-filter: blur(40px) saturate(200%);
  -webkit-backdrop-filter: blur(40px) saturate(200%);
  border: 1px solid var(--glass-border);
  border-radius: 28px;
  box-shadow: var(--shadow-glass), 0 40px 80px rgba(0,0,0,0.2);
  animation: modalIn 0.5s cubic-bezier(0.2, 0.9, 0.3, 1.2);
}
@keyframes modalIn {
  from { opacity: 0; transform: scale(0.92) translateY(20px); }
  to   { opacity: 1; transform: scale(1) translateY(0); }
}
/* 遮罩也是玻璃 */
.modal-backdrop {
  background: rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
}
```

### 主题切换面板（双维度控制器）

这是"内置主题切换"的可见形态——一个胶囊形玻璃面板，内置明暗开关 + 主题色色点。应常驻在导航栏或页面角落：

```html
<div class="theme-switch glass glass-sheen">
  <!-- 明暗切换 -->
  <button class="theme-toggle" data-action="toggle-theme" aria-label="切换明暗">
    <span class="theme-toggle-icon"></span>
  </button>
  <span class="switch-divider"></span>
  <!-- 主题色色点 -->
  <div class="accent-swatches">
    <button class="swatch" data-accent-btn="mint"    style="--sw:#00D4AA" aria-label="薄荷绿"></button>
    <button class="swatch" data-accent-btn="azure"   style="--sw:#0071E3" aria-label="蔚蓝"></button>
    <button class="swatch" data-accent-btn="rose"    style="--sw:#FF2D55" aria-label="玫瑰"></button>
    <button class="swatch" data-accent-btn="violet"  style="--sw:#AF52DE" aria-label="紫罗兰"></button>
    <button class="swatch" data-accent-btn="emerald" style="--sw:#34C759" aria-label="翡翠"></button>
    <button class="swatch" data-accent-btn="amber"   style="--sw:#FF9500" aria-label="琥珀"></button>
    <button class="swatch" data-accent-btn="indigo"  style="--sw:#5856D6" aria-label="靛青"></button>
  </div>
</div>
```

```css
.theme-switch {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 6px 10px;
  border-radius: 999px;
}
.switch-divider {
  width: 1px; height: 20px;
  background: var(--glass-border);
}
.theme-toggle {
  width: 30px; height: 30px;
  border-radius: 50%;
  border: none; cursor: pointer;
  background: var(--glass-tint-soft);
  color: var(--text-secondary);
  display: grid; place-items: center;
  transition: transform 0.2s var(--ease-liquid);
}
.theme-toggle:hover { transform: rotate(20deg) scale(1.08); }
.theme-toggle-icon::before { content: '☀️'; }
:root[data-theme="dark"] .theme-toggle-icon::before { content: '🌙'; }

.accent-swatches { display: inline-flex; align-items: center; gap: 6px; }
.swatch {
  width: 20px; height: 20px;
  border-radius: 50%;
  border: 2px solid transparent;
  background: var(--sw);
  cursor: pointer;
  box-shadow: 0 1px 3px rgba(0,0,0,0.2);
  transition: transform 0.2s var(--ease-liquid), border-color 0.2s ease;
}
.swatch:hover { transform: scale(1.2); }
/* 当前激活的主题色：外圈高光环（用该色光晕） */
.swatch[aria-pressed="true"] {
  border-color: #fff;
  box-shadow: 0 0 0 3px var(--sw), 0 1px 4px rgba(0,0,0,0.3);
  transform: scale(1.1);
}
```

```js
// 绑定明暗开关
document.querySelector('[data-action="toggle-theme"]').addEventListener('click', () => {
  const cur = document.documentElement.getAttribute('data-theme');
  setTheme(cur === 'dark' ? 'light' : 'dark');
});
// 绑定主题色色点
document.querySelectorAll('[data-accent-btn]').forEach(btn => {
  btn.addEventListener('click', () => setAccent(btn.dataset.accentBtn));
});
// 同步面板激活态（初始化 + 切换后都要刷新）
function syncSwitchUI() {
  const cur = document.documentElement.getAttribute('data-accent');
  document.querySelectorAll('[data-accent-btn]').forEach(b => {
    b.setAttribute('aria-pressed', b.dataset.accentBtn === cur ? 'true' : 'false');
  });
}
// 切换函数里追加调用 syncSwitchUI()
```

---

## 动效系统（七大维度，完整实现，全合成层）

核心理念：**动效是必需品，覆盖加载/状态/入场/滚动/指针/过渡/灵动七大维度，一个不少**。动效不止服务于玻璃材质，更是整个界面的呼吸感与生命力。所有动效**一律用合成层属性（transform/opacity/filter）实现**，因此完整保留也不掉帧。

> ⚠️ 七大维度动效一律完整实现（这是"活"的根本）。"该砍则砍"只针对昂贵装饰：氛围色斑常驻漂移、box-shadow 动画、细碎元素多余 blur——详见上文「核心命题」。**绝不为性能删减任何功能性动效。**

### 动效设计五大原则

| 原则 | 含义 |
|------|------|
| **完整动效** | 七大维度（加载/状态/入场/滚动/指针/过渡/灵动）一律完整实现，每个维度都准备多种变体适配不同场景 |
| **物理真实** | 动效有质量、惯性、阻尼——像真实物体在运动，不是凭空跳变 |
| **层次编排** | 错落（stagger）、时序、焦点引导——动效是有节奏的编舞，不是齐步走 |
| **持续响应** | 滚动、指针、状态变化始终在反馈，加载/等待有转圈/骨架，状态有心跳/呼吸 |
| **合成层优先** | 只动 transform/opacity/filter，可放心做无限循环（spinner/脉冲/shimmer/边缘流光）；布局/重绘属性（含 box-shadow）永不入动画 |

### 缓动曲线库（按物理特性分类）

告别 `linear` / `ease` / `ease-in-out`。每类动效匹配对应的物理曲线：

```css
:root {
  /* 液态 / 丝滑 —— 标准UI位移过渡 */
  --ease-liquid:    cubic-bezier(0.2, 0.8, 0.2, 1);
  --ease-smooth:    cubic-bezier(0.4, 0, 0.2, 1);
  /* 弹簧 —— 强回弹，按钮/卡片浮现/磁吸归位 */
  --ease-spring:    cubic-bezier(0.34, 1.56, 0.64, 1);
  --ease-bounce:    cubic-bezier(0.2, 0.9, 0.3, 1.3);
  /* 衰减 —— 入场"落定"，急停感 */
  --ease-out-expo:  cubic-bezier(0.16, 1, 0.3, 1);
  --ease-out-quint: cubic-bezier(0.22, 1, 0.36, 1);
  /* 快速吸附 —— 开关、tab 选中 */
  --ease-snap:      cubic-bezier(0.4, 0, 0.2, 1);
  /* 主题切换 —— 柔和流动 */
  --ease-theme:     cubic-bezier(0.65, 0, 0.35, 1);
}
```

| 曲线 | 适用 |
|------|------|
| `--ease-liquid` | 默认液态过渡（位移、缩放基础态） |
| `--ease-spring` | 需要回弹的交互（按钮、卡片浮现、磁吸归位） |
| `--ease-out-expo` | 元素入场"落定"（从模糊/远处急停） |
| `--ease-smooth` | 颜色、透明度等无位移的状态过渡 |
| `--ease-theme` | 主题色/明暗换肤的柔和流动 |
| `--ease-snap` | 开关、tab 切换的快速吸附 |

### 入场动效体系（页面加载的惊艳时刻）

页面加载是一次性的高光时刻，必须编排到位——**错落入场 + 模糊聚焦 + 多轴运动**，而非整齐淡入。

```css
/* 液态浮起（默认）：从下方模糊处浮出 */
@keyframes riseIn {
  from { opacity: 0; transform: translateY(24px) scale(0.98); filter: blur(8px); }
  to   { opacity: 1; transform: none; filter: blur(0); }
}
/* 模糊聚焦：从大模糊收锐，电影感 */
@keyframes blurIn {
  from { opacity: 0; filter: blur(20px); transform: scale(1.05); }
  to   { opacity: 1; filter: blur(0); transform: none; }
}
/* 缩放弹出：从中心弹出带回弹 */
@keyframes popIn {
  from { opacity: 0; transform: scale(0.85); }
  to   { opacity: 1; transform: scale(1); }
}
/* 侧向滑入：从边缘切入 */
@keyframes slideInLeft {
  from { opacity: 0; transform: translateX(-40px); }
  to   { opacity: 1; transform: none; }
}
```

**错落编排**（核心技巧）：用 `animation-delay` + CSS 变量统一节奏，营造编舞感：

```css
/* 给每个入场元素设 --i（序号），delay 随之递增 */
[data-enter] {
  opacity: 0;
  animation: riseIn 0.8s var(--ease-out-expo) forwards;
  animation-delay: calc(var(--i, 0) * 80ms);
}
```

```html
<!-- 显式声明序号，或由 JS 批量设置 -->
<header data-enter style="--i:0">...</header>
<section data-enter style="--i:1">...</section>
<section data-enter style="--i:2">...</section>
```

> 编排心法：首屏主标题 `--i:0` 最先入场（200ms 内），副内容逐级延后（每级 60–100ms），形成"标题落下 → 内容跟上"的焦点引导。同一视口内错落总数控制在 5–8 个，超过则改为滚动触发。

### 滚动驱动动效（让页面"活"在滚动中）

滚动是用户主动施加的力，界面应**实时回应**——视差、显现、进度。这是"感知极强"的主要来源。

**(1) 滚动入场（Reveal）**：元素进入视口才触发入场动画。

```css
[data-reveal] {
  opacity: 0;
  transform: translateY(40px);
  transition: opacity 0.8s var(--ease-out-expo),
              transform 0.8s var(--ease-out-expo);
}
[data-reveal].in-view { opacity: 1; transform: none; }
```

```js
// IntersectionObserver 触发，一次性，避免反复触发
const io = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      e.target.classList.add('in-view');
      io.unobserve(e.target);
    }
  });
}, { threshold: 0.15, rootMargin: '0px 0px -10% 0px' });
document.querySelectorAll('[data-reveal]').forEach(el => io.observe(el));
```

**(2) 视差（Parallax）**：背景/装饰层以不同速率移动，制造纵深。

```js
// rAF 节流的滚动视差：不同元素给不同 data-speed（0.1–0.5）
let ticking = false;
window.addEventListener('scroll', () => {
  if (ticking) return;
  ticking = true;
  requestAnimationFrame(() => {
    const y = window.scrollY;
    document.querySelectorAll('[data-parallax]').forEach(el => {
      const speed = parseFloat(el.dataset.parallax);
      el.style.transform = `translate3d(0, ${y * speed}px, 0)`;
    });
    ticking = false;
  });
}, { passive: true });
```

**(3) 滚动进度条**：顶部细线随滚动填充，强烈的位置反馈。

```css
.scroll-progress {
  position: fixed; top: 0; left: 0; height: 3px; width: 100%;
  background: var(--accent);
  transform: scaleX(0); transform-origin: left;
  z-index: 200; box-shadow: 0 0 12px var(--accent-glow);
}
```
```js
const bar = document.querySelector('.scroll-progress');
window.addEventListener('scroll', () => {
  const h = document.documentElement;
  const p = h.scrollTop / (h.scrollHeight - h.clientHeight);
  bar.style.transform = `scaleX(${p})`;
}, { passive: true });
```

### 指针交互动效（界面"感知"到你的存在）

最强感知来自**界面跟随指针实时形变**。三种核心手法：

**(1) 磁吸（Magnetic）**：按钮被指针"吸引"偏移，松手弹簧归位。

```js
// ⚠️ 性能：pointermove 高频触发，必须 rAF 节流（一帧只应用一次），否则鼠标移动即掉帧。
// 元素加 data-magnetic，strength 控制吸引强度（0.2–0.4）
const rafQueued = new WeakSet();
document.querySelectorAll('[data-magnetic]').forEach(el => {
  const strength = parseFloat(el.dataset.magnetic) || 0.3;
  let px = 0, py = 0;
  el.addEventListener('pointermove', e => {
    const r = el.getBoundingClientRect();
    px = (e.clientX - r.left - r.width / 2) * strength;
    py = (e.clientY - r.top - r.height / 2) * strength;
    if (rafQueued.has(el)) return;
    rafQueued.add(el);
    requestAnimationFrame(() => {
      el.style.transform = `translate(${px}px, ${py}px)`;
      rafQueued.delete(el);
    });
  });
  el.addEventListener('pointerleave', () => { el.style.transform = ''; });
});
```
```css
[data-magnetic] { transition: transform 0.5s var(--ease-spring); }  /* 归位用弹簧 */
```

**(2) 3D 倾斜（Tilt）**：卡片随指针位置做 perspective 倾斜，立体感拉满。

```html
<div class="card glass glass-sheen" data-tilt>
  <h3>指针移动，卡片随之倾斜</h3>
</div>
```
```css
[data-tilt] { transform-style: preserve-3d; transition: transform 0.2s var(--ease-liquid); }
[data-tilt] > * { transform: translateZ(40px); }  /* 内容浮起，强化景深 */
```
```js
// ⚠️ 性能：tilt 是 pointermove 驱动，必须 rAF 节流。多个 tilt 卡片用共享队列，一帧合并应用。
const tiltPending = new Map();   // el -> {px,py}
function tiltFlush() {
  tiltPending.forEach((v, el) => {
    el.style.transform =
      `perspective(800px) rotateX(${-v.py * 10}deg) rotateY(${v.px * 10}deg)`;
  });
  tiltPending.clear();
  tiltRaf = 0;
}
let tiltRaf = 0;
document.querySelectorAll('[data-tilt]').forEach(el => {
  el.addEventListener('pointermove', e => {
    const r = el.getBoundingClientRect();
    tiltPending.set(el, {
      px: (e.clientX - r.left) / r.width - 0.5,
      py: (e.clientY - r.top) / r.height - 0.5,
    });
    if (!tiltRaf) tiltRaf = requestAnimationFrame(tiltFlush);
  });
  el.addEventListener('pointerleave', () => { el.style.transform = ''; });
});
```

**(3) 涟漪点击（Ripple）**：点击瞬间从触点扩散光圈，强化"按下"反馈。

```js
document.querySelectorAll('[data-ripple]').forEach(el => {
  el.addEventListener('click', e => {
    const r = el.getBoundingClientRect();
    const ripple = document.createElement('span');
    ripple.className = 'ripple';
    ripple.style.left = `${e.clientX - r.left}px`;
    ripple.style.top  = `${e.clientY - r.top}px`;
    el.appendChild(ripple);
    setTimeout(() => ripple.remove(), 600);
  });
});
```
```css
[data-ripple] { position: relative; overflow: hidden; }
.ripple {
  position: absolute; width: 8px; height: 8px; border-radius: 50%;
  transform: translate(-50%, -50%) scale(0);
  background: radial-gradient(circle, rgba(255,255,255,0.6), transparent 70%);
  animation: rippleOut 0.6s var(--ease-out-expo);
  pointer-events: none;
}
@keyframes rippleOut { to { transform: translate(-50%, -50%) scale(40); opacity: 0; } }
```

> 光斑跟随（`data-glow`）见下文「光照系统」，三者常组合：`data-tilt data-glow data-ripple` 一张卡片即具备立体倾斜 + 边缘流光 + 点击涟漪，感知拉满。

### 数字与内容动效（让数据"活"起来）

**(1) 数字滚动（Count-up）**：指标数字从 0 滚到目标值。

```js
function countUp(el, target, duration = 1600) {
  const start = performance.now();
  function tick(now) {
    const p = Math.min((now - start) / duration, 1);
    const eased = 1 - Math.pow(1 - p, 4);   // ease-out-quart
    el.textContent = Math.round(eased * target).toLocaleString();
    if (p < 1) requestAnimationFrame(tick);
  }
  requestAnimationFrame(tick);
}
// 进入视口时触发
new IntersectionObserver((entries, obs) => {
  entries.forEach(e => {
    if (e.isIntersecting) { countUp(e.target, +e.target.dataset.count); obs.unobserve(e.target); }
  });
}, { threshold: 0.5 }).observe(document.querySelector('[data-count]'));
```
```html
<span data-count="128000">0</span>
```

**(2) 打字机（Typewriter）**：标题逐字显现，营造"正在生成"的期待感。

```css
[data-typewriter] {
  overflow: hidden; white-space: nowrap;
  border-right: 2px solid var(--accent);
  animation: caretBlink 0.8s step-end infinite;
}
@keyframes caretBlink { 50% { border-color: transparent; } }
```
```js
const el = document.querySelector('[data-typewriter]');
const full = el.textContent; el.textContent = '';
let i = 0;
const type = () => {
  if (i < full.length) { el.textContent += full[i++]; setTimeout(type, 45); }
  else el.style.animation = 'none';   // 打完停止闪烁光标
};
setTimeout(type, 300);
```

**(3) 骨架闪烁（Shimmer）**：内容加载时玻璃面板内流光扫过，暗示"即将出现"。

```css
/* 用伪元素 + transform 流光，而非 background-position（后者触发布局外重绘）。
   走 transform 是合成层，可无限循环不掉帧。 */
.skeleton {
  position: relative;
  overflow: hidden;
  background: var(--glass-tint-soft);
}
.skeleton::after {
  content: '';
  position: absolute; inset: 0;
  transform: translateX(-100%);
  background: linear-gradient(90deg, transparent, var(--glass-tint) 50%, transparent);
  animation: shimmer 1.4s var(--ease-smooth) infinite;
}
@keyframes shimmer { to { transform: translateX(100%); } }
```

### 加载与状态动效（维度①②，必需，必须完整实现多种变体）

加载和状态反馈是动效的**刚需**——用户等待时必须有明确的"正在工作"信号，状态变化必须被感知。**不要只做一种 spinner 就完事**：不同场景配不同的加载动效（短操作用转圈，列表用骨架屏，进度用进度流光，按钮用 loading 态），层次更丰富。这些动效天然适合无限循环，因为它们全走合成层。

#### 加载动效变体库（按场景选择，都要有）

**(1) 加载转圈（Spinner）**：最基础，短时异步操作（连接、保存、提交）必备。

```css
.spinner {
  width: 30px; height: 30px;
  border: 2.5px solid var(--glass-border);
  border-top-color: var(--accent);
  border-radius: 50%;
  animation: spin 0.7s linear infinite;   /* 纯 transform: rotate，合成层，无限循环安全 */
  will-change: transform;                  /* 持续动画提升为独立合成层 */
}
@keyframes spin { to { transform: rotate(360deg); } }
```

**(2) 双圈旋转（Dual Ring）**：强调色与白色双圈反向旋转，比单圈更有"机械感"，用于关键加载（终端连接、大数据加载）。

```css
.spinner-dual {
  position: relative; width: 36px; height: 36px;
}
.spinner-dual::before,
.spinner-dual::after {
  content: ''; position: absolute; inset: 0; border-radius: 50%;
  border: 2.5px solid transparent;
}
.spinner-dual::before { border-top-color: var(--accent); animation: spin 0.9s linear infinite; }
.spinner-dual::after  { border-bottom-color: var(--glass-highlight); animation: spin 1.4s linear reverse infinite; }
/* 双圈两层都是 transform: rotate，合成层，正反不同速 */
```

**(3) 三点跳动（Loader Dots）**：三个圆点依次跳动，比转圈更"轻盈"，用于内容加载（列表、面板、轻量请求）。

```css
.loader-dots { display: inline-flex; gap: 6px; }
.loader-dots span {
  width: 8px; height: 8px; border-radius: 50%; background: var(--accent);
  animation: loader-dot 1.2s var(--ease-smooth) infinite;
}
.loader-dots span:nth-child(2) { animation-delay: 0.15s; }
.loader-dots span:nth-child(3) { animation-delay: 0.3s; }
@keyframes loader-dot {
  0%, 60%, 100% { transform: translateY(0); opacity: 0.4; }
  30%           { transform: translateY(-8px); opacity: 1; }   /* transform+opacity，合成层 */
}
```

**(4) 骨架屏（Skeleton Shimmer）**：内容加载时的占位骨架，暗示"即将出现"。用伪元素流光扫过。

```css
/* 用伪元素 + transform 流光，而非 background-position（后者触发布局外重绘）。
   走 transform 是合成层，可无限循环不掉帧。 */
.skeleton {
  position: relative;
  overflow: hidden;
  background: var(--glass-tint-soft);
  border-radius: 8px;
}
.skeleton::after {
  content: '';
  position: absolute; inset: 0;
  transform: translateX(-100%);
  background: linear-gradient(90deg, transparent, var(--glass-tint) 50%, transparent);
  animation: shimmer 1.4s var(--ease-smooth) infinite;
}
@keyframes shimmer { to { transform: translateX(100%); } }
```

> 骨架屏要"多层"才有真实感：标题用宽矩形、正文用细长矩形、头像/图标用圆形，按真实内容布局排列多个 `.skeleton`，整组一起 shimmer。

**(5) 进度流光（Progress Shimmer / Indeterminate）**：上传/下载/加载进度。

```css
.progress-fill {
  background: linear-gradient(90deg, var(--accent), var(--accent-2));
  position: relative; overflow: hidden;
}
/* 确定进度：表面叠流光（progress-shimmer），让"正在推进"被持续感知 */
.progress-fill::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent);
  transform: translateX(-100%);
  animation: progress-shimmer 1.6s var(--ease-smooth) infinite;
}
@keyframes progress-shimmer { to { transform: translateX(100%); } }
/* 不定进度（不知道百分比）：整条强调色条左右滑动暗示在动 */
.progress-fill.indeterminate {
  width: 40% !important;
  animation: progress-slide 1.2s var(--ease-smooth) infinite;
}
@keyframes progress-slide {
  0%   { transform: translateX(-100%); }
  100% { transform: translateX(250%); }
}
```

**(6) 按钮 loading 态**：点击后按钮内部转圈 + 整体半透明禁用，防止重复提交。

```css
.btn.loading { pointer-events: none; opacity: 0.8; }
.btn.loading::after {   /* 复用 spinner，居中在按钮内 */
  content: ''; display: inline-block;
  width: 14px; height: 14px; margin-left: 8px;
  border: 2px solid rgba(255,255,255,0.4); border-top-color: #fff;
  border-radius: 50%; vertical-align: middle;
  animation: spin 0.7s linear infinite;
}
```

> **加载动效选型表**（别只做一种）：

| 场景 | 选用 | 理由 |
|------|------|------|
| 短时按钮操作（保存/提交） | 按钮 loading 态（内转圈） | 反馈集中在按钮，不打断布局 |
| 连接/认证（中等时长） | spinner 转圈 或 双圈 | 居中遮罩 + 文案"正在连接..." |
| 列表/面板/卡片内容加载 | 骨架屏（多层 shimmer） | 占位保持布局稳定，避免跳动 |
| 长内容流/树加载 | 三点跳动 loader-dots | 轻盈，不喧宾夺主 |
| 上传/下载/已知进度 | 进度流光（progress-shimmer） | 强调色流光 + 百分比 |
| 未知进度（解析/等待） | 不定进度条（indeterminate） | 整条滑动暗示在动 |

#### 状态动效（实时状态感知，必须完整实现）

**(1) 状态脉冲（Pulse）**：连接中、加载点的"心跳"。

```css
.status-dot.pending { animation: pulse-dot 1.8s ease-in-out infinite; }
@keyframes pulse-dot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50%      { opacity: 0.4; transform: scale(0.85); }   /* opacity+transform 双通道，合成层 */
}
```

**(2) 呼吸（Breathe）**：在线状态的轻缩放呼吸，比脉冲更"活着"。

```css
@keyframes breathe {
  0%, 100% { transform: scale(1); opacity: 1; }
  50%      { transform: scale(1.15); opacity: 0.75; }
}
.status-dot.ready { animation: breathe 2.4s var(--ease-smooth) infinite; }
```

**(3) 在线光晕（Online Glow）**：在线状态点外圈强调色光晕轻呼吸（ring-pulse），比静态绿点更"活"。

```css
.online-glow { position: relative; }
.online-glow::before {
  content: ''; position: absolute; inset: 0; border-radius: 50%;
  border: 2px solid #34C759;
  animation: ring-pulse 2s var(--ease-out-expo) infinite;
}
```

**(4) 连接流光（Connect Stream）**：连接建立过程中，强调色条沿连接线流动，比静态转圈更有"正在握手"的叙事感。

```css
@keyframes connect-stream {
  0%   { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}
.connect-line { position: relative; overflow: hidden; }
.connect-line::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(90deg, transparent, var(--accent), transparent);
  animation: connect-stream 1.2s var(--ease-smooth) infinite;
}
```

**(5) 状态点变色过渡**：状态从 pending→ready→error 时颜色变化。**注意**：高频状态点（数量可能多）的颜色变化用**瞬时切换**（不过渡 background），仅 transform/opacity 类的状态点（单个、低频）允许过渡 background。

> **状态动效选型**：连接中 = pulse 心跳；已连接在线 = breathe 呼吸 + online-glow 光晕；加载中 = 三点跳动 / spinner；错误 = shake 抖动 + 红色瞬时切换；离线 = 静态灰色点。每个状态都要有对应的视觉反馈，不能"状态变了 UI 没反应"。

### 状态过渡动效（变化"流动"而非跳变）

**(1) FLIP 列表重排**：列表增删/排序时，元素从旧位置"飞"到新位置。

```js
// 记录旧位置(First) → 更新DOM → 算位移(Last) → 反演并播放(Invert/Play)
function flipReorder(list, applySort) {
  const oldRects = [...list.children].map(c => c.getBoundingClientRect());
  applySort();
  [...list.children].forEach((c, i) => {
    const dx = oldRects[i].left - c.getBoundingClientRect().left;
    const dy = oldRects[i].top  - c.getBoundingClientRect().top;
    if (!dx && !dy) return;
    c.animate(
      [{ transform: `translate(${dx}px,${dy}px)` }, { transform: 'none' }],
      { duration: 500, easing: 'cubic-bezier(0.2,0.8,0.2,1)' }
    );
  });
}
```

**(2) 展开/折叠（Expand）**：`grid-template-rows: 0fr → 1fr` 无需测高的丝滑展开。

```css
.accordion-body {
  display: grid; grid-template-rows: 0fr;
  transition: grid-template-rows 0.5s var(--ease-liquid);
}
.accordion.open .accordion-body { grid-template-rows: 1fr; }
.accordion-body > div { overflow: hidden; }
```

**(3) 视图淡入切换**：Tab/路由切换时旧视图淡出、新视图 `riseIn` 入场，用 opacity 交叉淡入。

### 主题切换过渡（换肤的优雅时刻）

主题色/明暗切换时，色斑与强调色应**柔和地"流动"到新色**。除全局 `transition`（见变量定义末尾）外，给氛围层加专属过渡：

```css
.aura::before, .aura::after, .aura .blob-3, .aura .blob-4 {
  transition: background 0.8s var(--ease-theme);
}
```

进阶：切换时从点击点扩散**圆形蒙版揭露**（View Transition API），新主题色像水波般铺开：

```js
function setThemeWithReveal(theme, x, y) {
  if (!document.startViewTransition) { setTheme(theme); return; }
  document.documentElement.style.setProperty('--reveal-x', `${x}px`);
  document.documentElement.style.setProperty('--reveal-y', `${y}px`);
  document.startViewTransition(() => setTheme(theme));
}
```
```css
::view-transition-new(root) {
  animation: circleReveal 0.6s var(--ease-out-expo);
}
@keyframes circleReveal {
  from { clip-path: circle(0 at var(--reveal-x) var(--reveal-y)); }
  to   { clip-path: circle(150% at var(--reveal-x) var(--reveal-y)); }
}
```

### 动效编排心法

| 手法 | 说明 |
|------|------|
| **七大维度齐全** | 加载/状态/入场/滚动/指针/过渡/灵动每个维度都要有对应动效，缺一不可；每维度准备多种变体适配场景 |
| **错落 stagger** | 同组元素逐个延后入场（`--i * 80ms`），制造层次与节奏 |
| **焦点先行** | 标题/主按钮先入场（最快），内容随后，引导视线 |
| **一强多弱** | 每屏只放一个"强动效"（如 3D tilt 大卡），其余用克制的淡入/位移衬托 |
| **同源时序** | 同一交互链共享缓动曲线与时长基调，视觉统一 |
| **停止即静** | 入场/过渡必须 `forwards` 落定；但**功能性的无限循环（spinner/脉冲/骨架屏/边缘流光/光晕呼吸/连接流光）要保留**——它们走合成层，不掉帧 |

### 性能与可访问性

**性能（60fps 守则，该砍只砍装饰）**：
- **七大维度动效完整保留**（加载/状态/入场/滚动/指针/过渡/灵动）——它们只动合成层，完整实现也不掉帧。
- 只动 `transform` / `opacity` / `filter`——走合成层，不触发重排重绘。**这些属性可放心无限循环**（spinner/脉冲/shimmer/边缘流光/连接流光）。
- 该砍则砍只针对昂贵装饰：氛围色斑砍常驻漂移（只留入场）、细碎元素砍多余 blur（改纯 tint）、box-shadow 永不入动画（静态阴影保留，变化改伪元素+opacity）。
- 禁止动画 `width/height/top/left/margin`（触发 layout）。需要尺寸过渡用 `transform: scale` 或 `grid-template-rows: 0fr→1fr`。
- 持续动画的元素加 `will-change: transform`（spinner/脉冲等本就持续动，常驻合理；一次性动画的 `will-change` 用完移除）。
- 滚动/指针监听用 `requestAnimationFrame` 节流，监听加 `{ passive: true }`。
- `backdrop-filter` 开销大，同屏玻璃元素 ≤ 8 个，**细碎/高频元素一律纯 tint**。

**完整动效，可访问性做渐进增强**：

本 skill **不读取 `prefers-reduced-motion` 做运行时分档**，但提供纯 CSS 的渐进增强兜底——偏好减少动效的用户会得到"动画时长极短但仍然存在"的体验（转圈仍是转圈、入场仍是入场，只是更快、更轻），而非完全静态：

```css
/* 偏好减少动效：缩短到几乎瞬时，但保留功能性反馈（不删除） */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;        /* spinner/脉冲时长趋零，但仍在 */
    animation-iteration-count: 1 !important;       /* 循环类只跑一次 */
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

> 动效是界面的生命力——七大维度（加载/状态/入场/滚动/指针/过渡/灵动）一个都不删。完整保留加载转圈、双圈、三点跳动、骨架屏流光、进度流光、状态脉冲、在线光晕、连接流光、入场浮现、滚动视差、指针跟随、过渡流动、灵动反馈——它们全走合成层，集显上也 60fps。**该砍则砍只砍昂贵装饰（氛围漂移/阴影动画/多余 blur），绝不砍功能。**

---

## 🌟 灵动效果动效库（维度⑦，七大维度之一的"点缀层"，性能极低、视觉极佳）

**注意：这是七大维度动效体系中的第⑦维度（灵动点缀），不是全部**。前六维度（加载/状态/入场/滚动/指针/过渡）是必需的功能性动效，同样必须完整实现（见上文「加载与状态动效」「入场动效体系」「滚动驱动动效」「指针交互动效」「状态过渡动效」）。本库是让界面"灵"的最后一层点缀。

下面这套动效**全部只动 transform / opacity / filter**，开销几乎为零（合成器处理，单帧不触发主线程），却能让界面瞬间"活"起来。它们是"性价比最高"的灵动感来源——给主结构层（顶栏/卡片/按钮/列表）配 2–3 个常驻灵动（边缘流光、脉冲环、光标闪烁）+ 一批事件触发的灵动（光泽划过、涟漪、抖动、徽章弹现），界面立刻充满呼吸感。

> **使用心法**：这 18 种动效叠加十几个也不掉帧。它们与加载/状态/入场/滚动/指针/过渡六大维度动效**协同**——七大维度齐全，界面才完整"活"。

### 1. 玻璃边缘流光（Sheen Sweep）—— 玻璃表面周期性掠过一道光

让玻璃顶栏/卡片表面有"光在滑动"的生命感。一道高光带从左掠到右，周期慢、幅度大，像阳光扫过玻璃桌面。

```css
@keyframes sheen-sweep {
  0%   { transform: translateX(-120%) skewX(-18deg); opacity: 0; }
  20%  { opacity: 0.8; }
  80%  { opacity: 0.8; }
  100% { transform: translateX(120%) skewX(-18deg); opacity: 0; }
}
.glass-sweep { position: relative; overflow: hidden; }
.glass-sweep::after {
  content: ''; position: absolute; top: 0; bottom: 0; left: 0; width: 40%;
  background: linear-gradient(90deg, transparent, var(--glass-highlight) 50%, transparent);
  pointer-events: none;
  animation: sheen-sweep 5s var(--ease-smooth) infinite;
}
```
> 适用于顶栏、模态框、登录卡等**数量少**的玻璃主结构（≤2 个同屏），周期 ≥5s。`transform` 平移 + opacity，合成层安全可常驻。

### 2. 按钮光泽划过（Shine）—— hover 时一次性高光掠过

按钮被触碰瞬间，强调色表面掠过一道光，强化"被点亮"的触感。一次性触发，零常驻开销。

```css
.btn-shine { position: relative; overflow: hidden; }
.btn-shine::after {
  content: ''; position: absolute; top: 0; bottom: 0; left: -60%; width: 40%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.45), transparent);
  transform: skewX(-20deg); pointer-events: none;
}
.btn-shine:hover::after { animation: btn-shine 0.7s var(--ease-out-quint); }
@keyframes btn-shine { to { left: 120%; } }
```

### 3. 强调色脉冲环（Ring Pulse）—— 在线/活动指示器

强调色圆环周期性向外扩散淡出，像水波。比静态绿点更"活"，用于在线状态、实时数据指示。

```css
@keyframes ring-pulse {
  0%   { transform: scale(0.8); opacity: 0.7; }
  100% { transform: scale(2.4); opacity: 0; }
}
.ring-pulse { position: relative; }
.ring-pulse::before {
  content: ''; position: absolute; inset: 0; border-radius: inherit;
  border: 2px solid var(--accent);
  animation: ring-pulse 2s var(--ease-out-expo) infinite;
}
```

### 4. 数字滚动（Count-up）—— 指标从 0 滚到目标值

数据看板的灵魂动效。数字"跳动着"涨上来，比直接显示终值有冲击力得多。

```js
function countUp(el, target, duration = 1400) {
  const start = performance.now();
  function tick(now) {
    const p = Math.min((now - start) / duration, 1);
    const eased = 1 - Math.pow(1 - p, 4);          // ease-out-quart
    el.textContent = Math.round(eased * target).toLocaleString();
    if (p < 1) requestAnimationFrame(tick);
  }
  requestAnimationFrame(tick);
}
```

### 5. 列表项逐个浮现（Stagger Reveal）—— 列表"瀑布式"入场

列表项一个接一个淡入上浮，制造"信息正在加载呈现"的流动感。用 `--i` 序号 + `animation-delay`。

```css
.list-item {
  opacity: 0;
  animation: fadeInUp 0.5s var(--ease-out-expo) forwards;
  animation-delay: calc(var(--i, 0) * 50ms);
}
@keyframes fadeInUp { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: translateY(0); } }
```
```js
// JS 批量设序号
document.querySelectorAll('.list-item').forEach((el, i) => el.style.setProperty('--i', i));
```

### 6. 卡片悬停浮起（Hover Lift）—— transform 抬升 + 瞬时加深投影

悬停时卡片轻轻"浮"起来，配合瞬时（不过渡）加深的投影，立体感拉满。`transform` 走合成层，投影瞬时切换不重绘。

```css
.card { transition: transform 0.35s var(--ease-out-expo); }
.card:hover { transform: translateY(-4px); box-shadow: var(--shadow-glass), 0 24px 60px rgba(0,0,0,0.12); }
```

### 7. 涟漪点击（Ripple）—— 点击点扩散光圈

点击瞬间从触点扩散光圈，强化"按下"反馈。一次触发即移除，零常驻。

```js
document.querySelectorAll('[data-ripple]').forEach(el => {
  el.addEventListener('click', e => {
    const r = el.getBoundingClientRect();
    const ripple = document.createElement('span');
    ripple.className = 'ripple';
    ripple.style.left = `${e.clientX - r.left}px`;
    ripple.style.top  = `${e.clientY - r.top}px`;
    el.appendChild(ripple);
    setTimeout(() => ripple.remove(), 600);
  });
});
```
```css
.ripple {
  position: absolute; width: 8px; height: 8px; border-radius: 50%;
  transform: translate(-50%, -50%) scale(0);
  background: radial-gradient(circle, rgba(255,255,255,0.6), transparent 70%);
  animation: rippleOut 0.6s var(--ease-out-expo);
  pointer-events: none;
}
@keyframes rippleOut { to { transform: translate(-50%, -50%) scale(40); opacity: 0; } }
```

### 8. Toast 滑入（Toast Slide-in）—— 消息从下方弹回

提示消息从下方弹入带回弹，比突然出现自然得多。

```css
@keyframes toast-in {
  from { opacity: 0; transform: translateY(20px) scale(0.95); }
  to   { opacity: 1; transform: translateY(0) scale(1); }
}
.toast { animation: toast-in 0.4s var(--ease-spring) both; }
```

### 9. 抖动反馈（Shake）—— 错误/校验失败

表单校验失败时元素横向抖动，比红色文字更有"拒绝"的力度。

```css
@keyframes shake-x {
  0%, 100% { transform: translateX(0); }
  20% { transform: translateX(-5px); } 40% { transform: translateX(5px); }
  60% { transform: translateX(-4px); } 80% { transform: translateX(4px); }
}
.shake { animation: shake-x 0.45s var(--ease-smooth) both; }
```

### 10. 文本打字光标（Caret Blink）—— "正在生成"的期待感

标题/状态逐字显现时尾部光标闪烁，制造"系统正在工作"的氛围。

```css
@keyframes caret-blink { 0%, 50% { opacity: 1; } 51%, 100% { opacity: 0; } }
.caret::after { content: '▋'; animation: caret-blink 1s step-end infinite; color: var(--accent); }
```

### 11. 图标弹跳（Icon Bounce）—— 点击/激活时图标俏皮一弹

图标按钮被点击或激活时，图标先放大再回弹，像被"戳"了一下。比单纯变色反馈俏皮、有生命。纯 `transform: scale`，合成层。

```css
@keyframes icon-bounce {
  0%   { transform: scale(1); }
  30%  { transform: scale(1.3); }
  60%  { transform: scale(0.9); }
  100% { transform: scale(1); }
}
.icon-btn:active .icon { animation: icon-bounce 0.4s var(--ease-spring); }
/* 图标容器要 isolate，避免 transform 影响布局 */
.icon { display: inline-block; transform-origin: center; }
```

```js
// 程序化触发（如收到消息、新数据到达时让图标弹一下）
function bounceIcon(el) {
  el.classList.remove('icon-bounce-on');   // 重置以重播
  void el.offsetWidth;                      // 强制 reflow 重启动画
  el.classList.add('icon-bounce-on');
}
```

### 12. 下划线生长（Underline Grow）—— 导航/链接 hover 下划线从左生长

导航项/链接 hover 时，强调色下划线从左侧"长"出来，比直接出现自然、有方向感。`transform: scaleX(0→1)` + `transform-origin: left`，合成层。

```css
.nav-link { position: relative; }
.nav-link::after {
  content: ''; position: absolute; left: 0; bottom: -2px;
  width: 100%; height: 2px; background: var(--accent);
  transform: scaleX(0); transform-origin: left;
  transition: transform 0.3s var(--ease-out-expo);
}
.nav-link:hover::after,
.nav-link.active::after { transform: scaleX(1); }
/* 当前激活项的下划线常驻（持续可见的"当前位置"指示） */
```

### 13. 徽章弹现（Badge Pop）—— 新消息/未读数出现的瞬间

红点/未读徽章首次出现时，从中心弹出带轻微回弹，吸引注意。纯 `transform: scale` + `opacity`。

```css
@keyframes badge-pop {
  0%   { transform: scale(0); opacity: 0; }
  60%  { transform: scale(1.25); }
  100% { transform: scale(1); opacity: 1; }
}
.badge { animation: badge-pop 0.4s var(--ease-spring) both; }
```

### 14. 聚焦环呼吸（Focus Ring Pulse）—— 输入框聚焦时强调色环轻呼吸

输入框/可聚焦元素聚焦时，强调色光环做极轻的呼吸（scale + opacity），让"已聚焦"状态持续被感知，比静态聚焦环更有生命。合成层，可常驻。

```css
@keyframes focus-ring {
  0%, 100% { transform: scale(1); opacity: 0.6; }
  50%      { transform: scale(1.04); opacity: 0.35; }
}
.focus-ring { position: relative; }
.focus-ring:focus-within::before {
  content: ''; position: absolute; inset: -4px; border-radius: inherit;
  box-shadow: 0 0 0 2px var(--accent);
  animation: focus-ring 2.4s var(--ease-smooth) infinite;
  pointer-events: none;
}
/* 注：呼吸只动 scale/opacity，box-shadow 本身是静态值（不参与动画） */
```

### 15. 卡片光泽跟随（Sheen Follow）—— 鼠标在卡片上移动，高光跟随指针

比 `data-glow` 更轻量：用伪元素 + CSS 变量，鼠标位置驱动一道高光带，强化"玻璃在被抚摸"的触感。pointermove 用 rAF 节流。

```css
[data-sheen-follow] { position: relative; overflow: hidden; }
[data-sheen-follow]::after {
  content: ''; position: absolute; inset: 0;
  background: radial-gradient(
    200px circle at var(--mx, 50%) var(--my, 50%),
    var(--glass-highlight), transparent 50%
  );
  opacity: 0; transition: opacity 0.3s ease; pointer-events: none;
}
[data-sheen-follow]:hover::after { opacity: 0.5; }
```
```js
// rAF 节流，共享队列，一帧合并一次（同 data-glow）
const pending = new Map();
function flush() {
  pending.forEach((v, el) => {
    el.style.setProperty('--mx', v.x + 'px');
    el.style.setProperty('--my', v.y + 'px');
  });
  pending.clear(); rafId = 0;
}
let rafId = 0;
document.querySelectorAll('[data-sheen-follow]').forEach(el => {
  el.addEventListener('pointermove', e => {
    const r = el.getBoundingClientRect();
    pending.set(el, { x: e.clientX - r.left, y: e.clientY - r.top });
    if (!rafId) rafId = requestAnimationFrame(flush);
  });
});
```

### 16. 翻转入场（Flip-in）—— 卡片/面板切换时 3D 翻转

视图切换、卡片翻转（如设置页展开/收起详情）时，元素做 Y 轴翻转入场，有"翻牌"的戏剧感。`transform: rotateY` + `perspective`，合成层。

```css
@keyframes flip-in {
  from { opacity: 0; transform: perspective(800px) rotateY(-25deg); }
  to   { opacity: 1; transform: perspective(800px) rotateY(0); }
}
.flip-in { animation: flip-in 0.5s var(--ease-out-expo) both; }
```

### 17. 强调色光晕脉冲（Glow Pulse）—— 主按钮/焦点元素的呼吸光晕

主按钮、焦点卡片边缘的强调色光晕做极轻呼吸（伪元素 opacity），强化"可交互/重要"的视觉权重。比静态投影更"活"。

```css
@keyframes glow-pulse {
  0%, 100% { opacity: 0.4; transform: scale(1); }
  50%      { opacity: 0.7; transform: scale(1.03); }
}
.glow-pulse { position: relative; }
.glow-pulse::after {
  content: ''; position: absolute; inset: 0; border-radius: inherit;
  box-shadow: 0 0 24px var(--accent-glow);
  animation: glow-pulse 2.6s var(--ease-smooth) infinite;
  pointer-events: none; z-index: -1;
}
/* 注：只动伪元素的 scale/opacity（合成层），box-shadow 是静态值不参与动画 */
```

### 18. 进度流光充盈（Progress Shimmer）—— 确定性进度条的流光

确定性进度条（已知百分比）在填充时，表面叠加一道横向流光掠过，让"正在推进"被持续感知。`transform: translateX` 的伪元素，合成层。

```css
.progress-fill { position: relative; overflow: hidden; background: var(--accent); }
.progress-fill::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent);
  transform: translateX(-100%);
  animation: progress-shimmer 1.6s var(--ease-smooth) infinite;
}
@keyframes progress-shimmer { to { transform: translateX(100%); } }
```

### 灵动感编排心法

| 原则 | 说明 |
|------|------|
| **全走合成层** | 以上 18 个动效 100% 用 transform/opacity/filter，叠加十几个也不掉帧——这才是"性价比灵动感"的本质，也是"该砍则砍"的底气 |
| **常驻 vs 触发** | sheen-sweep / ring-pulse / caret / focus-ring / glow-pulse / 数字环境指标适合**常驻**（周期 ≥2s）；shine / ripple / toast / shake / icon-bounce / badge-pop / flip-in 适合**事件触发**（一次性） |
| **一强多弱** | 一屏选 1 个常驻强动效（如顶栏 sheen-sweep）当主角，其余用触发型点缀，避免满屏常驻晃动 |
| **与玻璃协同** | 灵动动效负责"活"，backdrop-filter 只给主结构负责"透"——两者分工；细碎元素砍掉 blur，把预算和视觉焦点让给灵感动效 |
| **覆盖全场景** | 加载(spinner)、状态(ring-pulse/focus-ring)、入场(stagger/flip-in)、交互(shine/ripple/icon-bounce/hover-lift)、反馈(toast/shake/badge-pop)、氛围(sheen-sweep/caret)——六类场景都有对应的免费灵动 |

> **核心洞察**：灵动感来自这些**几乎免费的合成层小动效的精心编排**——把它们用足，界面就有了呼吸感，而 GPU 依然轻松。它们与加载/状态/入场/滚动/指针/过渡六大维度动效共同构成完整的"七大维度动效体系"，界面才完整"活"。**该砍则砍只砍昂贵装饰（氛围漂移/阴影动画/多余 blur），绝不砍任何功能性动效。**

---

## 光照系统（动态边缘高光）

真正的高级感来自**跟随光源/交互的动态高光**。玻璃边缘会根据鼠标位置亮起：

```html
<div class="card glass glass-sheen" data-glow>
  <h3>鼠标移过，边缘会亮起</h3>
</div>
```

```css
/* 动态光斑层：默认透明，由 JS 设置径向渐变位置 */
[data-glow] { position: relative; }
[data-glow]::after {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: radial-gradient(
    300px circle at var(--mx, 50%) var(--my, 50%),
    var(--glass-highlight),
    transparent 60%
  );
  opacity: 0;
  transition: opacity 0.4s ease;
  pointer-events: none;
}
[data-glow]:hover::after { opacity: 0.6; }
```

```js
// ⚠️ 性能：data-glow 每次鼠标移动都重算径向渐变并重绘，是 hover 卡顿重灾区。
// 必须 rAF 节流（共享队列，一帧只应用一次），且只在 hover 时绑定、leave 即解绑。
function bindGlow(el) {
  let mx = 0, my = 0, raf = 0;
  const flush = () => {
    el.style.setProperty('--mx', mx + 'px');
    el.style.setProperty('--my', my + 'px');
    raf = 0;
  };
  el.addEventListener('pointermove', e => {
    const r = el.getBoundingClientRect();
    mx = e.clientX - r.left; my = e.clientY - r.top;
    if (!raf) raf = requestAnimationFrame(flush);
  });
}
document.querySelectorAll('[data-glow]').forEach(bindGlow);
```
```

---

## 布局与空间

| 原则 | 说明 |
|------|------|
| 内容居中浮起 | 最大宽度 `1120–1200px`，左右留白，玻璃漂浮在氛围层上 |
| 慷慨留白 | 模块间距 ≥ 48px，玻璃内部 padding ≥ 28px |
| 圆角分级 | 卡片 24–28px，按钮/标签 980px（胶囊），输入框 14px |
| 透明度分层 | 主层用 `--glass-tint-strong`，次层 `--glass-tint`，叠层 `--glass-tint-soft`，靠透明度建立层级而非靠颜色 |
| 避免嵌套过深 | 玻璃嵌玻璃最多两层，否则模糊叠加糊成一团 |

---

## 主题色亲和性（如何选主题色）

主题色不仅是装饰，还承载情绪与行业语义。按场景选择：

| 主题色 | 气质 | 适用场景 |
|--------|------|----------|
| **Mint** 薄荷绿 | 清新、生机、轻盈科技感 | 默认；科技产品、健康、数据看板、环保、现代应用 |
| **Azure** 蔚蓝 | 经典、信任、科技通用 | 科技产品、工具型应用、通用官网 |
| **Rose** 玫瑰 | 温柔、感性、时尚 | 美妆、时尚、生活方式、社交 |
| **Violet** 紫罗兰 | 创意、神秘、未来感 | 创意工具、AI 产品、艺术 |
| **Emerald** 翡翠 | 生机、健康、数据感 | 健康、环保、数据看板、金融正向指标 |
| **Amber** 琥珀 | 温暖、活力、食欲 | 餐饮、电商促销、活力品牌 |
| **Indigo** 靛青 | 专业、稳重、金融感 | 金融、企业服务、专业工具 |

默认 **Mint**（薄荷绿 `#00D4AA`）；除非用户指定品牌色或明确场景，否则保持默认。用户给定品牌色时，可仿照色相层结构新增一个 `[data-accent="custom"]` 块。

---

## 执行流程

### 第 1 步：铺氛围背景

先写 `.aura` 色斑层与 `body` 底色——这是玻璃显形的舞台。色斑取 `--aura-1~4`，切换主题色时整体换肤。无背景的玻璃等于失效。

### 第 2 步：注入三段式主题变量

完整复制「CSS 变量定义」中的：明暗基础层（`:root[data-theme]`）+ 全部 7 个色相层（`[data-accent]`）+ 派生色（`--accent-soft/glow`）+ 切换逻辑 JS。默认根节点 `<html data-theme="light" data-accent="mint">`。

### 第 3 步：搭玻璃组件

按内容选择组件：导航栏用 `.glass-strong` + 胶囊形；内容卡片用 `.glass.glass-sheen`；按钮/标签/输入框取对应模板。每个玻璃元素必须带"透射模糊 + 边缘高光 + 镜面投影"三件套。**所有强调色派生值用 `var(--accent)` / `rgba(var(--accent-rgb), α)`，禁止写死具体色值**。

### 第 4 步：加主题切换面板

放置「主题切换面板」组件，让明暗与主题色均可用户切换。绑定 `setTheme` / `setAccent` 并刷新激活态。

### 第 5 步：加生命（七大维度动效体系，完整实现）

按场景叠加动效，逐层制造强感知，每屏遵循"一强多弱"。**七大维度动效都要完整实现**，该砍则砍只砍昂贵装饰：
- **① 加载**：按场景配——按钮操作用按钮 loading 态；连接/认证用 spinner 转圈 或 双圈（遮罩+文案）；列表/面板/卡片内容加载用骨架屏（多层 shimmer）；轻量请求/树加载用三点跳动 loader-dots；上传下载用进度流光 progress-shimmer；未知进度用不定进度条 indeterminate。
- **② 状态**：连接中用 pulse 心跳；在线用 breathe 呼吸 + online-glow 光晕；连接建立用 connect-stream 流光；错误用 shake 抖动 + 红色瞬时切换；离线用静态灰点。每个状态都要有对应反馈。
- **③ 入场**：`data-enter` + `--i` 错落，首屏标题焦点先行；面板/卡片切换用 flip-in 翻转；指标用 count-up 数字滚动。
- **④ 滚动**：`data-reveal` 滚动入场 + `data-parallax` 视差 + 顶部滚动进度条。
- **⑤ 指针**：关键卡片 `data-tilt data-glow data-ripple` 组合；CTA 按钮 `data-magnetic`；导航项 underline-grow 下划线生长；卡片 `data-sheen-follow` 光泽跟随。
- **⑥ 过渡**：列表 stagger reveal + FLIP 重排；折叠用 grid 0fr→1fr；tab 切换 opacity 交叉淡入；换肤用 clip-path 圆形揭露。
- **⑦ 灵动点缀**：顶栏/主卡 sheen-sweep 边缘流光（常驻主角）；按钮 hover btn-shine 光泽划过；徽章 badge-pop 弹现；输入聚焦 focus-ring 呼吸；主按钮 glow-pulse 光晕；状态文本 caret 光标闪烁；图标 icon-bounce 弹跳。
- 所有动效走合成层（transform/opacity/filter），因此即使无限循环（spinner/双圈/三点/脉冲/呼吸/光晕/骨架流光/边缘流光/连接流光）也不掉帧。

### 第 6 步：自检清单

- [ ] 主结构玻璃元素都有 `backdrop-filter`（透射）+ 边缘高光（描边 + inset 白色阴影）？
- [ ] 背景氛围层存在，色斑取 `--aura-1~4`？
- [ ] 字体栈含 SF Pro / PingFang，禁用 Inter/Roboto？
- [ ] 明暗（`data-theme`）与主题色（`data-accent`）两个维度均可切换，默认 light + mint（薄荷绿）？
- [ ] 所有强调色派生值用 `var(--accent)` / `rgba(var(--accent-rgb), α)`，无写死色值？
- [ ] 切换主题色时，氛围色斑、主按钮、聚焦环是否整体换肤？
- [ ] **① 加载：所有异步操作都有动效反馈？不止一种 spinner——按场景用按钮 loading/转圈/双圈/骨架屏/三点跳动/进度流光/不定进度条，没有"干等"状态？**
- [ ] **② 状态：连接中(pulse)/在线(breathe+glow)/连接流光/错误(shake)/离线，每个状态都有对应视觉反馈？**
- [ ] **③ 入场：错落 riseIn + 焦点先行 + popIn/flipIn/countUp 都有实现？**
- [ ] **④ 滚动：data-reveal + 视差 + 滚动进度条至少在长页面实现？**
- [ ] **⑤ 指针：焦点元素有 magnetic/tilt/ripple/sheen-follow（rAF 节流）？**
- [ ] **⑥ 过渡：FLIP/折叠/tab/换肤过渡都有实现，而非跳变？**
- [ ] **⑦ 灵动：边缘流光/光泽/脉冲/浮起/光标/图标弹跳/下划线/徽章/聚焦环/光晕…是否用足？**
- [ ] 动效只动 transform/opacity/filter，用对应物理曲线，无线性 ease？
- [ ] 入场动画 `forwards` 落定；功能性循环（spinner/双圈/三点/脉冲/呼吸/光晕/骨架流光/边缘流光/连接流光）完整保留且走合成层？
- [ ] 滚动/指针监听 rAF 节流 + passive？
- [ ] 圆角符合分级规范？
- [ ] 根节点同时挂 `data-theme` 与 `data-accent`？
- [ ] **氛围色斑只有 `aura-in` 一次性入场（必备），没有 `infinite` 常驻漂移？**（该砍则砍——只砍装饰）
- [ ] **box-shadow 永不参与 transition/动画（静态阴影保留，hover 投影瞬时切换或改伪元素+opacity）？**（该砍则砍——只砍装饰）
- [ ] **没有全局 `*` 通配符 transition（主题过渡只挂 `:root`）；高频密集元素 hover 只过渡 transform/opacity，低频独立元素可过渡 background？**
- [ ] **同屏 `backdrop-filter` 元素 ≤ 8 个，且仅主结构层有 blur；细碎/高频元素（按钮/标签/输入框/状态栏）改用纯 `tint`？**（该砍则砍——只砍多余 blur）
- [ ] **`data-tilt`/`data-magnetic`/`data-glow`/`data-sheen-follow`/视差 全部 rAF 节流 + passive 监听？**
- [ ] **没有运行时 GPU/偏好检测、没有 `data-tier` 分支——流畅靠"只动合成层 + 该砍则砍"保证？**
- [ ] **确认：该砍则砍只砍了装饰（氛围漂移/阴影动画/多余 blur），没有删减任何七大维度功能性动效？**

---

## 设计准则

- **玻璃是主角，背景是配角**：背景色斑服务于玻璃的显形，不可喧宾夺主。
- **靠透明度与模糊分层**，而非靠浓重色块。两个玻璃面板的层级差，用 `tint-strong` → `tint` → `tint-soft` 表达。
- **强调色克制**：`--accent` 只用于主按钮、链接、焦点态、关键数据，不可大面积铺色——大面积色块会破坏玻璃的纯净感。
- **高光永远是白色系**（亮色 `rgba(255,255,255,0.85)`，暗色 `rgba(255,255,255,0.18)`），这是玻璃折射光线的物理本质。
- **主题色随场景选**：默认 Mint（薄荷绿）；优先贴合品牌/行业语义，而非随机选色。
- **明暗与色相解耦**：任何主题色都必须在亮/暗下都成立——dark 变体的 aura 取低明度同色系，accent 取更亮更饱和的同色相。
- **七大维度动效齐全**：加载/状态/入场/滚动/指针/过渡/灵动每个维度都要有对应动效，缺一不可；每维度按场景准备多种变体（加载就有 6 种），而非一种包打天下。
- **动效一强多弱**：每屏只放一个常驻强动效（如顶栏边缘流光），其余用触发型灵动点缀；强感知来自编排节奏，不是堆砌。
- **动效只动合成层**：transform/opacity/filter 走 GPU 合成，可放心无限循环（spinner/双圈/三点/脉冲/呼吸/光晕/骨架流光/边缘流光/连接流光）；width/height/margin/box-shadow 等触发重排重绘的属性禁止参与动画。
- **完整动效，该砍则砍**：七大维度功能性动效一律完整保留，绝不删减；该砍则砍只针对昂贵装饰（氛围常驻漂移、细碎元素多余 blur、box-shadow 动画）。

---

## 常见误区

| 误区 | 正确做法 |
|------|----------|
| **只做一种加载动效（到处都是同一个 spinner）** | 加载是七大维度之一，按场景用 6 种变体：按钮 loading/转圈/双圈/骨架屏（多层）/三点跳动/进度流光/不定进度条，层次更丰富 |
| **状态变化没有视觉反馈**（连接中/在线/错误/离线 UI 没反应） | 状态动效必须完整：pulse 心跳/breathe 呼吸/online-glow 光晕/connect-stream 流光/shake 抖动，每个状态对应一种 |
| **只有灵感动效，缺其他维度**（加载/入场/滚动/过渡都没做） | 七大维度动效齐全才是完整体系：加载/状态/入场/滚动/指针/过渡/灵动缺一不可，灵动只是第⑦维度 |
| **为了性能删掉所有动画**（加载不转圈、状态不脉冲、无入场） | 动效是必需品：保留全部七大维度，全部用 transform/opacity 实现（合成层），集显上也不掉帧 |
| **氛围色斑 4 个 60vmax 模糊圆同时 `infinite` 漂移**（idle ~180 swap/s） | 该砍则砍（只砍此装饰）：保留 `aura-in` 一次性入场，**砍掉常驻漂移**；流动感交给 sheen-sweep 灵感动效（近乎免费） |
| **细碎元素（按钮/标签/输入框）也加 `backdrop-filter`**（数量多，每层每帧模糊重采样） | 该砍则砍（只砍多余 blur）：细碎元素改纯半透明 `tint`（无 blur），模糊预算只给主结构层（同屏 ≤ 8） |
| **动画/transition 里动 `box-shadow`**（触发重绘） | 该砍则砍（只砍阴影动画）：box-shadow 永不入动画，**但静态阴影保留**；阴影变化改伪元素 + opacity 切换；hover 加深投影瞬时切换 |
| **全局 `* { transition: ... }` 通配符过渡**（鼠标滑过任何元素都进重绘过渡） | 主题/明暗过渡只挂 `:root`（离散操作）；高频密集元素 hover 只过渡 transform/opacity，低频独立元素可过渡 background |
| **动画用了 background/width/height/margin 等重绘布局属性**（每帧重排重绘） | 只动 transform/opacity/filter（合成层）；尺寸过渡用 transform: scale 或 grid-template-rows 0fr→1fr |
| **靠运行时检测给集显/偏好做分档**（`detectTier` / `data-tier` / `prefers-reduced-motion` 分支） | 不检测、不分档：单一集显友好基线（完整动效 + 该砍则砍）一揽子保证流畅 |
| **裸 `pointermove`/`scroll` 直接写样式**（每事件一次重算） | 全部 `requestAnimationFrame` 节流 + `{ passive: true }`，一帧合并一次 |
| **同屏堆 10+ 个 `backdrop-filter`**（每层每帧模糊重采样） | ≤ 8 个，且只主结构层；次要/细碎元素用纯半透明 tint |
| 玻璃放在纯白/纯灰平铺背景上 | 必须铺 `.aura` 色斑，玻璃透出色彩才有生命 |
| 只用 `backdrop-filter: blur()`，缺边缘高光 | 必须加白色描边 + `inset` 白色阴影，否则只是"毛玻璃"非"液态玻璃" |
| 玻璃元素加浓重纯色背景填充 | 玻璃靠半透明 `tint`，浓色填充会遮蔽透射效果 |
| 把强调色写死成具体色值（如 `rgba(0,113,227,0.35)`） | 用 `rgba(var(--accent-rgb), 0.35)`，否则切主题色时投影/聚焦环不跟随 |
| 色斑色值写死，不随主题色变 | 色斑必须取 `--aura-1~4`，让主题色切换时整体换肤 |
| 用线性 `ease` / `ease-in-out` 做动效 | 按物理特性选曲线：位移用 `--ease-liquid`，回弹用 `--ease-spring`，入场落定用 `--ease-out-expo` |
| 入场不加 `forwards` 干扰阅读 / 为性能删掉所有循环动效 | 入场一律 `forwards`/`both` 落定；但 spinner/双圈/三点/脉冲/呼吸/光晕/骨架流光/边缘流光/连接流光等功能性循环**必须保留**（走合成层，不掉帧） |
| 全屏所有元素都上强动效（tilt+磁吸+涟漪） | 每屏"一强多弱"，只给焦点元素强动效，其余用克制淡入衬托 |
| 滚动/指针监听不节流，导致掉帧 | 用 `requestAnimationFrame` 节流，监听加 `{ passive: true }` |
| 无视流畅度，为视觉效果堆砌重开销写法 | 完整动效（七大维度全保留）+ 该砍则砍（只砍氛围漂移/阴影动画/多余 blur）——动效完整且流畅，二者兼得 |
| 嵌套三层以上玻璃 | 最多两层，否则模糊糊成一团丢失层级 |
| 强调色大面积铺色（蓝色渐变铺满 hero） | 强调色只点睛，主视觉永远是玻璃透出的背景色斑 |
| 暗色主题把高光也做成强白 | 暗色高光 `rgba(255,255,255,0.18)`，含蓄反光才真实 |
| 暗色下色斑仍用亮色高明度值 | dark 变体 aura 取低明度同色系，夜色才深邃 |
| 忘记 `-webkit-backdrop-filter` | Safari 必须前缀，否则玻璃失效 |
| 用 Inter / Roboto / Arial | 用 SF Pro Display/Text + PingFang SC，通用字体拉低精致感 |
