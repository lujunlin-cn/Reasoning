# Tasks — Verifiable Evaluation Criteria

> 项目：`projects/verifiable-criteria`
> 更新：2026-11-17
> 上一版本：`git show ab6e110:projects/verifiable-criteria/tasks.md`（已退役，见 §1）

任务状态规范：`TODO` / `IN_PROGRESS` / `BLOCKED` / `IN_REVIEW` / `DONE` / `CANCELLED`
实验运行状态独立使用：`COMPLETE` / `PARTIAL` / `FAILED` / `BLOCKED`

任务状态与实验运行状态不得混用。

---

## 1. 退役记录

本轮对项目文档做了结构性调整，以下内容不再作为活跃交付物。记录在此以便追溯，不依赖聊天记录。

| 文件 | 处置 | 理由 | 原内容位置 |
|---|---|---|---|
| `charter.md` | 退役 | 其五条核心目标（Validity / Coverage / Discrimination / Consistency / Evidence-Validity 各建一模块）已被本轮检索否证为拥挤方向，见 `research_problem_discovery_01_reasoning.md` §7.1、§10 | `git show ab6e110:projects/verifiable-criteria/charter.md` |
| `idea_direction_01_verifiable_criteria.md` | 退役 | 其核心断言"criterion 缺陷与 executor 缺陷在原理上不可分离"（§0.2、§2.3）已被四类分离装置部分否证；其对 CARO 的描述（"维度间可分离性"）经原文核对为误述 | `git show ab6e110:projects/verifiable-criteria/idea_direction_01_verifiable_criteria.md` |
| `tasks.md`（旧版） | 被本文件取代 | 任务链围绕已退役的 E0 质量阶梯设计 | `git show ab6e110:projects/verifiable-criteria/tasks.md` |

**替代文件：** `research_problem_discovery_01_reasoning.md`（Claude，2026-11-17，commit `beb5f05`）

**保留 ID，不重编号。** 旧链的 `TASK-008` / `TASK-009` / `TASK-010` 标记为 `CANCELLED` 并保留编号；新链从 `TASK-011` 起。这样任何历史引用都不会指向错误的含义。

### 1.1 退役主张与替代主张的对应

| 退役文件中的主张 | 退役原因 | 替代表述（见 discovery 文档） |
|---|---|---|
| 五属性各建一模块 | 三轴分类已成共识，RRD 已命名四失败模式，ProRubric 已提出 reward validity（§7.1、§10） | 拆为五个独立研究对象（生成 / 内容 / 执行 / 聚合 / 下游），§5.1 |
| 标准缺陷与执行缺陷"在原理上不可分离" | Mubric 变异注入、RubricArmor 双条件、2607.27984 反事实替换、ReVeR label-blind 解耦，四种分离装置已存在（§2.1、§A.2） | 弱化为"无带误差界的定量分解"（§0 结论 1） |
| 标准质量可独立测量 | 2609.29709 已形式化非识别：KR-20 在固定 4.72% judge 误差下仅改条目库即在 0.01–0.68 间移动 | 降级为带识别条件的假设（§0 结论 3、问题 A） |
| 运行期错配无诊断 | RIPD 已命名并量化（9.5% / 27.9%）；held-out criterion shift 已存在 | 保留窄缺口：per-sample 在线可信度判定（§C.2） |
| `PReMISE` 为相关工作 | 本轮未能定位原文，**未纳入证据体系** | 不作为论文证据；若需引用须先核 |

---

## 2. 任务表

