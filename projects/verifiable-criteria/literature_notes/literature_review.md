# Generative Reward Modeling Literature Review

> 项目：生成式奖励模型（GRM）\
> 版本：v1.0\
> 日期：2026-11-17\
> 范围：本轮提供的 18 篇 PDF\
> 状态：文献综述完成；研究空白为基于当前文献集的判断，不是已批准的
> Research Proposal。

## 0. 范围与结论边界

本综述围绕已锁定的三个方向组织：

1.  **Reasoning-based Reward Modeling**：让 RM
    生成评价标准、分析证据、执行推理并输出奖励。
2.  **Foundation Reward Model
    与泛化能力**：研究奖励能力如何跨任务、领域和模态迁移。
3.  **Video Understanding + RL**：在视频理解任务中引入
    GRM，并验证奖励信号能否改善下游 Policy。

本综述以用户提供的 PDF 为证据基础。没有将所有视频奖励论文归类为
GRM，也没有把视频生成评价结果等同于视频理解能力。

### 0.1 四类工作必须区分

  ----------------------------------------------------------------------------------------------------------------------------------
  类型           典型输出                                主要作用                   本轮代表工作
  -------------- --------------------------------------- -------------------------- ------------------------------------------------
  显式生成式 RM  Principles、Critiques、偏好标签或评分   生成可解释的奖励判断       DeepSeek-GRM、GRAM、Mix-GRM、R1-Reward

  Rubric /       Rubric、维度分数、偏好标签              结构化评价标准并约束判断   OpenRubrics、MJ-VIDEO、AVBench
  判别式评价器                                                                      

  程序化         规则检查结果、成功率                    验证可形式化的正确性       VideoPhy-2、VideoRLVR
  Verifier                                                                          

  奖励驱动的     DPO、RL、蒸馏后的 Policy                验证奖励能否改善生成模型   VideoReward、UnifiedReward、GenRM-Flow、Reward
  Policy 对齐                                                                       Lightning
  ----------------------------------------------------------------------------------------------------------------------------------

**核心边界：**
部分论文研究判别式视频评价、Benchmark、程序化验证或视频生成 Policy
对齐。它们能提供数据、指标和训练机制，但不能证明 GRM 已解决对应问题。

------------------------------------------------------------------------

## 1. Executive Summary

### 1.1 18 篇论文呈现的五条技术脉络

1.  **从标量评分转向显式评价推理。**
    DeepSeek-GRM、GRAM、Mix-GRM、R1-Reward 和 VideoScore2
    等工作，尝试让模型输出评价过程。研究重点逐渐从 CoT
    长度转向评价标准质量、推理结构和过程---结果一致性。
2.  **从固定偏好转向条件化评价标准。** OpenRubrics 将评价标准拆为 Hard
    Rules 和 Principles；Omni-Reward 支持 Free-form
    Preferences；MJ-VIDEO 使用细粒度 Criteria 和动态路由。
3.  **从任务专用 RM 转向跨任务、跨模态迁移。** GRAM
    利用无标注数据预训练比较能力；UnifiedReward
    联合训练图像和视频理解、生成评价；MSRL
    先在文本偏好上训练奖励推理，再迁移到多模态任务。
4.  **从静态视频评价转向视觉证据和过程评价。** VR-Thinker
    允许模型主动检索视频帧；VideoPhy-2
    将物理规律作为评价依据；GenRM-Flow 从 Flow Matching
    生成误差中提取隐式奖励。
5.  **从 RM 指标转向下游 Policy 效果。** Scaling Laws for Generative
    Reward Models 揭示静态 Judge 准确率与在线 Rewarder
    效果可能不一致。VideoReward、UnifiedReward、GenRM-Flow 和 Reward
    Lightning
    研究奖励信号如何影响生成模型，但主要集中在视频生成，而非开放域视频理解。

### 1.2 对三个方向的总体判断

  -----------------------------------------------------------------------------------------------------------------------------------------------------
  方向              当前基础                                                   尚未解决的关键问题
  ----------------- ---------------------------------------------------------- ------------------------------------------------------------------------
  Reasoning-based   Principles、Critiques、Breadth / Depth、CoT、Consistency   推理是否真正由证据支持？如何评价推理过程，而不只检查最终答案？
  RM                Reward、主动视觉探索                                       

  Foundation RM     无标注预训练、跨任务联合训练、文本到多模态迁移、自由形式   跨任务收益是否稳定？是否真正迁移评价能力，而非记住任务格式或数据分布？
                    Rubric                                                     

  Video             Omni-Reward、UnifiedReward、MSRL、R1-Reward                缺少以 TV2T 为核心、具备证据定位和过程推理能力，并通过在线 RL
  Understanding +   提供多模态理解奖励基础；VideoRLVR 提供可验证视频 RL 范式   验证下游收益的完整闭环
  RL                                                                           
  -----------------------------------------------------------------------------------------------------------------------------------------------------

**当前最重要的研究判断：** 不应先追求复杂的 Foundation GRM 或长
CoT。首先需要验证 Video GRM
能否可靠判断"候选回答是否被视频证据支持"。如果奖励模型无法识别事件、时间关系和证据缺失，后续
RL 可能只会放大 Proxy Reward 的错误。

------------------------------------------------------------------------

## 2. 三个研究方向的文献矩阵

### 2.1 Reasoning-based Reward Modeling

  ----------------------------------------------------------------------------------------------------------------------------------
  论文           推理机制                        训练方式                  主要贡献                   主要边界
  -------------- ------------------------------- ------------------------- -------------------------- ------------------------------
  DeepSeek-GRM   自生成 Principles + Critiques   Rejective Fine-Tuning +   SPCT；支持采样和 Meta RM   通用语言奖励任务，不是视频 RM
                                                 Rule-based Online RL      投票                       

  Mix-GRM        Breadth-CoT + Depth-CoT         合成推理数据、SFT、RLVR   根据任务性质匹配推理结构   跨模态任务仍需验证

  R1-Reward      多模态 CoT + 结果一致性         Long-CoT SFT +            稳定 RL                    Referee
                                                 StableReinforce           并约束推理与答案一致       一致性不等于视觉证据正确

  VideoScore2    Think-before-score              SFT + GRPO                结构化分析 VQ、TA、PC      主要评价视频生成质量
                                                                           后评分                     

  VR-Thinker     Thinking-with-Image、主动选帧   Cold Start + RFT + GRPO   推理时重新获取视觉证据     主要是视频生成偏好评价，不是
                                                                                                      TV2T
  ----------------------------------------------------------------------------------------------------------------------------------

**归纳：** Reasoning-based RM
的有效性不应只用推理长度衡量。至少需要区分评价标准覆盖、推理深度、证据支持、结果一致性和最终奖励准确率。

