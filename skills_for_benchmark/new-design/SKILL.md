---
name: new-design
description: 当需要构建生产级、极具设计感且工程严谨的前端界面（Dashboard、看板、数据应用、落地页、Web App）时使用。这是一套「集大成稳健派」设计系统，目标是每个维度都做到无短板（综合 9.0+）。核心设计语言 = Aurora 极光液态玻璃（青→翡翠绿渐变 + 动态边缘镜面高光 + 微噪点）。输出四样硬通货：(1) 完整动效体系（入场/加载/指针/状态/滚动/过渡/灵动七类，只动 transform/opacity/filter 合成层，全程 60fps，内置 prefers-reduced-motion 兜底）；(2) 严格双自洽设计 token（原始色板 + 语义层 + 完整字号/间距/圆角/阴影/模糊层级，数据数字强制 tabular-nums）；(3) 最高完成度（8 模块全部真实落地、所有交互元素 5 态齐全 default/hover/active/focus/disabled、真实自洽 mock 数据）；(4) 顶级工程化与无障碍（TypeScript + Vite + Tailwind + Radix + Motion，完整 ARIA + 语义化 + 焦点可见 + 键盘可达 + 对比度达标，零 console error，零配置启动）。承诺产出可一键运行的工程，告别通用 AI 审美。
---

# New Design — Aurora 极光液态玻璃 · 集大成设计系统

> **一句话定位**：把所有第一梯队的单项第一融成一个没有短板的方案 —— ultra-design 的液态玻璃 + 动效体系、ui-ux-pro-max 的组件完成度、web-design-guidelines 的设计系统/无障碍/工程化、frontend-design 的衬线品牌记忆点、industrial-brutalist 的视觉辨识度。目标是**八个维度每个都 ≥ 8.5，综合 9.0+**。

---

## 一、设计三原则（最高指导，不可违背）

1. **每个维度都不能是短板。** 视觉、设计系统、动效、完成度、材质、响应式、工程化、无障碍 —— 这八项里**任何一项低于 8.5 都视为失败**。先保证下限，再拔高上限。绝不为"炫技"牺牲可访问性，绝不为"极简"牺牲完成度，绝不为"安全"牺牲视觉辨识度。
2. **动效是必需品，但只动合成层。** 页面必须有一套**完整且成体系**的动效，覆盖七大场景（见第五节），一个不少。**流畅不靠删减动效，而靠让所有动效只作用于 `transform` / `opacity` / `filter`（合成层），绝不动画化 `box-shadow` / `width` / `height` / `top` / `margin`。** 昂贵的常驻装饰（氛围色斑持续漂移、多层 blur 叠加）该砍则砍，但功能性/体验性动效一刀不减。
3. **真实落地，禁止占位。** 8 个模块全部真实实现，数据全部 mock 但真实可信、数值自洽（同比环比方向正确、进度条与数字一致、时间戳合理）。**严禁 TODO、严禁 lorem ipsum、严禁空状态伪装成完成。**

---

## 二、美学方向：Aurora 极光液态玻璃

### 2.1 视觉关键词
极光（Aurora）· 液态玻璃（Liquid Glass）· 动态镜面边缘高光 · 翡翠青绿 · 衬线大标题 · 数据等宽 · 微噪点质感

### 2.2 品牌色（Aurora Teal → Emerald 渐变体系）
**主色 Aurora Teal `#06B6D4` → Emerald `#10B981` → Mint `#34D399`**（青→翡翠绿→薄荷，呼应"Aurora 极光"的产品名，比通用紫蓝渐变更具辨识度）。
- 主色 primary：`#06B6D4`（Aurora Cyan）
- 主色深 primary-strong：`#0891B2`
- 主色亮 primary-soft：`#67E8F9`
- 辅助 accent：`#10B981`（Emerald，用于正向/上升）
- **避免**使用 `indigo→violet→pink`、`purple→blue` 这类已被 AI 滥用的通用渐变作为主色方向。可在极光氛围光斑里**微量**点缀 `#8B5CF6`（紫）、`#F472B6`（粉）作为远景光晕，但它们永远不主导。

### 2.3 语义色体系（必须完整且对比度达标）
- success / up：`#10B981`
- warning：`#F59E0B`
- danger / critical / down：`#EF4444`
- info：`#3B82F6`
- 三级告警配色：critical=danger、warning=warning、info=info，**告警卡片要有明确的左/顶部色条 + 图标**，不能只靠颜色区分。

