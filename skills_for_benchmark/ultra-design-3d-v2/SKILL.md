---
name: ultra-design-3d-v2
description: 当需要构建高质量、极具设计感的全面 3D 化前端界面（3D 落地页、空间化 Dashboard、3D 产品展示、沉浸式可视化、立体海报等）时使用。技术栈：TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion + Three.js + React Three Fiber + Drei + postprocessing + Zustand。三大取向 —— ① 玻璃彻底 3D 立体化 + 跟手 tilt（首要准则）：导航/卡片/按钮/输入框/面板/Modal 全是 3D 空间中的真玻璃物件，标题用 troika 3D 文字，正文/数字/图表用 Drei <Html transform> 嵌入同一 3D 空间（清晰/可选/可聚焦，且随透视/缩放/遮挡变形，是 3D 空间公民而非平面贴背景），DOM 玻璃与 mesh 玻璃共享 token + 色散配方视觉一体；每件玻璃随鼠标做 3D tilt（绕 X/Y 轴小幅旋转 + 透视吸附，跟手立体），叠加相机视差，立体感拉满。② 后景彻底无球 · 暗色宇宙星光：**移除一切玻璃球**（单个清晰大球贴在 UI 背后 = 突兀割裂，球群也会抢戏），后景改为 Drei <Sparkles> 焦散发光星光（多层异速闪烁、单 draw call、可被前景玻璃折射、绝不抢戏），默认暗色宇宙底，白/淡蓝星光点点缀，玻璃在暗景中折射发光最出彩。③ 视觉优先 + 主流机流畅：Bloom/景深/色差/暗角/阴影/多真玻璃默认全开，流畅靠工程手段（DPR≤2、samples/阴影分辨率封顶、合并 pass、shader 常量、实例化、累积阴影、轻量降级兜底）而非删减视觉。声明式 R3F + Drei，Zustand 桥接 DOM 主题与 3D 材质/光源 lerp 同步换肤。真玻璃用 Drei MeshTransmissionMaterial（transmission/ior/thickness/chromaticAberration/anisotropic）拉满折射与色散，HDR 环境光 + 主光阴影（只打前景）+ 强调色点光（只脉冲前景 UI）+ 鼠标跟随聚光。可选 glb hero 雕塑（Drei <useGLTF>），但必须 <Suspense> 占位 + 加载失败/超时回退内建几何体，绝不白屏。承诺一套完整的七大维度 3D Motion 动效体系（加载/状态/入场/交互/滚动/过渡/灵动）+ 约 14 种灵动微动，全部走 transform/uniform（合成层或 shader 常量），Motion 驱动 R3F（useMotionValue+useSpring → useFrame）。内置亮/暗双主题与 7 主题色切换（两维正交、任意组合），默认暗色。生成生产级、可直接运行的 TSX 代码，告别平面与伪 3D。
---

# Ultra Design 3D v2 — Liquid Glass 立体场景系统（玻璃彻底 3D · 暗色宇宙星光）

## 概述

本 skill 用于构建**全面 3D 化、视觉优先**的前端界面。界面不再是悬浮在背景上的平面，也不是「3D 只管氛围、内容留在平面」的折中 —— 而是**存在于真实三维空间中的玻璃物件群**：导航、卡片、按钮、输入框、面板、Modal 本身都是 3D 玻璃 mesh，**每件都随鼠标做 3D tilt 跟手**，有景深、有遮挡、有视差、有立体动量；Bloom 泛光、色散、阴影、景深虚化一应俱全；后景是**暗色宇宙里的焦散星光**，只点缀、只发光、绝不抢戏。

**首要准则：玻璃彻底 3D 立体化 + 后景彻底无球。** 「3D 只管氛围、内容留在平面」是伪 3D；「3D 只有一个清晰大球贴在 UI 背后」是突兀割裂。本 skill 的答案是：**所有 UI 控件本身就是真 3D 玻璃物件 + 逐件跟手 tilt**，立体感来自玻璃本身而非背景雕塑；后景**没有玻璃球**，只有散落的焦散星光（暗色宇宙底），退让、发光、可被前景玻璃折射，永远不抢前景。前景玻璃主导、后景星光点缀，两者处于同一 3D 空间、同一光照、同一台相机 —— 这才是「立体且不突兀」。

四个核心取向：

0. **玻璃彻底 3D 立体化 + 跟手 tilt（首要）**：导航/卡片/按钮/输入框/面板/Modal 全是真 3D 玻璃 mesh，标题用 troika 3D 文字，正文/数字/图表用 Drei `<Html transform>` 嵌入 3D 空间（保留清晰度、可选、可聚焦、可访问，且随透视/缩放/遮挡变形，是 3D 空间公民）；DOM 玻璃与 mesh 玻璃共享 token + 色散配方视觉一体。**每件玻璃随鼠标做 3D tilt**（绕 X/Y 轴小幅旋转 + 透视吸附）叠加相机视差，立体感拉满。
1. **后景彻底无球 · 暗色宇宙星光**：**移除一切玻璃球**（中心主球 / 氛围球群都不留 —— 前者是突兀根源，后者仍会抢戏）。后景改为 Drei `<Sparkles>` 焦散发光星光：多层异速闪烁、单 draw call、Additive 混合、可被前景玻璃折射、DOF 虚化后退让。默认暗色宇宙底（深蓝/黑），白/淡蓝星光点点缀。**没有任何清晰球可抢戏**。
2. **视觉优先，主流机流畅**：Bloom、阴影、多真玻璃、色散、景深、后处理**默认全开**，视觉质感与动效只增不减。流畅靠工程手段保证（DPR≤2、samples/阴影分辨率封顶、合并 pass、shader 常量、实例化、累积阴影、轻量降级兜底），不为性能删减视觉。
3. **声明式 R3F + Drei，Zustand 桥接**：用 JSX 声明 3D 场景，组件化玻璃物件；用 Zustand 作为 DOM ↔ R3F 的单一数据源，换肤时 3D 材质/光源 lerp 同步。

### 设计五原则

1. **玻璃彻底 3D + 跟手 tilt（首要）**：UI 控件本身就是真 3D 玻璃物件而非平面 DOM；每件玻璃随鼠标 tilt，立体感来自玻璃本身 + 相机视差，而非背景雕塑。**永远不要一个清晰大球贴在 UI 背后抢戏，也不要背景球群** —— 后景交给星光。
2. **材质真实**：3D 玻璃必须真折射环境光、有色散、有厚度，而非贴一张透明贴图。UI 控件（按钮/卡片/输入框）享有同等的真玻璃材质。
3. **光照生命**：3D 场景必须有真实光源体系 —— HDR 环境光打底、主光塑形（带阴影，只打前景）、强调色点光点睛（脉冲/环绕，**只作用于前景 UI**）、鼠标跟随聚光。光照驱动折射、高光、阴影、色散，构成玻璃的「活」。
4. **空间纵深**：利用相机视差/呼吸/微摆、玻璃逐件 tilt、景深虚化、阴影、全 UI 立体化，制造「可以走进去」的空间感。UI 散布在 z 轴不同深度，而非贴在屏幕上；远景星光落到 DOF 虚化区退让。
5. **视觉优先，主流机流畅**：Bloom/阴影/多真玻璃/色散/景深/后处理默认全开，视觉与动效只增不减。流畅靠工程手段（参数封顶 + 轻量降级 + 合并 pass + 实例化 + 累积阴影），而非删减效果。

---

## 技术栈约定

- **Next.js 14+（App Router）**：动效与 3D 组件用 `'use client'` 标注；主题变量放在 `globals.css`。
- **Tailwind CSS v3/v4**：用 CSS 变量承载 token，通过 `theme.extend` 或 `@theme` 暴露为工具类。组件里**只引用语义 token**，不写死色值。
- **shadcn/ui + Radix**：交互基座与无障碍（`Dialog`/`DropdownMenu`/`Tabs`/`Tooltip`/`Popover` 自带焦点管理、键盘可达、ARIA）。3D 化时这些组件可放进 Drei `<Html>` 嵌入 3D 空间，保留全部交互能力。
- **Motion（`motion/react`）**：DOM 层与「Motion 驱动 R3F」的主力。`useMotionValue` + `useSpring` 产生平滑数值，在 `useFrame` 里读入驱动 mesh transform。
- **Three.js + React Three Fiber（`@react-three/fiber`）**：声明式 3D 场景图，用 JSX 写 `<mesh>`/`<group>`/`<meshPhysicalMaterial>`，与 React 生命周期、Hooks、状态无缝集成。
- **Drei（`@react-three/drei`）**：提供 `MeshTransmissionMaterial`（真玻璃）、`Environment`（HDR 环境光）、`Float`（悬浮）、`PresentationControls`/`OrbitControls`（拖拽）、`ScrollControls`（滚动驱动）、`Html`（DOM 嵌入 3D）、`Text`/`Text3D`（troika 3D 文字）、`ContactShadows`/`AccumulativeShadows`/`SoftShadows`（阴影）、`Sparkles`（粒子尘埃）、`RoundedBox`（圆角几何）、`useGLTF`（glTF 模型）等。
- **@react-three/postprocessing（底层 `postprocessing`）**：合并 Bloom/DOF/色差/暗角/噪点/SMAA 为单一 `EffectComposer` 流水线。
- **glTF（`.glb`/`.gltf`）**：复杂 3D 模型用 glTF 加载，Drei `useGLTF` + `Clone`。模型可作为玻璃主体、折射内容、装饰雕塑。
- **Zustand（`zustand`）**：跨 DOM ↔ R3F 的轻量状态。主题（明暗/主题色）写入 store，DOM 主题面板与 R3F 场景都订阅，换肤时 3D 材质/光源 lerp 同步。
- **lucide-react**：统一图标体系（用于 `<Html>` 内的 DOM 图标）。

```bash
npm install three @react-three/fiber @react-three/drei @react-three/postprocessing postprocessing zustand motion lucide-react
npm install -D @types/three
```

R3F + Drei 让 3D 场景「组件化」—— 玻璃按钮、玻璃卡片、3D 输入框都是可复用组件，可像 shadcn 组件一样组合。这是「全 UI 3D 真融合」在工程上可行的前提：声明式 JSX 与 React 组件树统一、`useFrame` 自动调度渲染循环、Zustand 让 DOM 与 R3F 双向响应式订阅。

---

## 双维度主题系统

主题由**两个正交维度**组合决定：明暗（`data-theme`）× 主题色（`data-accent`）。根节点默认 `<html data-theme="dark" data-accent="mint">`（暗色宇宙底，星光最出彩）。

> **默认暗色**：本 skill 的后景是暗色宇宙 + 焦散星光，暗色底能让星光泛光、让玻璃折射发光最出彩，因此**默认 `data-theme="dark"`**。内置亮/暗双主题与 7 主题色任意组合；用户可切到亮色（亮色下仍是低明度宇宙感底色，星光存在感略弱但仍在）。Zustand store 初值 `mode: 'dark'`。

### CSS 变量（globals.css）

定义明暗基础层 + 7 色相层 + 派生色。关键变量：

| 变量 | 作用 |
|------|------|
| `--accent` / `--accent-rgb` | 当前主题强调色及其 RGB（用于 3D 光源/玻璃吸收色） |
| `--aura-1` ~ `--aura-4` | 氛围色（粒子/env 背景/渐变） |
| `--glass-bg-base` | 场景底色（canvas alpha 兜底、body 背景） |
| `--glass-bg` / `--glass-strong` / `--glass-soft` | 三档玻璃 token（`<Html>` 内 DOM 玻璃层） |
| `--glass-border` / `--glass-highlight` | 玻璃描边 / 高光 |
| `--text-primary` / `--text-secondary` | 正文文字色 |

### 7 色映射（DOM 与 R3F 共享）

```ts
const themeColorMap: Record<string, string> = {
  mint:    '#00D4AA',
  azure:   '#38BDF8',
  rose:    '#FB7185',
  violet:  '#A78BFA',
  emerald: '#34D399',
  amber:   '#FBBF24',
  indigo:  '#818CF8',
}
```

### 主题桥接：Zustand store（核心机制）