### 2.2 Foundation Reward Model 与泛化能力

  -----------------------------------------------------------------------------------------------------------------------------------------
  论文            泛化机制                   训练数据 / 方法        主要证据                       主要限制
  --------------- -------------------------- ---------------------- ------------------------------ ----------------------------------------
  GRAM            无标注预训练响应比较知识   Input-Response         在响应排序、RLHF               仍需任务适配；主要是文本 RM
                                             预训练，再用偏好数据   和任务适配中展示泛化收益       
                                             SFT；Label Smoothing                                  

  Scaling Laws    Judge / Policy             Qwen3 0.6B--14B        静态准确率排名与下游 Rewarder  仅在创意写作设置中研究；Gold Evaluator
  for Generative  容量与训练配方扩展         Rewarder、0.6B--8B     排名可能反转                   是学习得到的参考评估器
  Reward Models                              Policy、Online DPO                                    

  OpenRubrics     Rubric                     Contrastive Rubric     8 个 Benchmark；RUBRIC-RM      依赖 Rubric 生成质量和覆盖范围
                  生成与偏好一致性过滤       Generation；Hard       超过同规模基线                 
                                             Rules + Principles                                    

  Omni-Reward     Free-form Preference       5 模态、9 任务；248K   同时研究判别式 BT 和生成式 R1  R1 与 BT
                  条件化                     通用偏好 + 69K                                        训练数据规模不同，不能直接比较范式优劣
                                             指令数据                                              

  UnifiedReward   图像 /                     约 236K Pointwise +    多任务联合训练带来跨任务协同   视频理解下游收益具有 Benchmark 依赖性
                  视频理解与生成联合训练     Pairwise 数据；统一                                   
                                             VLM                                                   

  MSRL            文本奖励推理向多模态迁移   Text RL →              VL-RewardBench                 Caption 可能丢失时序细节，仍需多模态适配
                                             Caption-based RL +     66.6→75.9；Multimodal          
                                             CMKD → Multimodal RL   RewardBench 76.2→80.5          

  GenRM-Flow      从偏好优化 Generator       Flow Matching Velocity 不需要额外 Reward              依赖 Flow Matching；奖励是生成误差代理
                  中提取隐式奖励             Error Difference       Head，可在噪声 Latent          
                                                                    上计算奖励                     

  Reward          奖励与蒸馏共享 Latent 表示 LRM + Homologous       1--4 NFE                       主要解决视频生成加速，不是通用理解 RM
  Lightning                                  Preference             下兼顾偏好对齐与生成质量       
                                             Distillation                                          
  -----------------------------------------------------------------------------------------------------------------------------------------

**归纳：** Foundation RM 的泛化至少有三种不同来源，不能混为一谈：

-   **知识预训练：** GRAM 利用无标注数据学习响应比较知识。
-   **任务 / 模态迁移：** UnifiedReward、MSRL
    联合或分阶段训练多任务能力。
-   **评价条件泛化：** OpenRubrics、Omni-Reward 让评价标准随 Prompt
    或用户偏好变化。

### 2.3 Video Understanding + RL

  --------------------------------------------------------------------------------------------------------
  论文            任务类型                   奖励 / 评价机制                与目标方向的关系
  --------------- -------------------------- ------------------------------ ------------------------------
  R1-Reward       多模态理解与偏好判断       StableReinforce、Consistency   多模态 RM 训练稳定性基础
                                             Reward                         

  Omni-Reward     包含 TV2T 视频理解         Free-form Criteria、Generative 直接提供视频理解偏好评价任务
                                             R1                             

  UnifiedReward   图像 / 视频理解与生成      Pointwise + Pairwise、联合训练 直接提供 TV2T 评价与下游 DPO
                                                                            经验

  MSRL            多模态理解与生成奖励建模   Text RL、Caption               多阶段多模态 GRM 训练基础
                                             RL、CMKD、Multimodal RL        

  VideoPhy-2      视频生成物理常识评价       Semantic Adherence、Physical   可借鉴规则和证据标注，不是
                                             Commonsense、Physical Rules    TV2T RM

  Video Models    可验证视频轨迹生成         SDE-GRPO、Dense Decomposed     提供视频 RLVR 范式，不是 RM
  Can Reason with                            Rewards                        
  Verifiable                                                                
  Rewards                                                                   

  VR-Thinker      视频生成偏好评价           主动选帧、Visual Memory、GRPO  可迁移主动视觉证据获取机制

  VideoScore2     视频生成质量评价           CoT、VQ / TA / PC、GRPO        可借鉴评价推理数据构造

  VideoReward     视频生成偏好评价           VQ / MQ / TA、BTT、Flow-DPO /  多维偏好和下游对齐基础
                                             RWR / NRG                      

  MJ-VIDEO        视频生成偏好评价           5 个方面、28 个标准、双层 MoE  细粒度评价和动态权重参考

  AVBench         音视频联合生成评价         AT / VT / AV 专项              跨模态一致性和 Hard Negative
                                             Evaluator、概率评分            参考

  GenRM-Flow      视频生成偏好评价           Generator-derived Process      隐式过程奖励候选路线
                                             Reward                         

  Reward          视频生成偏好对齐与蒸馏     Latent Reward、Homologous      Latent RM 和多目标优化参考
  Lightning                                  Distillation                   
  --------------------------------------------------------------------------------------------------------

**任务边界：**
VideoReward、VideoScore2、MJ-VIDEO、VR-Thinker、VideoPhy-2、GenRM-Flow
和 Reward Lightning 的主要实验对象是视频生成评价或视频生成
Policy。它们的结果不能直接证明 TV2T 视频理解奖励模型有效。

------------------------------------------------------------------------

## 3. 18 篇论文 Paper Cards

每篇卡片按"问题---方法---证据---局限---对本研究的价值"整理。页码以 PDF
文件页序为准。

### P01 --- Inference-Time Scaling for Generalist Reward Modeling（DeepSeek-GRM）

-   **来源文件：** `Generative Reward Model（GRM，生成式奖励模型）.pdf`
-   **作者 / 机构：** Zijun Liu et al.，DeepSeek-AI / 清华大学。
-   **版本：** arXiv:2504.02495v3，2025-09。
-   **问题：** 通用领域奖励标准多样且缺少明确 Ground Truth；传统 Scalar
    / Pairwise RM
    难以灵活处理单个、成对和多个候选回答，也难以通过采样获得多样奖励信号。
-   **方法：** 采用 Pointwise Generative Reward Modeling；提出
    Self-Principled Critique Tuning（SPCT），由 Rejective Fine-Tuning
    冷启动，再通过 Rule-based Online RL 优化 Principle 和 Critique
    生成。推理阶段通过并行采样扩大 Principle 覆盖，并引入 Meta RM
    引导投票。
-   **证据：** 论文在多个 RM Benchmark 上验证 SPCT
    和推理时扩展的收益；Principles 与 Critiques
    共同构成更细粒度的奖励判断。
-   **局限：** 生成的 Principles
    本身不一定正确；需要通过训练和筛选提升有效性。Meta RM
    的投票收益也依赖 Meta RM 的判断质量。
-   **研究价值：** 提供"评价标准生成---证据分析---奖励输出"的 GRM
    范式。视频方向可以研究由视频内容和问题动态生成评价原则。
-   **证据锚点：** PDF pp. 1--7（问题、Pointwise
    GRM、Principles、SPCT）；后续 Inference-time Scaling 章节。

### P02 --- Scaling Laws for Generative Reward Models

