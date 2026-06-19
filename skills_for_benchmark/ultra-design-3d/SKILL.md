---
name: ultra-design-3d
description: 当需要构建全面 3D 化、立体动效拉满的前端界面（3D 落地页、空间化 Dashboard、3D 产品展示、沉浸式可视化、立体海报等）时使用。基于 ultra-design 的 Liquid Glass 设计语言与双维度主题系统，叠加 Three.js 真 3D 层。**承诺一套完整的 3D 动效体系**：加载动效（场景预热/玻璃聚合溶解入场/主体缩放归位）、状态动效（相机待机微摆/点光脉冲/在线光晕）、入场动效（从空间深处飞入+相机拉近/旋转落定）、交互动效（相机视差跟随指针/OrbitControls 拖拽）、过渡动效（主题换肤材质 lerp 渐变/滚动相机推进）、灵感动效（悬浮呼吸/缓慢自转/点光环绕/粒子分层异速/粒子脉动/相机呼吸/边缘虹彩/景深呼吸/点光8字等约 12 种）。全部 3D 动效只改 mesh.transform（position/rotation/scale，矩阵运算）或单光源单参数，不增 draw call、不增 render pass，因此完整实现也不掉帧。该砍则砍只针对昂贵 GPU 黑洞（Bloom/阴影全关、真玻璃≤1、粒子≤600），绝不动任何功能性 3D 动效一刀切。靠固定集显可承受参数 + 按需渲染实现流畅，无需任何运行时检测或降级方案。采用「3D WebGL 氛围层 + DOM 玻璃信息层」混合架构，主题变量桥接两层同步换肤。生成生产级、可直接运行的单文件 HTML（importmap + ESM Three.js），告别平面与伪 3D。
---

# Ultra Design 3D — Liquid Glass 立体场景系统

## 概述

Ultra Design 3D 是 `ultra-design` 的三维进阶版。它**继承** ultra-design 的核心资产 —— Liquid Glass 设计原则、双维度（明暗 × 主题色）正交主题系统、苹果字体栈、DOM 玻璃信息层（含其完整的七大维度动效体系）—— 在其之上**新增一层真正的 Three.js 3D 渲染**：真 3D 玻璃折射材质、空间相机、3D 粒子与光场、立体动效。

核心理念升级：**界面不再是悬浮在氛围色斑上的平面玻璃，而是存在于真实三维空间中的玻璃物体群 —— 有景深、有遮挡、有视差、有立体动量。3D 场景的动感来自一套「完整的 3D 动效体系」，覆盖加载/状态/入场/交互/过渡/灵动六大维度，一个不少**。

### 与 ultra-design 的关系（必须先读）

| 维度 | ultra-design | ultra-design-3d |
|------|--------------|-----------------|
| 渲染主体 | 纯 DOM + CSS（`backdrop-filter` 伪玻璃、`perspective` 伪 3D tilt） | **Three.js WebGL 真 3D 场景** + DOM 玻璃信息层（混合） |
| 玻璃材质 | 半透明 tint + backdrop-filter 模糊（屏幕空间） | `MeshPhysicalMaterial` 真**透射折射**（transmission/ior/thickness）+ 环境反射 |
| 动效来源 | CSS keyframes + 滚动/指针 transform（2.5D） | 3D 相机视差 + 3D 粒子 + 3D 光场 + 空间入场（真 3D） |
| 主题系统 | CSS 变量驱动 DOM | **同一套 CSS 变量**，额外桥接到 3D 材质/光源（两层同步换肤） |
| 信息层（文字/按钮/表单） | DOM | **DOM 不变**（沿用 ultra-design 组件库） |

> **结论**：DOM 玻璃信息层（导航栏、卡片、按钮、输入框、主题切换面板）**照搬 `ultra-design`**，本 skill 不重复其组件代码。本 skill 聚焦 **3D 层的全部内容**：场景搭建、真玻璃材质、3D 动效、主题桥接、混合架构的分层与性能。

### 设计四原则（在 ultra-design 基础上）

1. **材质真实**（继承）：3D 玻璃必须真折射环境光，而非贴一张透明贴图。
2. **光照生命**（继承 + 升级）：3D 场景必须有真实光源体系 —— 环境光打底、主光塑形、强调色点光点睛（并做脉冲/环绕）、鼠标跟随聚光。
3. **空间纵深**（新增）：利用相机视差/呼吸/微摆、景深、3D 分层布局，制造"可以走进去"的空间感。
4. **灵动为魂，该砍则砍**（新增，核心）：场景"活"优先用近乎免费的 transform 微动堆砌；昂贵的 GPU 黑洞（Bloom/阴影/多真玻璃）一律砍掉。

---

## 核心命题：完整 3D 动效体系 + 该砍则砍（最高优先级，凌驾一切 3D 效果）

**WebGL 比纯 CSS 对 GPU 敏感得多。** 但 3D 场景的"活"，来自一套**完整的 3D 动效体系**——不止是几个 transform 微动，而是覆盖加载、状态、入场、交互、过渡、灵动六大维度的立体动效，一个不少。

> **铁律**：六大维度 3D 动效（加载/状态/入场/交互/过渡/灵动）一律完整实现，一个不删；流畅靠「让所有 3D 动效只改 mesh.transform（矩阵运算）或单光源单参数」+「昂贵 GPU 黑洞该砍则砍」来保证。该砍则砍只砍昂贵后处理（Bloom/阴影/多真玻璃），**绝不删减任何功能性 3D 动效**。

### 六大维度 3D 动效体系（都必须完整实现）

| 维度 | 代表 3D 动效 | 必要性 | 实现要点 |
|------|-------------|--------|---------|
| **① 加载** | 场景预热（先渲静态一帧）/玻璃聚合溶解入场/主体 scale 从 0 归位/相机从远拉近 | ✅ 必需（3D 资源加载时的"正在构建"反馈） | 加载期间相机 z 远→近 lerp、玻璃 scale 0→1 lerp；DOM 层叠加 ultra-design 的 spinner/骨架屏 |
| **② 状态** | 相机待机微摆/点光脉冲（亮度呼吸）/在线光晕/连接流光 | ✅ 必需（实时状态：加载/就绪/连接中） | idle 时相机做极慢正弦微摆 + 点光 intensity 脉冲；状态切换时整体色调 lerp |
| **③ 入场** | 从空间深处飞入（相机 z=20→8 + 玻璃 scale 0+旋转→1 落定）/粒子从中心扩散 | ✅ 必需（首屏戏剧时刻） | 纯 lerp，一次性；配合 DOM 层 riseIn |
| **④ 交互** | 相机视差跟随指针（最强空间感知）/OrbitControls 拖拽旋转（展区）/视差滚动 | ✅ 必需（界面"感知"到指针） | pointermove 驱动相机 lerp，rAF 合并；视差滚动相机推进 |
| **⑤ 过渡** | 主题换肤时材质/光源/粒子色 lerp 渐变（非跳变）/滚动相机推进绕轨 | ✅ 必需（变化"流动"而非跳变） | applyThemeToScene 用 lerp 过渡色值；滚动驱动 camera.position.z |
| **⑥ 灵动点缀** | 悬浮呼吸/缓慢自转/点光环绕/粒子分层异速/粒子脉动/相机呼吸/边缘虹彩/景深呼吸/点光8字（约 12 种） | ✅ 必需（让场景"灵"，从持续微动产生生命感） | 全部改 mesh.transform 或单光源单参数，矩阵运算，不增 draw call/pass |

