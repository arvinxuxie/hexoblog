---
layout: post
title: Execution Environment
category: python
tags: [core_python]
---
## 可调用对象`Callable Objects`
**内建函数(BIFs)**
BIF是用c/c++写的，编译过后放入Python解释器，然后把他们作为第一(内建)名字空间的一部分加载进系统。这些函数在`__builtin__`模块里，并作为`__builtins__`模块导入到解释器。
**用户定义的函数(UDF)**
UDF(User-Defined Function)通常是用python写的，定义在模块的最高级，因此会作为全局名字空间的一部分(一旦创建好内建名字空间)装载到系统中。函数也可在其他函数体内定义，我们现可以对多重嵌套作用域中的属性进行访问。可以用`func_closure`属性来钩住在其他地方定义的属性。

