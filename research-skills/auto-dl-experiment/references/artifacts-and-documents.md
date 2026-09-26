# 产物、目录与文档规范

## 默认结构

适配已有项目；用户的数据集可位于任何外部目录。`docs/experience` 默认解释为目录，`INDEX.md` 是跨轮经验汇总文件。若已有同名文件或不同约定，保持原文件并在目录说明中记录映射。以下是按阶段逐步产生的结构，不要求在启动时创建空文件或空目录。

```text
project/
  docs/
    desc_experiment/
      01_DATASET.md
      02_EXPERIMENT_DETAILS.md
      03_EXPERIMENT_PROTOCOL.md
      DIRECTORY_LAYOUT.md
      WORKFLOW_STATE.json
      APPROVALS.md
    baseline/
      BASELINE_REPORT.md
    propose_arch/
      M001/
        LITERATURE.md
        NOVELTY_REVIEW.md
        DESIGN.md
        PROTOCOL.md
        IMPLEMENTATION.md
        RESULTS.md
        ABLATION_PLAN.md
        ABLATION_RESULTS.md
        FINAL_REPORT.md
    experience/
      INDEX.md
      M001.md
  src/
    data/
    models/baselines/
    models/proposed/
    training/
    evaluation/
  configs/
    common/
    baselines/
    proposed/M001/
  tests/
  artifacts/
    data_audit/
    splits/
    research_ledger/
    runs/<run_id>/
      PROTOCOL.md
      freeze.json
      RESEARCH_LEDGER_SNAPSHOT.md
      config.json
      logs/
      checkpoints/
      predictions/
      metrics.json
      coverage.json
      smoke.json
      timing.json
    results/
      INDEX.md
      baseline/<batch_id>/
      M001/<batch_id>/
```

`runs` 保存单次执行事实，`results` 保存跨运行汇总和图表，`docs` 保存人类可读解释；优先链接原始产物而非复制出相互矛盾的结果。已有项目使用其他结果根目录时，写清一一对应。方法编号不重复、不因失败回收；同一假设的小修订可用修订号，机制或范式变化分配新方法编号。

## 文档1：01_DATASET.md

- 用户提供的数据集简要描述、任务背景、公开/非公开/尚未确认；公开集列正式名称、版本与官方来源，不能因目录名像公开数据就推断公开性。
- 数据根目录、来源与核验方式；统计原始数量、排除理由、可用数量。区分文件数、WSI 数、病例数与患者数，明确一患者多病例的映射。
- 任务标签定义、类别总数；总病例数、总切片数、各类病例数及切片数，未知/缺失标签单列。多标签计数允许重叠，不能强求分项和等于总数；生存/回归不虚构类别数，报告事件/删失或连续结局分布。
- 病理特征：倍率（如20x）、MPP、patch 尺寸/步长与组织过滤规则（有记录才填）、提取器名称/版本/权重、特征维度/形状/类型、坐标和切片映射。不能由特征维度猜测提取器或倍率。
- 每 WSI 全部有效 patch 的最小/最大值及对应切片 ID、中位数、分位数、总量、空切片数；每病例切片数与 patch 数分布。需要全清单/全文件形状统计的事实不能以抽查冒充完整统计。
- 单模态/多模态；逐模态记录内容、获取时点、提取器、维度、缺失率、预处理、对齐 ID。文本报告说明字段/章节、长度与截断、去重、脱敏示例、是否包含预测时点之后的信息；不要未经允许上传原始报告。
- 统计事实、用户说明、推测与待补信息分开标注。对不存在的字段标“不适用”，不要留未解释空白。

若只有原始 WSI、没有预提取特征，将实际特征统计标为“尚未生成”，在协议中提出待确认的提取方案；不虚构维度和有效 patch 数。步骤1先确认可得事实及计划，批准的特征准备纳入步骤3的公共数据管线与资源预算，生成后补齐文档和输入审计，再冻结基线训练协议。新增实质数据排除或输入改变须回到相应文档确认。

## 文档2：02_EXPERIMENT_DETAILS.md

按论文实验细节写成连贯说明，并用表格整理公共超参数：

