# design_skills

> 前端设计 skill 的基准测试（benchmark）工作区 —— 用一道密封测试题，让多个前端设计 skill 在**完全相同**的条件下独立产出，再由主 agent 统一收集、整合、打分排名。

公平对比的三大支柱：**密封题 + 隔离环境 + 同技术栈**。

---

## ⚡ 性能测试台（`benchmark-temp/`，本地生成，已 gitignore）

除"对比哪个 skill 设计更好"外，本仓库还自带一个**性能归因测试台**，用来回答「这套设计系统的资源到底消耗在哪里」。

- 基于 `ultra-design-pro` 的完整可运行工程（同技术栈：Next.js + Tailwind + Motion + Zustand），把设计系统拆成**多个独立维度**，每页只开一个变量；
- 先在「空页面」记下基线（FPS / 内存 / GPU 进程），再逐页做差值，定位是哪一层在拖垮帧。

| 维度页 | 隔离测量的成本来源 | 怎么压测 |
|--------|------------------|----------|
| `/bench/empty` | React 水合 + 主题桥接（零点基线） | 记下基线 FPS / GPU |
| `/bench/starfield` | Canvas 2D 烘焙静态星点 | 拉高星点数看 canvas 合成与显存 |
| `/bench/aura` | 大半径模糊球漂移（`filter:blur`） | 复制球组倍率看 GPU 模糊/合成层 |
| `/bench/meteors` | DOM/CSS 流星 + drop-shadow | 拉高条数看合成层上限 |
| `/bench/stardust` | rAF 粒子循环 + 样式提交 | 拉高池上限 + 鼠标划动看主线程 |
| `/bench/glass` | `backdrop-filter` 玻璃矩阵 | 叠加面板数看 GPU 折射重算 |
| `/bench/motion` | framer-motion 无限动画矩阵 | 拉高元素数看每帧样式提交 |
| `/bench/full` | 全部层同时运行（最坏情况） | 总倍率整体加压，对比单维度定位瓶颈 |

```bash
cd benchmark-temp && npm run start   # http://localhost:3114/
```

> `benchmark-temp/` 已在 `.gitignore` 中忽略（含 `node_modules`，每次本地生成）。

---

## 这是什么

市面上有大量"前端设计"类 skill（`ultra-design`、`frontend-design`、`ui-ux-pro-max` ……），但缺少一个**公平、可复查**的方式去回答「到底哪个 skill 设计得更好」。本仓库提供一套标准化流程来做这件事：

1. 固定一道**密封**的综合型 Dashboard 测试题（`benchmark-task.md`），题目里明确锁死技术栈与交付形式。
2. 为每个待测 skill 启动一个**互不知晓彼此存在**的隔离 subagent，各自独立产出完整可运行工程。
3. 主 agent 真实启动每个工程、抓首屏截图、实际浏览操作，再按 **8 个维度**逐项打分并排名。
4. 产出整合审查页（`index.html`）与评分报告（`report.md`），支持一键跳转横向对比。

> 整个流程由本仓库自带 skill **`design-benchmark`** 驱动（见 [`.claude/skills/design-benchmark/SKILL.md`](.claude/skills/design-benchmark/SKILL.md)）。

> 另有一个互补 skill **`design-perf-benchmark`**（见 [`.claude/skills/design-perf-benchmark/SKILL.md`](.claude/skills/design-perf-benchmark/SKILL.md)）：前者回答「多个 skill 谁设计得更好」（多 skill 同题设计对比），后者回答「同一套设计语言内部，哪个特性最吃资源」（单 skill 性能因子拆解 → 单因子隔离页 → 统一 PerfHud → 性能排名）。

---

## 目录结构

```
.
├── .claude/
│   └── skills/                          # 仓库自带 skill
│       ├── create-skill/                # 创建新 skill 的行动手册
│       ├── design-benchmark/   # 多 skill 同题设计对比的驱动 skill（含 benchmark-task.md 密封题）
│       └── design-perf-benchmark/  # 单 skill 性能因子拆解的驱动 skill（拆特性→单因子隔离页→统一 PerfHud→排名）
├── .agents/skills -> ../.claude/skills  # 软链，兼容 agents 工具
├── skills_for_benchmark/                # 待测 skill 源文件（本地文件型，subagent 直接 Read）
│   ├── ultra-design/                    # Liquid Glass 设计系统（弱 blur(4px) + 静态 feTurbulence 噪点磨砂层，Motion 驱动，七维动效体系，双维度正交主题）
│   ├── ultra-design-pro/                # 纯 2D 液态玻璃（流星夜空 + 可调玻璃透明度 + 鼠标星尘/点击爆裂交互 + 弱 blur+噪点磨砂默认/效果优先开关；零 3D 依赖）
│   ├── ui-ux-pro-max/
│   └── web-design-guidelines/
├── benchmark-run/                       # ⚙️ 运行产物（已 gitignore，本地生成，不入库）
│   ├── results/<skill>/                 #   各 skill 的完整可运行工程 + _preview.png 首屏截图
│   ├── index.html                       #   整合审查页（一键跳转横向对比）
│   ├── report.md                        #   评分报告（排名 + 逐维度得分 + 总评）
│   └── run-meta.json                    #   本次运行元信息（skill 列表 / 时间 / 端口 / 分数）
├── benchmark-for-ultra-design/          # 🧪 ultra-design 性能因子测试套件（单 Next.js 工程多路由 + 实时 PerfHud）
│                                        #   做单 skill 内部的性能归因：把特性拆成 11 个互不重叠的因子（含基线空页 + blur 反例），
│                                        #   每页隔离一个变量，总览页 `/`（:3201）跳转各因子页，对照 HUD 读数定位资源消耗
├── CLAUDE.md                            # AI 助手的项目指令
├── AGENTS.md -> CLAUDE.md               # 软链，供其他 agent 工具读取
└── README.md                            # 本文件
```

