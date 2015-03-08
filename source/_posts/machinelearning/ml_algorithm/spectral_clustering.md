---
layout: post
title: 谱聚类算法
date: 2014-11-27
category: ml
tags: [ml, algorithm]
toc: true
---
谱聚类`Spectral Clustering, SC`是一种基于图论的聚类方法——将带权无向图划分为两个或两个以上的最优子图，使子图内部向量尽量相似，而子图间距离尽量较远，以达到常见的聚类的目的。其中的最优是指最优目标函数不同，<!--more-->可以是[割边](http://baike.baidu.com/view/388280.htm)最小分割——如图1(`Smallest cut`或`Min cut`),也可以是分割规模差不多且割边最小的分割——如图1(`Best cut`或`Normalized cut`)![谱聚类无向图划分——Smallest cut 和 Best cut]({{BASE_PATH}}/image/machinelearning/ml_algorithm/imag1.png)
这样，谱聚类能够识别任意形状的样本空间且收敛于全局最优解，其基本思想是利用样本数据的[**相似矩阵**](http://zh.wikipedia.org/zh/%E7%9B%B8%E4%BC%BC%E7%9F%A9%E9%99%A3)(拉普拉斯矩阵)进行特征分解后得到的特征向量进行聚类。
## 理论基础


