# Research Problem Discovery 01 — Reasoning-based Reward Modeling

> 项目：verifiable-criteria
> 阶段：Research Ideation → Research Audit（Claude）
> 日期：2026-11-17
> 状态：**未批准的 Idea Discovery 文档**。不是 Research Proposal，不构成实验授权。
> 上游输入：`literature_notes/literature_review.md`（P01–P18）、18 篇本地 PDF、本轮 12 条外部原始文献
> 下游：待 Human 决策（见 §11）

---

## 0. 结论摘要

本轮的核心判断只有一句：

> **"评价标准的质量"目前不是一个可独立测量的对象，而是一个必须通过标准、执行者、证据与标签的联合行为才能被部分识别的潜变量；而"标准缺陷与执行缺陷不可分离"这一旧命题，作为"现有方法完全无法分离"的强断言，已经被至少四篇工作证伪。**

由此产生三条必须写入正式记录的结论：

**结论 1（旧命题必须弱化）。** 原命题"所有现有方法的过滤信号形如 `keep(C,ψ)=1[J(C,ψ,y,e)=l]`，只观测到标准缺陷与执行缺陷的复合误差，因此二者不可分离"——**方向正确，强度错误**。Mubric（2609.37322）用变异算子注入缺陷、RubricArmor（2610.05308）用"verifier 判定满分（s=1）∧ 独立 quality judge 确认缺陷存在（q=1）"的双条件、ReVeR（2610.18766）用 label-blind distillation 把偏好标签降级为"过滤器"而非监督信号，都已经构造出**能分离二者的干预设计**。仍然成立的弱命题是：**这些装置给出的是方向性判断，没有任何一个给出带归因误差界的定量分解**。

**结论 2（旧五属性框架已被标准化，不再新）。** 领域已收敛到三轴质量分类（discriminativeness / atomicity-coverage / redundancy-correlation control），RRD 已把失败模式命名成四类（coverage 不足、维度混淆、偏好方向错位、条目相关），ProRubric 已把"reward validity"作为独立概念提出。再把 Validity / Coverage / Discrimination / Consistency / Evidence-Validity 作为五个新属性分别建模块，是**重复劳动**。

**结论 3（最有杀伤力的一篇新文献）。** arXiv 2609.29709《Three Ways Classical Test Theory Misleads for LLM Judges》已经从测量理论层面形式化了"标准与执行不可分"：rubric 条目上算出的内部一致性系数**不包含 scorer facet**；把 judge 误差率固定在 4.72%，仅重设计条目库，KR-20 就在 0.01–0.68 之间移动；反之亦然。原文结论是 "item design and judge error are **not separately identified**"。这不是"还没人做"，而是"已经有人证明在现有统计口径下不可识别"。**因此本项目不能把可识别性当作前提，只能当作待验证假设，并必须声明识别条件。**

**结论 4（可识别性仍然值得做，但降级为支撑性贡献）。** 因为 ReVeR（2610.18766）、ProRubric（2609.38847）、Scaling Laws for GRMs（P02）已分别证明：rubric fidelity、preference accuracy、downstream utility 是三个会**相互背离甚至反向**的量。若不先建立归因，任何"提升标准质量 → 下游收益"的主张都无法被检验。所以这项工作应该定位为 measurement foundation，而**不是** headline mechanism。

---

## 1. 核验方法与证据等级

### 1.1 证据来源

本轮的核验分四层，不同层级证据强度不同，文中逐处标注：

| 等级 | 含义 | 本轮来源 |
|---|---|---|
| **V1 — 原文全文** | 本地 PDF 提取正文，可定位到具体段落与表格 | 6 篇（DeepSeek-GRM、Mix-GRM、OpenRubrics、R1-Reward、VideoScore2、VideoPhy-2） |
| **V2 — 官方摘要页** | ACL Anthology 摘要 + BibTeX 元数据；arXiv abs 页摘要 + 作者 | RubricBench、MM-VeriRubric、SVR、MRRG、GenRubric、ARTS-Hybrid、ProRubric、Auto-Rubric、RRD |
| **V3 — 原文 HTML 正文 / 官方摘要页** | arXiv HTML 正文片段（可定位原句）、arXiv abs 摘要页、ACL Anthology BibTeX | SVR、CARO、Mubric、RubricArmor、2607.27984、2609.29709、ReVeR、Autorubric、Rubric-ARM、RubricAtlas、RIPD |
| **V4 — 未核实** | 检索未命中，或仅有转述 | 见 §10 |

### 1.2 本轮完成的纠错

用户简报中列出的 10 条"Claude 上一轮声称的工作"，经原文核对，**三条必须纠正**（详见 §3）：SVR 的数值、CARO 的任务域、Mix-GRM 的测量对象。另有两条检索入口需要修正（见 §1.4）。

### 1.3 检索工具限制

- arXiv API 与 Semantic Scholar API 在本轮多次返回 HTTP 429（限流）。最终证据来源为 **arXiv abs 摘要页**与**arXiv HTML 正文**（可定位原句），以及 **ACL Anthology 摘要与 BibTeX**。未取得任何论文的 PDF 版式全文，因此表格中的精确数值除已注明者外，本文不引用。
- 未能访问任何代码仓库。因此"某工作是否真的实现了其声称的机制"这一问题，本轮**一律不回答**。
- 凡本文引用的数字，均可回溯到 §11.1 所列的已核实来源；未列入者不引用。

### 1.4 检索入口修正

| 简报给出的入口 | 实际内容 | 处理 |
|---|---|---|
| `aclanthology.org/2026.emnlp-main.2230/` → MM-VeriRubric | ✅ 正确。Cao, Wang, Li, Liu, Zhang, Wang, Wei, Zhou，EMNLP 2026 | 采用 |
| `aclanthology.org/2026.acl-long.1439/` → RubricBench | ✅ 正确。Zhou, Zhang, Wang, Lyu, Ming, Xu, Sun, Zheng, Kang, Liu, Ma，ACL 2026 Vol.1，pp. 未标注 | 采用。注意：该 URL 的 HTML 页面在抓取时一度返回了 MM-VeriRubric 的内容，**BibTeX 元数据是可靠的判据** |

> 记录这一点是因为它是真实的复现风险：同一个 DOI 前缀下若按 HTML 抓取而非 BibTeX 判定，会得到错误的论文。

---

## 2. 直接竞争工作：方法对照表

按"验证对象"而非"方法名称"组织。这是本节的核心：**这些工作测的不是同一个东西**。

### 2.1 主表

