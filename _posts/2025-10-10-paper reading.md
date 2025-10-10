---
title: TEST
date: 2025-09-19 22:13 +/-TTTT
categories: [Medical Image, Multi-modal, Segmentation]
tags: [new papers sharing]
description: 这是一篇 arxiv 上最新的关于多模态医学图像分割任务中，解决模态缺失问题的研究。
math: True
---

### UniMRSeg: Unified Modality-Relax Segmentation via Hierarhical Self-Supervised Compensation

link: https://arxiv.org/abs/2509.16170v1

在真实应用场景下，由于传感器问题、低质量数据以及临床限制等等因素，可能会导致输入模态缺失的问题。最近在解决模态缺失问题的研究中，主要落脚到两个挑战。

#### Introduction
大多数方法（CMX、tokenfusion、CMNeXt、SSLSOD）聚焦于设计自适应的跨模态交互上，以合并多模态特征，同时保留单模态场景下的通用结构。然而在推理过程中，不同模态的结合需要分开的模型，或独立的模态编码器，这不仅提升了在实际部署中的资源消耗，同时需要额外的手动或自动的模态分类作为前提。尽管一些工作（M3AE、KD brats）使用了从完整模态到缺失模态的知识蒸馏方法，但它们仍然需要不同的模型来针对每一个模态子集，这复杂化了临床部署。

基于模态重建的方法（SSLSOD、M3AE、MaskMentor）目标在于预测缺失模态的输入，以在训练和推理过程中对齐特征。由于分割任务需要精确的空间特征和边界信息，而预训练的重建模型更加注重于全局特征压缩，这导致了特征表示的不足。因此直接从输入层面（SSLSOD、M3AE）或特征层面（MaskMentor）进行重建以降低模态差距是很困难的。特别地，如果将低质量重建预测结果作为分割网络的输入，会提升误差传播并降低分割效果。

#### Method
UniMRSeg 对于所有可能的模态输入完全共享 100% 的参数。完整的模态输入经常能够产生最好的预测结果，因此 UniMRSeg 的目标是在训练之后，使用任意模态输入的推理过程中，其能够接近完整的模态表征质量。对此， UniMRSeg 充分利用自监督学习，提出了一个层级化的补偿机制，作用于输入层面、特征层面和输出层面。

首先使用跨模态重建作为 pretext task。UniMRSeg 同时使用了全局和局部的掩码机制。然后，引入了一个 channel-wise 的模态 shuffling 操作，去破坏输入和重建模态之间的联系。进一步地，使用对比学习来完成特征层面的补偿机制。具体来说，UniMRSeg 将来自同一样本中的完整的和随机缺失的模态作为正样本对，那些来自其他样本的作为负样本对。为了增强对于分割任务的表征继承，UniMRSeg 联合优化空间距离度量和目标分割，以引导特征聚集到有益于分割结果的方向上。UniMRSeg 设计了一个轻量化的反向注意力 adapter ，嵌入到 3d SwinTransformer 中来捕获模态间高相应的互注意力模式，通过添加特征层面的一致性约束，确保 adapter 能够感知在任何模态缺失组合下，固有的部分表示缺陷。最后，UniMRSeg 在分割一致性约束下微调，输出的完整模态所表征的知识会被蒸馏到所有模态缺失组合中。

![img-description](pictures\UniMRSeg.png)
_UniMRSeg 架构图_