```ts
// store/theme.ts —— DOM 与 R3F 共享的单一数据源
import { create } from 'zustand'

type ThemeMode = 'light' | 'dark'
type Accent = 'mint' | 'azure' | 'rose' | 'violet' | 'emerald' | 'amber' | 'indigo'

interface ThemeState {
  mode: ThemeMode
  accent: Accent
  setMode: (m: ThemeMode) => void
  setAccent: (a: Accent) => void
}

export const useThemeStore = create<ThemeState>((set) => ({
  mode: 'dark',
  accent: 'mint',
  setMode: (mode) => {
    set({ mode })
    document.documentElement.setAttribute('data-theme', mode)
    localStorage.setItem('glass-theme', mode)
  },
  setAccent: (accent) => {
    set({ accent })
    document.documentElement.setAttribute('data-accent', accent)
    localStorage.setItem('glass-accent', accent)
  },
}))
```

DOM 主题面板写 store，R3F 玻璃组件读 store 并在 `useFrame` 里 lerp 材质/光源 —— React 响应式更新保证两层同步，无需手写事件 dispatch，lerp 让换肤「流动」而非跳变（见过渡动效）。

---

## 玻璃彻底 3D 立体化 + 后景无球（首要准则 · 最重要）

> 本 skill 的头号设计目标不是「把背景做炫」，而是「**玻璃本身彻底 3D 立体 + 后景永远不抢戏**」。一切材质、光照、后处理、Scene 组装都服务于这一准则。如果某个做法让背景球抢戏、让玻璃留在平面 —— 那它就是错的，哪怕它单独看很酷。

### 为什么后景彻底无球（核心命题）

3D 场景最容易踩的两个坑，都和「球」有关：

| 坑 | 表现 | 为什么错 |
|----|------|---------|
| **单个清晰大球贴在 UI 背后** | 前景 DOM 与立体球两层贴片，球喧宾夺主、前景被挤到角落 | 球是「物件」不是「氛围」，有清晰轮廓就会抢戏 |
| **背景氛围球群**（哪怕虚化低饱和） | 多球散布、半透、漂移，看似退让但仍占据视觉注意力、仍会被 Bloom 打亮成团 | 球群仍是「物件群」，数量一多 + Bloom 泛光 + 点光，必然从氛围变回主角 |

**结论：后景彻底不留任何玻璃球。** 把后景交给**焦散星光** —— 它是「点」不是「物件」，没有抢眼的轮廓，Additive 混合只发光、DOF 虚化后化成宇宙尘埃，且能被前景玻璃折射。**没有任何清晰球可抢戏** —— 这是「立体且不突兀」最干净的解法。

### 后景 = 暗色宇宙 + 焦散星光（Sparkles）

后景由两层组成，都不含球：

```
后景（暗色宇宙底 + 焦散星光）
├─ 底色：暗色宇宙背景（深蓝/黑，scene.background 或 <color attach="background">）
│         默认 data-theme="dark"，亮主题时切深色但仍是低明度宇宙感
└─ 星光：多层 Drei <Sparkles> 焦散发光点
          ├─ 近层：count 多、size 小、speed 快（轻微闪烁，z ≈ -2~-3，略虚）
          ├─ 中层：count 中、size 中、speed 慢（z ≈ -4~-6，DOF 虚化）
          └─ 远层：count 少、size 大、speed 极慢（z ≈ -8~-12，重度虚化，几乎只是光点）
          · 单 draw call / 层；Additive 混合；可被前景玻璃折射；落在 DOF 虚化区退让
          · 白色 + 淡蓝（#E0F2FE / #BAE6FD）为主，少量主题强调色点缀
```

```tsx
// 后景星光：多层异速 <Sparkles>，单 draw call，Additive，可被前景玻璃折射
{/* 近层：细密微闪，略虚 */}
<Sparkles count={120} scale={[14, 8, 6]} size={1.5} speed={0.4} color="#FFFFFF" opacity={0.9} position={[0, 0, -2.5]} />
{/* 中层：中等，DOF 虚化 */}
<Sparkles count={80}  scale={[20, 12, 8]} size={3}   speed={0.15} color="#E0F2FE" opacity={0.6} position={[0, 0, -5]} />
{/* 远层：稀疏大点，重度虚化成宇宙尘埃 */}
<Sparkles count={40}  scale={[28, 18, 12]} size={5} speed={0.06} color="#BAE6FD" opacity={0.4} position={[0, 0, -9]} />
{/* 少量主题强调色星光，呼应品牌色（数量极少，点缀不抢戏） */}
<Sparkles count={15} scale={[16, 10, 8]} size={2.5} speed={0.2} color={accentColor} opacity={0.5} position={[0, 0, -4]} />
```

> 星光是「氛围」而非「物件」：点状、Additive 发光、DOF 虚化，使它们只是宇宙里点点星尘垫在玻璃后方，绝不形成抢眼轮廓。**禁止给星光接点光脉冲、禁止 castShadow、禁止高 Bloom 单独强化**（Bloom threshold 调高，让星光自然泛一点微光即可，不要把它打成主光源）。

### 前景玻璃逐件跟手 tilt（立体感的命脉）

立体感不只来自相机视差 —— 更来自**每件玻璃本身随鼠标做 3D tilt**。这是「玻璃彻底 3D 立体化」区别于「伪 3D」的关键：玻璃不是贴在屏幕上的平面，而是会随手吸附鼠标方向的立体物件。

**原理（不给完整代码，子 agent 按此实现）**：

1. **数据源**：每件玻璃用一个 `<group ref>` 包裹。监听全局 `pointermove`（或在卡片自身 `onPointerMove`），把鼠标归一化偏移（`mx/my ∈ [-0.5, 0.5]`，或相对卡片中心的局部偏移）写入 Motion `useMotionValue`，经 `useSpring` 平滑。
2. **驱动**：在 `useFrame` 里读 spring 平滑值，驱动 group 的 `rotation.x` / `rotation.y`：
   - `rotation.x` = `my * 幅度`（鼠标上下 → 玻璃俯仰）
   - `rotation.y` = `-mx * 幅度`（鼠标左右 → 玻璃偏航）
   - 幅度取 **0.05 ~ 0.15 弧度**（约 3°~8°）—— 轻微但有明显立体感，幅度过大眩晕。
3. **进深**：tilt 同时让玻璃在 z 方向轻微进退（`position.z += mx * 0.1`），强化「伸手可触」的厚度感；可选 hover 时 `scale` 微放大 1.03~1.05。
4. **回正**：鼠标离开（`onPointerOut`）时 motion value 归 0，spring 平滑回正 —— 玻璃缓缓摆平。
5. **与相机视差叠加**：玻璃 tilt（物件级）+ 相机视差（场景级）双层独立叠加，立体感最强；两者用不同 spring 刚度避免同步机械感（玻璃跟手更紧 `stiffness:200`，相机更慵懒 `stiffness:60`）。

| 参数 | 推荐值 | 说明 |
|------|--------|------|
| tilt 幅度 | `0.05 ~ 0.15` rad | 3°~8°，轻微立体不晕 |
| spring stiffness | `150 ~ 250` | 跟手，比相机视差更紧 |
| spring damping | `15 ~ 25` | 不弹也不拖 |
| hover scale | `1.0 → 1.03~1.05` | 轻微放大，强化可触 |
| 回正 | motion value → 0，spring 平滑 | 离开缓缓摆平 |

> **铁律**：tilt 是物件级 transform（`group.rotation`，矩阵运算），**不增 draw call、不增 pass**，可以每件玻璃都挂、常驻不掉帧。立体感的主力就是它 —— 投预算到玻璃本身，而不是背景球。

### 无球自检子清单（首要，逐项必须通过）

- [ ] 后景**没有任何玻璃球**（无中心主球、无氛围球群、无 AmbientSphere / GlassMesh 主体）？
- [ ] 后景是**暗色宇宙底 + 多层 `<Sparkles>` 焦散星光**（近/中/远三层异速 + 少量主题强调色点缀）？
- [ ] 星光**不接点光脉冲、不 castShadow、不被 Bloom 单独强化**，只自然泛一点微光？
- [ ] 星光落在 DOF 虚化区（远层 z=-8~-12 重度虚化），只发光不抢前景？
- [ ] 前景 UI 物件（导航/卡片/按钮/输入框）是真 3D 玻璃 mesh + **逐件跟手 tilt**（rotation.x/y 随鼠标，幅度 0.05~0.15）？
- [ ] tilt 是物件级 transform（走 group.rotation）+ 相机视差叠加，双层不同 spring 刚度？
- [ ] 前景玻璃后方有**可折射内容**（星光 / 中景色块），不是空场景发黑？
- [ ] 正文/数字/图表用 `<Html transform>` 嵌入 3D 空间（清晰精确），DOM 玻璃与 mesh 玻璃**共享 token + 色散配方视觉一体**，而非平面贴背景？
- [ ] 没有「单个清晰大球贴在 UI 背后抢戏」，也没有「背景球群」？

### 错 vs 对（无球的核心图示）

```
❌ 错：单个清晰大球贴在 UI 背后 = 突兀、割裂
   相机                                  前景UI            大球
     👁 ──────────────────────────── 🟦🟦(清晰) ─── ⬤(清晰·抢戏·自转·脉冲高光)
     · 前景 DOM 与立体球两层贴片，球喧宾夺主，前景被挤到角落

❌ 也错：背景氛围球群（哪怕虚化低饱和）
   相机    前景UI              球群(z=-6~-12)
     👁 ── 🟦🟦(清晰主导) ── ∘∘∘∘(半透·漂移·仍会被 Bloom 打亮成团·仍抢戏)
     · 球群仍是物件群，数量一多就变回主角

✅ 对：玻璃逐件 tilt + 暗色宇宙星光后景 = 融合
   相机      前景UI(tilt·跟手)            星光(多层·Additive·DOF虚化)
     👁 ─── 🟦🟦(真3D玻璃·随鼠标tilt·主导) ── ✦✦✦(焦散星光·只发光·可折射·退让)
     · 立体感来自玻璃本身 tilt + 相机视差；后景只有星光，没有任何球可抢戏
     · 玻璃折射后方星光 → 玻璃内部透出星尘，质感拉满
```

---

## Liquid Glass 材质原理（真物理玻璃）

| 要素 | 物理含义 |
|------|---------|
| **透射（Transmission）** | 真**折射透射**：背景物件被真实折射扭曲，透过玻璃可见 |
| **边缘高光（Specular Edge）** | **菲涅尔反射**（Fresnel）：掠射角边缘反射率急剧上升，自然形成发光边；`clearcoat` 清漆强化顶面反光 |
| **镜面反射（Reflection）** | `scene.environment`（HDR envMap）真实环境反射，玻璃顶面映射出环境光 |
| **色散（Dispersion）** | 折射时不同波长光分离，玻璃边缘出现彩虹色散 —— 真 3D 质感 |
| **厚度（Thickness）** | 光线在玻璃内部穿行的距离，决定折射强度与内部吸收色（`attenuationColor`）的浓度 |

> **关键认知**：没有环境的玻璃等于没有玻璃。3D 玻璃必须放在有 HDR 环境、有可折射内容（粒子/色块/其他物件/其他玻璃）、有光源的场景中。**环境层 + 可折射内容层 + 玻璃层 + 光源层**永远成组出现。

---

## 真 3D 玻璃材质配方（核心，MeshTransmissionMaterial 拉满）

> **配方专供前景 UI**：前景 UI 物件要清晰、要被光照折射、要色散拉满（视觉主导）—— 它们是场景的主角。**后景无玻璃球**，不存在远景玻璃配方（旧版的「远景降饱和球」已废弃，后景交给暗色宇宙星光）。前景 UI 与星光共享同一套 envMap 与光照。

Drei `MeshTransmissionMaterial` 是 `MeshPhysicalMaterial` 的增强封装，**默认开启 chromatic 色散、anisotropic 各向异性、可调 samples/resolution**，把折射质感拉到电影级：

