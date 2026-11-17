# AI-assisted Research Workflow

## 多模型协作科研工作流

## 一、核心角色分工

科研工作由人类研究者、GPT、Claude 和 Codex 协同完成。

| 角色 | 核心职责 |
|---|---|
| Human（研究者） | 提出研究问题、确定研究方向、批准研究方案、最终决策 |
| GPT | 文献检索与精读、实验设计、代码审查、实验结果审查、独立论文审稿 |
| Claude | Idea Development、全过程严谨审查、Paper Writing |
| Codex | 代码实现、训练、实验执行、评测、日志与结果归档 |

基本原则：

- Human 负责科研决策。
- Claude 负责研究想法的形成、批判性审查和论文表达。
- GPT 负责文献证据、实验验证和独立审查。
- Codex 负责将实验方案转化为可运行、可复现的代码和实验。

三个 AI 共享同一个 Git Repository。所有重要研究成果必须以文件形式保存，不依赖聊天记录传递上下文。

**Claude 的严谨审查贯穿 Idea Development、Research Proposal 和 Paper Writing 三个阶段。** Claude 不应只负责完善研究想法，还必须主动寻找反例、质疑研究假设、检查创新性和证据充分性。

GPT 的 Independent Peer Review 与 Claude 的自我审查相互独立，不得相互替代。

---

## 二、完整科研工作流

### Phase 1：Research Ideation

**Step 1. Human — Research Question**

研究者提出研究问题。

提供：

- 研究兴趣。
- 初步 Idea。
- 希望解决的问题。
- 目标会议或期刊。

输出：初始研究问题。

**Step 2. GPT — Literature Review**

GPT 负责检索和阅读相关论文。

主要任务：

- 检索相关论文和最新工作。
- 阅读论文原文。
- 整理已有方法、数据集和 Benchmark。
- 比较不同论文的研究贡献。
- 分析现有研究的局限。
- 寻找潜在 Research Gap。

输出：

- 参考文献原件。
- Paper Cards。
- 文献对比矩阵。
- Literature Review。

所有文献结论必须保留原文出处和证据位置。

**Step 3. Claude — Idea Development**

Claude 根据研究者的初步想法和 GPT 提供的文献材料完善 Idea。

主要任务：

- 明确 Problem Definition。
- 提出 Research Hypothesis。
- 探索可能的方法创新。
- 形成方法框架。
- 设计论文叙事。
- 识别可能的贡献点。

同时，Claude 必须主动审查自己的研究构想：

- 是否存在更简单的替代解释？
- 是否有反例能够推翻当前假设？
- 是否存在尚未考虑的相关工作？
- 当前方法是否真正解决目标问题？

输出：Research Idea V1。

**Step 4. Claude — Research Audit**

Claude 对研究方案进行系统性批判审查。

主要任务：

- 检查研究问题是否成立。
- 检查创新点是否充分。
- 检查是否与已有工作重复。
- 检查方法是否能够解决定义的问题。
- 检查研究假设是否可验证。
- 判断研究贡献是否适合目标会议。
- 寻找潜在失败模式和反例。
- 评估研究方案是否具有足够的科学价值。

输出：Research Proposal V1。

Research Proposal 必须明确研究问题、创新点、方法假设、预期贡献、验证方式和主要风险。

### Phase 2：Research Approval

**Step 5. Human — Approve Research Proposal**

研究者审查 Research Proposal。

确认：

- 研究问题。
- 创新点。
- 方法假设。
- 评价指标。
- 目标投稿方向。
- 实验资源和时间预算。

研究者批准后，研究方案进入正式执行阶段。

未经批准，不进入正式实验。

### Phase 3：Experiment Design and Implementation

**Step 6. GPT + Codex — Experiment Design**

本阶段由 GPT 和 Codex 协作完成。

GPT 负责：

- 将研究假设转化为可验证的实验问题。
- 设计 Baseline。
- 确定 Dataset 和 Data Split。
- 确定 Evaluation Metrics。
- 设计必要的统计分析。
- 确定成功条件和停止条件。
- 编写 Experiment Specification。

Codex 负责：

- 根据实验规范实现代码。
- 实现训练和评测流程。
- 实现数据处理。
- 实现指标计算。
- 运行基础测试和 Smoke Test。