> **关键认知**：3D 中改 `mesh.position/rotation/scale` 是矩阵运算，**不增加 draw call、不增加 render pass**——可以无限持续（自转、悬浮、点光环绕）而几乎不掉帧。**所以"完整 3D 动效"的代价可以趋近于零，前提是只动 transform（或单光源单参数），不碰 Bloom/阴影/多真玻璃这些真正昂贵的后处理。** 把这些 GPU 黑洞砍掉，六大维度 3D 动效全保留——这就是 3D"又活又快"的全部秘密。

### 为什么是「完整 3D 动效 + 该砍则砍」而非「删动效」？

"删 3D 动效换流畅"是错误解法——它把场景变成死的。"完整 3D 动效 + 该砍则砍"才是正确解法：

| 来源 | 错误解法（删动效） | ✅ 正确解法（完整动效 + 该砍则砍） |
|------|---------------|----------------------|
| **加载** | 不入场，直接出现 | ✅ 玻璃从 scale 0 + 相机从远拉近，立体飞入 |
| **状态** | 相机不动、光不脉冲 | ✅ idle 相机微摆 + 点光亮度脉冲呼吸 |
| **入场** | 无戏剧时刻 | ✅ 从空间深处飞入 + 旋转落定 |
| **交互** | 相机不动只转物体 | ✅ 相机视差跟随指针（最强空间感） |
| **Bloom/后处理** | 删掉所有亮感 | ⚪ **该砍的是 Bloom 全屏模糊 pass**，靠 transmission+Additive粒子+点光补"亮" |
| **阴影** | 删掉所有体积感 | ⚪ **该砍的是 shadow map pass**，靠光照对比表达体积 |
| **真玻璃** | 删掉玻璃 | ⚪ **该砍的是多真玻璃**，主体保留 1 个，DOM 玻璃层补足 |
| **粒子** | 删掉氛围 | ⚪ **该砍的是超千粒子**，≤600 且分层异速更"活" |

**结论**：砍掉的永远是"昂贵的 GPU 黑洞"（Bloom/阴影/多真玻璃/超千粒子），**六大维度功能性 3D 动效一个不删**。场景因此既"完整活"又"流畅快"。

### 灵动 3D 动效为魂的预算分配

```
3D 动效预算，按"完整性 vs 开销"分配：
┌─────────────────────────────────────────────────────────┐
│ ✅ 六大维度 3D 动效全部完整实现（mesh.transform，矩阵运算）    │ ← 全保留（这才是"活"）
│    加载(玻璃归位/相机拉近) + 状态(微摆/点光脉冲) +            │
│    入场(立体飞入) + 交互(相机视差/拖拽) +                    │
│    过渡(材质lerp换肤/滚动推进) +                            │
│    灵动(悬浮/自转/点光环绕/粒子异速/相机呼吸…约12种)          │
│    —— 只改 transform 或单光源单参数，不增 draw call/pass      │
├─────────────────────────────────────────────────────────┤
│ ✅ transmission 真玻璃（1 个主体）+ Points 粒子（≤600，分层异速）│ ← 保留（必需），但量封顶
├─────────────────────────────────────────────────────────┤
│ ⚪ Bloom / UnrealBloom 后处理                               │ ← 该砍（GPU 黑洞）：彻底关，不做候选
│ ⚪ 阴影 / castShadow                                        │ ← 该砍（GPU 黑洞）：彻底关
│ ⚪ 多个真玻璃 / 超千粒子                                     │ ← 该砍：主体 1 个、粒子 ≤600
│ ⚪ setAnimationLoop 常驻渲染静态场景                         │ ← 该砍：按需渲染（markDirty）
└─────────────────────────────────────────────────────────┘
   只有最底下四行是"该砍"的——其余全是"必须完整保留"的 3D 动效。
```

### 为什么 3D 是 GPU 杀手（这些都要砍）

| 开销源 | 机制 | 集显机表现 |
|--------|------|-----------|
| `MeshPhysicalMaterial` transmission | 每帧对场景背景做透射折射采样（额外渲染 pass） | 多个真玻璃 → GPU 满载，帧时间翻倍 |
| `setPixelRatio(devicePixelRatio)` 不封顶 | 3x/4x retina 屏每帧像素量 ×9/×16 | 直接卡死 |
| 1800+ `Points` 粒子 + AdditiveBlending | 每个粒子独立 overdraw + 混合 | fill rate 爆 |
| `UnrealBloomPass` 后处理 | 额外多次全屏模糊 pass | 集显上单独就吃掉一帧预算 |
| 无条件 `setAnimationLoop` 持续渲染 | 静态场景也每帧跑完整渲染管线 | idle 时 GPU 持续满载、风扇狂转、耗电 |

### 集显友好基线（一套方案，无运行时探测，保留完整 3D 动效）

本 skill **不探测 GPU、不读取 `prefers-reduced-motion`、不准备 high/mid/low 多档配置**。复杂度集中在"一套以灵动 3D 微动为魂、且集显也流畅"的写法上。**3D 的"活"优先用近乎免费的 transform 微动堆砌**（悬浮/自转/点光环绕/脉冲/粒子异速/相机呼吸…，约 12 种），故昂贵的 GPU 黑洞该砍则砍。策略是：**按需渲染为主、参数封顶、彻底关掉 GPU 黑洞（Bloom/阴影/多真玻璃），但用免费的 transform 微动把场景堆"活"**。

| 配置项 | 固定值 | 理由 |
|--------|--------|------|
| `pixelRatio` 上限 | **1.5**（`Math.min(devicePixelRatio, 1.5)`） | retina 3x/4x 直跑会让每帧像素量 ×9/×16 直接卡死，1.5 是集显安全与清晰度的平衡 |
| `antialias` | **false** | AA 是全屏逐像素开销；DPR≥1.5 已能抵消锯齿感，省下一帧预算 |
| 真玻璃 transmission 物件 | **1 个**（主体只此一个） | 每个真玻璃是多一次透射采样 pass，集显上多个就爆；玻璃感由 DOM 玻璃层（ultra-design backdrop-filter）补足 |
| 粒子数量 | **≤ 600**（`Points` + AdditiveBlending） | 超千粒子 fill rate 爆；600 颗已足够构成折射内容与氛围 |
| 粒子组织 | **分层异速**（2–3 层不同转速） | 比堆数量更"活"，且不增粒子总数、不增 draw call |
| 阴影 | **关闭**（不 `castShadow`） | shadow map 是额外 render pass + 大纹理，集显上不划算；用光照对比代替（**不损失动效**） |
| Bloom / 后处理 | **彻底关闭，不做候选** | UnrealBloom 是多次全屏模糊 pass，集显上单独就吃掉一帧预算；靠 transmission 折射 + Additive 粒子 + 点光脉冲补"亮" |
| **所有 transform 微动** | **保留并扩充**（约 12 种） | 只改 position/rotation/scale，矩阵运算，不增 draw call/pass——这是 3D "活"的主力，近乎免费 |
| 渲染策略 | **按需渲染**（交互/换肤触发短暂 loop，idle 停止） | 常驻 `setAnimationLoop` 即使静态场景也每帧跑完整管线，集显 idle 风扇狂转。但动效进行时 loop 会正常跑，**视觉上动效完整** |