```tsx
import { MeshTransmissionMaterial, Environment } from '@react-three/drei'
import { useRef } from 'react'
import { useFrame } from '@react-three/fiber'

// 【前景 UI 玻璃】卡片/按钮/输入框/主体物件用 —— 清晰、色散拉满、视觉主导
function GlassMesh({ position }: { position?: [number, number, number] }) {
  const matRef = useRef<any>(null!)
  useFrame((state) => {
    const t = state.clock.elapsedTime
    // 灵动：玻璃内部折射色散轻微呼吸（shader 常量，零额外 pass）
    if (matRef.current) matRef.current.chromaticBlur = 0.05 + Math.sin(t * 0.5) * 0.02
  })
  return (
    <mesh position={position} castShadow>
      <icosahedronGeometry args={[1.6, 4]} />
      <MeshTransmissionMaterial
        ref={matRef}
        background={<Environment preset="city" />}   {/* 折射采样专用环境（可比主 env 更丰富） */}
        transmission={1}           {/* 完全透射 */}
        thickness={1.5}            {/* 厚度：越大折射越强、内部吸收色越浓 */}
        roughness={0.04}           {/* 极光滑 */}
        ior={1.5}                  {/* 玻璃折射率 */}
        chromaticAberration={0.5}  {/* 色散：折射边缘彩虹色散 */}
        anisotropy={0.1}           {/* 各向异性反射 */}
        distortion={0.5}           {/* 折射扭曲 */}
        distortionScale={0.3}      {/* 扭曲尺度 */}
        temporalDistortion={0.1}   {/* 时间扭曲（折射流动感） */}
        clearcoat={1}              {/* 清漆层，强化顶面反光 */}
        attenuationDistance={1.5}  {/* 颜色吸收距离 */}
        attenuationColor="#00D4AA" {/* 内部吸收色（Zustand store 里同步主题色） */}
        color="#ffffff"
        samples={6}                {/* 透射采样数（可按 fill rate 压力调高/低） */}
        resolution={512}           {/* 透射缓冲分辨率 */}
        backside={false}           {/* 双面玻璃更真实但更贵，可开 */}
      />
    </mesh>
  )
}
```

> **注意角色**：`GlassMesh` 这套全拉满配方专供**前景 UI 物件**（卡片本体/主体雕塑化的 UI 元素 / 可选的 hero 雕塑）使用 —— 前景是主角，该清晰、该色散拉满。**后景无玻璃球**，不要用这套或任何降饱和球配方做后景（后景只有暗色宇宙 + `<Sparkles>` 星光）。

### MeshTransmissionMaterial 生效四要素（缺一不可）

| 要素 | 作用 | 实现 |
|------|------|------|
| `Environment`（scene.environment） | 玻璃反射/折射的「环境内容」，无则发黑 | Drei `<Environment preset="city">` 或自定义 HDR |
| `background`（材质级） | 折射采样专用环境，可比主 scene.environment 更丰富 | `<MeshTransmissionMaterial background={<Environment .../>} />` |
| 背后有可折射内容 | 空场景里玻璃无折射可见 | 场景里放粒子（`<Sparkles>`）、色块、其他几何体、其他玻璃 |
| 多面/双面（可选） | 真实玻璃应有内外两面折射 | `backside` 或 `side={THREE.DoubleSide}` |

> 视觉优先下 `MeshTransmissionMaterial` 是**每个 UI 控件都可用**的材质 —— 玻璃按钮、玻璃卡片、玻璃输入框都是它的 mesh。若透射太多导致 fill rate 紧张，**调 `samples`/`resolution` 而非删材质**。

### 玻璃材质变体（靠透明度与厚度建立层级，玻璃嵌玻璃最多两层）

```tsx
// glass-strong：主导航/主卡片（厚、清漆强、色散明显）—— 前景主导
<MeshTransmissionMaterial thickness={1.5} clearcoat={1} chromaticAberration={0.5} ior={1.5} />
// glass：普通卡片/按钮（中）—— 前景
<MeshTransmissionMaterial thickness={0.8} clearcoat={0.6} chromaticAberration={0.3} ior={1.45} />
// glass-soft：标签/药丸（薄、半透明 tint 为主）—— 前景次级
<meshPhysicalMaterial transmission={0.6} thickness={0.3} roughness={0.1} transparent opacity={0.7} />
// 后景无玻璃球 —— 氛围完全交给暗色宇宙 + <Sparkles> 焦散星光（见「后景无球」章），不存在 glass-aura 球体配方
```

### DOM 玻璃 × 3D 玻璃视觉一体配方（智能融合的关键）

正文/数字/图表用 `<Html transform>` 嵌入 3D 空间（清晰精确），但其内部的 DOM 玻璃必须与 3D 玻璃 mesh **视觉一体** —— 否则 DOM 块会像一张平面贴纸糊在立体卡片上，仍是割裂。做法：DOM 玻璃与 mesh 玻璃**共享同一组 CSS token**，并用 `backdrop-filter` 模糊/折射 + 边缘渐变伪色散，使其在同一光照下看不出「这是 DOM 那是 3D」。

```css
/* globals.css —— DOM 玻璃与 3D 玻璃共享 token（3D 侧 attenuationColor 读同一变量） */
:root {
  --glass-bg:        rgba(255,255,255,0.55);   /* 与前景 MeshTransmissionMaterial 的半透感对齐 */
  --glass-strong:    rgba(255,255,255,0.72);
  --glass-soft:      rgba(255,255,255,0.38);
  --glass-border:    rgba(255,255,255,0.65);   /* 玻璃描边 = 菲涅尔边缘高光的 DOM 对应 */
  --glass-highlight: rgba(255,255,255,0.9);    /* 顶面清漆高光 */
  --glass-sheen:     linear-gradient(135deg, rgba(255,255,255,0.6) 0%, transparent 45%, rgba(255,255,255,0.25) 100%);
}

/* DOM 玻璃：与 3D 玻璃同款质感 —— 半透 + 模糊折射 + 边缘高光 + 伪色散 */
.glass {
  background: var(--glass-bg);
  backdrop-filter: blur(18px) saturate(160%);          /* ≈ 3D 玻璃的折射模糊 + 浓度 */
  -webkit-backdrop-filter: blur(18px) saturate(160%);
  border: 1px solid var(--glass-border);
  box-shadow:
    inset 0 1px 1px var(--glass-highlight),             /* 顶面清漆高光，对应 clearcoat */
    inset 0 0 22px rgba(255,255,255,0.18),              /* 内部厚度感 */
    0 12px 40px rgba(0,0,0,0.18);                       /* 投影接地 */
  position: relative;
  overflow: hidden;
}
/* 伪色散：玻璃边缘一道极细彩虹渐变，呼应 3D 玻璃的 chromaticAberration */
.glass::after {
  content: '';
  position: absolute; inset: 0;
  background: linear-gradient(115deg,
    transparent 0%, rgba(0,212,170,0.06) 30%, rgba(56,189,248,0.05) 55%, transparent 80%);
  mix-blend-mode: screen;
  pointer-events: none;
}
/* 边缘流光高光，呼应菲涅尔反射 */
.glass-sheen { background-image: var(--glass-sheen); }
```

> 这套 CSS 让 `<Html transform>` 内的 DOM 正文块与包裹它的 3D 玻璃 mesh **看不出接缝** —— 同样的半透、同样的模糊、同样的边缘高光、同样的色散调子。智能融合的本质：**正文虽然用 DOM 渲染（为清晰），但它「长得」跟 3D 玻璃一模一样，且住在 3D 空间里随相机变形**，于是视觉上与场景完全一体。

---

## 光照系统（3D 光场，阴影全开）

玻璃的边缘高光、色散、反射、落地阴影全部由真实 3D 光源物理驱动。

> **无球相关的光照分配**：强调色点光、鼠标聚光、带阴影的主光**只作用于前景 UI 物件**。后景星光**只靠自身 Additive 自发光 + 环境/env 柔光垫底** —— 不接点光脉冲、不 castShadow、不被聚光照射（星光是 `<Sparkles>` 点，本就不接受这些光照，天然不会被打亮成清晰轮廓）。光照分层 = 后景靠 Additive 自发光发光、前景靠点光/聚光塑形。

```tsx
function Lights() {
  const accent = useThemeStore((s) => s.accent)
  const accentColor = themeColorMap[accent]
  return (
    <>
      {/* 1. HDR 环境光打底（玻璃反射/折射的主环境）—— 在 Scene 层用 <Environment>；后景星光靠自身发光 + env 柔光 */}

      {/* 2. 主方向光：塑形 + 阴影（启用 castShadow）—— 照前景 UI 物件 */}
      <directionalLight
        position={[5, 8, 6]}
        intensity={1.5}
        castShadow
        shadow-mapSize={[2048, 2048]}        {/* 高分辨率阴影贴图（封顶，勿无限大） */}
        shadow-bias={-0.0001}
      >
        <orthographicCamera attach="shadow-camera" args={[-8, 8, 8, -8, 0.1, 30]} />
      </directionalLight>

      {/* 3. 环境光补底，避免阴影面死黑 */}
      <ambientLight intensity={0.4} />

      {/* 4. 强调色点光：随主题色，【只环绕/脉冲前景 UI 物件】，制造边缘染色 + 脉冲 */}
      <OrbitAccentLight color={accentColor} />

      {/* 5. 鼠标跟随聚光灯：交互时光斑跟随【前景】，最强感知 */}
      <FollowSpotLight />
    </>
  )
}
```

### 软阴影 / 累积阴影

普通 `castShadow` 是硬阴影 + 每帧重算 shadow map。视觉优先下用更好的阴影质量：

```tsx
import { AccumulativeShadows, RandomizedLight, SoftShadows, ContactShadows } from '@react-three/drei'

// 方案 A：接触阴影（轻量、常用于玻璃物件底部接地感）—— 静态、单帧烘焙
<ContactShadows position={[0, -3, 0]} opacity={0.5} scale={20} blur={2.5} far={4} color="#000" />

// 方案 B：累积阴影（最高质量软阴影，单帧累积后定格，不每帧重算）
<AccumulativeShadows position={[0, -3, 0]} frames={60} alphaTest={0.85} opacity={0.8} scale={20} color="#000">
  <RandomizedLight radius={4} ambient={0.5} intensity={1.5} position={[5, 8, 6]} bias={0.001} />
</AccumulativeShadows>

// 方案 C：全局软阴影（让所有 castShadow 更柔）—— 挂在 Canvas 内一次性
<SoftShadows size={25} samples={16} focus={0.6} />
```

> `AccumulativeShadows` 质量最高但适合静态/半静态场景（累积后定格，物件动得多需重算）；`ContactShadows` 最轻适合接地；`SoftShadows` 全局柔和。按场景选。

---

## 后处理：全开（电影级质感的来源）

用 `@react-three/postprocessing`，所有 pass 合并到单一 `EffectComposer`，比裸叠加高效：

```tsx
import {
  EffectComposer, Bloom, DepthOfField, Vignette, ChromaticAberration, Noise, SMAA,
} from '@react-three/postprocessing'
import { BlendFunction, KernelSize } from 'postprocessing'
import { Vector2 } from 'three'

<EffectComposer multisampling={4} enableNormalPass={false}>
  <SMAA />                                              {/* 高质抗锯齿 */}

  {/* Bloom：玻璃边缘高光、点光、星光泛光 */}
  <Bloom
    intensity={0.6}
    luminanceThreshold={0.6}        {/* 只让高亮部分泛光，不糊全场；星光自然泛一点微光，但不被单独强化 */}
    luminanceSmoothing={0.9}
    mipmapBlur                       {/* mipmap Bloom 更柔和、更高效 */}
    kernelSize={KernelSize.LARGE}
  />

  {/* 景深虚化：焦点落在【前景 UI】（z≈1），远层星光（z=-8~-12）必然落进虚化区退让 —— 这是后景星光不抢戏的关键 */}
  <DepthOfField
    focusDistance={0.018}      {/* 对焦前景 UI（焦点近，远景必虚） */}
    focalLength={0.06}         {/* 焦段稍长，虚化过渡更明显 */}
    bokehScale={4}             {/* 散景更大，远层星光化成宇宙尘埃 */}
    height={480}
  />

  {/* 色差：镜头边缘的色彩分离，电影感 */}
  <ChromaticAberration
    blendFunction={BlendFunction.NORMAL}
    offset={new Vector2(0.0005, 0.0005)}
    radialModulation={false}
    modulationOffset={0}
  />

  {/* 暗角：画面四角压暗，聚焦中心前景 */}
  <Vignette eskil={false} offset={0.3} darkness={0.7} />

  {/* 噪点：胶片颗粒，掩盖色带、增加质感 */}
  <Noise opacity={0.03} blendFunction={BlendFunction.OVERLAY} />
</EffectComposer>
```

> **无球关键的 DOF 调参**：旧版焦点模糊不清、远景球不一定虚。无球版把焦点明确钉在前景 UI、加大 `bokehScale`，使远层星光（z=-8~-12）**必然落进虚化区** —— 星光化成宇宙尘埃垫在内容后方，这是它们「退让、不抢戏」的物理保证。

