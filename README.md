# design_skills

> 前端设计 skill 的基准测试（benchmark）工作区 —— 用一道密封测试题，让多个前端设计 skill 在**完全相同**的条件下独立产出，再由主 agent 统一收集、整合、打分排名。

公平对比的三大支柱：**密封题 + 隔离环境 + 同技术栈**。

---

## 这是什么

市面上有大量"前端设计"类 skill（`ultra-design`、`frontend-design`、`industrial-brutalist-ui` ……），但缺少一个**公平、可复查**的方式去回答「到底哪个 skill 设计得更好」。本仓库提供一套标准化流程来做这件事：

1. 固定一道**密封**的综合型 Dashboard 测试题（`benchmark-task.md`），题目里明确锁死技术栈与交付形式。
2. 为每个待测 skill 启动一个**互不知晓彼此存在**的隔离 subagent，各自独立产出完整可运行工程。
3. 主 agent 真实启动每个工程、抓首屏截图、实际浏览操作，再按 **8 个维度**逐项打分并排名。
4. 产出整合审查页（`index.html`）与评分报告（`report.md`），支持一键跳转横向对比。

> 整个流程由本仓库自带 skill **`frontend-design-benchmark`** 驱动（见 [`.claude/skills/frontend-design-benchmark/SKILL.md`](.claude/skills/frontend-design-benchmark/SKILL.md)）。

---

## 目录结构

```
.
├── .claude/
│   └── skills/                          # 仓库自带 skill
│       ├── create-skill/                # 创建新 skill 的行动手册
│       ├── frontend-design-benchmark/   # 本基准测试流程的驱动 skill（含 benchmark-task.md 密封题）
│       └── ultra-design/                # Liquid Glass 设计系统 skill
├── .agents/skills -> ../.claude/skills  # 软链，兼容 agents 工具
├── skills_for_benchmark/                # 待测 skill 源文件（本地文件型，subagent 直接 Read）
│   ├── ultra-design-v2/
│   ├── ultra-design-3d/                 # 真 3D（裸 Three.js · 该砍则砍，集显友好）
│   ├── ultra-design-3d-v2/              # 真 3D（R3F+Drei · 玻璃彻底 3D + 跟手 tilt：后景彻底无球·暗色宇宙星光，前景全 UI 真玻璃 mesh + 逐件 tilt，视觉优先·主流机流畅）
│   ├── ultra-design/
│   ├── ui-ux-pro-max/
│   ├── web-design-guidelines/
│   ├── high-end-visual-design/
│   ├── industrial-brutalist-ui/
│   ├── design-taste-frontend/
│   └── gpt-taste/
├── benchmark-run/                       # ⚙️ 运行产物（已 gitignore，本地生成，不入库）
│   ├── results/<skill>/                 #   各 skill 的完整可运行工程 + _preview.png 首屏截图
│   ├── index.html                       #   整合审查页（一键跳转横向对比）
│   ├── report.md                        #   评分报告（排名 + 逐维度得分 + 总评）
│   └── run-meta.json                    #   本次运行元信息（skill 列表 / 时间 / 端口 / 分数）
├── CLAUDE.md                            # AI 助手的项目指令
├── AGENTS.md -> CLAUDE.md               # 软链，供其他 agent 工具读取
└── README.md                            # 本文件
```

### 待测 skill 的两种来源

| 类型 | 说明 | 示例 |
|------|------|------|
| **本地文件型** | 仓库内有 `SKILL.md`，subagent 直接 `Read` 加载 | `skills_for_benchmark/` 下的 8 个 |
| **插件 / 全局型** | 由 plugin 提供，subagent 用 Skill 工具按全名调用 | `frontend-design:frontend-design` |

---

## 最新排名（最近一次运行）

> 数据来源：`benchmark-run/run-meta.json` 与 `benchmark-run/report.md`（运行于 2026-06-18，2026-06-19 增补 `ultra-design-v2`、迁移至 Next.js、精简；2026-06-19 密封重测 `gpt-taste`；2026-06-19 增补两个 3D skill；2026-06-20 密封重测 `ultra-design-3d-v2`）。
> 全部 11 个 skill 均通过启动验证（`next start` 零配置启动、首屏 HTTP 200、无阻断性 console error）。

