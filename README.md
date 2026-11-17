# Reasoning — Generative Reward Modeling Research Workspace

面向人类研究者的仓库入口。AI 工作流规范见根目录 `AGENTS.md`。

## 当前研究项目

| 项目 | 主题 | 状态 |
|---|---|---|
| `projects/verifiable-criteria/` | 让生成式奖励模型（GRM）的评价标准本身具备可验证性 | Idea Development 完成，待 Research Audit 与 Human 批准 |

## 研究方向

**Reasoning-based Reward Modeling。** 当前 GRM 已能生成 Principles、Critiques 与 CoT，但评价标准本身可能存在错误、遗漏、不适用或缺乏区分能力；标准看似合理时，执行过程也可能与真实证据不一致。

本项目研究：如何让 GRM 不仅执行评价标准，还能生成、检查和验证评价标准本身。区分三类不可混同的失效：

- **Logical Consistency**：判定是否由标准与观察推得
- **Evidence Grounding**：观察本身是否为真
- **Judgment Validity**：标准本身是否恰当且完整

## 目录结构

```text
.
├── AGENTS.md                     # AI 工作流规范（GPT / Claude / Codex）
├── README.md                     # 本文件，面向人类
└── projects/
    └── verifiable-criteria/
        ├── charter.md            # 项目章程
        ├── tasks.md              # 任务与状态
        ├── idea_direction_01_verifiable_criteria.md   # Claude：Idea Development
        ├── references/papers/    # 18 篇原始 PDF
        ├── literature_notes/     # GPT：Literature Review
        ├── experiments/          # EXP-xxx / RUN-xxx（冻结协议与原始结果）
        ├── results/              # GPT 审查后的结果总结
        └── writing/              # 论文草稿与审稿意见
```

## 当前阶段

```text
Human 提问 → GPT 文献 → Claude Idea Development（已完成）
                                 ↓
                    Claude Research Audit（下一步）
                                 ↓
                    Human 批准 Research Proposal
                                 ↓
                    实验设计 → Experiment Freeze → 执行
```

未经 Human 批准，不启动任何正式实验。

## 快速导航

- 研究想法与新意边界：`projects/verifiable-criteria/idea_direction_01_verifiable_criteria.md`
- 文献证据：`projects/verifiable-criteria/literature_notes/literature_review.md`
- 任务状态：`projects/verifiable-criteria/tasks.md`
- 协作规范：`AGENTS.md`