> **设计哲学**：关掉的是 Bloom 和阴影这类"纯视觉锦上添花"的 GPU 黑洞，**保留并扩充的是所有 transform 微动**（相机视差、玻璃自转、点光环绕、点光脉冲、粒子异速、粒子脉动、相机呼吸、入场归位…）。3D 只做"氛围与主体物件"，靠 DOM 玻璃信息层（沿用 ultra-design）承载内容。**宁可关掉 Bloom，也不关掉相机视差**——前者是昂贵装饰，后者是空间感的命脉且近乎免费。

---


## 混合架构（核心，必须严格遵循）

工业级的 3D 网页不是"把所有 UI 塞进 WebGL"，而是**分层**：

```
┌─────────────────────────────────────────────┐
│  z-index: 1   DOM 玻璃信息层（文字/按钮/UI）   │  ← 沿用 ultra-design 组件
│            半透明，透出下方 3D 场景             │
├─────────────────────────────────────────────┤
│  z-index: 0   <canvas> Three.js 3D 场景层      │  ← 本 skill 核心
│            透明背景(alpha)，真 3D 玻璃+粒子+光  │
├─────────────────────────────────────────────┤
│  底色 body background                          │  ← 主题底色
└─────────────────────────────────────────────┘
```

**为什么是混合而非全 3D**：

| 全 3D（UI 也在 WebGL） | 混合（推荐） |
|----------------------|-------------|
| 文字需 Troika/CSS3DRenderer，清晰度与可访问性差 | DOM 文字原生清晰，屏幕阅读器/选中/缩放全可用 |
| 表单、滚动、焦点态全要重造 | 直接复用 ultra-design 成熟组件 |
| 改造成本高、维护重 | 3D 只管"氛围与主体物件"，职责单一 |
| 仅适合纯视觉 hero / 3D 展示页 | 适合绝大多数生产级页面 |

> **何时用全 3D**：仅当目标是"纯视觉冲击的 3D 产品展示 / 沉浸式 hero、几乎无文本 UI"时，才考虑全 3D（见文末「全 3D 架构」决策表）。其余一律混合。

### 分层骨架

```html
<!DOCTYPE html>
<html data-theme="light" data-accent="mint">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ultra Design 3D</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <!-- 3D 场景层：透明 canvas，fixed 铺满 -->
  <canvas id="scene" class="scene-canvas"></canvas>

  <!-- 可选：CSS 氛围色斑层（与 3D 粒子互补，低开销打底氛围） -->
  <div class="aura"><div class="blob-3"></div><div class="blob-4"></div></div>

  <!-- DOM 玻璃信息层：所有文字/UI，浮在 3D 之上 -->
  <div class="content">
    <nav class="navbar glass-strong glass-sheen">...</nav>
    <main>...</main>
  </div>

  <!-- 主题切换面板（沿用 ultra-design） -->
  <div class="theme-switch glass glass-sheen">...</div>
</body>
</html>
```

```css
/* 3D canvas：透明、铺满、置底 */
.scene-canvas {
  position: fixed;
  inset: 0;
  z-index: 0;
  display: block;
  pointer-events: none;   /* 默认穿透，让 3D 不挡 DOM 交互；需 3D 内交互时局部打开 */
}
/* DOM 信息层浮于 3D 之上 */
.content, .theme-switch { position: relative; z-index: 1; }
/* 底色 */
body { background: var(--glass-bg-base); min-height: 100vh; overflow-x: hidden; }
```

> `pointer-events: none` 让 canvas 默认不拦截 DOM 交互。若需要"拖拽旋转 3D 物体"，在该物体区域用独立交互层或 `OrbitControls` 限定范围。

---

## Three.js 引入（importmap + ESM，固定版本）

**必须用 importmap + ES Module**，禁用旧式全局 `three.min.js`。固定版本避免 API 漂移：

```html
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.161.0/build/three.module.js",
    "three/addons/": "https://unpkg.com/three@0.161.0/examples/jsm/"
  }
}
</script>
<script type="module">
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { RoomEnvironment } from 'three/addons/environments/RoomEnvironment.js';
// 后处理（Bloom）默认不使用，仅确认独显环境手动开启时才 import：
// import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
// import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
// import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';

// 主入口
main();
</script>
```

> 无网络/内网环境：把 `three.module.js` 与 addons 目录下载到本地 `vendor/three/`，importmap 指向本地路径。CDN 仅用于能联网的开发演示。

---

## 双维度主题系统（沿用 ultra-design，3D 层共享变量）

**完整变量定义照搬 `ultra-design` 的「CSS 变量定义」整段**（明暗基础层 + 7 个色相层 + 派生色 + 切换逻辑 JS）。默认 `<html data-theme="light" data-accent="mint">`（薄荷绿 `#00D4AA`）。

本 skill 的关键约定：**3D 场景的所有颜色（玻璃 attenuation、光源色、粒子色、env 背景）一律读取这些 CSS 变量**，绝不写死。这样 DOM 切主题色/明暗时，3D 场景同步换肤——这是"立体感知极强且主题一致"的命门。

可被 3D 读取的关键变量：`--accent`、`--accent-rgb`、`--aura-1`~`--aura-4`、`--glass-bg-base`、`--glass-highlight`。

### 主题桥接：让 3D 监听换肤（核心机制）

ultra-design 的 `setTheme` / `setAccent` 只改 DOM。3D 层需额外获通知。**在 setTheme/setAccent 内派发自定义事件**：

```js
// 在 ultra-design 的 setTheme / setAccent 末尾各追加一行：
window.dispatchEvent(new CustomEvent('glass-theme-change'));
```

```js
// 3D 层读取 CSS 变量并应用到材质/光源
function readVar(name) {
  return getComputedStyle(document.documentElement).getPropertyValue(name).trim();
}
function applyThemeToScene() {
  const accent = readVar('--accent') || '#00D4AA';
  const aura1  = readVar('--aura-1')  || '#B5F0E0';
  const aura2  = readVar('--aura-2')  || '#A0E8E0';
  const aura3  = readVar('--aura-3')  || '#C8F5E8';
  const baseBg = readVar('--glass-bg-base') || '#F5F5F7';

  // 强调色点光：玻璃边缘与高光的主着色
  accentLight.color.set(accent);
  // 玻璃材质折射时的边缘色（attenuation）随主题色
  glassMaterial.attenuationColor.set(accent);
  // 粒子海取氛围色之一
  particles.material.color.set(aura2);
  // 渐变环境背景
  scene.background = makeGradientTexture(baseBg, aura1, aura3);
  // 场景雾气底色
  scene.fog.color.set(baseBg);
}
window.addEventListener('glass-theme-change', applyThemeToScene);
```

> `makeGradientTexture` 用 Canvas2D 画一张径向渐变贴图返回 `THREE.CanvasTexture`，作为 `scene.background` 与玻璃 envMap 来源（见下文）。

---

## 3D 场景骨架（完整可复制）