| 后处理 | 视觉作用 |
|--------|---------|
| **Bloom** | 高光泛光，玻璃边缘、点光、星光的「亮」。`mipmapBlur` + `luminanceThreshold` 控制只泛高亮 —— **调高 threshold（0.6）只让前景高光泛光，星光自然泛一点微光但不被单独强化、不打成主光源** |
| **景深 DOF** | 远近虚化，纵深。**焦点钉在前景 UI**，远层星光落进虚化区退让成尘埃 |
| **色差** | 镜头色散，电影感，`offset` 很小即可 |
| **暗角** | 四角压暗，聚焦中心前景 |
| **噪点** | 胶片颗粒，掩盖色带，`opacity` 极低 |
| **SMAA** | 高质抗锯齿，替代 `antialias:true` |

---

## 全 UI 3D 真融合（UI 控件本身就是真 3D 物件）

导航、卡片、按钮、输入框、面板、Modal 全部成为 3D 空间中的玻璃 mesh，用 R3F 组件化封装。

### 3D 玻璃卡片（GlassCard3D · 智能融合）

卡片是 3D 玻璃 mesh（被光照、折射、随场景立体），**标题用 troika 真 3D 文字（被光照折射）**，**正文/数字/图表用 `<Html transform>` 嵌入（DOM 锐利清晰，但随 3D 透视/缩放/遮挡，是 3D 空间公民）**。DOM 玻璃与卡片 mesh 共享 token + 色散配方 → 视觉无缝，不是平面贴背景。

```tsx
'use client'
import { MeshTransmissionMaterial, Html, Float, RoundedBox, Text } from '@react-three/drei'

function GlassCard3D({ position, title, children }: {
  position: [number, number, number]
  title: string
  children: React.ReactNode
}) {
  return (
    <Float speed={2} rotationIntensity={0.2} floatIntensity={0.4}>
      {/* ★ 跟手 tilt：用 <group ref> 包裹卡片，useMotionValue(鼠标偏移)+useSpring → useFrame 驱动
            group.rotation.x/y（幅度 0.05~0.15，见「前景玻璃逐件 tilt」章）。这是卡片立体感的命脉，
            这里省略 tilt 实现代码，仅示意包裹位置；务必补上 tilt 驱动，否则卡片是平面贴片。 */}
      <group position={position}>
        {/* 卡片玻璃本体（前景真玻璃，色散拉满） */}
        <RoundedBox args={[3, 2, 0.3]} radius={0.15} smoothness={4} castShadow>
          <MeshTransmissionMaterial thickness={0.8} chromaticAberration={0.3} ior={1.45} />
        </RoundedBox>

        {/* 标题：troika 真 3D 文字（被光照、折射、雕塑感） */}
        <Text position={[0, 0.6, 0.2]} fontSize={0.18} letterSpacing={-0.02} color="#1D1D1F">
          {title}
        </Text>

        {/* 正文/数字/图表：<Html transform> 嵌入 —— DOM 锐利清晰，但随 3D 透视/缩放/遮挡
            DOM 玻璃用 .glass 类（共享 token + 伪色散），与外层 mesh 视觉一体 */}
        <Html transform position={[0, -0.1, 0.2]} distanceFactor={4} occlude>
          <div className="glass rounded-2xl p-3 w-64">{children}</div>
        </Html>
      </group>
    </Float>
  )
}
```

> **智能融合 + tilt 要点**：`<Html transform>` 不是「把 DOM 盖在 canvas 上」，而是把 DOM **注册进 3D 场景** —— 它随相机透视变形、随距离缩放、被前方玻璃遮挡。配上 `.glass` 类（与 mesh 共享 token + 伪色散），DOM 正文块「长得」跟 3D 玻璃一模一样、又住在 3D 空间里，于是视觉上与卡片 mesh 完全一体 —— 这就是「全 3D 真融合」同时保留数据清晰度的工程实现。**外层 group 的跟手 tilt 让整张卡片随鼠标立体吸附，正文 `<Html>` 随之一起倾斜**（它是 3D 空间公民，跟随父 group 变换），立体感贯穿文字与玻璃。

一个带数据/图表的卡片正文示例（`<Html transform>` 内是普通 DOM，可放任何 shadcn/Radix 组件、Canvas 图表）：

```tsx
<GlassCard3D position={[-2.5, -0.5, 0.5]} title="营收总览">
  <div className="space-y-2">
    <p className="text-2xl font-semibold tabular-nums text-text-primary">¥ 1,284,500</p>
    <div className="h-10 w-full rounded-md bg-[var(--glass-soft)]">
      {/* 任意精确图表（Canvas/SVG/shadcn Chart），DOM 渲染不糊 */}
      <Sparkline data={[3,5,4,7,9,8,11]} />
    </div>
    <p className="text-sm text-text-secondary">同比 <span className="text-[var(--accent)]">+12.4% ↑</span></p>
  </div>
</GlassCard3D>
```

### 3D 玻璃按钮（GlassButton3D，hover 浮起 + 光泽 + active 回弹）

```tsx
'use client'
import { useRef, useState } from 'react'
import { useFrame } from '@react-three/fiber'
import { RoundedBox, MeshTransmissionMaterial, Text } from '@react-three/drei'
import * as THREE from 'three'

function GlassButton3D({ position, children, onClick }: {
  position: [number, number, number]
  children: React.ReactNode
  onClick?: () => void
}) {
  const groupRef = useRef<THREE.Group>(null!)
  const [hovered, setHovered] = useState(false)
  const target = useRef({ y: 0, z: 0, scale: 1 })

  // ★ 跟手 tilt：除下方 hover/active 外，按钮也应挂 tilt（group.rotation 随鼠标，幅度 0.05~0.1）。
  //   hover 浮起 + active 回弹（lerp 落定，走 transform）
  useFrame(() => {
    const g = groupRef.current
    if (!g) return
    g.position.y += (position[1] + target.current.y - g.position.y) * 0.1
    g.position.z += (position[2] + target.current.z - g.position.z) * 0.1
    const s = target.current.scale
    g.scale.x += (s - g.scale.x) * 0.2
    g.scale.y += (s - g.scale.y) * 0.2
    g.scale.z += (s - g.scale.z) * 0.2
  })

  return (
    <group ref={groupRef} position={position}>
      <RoundedBox
        args={[1.6, 0.5, 0.2]} radius={0.1} smoothness={4}
        onPointerOver={(e) => { e.stopPropagation(); setHovered(true); target.current = { y: 0.08, z: 0.05, scale: 1.05 }; document.body.style.cursor = 'pointer' }}
        onPointerOut={() => { setHovered(false); target.current = { y: 0, z: 0, scale: 1 }; document.body.style.cursor = 'auto' }}
        onPointerDown={(e) => { e.stopPropagation(); target.current.scale = 0.95 }}
        onPointerUp={(e) => { e.stopPropagation(); target.current.scale = hovered ? 1.05 : 1; onClick?.() }}
        castShadow
      >
        <MeshTransmissionMaterial thickness={0.6} chromaticAberration={0.25} ior={1.45} roughness={0.05} />
      </RoundedBox>
      <Text position={[0, 0, 0.12]} fontSize={0.16} color="#1D1D1F">{children as any}</Text>
    </group>
  )
}
```

### 全 UI 3D 真融合选型表

| UI 控件 | 3D 化方式 | 文字处理 |
|---------|----------|---------|
| 导航栏 | `<RoundedBox>` 玻璃条 + `<Float>` 微悬浮 | Logo/菜单项用 3D 文字，下拉用 `<Html>` |
| 卡片 | `<RoundedBox>` 玻璃 + `<Float>` | 标题 3D 文字，正文 `<Html>` |
| 主按钮 | `<RoundedBox>` 胶囊玻璃 + hover/active lerp | 3D 文字 |
| 输入框 | `<RoundedBox>` 玻璃面板，聚焦 emissive 边缘 | `<Html>` 内 `<input>`（聚焦输入） |
| 标签/药丸 | `<meshPhysicalMaterial>` 半透 tint | 3D 文字 |
| Modal | `<RoundedBox>` 玻璃，入场 scale+rotate | 标题 3D 文字，表单 `<Html>` |
| 数据看板 | `<RoundedBox>` 玻璃容器 | KPI 大数字 3D 文字（tabular-nums），明细数字/图表 `<Html transform>` |

> **取舍心法（智能融合的核心）**：需要被光照、折射、雕塑感的（标题、KPI 大数字、按钮 label）→ 真 3D 文字/mesh；需要精确清晰、可选、可聚焦、滚动的（正文、表单、数字明细、图表、列表）→ `<Html transform>` 嵌入。**正文/数字/图表统一用 `<Html transform>`，绝不塞 troika（长文会糊、数字失精度）** —— 这是「智能融合」与「纯全 3D」的分水岭：前者清晰又一体，后者纯视觉但数据糊成团（反扣分）。两者共享玻璃 token + 色散配方，在 3D 空间视觉一体共存。

### 可选 glb hero 雕塑（必须回退，绝不白屏）

hero 区可用一个 glTF 模型（`.glb`）作为「英雄玻璃雕塑」（Drei `<useGLTF>` + `<Clone>`），给场景一个独特的视觉锚点。但**密封测试环境下 subagent 可能下不到模型 / 路径错 / 加载超时**，必须写回退，**任何情况下都不白屏**：

```tsx
'use client'
import { Suspense, useState, useEffect } from 'react'
import { useGLTF } from '@react-three/drei'
import { MeshTransmissionMaterial } from '@react-three/drei'

// 1. 真实 glb 雕塑（加载成功才渲染）
function GlbSculpture({ url }: { url: string }) {
  const { scene } = useGLTF(url)
  return <primitive object={scene} />
}

// 2. 内建几何体回退（glb 下不到 / 超时 / 报错时用）—— 绝不白屏
function FallbackSculpture() {
  return (
    <mesh castShadow>
      <icosahedronGeometry args={[1.6, 4]} />
      <MeshTransmissionMaterial thickness={1.5} chromaticAberration={0.5} ior={1.5} />
    </mesh>
  )
}

// 3. hero 区：glb 优先 + 加载超时/失败回退
function HeroSculpture({ url = '/models/hero.glb' }) {
  const [failed, setFailed] = useState(false)
  useEffect(() => {
    // 加载超时兜底（密封环境 subagent 可能下不到模型，超时即回退）
    const timer = setTimeout(() => setFailed(true), 4000)
    return () => clearTimeout(timer)
  }, [])
  if (failed) return <FallbackSculpture />
  return (
    <Suspense fallback={<FallbackSculpture />}>
      <ErrorBoundary onError={() => setFailed(true)} fallback={<FallbackSculpture />}>
        <GlbSculpture url={url} />
      </ErrorBoundary>
    </Suspense>
  )
}
```

> **回退铁律**：glb 是「锦上添花」，**绝不能因为它导致白屏**。`<Suspense>` 占位 + 超时定时器 + `<ErrorBoundary>` 报错捕获，三重兜底保证下不到模型时立即回退内建几何体雕塑。**默认产物里可以不自带 glb 文件**（用 `FallbackSculpture` 即可），glb 仅作为「资源齐全时」的增强路径 —— 这样密封 benchmark 的 subagent 即使零外部资源也能独立产出完整可运行工程。

---

## 文字处理（标题 3D 文字 + 正文 Html 嵌入）

### 3D 文字（troika，标题/数据/按钮 label）

Drei `<Text>` 底层 troika-three-text，SDF 字体在 GPU 渲染，可缩放不失真、被光照、可折射、随场景立体：

```tsx
import { Text3D, Text } from '@react-three/drei'

// 巨型 3D 标题（带厚度，雕塑感最强）
<Text3D font="/fonts/Geist_Bold.json" size={0.8} height={0.15} curveSegments={12}
  bevelEnabled bevelThickness={0.02} bevelSize={0.02} bevelSegments={5}>
  HELLO 3D
  <meshStandardMaterial color={accentColor} metalness={0.4} roughness={0.15} envMapIntensity={1.5} />
</Text3D>

// 平面 SDF 文字（轻量，用于卡片标题/按钮 label）
<Text font="/fonts/Geist-Bold-msdf.json" fontSize={0.2} color="#1D1D1F" anchorX="center">
  标题
  <meshBasicMaterial toneMapped={false} />
</Text>
```

> `toneMapped={false}` 让文字颜色不被 ACES tone mapping 压暗，保持精确。数据标题用 `fontVariant="tabular-nums"`。

### 正文/表单（Html 嵌入，清晰可访问）

