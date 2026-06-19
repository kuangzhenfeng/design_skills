---
name: frontend-design-benchmark
description: 当需要公平对比多个前端设计 skill（如 ultra-design、frontend-design 等）在同题下的设计效果时使用。为每个待测 skill 创建隔离的 subagent 并行做同一道密封测试题，收集产物后生成可跳转的整合审查页，并由主 agent 按多维标准打分排名。
---

# Frontend Design Benchmark（前端设计对比测试）

## 概述

**核心原则**：用一道密封、固定的测试题，让多个前端设计 skill 在完全相同条件下各自独立产出，然后由主 agent 统一收集、整合成可一键跳转的审查页、并按统一维度打分排名——保证对比的**公平性**与**可审查性**。

> 这是基准测试，不是普通开发任务。**密封题 + 隔离环境 + 同技术栈 + 统一评分**是公平的三大支柱，缺一不可。

---

## 0. 输入：确认待测 skill 列表

执行前先确定本次参与对比的 skill。默认待测列表（位于仓库）：

| 待测 skill | 加载方式 |
|-----------|---------|
| ultra-design | 本地文件：`skills_for_benchmark/ultra-design/SKILL.md`（subagent 直接 Read） |
| frontend-design | 插件 skill：subagent 用 Skill 工具调用全名 `frontend-design:frontend-design` |

> 待测 skill 有两种来源——**本地文件型**（仓库里有 SKILL.md，subagent 直接 Read 该文件并遵循）与**插件/全局型**（由 plugin 提供，subagent 用 Skill 工具按全名调用）。确定列表时逐个判明类型：先 `find . -name SKILL.md` 找本地文件，找不到再视为插件型并确认其 Skill 工具全名。**若两类都定位不到，跳过并告知用户**。若用户指定了不同/更多 skill，按用户给定的为准。

---

## 执行流程

### 步骤 1 · 准备工作区与目录结构

在仓库根创建一次性运行目录（**每次运行用固定名 `benchmark-run/`，覆盖上次**）：

```
benchmark-run/
├── results/                # 各 skill 的产物根
│   ├── ultra-design/       # ← 该 skill 的完整可运行工程放这里
│   └── frontend-design/
├── index.html              # 整合审查页（步骤 5 生成）
├── report.md               # 评分报告（步骤 6 生成）
└── run-meta.json           # 本次运行元信息（skill 列表、时间、状态）
```

产出：`benchmark-run/` 目录就绪。

### 步骤 2 · 锁定密封测试题与技术栈

- 测试题就放在本 skill 同目录的 `benchmark-task.md`，是一道**综合型 Dashboard** 题（8 个模块、亮暗双主题、完整动效体系、响应式）。
- 技术栈已在该题中锁死：**TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion**，**仅输出代码**。

> **若用户想换题**：直接编辑 `benchmark-task.md` 即可，下次运行自动生效，无需改本流程。

判断规则：

| 情况 | 处理 |
|------|------|
| 用户没指定换题 | 直接用现有 `benchmark-task.md` |
| 用户给了新题目/新约束 | 用 Edit 改写 `benchmark-task.md`，保持"模块清单 + 设计要求 + 技术栈 + 公平性铁律"四段结构 |
| 用户想换技术栈 | 同样改 `benchmark-task.md` 的「技术栈约束」表 |

### 步骤 3 · 隔离调度：为每个 skill 创建 subagent 并行产出

**关键：所有 subagent 必须在同一轮（单条消息里多个 Agent 工具调用）并发启动**，互不知晓彼此存在。

对每个待测 skill，构造一个 subagent，prompt 严格遵循下方模板。**三条公平性铁律**必须逐字写进每个 prompt：

1. **密封**：subagent 收到的题目 = `benchmark-task.md` 全文。**不得**在 prompt 中出现"对比""benchmark""其他 skill""评分"等字眼，只说"为客户做一个定稿级 Dashboard"。
2. **隔离**：每个 subagent 用 `isolation: "worktree"` 或被明确指派到**各自独立的产物目录**（`benchmark-run/results/<skill-name>/`），互不串扰。
3. **同栈同交付**：技术栈与"仅输出代码"在 prompt 中显式重申，避免 skill 自由发挥破坏公平。

