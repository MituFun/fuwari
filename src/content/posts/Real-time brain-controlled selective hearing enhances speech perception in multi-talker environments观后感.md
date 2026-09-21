---
title: Real-time brain-controlled selective hearing enhances speech perception in multi-talker environments观后感
published: 2026-09-21
description: ''
image: ''
tags: ['算法','脑机接口']
category: '科研'
draft: false 
lang: ''
---

> 论文原文：https://www.nature.com/articles/s41593-026-02281-5

这篇论文实现了根据人脑意愿，具有特异性地提高人脑注意的声音音量。

此论文之前对于 AAD（听觉注意力解码）已有较多实现方法，但是并没有具体实践，此论文实现了 AAD 的应用。

此论文实现了以下操作：

1. 获取 iEEG（颅内脑电）
2. 通过 iEEG 使用 linear regression decoder 重建 speech envelope
3. 利用重建出的 speech envelope 与两段声音的 speech envelope 进行比对（采用 Pearson correlation 实现），实现 AAD 来获取目标声音
4. 放大 attended speaker，压低 unattended speaker

关于 speech envelope 为何能实现 AAD：

speech envelope 可以理解为一句话随时间变化的声音强弱节奏，听觉皮层的神经活动对此会产生相应的神经响应。而伴随着人脑对某一声音的注意更强，神经活动会更符合那段声音的 speech envelope。

---

关于第三步的一些细节：

脑信号很嘈杂，很短一段时间内无法实现 AAD。故论文采取滑动窗口，窗口大小为 4 秒，每 0.5 秒更新一次。

关于为什么是 4 秒：

- 时间短会导致判断容易错，因为数据太少
- 时间长会导致反应太慢

此为作者折中之选

---

作者的三个实验：

1. **Experiment 1: real-time AAD provides significant and multifaceted perceptual benefits**

   先关闭系统，后打开系统。关闭的时候 TMR\*=-6db，打开后实时增强

   关于 benefits 的判定，作者用三种方法判定：

   - 每人 20 个 trials，选择 ON 占比 75%~95%

   - ON 后回答关于目标语音的问题表现更好

   - 通过 pupil dilation（瞳孔扩张）来判断受试者的听觉努力程度

     这个我感觉相当的巧妙，因为瞳孔变化可以作为 listening effort 的一种生理指标，将一个难以评判的主观感受客观化

   \* TMR：Target-to-Masker Ratio，可以理解为目标声音相对于干扰声音有多响。

   此实验证明了论文实现的算法对听觉体验有实际帮助。

2. **Experiment 2: real-time AAD tracks instructed attention shifts**

   系统全程开启，一开始要求受试者注意 A，随后中途屏幕提示改变为注意B。实验证明系统可以成功切换。

   实验指出从提示到 TMR=0db 平均需要 5.1s。此延迟与上文所述滑动窗口大小以及为了体验更好而采取的声音较为流畅地平滑音量变化有关。

   这个我一开始读的时候也有疑问，我认为会进入一个正反馈循环。但是这个实验证明确实没什么问题。

3. **Experiment 3: real-time AAD tracks self-initiated attention shifts**

   就是把 Experiment 2 的屏幕提示改为受试者主动改变注意。

   实验成功。