```js
let renderer, scene, camera, clock;
let glassMesh, particles, accentLight, glassMaterial;

function initScene() {
  const canvas = document.getElementById('scene');
  clock = new THREE.Clock();

  // 渲染器：参数为固定集显友好值（antialias:false / pixelRatio 封顶 1.5），无运行时分档
  renderer = new THREE.WebGLRenderer({
    canvas, antialias: false, alpha: true, powerPreference: 'high-performance',
  });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));  // 封顶 1.5，retina 3x+ 不直跑
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.toneMapping = THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure = 1.05;
  renderer.outputColorSpace = THREE.SRGBColorSpace;

  // 场景 + 雾（制造纵深与远近层次）
  scene = new THREE.Scene();
  scene.fog = new THREE.FogExp2(0xF5F5F7, 0.025);

  // 透视相机：50° 视场，近大远小
  camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(0, 0, 8);

  // 环境反射（玻璃折射必备）：用主题渐变生成 envMap
  buildEnvironment();

  // 光照体系（不开启阴影：shadow map 是额外 render pass，集显上不划算）
  buildLights();

  // 3D 玻璃主体物件：固定 1 个（多个 transmission 物件在集显上会爆）
  buildGlassObjects(1);

  // 3D 粒子海：固定 600 颗（超千 fill rate 爆）
  buildParticles(600);

  // 应用一次主题（读取 CSS 变量着色）
  applyThemeToScene();

  // 自适应（resize 节流）
  let rzT = 0;
  window.addEventListener('resize', () => { clearTimeout(rzT); rzT = setTimeout(onResize, 150); });

  // 渲染策略：按需渲染（不跑 setAnimationLoop）。idle 时 GPU 完全空闲，
  // 仅交互/换肤/动效进行时跑短暂 loop，落定即停。
  renderer.render(scene, camera);   // 初始一帧
  startOnDemandRender();
}
```

### 按需渲染（核心节能策略，默认即用，动效仍完整）

不跑持续 loop——只在「需要变化」时渲染一帧，idle 时 GPU 完全空闲。用一个「脏标记 + 短暂 loop」实现：交互/换肤触发时标记脏，跑 ~1s 的 loop 让动效落地，之后自动停止。**注意：这是"省电"而非"省动画"**——当鼠标移动、滚动、换肤时，动效完整播放（相机视差、玻璃自转、点光环绕都跑满），只在长时间静止后才停止渲染。idle 停止是因为场景本就不变了，不是因为删了动效。这是本 skill 在集显上也能流畅的根本原因。

```js
let loopUntil = 0;   // 持续渲染到的时间戳
function markDirty(durationMs = 1000) {
  loopUntil = performance.now() + durationMs;
  ensureLooping();
}
let looping = false;
function ensureLooping() {
  if (looping) return;
  looping = true;
  const tick = () => {
    animate();   // 渲染一帧（含相机视差、玻璃自转等）
    if (performance.now() < loopUntil) {
      requestAnimationFrame(tick);
    } else {
      looping = false;   // idle，停止，GPU 空闲
    }
  };
  requestAnimationFrame(tick);
}
function startOnDemandRender() {
  // 鼠标视差、滚动、换肤 → 标记脏，触发短暂渲染
  window.addEventListener('pointermove', () => markDirty(800), { passive: true });
  window.addEventListener('scroll', () => markDirty(800), { passive: true });
  window.addEventListener('glass-theme-change', () => markDirty(1000));
  // 首屏入场
  markDirty(2000);
}
```

---

## 真 3D 玻璃材质配方（核心，区分于 ultra-design 的伪玻璃）

`ultra-design` 的 `backdrop-filter` 是屏幕空间模糊（伪透射）。本 skill 用 `MeshPhysicalMaterial` 的 **transmission（透射）+ ior（折射率）+ thickness（厚度）** 实现真物理折射——玻璃会真实地扭曲其背后的 3D 物件与粒子：

```js
glassMaterial = new THREE.MeshPhysicalMaterial({
  transmission: 1.0,          // 完全透射（1=纯净玻璃）
  thickness: 1.2,             // 厚度越大折射越强
  roughness: 0.04,            // 极光滑表面
  metalness: 0,
  ior: 1.5,                   // 玻璃折射率
  clearcoat: 1.0,             // 透明清漆层，强化顶面反光
  clearcoatRoughness: 0.08,
  envMapIntensity: 1.3,       // 环境反射强度
  attenuationColor: new THREE.Color('#00D4AA'),  // 玻璃内部吸收色（随主题色，applyThemeToScene 里同步）
  attenuationDistance: 1.5,   // 颜色吸收距离（越小边缘越染色）
  transparent: true,
  side: THREE.DoubleSide,
});

// 主体玻璃物件：一个悬浮的圆角玻璃形态（IcosahedronGeometry 雕塑感）
glassMesh = new THREE.Mesh(new THREE.IcosahedronGeometry(1.6, 1), glassMaterial);
scene.add(glassMesh);
```

**transmission 生效的三要素**（缺一不可，常见坑）：

| 要素 | 作用 | 实现 |
|------|------|------|
| `scene.environment`（envMap） | 玻璃反射/折射的"环境内容"，无则玻璃发黑 | `PMREMGenerator` 从渐变场景生成 |
| 渲染器透传目标 | 透射需要采样背景缓冲 | Three r150+ 默认支持，确保 `transparent: true` |
| 背后有内容可折射 | 空场景里玻璃无折射可见 | 场景里放粒子/色块/其他物件 |

```js
function buildEnvironment() {
  const pmrem = new THREE.PMREMGenerator(renderer);
  pmrem.compileEquirectangularShader();
  // 用主题色斑构建一个微型渐变环境场景，玻璃反射出主题色
  const envScene = makeEnvScene();
  const envTex = pmrem.fromScene(envScene, 0.04).texture;
  scene.environment = envTex;
  pmrem.dispose();
}
```

> 高级：玻璃背后的折射内容越丰富（彩色粒子、多光源、其他几何体），折射越惊艳。让粒子海/色块均匀分布在玻璃周围。

---

## 光照系统（3D 光场，让玻璃"活"过来）

ultra-design 的光照是 CSS 高光（静态）。本 skill 用真实 3D 光源——玻璃的边缘高光、色散、反射全部由光源物理驱动：

```js
function buildLights() {
  // 1. 环境光：全局补底，避免阴影面死黑
  scene.add(new THREE.AmbientLight(0xffffff, 0.4));

  // 2. 主方向光：塑形（不 castShadow —— shadow map 是额外 render pass，
  //    集显上不划算，靠光照对比表达体积）
  const key = new THREE.DirectionalLight(0xffffff, 1.2);
  key.position.set(5, 8, 6);
  scene.add(key);

  // 3. 强调色点光：随主题色，环绕玻璃，制造边缘染色
  accentLight = new THREE.PointLight(0x00D4AA, 8, 20, 2);
  accentLight.position.set(-3, 2, 3);
  scene.add(accentLight);

  // 4. 鼠标跟随聚光灯：交互时光斑跟随，最强感知（见动效系统）
  followLight = new THREE.SpotLight(0xffffff, 4, 25, Math.PI / 6, 0.4, 1.5);
  followLight.position.set(0, 5, 5);
  scene.add(followLight);
}
```

---

## 立体动效系统（核心增强，覆盖五大维度，完整实现）