### 待测 skill 的两种来源

| 类型 | 说明 | 示例 |
|------|------|------|
| **本地文件型** | 仓库内有 `SKILL.md`，subagent 直接 `Read` 加载 | `skills_for_benchmark/` 下的 4 个 |
| **插件 / 全局型** | 由 plugin 提供，subagent 用 Skill 工具按全名调用 | `frontend-design:frontend-design` |

---

## 最新排名（最近一次运行）

> 数据来源：`benchmark-run/run-meta.json` 与 `benchmark-run/report.md`。当前 5 个参评 skill（4 本地 + 1 插件）均通过启动验证（`npm run dev` 零配置启动、首屏 HTTP 200、无 console error）。

| 排名 | Skill | 加权总分 | 可运行 | 来源 |
|------|-------|:-------:|:------:|------|
| 🥇 1 | **ultra-design**（弱 blur + 噪点磨砂） | **9.0 / 10** | ✅ | 本地 `skills_for_benchmark/ultra-design/SKILL.md` |
| 🥇 1 | **ultra-design-pro**（纯 2D · 流星夜空液态玻璃 · 可调透明度 · 弱 blur+噪点磨砂默认/效果优先开关） | **9.0 / 10** | ✅ | 本地 `skills_for_benchmark/ultra-design-pro/SKILL.md` |
| 🥉 3 | **ui-ux-pro-max** | 8.5 / 10 | ✅ | 本地 |
| 3 | **web-design-guidelines** | 8.5 / 10 | ✅ | 本地 |
| 3 | **frontend-design**（插件对照） | 8.5 / 10 | ✅ | 插件 `frontend-design:frontend-design` |

**评分维度（共 8 项，按权重加权）**：视觉冲击力 15% · 设计系统 15% · 动效体系 20% · 组件完成度 15% · 材质与质感 10% · 响应式与适配 10% · 可运行性与工程化 10% · 无障碍与细节 5%。

> `ultra-design`（Liquid Glass 基准范本）与 `ultra-design-pro`（流星夜空液态玻璃进阶变体）设计取向互补、并列第一。完整逐维度得分与每个 skill 的总评，见 [`benchmark-run/report.md`](benchmark-run/report.md)。

> 完整逐维度得分与每个 skill 的总评，见 [`benchmark-run/report.md`](benchmark-run/report.md)。

---

## 如何运行基准测试

通过自带 skill 驱动，最简方式：

```
/design-benchmark
```

或在对话中描述：「重新跑一次前端设计 skill 对比测试」。流程会：

1. 在 `benchmark-run/results/` 下为每个待测 skill 建好隔离产物目录；
2. **同一轮消息内并发**为每个 skill 启动一个 subagent（互不知晓彼此、以为是真实交付），各自产出完整可运行工程；
3. 逐个真实启动工程（`npm install && npm run dev` / `next start`）、抓首屏截图、记录端口；
4. 生成整合审查页 `benchmark-run/index.html` 与评分报告 `benchmark-run/report.md`；
5. 用本地 http server 暴露 `benchmark-run/`，给出统一审查入口。

### 想换题 / 换技术栈 / 增删待测 skill

- **换题或换技术栈**：直接编辑密封题 [`.claude/skills/design-benchmark/benchmark-task.md`](.claude/skills/design-benchmark/benchmark-task.md)，下次运行自动生效。
- **增删待测 skill**：在 `skills_for_benchmark/` 增删目录，并同步 `run-meta.json` 与本 README 的排名表（详见 `CLAUDE.md` 中的「更新 README.md」要求）。

---

## 技术栈基线

所有待测 skill 在同一道密封题下统一使用（七项缺一不可、不得替换，以保证对比公平）：

**TypeScript · React · Next.js (App Router) · Tailwind CSS · shadcn/ui · Radix · Motion**

---

## 查看结果

```bash
cd benchmark-run && python3 -m http.server 8765
# 浏览器打开 http://localhost:8765/ → 整合审查页（可一键跳转各 skill 运行页 / 源码目录）
```

> `benchmark-run/` 与 `.playwright-mcp/` 已在 `.gitignore` 中忽略：产物体积大且含大量 `node_modules`，每次运行为一次性本地生成。
