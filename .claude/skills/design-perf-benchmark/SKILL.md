---
name: design-perf-benchmark
description: 当需要定位某个前端设计 skill（如 ultra-design、frontend-design 等）产出页面「到底哪个特性吃资源」时使用。把该 skill 的视觉/动效特性拆成互不重叠的性能因子（含一个纯框架基线空页），为每个因子创建一个只隔离单一变量的测试页，统一注入同一个 PerfHud（FPS/min-FPS/JS Heap/DOM 节点/Long Task/CLS），并生成总览页（一键跳转）与性能排名报告（逐因子指标 + 单项/综合最优）。
---

# Design Perf Benchmark（前端设计性能因子拆解测试）

## 概述

**核心原则**：选一个前端设计 skill，把它声明的「视觉/动效特性」拆成互不重叠的**性能因子**（含一个纯框架基线空页 + 可选反例对照），每个因子做成一个**只隔离单一变量**的测试页，统一注入**同一个 PerfHud**（FPS / min-FPS / JS Heap / DOM 节点 / Long Task / CLS），人工对照读数 + 排名报告，定位「是什么消耗的资源」。

> 这是性能归因测试，不是设计打分，也不是功能开发。**同一待测 skill + 同一 PerfHud 注入 + 单因子隔离 + 相同数量基准**是公平可比的四大支柱，缺一不可。与仓库内 `design-benchmark`（多 skill 同题设计对比）互补：那是回答「谁设计得更好」，这是回答「同一套设计语言内部，哪个特性最贵」。

---

## 0. 输入：确定待测 skill 与测试主题

执行前先确定**本次拆解哪一个 skill**。

| 待测 skill | 加载方式 |
|-----------|---------|
| ultra-design | 本地文件：`skills_for_benchmark/ultra-design/SKILL.md`（主 agent Read，提取其特性清单） |
| 其它本地 skill | `find . -name SKILL.md -path "*skills_for_benchmark*"` 找到后 Read |
| 插件型 skill | 主 agent 用 Skill 工具按全名调用一次，从输出里提取其声明的设计/动效特性 |

**若用户没指定 skill**：默认用 `ultra-design`（仓库内 Liquid Glass 设计语言，特性最丰富、最有拆解价值）。

> 与 `design-benchmark` 的关键区别：那个 skill 让 N 个 skill **各产出一份同题页面**再横向比；本 skill 只取**一个** skill，把它**自身的特性**拆成 N 个单因子页。所以这里**不需要密封题**——题目就是「把这个 skill 的某个特性单独激活成一个页面」。

---

## 执行流程

### 步骤 1 · 准备工作区与目录结构

在仓库根创建一次性运行目录（**每次运行用固定名 `benchmark-perf/`，覆盖上次**；若该名已被占用，加 `-{skill}` 后缀）：

```
benchmark-perf/
├── app/                    # 单一测试工程根（推荐：单 Next.js 多路由）
│   ├── layout.tsx          # 注入 PerfHud 全局 + globals.css（抄待测 skill 的 CSS 变量配方）
│   ├── page.tsx            # 总览页（步骤 5）
│   ├── components/perf-hud.tsx       # 统一注入的 PerfHud（步骤 2 模板，所有因子页共用）
│   ├── components/page-shell.tsx     # 吸顶特性条 + 返回总览
│   ├── components/{glass,aura,...}.tsx  # 抄待测 skill 的组件配方（按需）
│   └── app/<factor>/page.tsx         # 各因子页（步骤 3）
├── report.md               # 性能排名报告（步骤 6）
└── run-meta.json           # 本次运行元信息（skill / 因子列表 / 数量基准 / 时间）
```

产出：`benchmark-perf/app/` 目录就绪，`perf-hud.tsx` 已按步骤 2 模板写入。

### 步骤 2 · 提取待测 skill 的特性清单，映射为性能因子

**这一步是本 skill 的核心**：必须先 `Read` 待测 skill 的 SKILL.md（或 Skill 调用），**原文摘录它声明的设计/动效特性**，再按下表映射成因子。**不得凭印象编造因子**——每个因子都要能在 SKILL.md 里找到出处（章节名 + 关键词），写进 `run-meta.json` 的 `source` 字段。