```tsx
import { Html } from '@react-three/drei'

<Html
  transform                              // DOM 随 3D 变换（透视、缩放、遮挡）
  distanceFactor={8}
  position={[0, -1.5, 0.3]}
  occlude="blending"                     // 可被前方 3D 物件遮挡（z-blending）
  zIndexRange={[10, 0]}
>
  <div className="glass glass-sheen rounded-3xl p-6 max-w-md">
    <p className="text-text-secondary leading-relaxed">正文用 DOM 渲染：清晰、可选中、可聚焦。</p>
    <input className="glass rounded-[14px] px-4 py-2 mt-3 w-full" placeholder="输入" />
  </div>
</Html>
```

| Html 模式 | 特性 | 适用 |
|-----------|------|------|
| `transform`（推荐） | DOM 随 3D 变换（透视/缩放/遮挡），像贴在 3D 物体表面 | 卡片正文、3D 空间内的表单/图表 |
| 非 transform | DOM 固定屏幕坐标，仅 z 排序随 3D | 主题面板、Tooltip、全局 Toast |

> `<Html transform>` 让 DOM 内容真正「住进」3D 空间 —— 随相机透视变形、随距离缩放、可被前方玻璃遮挡。这是「全 UI 3D 真融合」同时保留 DOM 可用性（智能融合）的关键。

---

## 混合分层架构（全 UI 3D 真融合 · 两层 z 纵深版 · 无球）

UI 控件都 3D 化了，但并非「全部塞进一个 canvas」。架构沿 z 轴分**远景星光 → 前景 UI 物件**两层（无球，故无中景球层），DOM 覆盖层只承载少量纯交互控件。两层共享同一套光照、相机、envMap，前景与后景因此处于同一世界。

```
┌────────────────────────────────────────────────────────────────────┐
│  Canvas（R3F）  真 3D 场景层 · 两层 z 纵深（无球）                     │
│    ├─ 暗色宇宙底（scene.background / <color>，深蓝·黑）                │
│    ├─ Environment（HDR envMap，两层共享）                              │
│    ├─ 光源体系（环境光 + 主光+阴影 + 强调点光[只打前景] + 聚光跟随）      │
│    │                                                                    │
│    ├─ 【后景·焦散星光】 z=-2~-12（三层异速）                             │
│    │    多层 <Sparkles> Additive 发光点 · 白/淡蓝 + 少量主题色点缀          │
│    │    单 draw call/层 · 可被前景玻璃折射 · DOF 虚化后退让 · 只发光        │
│    │    （不接点光/不投影/不主泛光 —— 退让，无任何球）                      │
│    │                                                                    │
│    ├─ 【前景·UI 物件】 z=0.5~1.5                                       │
│    │    3D 玻璃 UI 物件（导航/卡片/按钮/输入框/面板/Modal）真玻璃拉满       │
│    │    ★ 每件玻璃跟手 tilt（group.rotation 随鼠标，幅度 0.05~0.15）       │
│    │    3D 文字标题（troika，被光照折射）                                 │
│    │    <Html transform> 嵌入层（正文/数字/图表，DOM 原生清晰·住进 3D）     │
│    │                                                                    │
│    ├─ ContactShadows（仅接前景物件，星光不投影）                         │
│    ├─ 后处理（Bloom[只泛前景高光+星光微泛] + DOF[焦点钉前景·虚化远星光]    │
│    │         + 色差 + 暗角 + 噪点）                                       │
│    └─ 相机（视差/呼吸/微摆/OrbitControls/ScrollControls）                │
├──────────────────────────────────────────────────────────────────────┤
│  z-index: 1   DOM 覆盖层（主题面板、Tooltip、Toast 等                    │
│              需要 DOM 交互且不需 3D 化的）                                │
├──────────────────────────────────────────────────────────────────────┤
│  body 底色（暗色宇宙底色，作为 3D canvas alpha 的兜底）                   │
└──────────────────────────────────────────────────────────────────────┘
```

> 两层纵深是无球的空间骨架：后景星光 Additive 发光 + DOF 虚化退让、前景 UI 逐件 tilt 主导。`<Html transform>` 让正文/表单保留 DOM 的清晰度与可访问性，同时「住进」3D 空间（这是 100% canvas 做不到的）。主题切换面板、全局 Toast 等更适合 DOM 覆盖层。**主体 UI（导航/卡片/按钮/输入框/标题）都在 canvas 内 3D 化且逐件 tilt，后景只有星光、无球**。

### 骨架（Next.js + R3F）

```tsx
// app/page.tsx
'use client'
import { Canvas } from '@react-three/fiber'
import { Scene } from '@/components/three/Scene'
import { ThemePanel } from '@/components/ThemePanel'

export default function Page() {
  return (
    <main className="relative h-dvh w-full overflow-hidden">
      <Canvas
        shadows                                {/* 阴影启用 */}
        dpr={[1, 2]}                           {/* 允许 retina 高 DPR */}
        gl={{ antialias: true, alpha: false, powerPreference: 'high-performance' }}
        camera={{ fov: 50, position: [0, 0, 8], near: 0.1, far: 100 }}
      >
        <Scene />
      </Canvas>

      {/* DOM 覆盖层：主题面板等（可选） */}
      <div className="pointer-events-none absolute inset-0 z-10">
        <div className="pointer-events-auto"><ThemePanel /></div>
      </div>
    </main>
  )
}
```

```tsx
// components/three/Scene.tsx —— 3D 场景根（两层 z 纵深 · 无球 · 暗色宇宙星光）
'use client'
import { Environment, ContactShadows, Sparkles } from '@react-three/drei'
import { EffectComposer, Bloom, DepthOfField, Vignette, ChromaticAberration, Noise } from '@react-three/postprocessing'
import { Navbar3D } from './Navbar3D'
import { Card3D } from './Card3D'
import { useThemeStore } from '@/store/theme'

export function Scene() {
  const accent = useThemeStore((s) => s.accent)
  const accentColor = themeColorMap[accent]
  return (
    <>
      {/* 暗色宇宙底（默认暗主题；亮主题时切低明度宇宙感底色） */}
      <color attach="background" args={['#05070F']} />

      {/* HDR 环境光（两层共享；玻璃反射/折射的环境内容） */}
      <Environment preset="city" />

      <Lights />

      {/* 【后景·焦散星光】多层 <Sparkles> 异速 Additive 发光点：前景玻璃的折射源 + 宇宙氛围 */}
      <Sparkles count={120} scale={[14, 8, 6]} size={1.5} speed={0.4} color="#FFFFFF" opacity={0.9} position={[0, 0, -2.5]} />
      <Sparkles count={80}  scale={[20, 12, 8]} size={3} speed={0.15} color="#E0F2FE" opacity={0.6} position={[0, 0, -5]} />
      <Sparkles count={40}  scale={[28, 18, 12]} size={5} speed={0.06} color="#BAE6FD" opacity={0.4} position={[0, 0, -9]} />
      {/* 少量主题强调色星光点缀（呼应品牌色，数量极少不抢戏） */}
      <Sparkles count={15} scale={[16, 10, 8]} size={2.5} speed={0.2} color={accentColor} opacity={0.5} position={[0, 0, -4]} />

      {/* 【前景·UI 物件】z=0.5~3.2，真玻璃拉满 + 逐件跟手 tilt，清晰主导 */}
      <Navbar3D position={[0, 3.2, 0]} />
      <Card3D position={[-2.5, -0.5, 0.5]} />
      <Card3D position={[2.5, -0.5, 0.5]} />

      {/* 接触阴影（仅接前景物件，星光不投影） */}
      <ContactShadows position={[0, -3, 0]} opacity={0.5} scale={20} blur={2.5} far={4} />

      {/* 后处理：全开 —— DOF 焦点钉前景、虚化远层星光；Bloom 只泛前景高光 + 星光微泛 */}
      <EffectComposer>
        <Bloom intensity={0.6} luminanceThreshold={0.6} luminanceSmoothing={0.9} mipmapBlur />
        <DepthOfField focusDistance={0.018} focalLength={0.06} bokehScale={4} />
        <ChromaticAberration offset={[0.0005, 0.0005]} />
        <Vignette eskil={false} offset={0.3} darkness={0.7} />
        <Noise opacity={0.03} />
      </EffectComposer>
    </>
  )
}
```

> **关键变化**：删除了一切玻璃球（旧版中心主球 `GlassMesh` 与球群 `AmbientSphereField` 都删除 —— 前者是突兀根源，后者仍会抢戏），换成**多层 `<Sparkles>` 焦散星光**（白/淡蓝 + 少量主题色点缀，Additive 发光，可被前景玻璃折射，DOF 虚化后退让成宇宙尘埃）+ 前景 UI（逐件 tilt 主导）+ 暗色宇宙底。立体感从背景雕塑转移到**玻璃本身的跟手 tilt + 相机视差**。这就是无球的最终落地。

---

## 七大维度 3D Motion 动效体系（完整实现）

3D 动效是空间生命感的来源，必须完整实现 —— 开销在 transform/uniform（合成层或 shader 常量），不增 draw call、不增 pass。

> **铁律**：所有 3D 动效作用于 mesh transform（position/rotation/scale，矩阵运算）或 shader uniform / 单光源单参数。**不动布局属性**。Motion 驱动 R3F 时，用 `useMotionValue` + `useSpring` 产生平滑数值，在 `useFrame` 里读入驱动 mesh。

### ① 加载：场景预热 / 玻璃聚合溶解 / 进度流光 / 数字滚动 / 按钮 loading

3D 资源（模型/纹理/envMap）加载时，必须有「正在构建」反馈。用 Drei `useProgress` 监听加载进度，配合 3D 入场与 DOM 层 spinner/骨架屏。

```tsx
'use client'
import { useProgress, Html } from '@react-three/drei'
import { motion } from 'motion/react'

function LoadingOverlay() {
  const { progress, active } = useProgress()
  return (
    <Html center>{active && (
      <div className="flex flex-col items-center gap-3">
        <motion.span className="h-7 w-7 rounded-full border-[2.5px] border-[var(--glass-border)] border-t-[var(--accent)]"
          animate={{ rotate: 360 }} transition={{ duration: 0.7, repeat: Infinity, ease: 'linear' }} />
        <span className="tabular-nums text-sm text-text-secondary">{Math.round(progress)}%</span>
      </div>
    )}</Html>
  )
}
```

选型表：3D 资源加载 → `useProgress` + 3D 入场 + DOM spinner/进度；列表/面板 → 骨架屏（多层）；轻量请求 → 三点跳动；上传下载 → 进度流光；按钮操作 → 按钮 loading（`<Html>` 内 DOM spinner）；数据 → 数字滚动（`useMotionValue` + `animate`，可驱动 3D 文字 `Text` 的内容）。

### ② 状态：相机待机微摆 / 点光脉冲 / 在线光晕 / 连接流光 / 心跳脉冲

实时状态感知，用 3D 光源/material uniform + DOM Motion 无限循环。

```tsx
// 强调色点光脉冲（intensity 呼吸）+ 环绕 —— 【只作用前景 UI 区域】（环绕半径 4，正好覆盖前景物件，照不到远层星光）
function OrbitAccentLight({ color }: { color: string }) {
  const ref = useRef<THREE.PointLight>(null!)
  useFrame((state) => {
    const t = state.clock.elapsedTime
    ref.current.intensity = 6 + Math.sin(t * 1.4) * 2.5   // 点光脉冲（在线感）
    ref.current.position.x = Math.sin(t * 0.6) * 4          // 环绕（半径 4，覆盖前景）
    ref.current.position.z = Math.cos(t * 0.6) * 4
    ref.current.position.y = 2 + Math.sin(t * 1.2) * 1.5    // 8 字起伏
  })
  return <pointLight ref={ref} color={color} distance={9} decay={2} castShadow />  {/* distance 限死，不外溢到远层星光 */}
}

// 在线光晕 / 心跳脉冲（DOM Motion，放 <Html> 或覆盖层）
const OnlineGlow = () => (
  <motion.span className="inline-block h-2.5 w-2.5 rounded-full bg-emerald-500"
    animate={{ opacity: [1, 0.4, 1], scale: [1, 0.85, 1] }}
    transition={{ duration: 1.8, repeat: Infinity, ease: 'easeInOut' }} />
)
```

状态选型：连接中 = pulse；在线 = breathe + glow；加载 = dots/spinner；错误 = shake + danger 色；离线 = 静态灰点。相机 idle 时做极慢正弦微摆（待机感）。

### ③ 入场：从空间深处飞入 + 相机拉近 / 旋转落定 / 错落浮现 / 翻转 / 缩放弹出

首屏戏剧时刻 —— 从空间深处立体飞入，而非整齐淡入。

