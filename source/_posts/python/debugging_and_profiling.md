---
layout: post
title: Debugging and Profiling
date: 2014-10-11
category: python
tags: python
---
这些库帮助你进行python的开发，调试器`debugger`能够逐步执行代码，分析栈帧设置断点等，这些剖析器能够跑你的代码给你详尽的错误信息，帮你确认程序的瓶颈。
<!--more-->
Source code[lib/bdb.py](https://hg.python.org/cpython/file/2.7/Lib/bdb.py)
## bdb-Debugger framework
下面一些异常`exception`的定义:
bdb.BdbQuit：  退出调试器由Bdb类产生的异常。

`bdb`模块还定义了下面两个类：
*class* bdb.Breakpoint(self, file, temporary=0, cond=None, funcname=None)
这个类实现了临时断点，忽略`ignore counts, disabling and (re)-enabling and conditionals`
断点通过列表按数值索引称之为`bpbynumber`通过文件行号对(file, line)索引称之为`bplist`。前者主要指向一个断点类的实例,后者指向一个实例化的列表，因为每一行不止一个断点。
当我们创建一个断点`breakpoint`，与之关联的文件i名称应当被规范化，如果一个文件名被定义，一个断点触`breakpoint hit`将被记录当函数的的第一行被执行的时候。一个条件断点总是记录一个触动`A conditional breakpoint always counts a hit`

`Breadpoint`实例有以下方法:
`deleteMe()`: 删除断点从一个关联的文件/行的列表里。如果是最后一个断点的位置它也会删除整个file/line.
`enable()`: 标记使用断点。
`disable()`: 标记断点不可使用
`pprint([out])`: 打印关于断点的所有信息：
* 断点的序号(The breakpoint number)
* 判断它是否是临时的(if it is temporary or not)
* 它的文件和行号(Its file, line position)
* 是否在N次后被忽略(if it must be ignored the next N times)
* 断点命中次数(The breakpoint hit count)

*class* bdb.Bdb(skip=None)
这个类的作用是产生Python调试器的基类

这个类关心追踪的细节(trace facility)，派生类(derived class)实现用户交互。标准的调试器类`pdb.Pdb`就是一个例子。

这个`skip`参数如果了，一定是一个可迭代的`glob-stype module name patterns`。

下面这些方法一般不会被重写
`canonic(filenmae)`:   辅助`Auxiliary`方法为了获取一个规范`canonical form`的文件名。
`reset()`:   设置`botframe, stopframe, returnframe, quiting`属性的值准备调试
`trace_dispacth(frame, event, arg)`:   这个函数被作为追踪函数调试帧被装载。
这个默认实现取决于如何分配帧，支持被执行的事件类型。事件类型可以是下面的一种。
* `line`: 新的一行代码将被执行
* `call`: 一个函数被调用或是一些代码块被执行
* `return`: 一个函数或是其他代码块的返回
* `exception`: 一个异常的发生
* `c_call`: 一个C函数被调用
* `c_return`: 一个C函数返回
* `c_exception`: 一个C函数引起的异常

`dispatch_line(frame)`: 如果一个`debugger`应该停止在当前行，涉及`user_line()`方法(应当被子类重写)。引起一个`BdbQuit`异常。如果`Bdb.quitting`标志被设置(可以通过`user_line()`设置)。返回一个关于`trace_dispatch()`的方法进一步追踪在这个范围。
`dispatch_call(frame, arg)`: 如果这个

## pdb--The python debugger