通用因子骨架（任何 skill 都适用，按待测 skill 实际有的特性取舍/增补）：

| 因子（路由） | 隔离的特性 | 必须出自 SKILL.md 的哪类章节 | 数量基准 |
|--------------|-----------|------------------------------|---------|
| `/baseline` | **纯框架零点**（无视觉无动效，最小 DOM） | ——（固定基线，每个 run 都有） | 最小 |
| `/aura-static` | 静态氛围/背景层 | 「背景层」「氛围」「mesh」「background」 | 1 层 |
| `/aura-animated` | 氛围层 + 常驻漂移 | 同上 + 「漂移」「animation」「drift」 | 1 层带动画 |
| `/glass` | 玻璃/材质面板 × 数量 | 「材质」「玻璃」「glass」「shadow」「sheen」 | ~80 个 |
| `/entrance` | 一次性入场动效 | 「入场」「entrance」「whileInView」「错落」 | ~60 个 |
| `/pointer` | 指针跟随（pointermove） | 「指针」「磁吸」「tilt」「magnetic」「sheen-follow」 | ~24 个 |
| `/loop` | 常驻循环动效 | 「状态」「脉冲」「spinner」「呼吸」「loop」 | ~40 行 |
| `/scroll` | 滚动驱动动效 | 「滚动」「视差」「parallax」「useScroll」 | ~30 块 |
| `/transition` | 过渡 / layoutId | 「过渡」「AnimatePresence」「layoutId」「FLIP」 | ~20 项 |
| `/<该 skill 专属强动效>` | 该 skill 独有的标志性常驻动效 | 在其 SKILL.md 里找出「全页仅 1 处」「常驻主角」之类编排心法 | 按其规则 1 处 |
| `/blur-anti`（**可选反例**） | 故意违反该 skill 铁律的对照（如禁用的 blur/backdrop-filter） | 「铁律」「禁止」「filter:blur」「backdrop-filter」常以「禁止」出现 | ~4 个 |

> **反例页是否生成**：仅当 SKILL.md 明确**禁止**了某类昂贵特性（如 ultra-design 禁 `filter:blur()`/`backdrop-filter`）时才生成 `/blur-anti`，用于证明「被砍掉的开销大头」。若该 skill 没有明确禁令，跳过反例页。

**数量基准（`COUNT`）是公平的关键**：同一因子在不同 run 用相同数量（如 `/glass` 恒 80 个卡片），否则无法横向比较「加多少 vs 开销多少」。把每页的实际 `COUNT` 写进 `run-meta.json`。

产出：`run-meta.json` 的 `factors[]`，每条含 `{no, href, feature, expect, source, count}`。

### 步骤 3 · 注入统一 PerfHud + 编写各因子页

**铁律：所有因子页注入的是同一个 `perf-hud.tsx` 组件**（步骤 2 模板原文照抄），不得为某个因子单独改 HUD。HUD 自身只做 `transform/opacity` + 定时刷新，开销极小，避免观察者效应。

`components/perf-hud.tsx` 模板（'use client'，浮动右上角玻璃面板，可折叠）：