| 工作 | 出处 | 验证的**对象** | 装置 | 与"标准质量可独立识别"的关系 |
|---|---|---|---|---|
| **SVR** (Support Vector Rubrics) | arXiv 2606.08077，Sun, Li, Yu, Zhang, Ye | 标准在**偏好对**上的判别边界 | max-margin 学习；全局 rubric bank；prompt-conditioned selector；support-pair selection + adversarial hard negatives；推理时**只看 prompt** 检索 top-k | **部分反例**：把 rubric 生成与候选回答解耦（推理只 condition on x），消除"标准被回答反向诱发"的混杂。但**只用单一 judge**（GPT-OSS-120B）建 bank，作者自认 bank 会继承该 judge 的系统偏差 |
| **MM-VeriRubric** | EMNLP 2026 main.2230，Cao et al. | 标准能否检出**受控注入的错误维度** | task-aware visual clue extraction；evidence-grounded rubric；rubric-aligned rejected response（controlled error dimensions）；consistency checking | **最接近分离器**：缺陷是**构造注入**的，因此"检不出"可由构造保证归因到标准侧。局限：注入维度由数据管线定义，覆盖受限于该维度表 |
| **RubricBench** | ACL 2026 long.1439，Zhou et al. | 模型生成 rubric 与**人类 rubric 的差距** | 1,147 pairwise；多维过滤筛难例与表面偏差；每条样本配 expert-annotated atomic rubric，**strictly derived from instructions** | 纯 **Benchmark**。它把"标准本体"锚定在 instruction 上——这是五种 coverage 语义中的一种，不是全部 |
| **MRRG** | arXiv 2607.01830，Fu, Yang, Guo, Fan | 多角色抽取能否补足**维度盲点** | training-free, reference-free；多互补角色抽取 → 合并为可审计 scorer；用于 pairwise 验证与 GRPO/RLVR 奖励 | 用**角色冗余**对抗 coverage 缺失。属"多写几条标准"路线，不涉及归因 |
| **GenRubric** | arXiv 2608.29856，Chen, Li, Ai, Zhu, Chua, Zhang, Liu | 生成 rubric 与**专家 rubric** 诱导出的一致评价 | rubric-induced self-consistency：同 query 独立采样的 rubric 互为部分视图；cross-rubric comprehensiveness + group-level + criterion-level reward；RL；4B/8B/14B | 目标是**描述保真**，不是因果效用。**criterion-level reward 是最接近"标准级监督"的现有信号** |
| **RRD** | arXiv 2602.05125，Shen, Qiu, Whitehouse, Alazraki, Goel, Barbieri, Willi, Mathur, Leontiadis | 四条**命名失败模式**是否被修复 | recursive decompose-filter；命名 coverage 不足 / 维度混淆 / 偏好方向错位 / 条目相关；correlation-aware weighting | **已经完成了"多因混淆"的问题命名**。但修复靠启发式规则，不是从数据识别 |
| **ARTS-Hybrid** | arXiv 2610.13375，Qian et al. | **设计因子**（组织 / 操作化 / 聚合）各自的边际贡献 | 系统性机理分析 + adaptive rubric tree search + response-calibrated refinement | 结论："语义规范化本身不足以保证 criterion 级判断有效；复杂聚合增益有限"。这是**因子消融**，不是方差分解——它证明操作化重要，但不识别各占多少 |
| **ProRubric** | arXiv 2609.38847，Liu et al. | **聚合方式**对 reward validity 的决定作用 | 加法聚合导致 criteria 互相补偿；protocol-level dimension（全满足且失败子句未触发才计分）；离线分组，不改 optimizer | **最强的负面证据**：临床问诊中 rubric coverage 上升、held-out physician criteria 的 appropriateness 下降；改聚合后 +10.8。直接反驳"coverage 提升 = 质量提升" |
| **Auto-Rubric** | arXiv 2510.17314，Xie et al. | 标准的**判别性** | training-free；verification-driven refinement（在 held-out 偏好对上检验）；information-theoretic coding rate 压缩；分层 rubric；**仅用 70 个偏好对**即在 RewardBench2 得 80.91 | 判别性优化路线的代表。**"verify 后压缩"是 coverage/discrimination 权衡的显式形式** |
| **RubricArmor** | arXiv 2610.05308，Yang et al. | 标准是否**防得住 reward hacking** | 对抗演化：attack（构造满足当前 rubric 但未完成任务的回答）↔ repair（补标准使缺陷可检出）；成功攻击判据 `s(x,y_t;R_t)=1 ∧ q_t=1` | **装置最锐利**：verifier 判满分且独立 quality judge 判缺陷成立，二者同时成立才触发修补，因此该轮修补的**归因指向标准侧**。消融 71.69 / 72.80 / 77.09 见 §A.3 |
| **Mubric** | arXiv 2609.37322，Yang, Zhang, Lou, Chen | 标准是否**检得出已知注入的缺陷** | 变异测试：从真实 preferred/dispreferred 对中挖掘缺陷 → 抽象为可复用 mutation operator；对新任务注入缺陷，检查评分是否下降；未充分惩罚的缺陷用于补标准；**703 任务 / 4 域**，超最强基线 7.48 点 | **唯一的 criterion 侧真值锚点**：缺陷由构造保证存在，"检不出"可归因到标准侧。局限：缺陷空间由 operator 集合定义 |
| **CARO** | arXiv 2603.00451，Chu, Li, Yang, Copur-Gencturk, Krajcik, Shin, Tang | **误判模式**（confusion matrix 分解） | 混淆矩阵把混合误差拆成模式；对主导模式合成 fixing patches；diversity-aware selection；解决"rule dilution"；用于 **LLM 自动评分（教师教育、STEM）** | ⚠️ **领域错配**。不是"维度可分离性"，是"误差模式可分离性"；无 preference pair、无 policy、无 reward。方法学同构（复合误差按模式归因），但结论**不能外推到 GRM** |
| **ReVeR** | arXiv 2610.18766，Liu, Wang | rubric fidelity、preference accuracy、downstream utility 三者关系 | pointwise rubric RM；从 instruction 单独推导 hard rules + graded principles（切断 response-induced bias）；tool-augmented judging；label-blind distillation | **最强的概念性负面证据**：报告"generator RL 提高 preference accuracy 同时降低 rubric fidelity"；并明言**三个性质是不同的**（"distinct properties"） |
| **2609.29709** | arXiv 2609.29709，Zhu | 经典测量理论统计量能否隔离 judge | 三种单次施测、无需金标的信度统计量；模拟中固定 judge 4.72% 误差率，**仅改条目库设计使 KR-20 从 0.01 变到 0.68**；结论：判断关于 judge 的陈述需要金标或**变化的 scorer facet** | **形式化的非识别结论**。这是全表唯一直接攻击"标准质量可独立测量"的工作 |

### 2.2 对照表的读法

三行结论：

1. **判别性已经饱和。** SVR、Auto-Rubric、RRD、RubricArmor、GenRubric 都在优化"标准能不能把两个回答分开"。再提"Discrimination 是新维度"会被直接拒稿。
2. **coverage 已经有人证明它不是质量。** ProRubric 的 coverage↑ / validity↓ 是决定性的。任何把 coverage 当质量指标的设计必须先解释这个反例。
3. **没有一个工作输出带误差界的归因分解。** 这是全表唯一的空格。

---

## 3. 必须纠正的三处既有描述

### 3.1 SVR 的数值：不是 21 → 0.3，是 24.1 → 0.3，且量纲被误读

**原文表述**（ACL 2026 main.875 摘要，与 arXiv 2606.08077 HTML 一致）：

> "On RubricBench, SVR **narrows the gap to human reference rubrics from 24.1 to 0.3 points**."

**正确的读法：**

- 被测量的是 **RubricBench 上的 pairwise accuracy 点差**，参照物是 **human-rubric oracle = 83.1**。
- 24.1 = 83.1 − 58.97（自生成 rubric 基线）；0.3 = 83.1 − 82.8（SVR）。
- 因此 "24.1 → 0.3" 的语义是：**"模型自生成标准与人类专家标准之间的距离从 24.1 分缩到 0.3 分"**。
- 它**不是**"标准质量分从 21 降到 0.3"，**不是** reward error，**不是** policy 增益。原报告把量纲读成了"质量分下降"，方向与含义都错。

**更重要的自我限定**（arXiv 2606.08077 结论与局限段）：

> "We train the rubric bank on preference data from a single LLM judge (GPT-OSS-120B) … the bank inherits whatever systematic biases the training judge carries."
>
> "**our experiments only verify its discrimination quality on preference pairs and do not directly demonstrate that rubric-level gains transfer downstream to policy model optimisation.**"

第二句是本轮最重要的一句引用。它意味着：**该文作者自己明确否证了"标准质量提升 → 下游 Policy 收益"这一链条在其实验范围内的成立**。任何把 SVR 当作"标准质量已被验证为下游有效"的引证，都是误引。

### 3.2 CARO 的任务域：不是维度可分离性，是自动评分中的误判模式分解

