# 步骤4：实现提出的方法

目标：把已确认的方法落成代码，研究者用一条命令就能复现。

## 做什么

1. 在 `src/models/proposed/` 中加入新方法，共享基线的数据、划分、评价和选轮代码。
2. 按[配置模板](../../assets/formats/config.template.yaml)写 `configs/Mxxx.yaml`：本方法的全部超参数都在这一个文件里，公共设置与基线一致。
3. 核对实现与 `method.md` 逐项对应；有偏差时写进 `method.md` 的“复现”一节。
4. 做有限范围的合成输入检查和接口检查，不启动正式训练。
5. 若实质改变已确认的方法或输入，先回到步骤3确认，不能在实现中悄悄换方案。

## 产出

- 代码和 `configs/Mxxx.yaml`。
- `method.md` 的“复现”一节：一条可直接执行的命令 `python train.py --config configs/Mxxx.yaml`，以及实现与设计的偏差（没有则不写）。

## 完成后

直接进入步骤5。