```tsx
// 相机从 z=20 拉到 z=8 + 玻璃 scale 0+旋转→1 落定
function CameraEntry() {
  const { camera } = useThree()
  useFrame(() => {
    if (camera.position.z > 8.05) {
      camera.position.z += (8 - camera.position.z) * 0.04   // 相机拉近 lerp
      camera.lookAt(0, 0, 0)
    }
  })
  return null
}

// 玻璃群错落落定（Motion 控制 R3F group 的 scale via ref）
function StaggeredEntry({ children }: { children: React.ReactNode }) {
  const ref = useRef<THREE.Group>(null!)
  const scale = useMotionValue(0)
  const spring = useSpring(scale, { stiffness: 100, damping: 20 })
  useEffect(() => { const t = setTimeout(() => scale.set(1), 200); return () => clearTimeout(t) }, [scale])
  useFrame(() => { if (ref.current) ref.current.scale.setScalar(spring.get()) })
  return <group ref={ref}>{children}</group>
}
```

### ④ 交互（指针）：玻璃逐件 tilt / 相机视差跟随 / OrbitControls 拖拽 / 磁吸 / 涟漪 / 光泽跟随

最强空间感知来自**玻璃逐件跟手 tilt + 相机视差双层叠加**（立体感的命脉，见「前景玻璃逐件 tilt」章）。

```tsx
// 相机视差跟随指针（场景级立体感）：Motion useMotionValue → useFrame 读入驱动相机
function CameraParallax() {
  const { camera } = useThree()
  const mx = useMotionValue(0), my = useMotionValue(0)
  const sx = useSpring(mx, { stiffness: 60, damping: 20 })
  const sy = useSpring(my, { stiffness: 60, damping: 20 })
  useEffect(() => {
    const onMove = (e: PointerEvent) => {
      mx.set((e.clientX / window.innerWidth - 0.5) * 2.2)
      my.set(-(e.clientY / window.innerHeight - 0.5) * 1.6)
    }
    window.addEventListener('pointermove', onMove)
    return () => window.removeEventListener('pointermove', onMove)
  }, [mx, my])
  useFrame((state) => {
    const t = state.clock.elapsedTime
    const swayX = Math.sin(t * 0.3) * 0.4, swayY = Math.cos(t * 0.27) * 0.3  // 自动微摆
    camera.position.x += (sx.get() + swayX - camera.position.x) * 0.05
    camera.position.y += (sy.get() + swayY - camera.position.y) * 0.05
    camera.position.z = 8 + Math.sin(t * 0.2) * 0.3                          // 相机呼吸
    camera.lookAt(0, 0, 0)
  })
  return null
}

// 玻璃逐件 tilt（物件级立体感，跟手）：与相机视差不同 spring 刚度，避免机械同步
//   全局监听 pointermove，每件玻璃读各自 motion value → useFrame 驱动 group.rotation.x/y
//   幅度 0.05~0.15、stiffness 150~250、damping 15~25；离开回正（见 tilt 原理章）
```

```tsx
// 展区拖拽旋转 3D 物件（可选，用于 hero 雕塑区）
import { PresentationControls } from '@react-three/drei'
<PresentationControls global polar={[-0.2, 0.4]} azimuth={[-0.6, 0.6]} config={{ mass: 2, tension: 300 }}>
  <HeroSculpture />   {/* glb hero 或内建几何体雕塑（见 hero 回退章） */}
</PresentationControls>
```

DOM 交互（`<Html>` 内）：磁吸 + 3D 倾斜 + 涟漪 + 光泽跟随 —— 全部用 Motion 实现（`useMotionValue` + `useSpring`）。

### ⑤ 滚动：滚动显现 / 相机推进绕轨 / 视差 / 滚动进度条

用 Drei `<ScrollControls>` 让相机随滚动在 3D 空间推进/绕轨，整页空间穿梭。

```tsx
import { ScrollControls, Scroll, useScroll } from '@react-three/drei'

<ScrollControls pages={4} damping={0.3}>
  <CameraScroll />
  <Scroll html>{/* DOM 内容层（Reveal/Parallax） */}</Scroll>
</ScrollControls>

function CameraScroll() {
  const data = useScroll()
  const { camera } = useThree()
  useFrame(() => {
    const offset = data.offset   // 0~1
    camera.position.z = 8 - offset * 3                  // 滚动推进
    camera.position.x = Math.sin(offset * Math.PI) * 2  // 绕轨
    camera.lookAt(0, 0, 0)
  })
  return null
}
```

DOM 滚动进度条（顶部强调色细线）：`useScroll` + `useSpring` + `scaleX`。

### ⑥ 过渡：主题换肤材质 lerp / 相机运镜 / FLIP 重排 / 圆形揭露换肤 / 折叠展开

主题换肤时，3D 材质/光源/粒子色用 Zustand store + `useFrame` lerp 渐变（非跳变）。

```tsx
function GlassMesh() {
  const accent = useThemeStore((s) => s.accent)
  const matRef = useRef<any>(null!)
  const lightRef = useRef<THREE.PointLight>(null!)
  const target = useMemo(() => new THREE.Color(themeColorMap[accent]), [accent])
  useFrame(() => {
    matRef.current.attenuationColor.lerp(target, 0.05)           // 玻璃内部吸收色 lerp
    if (lightRef.current) lightRef.current.color.lerp(target, 0.05)  // 点光色 lerp
  })
  return (<>
    <mesh><MeshTransmissionMaterial ref={matRef} attenuationColor={themeColorMap[accent]} /></mesh>
    <pointLight ref={lightRef} />
  </>)
}
```

DOM 过渡：`AnimatePresence` + `layout`/`layoutId`、grid `0fr→1fr` 折叠、`document.startViewTransition` + `clip-path: circle()` 圆形揭露换肤。

### ⑦ 灵动点缀（合成层/shader 常量微动效，叠加十几个也不掉帧）

3D 层除了相机视差这种「大动效」，还有一批**几乎零开销的 3D 微动** —— 改 mesh transform 或 shader uniform（矩阵运算或常量赋值），不增 draw call、不增 pass，却让场景「灵」起来。**场景「活」的主力是这些**。

```tsx
import { Float, Sparkles } from '@react-three/drei'

// Float：自动悬浮 + 微旋转（零配置）—— 用于前景玻璃物件
<Float speed={1.5} rotationIntensity={0.4} floatIntensity={0.6}><GlassCard /></Float>

// 焦散星光（Sparkles，Additive，分层异速）—— 后景宇宙星光，自带闪烁即灵动
<Sparkles count={120} scale={[14,8,6]} size={1.5} speed={0.4} color="#FFFFFF" opacity={0.9} position={[0,0,-2.5]} />
<Sparkles count={80}  scale={[20,12,8]} size={3}   speed={0.15} color="#E0F2FE" opacity={0.6} position={[0,0,-5]} />
<Sparkles count={40}  scale={[28,18,12]} size={5} speed={0.06} color="#BAE6FD" opacity={0.4} position={[0,0,-9]} />

// 前景玻璃/hero 雕塑自转 + 边缘虹彩微 tilt（shader 常量，零额外 pass）
useFrame((state) => {
  const t = state.clock.elapsedTime
  glassRef.current.rotation.x = t * 0.15
  glassRef.current.rotation.y = t * 0.2
  glassRef.current.rotation.z = Math.sin(t * 0.4) * 0.12
})
```

DOM 灵动（`<Html>`/覆盖层）：边缘流光 `SheenSweep`、徽章弹现、图标弹跳、光泽划过、脉冲环、悬停浮起、抖动、光标闪烁、下划线生长、聚焦环呼吸、光晕脉冲。

编排心法：一屏选 1 个常驻强动效（如相机视差）当主角，其余用触发型点缀；同源时序（共享曲线与时长基调）。

---

## 🌟 灵动 3D 动效库（性价比微动，场景「活」的主力）

3D 的「灵」主要来自这批**几乎免费的 transform/uniform 微动**的持续叠加。它们不增 draw call、不增 pass，与全开后处理共存而不互相挤占 —— Bloom 提供泛光质感，微动提供生命力，两者叠加才是「又亮又活」。

| # | 动效 | 实现 | 维度 |
|---|------|------|------|
| 1 | 玻璃悬浮呼吸（Float） | `position.y = sin(t*0.8)*0.15` 或 Drei `<Float>` | 灵动 |
| 2 | 缓慢自转（Slow Spin） | `rotation.x/y = t*0.15/0.2`（**前景 UI 物件 / hero 雕塑用**） | 灵动 |
| 3 | 强调点光环绕（Orbit Light） | `light.position.x/z = sin/cos(t*0.6)*4` | 灵动 |
| 4 | 粒子分层异速（Layered Drift） | 多个 `<Sparkles>` 不同 `speed`/`scale` | 灵动 |
| 5 | 相机呼吸绕轨（Camera Breathe） | `camera.position.z = 8 + sin(t*0.2)*0.3` | 灵动 |
| 6 | 入场缩放归位（Pop-in） | `scale.lerp` + 相机拉近 | 加载/入场 |
| 7 | 点光脉冲（Light Pulse） | `light.intensity = 6 + sin(t*1.4)*2.5` | 状态 |
| 8 | 粒子脉动缩放（Particle Pulse） | `particles.scale = 1 + sin(t*0.5)*0.06` | 灵动 |
| 9 | 相机微摆（Camera Sway） | `sin(t*0.3)*0.4` 叠加鼠标视差 | 灵动 |
| 10 | 边缘虹彩微 tilt（Iridescent Tilt） | `rotation.z = sin(t*0.4)*0.12` | 灵动 |
| 11 | 景深呼吸（DOF Breathe） | 后处理 DOF `focusDistance` 微呼吸 / `fog.density` | 灵动 |
| 12 | 点光 8 字起伏（Light Bob） | `light.position.y = 2 + sin(t*1.2)*1.5` | 灵动 |
| 13 | 色散呼吸（Chroma Breathe） | `material.chromaticAberration` 微呼吸 | 灵动 |
| 14 | Bloom 阈值呼吸（Bloom Breathe） | Bloom `luminanceThreshold` 微调 | 灵动 |

### 灵动编排心法

| 原则 | 说明 |
|------|------|
| **玻璃 tilt + 相机是王** | 玻璃逐件跟手 tilt + 相机呼吸/视差/微摆的「场景级」动效，比单个物体的转动感知强 10 倍 —— 优先把灵动预算投给前景玻璃 tilt 与相机（后景星光自带 `<Sparkles>` 异速闪烁，几乎零配置就够活） |
| **全走 transform/uniform** | 上述动效改 transform 或 shader uniform / 单光源单参数，不增 draw call/pass —— 这是「性价比灵动」的本质 |
| **组合使用** | 前景玻璃悬浮 + tilt + 星光异速 + 点光环绕 + 点光脉冲 + 相机呼吸叠加，场景立刻「活」了 |
| **慢即是美** | 3D 灵动感来自**极慢**的持续微动（系数 0.03~0.6），不是快速旋转 |
| **分层 > 堆量** | 粒子分层异速、点光做 8 字，比堆粒子数量/堆光源更「活」，且不增 draw call |

---

## Motion × R3F 协同（核心机制）

动效主力是「Motion 驱动 R3F」—— 用 Motion 的 `useMotionValue` + `useSpring` 产生平滑数值，在 R3F 的 `useFrame` 里读入驱动 mesh transform。结合了 Motion 的弹性调度与 R3F 的逐帧控制：

```tsx
'use client'
import { useMotionValue, useSpring } from 'motion/react'
import { useFrame } from '@react-three/fiber'

function MotionDrivenGlass() {
  const meshRef = useRef<THREE.Mesh>(null!)
  const mx = useMotionValue(0), my = useMotionValue(0)
  const sx = useSpring(mx, { stiffness: 200, damping: 20 })
  const sy = useSpring(my, { stiffness: 200, damping: 20 })
  useFrame(() => {
    meshRef.current.rotation.y = sx.get() * 0.3
    meshRef.current.rotation.x = sy.get() * 0.3
  })
  return (
    <mesh ref={meshRef}
      onPointerMove={(e) => { mx.set(e.point.x); my.set(e.point.y) }}
      onPointerLeave={() => { mx.set(0); my.set(0) }}
    >{/* 玻璃 */}</mesh>
  )
}
```

| 层 | Motion 负责 | R3F 负责 |
|----|------------|---------|
| DOM（`<Html>`/覆盖层） | 全部动效（入场/状态/指针/过渡/灵动） | 不涉及 |
| 3D 物件 | `useMotionValue`+`useSpring` 产生平滑值 | `useFrame` 读入驱动 transform |
| 相机 | 视差的平滑值 | `useFrame` 驱动 `camera.position` |