-   **来源文件：** `Scaling Laws for Generative Reward Models.pdf`
-   **作者：** Angel Raychev、Nathan Lile、Rafael Rafailov。
-   **版本：** arXiv:2610.30765v1，2026-10-22。
-   **问题：** 静态 Benchmark 上更准确的 Generative
    Judge，是否一定能在在线 Policy Optimization 中提供更好的奖励信号？
-   **方法：** 在创意写作任务上比较 Thinking GRPO Judge 与 Answer-Only
    SFT Judge；扫描 Rewarder 规模（0.6B--14B）、Policy
    规模（0.6B--8B）、训练时长和 DPO 正则化，并用固定的学习型 32B Gold
    Evaluator 评估 Policy 轨迹。
-   **证据：** Thinking Judge 的静态 Gold-label Accuracy 更高；但在 8
    组匹配设置中的 7 组，Answer-Only Rewarder 带来更高的 Peak /
    Top-five-average Policy Elo。中间训练 Checkpoint
    也可能优于静态准确率更高的后期 Checkpoint。
-   **局限：** 研究集中在创意写作；Gold Evaluator
    是由偏好数据训练的参考模型，不等于独立人类质量标准。结论依赖当前训练管线和参考评估器。
-   **研究价值：** 必须分别测量 RM 静态准确率和 Policy
    优化收益，不能用前者代替后者。
-   **证据锚点：** PDF pp. 1--8（摘要、实验设计和主要结果）；Limitations
    章节。

### P03 --- Beyond Length Scaling: Synergizing Breadth and Depth for Generative Reward Models（Mix-GRM）

-   **来源文件：**
    `Beyond Length Scaling Synergizing Breadth and Depth for Generative Reward Models.pdf`
-   **作者 / 机构：** Qiyuan Zhang et al.，City University of Hong Kong
    / Tencent Hunyuan / MBZUAI。
-   **发表信息：** ACL 2026 Findings。
-   **问题：** 仅增加 CoT 长度无法保证 RM
    变强；不同任务需要不同推理结构。
-   **方法：** 将原始评价理由拆成 Principle--Judgment--Verdict
    原子单元，再合成为 Breadth-CoT（并行覆盖多个评价原则）和
    Depth-CoT（先求解或深入分析，再形成判断）。通过 SFT 和 RLVR 训练
    Mix-GRM。
-   **证据：** 五个 Benchmark 上取得强结果；Breadth-CoT
    更适合主观偏好，Depth-CoT
    更适合客观正确性；混合机制优于单一机制。RLVR
    进一步强化机制与任务的匹配。
-   **局限：**
    推理机制与任务类型的匹配依赖训练数据构造；跨模态、尤其视频理解任务上的收益仍需验证。
-   **研究价值：** 视频理解 GRM 不应只有一种固定
    CoT。事实核验、事件覆盖、时序推理和开放式偏好可能需要不同推理路径。
-   **证据锚点：** PDF pp. 1--6（问题、B-CoT /
    D-CoT、合成流程）；实验章节及 Tables 2、12--14。

### P04 --- GenRM-Flow: Generators are Process-aware Reward Models in Flow Matching

-   **来源文件：**
    `GenRM-Flow_Generators are Process-aware Reward Models in Flow Matching.pdf`
-   **作者：** Siming Fu et al.。
-   **版本：** arXiv:2611.11421v1，2026-11-08。
-   **问题：** 现有视频 RM 依赖像素空间 VLM 或独立 Scoring
    Head，无法充分利用生成模型自身的 Velocity Prediction
    能力，也难以在高噪声 Latent 阶段评价。
-   **方法：** 从 KL-Regularized Optimal Policy 推导偏好奖励，再用 Flow
    Matching Velocity Prediction Error 近似
    Log-Likelihood。隐式奖励由偏好优化 Generator 与 Reference Generator
    的 Velocity Error Difference 得到，并接入 FlowAWR。
-   **证据：** 在 VideoGen-RewardBench 和 GenAI-Bench
    上报告更高的偏好准确率；在 Wan2.1-T2V 1.3B / 14B 的 FlowAWR
    实验中，使用该奖励后 VBench 多项指标改善。
-   **局限：** 依赖 Flow Matching；Velocity Error Difference
    是生成似然代理，不等于无条件真实偏好。下游 FlowAWR
    通过对生成终态再加噪评价不同噪声时间步，不应直接等同于原始采样轨迹逐步在线验证。
-   **研究价值：** 提供 Explicit GRM 之外的 Generator-derived Reward
    路线。若目标模型是自回归视频问答模型，应研究 Token Log-Probability
    Ratio，而不能直接套用 Flow Matching 公式。
-   **证据锚点：** PDF pp. 1--8（理论、隐式奖励和
    FlowAWR）；pp. 9--12（实验）。

### P05 --- VR-Thinker: Boosting Video Reward Models through Thinking-with-Image Reasoning

-   **来源文件：**
    `VR-Thinker Boosting Video Reward Models through Thinking-with-Image Reasoning.pdf`
-   **作者 / 机构：** Qunzhong Wang et al.，CUHK MMLab / Kuaishou Kling
    Team。
-   **版本：** arXiv:2510.10518v4，2026-03。
-   **问题：** 视频帧占用大量 Context，固定采样会丢失细节；传统 CoT
    只在文本中推理，无法在推理过程中重新查看视频证据。
-   **方法：** 引入 `select_frames` 视觉操作和可配置 Visual Memory
    Window；训练采用 Cold Start、Rejection Sampling
    Fine-Tuning、GRPO。奖励包含 Format、Accuracy、CoT Gain 和
    Exploratory Incentive。
-   **证据：** 7B 模型在 VideoGen-Reward、GenAI-Bench、MJ-Bench-Video
    上取得较强视频偏好判断结果，长视频子集收益更明显。
-   **局限：** 主要任务是视频生成偏好评价，而不是 TV2T
    视频理解问答。主动选帧机制能否改善 TV2T，需要单独实验。
-   **研究价值：**
    提供主动证据获取机制。可迁移为根据问题定位关键时间片段，再用证据支持候选回答判断。
-   **证据锚点：** PDF pp. 1--3（问题和框架）；方法、训练和实验章节。

### P06 --- Omni-Reward: Towards Generalist Omnimodal Reward Modeling with Free-Form Preferences

-   **来源文件：**
    `OMNI-REWARD TOWARDS GENERALIST OMNIMODAL REWARD MODELING WITH FREE-FORM PREFERENCES.pdf`
-   **作者：** Zhuoran Jin et al.，中国科学院自动化研究所。
-   **版本：** arXiv:2510.23451v1，2025-10-27。
-   **问题：** 现有 RM 存在 Modality Imbalance 和 Preference
    Rigidity：视频、音频等模态覆盖不足；固定二元偏好无法表达用户自定义评价标准。
-   **方法：** 构建 Omni-RewardBench（5 模态、9 任务、3,725
    个人工偏好对）和 Omni-RewardData（248K 通用偏好对、69K
    指令偏好对）。训练判别式 Omni-RewardModel-BT 和生成式
    Omni-RewardModel-R1；R1 使用 GRPO 生成 Critique 和偏好判断。
