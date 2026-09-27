# 实验目录结构

```text
free_research/
├─ data/metadata/            # 病例标签、病理报告及数据清单；图像特征位于外部数据目录
├─ src/                      # 公共模块：data 数据读取、models 模型、metrics 指标
├─ scripts/                  # 执行入口：prepare 准备、train/launch 训练、evaluate 评价、audit 检查
├─ configs/                  # 环境与公共配置；每次实验的配置保存在对应产物目录
├─ tests/                    # 代码测试
├─ tools/                    # 工作区整理等辅助工具
├─ docs/
│  ├─ desc_experiment/       # 数据集、实验细节、总体协议、本目录说明及流程状态
│  ├─ baseline/              # 基线结果与比较表
│  ├─ propose_arch/          # 方法设计、实现、结果与最终报告
│  ├─ experience/            # 各轮经验与结论索引
│  └─ protocols/、research/、results/、history/、project_map/
│                            # 历史协议、研究资料、报告及导航
└─ artifacts/
   ├─ results/               # 跨实验结果索引，从 INDEX.md 查找各版本
   ├─ research_ledger/       # 研究结论、失败经验及依据
   ├─ data_audit/            # 数据统计、核验记录与整理快照
   ├─ runs/<batch_id>/       # 新批次：协议、输入清单、冻结源码和运行结果
   │  └─ runs/<方法>/foldN/ # 方法下的第 N 折；保存检查点、逐轮验证及最终测试预测
   └─ <实验名称>_vN/         # 各版本的原始产物，保持独立；内部布局因历史版本而异
      ├─ PROTOCOL.md         # 本版本实验方案
      ├─ run_config.json     # 本版本运行参数（部分版本名为 config.json）
      ├─ source/、source_snapshot/  # 实验代码及冻结副本（若有）
      ├─ data/               # 本版本的数据清单与划分
      ├─ runs/<运行分组>/    # 各模型或实验臂的权重、预测与训练记录
      └─ analysis/           # 指标核验、诊断与汇总分析
```

## 版本与文件名

以下以已有实验 `artifacts/recurrence_binary_cls_repair_v23/` 为例：

| 名称或字段 | 含义 |
|---|---|
| `recurrence_binary_cls_repair_v23` | `recurrence` 为复发任务，`binary_cls_repair` 为实验主题，`v23` 为历史版本号。完整目录名标识实验；不同主题可能共用同一个版本号。 |
| `runs/abmil/`、`runs/clam/` | 同一版本下的模型分组；其他实验臂名称按该版本协议解释。不同版本的运行文件各自保存，不相互覆盖。 |
| `best.pt`、`last.pt`、`resume.pt` | 选中的最佳模型、末轮模型、断点续训状态；选轮规则见该版本配置。 |
| `predictions_epoch01.json`、`best_predictions.json` | `epoch01` 表示第 1 轮的预测；`best` 为选中模型的预测。 |
| `history.json`、`result.json` | 逐轮训练记录、该运行的结果汇总。 |

历史实验沿用原目录名。后续新方法按 `M001`、`M002` 编号，同方法修订用 `M001-r02`；每次执行另设唯一 `run_id`，区分方法版本与运行批次。

基线用方法名标识，不编方法编号；例如批次 `baseline_dataset2_20260926` 表示数据集二在该日期准备的基线运行，`fold1` 表示第一折。

查看全部实验：[结果索引](../../artifacts/results/INDEX.md)。