### 2.4 字体（三档，含一个衬线记忆点）
- **大标题（display/h1）**：`"Fraunces"` 或 `"Instrument Serif"` —— 衬线，带品牌气质与记忆点（差异化于纯无衬线方案），用于 Hero 大标题与关键数字。
- **正文/UI（sans）**：`"Plus Jakarta Sans"` 或 `"Inter"` —— 无衬线，现代。
- **数据/代码（mono）**：`"JetBrains Mono"` —— 等宽，用于 KPI 数字、时间戳、代码、图表坐标轴数字。
- **所有数据数字强制 `font-variant-numeric: tabular-nums`**（数字滚动/刷新时不抖动），通过一个 `.tabular` 工具类或直接给数字元素应用。
- 字号层级：display 48/40、h1 32、h2 22、h3 18、body 15、small 13、caption 11，**严格按 token，不散落硬编码**。
- 行高与字重也要分层：标题更紧（line-height 1.1–1.2、weight 600–700），正文更松（line-height 1.6、weight 400–500）。

---

## 三、设计 Token 体系（双自洽：原始层 + 语义层）

**必须有两层 token，不可只有一层硬编码。**

### 3.1 原始色板（raw）
```
--c-cyan-50 … --c-cyan-950
--c-emerald-50 … --c-emerald-950
--c-gray-50 … --c-gray-950（中性，暗色用 gray-900 为底，亮色用 white/gray-50）
```

### 3.2 语义层（semantic，亮/暗各一套）
```
--background, --foreground
--card, --card-foreground
--border, --border-strong
--primary, --primary-foreground, --primary-strong
--muted, --muted-foreground
--success / --warning / --danger / --info (+ 对应 -foreground)
--ring（focus ring 用）
```
**语义层引用原始层**，组件里**只引用语义层**，严禁组件里直接写 `#06B6D4`。这样换主题只改语义层映射。

### 3.3 其它 token
- 圆角：`--radius-sm 8 / --radius 12 / --radius-lg 16 / --radius-xl 24 / --radius-full 9999`
- 间距：基于 4px 基线（4/8/12/16/20/24/32/40/48/64）
- 阴影层级（三档）：`--shadow-sm / --shadow / --shadow-lg`，外加玻璃专属 `--shadow-glass`（带半透明投影 + 细内高光）
- 模糊：`--blur-glass 16px`（玻璃），`--blur-strong 24px`

> 用 Tailwind v3/v4 的 `theme.extend` 或 CSS variables 统一承载，不要散落到各组件。

---

## 四、材质：液态玻璃（Liquid Glass）

### 4.1 玻璃卡片标准
- `background: color-mix(in srgb, var(--card) 70%, transparent)` 或 `rgba(...)`
- `backdrop-filter: blur(var(--blur-glass)) saturate(180%)`
- **1px 半透明边框**：`border: 1px solid color-mix(in srgb, var(--foreground) 8%, transparent)`
- **动态边缘镜面高光**：用 `::before` 伪元素 + `linear-gradient` 从左上到右下的 1px 高光线（`rgba(255,255,255,.18)` → transparent），模拟玻璃边缘反光；hover 时高光线**流动**（background-position 动画，只动 background-position 走合成层可行，或用 transform 平移一个高光层）。
- 细内阴影顶部高光：`box-shadow: inset 0 1px 0 0 rgba(255,255,255,.12)`（**inset shadow 静态可用，但绝不做动画**）。

### 4.2 极光氛围背景
- 页面底层一个固定的 `fixed` 极光光斑层：2–3 个 `radial-gradient` 色斑（cyan / emerald / 微量 violet），`filter: blur(80px–120px)`，`opacity` 低（0.3–0.5）。
- **这些光斑不要做常驻漂移动画**（性能杀手，且与"该砍则砍"原则冲突）。它们是静态氛围，让玻璃卡片透出极光即可。给极光层一个极慢（20s+）的 `opacity` 呼吸是可接受的，但不要位移。
- 加一层 SVG `feTurbulence` 微噪点（`opacity 0.03`，`mix-blend-mode: overlay`）增加材质颗粒感，**静态，不动画**。

---

## 五、完整动效体系（七类，全部实现，只动合成层）

> **铁律**：所有动效只动 `transform` / `opacity` / `filter`。`box-shadow` / `width` / `height` / `top` / `margin` 一律不动。告警脉冲/呼吸用 `filter: drop-shadow` 或 `opacity`，不用 `box-shadow` 动画。