-   **证据：** 在自建多模态 Benchmark 和已有 RM Benchmark
    上进行评测；TV2T 是其视频理解任务之一。BT 与 R1
    提供不同的可解释性和性能权衡。
-   **局限：** R1 仅使用约 3% 数据训练，与使用完整数据的 BT
    不是严格公平的模型范式比较；Benchmark 性能不等于在线 RL 收益。
-   **研究价值：** Free-form Criteria
    可用于视频理解任务条件化评价，例如事件完整性、时序关系或证据充分性。
-   **证据锚点：** PDF
    pp. 1--5（问题、Benchmark、数据和模型）；实验与消融章节。

### P07 --- Improving Video Generation with Human Feedback

-   **来源文件：** `Improving Video Generation with Human Feedback.pdf`
-   **作者 / 机构：** Jie Liu et al.，Kling Team / Tsinghua University /
    CUHK。
-   **发表信息：** NeurIPS 2025；arXiv:2501.13918v2。
-   **问题：** 旧视频偏好数据无法反映现代 T2V
    模型质量；缺少可靠的多维视频奖励；传统 Diffusion-DPO 直接迁移到
    Rectified Flow 会出现优化问题。
-   **方法：** 构建约 182K 多维人类偏好 Triplets；提出 VideoReward，评价
    Visual Quality、Motion Quality、Text Alignment，使用 Bradley-Terry
    with Ties 和独立奖励 Token。进一步提出
    Flow-DPO、Flow-RWR、Flow-NRG。
-   **证据：** VideoReward 在现代视频偏好 Benchmark
    上优于多种基线；Flow-DPO 使用 Constant KL Weight
    时，在论文设置下获得更均衡的对齐效果；Flow-NRG
    支持推理时多目标权重调整。
-   **局限：** 研究目标是 T2V
    生成质量和生成模型对齐，不是视频理解回答正确性。Reward Hacking
    仍然存在。
-   **研究价值：** 多维奖励设计、Tie 处理、偏好数据构造和奖励驱动 Policy
    优化的参考。
-   **证据锚点：** PDF pp. 1--8（数据、VideoReward、Flow-DPO / RWR /
    NRG）；pp. 10--15（结果和消融）。

### P08 --- VideoScore2: Think Before You Score in Generative Video Evaluation

-   **来源文件：**
    `VIDEOSCORE2 THINK BEFORE YOU SCORE IN GENERATIVE VIDEO EVALUATION.pdf`
-   **作者：** Xuan He et al.。
-   **版本：** arXiv:2509.22799v1，2025-09-26。
-   **问题：** 视频评价只输出单一分数或粗略分析，缺乏可解释性；SFT
    评价器可能存在 OOD 泛化问题。
-   **方法：** 构建 VideoFeedback2，包含 27,168 个视频、2,933 个 Prompt
    和 81,504 条维度评分 / 推理记录。评价维度为 Visual Quality、Text
    Alignment、Physical / Common-sense
    Consistency。通过人工评分和评论扩展推理理由，再采用 SFT + GRPO。
-   **证据：** 在 VideoScore-Bench-V2 和四个 OOD Benchmark
    上报告提升；也验证了 Best-of-N 视频筛选的应用。
-   **局限：** 任务是 T2V 生成评价；CoT 主要通过 LLM 扩展，Accuracy
    Reward 检查最终评分，不直接证明每一步推理都由视频证据支持。
-   **研究价值：** 人工评分 + 简短诊断评论 + LLM 扩展 CoT
    的数据构造路线；迁移到 TV2T 时需要增加 Evidence Grounding。
-   **证据锚点：** PDF
    pp. 1--3（摘要、问题、数据概述）；训练与实验章节。

### P09 --- OpenRubrics: Towards Scalable Synthetic Rubric Generation for Reward Modeling and LLM Alignment

-   **来源文件：** `OpenRubrics.pdf`
-   **作者：** Tianci Liu et al.。
-   **发表信息：** ACL 2026。
-   **问题：** 标量或二元偏好难以表达多维质量；人工构造 Rubric
    成本高，直接调用强模型生成 Rubric 又难以控制质量。
-   **方法：** 提出 Contrastive Rubric Generation（CRG），利用 Chosen /
    Rejected 回答差异生成两类 Rubric：Hard Rules（明确约束）和
    Principles（隐含质量）。通过 Preference--Label Consistency Rejection
    Sampling 过滤不能正确解释偏好标签的 Rubric，再训练 RUBRIC-RM。
-   **证据：** 在 8 个 Benchmark 上，RUBRIC-RM 相比同规模强基线报告 8.4%
    提升；使用其奖励进行 DPO，在指令遵循和生物医学任务上获得收益。
-   **局限：** Rubric
    的覆盖范围和正确性仍依赖生成与过滤流程；一致性通过偏好标签验证，不等于每条
    Rubric 都具有因果解释力。
-   **研究价值：** 视频理解可采用"明确可验证规则 + 隐含推理原则"的
    Rubric 结构，并用对照回答筛选区分性标准。
-   **证据锚点：** PDF pp. 1--5（CRG、Rubric
    类型和过滤）；pp. 6--8（Benchmark、DPO 和领域实验）。

### P10 --- MJ-VIDEO: Fine-Grained Benchmarking and Rewarding Video Preferences in Video Generation

-   **来源文件：**
    `MJ-VIDEO Fine-Grained Benchmarking and Rewarding Video Preferences in Video Generationpdf.pdf`
-   **作者：** Haibo Tong et al.。
-   **版本：** arXiv:2502.01719v3，2025-02-07。
-   **问题：**
    视频偏好涉及多维质量，固定整体偏好无法解释不同场景的评价重点；缺少细粒度偏好标注。
-   **方法：** 构建 MJ-BENCH-VIDEO，包含 5 个方面和 28 个 Criteria；提出
    InternVL2-2B MoE 奖励模型，包含 Aspect MoE 和 Criteria
    MoE。训练依次进行 Criteria Scoring、Aspect Routing 和 Joint
    Training。
-   **证据：** 最终 Benchmark 包含 5,421 个视频偏好对；MJ-VIDEO
    在多个偏好 Benchmark 上优于对比模型，并用于下游视频生成偏好优化。
-   **局限：** 任务是视频生成评价；MoE 动态权重依赖 Backbone
    的视觉理解能力；细粒度标注成本较高。
-   **研究价值：** 可借鉴动态评价维度，但应为 TV2T 重新定义 Event
    Accuracy、Temporal Order、Evidence Grounding 等标准。
-   **证据锚点：** PDF pp. 1--7（Benchmark、28 Criteria、MoE
    和训练）；实验章节。

### P11 --- AVBench: Human-Aligned and Automated Evaluation Benchmark for Audio-Video Generative Models

-   **来源文件：**
    `AVBench Human-Aligned and Automated Evaluation Benchmark for Audio-Video Generative Models.pdf`