随后 GPT 审查 Codex 的代码实现。

审查内容：

- 代码是否正确实现实验协议。
- 指标计算是否正确。
- Baseline 是否公平。
- 数据划分是否存在泄漏。
- 是否存在实现层面的逻辑错误。
- 是否能够可靠记录实验结果。

如果审查失败，Codex 修改实现，GPT 重新审查。

**Experiment Freeze：**

实验正式启动前，冻结：

- Research Hypothesis。
- Experiment Specification。
- Dataset 和 Split。
- Baseline。
- Evaluation Metrics。
- Success / Stop Criteria。
- Git Commit SHA。
- 实际运行配置。

冻结后不得静默修改实验协议。

如果需要改变实验假设、指标或数据划分，必须建立新的实验版本或 Experiment ID。

### Phase 4：Experiment Execution

**Step 7. Codex — Experiment Execution**

Codex 负责执行实验。

主要任务：

- 执行训练。
- 执行推理和评测。
- 记录运行环境。
- 保存训练日志。
- 保存原始指标。
- 生成图表。
- 记录异常和失败案例。

每次实验必须产生：

1. `run_manifest.json`
2. `metrics.csv`
3. `report.md`
4. `logs/`
5. `figures/`

实验报告必须区分：

- COMPLETE
- PARTIAL
- FAILED
- BLOCKED

不得将部分执行结果描述为完整实验结果。

### Phase 5：Result Verification

**Step 8. GPT — Result Audit**

GPT 独立检查 Codex 产生的实验结果。

主要任务：

- 检查原始日志。
- 核对指标计算。
- 检查实验是否符合冻结的协议。
- 分析统计结果。
- 检查 Baseline 公平性。
- 检查结果是否支持 Research Hypothesis。
- 分析失败案例。
- 确定结论的适用范围。

输出：

- Result Audit。
- Verified Findings。
- Unresolved Issues。
- Recommendations。

未经审查的实验结果不得直接作为论文中的确定性结论。

### Phase 6：Paper Writing

**Step 9. Claude — Paper Writing**

Claude 根据已经审查的实验结果撰写论文。

主要任务：

- Introduction。
- Related Work。
- Method。
- Experiments。
- Results and Analysis。
- Limitations。
- Conclusion。

Claude 在写作过程中必须继续执行严谨审查：

- 检查每项论文主张是否有对应证据。
- 检查方法描述是否与实际代码一致。
- 检查实验结论是否超出数据支持范围。
- 检查 Related Work 是否遗漏重要工作。
- 检查是否存在选择性报告实验结果的情况。
- 检查论文叙事是否与实际研究贡献一致。
- 检查 Limitations 是否充分披露。

要求：

- 所有实验数据来自已归档的原始结果。
- 所有重要论断有证据支持。
- 不得扩大实验结论。
- 不得虚构引用和实验结果。
- 论文叙事必须与实际研究贡献一致。

输出：Paper Draft。

### Phase 7：Independent Review

**Step 10. GPT — Independent Peer Review**

GPT 模拟严格审稿人，独立审查论文。

主要维度：

1. Novelty：创新性是否成立？
2. Problem：研究问题是否重要？
3. Method：方法是否正确？
4. Experiments：实验是否充分且公平？
5. Evidence：结论是否得到实验支持？
6. Related Work：是否遗漏重要相关工作？
7. Writing：论文表达是否清晰？
8. Reproducibility：是否具备复现条件？

输出：

- Reviewer Report。
- Major Concerns。
- Minor Concerns。
- Required Experiments。
- Acceptance Risks。

Claude 根据审稿意见修改论文，并重新检查修改后的论证和证据。

最终由研究者决定是否投稿。

---

## 三、Git Repository 组织结构

采用一个统一的 Research Workspace。

