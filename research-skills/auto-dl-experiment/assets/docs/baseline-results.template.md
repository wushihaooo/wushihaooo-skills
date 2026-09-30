<!-- 模板：复制为 docs/baseline/<B001-ABMIL>/results.md（去掉 .template）。尖括号为待填项，数值取自该基线的运行目录。填完删除本注释。 -->
# <B001-ABMIL> 结果

运行目录：[`runs/<B001-ABMIL>/<YYYYMMDD-HHMM>/`](<相对路径>)；状态：<已完成 / 未完成>。公共训练设置见 `desc-experiments/02-experiment-details.md`；本方法的全部参数和启动命令见运行目录的 `config.yaml`、`log.txt`。

<与原论文不同的适配，一两句话，如“按病例级两级聚合适配；CLAM 的 top/bottom-k 仅用于辅助损失”。无则删除本行。>

<K> 折，每折按验证 <选轮指标> 选出一个 checkpoint，下表全部指标都取自这个 checkpoint。阈值 <t>；Recall、Precision、F1 针对 <正类>；混淆矩阵顺序 [[TN, FP], [FN, TP]]。末行为 <K> 折均值 (标准差)（ddof=1），标准差不是置信区间。

## 验证集

各折在自己的验证分区上评价；含选轮偏差，不作为独立泛化估计。

| 折 | 选中 epoch | ACC | AUC | Recall | Precision | F1 | confusion matrix |
|---|---:|---:|---:|---:|---:|---:|---:|
| 1 |  |  |  |  |  |  |  |
| … |  |  |  |  |  |  |  |
| 均值 (标准差) | — |  |  |  |  |  |  |

## 测试集

<K> 折选中的模型分别评价同一固定测试集。

| 折 | ACC | AUC | Recall | Precision | F1 | confusion matrix |
|---|---:|---:|---:|---:|---:|---:|
| 1 |  |  |  |  |  |  |
| … |  |  |  |  |  |  |
| 均值 (标准差) |  |  |  |  |  |  |

<与病例级 MIL 规则的偏离（须为研究者已授权的探索性设置），或影响解读的实质限制。无则删除本行。>