原文（arXiv 2603.00451，2026-02-28）：

> "CARO leverages the **confusion matrix** to decompose monolithic error signals into distinct modes, allowing for the diagnosis and repair of specific misclassification patterns individually… Empirical evaluations on **teacher education and STEM datasets**."

对照简报中的描述"CARO：维度可分离性"——**两处错误**：

1. 对象错了：CARO 分解的是**误判模式**（false positive / false negative 及其亚型），不是 rubric **维度**之间的可分离性。
2. 领域错了：CARO 是 **LLM 自动评分（automated grading）** 的 prompt 优化工作，评估指标是 accuracy 与 Cohen's κ，没有 preference label、没有 reward、没有 policy。

**正确的借用方式：** CARO 提供的是"把混合误差按混淆模式拆开、再对主导模式定向修补"这一**归因卫生**方法，与 GRM 的 criterion/executor 分离**结构同构**（两者都是"把复合误差拆成可诊断的成因"）。可以引为方法学先例，**不能引为其结论适用于 reward modeling**。

### 3.3 Mix-GRM 的数值：测的不是标准质量，是推理结构与任务的匹配

简报要求"对 SVR 与 Mix-GRM 中有关标准质量 / 标准选择收益的不同数值，先核对测量对象、基准和单位"。核对结果：**这两个数值根本不在同一条因果链上，不存在"兼容性"问题，只有"不可比"结论。**

从本地 PDF（P03）实测：

| Mix-GRM 的数值 | 实际测量对象 | 基准 / 单位 |
|---|---|---|
| 3.5% | Mix-GRM 相对**最强开源基线**的平均提升 | 5 个 benchmark 的平均分，SFT/RLVR 阶段 |
| 75.1 → 79.4 (+4.3)，Base 73.3 → 76.9 (+3.6) | **RLVR 增益** | 同上；RLVR = GRPO，奖励为 verdict 与 ground-truth human label 一致 |
| **72.2 → 70.2**（B-CoT on Correctness） | 把 breadth 机制**强加**到客观正确性任务上造成的**下降** | 同 benchmark 内该域平均分 |
| **68.2 → 65.9**（D-CoT on Preference） | 把 depth 机制**强加**到主观偏好任务上造成的**下降** | 同上 |
| 9K vs ≈2.5M | 样本效率对比（FARE-8B） | 训练样本数 |

**判定：** Mix-GRM 的自变量是 **Breadth-CoT / Depth-CoT 的机制分配**，不是 rubric 的质量或选择。`68.2 → 65.9` 说的是"机制与任务错配会掉 2.3 分"，**不是**"标准质量下降导致 2.3 分损失"。

因此：

> **不能用任何解释把 "SVR 24.1→0.3" 与 "Mix-GRM 68.2→65.9" 说成兼容。** 前者是"标准描述性 vs 判别性目标不匹配造成的、相对人类标准的距离"，后者是"推理结构被强制错配造成的域内平均分下降"。两者共享的唯一成分是"都以人类偏好标签作为验证目标"——而这恰恰是两者共同的方法学弱点（§6 问题 D）。

### 3.4 由 3.3 推出的一条独立观察

Mix-GRM 与 SVR 都**以偏好标签为验证目标**：SVR 在 preference pairs 上学边界；Mix-GRM 的 RLVR 奖励是 verdict 是否等于 human label。这意味着**两者都没有用"标准"验证过"标准"**——都在用下游标签间接验证上游对象。这一共同点支持 §6 的核心论断：现有全部方法的验证信号都在**标签侧**，而不是**标准侧**。

---

## 4. VideoPhy-2 规则级一致性核查

简报要求："核查标注定义、统计口径和适用范围。不能把某一任务的一致性上限推广到所有 Rubric。"

从本地 PDF（P15）实测得到的事实：

| 项目 | 原文事实 |
|---|---|
| 规模 | 197 种真实动作 → 3,940 个 prompt |
| 规则来源 | **VLM 两阶段生成候选规则**（先 caption，再 rule generation），人类随后 **verify rule violations、suggest missing rules** |
| 标签空间 | 每条规则三级：**violated (0) / followed (1) / cannot be determined, CBD (2)** |
| 标注者数 | 每视频 **3 名**；先经结构化训练，用 1–5 锚定量表 |
| 总体一致性 | **80%**（percent agreement），作者称与 prior work 可比 |
| 规则级 / 法条级一致性 | **70%–80%**；弹性（elasticity）偏低，反射（reflection）可达 80% |
| 联合表现 | 最佳 Wan2.2-27B-A14B 55.4% 全量 / 47.7% hard subset；Wan2.1-14B 32.6% / 22% |

**四条限制，任何引用都必须同时说明：**

1. **80% 是 percent agreement，不是 chance-corrected κ。** 在三级标签上不做机会校正会系统性高估一致性。原文未报告 Cohen's κ。
2. **规则是机器提议、人工增删的。** 因此"规则本体"本身不是纯人工产物；"规则可观察性"的上界受 VLM 提议质量约束。
3. **CBD 是一个第三类别，不是缺失值。** 它把"证据不足无法判断"编码进标签空间。这只有在**物理规则的天然三态**下才可行——不能移植到"这段话是否更有帮助"这类无天然三态的标准上。
4. **适用范围是"某条物理规则是否可观察地被违反于某段生成视频"。** 它既不是 criterion quality，也不是 coverage，也不是 reward validity。

**判定：** 该 70–80% 是**单一任务族（视频生成的物理常识）内、针对可观察物理规则的三级分类一致性**。把它当作"Rubric 级一致性的天花板"是**范畴错误**。它可以支持一个弱得多的说法：*在规则可被视觉直接判定的任务上，三人标注的一致性上界约为 80%（未做机会校正）*。这个数字对本项目的正确用法是：**作为"如果一个标准体系连这个上界都达不到，它一定有问题"的粗筛线**，而不是目标值或可迁移常数。

---

## 5. 对象分解与因果 / 测量关系图

### 5.1 把"标准质量"拆成五个独立研究对象

简报要求"将标准生成、标准质量、标准执行、标准聚合、下游行为拆成不同研究对象"。以下是本轮采用的分解。**关键点：这五个对象各自有独立的自变量、独立的观测通道、独立的可识别条件。**

| 对象 | 符号 | 自变量 | 唯一可观测量 | 识别条件 |
|---|---|---|---|---|
| **生成** | G | prompt x（± 候选对 y₁,y₂） | 生成文本 | 直接可观测（文本存在），但"生成得好不好"不可直接观测 |
| **标准内容** | C = {c₁..c_K} | G 的输出 + 注入约束 | 文本 + 是否存在某维度 | 需要**本体参照**才能谈覆盖 |
| **执行** | J | C, x, e, y₁, y₂ | 逐条 verdict vₖ | 直接可观测（verdict 存在），但"执行对不对"需要 criterion 级真值 |
| **聚合** | A | {vₖ}, {wₖ} | 标量 r 或偏好标签 | 可直接操纵（改 A 看 r 变化） |
| **下游** | π | r | 策略输出分布 | 需要独立评测，不能用 r 自证 |

**核心不可观测结构：** 观测到的只有 r（或 ŷ）。C 的质量、J 的质量、e 的充分性、ℓ 的分歧，四者都**只通过 r 显现**，而 r 是四者的复合。

### 5.2 因果 / 测量关系图