-   **作者：** Jialiang Yang et al.，Tsinghua University / CUHK。
-   **版本：** arXiv:2605.24652v1，2026-05-23。
-   **问题：** T2AV
    评价不能只检查音频和视频各自质量，还需要检查跨模态一致性、说话人属性、语义和时间同步。
-   **方法：** 定义 10 个维度；通过真实视频和受控扰动构造训练样本；训练
    AT、VT、AV 三个专项 Evaluator。使用 Yes / No Token Probability
    构造连续分数。
-   **证据：** 专项 SFT Evaluator 在受控 Hard Negative
    测试中优于通用模型；论文报告了与人类评价的相关性。
-   **局限：** 主要针对人类中心的音视频生成；评价器为判别式 SFT
    模型；论文没有完成完整 T2AV 在线 RLHF 闭环。
-   **研究价值：** 受控 Hard Negative Mining 和跨模态 Evidence
    Consistency 可迁移到视频理解任务。
-   **证据锚点：** PDF pp. 1--8（维度、数据和
    Evaluator）；实验章节与附录。

### P12 --- MSRL: Scaling Generative Multimodal Reward Modeling via Multi-Stage Reinforcement Learning

-   **来源文件：**
    `Wang_MSRL_Scaling_Generative_Multimodal_Reward_Modeling_via_Multi-Stage_Reinforcement_Learning_CVPR_2026_paper.pdf`
-   **作者：** Chenglong Wang et al.，Northeastern University /
    ByteDance。
-   **发表信息：** CVPR 2026；arXiv:2603.25108。
-   **问题：** 多模态偏好标注昂贵，直接依赖多模态 RLVR 数据难以扩展。
-   **方法：** 三阶段 Multi-Stage
    RL：先用大规模文本偏好训练通用奖励推理；再用 Caption-based RL 缩小
    Task Gap，并通过 Cross-Modal Knowledge Distillation（CMKD）缩小
    Modality Gap；最后用有限原始多模态数据 RL 适配。
-   **核心证据：** 论文报告 VL-RewardBench 从 66.6% 提高到
    75.9%，Multimodal RewardBench 从 76.2% 提高到
    80.5%；不同模型规模均观察到文本 RL 的收益。
-   **局限：** Caption 可能丢失细粒度时序和视觉证据；CMKD 的 Teacher
    轨迹一致不等于轨迹真实；论文没有完成 Video Understanding Policy
    的在线 RL 闭环。
-   **研究价值：**
    先建立通用奖励推理，再迁移到视频任务，是当前值得重点研究的多模态 GRM
    训练课程。
-   **证据锚点：** PDF
    pp. 1--6（问题、三阶段训练、CMKD）；pp. 7--10（实验和消融）。

### P13 --- Unified Reward Model for Multimodal Understanding and Generation

-   **来源文件：**
    `Unified Reward Model for Multimodal Understanding and Generation.pdf`
-   **作者：** Yibin Wang et al.，Fudan University / Shanghai Innovation
    Institute / Shanghai AI Lab。
-   **版本：** arXiv:2503.05236v2，2026-02-25。
-   **问题：**
    视觉奖励模型通常任务专用；图像理解、图像生成、视频理解和视频生成之间可能存在可迁移的视觉知识。
-   **方法：** 构建约 236K Pointwise + Pairwise 偏好数据；基于统一 VLM
    进行多任务 SFT，支持 Pointwise Scoring 和 Pairwise Ranking。使用
    Pair Ranking + Point Sifting 构造下游偏好数据，再通过 DPO
    对齐视觉模型。
-   **核心证据：** 联合训练在图像 /
    视频理解和生成评价任务上优于多个单任务设置；论文报告跨任务协同。下游视频理解
    DPO 在部分 Benchmark 上有明显收益，但不同数据集的增益并不一致。
-   **局限：** 不是以 GRPO 或显式 CoT 为核心的
    GRM；多任务收益不能自动推广到所有视频理解任务；下游 DPO
    结果不等于在线 RL 收益。
-   **研究价值：** 提供 TV2T 评价数据和跨任务训练基础；Pairwise +
    Pointwise 可用于构造高区分度回答偏好对。
-   **证据锚点：** PDF pp. 1--5（问题、236K
    数据、模型和偏好构造）；pp. 6--10（消融和下游实验）。

### P14 --- R1-Reward: Training Multimodal Reward Model Through Stable Reinforcement Learning

-   **来源文件：**
    `R1-Reward Training Multimodal Reward Model Through Stable Reinforcement Learning.pdf`
-   **作者：** Yifan Zhang et al.。
-   **版本：** arXiv:2505.02835。
-   **问题：** 直接用传统 RL 训练多模态 RM 容易数值崩溃；简单 Outcome
    Reward 无法约束推理与最终偏好的一致性。
-   **方法：** 提出 StableReinforce，包括 Pre-Clip 和 Advantage
    Filter；增加由 MLLM Referee 评估的 Consistency Reward。采用 Long-CoT
    SFT 冷启动，再按 GPT-4o 尝试次数筛选困难样本进行 RL。
-   **核心证据：** 在 VL-RewardBench、Multimodal Reward Bench 和 MM-RLHF
    Reward Bench 上报告竞争力；多数投票进一步提升推理时表现。论文观察到
    Reinforce++ 在其设置下训练崩溃，而 StableReinforce 保持稳定。
-   **局限：** Consistency Reward
    只验证推理与答案是否一致，不保证推理基于真实视觉证据；Referee
    本身可能判断错误；视频维度不是主要训练目标。
-   **研究价值：** 多模态 GRM 的 RL 稳定性和一致性奖励设计参考。
-   **证据锚点：** PDF pp. 1--6（问题、StableReinforce 和奖励）；后续
    Benchmark 与消融章节。

### P15 --- VideoPhy-2: A Challenging Action-Centric Physical Commonsense Evaluation in Video Generation

-   **来源文件：**
    `ICLR-2026-videophy-2-a-challenging-action-centric-physical-commonsense-evaluation-in-video-generation-Paper-Conference.pdf`
-   **作者 / 机构：** Hritik Bansal et al.，UCLA / Google Research。
-   **发表信息：** ICLR 2026。
-   **问题：**
    视频生成模型可以视觉逼真，却不一定遵循质量守恒、动量守恒、重力等物理规律；既有
    Benchmark 规模和物理规则标注不足。
-   **方法：** 从 197 种真实动作生成 3,940 个 Prompt；人工评价 Semantic
    Adherence 和 Physical Commonsense；为视频构造 Physical Rules，并标记
    Followed / Violated / Cannot Be Determined。训练
    VideoPhy-2-AutoEval。
-   **核心证据：** 最佳模型在 Hard Subset 上的 Joint Performance 为
    47.7%；模型在质量守恒和动量守恒等规律上表现较弱。
-   **局限：** 研究对象是视频生成物理合理性，不是 TV2T RM；AutoEval
    学习人工判断，不是严格物理仿真器；Hard Subset
    由参考模型失败样本构造。
-   **研究价值：** Physical Rules Grounding
    可用于检查视频理解回答是否符合可观察的物理过程，但应与回答正确性标签结合。
-   **证据锚点：** PDF pp. 1--4（Benchmark 构造和标注）；实验与 AutoEval
    章节。