ultra-design 的动效是 2.5D（transform tilt / parallax）。本 skill 全部升级为真 3D。**这些 3D 动效是空间生命感的来源，必须完整实现**——它们开销在 transform 计算上（廉价），真正昂贵的是 Bloom/阴影/多真玻璃（已在基线中关掉）。所以：**保留所有运动，关掉纯装饰的 GPU 黑洞**。

### (1) 动态相机视差（最强立体感知来源，必须保留）

相机随鼠标做空间偏移与微旋转，整个场景产生视差——这是"可以走进去"的空间感来源：

```js
let mx = 0, my = 0;          // 鼠标归一化位置 (-0.5~0.5)
window.addEventListener('pointermove', e => {
  mx = e.clientX / window.innerWidth - 0.5;
  my = e.clientY / window.innerHeight - 0.5;
});

function animate() {
  const t = clock.getElapsedTime();
  const dt = clock.getDelta();

  // 相机视差：跟随鼠标做空间位移 + 缓动（lerp 惯性）
  const camTX = mx * 2.2;
  const camTY = -my * 1.6;
  camera.position.x += (camTX - camera.position.x) * 0.05;
  camera.position.y += (camTY - camera.position.y) * 0.05;
  // 相机自身做极慢绕轨呼吸，强化"活的场景"
  camera.position.z = 8 + Math.sin(t * 0.2) * 0.3;
  camera.lookAt(0, 0, 0);

  // 玻璃主体：缓慢自转 + 轻微悬浮
  glassMesh.rotation.x = t * 0.15;
  glassMesh.rotation.y = t * 0.2;
  glassMesh.position.y = Math.sin(t * 0.8) * 0.15;

  // 强调色点光环绕玻璃运动，玻璃边缘高光随之流动
  accentLight.position.x = Math.sin(t * 0.6) * 4;
  accentLight.position.z = Math.cos(t * 0.6) * 4;

  // 鼠标跟随聚光灯
  followLight.position.set(mx * 6, my * 6 + 3, 6);

  // 粒子整体缓慢自转
  particles.rotation.y = t * 0.03;

  renderer.render(scene, camera);
}
```

### (2) 3D 粒子海（空间中的氛围星尘）

`InstancedBufferGeometry` 或 `Points`，环绕玻璃，既是折射内容也是氛围：

```js
function buildParticles() {
  const COUNT = 1800;
  const positions = new Float32Array(COUNT * 3);
  for (let i = 0; i < COUNT; i++) {
    // 球壳分布 + 随机扰动，避免均匀网格感
    const r = 4 + Math.random() * 12;
    const theta = Math.random() * Math.PI * 2;
    const phi = Math.acos(2 * Math.random() - 1);
    positions[i*3]   = r * Math.sin(phi) * Math.cos(theta);
    positions[i*3+1] = r * Math.sin(phi) * Math.sin(theta);
    positions[i*3+2] = r * Math.cos(phi);
  }
  const geo = new THREE.BufferGeometry();
  geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));

  const mat = new THREE.PointsMaterial({
    size: 0.06,
    color: new THREE.Color('#A0E8E0'),   // applyThemeToScene 里同步换色
    transparent: true,
    opacity: 0.8,
    blending: THREE.AdditiveBlending,    // 叠加发光
    depthWrite: false,
    sizeAttenuation: true,               // 远小近大，强化纵深
  });
  particles = new THREE.Points(geo, mat);
  scene.add(particles);
}
```

### (3) 立体入场（从空间深处飞入并旋转落定）

页面加载时，3D 物件从远处 + 大模糊态飞入落定（呼应 ultra-design 的 `blurIn`/`riseIn`，但发生在 3D 空间）：

```js
// 入场：相机从 z=20 拉到 z=8，玻璃从 scale 0 旋开
let entered = false;
function playEntry() {
  camera.position.set(0, 0, 20);
  glassMesh.scale.setScalar(0.01);
  glassMesh.rotation.set(-2, -3, 0);
  entered = true;
}
// animate 里当 entered 时做 lerp 落定：
//   camera.position.z += (8 - camera.position.z) * 0.04;
//   glassMesh.scale.x += (1 - glassMesh.scale.x) * 0.06;
//   glassMesh.rotation.x += (0 - glassMesh.rotation.x) * 0.05;
// 落定后 entered=false，切换到常规自转逻辑。
```

### (4) 滚动驱动相机推进（页面级 3D 视差）

ultra-design 的滚动视差是 2D transform。本 skill 让相机随滚动在 3D 空间推进/绕轨，整页产生空间穿梭感：

```js
let scrollY = 0;
window.addEventListener('scroll', () => { scrollY = window.scrollY; }, { passive: true });
// animate 里：
//   const scrollN = scrollY / (document.body.scrollHeight - innerHeight);
//   camera.position.z = 8 - scrollN * 3;        // 滚动推进
//   glassMesh.rotation.y += scrollN * 0.5 * dt;  // 滚动加速旋转
```

### (5) 后处理 Bloom（默认关闭，集显不启用）

Bloom 让玻璃边缘高光与粒子产生柔和泛光、电影感拉满，但 `UnrealBloomPass` 是多次全屏模糊 pass，集显上单独就吃掉一帧预算。本 skill 的基线**不开 Bloom**——靠 transmission 折射 + AdditiveBlending 粒子 + 强调色点光已足够"亮"。仅当确认目标均为独显机（如内部高端工作站）时方可手动开启：

```js
// 仅独显环境手动启用，基线不包含此段
const composer = new EffectComposer(renderer);
composer.addPass(new RenderPass(scene, camera));
const bloom = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  0.6,   // strength
  0.4,   // radius
  0.85,  // threshold（只让高亮部分泛光）
);
composer.addPass(bloom);
// animate 末尾用 composer.render() 替代 renderer.render()
```

---

## 缓动与时间（3D 物理曲线）

3D 动效用 `lerp`（线性插值 + 阻尼系数）实现惯性，而非 CSS 曲线。阻尼系数对照：

| 阻尼系数 | 效果 | 适用 |
|---------|------|------|
| `0.02~0.04` | 慢而丝滑的大位移（相机推进、入场） | 相机、场景级 |
| `0.05~0.08` | 中等惯性（相机视差、玻璃归位） | 默认指针跟随 |
| `0.1~0.15` | 快速吸附（光源跟随、即时反馈） | 强调色点光 |

> 复杂编排可引入 GSAP（`gsap.to(mesh.position, {...})`），但单文件优先用纯 lerp + `clock.getDelta()` 归一化，避免额外依赖。

---

## 🌟 性价比灵动 3D 动效（核心中的核心：开销极低、视觉极佳）

这是本 skill 的**灵魂所在**。3D 层除了相机视差这种"大动效"，还有一批**几乎零开销的轻量 3D 动效**——它们只改 transform（Three.js 里改 mesh.position/rotation/scale，本质是矩阵运算，不增加 draw call、不增加渲染 pass），却能让场景"灵动"起来。**场景"活"的主力是这些**，从而把昂贵的 Bloom/多真玻璃/阴影该砍则砍。

> **使用心法**：这 12 个 transform 微动叠加起来，玻璃悬浮着、慢慢转着、点光绕着脉冲着、粒子分层异速飘着、相机呼吸着——场景立刻充满空间生命力，而 Bloom 一个都不需要、真玻璃只一个、阴影全关。**这是"该砍则砍"能成立的全部底气**——免费的 transform 微动足够"活"，所以昂贵的 GPU 黑洞砍得起。