**Subagent prompt 模板**（`<skill>` 为待测 skill 名，`<skill-md-path>` 为其 SKILL.md 路径）：

> ⚠️ 主 agent 构造每个 prompt 时，**必须先用 Read 读出 `benchmark-task.md` 的完整内容**，替换掉模板里的 `<此处插入...>` 行——绝不能把占位符原样发给 subagent。`<skill>` 与 `<skill-md-path>` 同样替换为该 subagent 对应的真实值；`<abs-path>` 替换为仓库根绝对路径。

```
你要为真实客户「Aurora 智能分析平台」从零交付一个【定稿级、生产质量】的主控 Dashboard。这是为客户的正式交付，请拿出你的最高水准。

【第一步·加载设计规范】
<LOAD_STEP>   ← 见下方替换说明

【任务】
请阅读下面的需求并完整实现。需求文件内容如下（请逐字遵循）：

<此处插入 benchmark-task.md 的完整内容>

【交付要求】
- 技术栈、交付形式**严格按需求文件「技术栈约束」一节**执行（本题即 TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion，七项缺一不可、不得替换），不得自行更换。
- 把完整可运行工程写到指定目录：<abs-path>/benchmark-run/results/<skill>/（该目录已为你创建，工程根即此目录）。
- 工程必须按需求文件指定的方式零配置启动（本题即 `npm install && npm run dev`，Next.js 默认 `http://localhost:3000`），浏览器打开即见完整效果。
- 仅输出代码：README 只放启动说明，不要写任何设计思路自述或对所用技巧的解释。
- 不调用任何真实 API，数据全部 mock。

现在开始：先加载 skill 规范，再按需求实现，完成后确保本地能 `npm install && npm run dev` 启动。
```

**`<LOAD_STEP>` 替换说明**（按该 subagent 对应 skill 的来源类型二选一）：

- 本地文件型 → 替换为：
  `请先用 Read 工具读取 <abs-path>/<skill-md-path>（相对仓库根），并严格遵循该文件中的全部设计规范作为本次设计的最高指导。`
- 插件型 → 替换为：
  `请先用 Skill 工具调用名为 <plugin-skill-fullname> 的 skill（这是你要遵循的设计语言与质量标准），将其作为本次设计的最高指导。`

调度方式选择：

| 方式 | 适用 | 说明 |
|------|------|------|
| **Agent 工具并发**（默认） | 通用 | 单条消息内对 N 个 skill 各发一个 Agent 调用；`subagent_type` 用 `general-purpose`。**本地文件型 skill**：在 prompt 里要求 subagent 先 `Read <skill-md-path>`；**插件型 skill**：要求 subagent 先用 Skill 工具按全名调用（如 `frontend-design:frontend-design`） |
| `isolation: "worktree"` | 仓库是 git 仓库时 | 各 subagent 在独立 git worktree 工作，天然隔离；最后把产物复制回 `benchmark-run/results/` |
| 独立产物目录（推荐用于本项目，非 git） | 非 git 仓库 | 直接在 prompt 里指定 `benchmark-run/results/<skill>/` 为工程根，subagent 之间目录不同即隔离 |

> **并发上限**：若 skill 很多（>6），分批启动，每批 ≤4 个，避免资源争抢导致都跑不完。

### 步骤 4 · 收集产物并验证可运行性

每个 subagent 返回后，主 agent 逐个核验产物：

1. 目录是否存在 `package.json` / 入口文件。
2. **尝试真实启动**（关键，否则评分无意义）：
   ```bash
   cd benchmark-run/results/<skill> && npm install && npm run dev  # 后台跑，几秒后 curl 首页/截图
   ```
   - 能起来 → 标记 `runnable: true`，记录本地预览 URL/端口。
   - 起不来 → 标记 `runnable: false`，记录报错摘要；该 skill 在评分时"可运行性"维度直接 0 分，但仍保留产物供人工查看。
3. 对每个能跑的产物**抓一张首屏截图**（用 Playwright 浏览器工具 navigate + take_screenshot），存到 `benchmark-run/results/<skill>/_preview.png`，供整合页与评分引用。

判断规则：

| 情况 | 处理 |
|------|------|
| 全部能跑 | 正常进入整合 |
| 部分跑不起来 | 继续整合，评分时如实扣分，并在报告里明确标注"未通过启动验证" |
| 全部跑不起来 | 停下，把各 subagent 的报错汇总给用户，先解决环境问题再重跑 |

### 步骤 5 · 生成整合审查页（index.html）

在 `benchmark-run/index.html` 生成一个**单文件审查页**，要求：

- 顶部：标题"Aurora Dashboard 设计对比" + 一句话说明 + 预览方式提示。
- 主区域：为每个 skill 一张**卡片**，横向并排（响应式可折行），每张卡片包含：
  1. skill 名 + 是否通过启动验证的徽标（✅ 可运行 / ⚠️ 启动失败）
  2. **首屏截图**（`_preview.png`，用相对路径引用；用 base64 内联或 `<img src="results/<skill>/_preview.png">` 均可）
  3. **两个跳转入口**：
     - 「在新标签打开运行中的页面」→ 指向该 skill 的本地 dev server URL（Next.js 默认 `http://localhost:3000`）；
     - 「打开源码目录」→ 指向 `results/<skill>/`（若审查页通过本地 http server 打开则可点击）。
  4. 待填的**评分槽位**（步骤 6 填入后会重写此页加入分数，或在报告里呈现）。
