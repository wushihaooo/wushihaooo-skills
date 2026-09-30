# 步骤2：实现并运行基线

目标：在已确认的设置下得到可信、可复现的基线结果，作为后续所有方法的比较对象。

## 做什么

1. 实现共享的数据管线、划分、评价和训练器，输入和运行目录的格式严格按[训练规范](../guidelines/training.md)。把病例标签和划分整理成 `data/metadata/labels.csv`、`data/splits/splits.csv`。公共训练设置以 `02-experiment-details.md` 为准，本步骤定下的值同步写回那里。
2. 逐个实现 `03-experiment-protocol.md` 中列出的基线。每个基线是独立的实验，有自己的配置 `configs/<编号>.yaml`（按[配置模板](../../assets/formats/config.template.yaml)）、运行目录和文档目录。所有比较方法保持相同的数据使用与训练策略，不为某个方法单独增加训练预算。
3. 按[训练规范](../guidelines/training.md)的“一批正式训练的流程”运行，同时遵守[病例级 MIL 规则](../guidelines/pathology-mil.md)。多个基线可以同一批开跑，各自写各自的运行目录。
4. 为每个基线写 `docs/baseline/<编号>/results.md`（见“产出”）。
5. 创建 `docs/experience.md`，在“项目现状”写明最强基线及其主要指标（见 SKILL.md“记忆性”）。

## 产出

每个基线一个目录 `docs/baseline/<编号>/`，里面只有 `results.md`，使用[基线结果模板](../../assets/docs/baseline-results.template.md)。

- 参数和启动命令不在文档里重复，看运行目录的 `config.yaml`、`log.txt`；与原论文不同的适配（如 CLAM 的 top/bottom-k 仅用于辅助损失）在开头用一两句话说明。
- 验证与测试报告同一个选中 checkpoint 的全部指标，不能每项指标各取最佳 epoch。
- 只填已完成的结果；未完成的留空，不以历史最优数字补位。
- 基线之间的比较不单独成文，由各方法的 `results.md` 引用。

## 补充基线

任何阶段研究者都可以要求补充基线：在 `03-experiment-protocol.md` 中分配下一个编号（如 `B006-DSMIL`），只对这个基线执行本步骤，不重跑已有基线；完成后更新 `experience.md` 的“项目现状”，并告知研究者哪些已完成方法的比较需要补上这个基线。确认后回到补充前的状态。

## 完成后

停下，请研究者确认基线结果及进入步骤3。