```text
                              ┌──────────────── 循环边（问题 D） ───────────────┐
                              │                                                 │
                              ▼                                                 │
   x ──────────────► G ──────────────► C ───────┐                               │
   │                 ▲                          │                               │
   │                 │  (contrastive 边；         ├──► 覆盖 O（本体）              │
   │                 └── SVR 切断此边)            │                               │
   │                 y₁,y₂ ─────────────────────┘                               │
   │                                            │                               │
   ├──────────────► e ──────────────────────────┼──► 可观察性 O_e（问题 C）       │
   │                                            │                               │
   ├──────────────► y₁,y₂ ──┐                   │                               │
   │                        ▼                   ▼                               │
   │                    ┌──────────────────────────────┐                        │
   └───────────────────►│            J                 │──► v = {vₖ}             │
                        └──────────────────────────────┘         │              │
                             ▲                                   │              │
                             │ 共享盲点（问题 D）                  ▼              │
                             │                          ┌─────────────────┐     │
                             └──────────────────────────│        A        │──► r │
                                                        └─────────────────┘     │
                                                                 │             │
                                                                 ▼             │
                                                              π（policy）───────┘
                                                                 │
                                                                 ▼
                                                              held-out 评测

   ℓ（金标）───► 训练信号，同时喂给 G 和 J ──────────（这是循环性的物理来源）
```

**读图要点：**

- **C 与 J 到 r 的两条路径在 v 处汇合。** 这是所有识别困难的几何来源：只要只看 r，两条路径的贡献就叠加。
- **A 是乘性调节器。** ProRubric 证明 A 能把"同样的 C"变成不同的 r（+10.8），所以 A 不是无害的实现细节。
- **ℓ → (G, J) 这条边就是问题 D 的物理来源。** 只要 G 和 J 都从同一个 ℓ 学，二者就共享 ℓ 的盲点。Rubric-ARM 的交替 RL 优化恰好**最大化**这条边的强度。
- **O_e 是一条被忽视的边。** 证据不足时，C 是否正确**在原理上不可判定**——这不是执行错误，也不是标准错误，是**可判定性缺失**。VideoPhy-2 的 CBD 与 Autorubric 的 abstention 是仅有的两个显式通道。

---

## 6. 问题 A–D 的正面回答

### 问题 A：标准质量与标准执行质量是否可识别地分离？

#### A.1 形式化

设观测 ŷ = A({J(c_k; x, e, y₁, y₂)}_{k=1..K})，金标 ℓ。

**平凡命题（不足以构成贡献）**：映射 (C, J, A) → ŷ 是多对一的。构造反例：取维度 d；
- 组合 1：C¹ 覆盖 d，但 J¹ 在 d 上误判；
- 组合 2：C² 完全不含 d，J² 在已含维度上全对。

两者可产生同一个错误 ŷ。**这只说明"复合分不能唯一分解"，是任何复合指标都有的性质，不构成研究问题。** 如果本项目只停留在这一步，它就是无价值的。

**真正的命题（部分可识别性）**：需要问的是"加什么最小观测，两个 facet 才可分离"。标准答案来自 generalizability theory 的交叉设计：

| 想要识别的 | 最小观测要求 |
|---|---|
| criterion facet（条目难度/质量） | 同一 criterion 由 **≥2 个 executor**（或 ≥2 个独立 occasion）评同一对 |
| executor facet（评分者效应） | 同一 executor 评 **≥2 个 criterion**，且有跨条目的难度结构 |
| criterion × executor 交互 | 必须**交叉**（crossed），不能嵌套（nested） |
| **构念效度**（不只是信度） | 额外需要一个**已知真值的操纵**：注入缺陷、程序化检查、或 criterion 级人工切片 |

**关键推论：单 judge 单次观测下，criterion facet 与 executor facet 完全混淆。** 这不是工程限制，是设计限制。2609.29709 用可计算的方式给出了同一结论：内部一致性系数里**没有 scorer facet**，固定 judge 误差后重设计条目库，系数从 0.01 走到 0.68。

#### A.2 已有方法做到了哪一步（这就是"是否存在反例"的答案）

**存在反例，且不止一个。** 下表是本节最重要的产出：分离**装置**已经存在，定量**归因**不存在。

| 装置 | 代表工作 | 分离了什么 | 没有分离什么 |
|---|---|---|---|
| **受控注入缺陷（变异算子）** | Mubric (2609.37322)；MM-VeriRubric | 标准能否检出**已知存在**的缺陷。缺陷的真值由构造保证 → criterion 侧归因成立 | 检不出时，是"标准没写"还是"judge 没执行"，仍未分离；缺陷空间由 operator 集合定义 |
| **满足 ∧ 有缺陷 双条件** | RubricArmor (2610.05308) | **最锐利的一个**：verifier 判定 `s(x,y_t;R_t)=1`（评分侧确认 y 满分），且独立 quality judge 判定 `q_t=1`（缺陷存在且显著降低质量）⇒ 标准缺失，而执行侧被确认合规 | quality judge 自身的误差率未被估计——这个问题 D 直接命中 |
| **label-blind distillation** | ReVeR (2610.18766) | 把偏好标签从"监督信号"降级为"教师输出过滤器"，切断 rubric 对候选回答的依赖 | 不提供 criterion 级真值 |
| **按错误模式归因** | CARO (2603.00451) | 混合误差按混淆模式拆开、对主导模式定向修补（**领域：自动评分**） | 不适用于 preference reward；无 policy |
| **消融式因子归因** | ARTS-Hybrid (2610.13375) | 组织 / 操作化 / 聚合三个设计因子各自的**边际**贡献 | 边际增益 ≠ 方差分量；不做归因误差界 |
| **交替联合优化** | Rubric-ARM (2602.01511) | 什么都没分离——它**主动耦合**生成器与 judge（rubric 生成作为 latent action，与 judge 交替优化） | 恰恰是问题 D 的最坏情形 |
| **交叉设计方差分解** | **无** | — | **全部** |

#### A.3 是否存在"两个不同的 (C,J) 组合产生同一观测"的可构造反例？

**存在，而且不需要构造——RubricArmor 的 ablation 就是现成的经验证据。**
其消融：Adv.-only 71.69、Resp.-only 72.80、Full 77.09（macro accuracy）。Adv.-only 与 Resp.-only 是两条**机制不同**的路径，单独使用时的观测表现接近（差 1.11 分），说明**单看最终宏平均无法区分"补标准覆盖"与"校准判断"两种干预**。Full 才把它们分开（+4.29 / +5.40）。

这说明：**识别需要两个装置同时在场，缺一不可。**

#### A.4 加什么最小观测才能识别

**答案（本轮的核心方法学结论）：**

> 最小观测 = **一个交叉设计** + **一个非语言锚点**。
>
> - 交叉设计：同一 criterion 由 ≥2 个 executor 评（跨 judge），且同一 executor 评 ≥2 个 criterion（已在 rubric 内天然满足）。
> - 非语言锚点：**至少一个不是 LLM 的证据源**——注入缺陷（真值由构造保证）、程序化检查（代码/规则）、或小规模 criterion 级人工切片。
>
> **只扩展 judge 数量是不够的**：同族 judge 共享预训练盲点，加第二个 judge 带来的是高度相关的信息，无法打破共享盲点。这直接引出问题 D。

#### A.5 这种可识别性是否值得研究，还是仅属测量问题？

**判断：值得，但必须降级定位。**

支持：
- ReVeR（2610.18766）报告 **generator RL 提高 preference accuracy 同时降低 rubric fidelity**；并在 GRPO 下观察到"最准确的 pairwise rubric RM 训练出的 policy 会自证合规，在 IFEval 上低于 base policy"。→ 三个量**背离**，甚至**反向**。
- ProRubric：coverage↑ / held-out validity↓。
- Scaling Laws for GRMs（P02）：静态准确率更高的 Thinking Judge，Answer-Only rewarder 在 **8 组匹配设置中 7 组**给出更高的 peak 与 top-5 policy Elo（例外是最小 policy + 4B judge）。**但需注意**：200 次自助重采样下，8 组中只有 **5 组**的 peak 差与 5 组的 top-5 差的 95% 区间不含零——即这一反转是**方向一致但统计强度有限**的。
- SVR：作者自认未验证下游传递。