- 卡片支持点击放大截图（lightbox 效果，纯 JS 实现即可）。
- 页面本身也要有一定设计感（但这是工具页，简洁优先，别喧宾夺主）。

> 跳转目标的处理：由于各 skill 的 dev server 端口不一定相同，**步骤 4 启动时记录每个 skill 实际占用的端口**，整合页里写死对应 URL。若不方便常驻多个 server，备选方案：给每个 skill 跑 `npm run build` 出静态产物，用 `python3 -m http.server` 各起一个静态端口，整合页指向这些静态 URL——更稳定，推荐用于最终审查。

### 步骤 6 · 主 agent 多维度评分与排名

主 agent 作为评审，**对每个通过启动验证的 skill 逐项打分**（0–10 分制，可给到一位小数）。维度如下：

| 维度 | 权重 | 评分要点 |
|------|-----|---------|
| **视觉冲击力** | 15% | 第一眼是否有强烈设计感，是否摆脱通用 AI 审美 |
| **设计系统** | 15% | token 体系是否完整统一、色彩/字号/间距/圆角/阴影是否有层次 |
| **动效体系** | 20% | 是否覆盖入场/加载/指针/状态/过渡五类、是否成体系、是否流畅不掉帧 |
| **组件完成度** | 15% | 8 个模块是否全部真实落地、状态是否完整、交互是否可用 |
| **材质与质感** | 10% | 玻璃/光晕/渐变/阴影等材质表达是否高级、是否服务信息层级 |
| **响应式与适配** | 10% | 三档断点是否合理、移动端是否可用 |
| **可运行性与工程化** | 10% | 能否零配置启动、代码组织是否清晰、是否有 console error |
| **无障碍与细节** | 5% | 键盘可达、focus ring、语义化、对比度 |

**评分操作要求**：

1. 主 agent 必须先实际**运行并浏览**每个产物（看截图 + 点开 dev server 实际操作），不允许只看代码臆测。
2. 每个维度给分时**简述一句依据**（写在报告里），避免无依据的分数。
3. 按权重算**加权总分**，并给出**最终排名**。
4. 每个技能写 2–3 句**总评**（优点 / 最明显短板）。
5. 给出**逐维度对比表**（skill × 维度），并标出每列的最高分。

产出：`benchmark-run/report.md`（评分报告）。然后**回到 `index.html` 把每个卡片的分数与总分徽标补上**，让审查页一眼能看到排名。

`report.md` 结构模板：

```markdown
# Aurora Dashboard 设计对比 · 评分报告

> 运行时间：[填写] ｜ 参评 skill：[列表] ｜ 测试题：benchmark-task.md

## 一、最终排名

| 排名 | Skill | 加权总分 | 可运行 |
|------|-------|---------|--------|
| 🥇 | ... | x.x/10 | ✅ |

## 二、逐维度得分（含权重）

| Skill | 视觉(15%) | 设计系统(15%) | 动效(20%) | 完成度(15%) | 材质(10%) | 响应式(10%) | 工程化(10%) | 无障碍(5%) | 总分 |
|-------|-----------|--------------|-----------|-------------|-----------|-------------|-------------|------------|------|

## 三、逐 skill 总评
### 1. <skill>
- 亮点：...
- 短板：...
- 维度依据：...

## 四、结论与建议
- 综合最强：...
- 各维度单项最优：...
- 适用场景建议：...
```