```tsx
'use client';
import { useEffect, useRef, useState } from 'react';

type Stats = { fps: number; minFps: number; heapMB: number | null;
  domNodes: number; longTaskCount: number; longTaskTotalMs: number; cls: number; };

export function PerfHud({ feature }: { feature: string }) {
  const [collapsed, setCollapsed] = useState(false);
  const [stats, setStats] = useState<Stats>({ fps:0, minFps:60, heapMB:null, domNodes:0, longTaskCount:0, longTaskTotalMs:0, cls:0 });
  const frameCount = useRef(0); const lastTs = useRef<number|null>(null);
  const fpsWindow = useRef<number[]>([]); const ltCount = useRef(0);
  const ltTotal = useRef(0); const clsSum = useRef(0);

  useEffect(() => {
    let rafId = 0;
    const tick = (ts:number) => {
      if (lastTs.current != null) {
        const dt = ts - lastTs.current;
        if (dt > 0) { const instant = 1000/dt; fpsWindow.current.push(instant);
          if (fpsWindow.current.length > 60) fpsWindow.current.shift(); }
      }
      lastTs.current = ts; frameCount.current += 1;
      rafId = requestAnimationFrame(tick);
    };
    rafId = requestAnimationFrame(tick);

    const interval = setInterval(() => {
      const samples = fpsWindow.current;
      const avgFps = samples.length ? samples.reduce((a,b)=>a+b,0)/samples.length : 60;
      const minFps = samples.length ? Math.min(...samples) : 60;
      const mem = (performance as Performance & { memory?: { usedJSHeapSize: number } }).memory;
      const heapMB = mem ? mem.usedJSHeapSize/1048576 : null;
      const domNodes = document.getElementsByTagName('*').length;
      setStats({ fps: Math.round(avgFps), minFps: Math.round(minFps),
        heapMB: heapMB!=null ? Math.round(heapMB) : null, domNodes,
        longTaskCount: ltCount.current, longTaskTotalMs: Math.round(ltTotal.current),
        cls: Math.round(clsSum.current*1000)/1000 });
    }, 500);

    let ltObs: PerformanceObserver | null = null;
    try { ltObs = new PerformanceObserver((l)=>{ for(const e of l.getEntries()){ ltCount.current+=1; ltTotal.current+=e.duration; } });
      ltObs.observe({ entryTypes:['longtask'] }); } catch {}
    let lsObs: PerformanceObserver | null = null;
    try { lsObs = new PerformanceObserver((l)=>{ for(const e of l.getEntries() as (PerformanceEntry & {hadRecentInput?:boolean; value?:number})[]){ if(e.hadRecentInput) continue; if(typeof e.value==='number') clsSum.current+=e.value; } });
      lsObs.observe({ entryTypes:['layout-shift'] }); } catch {}
    return () => { cancelAnimationFrame(rafId); clearInterval(interval); ltObs?.disconnect(); lsObs?.disconnect(); };
  }, []);

  const fpsColor = stats.fps>=50 ? 'var(--accent)' : stats.fps>=30 ? '#fbbf24' : '#ef4444';
  const minColor = stats.minFps>=50 ? 'var(--accent)' : stats.minFps>=30 ? '#fbbf24' : '#ef4444';
  const theme = typeof document!=='undefined' ? document.documentElement.getAttribute('data-theme') ?? 'dark' : 'dark';
  return (
    <div style={{ position:'fixed', top:16, right:16, zIndex:9999, fontFamily:'var(--font-mono)', fontSize:12, color:'var(--text-primary)' }}>
      <div className="glass glass-sheen" style={{ borderRadius:16, padding: collapsed?'10px 14px':'14px 16px', minWidth: collapsed?0:232 }}>
        <div style={{ display:'flex', alignItems:'center', justifyContent:'space-between', gap:12 }}>
          <span style={{ fontWeight:700, letterSpacing:'0.04em', color:'var(--accent)' }}>PERF HUD</span>
          <button onClick={()=>setCollapsed(c=>!c)} aria-label={collapsed?'展开':'折叠'}
            style={{ background:'transparent', border:'1px solid var(--glass-border)', color:'var(--text-secondary)', borderRadius:8, padding:'2px 8px', cursor:'pointer', fontSize:11 }}>{collapsed?'▾':'▴'}</button>
        </div>
        {!collapsed && (
          <div style={{ marginTop:10, display:'flex', flexDirection:'column', gap:6 }}>
            <Row label="因子" value={feature} />
            <Row label="主题" value={theme} valueColor="var(--text-tertiary)" />
            <div style={{ height:1, background:'var(--glass-border)', margin:'2px 0' }} />
            <Row label="FPS" value={String(stats.fps)} valueColor={fpsColor} big />
            <Row label="FPS(min60)" value={String(stats.minFps)} valueColor={minColor} />
            <Row label="JS Heap" value={stats.heapMB!=null?`${stats.heapMB} MB`:'N/A'} />
            <Row label="DOM 节点" value={stats.domNodes.toLocaleString()} />
            <Row label="Long Task" value={`${stats.longTaskCount} · ${stats.longTaskTotalMs}ms`} valueColor={stats.longTaskCount>0?'#fbbf24':'var(--text-secondary)'} />
            <Row label="CLS" value={String(stats.cls)} />
            <div style={{ height:1, background:'var(--glass-border)', margin:'2px 0' }} />
            <div style={{ color:'var(--text-tertiary)', fontSize:10.5, lineHeight:1.5 }}>合成层/绘制请用<br/>DevTools → More tools → Layers</div>
          </div>
        )}
      </div>
    </div>
  );
}
function Row({label,value,valueColor,big}:{label:string;value:string;valueColor?:string;big?:boolean}) {
  return (<div style={{ display:'flex', justifyContent:'space-between', alignItems:'baseline', gap:12 }}>
    <span style={{ color:'var(--text-tertiary)' }}>{label}</span>
    <span className="tabular-nums" style={{ color:valueColor??'var(--text-primary)', fontWeight:big?700:500, fontSize:big?16:12 }}>{value}</span>
  </div>);
}
```

