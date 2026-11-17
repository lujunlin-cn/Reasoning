# Tasks — Verifiable Evaluation Criteria

状态规范：`TODO` / `IN_PROGRESS` / `BLOCKED` / `IN_REVIEW` / `DONE` / `CANCELLED`
实验运行状态独立使用：`COMPLETE` / `PARTIAL` / `FAILED` / `BLOCKED`

## 任务表

| ID | Task | Owner | Status | Due | Deliverable |
|---|---|---|---|---|---|
| TASK-001 | Literature Review（18 篇基线文献） | GPT | DONE | 2026-11-17 | `literature_notes/literature_review.md` |
| TASK-002 | Idea Development：可验证评价标准 | Claude | DONE | 2026-11-17 | `idea_direction_01_verifiable_criteria.md` |
| TASK-003 | 核实 2026 年补充文献（SVR / MM-VeriRubric / Auto-Rubric / CARO / MRRG / RRD 等） | GPT | TODO | 2026-11-24 | `literature_notes/supplementary_verification.md` |
| TASK-004 | 补检四个开放方向（预测效度 / 蜕变测试 / 本体评价 / 泄漏） | GPT | TODO | 2026-11-24 | `literature_notes/gap_search.md` |
| TASK-005 | Research Audit：攻击核心命题与三方案假设 | Claude | TODO | 2026-12-01 | `research_audit_01.md` |
| TASK-006 | Human 决策：任务族 / 时间预算 / 本体资源 / 是否先做 E3 | Human | TODO | 2026-12-01 | 决策记录（写入本文件） |
| TASK-007 | Research Proposal V1 | Claude | TODO | 2026-12-08 | `research_proposal.md` |
| TASK-008 | Experiment Specification（E0 质量阶梯） | GPT | TODO | 2026-12-15 | `experiments/EXP-001/spec.md` |
| TASK-009 | E0 实现与执行 | Codex | TODO | 2026-12-22 | `experiments/EXP-001/runs/` |
| TASK-010 | E0 结果审查 | GPT | TODO | 2026-12-29 | `experiments/EXP-001/result_audit.md` |

## 依赖关系

```text
TASK-001 ─┐
TASK-002 ─┴─→ TASK-003 ─┬─→ TASK-005 ─→ TASK-006 ─→ TASK-007 ─→ TASK-008 ─→ TASK-009 ─→ TASK-010
TASK-004 ───────────────┘
```

**关键路径：** TASK-003 是阻塞项。SVR 与 MM-VeriRubric 可能已覆盖方案二的核心机制，核实结果直接决定 TASK-005 的审查对象与 TASK-007 的方案取舍。

## Human 决策待决项

| 编号 | 决策 | 影响 |
|---|---|---|
| D-01 | 主任务族：文本 / 图像 / 视频 TV2T | 决定方案排序（视频↑ OSC、↓ LDCI） |
| D-02 | 时间预算：6 个月 / 12 个月 | 6 个月走降级路径，只做 E0 + E4 |
| D-03 | E0/E4 能否作为独立贡献成文 | 若可，方案失误风险大幅下降 |
| D-04 | 本体构建人力（2–3 人日/任务族） | 决定 OSC 是否可行 |
| D-05 | 是否授权先做 E3（可能产出对现有文献不利的结论） | 决定执行顺序 |

## 备注

- TASK-001 与 TASK-002 在 2026-11-17 完成。TASK-002 的交付物明确标注为未批准的 Idea，不是 Research Proposal。
- 未获 Human 批准（TASK-006 完成）前，不启动 TASK-008 及之后任何实验任务。