### 1. 玻璃主体悬浮呼吸（Float）—— 缓慢上下浮动

真玻璃不静止，而是像悬浮在空中般轻轻起伏。只改 `position.y`，零额外开销。

```js
// animate() 内
glassMesh.position.y = Math.sin(t * 0.8) * 0.15;
```

### 2. 缓慢自转（Slow Spin）—— 展示折射的不同面

玻璃极慢旋转，让透射折射的内容不断变化，"玻璃在转动"的感知。只改 `rotation`。

```js
glassMesh.rotation.x = t * 0.15;
glassMesh.rotation.y = t * 0.2;
```

### 3. 强调色点光环绕（Orbit Light）—— 边缘高光流动

强调色点光绕玻璃做圆周运动，玻璃边缘的高光随之流动——这是 3D 玻璃"有生命"的关键。单光源位移，开销极低。

```js
accentLight.position.x = Math.sin(t * 0.6) * 4;
accentLight.position.z = Math.cos(t * 0.6) * 4;
```

### 4. 粒子分层异速自转（Layered Particle Drift）—— 星尘分层流转

粒子分成 2–3 层（不同 `Points` 组或按顶点分组），各层以不同转速旋转——比整组同速更"活"，制造层次纵深。**总粒子数不增、draw call 不增**（每层一个 Points），却比堆数量更有生命力。

```js
// 分层：近层快、远层慢（建议 2 层即可，避免 draw call 翻倍）
particlesNear.rotation.y  = t * 0.05;
particlesNear.rotation.x  = Math.sin(t * 0.07) * 0.12;
particlesFar.rotation.y   = t * 0.02;          // 远层更慢，强化视差
particlesFar.rotation.z   = Math.sin(t * 0.03) * 0.08;
```

### 5. 相机呼吸绕轨（Camera Breathe）—— 微妙的空间深度变化

相机 z 轴做极慢正弦摆动，整个场景的透视微妙呼吸，强化"可以走进去"的临场感。

```js
camera.position.z = 8 + Math.sin(t * 0.2) * 0.3;
```

### 6. 入场缩放归位（Pop-in）—— 从远到近的落定

加载时玻璃从 scale 0.01 + 远处 lerp 到位，配合相机拉近，首屏就有"物体从空间深处浮现"的戏剧感。纯 lerp，零开销。

```js
// 入场期间 lerp
glassMesh.scale.x += (1 - glassMesh.scale.x) * 0.06;
camera.position.z += (8 - camera.position.z) * 0.04;
```

### 7. 点光脉冲（Light Pulse）—— 强调色点光亮度呼吸

强调色点光的 `intensity` 做正弦呼吸（注意：是 intensity 而非颜色，单光源单参数，开销极低），玻璃边缘的高光亮度随之"心跳"。配合环绕，边缘流光更生动。

```js
accentLight.intensity = 6 + Math.sin(t * 1.4) * 2.5;   // 亮度在 3.5~8.5 间呼吸
```

> ⚠️ `intensity` 变化会触发该光源着色重算，但单光源、低频（正弦）远比堆光源便宜。**只对 1 个强调色点光用**，不要对每个光源都脉冲。

### 8. 粒子脉动缩放（Particle Pulse）—— 星尘整体呼吸缩放

整组粒子做极轻的 scale 呼吸（远小近大的 sizeAttenuation 让缩放产生"涌向/退离"的纵深错觉）。整组一个 scale，零额外 draw call。

```js
const pulse = 1 + Math.sin(t * 0.5) * 0.06;   // 0.94~1.06 轻微缩放
particles.scale.setScalar(pulse);
```

### 9. 相机微摆（Camera Sway）—— 微妙的水平/俯仰摆动

相机除 z 轴呼吸外，再做极小幅度的水平/俯仰正弦摆动（独立于鼠标视差，是自动的"呼吸"），场景产生"正在被注视"的微妙临场感。与鼠标视差叠加（视差是手动的，微摆是自动的）。

```js
// 与鼠标视差叠加：基础微摆 + 鼠标 lerp
const swayX = Math.sin(t * 0.3) * 0.4;
const swayY = Math.cos(t * 0.27) * 0.3;
camera.position.x += ((mx * 2.2 + swayX) - camera.position.x) * 0.05;
camera.position.y += ((-my * 1.6 + swayY) - camera.position.y) * 0.05;
```

### 10. 边缘虹彩随转（Iridescent Edge Tilt）—— 玻璃微倾让折射色散流动

玻璃除自转外，叠加一个极小的周期性 tilt（绕交替轴的微小旋转），让透射折射的色散/边缘色不断流动变化——"玻璃在折射不同角度的光"。纯 rotation，零开销。

```js
// 在自转基础上叠加微 tilt（与自转不同频率，避免同步）
glassMesh.rotation.z = Math.sin(t * 0.4) * 0.12;
```

### 11. 景深呼吸（Depth of Field Breathe）—— 远近层次微调

若用了 `focusAt`/雾，让相机的 lookAt 目标或 fog 密度做极轻正弦变化，远近层次的"虚化边界"缓慢呼吸——强化空间纵深的"活"。雾密度单参数，开销极低。

```js
scene.fog.density = 0.025 + Math.sin(t * 0.15) * 0.004;   // 雾密度轻微呼吸
```

### 12. 强调色点光高度起伏（Light Bob）—— 点光做 8 字运动

点光除水平环绕外，再叠加垂直方向的起伏，做立体 8 字/螺旋运动——边缘高光的流动轨迹更立体、更有机。单光源三轴位移，开销极低。

```js
accentLight.position.x = Math.sin(t * 0.6) * 4;
accentLight.position.z = Math.cos(t * 0.6) * 4;
accentLight.position.y = 2 + Math.sin(t * 1.2) * 1.5;   // 垂直起伏，构成螺旋
```

### 灵动编排心法

| 原则 | 说明 |
|------|------|
| **全走 transform** | 上述 12 个动效 100% 改 transform（position/rotation/scale）或单光源单参数（intensity/fog.density），不增 draw call、不增 pass，集显上也轻松——这是"性价比灵动"的本质，也是"该砍则砍"的底气 |
| **组合使用** | 悬浮 + 自转 + 点光环绕 + 点光脉冲 + 粒子异速 + 相机呼吸 6 个常驻微动叠加，玻璃立刻"活"了，而 Bloom 一个都不需要、真玻璃只一个、阴影全关 |
| **慢即是美** | 3D 灵动感来自**极慢**的持续微动（系数 0.03~0.6），不是快速旋转。慢动效更高级、更省感知负担 |
| **相机是王** | 相机呼吸 + 视差 + 微摆的"场景级"动效，比单个物体的转动感知强 10 倍——优先投预算给相机 |
| **分层 > 堆量** | 粒子分层异速、点光做 8 字，比堆粒子数量/堆光源更"活"，且不增 draw call |

> **核心洞察**：3D 的灵动感不来自 Bloom 和多真玻璃（那是 GPU 黑洞），而来自这 12 个**几乎免费的 transform 微动**的持续叠加。玻璃悬浮着、慢慢转着、点光绕着脉冲着、粒子分层异速飘着、相机呼吸着微摆着——场景就充满空间生命力，集显依然 60fps。**正因为 transform 微动承接了全部"活"，昂贵的 Bloom/阴影/多真玻璃才得以该砍则砍**——这是 3D"又活又快"的全部秘密。