这四条合起来意味着：**"标准更好 ⇒ 下游更好"目前是一个已被多方观察到会失效的假设。** 在归因建立之前，无法判断失效是因为标准侧、执行侧还是聚合侧。因此归因是**任何后续主张的前提条件**。

限制：
- 它**不是**机制贡献，是 measurement foundation。写成 headline 会被审稿人判为"只是评价方法学"。
- 它的价值完全依赖于它支撑的后续主张。若后续主张（候选问题 2）不成立，它单独的价值有限。

---

### 问题 B：Rubric 的"覆盖"应该相对于什么定义？

#### B.1 五种语义的现状

简报列出的五种 coverage 语义，逐条核对后，**其中四种已有可操作化**，这直接否证了"coverage 是空白"的假设：

| coverage 语义 | 已有操作化 | 出处 |
|---|---|---|
| **对任务本体（ontology）的覆盖** | 类型级问题树：从同一研究类型的多份专家报告中抽取**反复出现的分析问题**（dimension → question → required result → checkpoint）；实测单份报告只覆盖其类型约一半的分析问题 | RubricAtlas (2610.36878)：3,079 份中文金融研究报告，99 个研究类型，两 judge 管线 checkpoint κ=0.71、agent 排序 Kendall τ=0.87 |
| **对用户明确要求的覆盖** | atomic rubric **strictly derived from instructions** | RubricBench (ACL 2026 long.1439) |
| **对候选回答差异的覆盖** | discriminative margin on preference pairs | SVR；RRD；Auto-Rubric |
| **对人类偏好差异的覆盖** | 与专家 rubric 诱导评价的一致性 | GenRubric；MM-VeriRubric |
| **对下游 Policy 失败模式的覆盖** | **部分**：held-out physician criteria；held-out criteria shift（122 条 held-out criterion / 203 例） | ProRubric；2607.27984 |

#### B.2 真正未解决的测量问题

简报提出的两个反例——"高区分力但遗漏任务重要要求"与"高覆盖但无区分力"——**都已被实证**：

- 前者 = criterion deficiency。RubricArmor 的两个 case study 是教科书例子：直接生成的标准只检查"首都是巴黎"，漏掉"巴黎在亚洲"；只写"定义收敛"，漏掉收敛条件。
- 后者 = ProRubric 的 coverage↑/validity↓。

所以**问题不是"没人命名"，而是"没有一个把 (3) 和 (1) 联系起来的可迁移系数"**。

更进一步，2607.27984 给出了一个关键的**分离式**估计。在其直评设定中：rubric-ablated 对照 74.27、monolithic rubric (rank 64) 77.50 → **带标准相对无标准的净增益约 3.2 分**；而**反事实替换标准**（只换 rubric、其余全部锁定）使 locked-test 准确率从 76.38 掉到 73.72，配对差 −2.66（95% CI −3.26 至 −2.09）。

这两个数一起给出了一个比"标准有没有用"更强的判断：

> **标准确实因果地控制着决策，但它们承载的信息量相对回答本身是有限的（约 3 分 / 约 77 分的量表）。** 因此"对候选回答差异的覆盖"高，主要可能反映回答本身可分，而不是标准起到了区分作用。

**注意归因的边界：** 该文自己的外部对比并不显著——1.9 分增益的 criterion-bootstrap 区间为 −0.5 至 +4.4（p=0.138）。所以这个"约 3 分"应作为**效应量上界**使用，不能当作精确估计。

**判定：不要创造新的 Coverage 指标名称。** 应当研究的是：

> 在固定 executor 与固定 aggregation 的条件下，五种 coverage 语义之间的**偏序关系与可迁移性**——特别是 (1)(2) 能否预测 (5)，以及 (3) 是否是 (5) 的可靠代理。

ReVeR 的结论（"rubric fidelity, preference accuracy, and downstream utility are **distinct properties**"）已经是这个问题的直接负面答案的一半。

---

### 问题 C：标准在运行期是否会发生错配？

#### C.1 已有诊断机制（比预期多）

| 错配类型（简报原文） | 已有机制 | 出处 | 是否需要额外信息 |
|---|---|---|---|
| 标准适用于任务、不适用于当前样本 | **反事实标准替换**：只换 rubric、其余字段全部锁定 | 2607.27984（76.38 → 73.72，配对 −2.66，CI −3.26 至 −2.09） | 否，只需替换 |
| 标准适用于样本、但无可观察证据 | **CBD 三态标签**；**abstention 配置** | VideoPhy-2；Autorubric (2603.00077) | 需要标准体系支持"不可判定" |
| 标准可观察、但与其他标准冲突 | 加法聚合的补偿效应；protocol-level 分组（全满足才计分） | ProRubric (2609.38847) | 需要跨 criterion 的分组结构 |
| 标准正确、执行者在特定维度系统性盲点 | **criterion 与 judge 的分离式消融**；judge-family 差异；异族 judge panel | ARTS-Hybrid (2610.13375，操作化因子)；Autorubric（"systematic judge-family differences"）；PoLL (2404.18796) | 需要 ≥2 judge，且**异族** |
| 训练分布有效、Policy 优化后失效 | **RIPD**：benchmark-compliant 的 rubric 精修在 target 域诱导系统性偏移，helpfulness 最多 −9.5%、harmlessness 最多 −27.9%，并沿 alignment 管线传播为 policy 级漂移 | 2602.13576 | 需要 benchmark/target 双域切分 |
| 静态指标好、在线失效 | 静态准确率与在线 Rewarder 排序反转 | Scaling Laws for GRMs（P02） | 需要在线 RL |

**其中 RIPD 是最直接的命中**：它证明"标准在验证集上合规、在目标域上系统性偏移"是一个**可构造、可测量、可传播**的现象，量级到 27.9%。简报中"标准在训练分布中有效，在 Policy 优化后的输出分布中失效"这条，**已经被独立研究并命名了**。

#### C.2 仍然缺失的：运行期（per-sample，online）的可信度判定

现有全部机制的共同点是**离线**：验证集上的合规性、held-out criterion 的偏移、离线计算的方差分量。**没有一个给出"对当前这一个样本，是否应当相信该 rubric"的在线判定。**

Autorubric 的 abstention 最接近，但它是**配置项**（用户设定策略），不是**因果判定**（基于本条样本的证据充分性）。

这个缺口是真实的，但**窄**。它的正确形态是：把 O_e（可观察性）从"离线统计"变成"在线可估计量"。这在有天然三态的任务（物理规则）上可行，在开放式偏好任务上是否可行，是一个**开放的经验问题**，不是显然的。

---

### 问题 D：标准验证是否存在循环论证？

#### D.1 多模型共识是否真正独立？

**没有证据表明是，且已有文献明确警告不要假设。** 三层证据：

1. **SVR 自认**：bank 从单一 judge（GPT-OSS-120B）的偏好数据训练，未纳入人工标注、多 judge 集成或轻量蒸馏 judge，因此 "the bank inherits whatever systematic biases the training judge carries"。
2. **Verga et al., PoLL (2404.18796)**：多 judge panel 确实降低 intra-model bias——但其机理是 **"combination of disjoint model families"**，即**异族**；这恰好说明**同族** panel 不具备这一性质。独立性来自"家族不相交"这一设计约束，而非 judge 数量。
3. **Rubric-ARM (2602.01511)**：把 rubric 生成当作 latent action 与 judge 联合优化，共享同一 judgment-accuracy 目标。→ 这不是"可能相关"，是**结构上故意相关**。

#### D.2 同一模型生成对照样本并验证 Rubric 是否会自我确认？