各因子页统一骨架（差异只在主内容里激活哪个特性）：

```tsx
import { PageShell, PageContainer } from '@/components/page-shell';
import { PerfHud } from '@/components/perf-hud';
// 按需引入 aura / glass / motion 原语（抄待测 skill 的配方）

export default function XxxPage() {
  return (
    <PageShell feature="<因子名>" expect="<预期高开销点>">
      <PerfHud feature="<编号 因子名>" />
      {/* 按数量基准 COUNT 渲染只激活该特性的内容 */}
    </PageShell>
  );
}
```

**`globals.css` 必须照搬待测 skill 的 CSS 变量配方**（明暗基础层 + 色相层 + 派生色 + 玻璃/aura 类 + 缓动曲线），保证测的是该 skill 的真实材质/动效，而不是主 agent 即兴发挥。这些配方直接从其 SKILL.md 的代码块复制。

产出：`app/` 下 N 个因子页 + 1 个 `perf-hud.tsx`，全部 `npm install && next build` 零错误。

### 步骤 4 · 验证可运行性 + 人工实测读数

1. `cd benchmark-perf/app && npm install && npm run build && npm run start -- -p <port>`，确认所有因子路由 HTTP 200、无 console error、无 favicon 404（自带 `src/app/icon.svg`）。
2. **人工（主 agent 用 Playwright 或用户手动）逐页打开并实测**——这是性能测试的灵魂，禁止只看代码臆测：
   - `/baseline` 记下零点 FPS / Heap / DOM。
   - `/pointer`：移动鼠标横扫卡片若干秒，记 **min FPS** 与 **Long Task 累计**。
   - `/scroll`：从顶滚到底再回顶，记 min FPS 与 long task。
   - `/loop`、`/aura-animated`、`/<专属常驻>`：静置 10s，记稳态 FPS。
   - `/glass`、`/entrance`：首屏加载完即记 DOM 节点数与 Heap 峰值。
   - `/blur-anti`（若存在）：与 `/aura-animated` 对比，看 FPS 差距——证明被砍掉的开销。
3. 把每页的实测读数记进 `run-meta.json` 的 `factors[].measured`（每条至少 `fps / minFps / heapMB / domNodes / longTaskCount / longTaskTotalMs`）。

> **浏览器限制**：JS Heap（`performance.memory`）与 Long Task 仅 **Chromium 内核**支持；Firefox/Safari 仍可读 FPS/DOM/CLS。实测时务必用 Chrome。

### 步骤 5 · 生成总览页（一键跳转管理）

`app/page.tsx` = 单文件总览页：