### P16 --- GRAM: A Generative Foundation Reward Model for Reward Generalization

-   **来源文件：** `GRAM A Generative Foundation Reward Model.pdf`
-   **作者：** Chenglong Wang et al.。
-   **发表信息：** ICML 2025，PMLR 267。
-   **问题：** 传统 RM
    主要依赖标注偏好数据，难以形成可迁移的通用奖励知识；大量无标注
    Input-Response 数据未被充分利用。
-   **方法：** 先在大规模无标注 Input-Response
    数据上进行生成式预训练，学习输入与回答之间的关系；再用人工偏好数据
    SFT。推理时通过候选回答与模型生成的 Reference Response
    比较，并通过交换回答位置缓解位置偏差。使用 Label
    Smoothing，论文将其解释为正则化的 Bradley-Terry 排序目标。
-   **核心证据：** 在响应排序、PPO
    对齐和任务适配实验中展示跨任务泛化；作者报告在 RewardBench 上较
    Vanilla Discriminative RM 和 Vanilla Generative RM 有明显提升。
-   **局限：** 仍需任务偏好数据进行适配；Reference Response
    质量会影响奖励；主要研究文本任务。
-   **研究价值：** 提供 Foundation RM
    预训练路线：先学习通用比较知识，再学习任务偏好。可研究将无标注视频问答、视频描述和事件对比用于奖励能力预训练。
-   **证据锚点：** PDF pp. 1--5（问题、两阶段训练和评分）；实验及
    Appendix C--D。

### P17 --- Video Models Can Reason with Verifiable Rewards

-   **来源文件：** `Video Models Can Reason with Verifiable Rewards.pdf`
-   **作者：** Tinghui Zhu et al.，UC Davis / Microsoft Research / USC /
    UCSC。
-   **版本：** arXiv:2605.15458v1，2026-05-14。
-   **问题：**
    视频模型能够模仿看似合理的解题过程，但生成轨迹未必满足空间、时间和逻辑约束；仅靠
    SFT 不直接优化采样结果的客观正确性。
-   **方法：** 提出 VideoRLVR，在 Maze、FlowFree、Sokoban
    上使用程序化规划器生成 Ground Truth Trajectory；采用
    SDE-GRPO、Early-Step Focus 和 Dense Decomposed Rewards。
-   **核心证据：** 相比 SFT，三个任务成功率分别提升 6.1、5.5、3.2
    个百分点；Early-Step Focus 在保持接近性能的同时降低约 40% 训练时间。
-   **局限：**
    任务是规则明确的封闭式视频轨迹生成，不是开放域视频理解；奖励依赖任务专属
    Verifier，Verifier 漏洞仍可能被 Policy 利用。
-   **研究价值：** 可验证子目标、过程合法性和 Dense Reward
    可用于视频理解中可形式化的事实、数量、事件顺序等部分。
-   **证据锚点：** PDF pp. 1--4（任务、算法和奖励）；实验与消融章节。

### P18 --- Reward Lightning: Fast Video Generation via Homologous Preference Distillation

-   **来源文件：**
    `Reward Lightning Fast Video Generation via Homologous Preference Distillation.pdf`
-   **作者 / 机构：** Jiaxiang Cheng et al.，Tencent Hunyuan。
-   **版本：** arXiv:2607.03960v1，2026-07-04。
-   **问题：**
    视频生成偏好对齐与少步蒸馏通常在不同表示空间或不同阶段优化，容易出现梯度冲突和灾难性遗忘。
-   **方法：** 训练 Latent Reward Model（LRM），直接在 Flow Latent Space
    评价视频；再提出 Homologous Preference Distillation（HPD），让奖励
    Head 和蒸馏 Discriminator 共享 Backbone 和 Latent Feature，并通过
    Adaptive Preference Weight 控制偏好梯度的引入。
-   **核心证据：** LRM 在 VideoGen-RewardBench 上报告 72.24% 的 w/ Ties
    Preference Accuracy；HPD 支持 1--4 NFE，在 Wan2.2 T2V / I2V
    上取得较好的 VBench
    和人工偏好结果。共享表示设置下，论文报告的两类梯度余弦相似度从负值变为
    0.41。
-   **局限：** 主要优化视频生成与蒸馏，不是视频理解 RM；Homology
    缓解平均梯度冲突，不代表消除所有样本和训练阶段的冲突。
-   **研究价值：** Latent Reward
    和表示协调可作为生成式视频模型的候选路线；若 Policy
    是自回归视频问答模型，需要重新设计共享表示和训练目标。
-   **证据锚点：** PDF
    pp. 1--9（LRM、HPD、动态权重）；pp. 10--15（实验和梯度分析）。

------------------------------------------------------------------------

## 4. 跨论文方法对比

### 4.1 Reward Signal 的来源

  -------------------------------------------------------------------------------------------------------------------------------
  奖励来源            代表论文                                  优点                           主要风险
  ------------------- ----------------------------------------- ------------------------------ ----------------------------------
  人工偏好标签        VideoReward、Omni-Reward、UnifiedReward   与人类偏好直接关联             标注成本、偏好噪声和分布偏差

  LLM 扩展的评价理由  VideoScore2、R1-Reward                    降低长 CoT 人工标注成本        教师模型偏差；理由未必忠实于证据

  Rubric 条件化判断   OpenRubrics、MJ-VIDEO                     评价标准显式、便于诊断         Rubric 缺漏或错误会影响判断

  规则 / 程序验证     VideoPhy-2、VideoRLVR                     对可形式化目标提供客观信号     覆盖范围有限，规则漏洞可能被利用

  Generator-derived   GenRM-Flow                                不需要独立 Reward              依赖生成目标和概率代理的有效性
  Reward                                                        Head，可利用生成模型内部能力   

  Latent Reward Head  Reward Lightning                          直接在 Latent Space 评价，避免 需要独立训练和校准奖励 Head
                                                                VAE 解码                       
  -------------------------------------------------------------------------------------------------------------------------------

### 4.2 Reasoning 与 Process 的区别

  ----------------------------------------------------------------------------------------
  概念               定义                             本轮代表论文
  ------------------ -------------------------------- ------------------------------------
  Explicit CoT       生成可读的评价分析               DeepSeek-GRM、Mix-GRM、VideoScore2

  Rubric Reasoning   按显式标准组织评价               OpenRubrics、Omni-Reward

  Active Visual      推理时主动获取新视觉证据         VR-Thinker
  Reasoning                                           

  Consistency Reward 检查推理与最终答案是否一致       R1-Reward

  Verifiable Process 检查中间状态或轨迹是否满足规则   VideoRLVR

  Implicit Process   从生成过程的内部误差提取奖励     GenRM-Flow
  Reward                                              

  Time-conditioned   根据噪声时间步提取 Latent        Reward Lightning
  Latent Evaluation  评价特征                         
  ----------------------------------------------------------------------------------------

**不能混淆：** 生成 CoT 不代表进行了 Process Reward
Modeling；奖励可以分解到时间步，也不代表评价了原始采样轨迹中的每个决策。