**会，而且有直接证据。** MM-VeriRubric 的"controlled error dimension"是**同一数据管线构造**的缺陷，再用"consistency checking"验证——缺陷空间与验证空间同源。Mubric 的 mutation operator 是**从真实 preferred / dispreferred 回答对中挖掘**的缺陷抽象而成；这比凭空合成更接近真实失败，但 operator 集合仍然**定义了该体系能看见什么**，也就定义了它不能看见什么。

这不是致命缺陷（构造性缺陷仍有真值），但它意味着：**报告"检出率 95%"时，95% 的分母是被这套 operator 构造出来的缺陷，不是世界上所有缺陷。**

#### D.3 各类外部证据分别能提供什么

| 证据源 | 能提供 | 不能提供 |
|---|---|---|
| 独立人工（criterion 级切片） | **构念效度**的唯一硬证据 | 规模；无法覆盖长尾 |
| 程序化约束（代码 / 规则 / 沙箱） | 对**可形式化**部分给绝对真值 | 覆盖窄；规则漏洞可被 policy 利用（VideoRLVR 已指出） |
| 反事实干预（标准替换 / 反向 / 保留改写） | 因果归因：标准是否控制决策 | 不给"标准对不对"的真值，只给"标准是否起作用" |
| 注入缺陷（mutation） | 已知真值的 criterion 侧锚点 | 受限于缺陷分类体系 |
| 外部模型（异族） | 打破同族共享盲点 | 引入自身盲点；无法证明独立 |

**关键：这五类不是可替代的。** 人工给效度、程序给真值、反事实给因果、注入给覆盖锚点、异族模型给去相关。**任何单一来源都不足以估计验证器自身的误差率。**

#### D.4 如何估计验证器本身的错误率——这是本轮最干净的一个开放问题

经典工具是 **Dawid–Skene**（多标注者潜类别模型，联合估计每个标注者的误差率与每个样本的潜真值），另有 generalizability theory 的交叉设计方差分解。**本轮检索未找到它们在 rubric verifier 误差率估计上的直接应用**（该判断受 §1.3 的检索限制约束，属待核项，见 §11.1）。

**形式困难（这是真正的理论缺口）：**

Dawid–Skene 需要标注者**条件独立**。同族 LLM judge 违反此假设。设 k 个 judge，两两相关 ρ 未知。则：

> **verifier 的误差率在 ρ 未知时不可识别。**

因为"所有 judge 一致地错"与"所有 judge 一致地对"在观测上无法区分。需要一个**类型不同**的锚点（人工 / 程序 / 注入）来打破，或者对 ρ 施加限制性假设。

这就是 2609.29709 的 caution 所指向的、但它**没有解决的问题**：它证明现有统计量误归因，并给出四条报告纪律，但**没有给出在相关 judge 下的可识别估计量**。

**判定：这是一个形式清晰、可证伪、成本低（纯推理）、且确实空白的缺口。** 它的唯一风险是"看起来像统计学论文而不是 RM 论文"——定位需要谨慎。

---

## 7. 分类裁决：成熟 / 仅 Benchmark / 定义不清

简报要求"判断哪些问题已有成熟解决方案，哪些只有 Benchmark、尚无可靠机制，哪些连问题定义都不清楚"。裁决如下：

### 7.1 已有成熟解决方案（应停止投入）

| 问题 | 成熟度 | 依据 |
|---|---|---|
| 生成更好的 rubric（coverage、原子性、非冗余） | **饱和** | RRD、Auto-Rubric、GenRubric、MRRG、RubricArmor、ARTS-Hybrid 并行存在；三轴质量分类已成共识 |
| 判别性 / 边界学习 | **单设定内已解** | SVR（82.8 / oracle 83.1），但作者自认不迁移下游 |
| Rubric 质量分类学 | **已标准化** | 三轴（discriminative / atomicity-coverage / redundancy）＋ RRD 四失败模式；再造五属性是重复 |
| 聚合方式的补偿问题 | **近期已解** | ProRubric +10.8（分组式聚合）；AutoRubric 的可配置权重与聚合选项 |
| 人工 vs 模型 rubric 差距的度量 | **Benchmark 已就位** | RubricBench 1,147 pairs |

### 7.2 只有 Benchmark / 装置，尚无可靠机制

| 问题 | 状态 |
|---|---|
| rubric 层面的可验证性（不是偏好层） | RubricBench 给了度量，没给机制 |
| 反事实 / 注入式标准验证 | 装置齐全（Mubric 的变异注入、RubricArmor 的双条件、2607.27984 的反事实替换、ReVeR 的 label-blind 解耦），**未系统化、无统一估计量** |
| 运行期漂移诊断 | 现象已命名、量级已测（RIPD 9.5%/27.9%），**无在线判定** |
| 标准质量 → 下游效用的传递 | **只有背离证据**（ReVeR、ProRubric、Scaling Laws、SVR 自认），无函数关系 |

### 7.3 连问题定义都不清楚

只有一条，但它是本项目的地基：

> **"评价标准的质量"作为一个独立可测量的对象，其定义域、识别条件与误差模型。**
>
> 现状是：所有人都用"下游标签"间接定义它，而三条独立证据线（ReVeR、ProRubric、Scaling Laws）表明这个间接定义会失效甚至反向。**没有一个工作把"标准质量"写成带有明确识别条件的函数，并声明它在什么条件下可估、什么条件下不可估。**

这正是 2609.29709 触及但未完成的：它说"不可分离识别"，但停在 caution，没给识别条件。

---

## 8. 三个候选问题

每项含：最近工作、失败边界、可证伪假设、最小实验、成本。**按成本递增排序。**

### 候选问题 1（低成本，支撑性）：相关执行者下，rubric verifier 的误差率是否可估？

**问题陈述。** 当验证标准的验证器（verifier / meta-judge）与标准生成器同族时，它的错误率能否被估计？在 k 个相关性未知的 judge 下，verifier 误差的识别集是什么？哪一类锚点以最小代价把识别集压到可用宽度？

**最接近的已有工作。**
- 2609.29709：证明现有信度统计量误归因，给出报告纪律，**未给估计量**。
- Dawid–Skene：经典潜标注者模型，被引用但未用于 rubric verifier。
- Verga et al. 多 judge panel：假定独立性。
- RubricArmor 的 independent quality judge：其误差率未被测量。
- SVR：单 judge bank，自认污染。

**失败边界。**
- 必须至少有一个**类型不同**的锚点。同族第二个 judge 不构成锚点。
- 注入缺陷锚点只覆盖其缺陷分类体系内的空间。
- 若 judge 间相关 ρ 本身随样本变化（异方差），识别集可能不可用 → 这是真实的否定风险。

**可证伪假设（H1）。**
> 同族第二个 judge 关于第一个 judge 错误的边际信息量接近零；而一个程序化锚点或注入缺陷锚点在**单位成本**上贡献显著更多。

具体化为可测形式：在固定 rubric 集与已知注入缺陷上，比较五种配置各自的**自身误差率**（以小型人工 criterion 级切片为参照）：
(a) 单 judge；(b) 同族 2 judge；(c) 异族 2 judge；(d) judge + 程序化锚点；(e) judge + 注入缺陷锚点。

若 (b) 的误差率下降幅度与 (c)(d)(e) 无显著差异（即同族冗余已足够），**H1 被否证**，则廉价 ensembling 已解决 verifier 问题，本方向无必要。

**最小实验。** 纯推理，无需训练。一个任务族、约 1–2K 样本、3 个 judge 家族、一套注入缺陷体系、约 200–300 条 criterion 级人工切片作为参照。产出：五种配置的误差率及自助法区间；相关结构估计；识别集宽度。