- 固定划分还是 K 折、K 值；train/val/test 数量及比例、比例分母；患者分组不重叠、分层因素、中心/时间留出策略。K 折写清外层测试及内层验证如何产生；不把外层测试当选轮验证集。
- 这里只描述策略和汇总数，不报告 seed 或具体划分文件；在 `artifacts/splits`、运行配置和冻结清单中保存种子及真实分区，满足复现和审计。
- 公共训练策略：损失、优化器、学习率/调度、权重衰减、有效 batch、最大 epoch、早停、初始化、特征是否冻结、类别不平衡处理、各方法相同调参预算。方法特有损失/算子列出并说明公平控制。
- 验证集最佳 epoch：明确指标名、计算单位、最大化/最小化、验证频率、并列规则（默认较早 epoch）、早停规则；不得按测试结果选 epoch。
- 指标逐项解释：衡量内容、方向、定义/实现、计算单位、适用条件、阈值/平均方式和不确定性。不能只写 AUROC、F1 等缩写。
- 分类按任务选择 AUROC、AUPRC/AP、accuracy、precision、recall/sensitivity、specificity、F1、混淆矩阵及需要的校准指标；定义阳性类、多分类 one-vs-rest/宏微平均、AP 与梯形 PR-AUC 的区别。阈值及调阈值数据来源预定。
- 生存按协议选择 Harrell/Uno C-index、time-dependent AUC、Brier/IBS，说明预测时点、删失处理、IPCW 拟合来源、积分区间和可估计范围。若为其他任务，选其相应指标，不套用生存或分类口径。
- 明确同数据/特征/预处理/训练/选轮/评价规则；写出病例级两级聚合和全 patch 输入等适用约束。

## 文档3与每轮 PROTOCOL.md

全局 `03_EXPERIMENT_PROTOCOL.md` 给出总体约定，每轮 `PROTOCOL.md` 写清本批次实例化参数，正式运行保存不可变副本。至少包含：

1. 任务类型、预测单位、预测时点、输入/输出张量及语义、目标人群与标签定义。
2. 假设、支持与反例（原始证据链接）、本轮改变、固定基线与匹配对照。
3. 主要指标及方向、次要指标/重要不退化项、验证选轮、阈值、最终测试访问规则。
4. 数据使用、划分与排除规则、训练预算、搜索范围、随机性和可复现审计位置。
5. 预先量化的效果门槛及其依据、区间/检验方法、机制诊断、诊断充分性、成功/失败/证据不足判据。没有依据时与用户确认合理探索目标，不机械继承旧实验的 +0.02 等门槛。
6. 预设消融/干预及替代解释、技术失败处理、算力估计和审批范围。
7. 协议状态、版本、冻结时间和哈希；变更另存版本与原因，不能覆盖已出结果的判据。

## 状态与审批记录

`WORKFLOW_STATE.json` 至少保存 `project_root`、`dataset_roots`、`step`、`method_id`、`protocol_version`、`protocol_hash`、`status`、`artifact_paths`、`pending_decision`、`last_approval_ref`、`batch_id`、`approved_batch_scope`、`consecutive_technical_failures`、`updated_at`。可用状态：`in_progress`、`awaiting_input`、`awaiting_step_approval`、`awaiting_compute_approval`、`awaiting_paradigm_approval`、`awaiting_failure_decision`、`complete`。

步骤8交付时仍是 `awaiting_step_approval`；用户确认收尾后才是 `complete`。状态文件是恢复索引，不是权限来源。`APPROVALS.md` 追加实际用户回复、日期、对应步骤/批次/版本与批准范围；审批工具返回“已提交问题”不等于已批准。

## 每轮经验条目

记录方法编号、假设、相对前轮改变、主要/次要指标、匹配基线、效应区间、机制干预、支持与反证、尚未解决的替代解释、人类判断、可继承经验、避免重犯项和原始证据路径。基线阶段的经验也在索引记录。运行中只写待验证，不填未来结果。

仓库已有中心台账时同步追加简洁结论和链接。没有台账则在批准的目录建立；首轮冻结前可快照仅含背景与待验证状态的初始台账，不虚构历史。保存快照时包括经验索引和本轮相关记录，避免单纯复制索引后其链接内容继续变化。
