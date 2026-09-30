# 训练规范

## 一批正式训练的流程

步骤 2、5、6 的每一批正式训练（基线、新方法、消融）都按这个顺序：

1. **冒烟**：由 agent 运行，直接读输出和日志，出错就修复后重跑，直到通过。代码、数据、配置或环境有变化时重做。
   - 合成输入：张量形状、前向、损失、反向梯度有限。
   - 少量真实病例：保留其全部切片和 patch，并包含最大的病例；检查加载、前后向和显存峰值。
   - 检查点能保存和加载，预测的 `case_id` 与标签对齐。
   - 冒烟权重不用于正式训练；合成检查不能写成“真实数据冒烟通过”；显存不够时用等价的优化，不删 patch。
2. **交接**：冒烟通过后，按[开跑计划模板](../../assets/messages/launch-plan.template.md)报告冒烟结果和启动命令。
3. **训练**：永远由研究者在终端启动正式训练；agent 不代为启动，也不监控。进度状态为“等待研究者”，待决事项写“运行训练”。研究者通知结束后，先确认退出状态、`log.txt` 和各折产物齐全，再继续所在步骤；研究者报告出错时先诊断，不擅自重跑，不覆盖出错的运行目录。
4. **核验**：用各折的预测文件重算指标，与 `log.txt`、`metrics.csv` 一致。

## 训练与评价规则

- 所有比较的实验使用相同的训练上限、优化规则、验证频率和选轮规则；模型特有的设置写在各自的配置里。
- 每折按验证集上预定的指标选出 best 权重，全部指标都取自它；只保存 best 权重。
- 测试集不参与选 epoch、阈值、超参数或模块。
- 标准化等依赖数据的拟合只用训练部分。
- 重跑时新建时间目录，不覆盖旧结果。

## 输入文件

`train.py` 只读下面三种输入，格式严格按模板：

| 文件 | 模板 | 说明 |
|---|---|---|
| `configs/<实验编号>.yaml` | [config](../../assets/formats/config.template.yaml) | 每个实验一份完整配置；命令行参数可以覆盖其中的值（如 `--lr 1e-4`） |
| `data/metadata/labels.csv` | [labels](../../assets/formats/labels.template.csv) | 每张切片一行：`case_id, patient_id, slide_id, label`。生存任务把 `label` 换成 `time, event` 两列 |
| `data/splits/splits.csv` | [splits](../../assets/formats/splits.template.csv) | 每个病例一行：`set` 为 `dev` 或 `test`；`fold` 是该病例在第几折作验证集，测试集留空 |

## 运行目录

每次运行写到 `runs/<实验编号>/<YYYYMMDD-HHMM>/`。所有实验（基线、新方法、消融）的目录结构、文件名、CSV 列名和日志格式都严格按下面的规定，不增、不删、不改名；唯一随实验变化的是 best 权重文件名中的选轮指标和指标列。目录里只有训练程序自己写出的文件，不生成 markdown：

```text
runs/M001/20261001-0915/
├─ log.txt                  # 格式见 log 模板
├─ config.yaml              # 实际生效的全部超参数（配置文件与命令行覆盖合并后），字段同 config 模板
├─ metrics.csv              # 格式见 metrics 模板
├─ tensorboard/             # 训练曲线：各折的 loss、各项指标、学习率
└─ fold1/ … foldK/
   ├─ best-auc.pt           # 按验证选轮指标选中的权重，文件名随指标，如 best-cindex.pt
   ├─ val-predictions.csv   # best 权重在本折验证集上的逐病例输出，格式见 predictions 模板
   └─ test-predictions.csv  # best 权重在测试集上的逐病例输出，格式同上
```

| 文件 | 模板 | 规定 |
|---|---|---|
| `log.txt` | [log](../../assets/formats/log.template.txt) | 开头是启动命令、git commit 和开始时间；每个 epoch 一行，指标用 `\|` 分隔；每折结束写出选中的 epoch 及该权重的验证、测试结果；最后一行是结束时间。训练同时输出到终端；出错时把错误堆栈写入 `log.txt` 并以非零退出码结束 |
| `metrics.csv` | [metrics](../../assets/formats/metrics.template.csv) | 每折的验证、测试各一行，最后是验证和测试的均值行、标准差行（ddof=1） |
| `*-predictions.csv` | [predictions](../../assets/formats/predictions.template.csv) | 每个病例一行：`case_id, target, prob_<类别>…, pred`；之后算新指标、调阈值都不用重新推理 |