**为什么它排第一。** 它是候选 2 和 3 的**前置条件**：没有 verifier 误差估计，候选 2 的"下游收益"无法排除验证器自证；候选 3 的方差分量无法可靠估计。而且它最便宜、最快出结论、且有明确的否证路径。

**风险。** 定位可能被判为"统计学而非 RM"。缓解：必须以"rubric verifier 的自证问题"为叙事，而不是以"信度系数"为叙事。

---

### 候选问题 2（高成本，核心）：标准质量到下游 Policy 的传递系数是否存在、是否为正？

**问题陈述。** 在固定 executor 与固定 aggregation 的条件下，标准侧的改善（以归因后的 criterion facet 度量）能否预测 held-out Policy 收益？该传递系数是否随训练预算改变符号？

**最接近的已有工作。**
- **ReVeR (2610.18766)**：直接证据——"generator RL improves preference accuracy **while reducing rubric fidelity**"；GRPO 下"最准确的 pairwise rubric RM 训练的 policy 自证合规、在 IFEval 上低于 base"。结论句："rubric fidelity, preference accuracy, and downstream utility are **distinct properties**"。
- **ProRubric (2609.38847)**：coverage↑ / appropriateness↓，改聚合后 +10.8。
- **Scaling Laws for GRMs（P02）**：静态准确率更高的 judge，8 组匹配中 7 组在线 Rewarder 更差（但仅 5/8 的区间不含零）；中间 checkpoint 可能优于末端；作者明言该差异**未能识别机制**，且训练目标与回答格式同时变化。
- **SVR**：作者自认不验证下游。
- **Rubric-ARM (2602.01511)**：摘要声称在 offline 与 online RL 下均显著改善下游 policy alignment，但**摘要未给出具体数值**，本轮未取得其表格。这是目前唯一报告下游改善的正面证据，但其设计（交替联合优化）使 baseline 与 treatment 难以公平比较；在核到具体设置前，**不应引用任何增益数字**。

**失败边界。**
- 单一任务族、单一 policy 规模、单一聚合规则下的结论不可外推。
- 必须用**独立评测**测 policy，不得用 GRM 自身奖励（AGENTS.md 与既有 P4 原则均要求）。
- 混淆项：policy 对 under-covered criteria 的利用（reward hacking）会伪造"标准质量变差"的效果。必须用 held-out 独立 judge 加人工切片分离。

**可证伪假设（H2）。**
> 存在一个训练预算阈值 B\*，使得当 RL 预算超过 B\* 时，提升标准鲁棒性反而降低 held-out appropriateness；即传递系数在预算维度上**变号**。

若在预注册的预算扫描中不出现变号，且传递系数在各 criterion 强度水平上统计不可分，**H2 被否证**——这直接触发 §9 的停止条件。

**最小实验。** 2 个任务族 × {弱标准集, 强标准集} × {短预算, 长预算} 的 2×2×2 设计；policy 由独立 held-out judge 加人工切片评测；报告四项分离指标（criterion facet 归因分、静态准确率、held-out appropriateness、policy 独立成绩）。需 Codex 实现与 GPU 训练。

**为什么它排第二。** 它是项目的 **thesis**：如果标准质量不能传递到下游，"让标准可验证"就失去了效用论据。但它最贵，且**最可能给出负结果**——所以必须先由候选 1 与 3 建立测量基础，再投入。

**风险。** 若结果为负，本方向的**全部**效用论证失效（见 §9 主动停止条件）。这是必须由 Human 事先批准的赌注。

---

### 候选问题 3（中低成本，方法学）：四因归因分解——criterion / executor / evidence / label

**问题陈述。** 在交叉设计加非语言锚点的条件下，能否给出带误差界的四因方差分解？各因子的相对份额是否按任务族系统变化（客观/可验证 vs 主观/开放式）？现有分离装置（注入缺陷、双条件、标准替换、criterion-preserving 改写）能否组合成一个统一的估计量？

**最接近的已有工作。** 见 §A.2 全表。特别是：
- RubricArmor 的双条件（满足 ∧ 有缺陷）——最锐利的单点装置；
- Mubric 的注入缺陷——唯一的 criterion 侧真值锚点；
- 2607.27984 的标准替换 + response-only 对照——因果归因；
- ARTS-Hybrid 的操作化因子消融——执行侧归因；
- 2609.29709——非识别的形式化结论。

**失败边界。**
- **单 judge 管线（如 SVR 的设定）下完全不可识别**，所以主张必须显式声明适用范围。
- evidence 因子的分离需要一个"不可判定"通道（CBD 式），而开放式偏好任务是否有天然三态是开放的经验问题。
- label 因子（金标分歧）需要重复标注，成本随样本线性增长。
- 注入缺陷与 judge 盲点可能共线（若 judge 恰好对某类缺陷不敏感），导致两因不可分——**这是最实质的失败风险**。

**可证伪假设（H3）。**
> 在交叉设计 + 注入锚点下，criterion facet 与 executor facet 的方差分量均不可忽略，且二者比值按任务族系统变化：客观/可验证任务上 **executor 主导**，主观/开放任务上 **criterion 主导**。

若实测发现 executor 方差 ≈ 0（给定合格标准时 judge 近乎完美执行），**H3 被否证**，则归因问题退化，工作重心应全部转向标准生成——而标准生成已饱和，等价于本方向终止。

**最小实验。** 纯推理。单任务族、约 1–2K 对；3 judge × 完整 rubric 交叉；注入缺陷作锚点；CBD 式弃权通道；用 G-theory / 带相关项的潜类别模型估方差分量并报区间。

**为什么它排第三。** 它是候选 1 与候选 2 之间的桥梁——把"verifier 误差"扩成"四因分解"，并为候选 2 提供 criterion facet 的度量。它的独立性较弱（依赖候选 1 的结论），但一旦候选 1 成立，它是最自然的下一步。

**风险。** 注入缺陷与 judge 盲点共线会使分解退化。缓解：用至少两个来源不同的缺陷体系（如 Mubric 的 operator + 人工构造的对抗样本）。

---

## 9. 停止条件

简报要求"至少提出一个'如果现有方法已足够好，我们应该停止该方向'的停止条件"。以下三条按优先级排列，**建议全部预注册**。

### 9.1 主动停止条件（主）

> **若候选问题 2 的传递系数在预注册的扫描中不出现变号，且在两个任务族、两个预算水平上，criterion 强度操纵对 policy 独立成绩的效应 95% 区间均包含零，则停止本方向，并以负面结果形式发表。**

理由：本方向的全部效用论据是"让标准可验证 → 下游 policy 更好"。SVR 作者自认未验证该链条；ReVeR 观察到 fidelity 与 utility 背离；ProRubric 观察到 coverage↑/validity↓；Scaling Laws 观察到在线排序反转。**四条独立证据使"该链条可能不成立"成为一个现实的、而非稻草人的可能性。** 若实测确认不成立，继续投入是沉没成本。

这条停止条件的价值在于：它把"我们可能做错方向"变成**事前可执行的判决**，而不是事后的辩解。

### 9.2 退化停止条件（副）

> **若候选问题 3 显示 executor facet 方差 ≈ 0（给定合格标准时 judge 近乎完美执行），则归因研究退化。此时应停止归因方向，因为问题归约为标准生成——而标准生成已饱和（§7.1）。**

### 9.3 拥挤停止条件（副）

> **若在候选问题 1 完成前，出现同时满足以下三条的工作：(i) 交叉设计下的方差分解；(ii) 至少一个非语言锚点；(iii) 报告归因误差界——则本项目的候选 1 与 3 应合并进该工作，project 转向候选 2。**

判断依据：本轮已观察到该文献族在 2026 年下半年的密度（§A.2 中 7 条装置在约 10 个月内出现）。撞车风险是实质的，必须在 TASK 周期中设置定期复核。