### 4.3 训练范式对比

  ---------------------------------------------------------------------------------------------------------------------------------
  范式             代表论文                                           优势                           局限
  ---------------- -------------------------------------------------- ------------------------------ ------------------------------
  SFT /            GRAM、UnifiedReward、OpenRubrics                   训练稳定，便于利用大规模数据   不保证模型形成有效推理策略
  两阶段预训练                                                                                       

  SFT + RL         DeepSeek-GRM、R1-Reward、VideoScore2、VR-Thinker   能优化生成行为和奖励准确率     依赖可靠奖励和训练稳定性

  Multi-stage RL   MSRL                                               分阶段缩小 Task Gap 和         Caption 信息损失、迁移误差
                                                                      Modality Gap                   

  MoE / 多任务训练 MJ-VIDEO、UnifiedReward                            动态评价重点或跨任务共享       路由和任务采样可能引入负迁移

  Verifiable RL    VideoRLVR                                          对明确规则提供可验证信号       只适用于可验证目标

  Preference       Reward Lightning                                   同时改善偏好和推理效率         主要面向 Flow Matching
  Optimization +                                                                                     生成模型
  Distillation                                                                                       
  ---------------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 5. Research Gaps：基于当前 18 篇论文的研究空白

以下是对**当前文献集**的综合判断，不代表已经完成全领域检索，也不应直接视为原创性结论。

### Gap 1：视频生成评价与视频理解评价仍然存在任务断层

当前大量视频 RM 评价 T2V 生成质量，主要目标是 Visual Quality、Motion
Quality、Text Alignment 和 Physical Consistency。视频理解 RM
的目标则是判断回答是否正确、完整、符合视频证据。

**研究问题：** 能否构建专门面向 TV2T 的
GRM，评价候选回答的事件识别、时序关系、证据支持和推理正确性？

相关文献：Omni-Reward、UnifiedReward、MSRL、R1-Reward。

### Gap 2：CoT 与视觉证据之间缺少严格绑定

DeepSeek-GRM、Mix-GRM、R1-Reward 和 VideoScore2 研究显式推理；VR-Thinker
研究主动获取视频帧。但在当前文献中，尚未看到一个完整方案同时做到：

1.  生成评价原则；
2.  定位视频中的关键时间片段；
3.  将每条判断绑定到可核查证据；
4.  验证推理是否真正支持最终偏好；
5.  将证据错误转化为可训练奖励。

**研究问题：** 如何构建 Evidence-grounded Video
GRM，避免语言合理但视觉事实错误的 Critique？

相关文献：DeepSeek-GRM、R1-Reward、VR-Thinker、VideoScore2、VideoPhy-2。

### Gap 3：可验证奖励与生成式奖励尚未形成统一的开放域视频理解方案

VideoRLVR 展示了程序化 Verifier 对封闭任务的价值；GRM
能够处理更复杂的开放式评价。

**研究问题：** 如何组合 Verifiable Reward 与 Generative
Reward，并避免两者的尺度冲突、错误传播和奖励投机？

相关文献：VideoRLVR、VideoPhy-2、OpenRubrics、DeepSeek-GRM、R1-Reward。

### Gap 4：静态 RM 准确率与在线 RL 收益之间存在未解决的评价鸿沟

Scaling Laws for Generative Reward Models
表明，在其创意写作设置中，静态准确率更高的 Thinking Judge 不一定是更好的
Online Rewarder。

**研究问题：** 一个在 TV2T Benchmark 上准确的
GRM，是否真的能提高视频理解 Policy 的独立评测成绩？

相关文献：Scaling Laws for Generative Reward
Models、GRAM、VideoReward、UnifiedReward、GenRM-Flow。

### Gap 5：Process-aware Reward 尚未覆盖标准自回归视频理解模型

GenRM-Flow 利用 Flow Matching 的 Velocity Error Difference
提取隐式奖励；VideoRLVR 在 Flow Matching 视频生成过程中进行
RL；VR-Thinker 则在推理时主动检索视频证据。

**研究问题：** 如何在自回归视频理解 Policy 中定义
Token-level、Evidence-level 或 Event-level Process Reward，并验证其比
Outcome Reward 更有效？

相关文献：GenRM-Flow、VR-Thinker、R1-Reward、VideoRLVR。

### Gap 6：跨任务泛化与任务负迁移需要更严格验证

UnifiedReward 和 MSRL
证明联合训练或分阶段迁移能够改善多个任务，但不意味着所有任务都能互相促进。

**研究问题：** 哪些任务共享奖励表示，哪些任务需要独立
Rubric、专家或采样权重？

相关文献：GRAM、Omni-Reward、UnifiedReward、MSRL、MJ-VIDEO、AVBench。

### Gap 7：Benchmark 与奖励模型之间需要更强的独立性

如果 GRM 的训练、验证和最终 Policy
评价依赖同一类模型生成的标签，可能出现共同偏差。

**研究问题：**
如何建立独立的人工评测、规则验证和外部模型评估，使奖励模型不能仅通过迎合自己的
Proxy Evaluator 获得高分？

相关文献：Scaling Laws for Generative Reward
Models、VideoPhy-2、AVBench、VideoScore2。

------------------------------------------------------------------------

## 6. 面向 Video Understanding + RL 的研究建议

以下内容是研究建议，不是已经批准的研究方案。

### P0：先建立 Evidence-grounded TV2T Reward Benchmark

建议样本结构：

``` text
(video, question, response_A, response_B,
 preference_label, criteria, evidence_spans)
```

其中：

-   `preference_label`：回答偏好；
-   `criteria`：评价标准；
-   `evidence_spans`：支持判断的时间区间、关键帧或事件；
-   可选 `verifiable_labels`：事件存在性、数量、顺序等可程序化验证标签。

必须区分"回答与视频一致"和"回答本身语言合理"。

### P1：建立三类 Baseline

  Baseline                目的
  ----------------------- ---------------------------------------
  Discriminative BT MRM   测量判别式偏好判断能力
  Generative RM           测量显式 Critique / Reward Generation
  Rubric-conditioned RM   测量评价标准条件化能力

控制相同
Backbone、数据划分和训练数据量，避免将模型规模或数据量差异误判为范式收益。

### P2：加入 Evidence Grounding

要求模型对关键判断给出时间区间、关键帧或事件、支持证据、反证或缺失证据、最终偏好判断。Evidence
Verifier 应尽量独立于生成 Critique 的模型，避免自我验证造成错误闭环。

### P3：研究 Verifiable + Generative Hybrid Reward

可将奖励分为：

\[ R\_{`\text{total}`{=tex}} =
`\lambda`{=tex}R\_{`\text{verifiable}`{=tex}} +
(1-`\lambda`{=tex})R\_{`\text{generative}`{=tex}} \]

其中，Verifiable Reward 只处理具备可靠标签或规则的部分；Generative
Reward 处理开放式完整性、解释和语义质量。权重 (`\lambda`{=tex})
需要通过消融确定，不能预设固定最优值。

### P4：验证 Reward-to-Policy 闭环

至少分别报告：