| ID | Task | Owner | Status | Due | Deliverable |
|---|---|---|---|---|---|
| TASK-001 | Literature Review（18 篇基线文献） | GPT | DONE | 2026-11-17 | `literature_notes/literature_review.md` |
| TASK-002 | Idea Development V1 | Claude | DONE | 2026-11-17 | 已退役 → `git show ab6e110:…/idea_direction_01_verifiable_criteria.md` |
| TASK-003 | Research Problem Discovery + 直接竞争工作原文核验 | Claude | IN_REVIEW | 2026-11-17 | `research_problem_discovery_01_reasoning.md` |
| TASK-004 | 独立复核 discovery 文档的残余未核实项 | GPT | TODO | 2026-11-24 | `literature_notes/supplementary_verification.md` |
| TASK-005 | Gap search：Measurement Theory / Causal Evaluation / Verifier Independence | GPT | TODO | 2026-11-24 | `literature_notes/gap_search.md` |
| TASK-006 | Human 决策：定位、候选问题取舍、负结果风险、任务族、停止条件 | Human | TODO | 2026-12-01 | 本文档 §4 决策记录 |
| TASK-007 | Research Proposal V1 | Claude | TODO | 2026-12-08 | `research_proposal.md` |
| TASK-008 | Experiment Specification（E0 质量阶梯） | GPT | CANCELLED | — | 已退役，见 §1 |
| TASK-009 | E0 实现与执行 | Codex | CANCELLED | — | 已退役，见 §1 |
| TASK-010 | E0 结果审查 | GPT | CANCELLED | — | 已退役，见 §1 |
| TASK-011 | Experiment Specification（EXP-001：verifier 误差率可估性） | GPT | TODO | 2026-12-15 | `experiments/EXP-001/spec.md` |
| TASK-012 | EXP-001 实现与执行 | Codex | TODO | 2026-12-22 | `experiments/EXP-001/runs/` |
| TASK-013 | EXP-001 结果审查 | GPT | TODO | 2026-12-29 | `experiments/EXP-001/result_audit.md` |
| TASK-014 | 撞车复核（拥挤停止条件的定期检查，见 §5） | Claude | TODO | 每两周 | `literature_notes/collision_check.md` |

### 2.1 任务说明

**TASK-003（IN_REVIEW）** —— 等待 Human 与 GPT 双重审查。状态说明：交付物已存在并已推送（`beb5f05`），但未通过 GPT Result Audit，因此**不得**作为论文的确定性结论引用。

**TASK-004** —— GPT 必须独立完成，**不得**直接采信 discovery 文档中的机制描述与数字。`AGENTS.md` 要求 GPT 保持独立审查。重点核以下五项（来自 discovery 文档 §11.1）：

1. RubricArmor 的 independent quality judge 具体实现与其**自身误差率**（全文是否报告）。
2. Mubric 缺陷 operator 的完整集合与覆盖边界（摘要只说"从真实 preferred/dispreferred 对挖掘"）。
3. 2609.29709 的决策表与四条报告纪律的完整措辞。
4. Rubric-ARM 下游增益的**具体数值与实验设置**（摘要未给数字；discovery 文档已明确未引用任何增益数字）。
5. SVR 的 benchmark 切分与 RubricBench oracle（83.1）的构造方式。

同时须判定 discovery 文档对 CARO 的"领域错配"裁决是否成立——这是本轮下调旧 Idea 新颖性的依据之一，若有误须立即更正。

**TASK-005** —— 本轮检索受 arXiv / Semantic Scholar API 限流影响，三个方向的覆盖不完整（见 discovery 文档 §1.3、§12.3）。必须补齐：

- Measurement Theory：generalizability theory 交叉设计、Dawid–Skene 及其在 LLM judge 上的应用现状。
- Causal Evaluation：标准替换、criterion-preserving 改写的因果解释边界。
- Verifier Independence：异族 judge 去相关的可测形式，以及 PoLL 的 "disjoint model families" 前提是否被后续工作检验过。

**TASK-006** —— 阻塞项，见 §4。完成前不启动 TASK-011 及之后任何实验任务。

**TASK-011~013** —— 对应 discovery 文档候选问题 1（低成本、支撑性）。选它作为首个实验是因为：它是候选问题 2 与 3 的前置条件；有明确否证路径；且不需要 GPU 训练（纯推理）。

**TASK-014** —— discovery 文档 §9.3 的拥挤停止条件要求定期复核。该文献族在约 10 个月内出现了七种分离装置，撞车风险是实质的。每两周检索一次，若出现同时满足"交叉设计方差分解 + 非语言锚点 + 归因误差界"三者之一的工作，立即记录并触发 §5 的停止条件评估。

---

## 3. 依赖关系

```text
TASK-001 (DONE)
TASK-002 (DONE, 已退役)

TASK-003 (IN_REVIEW) ─┬─→ TASK-004 ─┐
                      └─→ TASK-005 ─┤
                                    ▼
                              TASK-006 (Human 决策)
                                    │
                                    ▼
                              TASK-007 (Research Proposal V1)
                                    │
                                    ▼
                              TASK-011 (EXP-001 spec)
                                    │
                                    ▼
                              TASK-012 (EXP-001 执行)
                                    │
                                    ▼
                              TASK-013 (EXP-001 审查)

TASK-014 (撞车复核) — 独立周期任务，与上述并行
```