使用 **Motion（`motion/react`）**。所有动效包裹 `prefers-reduced-motion` 兜底（`useReducedMotion` 或媒体查询，开启时降级为瞬时）。

1. **入场动效**：页面/模块**错落浮现**（stagger，每个元素延迟 60–80ms，`opacity 0→1` + `translateY(12px→0)`，duration 0.5s，ease `[0.22,1,0.36,1]`）。用 `whileInView` + `viewport={{ once: true }}`。
2. **加载/数字动效**：KPI 大数字**滚动计数**（`useMotionValue` + `animate` 从 0 到目标值，tabular-nums 保证不抖）；图表用**骨架/流光扫过**（一个 `linear-gradient` 高光带用 `transform: translateX` 横扫）；按钮 loading 用**三点跳动**或**旋转环**。
3. **指针动效**：卡片 `hover` **浮起**（`translateY(-3px)` + `filter` 提亮，**不用 box-shadow 动画**，浮起感靠 transform + 静态阴影档切换 via opacity）；按钮 **涟漪**（`::after` 圆形用 `transform: scale` 扩散 + `opacity` 淡出）；可加**光泽跟随**（mousemove 更新一个 radial-gradient 的 CSS 变量，只改 background，不触发重排——谨慎，确认只动合成层）。
4. **状态常驻动效**：在线光点 **呼吸**（`opacity 0.5↔1` + `transform: scale`）；告警 critical 卡片 **脉冲**（`filter: drop-shadow` 红光 opacity 呼吸 1s 周期）；状态徽章 online **光晕**。这些**只动 opacity/filter/transform**。
5. **过渡动效**：Tab / 时间范围（24h/7d/30d/90d）切换用 **crossfade + 轻微位移**（AnimatePresence，`mode="wait"`）；主题切换用 **opacity 淡入淡出**（不做圆形揭露也行，但要有过渡，不能瞬切）。
6. **滚动动效**：长列表/时间线滚动**渐入**；可选顶部**滚动进度条**（`scaleX` 跟随 scrollYProgress）。
7. **灵动点缀**：KPI 同比箭头 hover **弹跳**；图表 hover **tooltip 跟随**（`transform` 定位）；侧栏导航当前项 **高亮指示条用 layoutId 平滑滑动**（Motion `layoutId`）。

---

## 六、组件完成度（对标最高标准）

### 6.1 所有可交互元素必须 5 态齐全
`default / hover / active / focus-visible / disabled` 全部定义。**focus-visible 必须有清晰可见的 ring**（`--ring` 色，2px outline + 2px offset，或 box-shadow ring），这是无障碍硬指标。

### 6.2 图表全部手绘 SVG 或深度定制
**严禁使用 recharts/chart.js 等库的默认丑样式。** 要么手绘 SVG（折线用 `path` + Catmull-Rom 平滑曲线，面积用渐变填充，sparkline 单 path），要么深度定制库的每一条样式（网格线、坐标轴、tooltip、图例、颜色全部按 token 覆盖）。手绘 SVG 在本题是更稳的满分路径。

### 6.3 8 个模块全部真实落地（一个都不能少）
左侧导航、顶部栏、KPI×4、主图表、次级图表、数据表格、活动时间线、告警面板 —— 每个模块的功能点按题目要求**逐项实现**，数据真实可信，状态真实可切。

---

## 七、无障碍（对标顶级，不容打折）

- **语义化标签**：`<nav>` `<header>` `<main>` `<aside>` `<section>` `<article>` `<table>` `<ul>/<li>`，不用 `<div>` 做导航/列表。
- **ARIA**：图标按钮 `aria-label`；通知/告警 `role="status"` / `aria-live`；抽屉/下拉 `aria-expanded` / `aria-controls`；面包屑、tablist 用对应 role。
- **键盘可达**：所有交互元素 `tabindex` 正确，`Enter`/`Space` 触发，下拉/抽屉可 `Esc` 关闭，焦点陷阱。
- **焦点可见**：`:focus-visible` ring 全覆盖，不依赖 `:focus`（避免鼠标点击也留 ring）。
- **对比度**：正文 ≥ 4.5:1，大字 ≥ 3:1。玻璃卡片上的文字注意背景模糊后仍达标。
- **`prefers-reduced-motion`**：开启时所有动效降级为瞬时/极轻。
- **`prefers-color-scheme`**：可据此默认主题（可选）。

---

## 八、响应式（三档断点）