---

## 性能与可访问性（WebGL 60fps 守则，必须严守）

> 本节是「性能第一性原则」在 WebGL 侧的落地细则。**所有渲染参数是固定值**（不读运行时分档对象、不探测 GPU）：`antialias:false`、`pixelRatio` 封顶 1.5、真玻璃 ≤ 1、粒子 ≤ 600、关阴影、关 Bloom、按需渲染。这套固定值按"集显也能流畅跑"的预算设计。

WebGL 性能远比 CSS 敏感，违规直接掉帧：

**性能（固定参数，无运行时分档）**：
- `setPixelRatio(Math.min(devicePixelRatio, 1.5))`——retina 3x+ 不直跑，封顶 1.5。
- `antialias: false`——靠 DPR 抵消锯齿感，省一帧预算。
- 渲染策略默认**按需渲染**（交互/换肤触发 `markDirty()` 跑短暂 loop，idle 时 GPU 空闲）；**不使用 `setAnimationLoop`** 持续渲染（静态场景常驻 loop 会集显 idle 风扇狂转）。
- `transmission` 真玻璃物件数 **≤ 1**——每个真玻璃是额外透射采样 pass，集显上多个就爆；主体只一个，玻璃感由 DOM 玻璃层（ultra-design 的 backdrop-filter）补足。
- 粒子用 `Points` + `AdditiveBlending` + `depthWrite:false`；总数 **≤ 600**（超千 fill rate 爆）。
- **不开启阴影**（不 `castShadow`）——shadow map 是额外 render pass + 大纹理，集显上不划算。
- **不开 Bloom/后处理**——全屏多次模糊 pass 在集显上吃掉整帧预算。
- canvas `pointer-events: none` 避免无谓命中检测。

**可访问性（基线即克制，无需运行时检测）**：

本 skill 不读取 `prefers-reduced-motion`、不做"减少动效→静态降级"的运行时特判。**3D 动效是必需品**：相机视差、玻璃自转、强调色点光环绕、粒子、立体入场——这些构成空间生命感的运动全部保留。基线保证流畅靠的是"关掉 Bloom/阴影/多真玻璃这些 GPU 黑洞 + 按需渲染"，而非"删掉动效"。当鼠标移动、滚动、换肤时动效完整播放；仅长时间 idle 时停止渲染（场景本不变，停的是渲染而非动画）。因此偏好减少动效的用户与集显机型拿到的是同一套完整但节能的体验。

> 若希望严格尊重系统「减少动效」，可选地在入场处只渲染一帧静态构图（玻璃材质、粒子、光照都在，只去掉运动），而非空 canvas——但这不是默认行为，基线靠"无持续运动"本身已足够安静。

---

## 何时用全 3D 架构（决策表）

| 场景 | 推荐架构 |
|------|---------|
| Dashboard / 落地页 / 应用 UI / 可视化（有大量文字与交互） | **混合**（默认） |
| 3D 产品展示 / 沉浸式 hero / 艺术海报（纯视觉、极少文本） | **全 3D**（文字用 Troika Text，UI 极简） |
| 需要拖拽旋转 3D 物体的交互展区 | 混合 + 该区域局部开启 `OrbitControls` |

全 3D 时：文字用 `troika-three-text`，UI 用极简 3D 按钮（`Mesh` + raycaster 命中），并务必保留键盘可达性与 ARIA。成本显著高于混合，非必要不选。

---

## 执行流程

### 第 1 步：复用 ultra-design 主题与 DOM 玻璃层
完整引入 `ultra-design` 的 CSS 变量定义（明暗基础层 + 7 色相层 + 派生色）与切换逻辑 JS。DOM 信息层（导航/卡片/按钮/输入框/主题面板）直接用 ultra-design 组件。**在 setTheme/setAccent 末尾追加 `window.dispatchEvent(new CustomEvent('glass-theme-change'))`。**

### 第 2 步：铺分层骨架
写 `<canvas#scene class="scene-canvas">`（z-index:0，`pointer-events:none`）+ `.content`（z-index:1）。DOM 玻璃层透出底层 3D 场景。

### 第 3 步：引入 Three.js（importmap + ESM）
固定 `three@0.161.0`，按需 import `OrbitControls` / `RoomEnvironment` / 后处理。

### 第 4 步：搭建 3D 场景与真玻璃
复制「3D 场景骨架」+「真 3D 玻璃材质配方」+「光照系统」。务必 `buildEnvironment()` 生成 envMap，否则 transmission 失效。玻璃 `attenuationColor` 与光源色读取 CSS 变量。

### 第 5 步：加立体动效（六大维度 3D 动效体系，完整实现）

**六大维度 3D 动效都要完整实现**，"一强多弱"，昂贵 GPU 黑洞该砍则砍：
- **① 加载**：3D 资源加载期间，DOM 层叠加 ultra-design 的 spinner/骨架屏；3D 层用玻璃 scale 0→1 lerp + 相机从远拉近做"正在构建"反馈。
- **② 状态**：idle 时相机做极慢正弦微摆（待机感）+ 强调色点光 intensity 脉冲呼吸（在线感）；状态切换整体色调 lerp。
- **③ 入场（必选强动效）**：立体飞入——相机 z=20→8 lerp + 玻璃 scale 0+旋转→1 落定 + 粒子从中心扩散。首屏戏剧时刻。
- **④ 交互（必选强动效）**：相机视差跟随指针（最强空间感知，rAF 合并 lerp）；展区局部 OrbitControls 拖拽；长页面滚动相机推进绕轨。
- **⑤ 过渡**：主题换肤时材质/光源/粒子色用 lerp 渐变（非跳变）；滚动驱动 camera.position.z。
- **⑥ 灵动点缀（常驻微动叠加）**：玻璃悬浮 + 缓慢自转 + 点光环绕 + 点光脉冲 + 粒子分层异速 + 粒子脉动缩放 + 相机呼吸 + 相机微摆 + 边缘虹彩随转 + 景深呼吸 + 点光 8 字起伏（约 12 种）。
- **Bloom/阴影该砍则砍**（只砍 GPU 黑洞）：彻底关闭，靠 transmission 折射 + Additive 粒子 + 点光脉冲补"亮"。

### 第 6 步：主题桥接 + 性能约束 + 自检
`applyThemeToScene()` 监听 `glass-theme-change`，所有 3D 颜色读 CSS 变量。固定 `pixelRatio` 封顶、关阴影关 Bloom、按需渲染。跑自检清单。

---

## 自检清单

**性能第一性（最高优先级，必须全部通过，否则其余免谈）**：
- [ ] **没有任何运行时 GPU/偏好检测**（无 `detectTier3D`、无 `data-tier`、无 `prefers-reduced-motion` 分支）？
- [ ] `WebGLRenderer` 参数为固定值：`antialias: false`、`setPixelRatio(Math.min(dpr, 1.5))`，无写死的 `antialias:true` / `setPixelRatio(2)`？
- [ ] 默认**按需渲染**（`markDirty()` 短暂 loop + idle 停止），**没有** `setAnimationLoop` 持续渲染静态场景？
- [ ] 真玻璃 transmission 物件 **= 1**，粒子 **≤ 600**，**彻底未开启阴影、未开启 Bloom**（Bloom 不做候选）？
- [ ] 无任何在集显上也持续运行的 `setAnimationLoop` / `infinite` 动画？

