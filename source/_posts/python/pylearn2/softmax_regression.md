---
layout: post
title: Softmax regression
date: 2014-12-17
category: python
tags: [ml, python]
mathjax: ture
toc: ture
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
## 在这个例子中pylearn2做了什么
### 什么是softmax回归`softmax regression`, 数学上是怎么推导的
softmax回归是分类模型`classification model`的一种，这就意味着它是一种模式识别算法`pattern recognition algorithm`,<!--more-->能够把输入模型映射到不同的种类。在这个教程中输入模型是手写的数字，输出分类是(0-9)的数据。我们将用softmax回归去解决简单的视觉模式识别的问题`optical character recognition problem`。

你可能听说过[logistic regression](http://en.wikipedia.org/wiki/Logistic_regression)。Logistic回归是一种特殊的softmax回归，特别之处是它只有两种可能的输出类型。softmax回归是logistic回归的泛化，能产生多种输出类型(is a generalization of logistic regression to multiple categories)。

让我们定义一些基本术语`basic terms`。我们用变量\\(x\\)表示对softmax回归模型的输入，我们用变量\\(y\\)表示输出分类。\\(y\\)是一个非负整数，就像这样\\(0 \leq y \le k\\)，其中\\(k\\)是\\(x\\)可能属于的类别数。在我们的例子中我们把手写数字分类到值(0-9),值很好理解，当\\(y = 7\\)，分类识别就是7。在更多的应用中，我们理解\\(y\\)作为一个数值代码区分一种分类。e.g.(0 = cat, 1 = dog, 2 = airplane etc)

softmax回归分类器的工作就是预测\\(x\\)属于每个类的可能性，i.e. 我们能够计算概率\\(p(y=i \mid x)\\)对每个\\(i\\)对应的所有\\(k\\)可能的值。

像softmax回归的参数模型的作用是定义一个参数集合并且描述描述它们怎样映射到函数\\(f\\)(\\(f\\)的定义为\\(p(y \mid x)\\))。在softmax回归中，这个模型假设`the log probability of `\\(y=i\\)是一个仿射函数`affine function`的输入\\(x\\), `up to some constant`\\(c(x)\\)。\\(x\\)是任意使得分布加1的常数`make the distribution add up to 1`

为了使它更标准化，让\\(p(y)\\)写成一个向量\\([p(y=0),p(y=1),...,p(y=k-1)]^{T}\\)。假设\\(x\\)能够被表示一个数值型向量(在这个例子中，我们把灰度图像的每一个像素用[0,1]中的值来表示`we will regard each pixel of an grayscale image as being represented by a number in [0,1]`，我们将把2D序列的图像转换成一个向量用`numpy`的`reshape`方法)。然后假设softmax回归为
$$\log  p(y \mid x) = x^T W + b + c(x)$$
其中\\(W\\)是一个矩阵，\\(b\\)是一个向量，注意\\(c(x)\\)仅是一个标量`scalar`但这里我们把它加到一个向量上。在这数学公式里我用`numpy broadcasting rules`，即加\\(c(x)\\)到向量的每个元素，这个教程中我们都用这种规则。
\\(W\\)和\\(b\\)是这模型的参数，决定输入怎样映射到输出类型，我们通常称\\(W\\)为权重`weights`称\\(b\\)为偏置`biases`。根据一些代数学，用一些约束\\(p(y)\\)`must add up to 1`,我们可以得到:
$$ p(y \mid x) = \frac { \exp( x^T W + b ) } { \sum\_i \exp(x^T W + b\_i } = \text{softmax}( x^T W + b) $$
这里**softmax**是[softmax activation function](http://en.wikipedia.org/wiki/Softmax_function)

### softmax回归训练工作基本的原理
softmax模型会把\\(x\\)归类到正确的模型，如果它的参数被调整到使它们能够正确的映射(if its paramenters have been adjusted to make the specify the right mapping)，为了得到这样的结果，我们必须训练模型。基本的想法是我们已经收集了训练样例\\(D\\)。每一个样例是一个(x, y)元组。我们将调整训练集的模型，使得当跑训练数据时，它可以输出一个很好的估计——概率分布对于所有的\\(x\\)覆盖所有的\\(y\\)(it outputs a good estimate of the probability distribution over \\(y\\) for all of the \\(x\\)s)

一种适合的模型是极大似然估计[maximum likelihood estimation](http://en.wikipedia.org/wiki/Maximum_likelihood)。假设我们选取了一种策略变量\\(\hat {y}\\)从我们的模型分布\\(p(y \mid x)\\)对于所有的训练样例都是独立的。我们想极大化所有这些标签都是正确的可能性，为了这样做，我们极大似然化函数。
$$ J(\mathcal{D}, W, b) = \Pi\_{x,y \in \mathcal{D} } p(y \mid x ) $$
这个函数涉及很多连乘，可能是非常小的数(注意`softmax activation function`保证它们中没有元素会为0)。连乘很多个小数会造成数值精确度的降低，在实际中，我们通常使用对数函数来阻止下溢。
$$ J( \mathcal{D}, W, b) = \sum\_{x,y \in \mathcal{D} } \log p(y \mid x ) $$
很多不同的算法能够最大化\\(J\\)，在这个教程中我们将用名叫非线性共轭梯度算法[nonlinear conjugate gradient descent](http://en.wikipedia.org/wiki/Nonlinear_conjugate_gradient_method)最小化\\(-J\\)。在这个softmax回归中，最大值\\(J\\)是一个凸优化理论[convex optimization problem](http://en.wikipedia.org/wiki/Convex_optimization)所以任何优化算法应该发现相同的解。

## 怎样用pylearn2进行softmax回归
### 怎样载入数据到pylearn2中，特别是怎样载入MNIST数据集
为了用pylearn2训练一个模型，我们需要构造一些对象指定如何训练它，有两种方法去构造它。一种是显示的构造一个python对象，另一种是使用yaml字符串来指定它们。