| 排名 | Skill | 加权总分 | 可运行 | 来源 |
|------|-------|:-------:|:------:|------|
| 🥇 1 | **ultra-design-v2** | **9.0 / 10** | ✅ | 本地 `skills_for_benchmark/ultra-design-v2/` |
| 🥈 2 | **ultra-design** | **8.7 / 10** | ✅ | 本地 `skills_for_benchmark/ultra-design/` |
| 🥉 3 | **ultra-design-3d-v2**（3D · 2026-06-20 重测） | **8.6 / 10** | ✅ | 本地 `skills_for_benchmark/ultra-design-3d-v2/` |
| 4 | ui-ux-pro-max | 8.5 / 10 | ✅ | 本地 |
| 4 | frontend-design（插件对照） | 8.5 / 10 | ✅ | 插件 `frontend-design:frontend-design` |
| 4 | web-design-guidelines | 8.5 / 10 | ✅ | 本地 |
| 7 | high-end-visual-design | 8.4 / 10 | ✅ | 本地 |
| 7 | ultra-design-3d（3D · 裸 Three.js） | 8.4 / 10 | ✅ | 本地 |
| 9 | industrial-brutalist-ui | 8.3 / 10 | ✅ | 本地 |
| 9 | gpt-taste（2026-06-19 重测） | 8.3 / 10 | ✅ | 本地 |
| 11 | design-taste-frontend | 8.2 / 10 | ✅ | 本地 |

**评分维度（共 8 项，按权重加权）**：视觉冲击力 15% · 设计系统 15% · 动效体系 20% · 组件完成度 15% · 材质与质感 10% · 响应式与适配 10% · 可运行性与工程化 10% · 无障碍与细节 5%。

> `ultra-design-3d-v2` 已于 2026-06-19 重新设计：聚焦**玻璃彻底 3D 立体化 + 后景彻底无球** —— 移除一切玻璃球（中心主球 / 氛围球群都不留），后景改为 Drei `<Sparkles>` 焦散星光（多层异速闪烁、单 draw call、可被前景玻璃折射、绝不抢戏），默认暗色宇宙底；前景 UI（导航/卡片/按钮/输入框）全是真玻璃 mesh + 标题 troika 3D 文字 + 正文/数字/图表 `<Html transform>` 嵌入，**每件玻璃随鼠标做 3D tilt**（绕 X/Y 轴小幅旋转 + 透视吸附）叠加相机视差，立体感拉满。视觉优先（Bloom/DOF/色差/阴影/多真玻璃全开）+ 主流机流畅（DPR≤2、samples/阴影分辨率封顶、合并 pass、轻量降级兜底），可选 glb hero 雕塑（加载失败/超时回退内建几何体，绝不白屏）。

> 完整逐维度得分与每个 skill 的总评，见 [`benchmark-run/report.md`](benchmark-run/report.md)。

---

## 如何运行基准测试

通过自带 skill 驱动，最简方式：

```
/frontend-design-benchmark
```

或在对话中描述：「重新跑一次前端设计 skill 对比测试」。流程会：

1. 在 `benchmark-run/results/` 下为每个待测 skill 建好隔离产物目录；
2. **同一轮消息内并发**为每个 skill 启动一个 subagent（互不知晓彼此、以为是真实交付），各自产出完整可运行工程；
3. 逐个真实启动工程（`npm install && npm run dev` / `next start`）、抓首屏截图、记录端口；
4. 生成整合审查页 `benchmark-run/index.html` 与评分报告 `benchmark-run/report.md`；
5. 用本地 http server 暴露 `benchmark-run/`，给出统一审查入口。

### 想换题 / 换技术栈 / 增删待测 skill

- **换题或换技术栈**：直接编辑密封题 [`.claude/skills/frontend-design-benchmark/benchmark-task.md`](.claude/skills/frontend-design-benchmark/benchmark-task.md)，下次运行自动生效。
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