---

## 10. 旧 Idea 新颖性下调清单

简报要求："如果发现 MM-VeriRubric 或其他新工作已经覆盖原有核心假设，必须明确下调旧 Idea 的新颖性。"

逐条给出下调幅度与理由：

| 旧主张 | 下调后状态 | 理由 |
|---|---|---|
| "标准缺陷与执行缺陷不可分离" | **强断言已否证，弱断言保留** | Mubric、RubricArmor、2607.27984 的反事实替换、ReVeR 的 label-blind 解耦均构造了分离装置。仅保留"未被定量归因" |
| "Validity / Coverage / Discrimination / Consistency / Evidence-Validity 五属性是可能的质量属性" | **冗余，建议弃用** | 三轴分类已成共识；RRD 已命名四失败模式；ProRubric 已提出 reward validity |
| "判别性 / discrimination 研究不足" | **否证** | SVR、Auto-Rubric、RRD、RubricArmor、GenRubric 全部在优化它 |
| "Coverage 缺少定义" | **大幅下调** | 五种语义中四种已有操作化（§B.1）。保留的只是"语义间可迁移性" |
| "一致性过滤无法排除共同错误" | **保留，但最不新颖** | 且现有证据加强：R1-Reward 的 Final Reward = R_result × (1 + 0.5·R_consistency) + 0.5·R_format，consistency 仅在结果正确时计入；VideoScore2 的 accuracy reward 是纯分数匹配。两者都锚在最终答案 |
| "标准质量可独立识别" | **不得作为前提** | 2609.29709 已形式化非识别。只能作为带识别条件的假设 |
| "运行期错配无诊断" | **否证** | RIPD 已命名并量化（9.5% / 27.9%）；held-out criterion shift 已存在；Scaling Laws 已给静态/在线反转 |
| "标准质量 → 下游收益" | **前提失效风险高** | ReVeR / ProRubric / Scaling Laws / SVR 四条独立负面或未验证证据 |

**总体判断：** 项目的新颖性从"提出一个未被研究的质量属性体系"下调为**"在已有分离装置之上，建立带识别条件的归因估计量，并检验其下游传递"**。这是更窄、更方法学、但更站得住的定位。

---

## 11. 未核实项与残余风险

### 11.1 未核实（V4）

本轮已取得的证据等级，以及仍未取得的：

| 项 | 已核实 | 仍未核实 |
|---|---|---|
| SVR (2606.08077) | 摘要 + HTML 正文：24.1→0.3、oracle 83.1 / SVR 82.8、单 judge 限定原句、"不迁移下游"原句、推理只 condition on prompt | 全文 PDF 的表格细节；代码 |
| CARO (2603.00451) | 摘要：混淆矩阵分解、fixing patches、教师教育 / STEM 领域 | 全文；与 GRM 的可迁移性（本文判断为不可外推） |
| RubricArmor (2610.05308) | 摘要 + HTML 正文：`s(x,y_t;R_t)=1 ∧ q_t=1` 判据、独立 quality judge 存在、消融 71.69 / 72.80 / 77.09 | **quality judge 自身的误差率**（全文未报，这是候选问题 1 的核心前提） |
| Mubric (2609.37322) | 摘要：从真实 preferred/dispreferred 对挖掘缺陷、703 任务 / 4 域、+7.48 点 | 全文与缺陷 operator 的完整集合 |
| 2607.27984 | 摘要 + HTML 正文：76.38→73.72（−2.66）、74.27、77.50、122/203 held-out criteria、外部对比 CI 含零 | 论文所述完整设计规则的可推广性 |
| 2609.29709 | 摘要：KR-20 0.5223 vs 0.5231、0.01–0.68、4.72% 误差率、"需金标或变化的 scorer facet" | 全文决策表与四条报告纪律的完整措辞 |
| Rubric-ARM (2602.01511) | 摘要：交替优化、latent action、声称下游对齐改善 | **具体增益数值与实验设置**（摘要未给数）；本文未引用任何具体数字 |
| 代码可用性 | — | 全部未核（本轮未访问任何仓库） |

### 11.2 残余风险

1. **撞车风险（高）。** §A.2 的七条分离装置集中在约 10 个月内出现。候选 1 与 3 有实质被抢先风险。
2. **负结果风险（高）。** 候选 2 若给出零效应或变号，项目效用论证失效（见 §9.1）。这是设计上接受的赌注，不是意外。
3. **定位风险（中）。** 候选 1 与 3 是测量方法学，容易被判为"不是 RM 贡献"。必须以"verifier 自证"与"归因"为叙事，而非"信度统计"。
4. **可行性风险（中）。** evidence 因子的分离依赖"不可判定"通道；开放式偏好任务是否有天然三态，是一个未经验证的假设。若没有，四因分解退化为三因。
5. **共线风险（中）。** 注入缺陷与 judge 盲点可能共线，导致 criterion 与 executor 两因不可分。需要第二套缺陷体系对冲。

---

## 12. 交接

### 12.1 本文件性质

按 `AGENTS.md`，本文件是 **Claude 的 Research Ideation + Research Audit 交付物**，对应 Phase 1 Step 3–4。它**不是** Research Proposal，不代表 Human 已批准，也不授权任何实验。

### 12.2 需要 Human 决策的事项（阻塞后续）

| 编号 | 决策 | 影响 |
|---|---|---|
| D-01 | 是否接受"新颖性下调"的定位（测量基础而非机制） | 决定是否需要重写 project charter |
| D-02 | 三个候选问题的取舍：是否先做候选 1（低成本前置），还是直接押候选 2 | 决定 TASK 排序与资源预算 |
| D-03 | 是否批准候选 2 的负结果风险（可能否定整个效用论证） | 决定是否投入 GPU 训练 |
| D-04 | 任务族选择：文本 / 图像 / 视频 TV2T | 决定"evidence 因子是否有天然三态"是否成立 |
| D-05 | 是否接受 §9 的三条停止条件作为预注册条款 | 决定项目的可终止性 |

### 12.3 建议的下一步（待批准，不自动执行）

1. **GPT（TASK-003 补做）**：取得 SVR / CARO / RubricArmor / Mubric / 2609.29709 全文与代码，核验 §11.1 全部未核实项；特别是 RubricArmor 的 quality judge 实现与 Rubric-ARM 的下游实验设置。
2. **GPT（TASK-004 补做）**：对 §5 的因果图做系统性 gap search，重点覆盖 Measurement Theory（G-theory、Dawid-Skene 在 LLM 上的应用）、Causal Evaluation、Verifier Independence 三个方向——本轮检索受限流影响，覆盖不完整。
3. **Claude**：在 1、2 完成后，撰写 Research Proposal V1，只保留经 Human 批准的候选问题。
4. **Human**：完成 §12.2 的五项决策。

### 12.4 关于工作树的说明

本轮开始时，`projects/verifiable-criteria/` 下的 `charter.md`、`idea_direction_01_verifiable_criteria.md`、`tasks.md` 处于**工作树已删除、HEAD 仍保留**的状态。本文件未修改也未提交这三处删除；`tasks.md` 的 TASK 表因此无法在本轮更新。建议先决定这三个文件是保留、重写还是正式移除，再恢复任务追踪。

---

**本轮一句话结论：** 这不是"五个属性各做一个模块"的问题，而是"标准质量能否被识别、以及在什么设计下才能被识别"的问题。已有工作已经证明它**不能**被现有统计口径识别（2609.29709），也已经造出了多种**局部**分离装置（Mubric、RubricArmor、反事实替换、label-blind 解耦）——但没有任何工作给出带误差界的归因，也没有任何工作证明标准质量能传递到下游 policy。前者是方法学缺口，后者是效用缺口；先补前者，用后者作终止条件。