```text
research-workspace/
│
├── README.md
├── AGENTS.md
│
├── templates/
│   ├── paper_card.md
│   ├── experiment_spec.md
│   └── experiment_report.md
│
└── projects/
    │
    ├── project-name/
    │   │
    │   ├── charter.md
    │   ├── tasks.md
    │   ├── idea.md
    │   ├── research_proposal.md
    │   │
    │   ├── references/
    │   │   ├── papers/
    │   │   └── bibliography.bib
    │   │
    │   ├── literature_notes/
    │   │   ├── paper_matrix.csv
    │   │   ├── paper_001.md
    │   │   └── paper_002.md
    │   │
    │   ├── experiments/
    │   │   ├── EXP-001/
    │   │   │   ├── spec.md
    │   │   │   ├── runs/
    │   │   │   │   └── RUN-001/
    │   │   │   │       ├── run_manifest.json
    │   │   │   │       ├── metrics.csv
    │   │   │   │       ├── report.md
    │   │   │   │       ├── logs/
    │   │   │   │       └── figures/
    │   │   │   └── result_audit.md
    │   │   │
    │   │   └── EXP-002/
    │   │
    │   ├── results/
    │   │   └── result_summary.md
    │   │
    │   └── writing/
    │       ├── paper.md
    │       └── reviews/
    │
    └── another-project/
        └── ...

```

### 文件职责

**README.md**

面向人类研究者。

介绍：

- 仓库用途。
- 当前研究项目。
- 基本目录结构。

**AGENTS.md**

面向 GPT、Claude 和 Codex。

作为整个仓库唯一的 AI 工作流规范和索引入口。

记录：

- 完整科研工作流。
- GPT、Claude、Codex 的职责。
- 跨模型交接方式。
- Experiment Freeze 机制。
- 仓库目录和文件说明。

AI 开始处理任务时，先读取根目录的 `AGENTS.md`，再按需读取对应项目文件。

不设置额外的 `CLAUDE.md` 或 `RESEARCH_INDEX.md`。