### 步骤 7 · 收尾：打开审查页 + 汇报

1. 用本地 http server 暴露 `benchmark-run/`（保证整合页的截图相对路径与跳转都能用）：
   ```bash
   cd benchmark-run && python3 -m http.server 8765
   ```
   把 `http://localhost:8765/` 给用户，作为统一审查入口。
2. 在对话里**简要汇报**：参评 skill、谁通过了启动验证、最终排名前三、整合页与报告地址。
3. 提示用户：可直接在浏览器横向对比，详细评分见 `report.md`。

---

## 判断规则

### 公平性边界

| 场景 | 正确做法 |
|------|---------|
| 某 skill 要求不同技术栈 | **不允许破例**。技术栈（TypeScript + React + Next.js + Tailwind CSS + shadcn/ui + Radix + Motion）是公平基线；若该 skill 在锁死栈下表现差，正是测试要反映的结果，如实记录 |
| 某 subagent 没遵守"仅输出代码"夹带了大量自述 | 评分"工程化"维度酌情扣分，但**不剔除**其产物 |
| 两个 skill 产物高度雷同 | 如实记录；通常说明 prompt 注入足够"密封"，反而是公平的好迹象 |
| 某 skill 始终跑不起来 | 不放弃，记为启动失败，可运行性 0 分，但保留其余维度的人工评估空间 |
| 用户想加入新 skill | 加到步骤 0 的待测表，重跑步骤 3–7（可只跑新增的，再合并报告） |

### 评分客观性

| 要求 | 说明 |
|------|------|
| 先看实物再打分 | 必须运行 + 截图 + 实际点按，禁止只读代码打分 |
| 一句依据 | 每个分数配一句证据，杜绝无依据分数 |
| 主观维度也要锚定 | "视觉冲击"等用首屏截图横排对比来锚定相对差异，减少绝对主观漂移 |

---

## 常见误区

| 误区 | 正确做法 |
|------|---------|
| 把"这是对比测试"写进 subagent prompt | 永不告知；密封题要求 subagent 以为是真实交付，否则会"应试"失真 |
| 串行逐个跑 subagent | 必须并发（单消息多 Agent 调用），既省时也避免先跑的污染后续判断 |
| 跳过真实启动验证直接评分 | 启动验证是评分前提；跑不起来的"好设计"对用户无价值，必须实测 |
| 整合页跳转指向不存在的 URL | 步骤 4 必须记录每个 skill 实际端口；用静态 build + http.server 更稳 |
| 评分只给总分不给依据 | 每维度一句依据，报告才可复查、可复现 |
| 用不同技术栈对比 | 技术栈差异会污染设计能力对比；坚持同栈，差异留给"工程化"维度体现 |

---

## 示例：一次最小运行（2 个 skill）

1. 确认待测：`ultra-design`、`frontend-design`。
2. 建 `benchmark-run/results/{ultra-design,frontend-design}/`。
3. 同一消息并发两个 Agent（general-purpose）：
   - `ultra-design`（本地文件型）→ prompt 注入其 `Read skills_for_benchmark/ultra-design/SKILL.md` 加载步骤 + `benchmark-task.md` 全文 + 公平性铁律，产物落 `results/ultra-design/`。
   - `frontend-design`（插件型）→ prompt 注入其 `Skill(frontend-design:frontend-design)` 加载步骤 + 同样的题目与铁律，产物落 `results/frontend-design/`。
4. 两边各 `npm install && npm run dev`，截图存 `_preview.png`，记录端口。
5. 生成 `index.html`：两张卡，各含截图 + 跳转该 skill dev URL + 源码目录。
6. 实际浏览两个产物，按 8 维度打分，写 `report.md`，回填分数到卡片。
7. `python3 -m http.server 8765`，把 `http://localhost:8765/` 交给用户，对话里报排名。
