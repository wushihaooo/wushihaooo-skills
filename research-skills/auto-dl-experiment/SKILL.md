---
name: auto-dl-experiment
description: 计算机病理 WSI-MIL 深度学习实验的全流程自动化（建立背景文档、基线、方法设计与创新审查、实现、实验分析、消融、最终报告），每步产出人类刻度文档并由人类确认后推进。当用户要做 WSI/MIL 的基线实验、提出并验证新方法（Ours）、做消融实验或“科研探索”时使用。
---

# 自动深度学习实验

本 SKILL 指定了一个计算机病理 WSI MIL的实验研究的 pipeline。在了解实验背景信息和获取基线实验结果后，根据过往实验经验制定出新方法并进行实验验证分析，总结经验。最后得到一个能发表论文的方法。

## 实验流程

### 实验步骤模块

进入某个步骤前，先读该步骤对应的文件；只读当前步骤需要的，不要一次性读完全部 references。

| 步骤 | 名称 | 步骤说明（必读） | 相关规范与模板 |
| ---- | --- | --- | --- |
| 1    | 建立实验背景文档             | [step1](references/steps/step1-background.md)              | [产物与文档规范](references/artifacts-and-documents.md)      |
| 2    | 确认目录结构                 | [step2](references/steps/step2-dir-structure.md)           | [目录结构模板](assets/EXPERIMENT_DIRECTORY_STRUCTURE.md)、[产物与文档规范](references/artifacts-and-documents.md) |
| 3    | 实现并运行基线               | [step3](references/steps/step3-baseline.md)                | [基线报告模板](assets/BASELINE_REPORT.md)、[病例级 MIL 规则](references/pathology-mil.md)、[训练与证据规范](references/training-and-evidence.md)、[表格准则](references/表格准则.md) |
| 4    | 文献调查、创新审查与方法设计 | [step4](references/steps/step4-method-think-and-design.md) | [方法设计规范](references/method-design.md)                  |
| 5    | 实现提出的方法               | [step5](references/steps/step5-code-impl-method.md)        | [病例级 MIL 规则](references/pathology-mil.md)、[训练与证据规范](references/training-and-evidence.md) |
| 6    | 方法实验、证据分析与共同判断 | [step6](references/steps/step6-experiment-and-analysis.md) | [训练与证据规范](references/training-and-evidence.md)、[表格准则](references/表格准则.md) |
| 7    | 消融实验                     | [step7](references/steps/step7-ablation-experiment.md)     | [方法设计规范](references/method-design.md)（消融逻辑一节）、[训练与证据规范](references/training-and-evidence.md) |
| 8    | 最终汇报                     | [step8](references/steps/step8-final-report.md)            | [产物与文档规范](references/artifacts-and-documents.md)、[表格准则](references/表格准则.md) |

### 实验状态机

下面用 python 代码简要说明了每个步骤执行顺序的关系

```python
  step1()
  step2()
  step3()
  while True:
    step4()
    step5()
    step6()
    if (human_think_method_better_than_baseline ==  False):
      step8()
      continue
    step7()
    if (human_think_method_success == False):
      step8()
      continue
    step8()
    break
```

### 全局约定

- 所有结果表格遵循 [表格准则](references/表格准则.md)。
- 文档的写作原则、默认目录结构的格式见 [产物与文档规范](references/artifacts-and-documents.md)
- 每一步完成后，先更新 `WORKFLOW_STATE.json`，再停下等待人类确认。

## 特点

本 SKILL 有着人类可读性、人类介入性、人类可复现性、创新性、记忆性这些特点。

### 人类可读性

人类可读性包括以下几个方面：