**references/**

只存放参考文献原件和书目信息。

包括：

- 原始 PDF。
- arXiv 下载文件。
- 官方论文版本。
- BibTeX。

**literature_notes/**

存放 GPT 对文献的解读。

每篇重点论文使用一个 Paper Card。

Paper Card 记录：

- Bibliography。
- Research Problem。
- Main Contribution。
- Method。
- Experimental Evidence。
- Limitations。
- Relation to Our Research。
- Research Opportunities。
- Evidence Anchors。

参考文献原件与文献解读必须分开保存。

**tasks.md**

记录项目任务及其状态。

每项任务必须具有唯一 Task ID、负责人、状态和交付文件。

**experiments/**

存放每次实验的冻结协议、运行信息和原始结果。

`EXP-xxx` 表示独立实验，`RUN-xxx` 表示该实验的一次具体运行。

**results/**

存放 GPT 审查后的结果总结和经过验证的研究发现。

**writing/**

存放 Claude 撰写的论文和 GPT 的审稿意见。

### 任务状态规范

项目任务统一使用以下状态：

| 状态 | 含义 |
|---|---|
| TODO | 尚未开始 |
| IN_PROGRESS | 正在执行 |
| BLOCKED | 存在阻塞，暂时无法继续 |
| IN_REVIEW | 已提交，等待审查 |
| DONE | 已完成，交付物和审查均已通过 |
| CANCELLED | 已取消，不再执行 |

任务状态按以下规则流转：

```text
TODO → IN_PROGRESS → IN_REVIEW → DONE
            ↑             │
            └─────────────┘
                  审查未通过

IN_PROGRESS → BLOCKED → IN_PROGRESS

TODO / IN_PROGRESS / BLOCKED → CANCELLED
```

要求：

- 任务开始执行后更新为 `IN_PROGRESS`。
- 等待其他 AI 或研究者审查时更新为 `IN_REVIEW`。
- 审查未通过时返回 `IN_PROGRESS`。
- 只有交付物存在且验收通过，才能标记为 `DONE`。
- 遇到无法解决的依赖问题时标记为 `BLOCKED`，并记录阻塞原因。
- 取消任务时标记为 `CANCELLED`，保留取消原因。

`tasks.md` 使用以下字段：

| ID | Task | Owner | Status | Due | Deliverable |
|---|---|---|---|---|---|
| TASK-001 | Literature Review | GPT | DONE | 2026-12-01 | literature_notes/ |
| TASK-002 | Research Proposal | Claude | IN_PROGRESS | 2026-12-05 | research_proposal.md |
| TASK-003 | Experiment Implementation | Codex | TODO | 2026-12-15 | experiments/ |

实验运行状态继续使用：

- COMPLETE
- PARTIAL
- FAILED
- BLOCKED

任务状态与实验运行状态相互独立，不得混用。

### 文件命名规范

统一遵循以下命名规则：

**1. 目录名称**

使用小写 kebab-case。

例如：

```text
knowledge-internalization/
router-lora/
human-like-writing/
literature-notes/
```

**2. Markdown、JSON、CSV 文件**

使用小写 snake_case。

例如：

```text
research_proposal.md
experiment_spec.md
run_manifest.json
metrics.csv
result_audit.md
```

文件名使用英文和 ASCII 字符，不使用空格和中文。

**3. 任务和实验 ID**

统一使用大写前缀和三位数字：

```text
TASK-001
TASK-002

EXP-001
EXP-002

RUN-001
RUN-002
```

ID 在所属项目中唯一，不因任务名称修改而改变。

**4. 文献文件**

使用：

```text
first_author_year_short_title.pdf
first_author_year_short_title.md
```

例如：

```text
smith_2026_knowledge_internalization.pdf
smith_2026_knowledge_internalization.md
```

PDF 放入 `references/papers/`，对应 Paper Card 放入 `literature_notes/`。

**5. 版本管理**

优先使用 Git Commit 记录修改历史。

不得使用以下形式：

```text
paper_final.md
paper_final_v2.md
paper_final_revised_new.md
```

正式实验协议发生变化时，创建新的实验版本或 Experiment ID，并保留原始记录。

---

## 四、Experiment Freeze 机制

Experiment Freeze 是所有正式实验的强制环节。

每个实验必须经过两个阶段。

### Protocol Freeze

在代码实现之前冻结：

- Research Question。
- Hypothesis。
- Dataset。
- Data Split。
- Baselines。
- Metrics。
- Statistical Analysis。
- Success Criteria。
- Stop Criteria。

GPT 完成实验协议，研究者批准后，Codex 才能进入正式实现。

### Execution Freeze

在正式运行之前冻结：

- 实际代码 Git Commit SHA。
- 实验配置。
- 运行环境。
- Random Seed。
- 数据版本。
- 执行命令。

实验启动后保留原始日志和结果。

任何协议变更都必须创建新的实验版本，不能覆盖原有实验记录。

代码实现发生变化时，必须记录新的 Git Commit SHA。

---

## 五、跨模型交接机制

所有交接以 Git 文件为准，不以聊天记录为准。

| 交接 | 必须产生的文件 |
|---|---|
| GPT → Claude | Paper Cards、Literature Review |
| Claude → Human | Research Proposal V1 |
| Human → GPT / Codex | Approved Research Proposal |
| GPT → Codex | Experiment Specification |
| Codex → GPT | Code、Test Results |
| GPT → Codex | Implementation Audit / 修改意见 |
| Codex → GPT | Run Manifest、Metrics、Logs、Report |
| GPT → Claude | Result Audit、Verified Findings |
| Claude → GPT | Paper Draft |
| GPT → Claude | Independent Review |

每次交接必须明确：

- 当前任务。
- 输入文件。
- 输出文件。
- 当前状态。
- 尚未解决的问题。

重要文件通过 Git Commit 保存和同步。避免多个 AI 同时修改同一个文件。

---

## 六、最终原则

1. Git Repository 是所有 AI 的共同工作区。
2. 原始文献、文献解读、实验结果必须分开保存。
3. Paper Card 是文献理解与证据传递的标准格式。
4. README 面向人类，AGENTS 面向 AI。
5. 不使用冗余的 CLAUDE.md 和 RESEARCH_INDEX.md。
6. 重要研究决策必须有文件记录。
7. Experiment Freeze 必须执行。
8. 原始实验数据不得被覆盖。
9. 未经 GPT Result Audit 的结果不得直接作为论文确定性结论。
10. Claude 必须对 Idea、Research Proposal 和 Paper Writing 执行严谨审查。
11. GPT 必须保持独立审查，不以 Claude 的自我审查代替。
12. 最终科研决策权始终属于 Human。

**核心目标：让 Claude 负责提出、质疑和表达研究，让 GPT 负责证据、实验审查和独立审稿，让 Codex 负责实验实现，让研究者负责判断。**