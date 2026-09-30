<!-- 模板：复制为 docs/desc-experiments/project-dir-structure.md（去掉 .template）。尖括号为待填项；按项目实际目录增删行，只保留有助于定位的层级；规划中尚不存在的目录标“（待建）”。填完删除本注释。 -->
# 实验目录结构

```text
<项目名>/
├─ train.py                       # 训练入口：python train.py --config configs/<实验编号>.yaml
├─ requirements.txt               # 依赖及版本
├─ configs/                       # <每个实验一份 YAML>
├─ docs/                          # 我们写的文档
│  ├─ global-state.md             # 当前进度
│  ├─ desc-experiments/           # 数据集、实验细节、实验协议、本目录说明
│  ├─ baseline/<B001-ABMIL>/      # 每个基线一个目录：results.md
│  ├─ propose-method/<Mxxx>/      # 每个方法：method.md、results.md、final-report.md
│  └─ experience.md               # 实验经验：每轮一条
├─ src/                           # 源码
│  ├─ datasets/                   # <Dataset 类、划分逻辑>
│  ├─ models/                     # <baselines/ 基线模型；proposed/ 新方法>
│  ├─ training/                   # <训练与验证一个 epoch、损失、选轮>
│  ├─ metrics/                    # <各种指标>
│  └─ utils/                      # <日志、参数解析>
├─ data/                          # <元信息与划分清单；图像特征在外部目录>
└─ runs/                          # 运行结果：实验编号 / 启动时间 / 各折
   ├─ <B001-ABMIL>/<YYYYMMDD-HHMM>/
   ├─ <M001>/<YYYYMMDD-HHMM>/
   └─ <M001-A01-no-gate>/<YYYYMMDD-HHMM>/   # 消融臂
```

数据位置：图像特征 `<外部特征目录>`；标签与元数据 `<路径>`。

## 一次运行里有什么

以 `<一个实际存在的运行目录，如 runs/B001-ABMIL/20260926-1430/>` 为例：

| 文件 | 内容 |
|---|---|
| `log.txt` | 运行日志：启动命令、git commit，之后每个 epoch 一行指标 |
| `config.yaml` | 本次实际生效的全部超参数，用它即可复现 |
| `metrics.csv` | 各折的验证与测试指标，以及均值、标准差 |
| `tensorboard/` | 训练曲线 |
| `fold<k>/best-<指标>.pt` | 按验证选轮指标选中的权重 |
| `fold<k>/val-predictions.csv`、`test-predictions.csv` | 验证集、测试集逐病例的 target、prob、pred |

## 实验编号

| 编号 | 实验 | 文档 |
|---|---|---|
| <B001-ABMIL> | <基线：ABMIL> | [B001-ABMIL](../baseline/B001-ABMIL/) |
| <M001> | <一句话说明方法> | [M001](../propose-method/M001/) |
| <M001-A01-no-gate> | <M001 去掉门控的消融> | [M001 结果·消融](../propose-method/M001/results.md) |

<只列项目实际存在的编号。有历史实验目录时，在此加一张“历史目录 → 对应位置”的映射表；没有则删除本行。>