**架构与材质**：
- [ ] 采用混合架构：3D canvas（z-index:0, pointer-events:none）+ DOM 玻璃信息层（z-index:1）？
- [ ] Three.js 用 importmap + ESM，固定版本（0.161.0），非旧式全局脚本？
- [ ] 真玻璃用 `MeshPhysicalMaterial` 的 transmission+ior+thickness，且 `scene.environment` 已生成（PMREM envMap）？
- [ ] 场景里有可折射内容（粒子/色块/几何体），玻璃折射可见而非发黑？
- [ ] 光照体系齐全：环境光 + 主方向光 + 强调色点光 +（可选）鼠标跟随聚光？
- [ ] **① 加载：3D 资源加载有"正在构建"反馈（玻璃归位 lerp + 相机拉近）+ DOM 层 spinner/骨架屏？**
- [ ] **② 状态：idle 相机微摆 + 点光 intensity 脉冲呼吸；状态切换色调 lerp？**
- [ ] **③ 入场：立体飞入（相机拉近 + 玻璃 scale 归位 + 旋转落定）已实现？**
- [ ] **④ 交互：相机视差跟随指针（rAF 合并 lerp）；展区 OrbitControls？**
- [ ] **⑤ 过渡：主题换肤材质/光源/粒子色 lerp 渐变（非跳变）？**
- [ ] **⑥ 灵动：是否用足了 transform 微动（悬浮/自转/点光环绕/点光脉冲/粒子分层异速/粒子脉动/相机呼吸/相机微摆/边缘虹彩/景深呼吸/点光8字/入场归位），而非靠 Bloom/多真玻璃撑场面？**
- [ ] 所有 3D 颜色（材质 attenuation、光源、粒子、env 背景、雾）读 CSS 变量，无写死色值？
- [ ] setTheme/setAccent 派发 `glass-theme-change`，3D 层监听并同步换肤？
- [ ] 动效遵循"一强多弱"：每场景一个强动效（相机视差/立体入场），其余用克制微动衬托？
- [ ] 真玻璃物件 = 1，粒子用 Points+AdditiveBlending+depthWrite:false，且分层异速（2–3 层不同转速）？
- [ ] DOM 信息层沿用 ultra-design（字体 SF Pro/PingFang、玻璃三件套、主题面板、完整七大维度动效）？
- [ ] resize 监听更新 camera.aspect / renderer.setSize？
- [ ] **确认：该砍则砍只砍了 GPU 黑洞（Bloom/阴影/多真玻璃/超千粒子），没有删减任何六大维度功能性 3D 动效？**

---

## 设计准则

- **3D 服务氛围，信息层服务内容**：3D 玻璃物件/粒子是氛围与空间感的来源，不可遮挡或干扰 DOM 文字阅读。
- **完整 3D 动效，该砍则砍**：六大维度 3D 动效（加载/状态/入场/交互/过渡/灵动）一律完整保留，绝不删减；该砍则砍只针对昂贵 GPU 黑洞（Bloom/阴影/多真玻璃/超千粒子）。
- **真折射 > 贴图**：玻璃用 transmission 真折射，背景必须放可折射内容；空场景里的真玻璃是浪费。
- **相机是最大的动效**：相机视差/推进/呼吸/微摆带来的空间感，远胜单个物体的旋转。优先把"强动效"预算花在相机上。
- **颜色全部走变量**：3D 与 DOM 共享同一套 CSS 变量，换肤时两层一致——这是"立体且主题统一"的根本。
- **一强多弱**：每屏一个强 3D 动效（相机穿梭 / 立体入场 / 大玻璃雕塑自转），其余用克制的粒子与微光衬托。
- **强调色点睛**：`accentLight` 与 `attenuationColor` 用主题强调色，3D 玻璃透出品牌色族，而非彩虹乱炖。

---

## 常见误区

| 误区 | 正确做法 |
|------|---------|
| **只有灵动 transform 微动，缺其他维度**（无加载入场/无状态脉冲/无交互视差） | 六大维度 3D 动效齐全才是完整体系：加载(归位)/状态(微摆+点光脉冲)/入场(立体飞入)/交互(相机视差)/过渡(材质lerp)/灵动(12种) 缺一不可 |
| **3D 场景"死气沉沉"，只靠 Bloom 泛光撑场面** | 用六大维度完整动效 + 灵动 transform 微动库（12 种）：悬浮/自转/点光环绕/脉冲/粒子异速/相机呼吸/微摆/边缘虹彩/景深呼吸——这是空间生命力的主力，近乎免费 |
| **核显也强行开 transmission+Bloom+阴影+持续 loop** | **用单一集显友好基线 + 完整动效**：真玻璃 = 1、粒子 ≤ 600、关阴影、彻底关 Bloom、按需渲染，参数全固定值；"活"交给六大维度 transform 动效 |
| **`setPixelRatio(2)` / `antialias:true` 写死在代码里** | 固定 `Math.min(dpr, 1.5)` + `antialias:false`，不写死高档参数 |
| **`setAnimationLoop` 持续跑（静态场景也每帧渲染）** | 默认按需渲染（`markDirty()` 短暂 loop + idle 停止），静态场景仅交互/换肤时 render 一次 |
| **靠运行时检测做集显/偏好分档**（`detectTier3D` / `data-tier` / `prefers-reduced-motion` 分支） | 不检测、不分档：单一集显友好基线（完整动效 + 该砍则砍）一揽子保证流畅 |
| 把所有 UI（含文字表单）塞进 WebGL | 混合架构：文字/UI 留 DOM，3D 只管氛围与主体物件 |
| 用旧式 `three.min.js` 全局脚本 | importmap + ESM，固定版本，按需 import addons |
| transmission 玻璃发黑/无折射 | 必须生成 `scene.environment`（PMREM envMap），且背景有可折射内容 |
| 3D 颜色写死（`new Color('#00D4AA')` 后不变） | 读 CSS 变量，换肤时 dispatch 事件同步更新 |
| setTheme/setAccent 只改 DOM，3D 场景不换肤 | 派发 `glass-theme-change`，3D 监听重着色 |
| 每帧重算几何/重分配粒子 | 几何与材质初始化一次，循环里只改 transform/uniform |
| 全屏真玻璃 + 全屏 Bloom + 4K 阴影 | 真玻璃 = 1，彻底不开 Bloom/阴影，pixelRatio 封顶 1.5 |
| 靠堆粒子数量（超千）制造氛围 | 该砍则砍：粒子 ≤ 600，改用**分层异速**（2–3 层不同转速）比堆数量更"活"且不增 draw call |
| 持续 setAnimationLoop 跑纯静态场景 | 静态场景按需渲染，仅交互/换肤时 render 一次 |
| 相机不动只转物体，没有空间感 | 相机视差/推进/呼吸/微摆是立体感主源，优先投预算到相机 |
| canvas 挡住 DOM 交互（按钮点不到） | canvas `pointer-events:none`，仅交互展区局部开启 OrbitControls |
| 用 2.5D 的 CSS perspective 冒充 3D | 要"立体动效"就用真 WebGL 3D；本 skill 的全部价值在于真 3D |