> Motion 的 `useMotionValue`/`useSpring` 天然 rAF 节流，平滑且不掉帧，是「DOM 与 3D 共享数值源」的桥梁。

---

## 缓动曲线库

DOM 层用 CSS 缓动曲线（`--ease-liquid`/`--ease-spring`/`--ease-out-expo` 等）。3D 侧用 `lerp`（线性插值 + 阻尼系数）或 Motion 的 `useSpring` 实现惯性：

| 阻尼系数 / 弹簧 | 效果 | 适用 |
|----------------|------|------|
| `lerp factor 0.02~0.04` | 慢而丝滑的大位移 | 相机推进、入场 |
| `lerp factor 0.05~0.08` | 中等惯性 | 相机视差、玻璃归位 |
| `lerp factor 0.1~0.15` | 快速吸附 | 光源跟随、即时反馈 |
| `useSpring { stiffness:200, damping:20 }` | 弹性 | 指针跟随、磁吸归位 |

---

## 性能：视觉优先下的工程优化（主流机流畅 · 不删视觉）

视觉优先不等于粗暴堆砌到卡死。它是**对效果的取舍倒转**：在「效果」与「性能」冲突时，永远先保效果，再用工程手段补回性能 —— 参数封顶、实例化、累积阴影、合并 pass、轻量降级兜底，而非删减效果。目标：**主流机（含主流集显 / 中端独显）流畅 60fps**。

> **无球后 GPU 压力已大减**：移除一切玻璃球（旧版 transmission 主体球 + 球群是 fill rate / 透射采样的重灾区）后，真玻璃只剩前景 UI 物件，加上后景星光是 `<Sparkles>` 单 draw call Additive 点 —— 整体渲染压力远低于带球方案。这给了「视觉全开」更大的流畅余量。

| 优化手段 | 作用 |
|---------|------|
| **后处理合并 pass** | `@react-three/postprocessing` 单一 `EffectComposer`，比裸叠加高效 |
| **Bloom 用 `mipmapBlur` + `luminanceThreshold`** | 只泛高亮部分，不全屏糊，更高效 |
| **`MeshTransmissionMaterial` 调 `samples`/`resolution`** | 控制透射采样开销（视觉紧张时调低、宽松时调高）。前景 UI 物件数量多时，每件 `samples` 别拉满（4~8 足够） |
| **星光用 `<Sparkles>`** | 单 draw call / 层，Additive，无 transmission 采样开销 —— 后景几乎免费 |
| **多前景 UI 物件共享材质实例** | 多个同款玻璃按钮/卡片共享同一 `MeshTransmissionMaterial` 实例，省材质初始化 |
| **`AccumulativeShadows` 单帧累积** | 高质软阴影不每帧重算 |
| **`dpr={[1, 2]}` 上限 2** | 允许 retina 但不无限 |
| **`shadow-mapSize` 封顶 2048** | 阴影分辨率封顶，勿无限大吃显存 |
| **shader 常量动画** | `chromaticAberration`/`fog.density` 等微动改 uniform 不增 pass |
| **`<Suspense>` + 加载降级 + glb 回退** | 资源加载时占位；glb 加载失败回退内建几何体，绝不白屏 |
| **LOD（`<Detailed>`）** | 远处物件降面数 |

> **设计哲学**：关掉的永远是「真正无意义的浪费」（如重复的全屏 pass、未优化的 shadow map 重算、无限大的 DPR/阴影分辨率），**保留的是所有视觉质感与动效**（Bloom/阴影/多真玻璃/色散/景深全在）。宁可调 Bloom 参数 / 封顶 DPR，也不关 Bloom —— 前者是优化，后者是删视觉。

### 主流机流畅的参数封顶（固定值，无需运行时探测）

为让主流机也能流畅跑「视觉全开」，给昂贵参数设固定上限（不读运行时分档、不探测 GPU）：

| 参数 | 固定上限 | 理由 |
|------|----------|------|
| `dpr` | `{[1, 2]}`（上限 2） | 允许 retina 但封顶 2，避免 3x/4x 直跑爆 fill rate |
| `shadow-mapSize` | `2048×2048` | 高质阴影封顶，再高吃显存、收益递减 |
| `MeshTransmissionMaterial.samples` | 前景每件 `4~8` | 透射采样是 fill rate 大头，多件 UI 时别拉满 |
| `MeshTransmissionMaterial.resolution` | `512` | 透射缓冲封顶 |
| 星光 `<Sparkles>` 总量 | 三层合计 ≤ ~250 | Additive 单 draw call 本很轻，但总量也封顶防极端 |
| 后处理 | 单一 `EffectComposer` 合并 | 不裸叠加多个 composer |

### 轻量降级兜底（非运行时探测，做防御性编程）

视觉优先不排除「最坏情况不崩」。两种兜底，都是**防御性写法**而非运行时性能探测：

- **glb 加载失败 → 回退内建几何体**（见 hero 回退章）：`<Suspense>` + try/catch + 超时回退，任何情况下 hero 区都有可见雕塑，不白屏。
- **`visibilitychange` 长时 idle 暂停重型后处理**（可选）：用户切到别的标签页时，可暂停 Bloom/DOF 等重型 pass省电；回来时恢复。这是「省电」而非「省动画」。

### 常驻渲染 vs 按需渲染

沉浸式 3D 场景**默认常驻渲染**（`useFrame` 持续驱动相机呼吸、玻璃 tilt、星光闪烁、点光环绕 —— 这些「活」需要逐帧）。长时间 idle（如用户切到其他标签页）时，可用按需渲染省电 —— 监听 `visibilitychange`，隐藏时停渲染，回来时恢复。这是「省电」而非「省动画」，动效进行时渲染完整跑。

```tsx
// 可选：长时间 idle 省电（非默认，按需启用）
useEffect(() => {
  const handler = () => { if (document.hidden) /* 可暂停重型后处理 */ }
  document.addEventListener('visibilitychange', handler)
  return () => document.removeEventListener('visibilitychange', handler)
}, [])
```

---

## 字体系统

字体栈：Geist/Inter（西文）+ 中文系统字体，数据用 JetBrains Mono。

- **标题（3D 文字）**：Drei `<Text>`/`<Text3D>`（底层 troika）加载同一字体，在 3D 空间渲染。troika 支持 SDF 字体，可缩放不失真，可加 bevel/材质。`tabular-nums` 数据标题用 `fontVariant="tabular-nums"`。
- **正文/表单（Html 嵌入）**：Drei `<Html>`，内部是 DOM，自动继承字体栈与 `globals.css`，无需重做排版。

---

## 可访问性（全 UI 3D 真融合下的特殊考量）

3D 化 UI 的可访问性比平面 DOM 更需刻意处理：

- **`<Html>` 保留 DOM 语义**：正文/表单/导航菜单项用 `<Html>`（即使 `transform`），内部仍是语义化 DOM（`nav`/`button`/`input`/`label`），屏幕阅读器、键盘 Tab、聚焦全可用。
- **3D 交互物件兜底**：纯 3D 按钮（无 `<Html>` 内 DOM）对键盘/屏幕阅读器不可达。**重要交互（提交、导航）应在 `<Html>` 内用真实 `<button>`**，纯 3D 按钮仅用于视觉装饰或次要操作。
- **键盘焦点可见**：`<Html>` 内 `:focus-visible` ring 全覆盖（`outline: 2px solid var(--accent); outline-offset: 2px`）。
- **`prefers-reduced-motion`**：用 Motion 的 `useReducedMotion()`。开启时把相机视差/磁吸幅度归零、循环降为单次、保留功能性反馈（状态指示、加载）。视觉优先下降低幅度而非删除质感 —— 玻璃/Bloom/阴影仍在，只是不动。
- **对比度**：3D 文字 + Bloom 下注意过曝，正文用 `<Html>` 保证对比度达标。

```tsx
import { useReducedMotion } from 'motion/react'
const reduce = useReducedMotion()
// reduce 为真：相机视差幅度归零、Float 的 floatIntensity 减半、循环降单次
```

---

## 响应式（3D 视场自适应）

- **桌面 ≥1280**：完整 3D 场景，相机 `fov: 50`，侧栏常驻。
- **平板 768–1279**：`fov` 调大（55）容纳更宽视场，相机后退；并排 3D 卡片变堆叠。
- **移动 <768**：相机后退（`position.z` 加大）或缩小场景；3D 卡片单列；`<Html>` 内容单列可滚动。用 Tailwind 断点控制 `<Html>` 内 DOM 布局。
- 用 `min-h-dvh`，`Canvas` 用 `h-dvh w-full`；resize 时 R3F 自动更新 `camera.aspect`。

```tsx
function ResponsiveCamera() {
  const { camera, size } = useThree()
  useEffect(() => {
    (camera as THREE.PerspectiveCamera).fov = size.width < 768 ? 60 : size.width < 1280 ? 55 : 50
    ;(camera as THREE.PerspectiveCamera).updateProjectionMatrix()
  }, [size.width, camera])
  return null
}
```

---

## 执行流程

### 第 1 步：定义主题系统 + Zustand 桥接
在 `globals.css` 定义双维度 CSS 变量（明暗基础层 + 7 色相层 + 派生色），根节点**默认 `<html data-theme="dark" data-accent="mint">`**（暗色宇宙底，星光最出彩）。新建 Zustand `useThemeStore`（`mode` 初值 `'dark'`），`setMode`/`setAccent` 同时写 DOM 属性 + store + localStorage。

### 第 2 步：装依赖 + 铺 Canvas 骨架
`npm install three @react-three/fiber @react-three/drei @react-three/postprocessing postprocessing zustand motion`。写 `<Canvas shadows dpr={[1,2]} gl={{ antialias:true }}>` + `<Scene/>`。

### 第 3 步：搭 3D 场景（两层 z 纵深 · 无球：后景星光 + 前景 UI）
`<color attach="background" args={['#05070F']}>`（暗色宇宙底）+ `<Environment preset="city">` + `<Lights>`（主光+阴影[只打前景] + 强调点光[只脉冲前景] + 聚光跟随）+ **后景多层 `<Sparkles>` 焦散星光**（近/中/远三层异速 + 少量主题强调色点缀，前景玻璃的折射源 + 宇宙氛围）+ 前景 UI 物件。**不放任何玻璃球**。务必有 envMap 与前景玻璃可折射的星光内容。

### 第 4 步：全 UI 3D 真融合 + 逐件跟手 tilt
把导航/卡片/按钮/输入框封装为 R3F 组件（`<RoundedBox>` + `MeshTransmissionMaterial` + `<Float>`）。标题用 `<Text>`/`<Text3D>`，正文/表单用 `<Html transform>`。**每件玻璃挂跟手 tilt**（group.rotation 随鼠标，幅度 0.05~0.15，spring stiffness 150~250）+ 相机视差双层叠加。

### 第 5 步：（可选）glb hero 雕塑 + 回退
hero 区若用 glb，务必 `<Suspense>` + 超时 + `<ErrorBoundary>` 回退内建几何体雕塑，绝不白屏。默认产物可用 `FallbackSculpture`，不依赖外部 glb 文件。

### 第 6 步：开后处理（全开 · DOF 焦点钉前景、虚化远层星光）
`<EffectComposer>` 内叠 SMAA + Bloom（threshold 调高 0.6，只泛前景高光 + 星光微泛）+ DOF（焦点钉前景 z≈1，`bokehScale` 加大让远层星光虚化的成尘埃）+ 色差 + 暗角 + 噪点。

### 第 7 步：加七大维度 3D Motion 动效（完整实现）
① 加载（`useProgress` + 3D 入场 + DOM spinner）② 状态（点光脉冲/光晕/心跳）③ 入场（相机拉近 + 玻璃落定 + 错落）④ 交互（**玻璃逐件 tilt** + 相机视差 + OrbitControls + Motion×R3F）⑤ 滚动（`ScrollControls` 相机推进）⑥ 过渡（材质 lerp 换肤 + DOM `AnimatePresence`）⑦ 灵动（Float + Sparkles 分层 + 自转 + 点光环绕 + 相机呼吸 + 色散呼吸 + Bloom 呼吸，约 14 种）。

### 第 8 步：性能封顶 + 主题桥接 + 可访问性 + 自检
参数封顶（DPR≤2、shadow-mapSize≤2048、samples 4~8、星光总量 ≤~250）。Zustand store 驱动材质/光源 lerp 换肤。`<Html>` 保留 DOM 语义 + `useReducedMotion` + 键盘焦点。跑自检清单。