- 顶部：标题「<skill> 性能因子拆解」+ 一句话说明 + meta chip（技术栈 / 因子数 / 端口）。
- 「如何分析」说明卡：3 步法（baseline 零点对照 → 交互页施压 → DevTools Layers 细归因）。
- **因子卡片网格**：每张 = 编号 + 因子名 + 隔离特性 + 预期高开销点 + 数量基准 + 该 skill 声明的出处章节 + 「▶ 打开 /xxx」按钮（`<a href="/xxx" target="_blank" rel="noopener">`，同源相对路径，新标签）。
- 卡片风格抄待测 skill 的 token（玻璃 tint + 投影），但**卡片层不加 backdrop-filter**（工具页别喧宾夺主，也避免给自身引入被测开销）。
- 底部：链接到 `skills_for_benchmark/<skill>/SKILL.md`。

产出：`app/page.tsx`。

### 步骤 6 · 主 agent 生成性能排名报告（report.md）

主 agent 把步骤 4 实测读数整理成排名。**不做主观设计打分**，只做客观指标排名。

`report.md` 结构模板：

```markdown
# <skill> 性能因子拆解 · 报告

> 待测 skill：<skill> ｜ 因子数：N ｜ 运行时间：[日期] ｜ 浏览器：Chrome [版本] ｜ 端口：<port>
> 注：FPS/Heap/Long Task 在 Chromium 内核下采集；min FPS 为最近 60 帧窗口最低值。

## 一、逐因子指标实测表

| 因子 | COUNT | FPS | min-FPS | JS Heap(MB) | DOM 节点 | Long Task(次·ms) | CLS |
|------|:-----:|:---:|:-------:|:-----------:|:--------:|:----------------:|:---:|
| ① 基线 | - | 60 | 60 | 38 | 42 | 0·0 | 0.00 |
| ⑥ 指针 | 24 | 42 | 28 | 56 | 312 | 14·1280 | 0.00 |
| ⑪ blur 反例 | 4 | 31 | 19 | 61 | 88 | 22·3100 | 0.02 |
| ... | | | | | | | |

## 二、相对基线的开销排名（min-FPS 越低 / Long Task 越多 = 越贵）

| 排名 | 因子 | 相对基线 FPS 掉幅 | Long Task 总时长 | 结论 |
|------|------|:----------------:|:----------------:|------|
| 🥇 最贵 | ⑪ blur 反例 | -29 FPS | 3100ms | 验证 skill 禁 blur 的正确性 |
| 🥈 | ⑥ 指针 | -18 FPS | 1280ms | pointermove→useSpring 是主要掉帧源 |
| ... | | | | |

## 三、单项最优 / 综合提示

- **最低开销**：② 静态氛围（min-FPS 60，几乎无开销）—— skill 用单层 mesh 替代 blur 球的取舍成立。
- **最贵特性**：<因子> —— 建议 <温和优化方向，不删视觉>。
- **反例对照**（若有）：/blur-anti 比 /aura-animated 多掉 <n> FPS，量化「被砍掉的 blur 开销」。

## 四、温和优化建议（不删视觉与动效）

针对最贵因子，按该 skill 自身声明的优化心法（合成层 / useSpring 节流 / 常驻光效仅 1 处等）给出 2–3 条，**不建议牺牲视觉质感**。
```

**排名客观性要求**：每条结论配一个**实测数字依据**（如「min-FPS 28」），杜绝「感觉很卡」之类无依据判断；相对基线的开销用差值表达（与 baseline 的 FPS/Heap/longtask 之差）。

### 步骤 7 · 收尾：启动 + 汇报

1. `npm run start -- -p <port>` 暴露总览页，把 `http://localhost:<port>/` 给用户。
2. 对话里**简要汇报**：待测 skill、拆出几个因子、最贵的前三、反例对照的差距、总览页与报告地址。
3. 提示用户：并排开 `/baseline` 与最贵因子页对照各自 PerfHud 即可秒级归因。

---

## 判断规则

### 公平性边界