**关键路径：** `TASK-003` → `TASK-004` / `TASK-005` → `TASK-006`。

`TASK-006` 是整个项目的闸门。若 Human 不接受 discovery 文档 §10 的"新颖性下调"，则 TASK-007 的对象需重新定义，TASK-011 之后全部作废。因此**在 TASK-006 完成前，不得启动任何实验**。

---

## 4. Human 决策待决项

### 4.1 当前待决（阻塞 TASK-007）

| 编号 | 决策 | 选项 | 影响 |
|---|---|---|---|
| D-01 | 是否接受"新颖性下调"后的定位：从"提出质量属性体系"改为"建立带识别条件的归因估计量" | 接受 / 部分接受 / 拒绝并重写 | 决定 TASK-007 的对象与 project 的存在理由 |
| D-02 | 候选问题取舍：是否先做候选 1（verifier 误差率，低成本，纯推理） | 先 1 / 直接押 2 / 1+3 合并 | 决定 TASK-011 的 spec，以及是否需要 GPU 预算 |
| D-03 | 是否批准候选 2 的**负结果风险**：若传递系数为零或变号，项目效用论证失效 | 批准 / 不批准 | 决定是否进入候选 2；这是设计上接受的赌注 |
| D-04 | 任务族：文本 / 图像 / 视频 TV2T | 三选一 | 决定"evidence 因子是否有天然三态"是否成立（候选 3 的可行性前提） |
| D-05 | 是否把 discovery 文档 §9 的三条停止条件作为**预注册条款**写入 Research Proposal | 接受 / 修改 / 拒绝 | 决定项目的可终止性；拒绝则 §9.1 失效 |

### 4.2 已作废的待决项

旧版 tasks.md 的 D-01~D-05（主任务族、时间预算、E0/E4 能否独立成文、本体构建人力、是否授权先做 E3）随 E0 链一并作废。

其中两条在本轮被重新表述，不是原样继承：

- 旧 D-01（主任务族）→ 新 D-04，但判据不同：旧版按"方案排序"选，新版按"evidence 因子是否有天然三态"选。
- 旧 D-05（是否授权可能产出不利结论的实验）→ 新 D-03，但对象不同：旧版指 E3，新版指候选问题 2 的传递系数检验。

其余三条（时间预算、E0/E4 独立成文、本体人力）在当前方案下无对应项——本体构建不再是硬依赖，因为 discovery 文档 §B.1 显示五种 coverage 语义中四种已有现成操作化。

---

## 5. 停止条件（待 D-05 批准后生效）

摘自 `research_problem_discovery_01_reasoning.md` §9。**未获 D-05 批准前，以下条款仅为建议，不具约束力。**

| 编号 | 条件 | 触发后的动作 |
|---|---|---|
| **S-1（主动）** | 候选 2 的传递系数在预注册扫描中不变号，且 criterion 强度操纵对 policy 独立成绩的效应 95% 区间在两个任务族、两个预算水平上均含零 | 停止本方向，以负面结果形式发表 |
| **S-2（退化）** | 候选 3 显示 executor facet 方差 ≈ 0 | 停止归因方向（问题归约为标准生成，而已饱和） |
| **S-3（拥挤）** | 出现同时满足交叉设计方差分解 + 非语言锚点 + 归因误差界的工作 | 候选 1 与 3 合并进该工作，project 转向候选 2 |

S-3 由 TASK-014 每两周检查一次。

---

## 6. 备注

- TASK-001 与 TASK-002 于 2026-11-17 完成。TASK-002 的交付物已退役，但该记录保留，因为它包含本轮被否证的具体主张，其否证过程本身是本项目的方法学证据。
- TASK-003 的交付物明确标注为**未批准的 Idea Discovery**，不是 Research Proposal。
- 未获 Human 批准（TASK-006 完成）前，不启动 TASK-011 及之后任何实验任务。
- `AGENTS.md` 要求 Experiment Freeze 分两阶段（Protocol Freeze / Execution Freeze）。EXP-001 的 Protocol Freeze 由 TASK-011 承担，Execution Freeze 由 TASK-012 在运行前记录 Git Commit SHA、随机种子、环境与执行命令。
- 任何协议变更必须创建新的 Experiment ID，不得覆盖 EXP-001 的记录。