---

## 自检清单

**玻璃彻底 3D + 无球（首要，逐项必须通过）**：
- [ ] 后景**没有任何玻璃球**（无中心主球、无氛围球群、无 AmbientSphereField / GlassMesh 主体）？
- [ ] 后景是**暗色宇宙底 + 多层 `<Sparkles>` 焦散星光**（近/中/远三层异速 + 少量主题强调色点缀）？
- [ ] 星光**不接点光脉冲、不 castShadow、不被 Bloom 单独强化**，只自然泛一点微光？
- [ ] 星光落在 DOF 虚化区（远层 z=-8~-12 重度虚化），只发光不抢前景？
- [ ] 前景 UI 物件（导航/卡片/按钮/输入框）是真 3D 玻璃 mesh + **逐件跟手 tilt**（rotation.x/y 随鼠标，幅度 0.05~0.15）？
- [ ] tilt 是物件级 transform（走 group.rotation）+ 相机视差叠加，双层不同 spring 刚度？
- [ ] 前景玻璃后方有**可折射内容**（星光），不是空场景发黑？
- [ ] 默认 `data-theme="dark"`（暗色宇宙底，星光最出彩），Zustand `mode` 初值 `'dark'`？
- [ ] 没有「单个清晰大球贴在 UI 背后抢戏」，也没有「背景球群」？

**视觉优先 + 主流机流畅（最高优先级，必须全部通过）**：
- [ ] Bloom/景深/色差/暗角/噪点后处理全部默认开启（`<EffectComposer>` 全开）？
- [ ] 阴影启用（`<Canvas shadows>` + 主光 `castShadow` 或 `<ContactShadows>`/`<AccumulativeShadows>`）？
- [ ] 多真玻璃不限量（每个 UI 控件都可是 `MeshTransmissionMaterial`）？
- [ ] 玻璃用 `MeshTransmissionMaterial` 且 `chromaticAberration` 色散开启？
- [ ] `dpr={[1,2]}`、`antialias:true`（或 SMAA）？
- [ ] 七大维度动效一个不删？流畅靠工程优化（参数封顶 + 合并 pass + shader 常量 + 轻量降级）而非删视觉？
- [ ] **参数封顶到位**：DPR≤2、`shadow-mapSize`≤2048、前景每件 `samples` 4~8、星光总量 ≤~250？

**架构与材质**：
- [ ] 两层 z 纵深（无球）：后景星光（可折射内容 + 宇宙氛围）+ 前景 UI 物件（真玻璃清晰主导 + tilt）？
- [ ] 暗色宇宙底（`<color attach="background">`）？
- [ ] 技术栈 = TS+React+Next.js+Tailwind+shadcn+Radix+Motion+R3F+Drei+postprocessing+Zustand？
- [ ] `<Environment>` 生成 envMap，玻璃反射/折射可见而非发黑？前景玻璃后方有可折射内容（星光）？
- [ ] 光照体系齐全：环境光 + 主光+阴影（只打前景）+ 强调色点光（脉冲/环绕，**只作用前景**，`distance` 限死不外溢）+ 鼠标跟随聚光？
- [ ] 全 UI 3D 真融合：导航/卡片/按钮/输入框/面板/Modal 都是 3D 玻璃 mesh（`<RoundedBox>` + `MeshTransmissionMaterial` + `<Float>`）？
- [ ] 标题用 troika 3D 文字（`<Text>`/`<Text3D>`），正文/数字/图表用 `<Html transform>` 嵌入（清晰/可选/可聚焦，且住进 3D 空间）？
- [ ] DOM 玻璃（`.glass`）与 mesh 玻璃共享 token + 色散配方，视觉一体，无平面贴背景割裂感？
- [ ] 所有 3D 颜色（材质 attenuation/光源/星光/env 背景/雾/Bloom 阈值）读 CSS 变量或 Zustand store，无写死？

**动效（七大维度完整）**：
- [ ] **① 加载**：`useProgress` + 3D 入场 + DOM spinner/骨架屏/进度流光？
- [ ] **② 状态**：点光 intensity 脉冲 + 在线光晕 + 心跳 + 相机 idle 微摆？
- [ ] **③ 入场**：相机拉近 + 玻璃 scale 归位 + 旋转落定 + 错落？
- [ ] **④ 交互**：**玻璃逐件跟手 tilt**（rotation 随鼠标，幅度 0.05~0.15）+ 相机视差跟随指针（Motion×R3F）+ `PresentationControls`/`OrbitControls` 拖拽？
- [ ] **⑤ 滚动**：`ScrollControls` 相机推进绕轨 + DOM 滚动进度条？
- [ ] **⑥ 过渡**：主题换肤材质/光源 lerp（Zustand + `useFrame`）+ DOM `AnimatePresence`/圆形揭露？
- [ ] **⑦ 灵动**：Float + Sparkles 分层异速 + 自转 + 点光环绕 + 相机呼吸 + 微摆 + 边缘虹彩 + 色散呼吸 + Bloom 呼吸（约 14 种）？
- [ ] 动效作用于 transform/uniform（合成层/shader 常量），不动布局属性？

**工程与可访问性**：
- [ ] 后处理用单一 `EffectComposer`（合并 pass），非裸叠加？
- [ ] glb（若有）有 `<Suspense>` + 超时 + `<ErrorBoundary>` 回退内建几何体，**任何情况不白屏**？
- [ ] Zustand store 桥接 DOM ↔ R3F，换肤 lerp 同步？
- [ ] `<Html>` 内保留 DOM 语义，重要交互用真实 `<button>`，键盘焦点可见，`useReducedMotion` 兜底？
- [ ] resize 时更新 `camera.aspect`/`fov`，三档响应式合理，移动端 `<Html>` 可滚动？

---

## 设计准则

- **玻璃彻底 3D + 跟手 tilt（首要）**：UI 控件本身就是真 3D 玻璃物件，每件随鼠标 tilt 跟手；立体感来自玻璃本身 + 相机视差，而非背景雕塑。**永远不要清晰大球贴在 UI 背后，也不要背景球群** —— 后景交给暗色宇宙星光。
- **后景彻底无球 · 暗色宇宙星光**：后景是 `<Sparkles>` 焦散星光（多层异速、Additive、可折射、DOF 虚化退让），没有任何玻璃球。星光只发光点缀，绝不抢前景。
- **全 UI 3D 真融合，信息分层**：UI 控件本身是真 3D 玻璃物件；标题用 3D 文字，正文/数字/图表用 `<Html transform>` 嵌入（住进 3D 空间、视觉一体）。3D 服务立体感与氛围，不遮挡内容阅读。
- **视觉优先，主流机流畅**：Bloom/阴影/多真玻璃/色散/景深/后处理默认全开，视觉与动效只增不减。流畅靠工程优化（参数封顶 + 合并 pass + shader 常量 + 轻量降级 + 实例化 + 累积阴影），不删视觉。
- **真折射 + 色散 > 贴图**：玻璃用 `MeshTransmissionMaterial` 真折射 + 色散，前景玻璃后方必须放可折射内容（星光）；空场景里的真玻璃是浪费。
- **玻璃 tilt + 相机是最大的动效**：玻璃逐件 tilt（物件级）+ 相机视差/推进/呼吸/微摆（场景级）双层叠加，带来最强立体感。优先把「强动效」预算花在玻璃 tilt 与相机上。
- **颜色全部走变量/store**：3D 与 DOM 共享 CSS 变量 + Zustand store，换肤时两层 lerp 同步 —— 这是「立体且主题统一」的根本。
- **Motion × R3F 协同**：Motion 的 `useMotionValue`+`useSpring` 产生平滑值，R3F 的 `useFrame` 读入驱动 mesh —— DOM 与 3D 共享数值源（tilt / 相机视差都用它）。
- **一强多弱**：每屏一个强 3D 动效（玻璃 tilt + 相机穿梭 / 立体入场），其余用克制星光与微光衬托。
- **强调色点睛**：强调点光与玻璃 `attenuationColor` 用主题强调色，3D 玻璃透出品牌色族，少量主题色星光点缀，而非彩虹乱炖。
- **绝不白屏**：glb 有回退、资源有 `<Suspense>`，密封测试环境零外部资源也能独立产出完整可运行工程。

---

## 常见误区

| 误区 | 正确做法 |
|------|---------|
| **中心一个大玻璃球贴在 UI 背后抢戏/突兀** | **彻底移除所有玻璃球**。后景改为暗色宇宙底 + 多层 `<Sparkles>` 焦散星光（Additive 发光、可折射、DOF 虚化退让）；立体感交给玻璃本身 tilt + 相机视差 |
| **背景放氛围球群（哪怕虚化低饱和）以为不抢戏** | 球群仍是物件群，数量一多 + Bloom 就变回主角。**后景只有星光，没有任何球** —— 星光是点不是物件，没有抢眼轮廓 |
| **正文用平面 DOM 直接盖在 canvas 上，与 3D 割裂** | 正文用 `<Html transform>` 嵌入 3D 空间（随透视/缩放/遮挡），DOM 玻璃与 mesh 玻璃共享 token + 色散配方视觉一体 |
| **玻璃留在平面，没有跟手 tilt（伪 3D）** | 每件玻璃挂跟手 tilt：`useMotionValue`(鼠标偏移)+`useSpring` → `useFrame` 驱动 `group.rotation.x/y`（幅度 0.05~0.15），与相机视差双层叠加 |
| **为性能关 Bloom/阴影/多真玻璃** | 视觉优先：该开则开，全部默认开；流畅靠参数封顶 + 合并 pass/shader 常量/调参/累积阴影/实例化/轻量降级，不删视觉。无球后 GPU 压力已大减 |
| **UI 控件留在平面 DOM** | 全 UI 3D 真融合：导航/卡片/按钮/输入框都是 3D 玻璃 mesh；标题用 3D 文字，正文/图表用 `<Html transform>` 嵌入 |
| **用裸 Three.js 命令式写场景** | 用 R3F + Drei 声明式 JSX，组件化 3D 物件，与 React/Zustand 集成 |
| **所有文字都塞进 canvas**（100% canvas，清晰度/可访问性差） | 标题用 troika 3D 文字，正文/数字/图表用 `<Html transform>` 嵌入（清晰/可选/可聚焦） |
| **玻璃材质无色散**（用裸 `MeshPhysicalMaterial`） | 用 Drei `MeshTransmissionMaterial`（chromaticAberration 色散 + anisotropic），电影级折射 |
| **3D 场景死气沉沉，只靠 Bloom 撑场面** | 用七大维度完整动效 + 14 种 transform/uniform 灵动微动：Float/自转/点光环绕/脉冲/星光异速/相机呼吸/色散呼吸 —— 与 Bloom 叠加才「又亮又活」 |
| **裸 pointermove 直写 mesh 样式（掉帧）** | Motion `useMotionValue`+`useSpring`（rAF 节流）→ `useFrame` 读入驱动 mesh（tilt / 相机视差都用它） |
| **后处理裸叠加多个 EffectComposer** | 单一 `EffectComposer` 合并所有 pass（SMAA+Bloom+DOF+色差+暗角+噪点） |
| **每个光源都脉冲（贵）** | 只对 1 个强调色点光脉冲，其余静态 |
| **3D 颜色写死**（`new Color('#00D4AA')` 不变） | 读 CSS 变量或 Zustand store，换肤 lerp 同步 |
| **纯 3D 按钮键盘/屏幕阅读器不可达** | 重要交互在 `<Html>` 内用真实 `<button>`；纯 3D 按钮仅装饰/次要 |
| **shadow map 每帧重算（贵且抖）** | 用 `<AccumulativeShadows>`（单帧累积）或 `<ContactShadows>`（静态），且 `shadow-mapSize` 封顶 2048 |
| **持续渲染纯静态场景耗电** | 长时间 idle 时按需渲染（`visibilitychange`）省电 —— 但动效进行时正常渲染 |
| **换肤时 3D 跳变** | Zustand store + `useFrame` lerp 材质/光源色 |
| **没有 envMap，玻璃发黑** | 必须 `<Environment>`，且前景玻璃后方有可折射内容（星光） |
| **glb 下不到 / 加载超时 → 白屏** | glb 必须 `<Suspense>` + 超时定时器 + `<ErrorBoundary>` 回退内建几何体雕塑，任何情况不白屏 |
| **默认亮色底，星光不出彩** | 默认 `data-theme="dark"` 暗色宇宙底，星光泛光、玻璃折射发光最出彩 |