| 场景 | 正确做法 |
|------|---------|
| 待测 skill 没声明氛围层/玻璃/动效等某类特性 | 该因子页跳过；不硬造 skill 没有的特性 |
| 某 skill 的动效配方无法用 CSS 复刻（如依赖 Motion 的 useSpring） | 必须用该 skill 指定的库（Motion）真实复刻，不得用 CSS animation 近似——否则测的不是 skill 行为 |
| 数量基准拿捏不准 | 默认用「足以读出差异」的量：玻璃卡 ~80、入场 ~60、指针 ~24、常驻循环 ~40 行、滚动 ~30 块；可调但同一 run 内固定 |
| 用户想加因子 | 在步骤 2 的 factors[] 增一条（必须能在 SKILL.md 找出处），补一个因子页，重跑步骤 3–6 |
| 用户想换待测 skill | 改步骤 0 的输入，重跑；旧 run 可保留为 `benchmark-perf-<old-skill>/` |
| 测试工程跑不起来 | 停下，把 build/start 报错汇总给用户，先解决工程问题再跑——跑不起来的性能测试无意义 |

### 指标客观性

| 要求 | 说明 |
|------|------|
| 同一 HUD 注入所有因子页 | 步骤 2 的 `perf-hud.tsx` 原文照抄，禁止为单页改 HUD，否则读数不可比 |
| 实测优先于代码臆测 | 步骤 4 必须真实打开页面施压（指针页移动鼠标、滚动页滚动、常驻页静置 10s），记录读数 |
| Chromium 限制要注明 | JS Heap / Long Task 仅 Chrome 支持；报告头部注明浏览器，避免跨浏览器误读 |
| 反例页仅当 skill 明确禁止某特性时生成 | 若 skill 没禁令，不要臆造「反例」 |

---

## 常见误区

| 误区 | 正确做法 |
|------|---------|
| 因子是主 agent 凭印象编的（如「加个粒子效果」） | 每个因子必须能在待测 skill 的 SKILL.md 里找到出处（章节 + 关键词），写进 run-meta.json 的 source |
| 给不同因子页用不同 HUD | 所有页面共用步骤 2 的同一个 perf-hud.tsx，否则读数不可比 |
| 数量基准随性定（/glass 放 10 个、/pointer 放 200 个） | 同一 run 内每个因子用固定数量基准并记进 run-meta.json；跨 run 也要尽量一致 |
| 只跑 build 不实测、照代码估开销 | 性能必须实测（施压 + 读 HUD），代码臆测无意义；指针/滚动/常驻页尤其要真实操作 |
| 用 Firefox/Safari 跑然后怪 Heap 读不到 | 报告头部注明必须用 Chrome；Heap/Long Task 是 Chromium 专属 API |
| 把性能测试做成设计打分 | 本 skill 只排客观性能指标（FPS/内存/longtask/CLS），不做主观视觉打分；主观打分留给 design-benchmark |
| 建议「为了性能删掉玻璃/动效」 | 该 skill 通常已声明「视觉质感优先、只做温和优化」；优化建议必须顺着其自身心法（合成层/useSpring 节流/常驻光效限 1 处），不牺牲视觉 |
| 总览页自己用了 backdrop-filter | 工具页别引入被测开销，卡片用半透 tint + 投影即可 |

---

## 示例：一次最小运行（待测 = ultra-design）

1. Read `skills_for_benchmark/ultra-design/SKILL.md`，摘出特性：氛围 mesh、晶体玻璃、七大维度 Motion 动效、常驻 sheen-sweep、明确禁 `filter:blur()`/`backdrop-filter`。
2. 映射 11 个因子：baseline / aura-static / aura-animated / glass(×80) / entrance(×60) / pointer(×24) / loop(×40) / scroll(×30) / transition(×20) / sheen-sweep（专属常驻）/ blur-anti（反例，因有禁令）。
3. 建 `benchmark-perf/app/`，照抄 ultra-design 的 globals.css（明暗基础层 + 7 色相层 + glass/aura 类 + 缓动曲线），写 perf-hud.tsx + 11 个因子页 + 总览页。
4. `npm install && next build && next start -p 3201`，逐页实测：baseline 60FPS / pointer 移鼠标 min-FPS 28 / blur-anti min-FPS 19。
5. 总览页 `/` 11 张卡片可跳转。
6. 写 report.md：逐因子表 + 相对基线开销排名（blur-anti 最贵、pointer 次之）+ 温和优化建议。
7. 把 `http://localhost:3201/` 与报告路径交给用户，对话报最贵前三。
