---
layout: post
title: 语义分割和场景分析
---
## 	任务
将图片分割成和语义类别有关的区域。
* semantic segmentation: 分割图片中的objects。e.g., PASCAL VOC 将图片分成20个object类别和1个背景类别。
* scene parsing: 分割图片中的objects & stuff。e.g., ImageNet Scene Parsing 将图片分成包括objects和stuff在内的150个类别。
<!--break-->

## Recent works
（不包括video和RGBD）

CVPR17
* 5 弱监督，其中有2个用webly supervised
* 2 instance
* 1  Joint Multi-Person Pose Estimation and Semantic Part Segmentation
* 其他
* workshop有一个from scratch

ICCV17
* 1弱监督
* 1半监督，用GAN增加训练数据
* 2和语言结合

## 基于深度学习的主要方法
需要解决的问题：数据不足以训练深度网络
* 在分类任务上预训练网络
	* 分类用的网络产生的特征图分辨率太低
	* 分类这个任务对位置不敏感，预训练的网络产生的特征图在位置上也不够精确
* 用参数少的网络就不用预训练了
	* 基于dense net
* 弱监督、半监督

### FCN
* 用卷积层产生每个类的标签图
* 用解卷积放大标签图
* 层之间的skip connection
* PASCAL VOC test: 62.2 mIoU
* VGG

### DeepLab
* 用扩张卷积得到高分辨率的特征图
* 处理“多种尺寸的目标“：用多尺度的扩张卷积核产生多尺度的特征图
* CNN特征图对位置不敏感导致目标的边缘不清楚，用CRF解决

### PSPNet
* 用扩张卷积产生比较大的feature maps
* 将最后一个卷积层输出的feature maps池化4个尺寸。这4个尺寸的feature maps和原来的feature maps串联，用串联的feature maps产生分割结果。增大了后续卷积的接受域。
* PASCAL VOC test: mIoU 79.7
* ResNet

### RefineNet
* 融合深层特征和浅层特征（不断从深层融合到浅层）来得到高分辨率的特征图
* 用chained residual pooling利用全局信息
* PASCAL VOC test: mIoU 83.4
* ResNet

### Large Kernel Matters
* 用1*k串联k*1的方式，使得可以使用大的卷积核同时又不引入过多参数
* 大卷积核的好处是直接扩大了接受域，使得分类更准确（感受域能够覆盖整个object）
* 提出了"boundary refinement"方法来更准确地定位语义区域的边缘
* PASCAL VOC test: mIoU 82.2
* ResNet

### denseNet from scratch
* 和FCN一样的用法，只是将VGG的部分换成了DenseNet

### 弱监督
#### Webly Supervised Semantic Segmentation
* 从网上抓取三种类型的图片：白色背景上的某类物体，一般背景（天空、森林之类的）、自然场景里的某类物体
* 用MR saliency 将白色背景上的物体抠出来，用抠出来的物体和一般背景的每个像素的hyper column特征，训练SNN （浅神经网络）分类器。有几个类就训练几个SNN分类器。
* 用CRF refine SNN分类的结果，用refine的结果再训练SNN
* 用SNN的结果训练DCNN
* CVPR17, PASCAL VOC test: mIoU 55.3

#### Adversarial erasing
* 训练分类网络　
* 用"learning deep features for discriminative localization", CAM，找出和类别相关的区域
* 去除找出的相关区域
* 通过迭代重复上述两步，找出更多相关区域。
* CVPR17, PASCAL VOC test: mIoU 55.7

### Learning Random Walk
* 用稀疏的标签监督
* 用label propagation的方法求出标签的分布，训练网络时最小化网络输出和这个分布之间的交叉熵
* CVPR17, PASCAL VOC val: mIoU 59.5

#### Semi-supervised using GAN
* 额外增加一个fake类
* 用FCN做判决网络
* 生成网络的训练目标为生成图片的像素不被分到fake类，判决网络的训练目标为正确分类图片里的像素，包括fake类。

### 和语言结合
#### open vocabulary scene parsing
* 让模型能够分割训练样本里没有的类别。
* 对AED20k里的标签标注WordNet里的上下位词分级
* 训练模型把像素和词语嵌入到同一个空间。这样模型能把没见过的类别分类成相似的词语。

#### VQS
将语义分割和视觉问题回答结合，提出新任务：通过分割图片回答视觉问题。