- 桌面 ≥1280：完整布局，侧栏常驻。
- 平板 768–1279：侧栏可折叠为图标态，次级图表从并排变堆叠。
- 移动 <768：侧栏折叠为**抽屉**（汉堡触发），KPI 卡 1 列，图表自堆叠，表格横向滚动或卡片化，顶部栏搜索/通知收纳进 `...` 菜单。
- 用 Tailwind 断点 `sm/md/lg/xl/2xl`，不要写死像素媒体查询散落各处。

---

## 九、工程化（对标顶级）

- **技术栈锁死**：TypeScript + React 18 + Vite 5/6 + Tailwind CSS v3/v4 + Radix UI（无障碍/交互基座）+ Motion（`motion/react`）+ lucide-react（图标，统一体系，不要混用多个图标库）。
- **目录结构清晰**：`src/components`（组件）、`src/lib`（utils/tokens）、`src/data`（mock）、`src/hooks`、`App.tsx`、`main.tsx`。
- **路径别名** `@ -> src`。
- **`npm install && npm run dev` 零配置启动**，首屏 HTTP 200，**零 console error / 零 console warn**。
- **README 只放启动说明**，不写设计自述（公平性铁律：仅输出代码）。
- **mock 数据写在 `src/data`**，禁止调真实 API。
- vite.config 设 `server.strictPort: true` 用固定端口，避免漂移。

---

## 十、8 模块落地清单（逐项对照，缺一不可）

1. **左侧导航栏**：Logo + ≥6 导航项（分组+分隔）+ 当前项高亮（layoutId 滑动指示条）+ 可折叠（折叠态保留图标）+ 底部用户区。
2. **顶部栏**：面包屑/标题 + 全局搜索（⌘K 触发 + 建议下拉）+ 通知铃铛（未读红点/角标 + 下拉面板）+ 主题切换（亮/暗真实生效）+ 用户头像菜单。
3. **KPI ×4**：每张卡大数字（tabular-nums + 滚动计数动效）+ 同比/环比（方向箭头+红绿语义色）+ sparkline + hover 浮起。四张分别为「活跃用户/数据吞吐量/异常事件/平均响应延迟」，其中「异常事件」有**警告态视觉**（danger 色 + 脉冲）。
4. **主图表区**：可切时间范围（24h/7d/30d/90d，crossfade 过渡）的折线/面积图，图例 + hover tooltip + 平滑曲线 + 视觉主次（观测值实线 vs 预测虚线，可选置信带）。
5. **次级图表**：环形/饼图（事件分布）+ 横向条形图/排行榜（Top5 数据源），并排或上下。
6. **数据表格**：状态标签（在线/离线/异常三色）+ 进度条 + 负责人头像 + 最后更新时间，行 hover 高亮，≥6–8 行真实数据。
7. **活动时间线**：垂直时间线 + 时间戳 + 操作类型图标 + 操作人 + "加载更多"占位。
8. **告警/状态面板**：独立卡片/抽屉，列当前告警，三级严重度配色（critical/warning/info）+ "忽略/处理"按钮 + 实时感（脉冲/呼吸光晕状态动效）。

---

## 十一、质量自检清单（产出前逐项过）

- [ ] 8 模块全部真实落地，无 TODO/占位
- [ ] 亮色 + 暗色双主题可切且都对比度达标
- [ ] 动效覆盖七类且只动 transform/opacity/filter，无重排动画
- [ ] 所有动效有 prefers-reduced-motion 兜底
- [ ] 所有交互元素 5 态齐全，focus-visible ring 清晰
- [ ] 数据数字全部 tabular-nums
- [ ] 图表手绘 SVG 或深度定制，无默认丑样式
- [ ] 语义化标签 + 完整 ARIA + 键盘可达
- [ ] 三档响应式断点合理，移动端抽屉可用
- [ ] token 双自洽，组件无硬编码色值
- [ ] `npm install && npm run dev` 零配置启动，零 console error/warn
- [ ] README 仅启动说明，无设计自述
- [ ] 技术栈 = TS + React + Vite + Tailwind + Radix + Motion

> **记住**：你的目标是「没有短板的满分方案」，不是「某一个维度极致其余妥协」。先确保上面每一项都达标（保证下限 8.5），再在视觉辨识度（液态玻璃 + 衬线 + 极光青）与动效体系上拔高上限。每一个维度的单项第一都被你吸收：ultra-design 的材质与动效、ui-ux-pro-max 的完成度、web-design-guidelines 的工程与无障碍、frontend-design 的品牌记忆、industrial-brutalist 的视觉独特性 —— 集于一身。