1.  RM Static Accuracy；
2.  Evidence Grounding Accuracy；
3.  Reward 与独立人类评价的一致性；
4.  Policy 的独立视频理解成绩；
5.  Reward Hacking / 事实幻觉变化；
6.  训练成本和推理成本。

不得只用 GRM 自身的奖励分数证明 Policy 变强。

------------------------------------------------------------------------

## 7. 推荐的后续研究顺序

### 阶段 A：先定义研究问题

优先复查：

1.  DeepSeek-GRM：SPCT 和推理时扩展；
2.  Mix-GRM：Breadth / Depth 的任务适配；
3.  OpenRubrics：Hard Rules / Principles；
4.  R1-Reward：StableReinforce 和 Consistency Reward；
5.  VR-Thinker：主动视觉证据获取；
6.  MSRL：文本奖励推理向多模态迁移。

目标：确定 Video GRM 的输入、输出、证据结构和训练目标。

### 阶段 B：确定评价数据和泛化方案

优先复查：

1.  Omni-Reward：TV2T 与 Free-form Preference；
2.  UnifiedReward：理解 / 生成联合训练；
3.  VideoPhy-2：物理规则和证据标注；
4.  AVBench：受控 Hard Negative；
5.  MJ-VIDEO：细粒度标准与动态权重。

目标：构建 TV2T Benchmark、Criteria 和 Verifier。

### 阶段 C：设计训练与下游验证

优先复查：

1.  R1-Reward：多模态 RM 的 RL 稳定性；
2.  MSRL：多阶段训练；
3.  VideoRLVR：Dense Verifiable Reward；
4.  GenRM-Flow：隐式过程奖励；
5.  Scaling Laws：静态评测与在线 Rewarder 的差异。

目标：确定 GRM 训练方式，并设计独立的在线 RL 验证。

------------------------------------------------------------------------

## 8. 与项目科研工作流的衔接

根据根目录 `AGENTS.md`，本文件属于 GPT 的 Literature Review 交付物，不是
Research Proposal，也不代表 Human 已批准实验。

下一步建议：

1.  **Human：** 从三个已锁定方向中确定本轮主攻问题，优先明确 TV2T
    的具体任务边界。
2.  **GPT：** 根据选定问题补充针对性文献检索、Benchmark 和实验协议。
3.  **Claude：** 进行 Idea Development 和 Research
    Audit，主动检查创新性、反例、替代解释和失败模式。
4.  **Human：** 批准 Research Proposal。
5.  **GPT + Codex：** 设计并实现实验；正式运行前执行 Experiment Freeze。
6.  **Codex：** 训练、评测并归档原始日志和指标。
7.  **GPT：** 独立审查实验结果。
8.  **Claude：** 基于已验证结果撰写论文，并持续执行严谨审查。

未经 Human
批准的研究想法不得直接进入正式实验。实验假设、数据划分、Baseline、指标和
Git Commit SHA 必须在 Experiment Freeze 时冻结。

------------------------------------------------------------------------

## 9. 证据与使用说明

### 9.1 证据原则

-   本综述的论文结论以对应 PDF 为准。
-   页码均指上传 PDF 的文件页序，不一定等于论文印刷页码。
-   不同论文的
    Benchmark、数据划分和指标定义不同，表格数值不能直接横向排名。
-   "Research Gap"是对当前 18
    篇文献的归纳，不是全领域不存在相关工作的证明。
-   视频生成评价、视频理解奖励建模和视频生成 Policy RL
    必须分别报告，不得混用指标。

### 9.2 18 篇原始 PDF 索引

  ---------------------------------------------------------------------------------------------------------------------------------------------------------
  ID        论文             原始 PDF 文件
  --------- ---------------- ------------------------------------------------------------------------------------------------------------------------------
  P01       Inference-Time   `Generative Reward Model（GRM，生成式奖励模型）.pdf`
            Scaling for      
            Generalist       
            Reward Modeling  

  P02       Scaling Laws for `Scaling Laws for Generative Reward Models.pdf`
            Generative       
            Reward Models    

  P03       Beyond Length    `Beyond Length Scaling Synergizing Breadth and Depth for Generative Reward Models.pdf`
            Scaling:         
            Synergizing      
            Breadth and      
            Depth for        
            Generative       
            Reward Models    

  P04       GenRM-Flow       `GenRM-Flow_Generators are Process-aware Reward Models in Flow Matching.pdf`

  P05       VR-Thinker       `VR-Thinker Boosting Video Reward Models through Thinking-with-Image Reasoning.pdf`

  P06       Omni-Reward      `OMNI-REWARD TOWARDS GENERALIST OMNIMODAL REWARD MODELING WITH FREE-FORM PREFERENCES.pdf`

  P07       Improving Video  `Improving Video Generation with Human Feedback.pdf`
            Generation with  
            Human Feedback   

  P08       VideoScore2      `VIDEOSCORE2 THINK BEFORE YOU SCORE IN GENERATIVE VIDEO EVALUATION.pdf`

  P09       OpenRubrics      `OpenRubrics.pdf`

  P10       MJ-VIDEO         `MJ-VIDEO Fine-Grained Benchmarking and Rewarding Video Preferences in Video Generationpdf.pdf`

  P11       AVBench          `AVBench Human-Aligned and Automated Evaluation Benchmark for Audio-Video Generative Models.pdf`

  P12       MSRL             `Wang_MSRL_Scaling_Generative_Multimodal_Reward_Modeling_via_Multi-Stage_Reinforcement_Learning_CVPR_2026_paper.pdf`

  P13       Unified Reward   `Unified Reward Model for Multimodal Understanding and Generation.pdf`
            Model            

  P14       R1-Reward        `R1-Reward Training Multimodal Reward Model Through Stable Reinforcement Learning.pdf`

  P15       VideoPhy-2       `ICLR-2026-videophy-2-a-challenging-action-centric-physical-commonsense-evaluation-in-video-generation-Paper-Conference.pdf`

  P16       GRAM             `GRAM A Generative Foundation Reward Model.pdf`

  P17       Video Models Can `Video Models Can Reason with Verifiable Rewards.pdf`
            Reason with      
            Verifiable       
            Rewards          

  P18       Reward Lightning `Reward Lightning Fast Video Generation via Homologous Preference Distillation.pdf`
  ---------------------------------------------------------------------------------------------------------------------------------------------------------

### 9.3 归档建议

按照 `AGENTS.md`，建议将本文件保存为：

``` text
projects/generative-reward-model/
└── literature_notes/
    └── literature_review.md
```

原始 PDF 单独归档到：

``` text
projects/generative-reward-model/
└── references/
    └── papers/
```

Paper Cards 后续可拆分为 `paper_001.md` 至
`paper_018.md`，本文件继续作为总览和文献索引。不要创建额外的
`RESEARCH_INDEX.md`；项目入口仍由根目录 `AGENTS.md` 管理。

------------------------------------------------------------------------

**本轮结论：** 18
篇文献已按三个研究方向完成归类。下一阶段的核心任务不是继续泛读，而是围绕
TV2T 建立 Evidence-grounded Video GRM 的明确问题定义，并由 Claude
执行独立 Idea Development 与 Research Audit。