1. 文档长度：ai agents 的输出长度是远超出人类阅读接受长度的，你需要分辨那些是需要呈现给人类阅读的内容，那些是因为严谨需要简要标注，那些是需要保存在一个复杂文档，不需要呈现给人类的内容。
2. 专业程度：在描述深度学习方法的过程中，需要考虑如何描述更加让人类易懂。如：这个知识是否是计算机深度学习默认的知识，还是需要解释。并且描述方法需要按照论文 Method 一节那样呈现出人类可读性，而不是AI设计很多繁杂的模块。
3. 图表优先：优先使用图表来展现内容，人类是视觉动物，图表更加直观。
4. 过往描述的内容可以简略，在曾经的设计中使用过的方法可以简略介绍，并表明在那里讲解过了。
5. 先写关键内容、结论和待决事项，优先用简短表格或结构图；不堆流程复述、重复约束、核验流水账和无关历史。
6. 若 SKILL 中提供了模板，优先使用模板，按照模板，不要添加额外内容。

### 人类介入性

人类介入性是允许人类介入到实验流程中的决策过程中，而不是让 agents 毫无指导地迭代实验，白白浪费计算资源。

1. 每个 step 完成，都要由人类监督，认为完成才能进入下一个 step。
2. 在 step4 时，人类可以对 agents 的方案提出否定，但是这并不能形成经验，因为否定的原因不一定是表现不行。否定的方案需要重新设计，并不要用特殊记号说明这是被人类否定过的。
3. 每个 step 的产出都要人类确认，这些产出大部分都需要是人类可读的。

### 人类可复现性

实验不能是 agents 来启动，未来人类会不知道如何复现这个实验，哪怕代码就在这里。这要求项目拥有良好的目录结构和方法（方法是指ABMIL和CLAM这种方法）的解耦性。

要求每一步都是由人类在终端输入指令来执行，agents需要在 step3 和 step5 给出执行的指令，如:
```
python main.py --model_type "abmil"
```

### 创新性

Ours 方法的目标是达到可投稿于同领域期刊/会议的创新水平，而不是对已有方法的微调或简单组合。

创新的判断标准，满足以下至少一条，且必须有明确动机支撑：

- 针对任务/数据的特定问题（如：数据特性、临床先验、已有方法的失效模式）提出新的机制；
- 对已有方法的局限做出有依据的分析，并提出针对性改进；
- 以新的方式建模或融合信息，且能解释其必要性。

以下情况不视为创新：
- 直接复用他人论文的核心结构，仅更换名称或数据集；
- 仅堆叠已有模块（如 A + B + C）而无"为什么需要这种组合"的论证；
- 仅调整超参数、backbone、特征提取器或训练技巧。

提出方法前必须完成

1. 文献对照：检索并列出与本方法最接近的 3-5 篇工作，逐篇说明与本方法的相同点和核心区别。不得凭记忆编造论文或结论，引用的工作必须能检索到出处。
2. 动机链条：写明“观察到的问题 -> 假设 -> 机制设计"，每个模块需说明其解决的具体问题。
3. 创新点声明：用一句话概括核心创新，并说明可以用那个实验/指标来验证它。

从”与最相近工作的差异程度、动机是否充分、是否可被消融验证“三方面自评，给出等级（高/中/低）及理由。

- 若为”低“：不得启动正式实验，应该返回调研，或和人类讨论。
- 不得为通过而夸大创新性，如实报告”创新不足“是被鼓励的。
- agent 的自评只作参考，人类确认后才进入大算力实验。
- 优先选择简洁、动机清晰的改进，而非复杂的多模块堆叠

### 记忆性

### 记录

```
docs/
├── experience/
│   ├── INDEX.md            # 经验索引（一行一条）
│   └── lessons/            # 提炼后的经验
└── explorations/
    └── 2026-xx-xx_主题/
        └── report.md       # 该次探索的完整记录
```



每次科研结束

失败是成功之母，失败的设计与实验要提取成经验管理到一个经验文档中，而不只是存放在那里，让后续的实验摸不着头脑。
经验包括，实验的失败否定了什么，否定力度怎么样。但是其中某些模块又是成功的，可以保留设计，拥有继续探索的潜力。

总的经验目录放置在 docs/experience/lessons