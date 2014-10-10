---
layout: post
title: Modules
category: python
tags: core__python
---
## 模块和文件
如果模块是逻辑上组织你的代码，那么文件是物理上的组织模块。模块的文件名就是模块的名字加上扩展名.py。
**模块的名称空间**:即使属性之间有名称冲突，但它们的完整授权名称(fully qualified name)--通过句点属性标识指定了各自的名称空间--防止了名称冲突的发生。

**搜索路径和路径搜索**`search path and path search`
模块的导入需要一个叫做路径搜索的过程。这个过程会检测“预定义区域”`predefined areas`查找你要导入的文件。这些预定义的区域只不过是你python搜索路径的集合。为避免两者的混淆，可以认为路径搜索是通过一组目录去找文件，后者是搜索路径。

默认搜索路径是在编译或是安装时指定的。它可以在一是启动Python的shell或命令行的PYTHONPATH环境变量修改。该变量的内容是一组冒号分割的目录路径。在启动解释器前设置修改此变量，解释器会使用该变量。解释器启动后它会被保存在`sys`模块中的`sys.path`变量里。
```
In [161]: sys.path
Out[161]: 
['',
 '/usr/bin',
 '/usr/lib/python2.7',
 '/usr/lib/python2.7/plat-linux2',
 '/usr/lib/python2.7/lib-tk',
 '/usr/lib/python2.7/lib-old',
 '/usr/lib/python2.7/lib-dynload',
 '/usr/local/lib/python2.7/dist-packages',
 '/usr/lib/python2.7/dist-packages',
 '/usr/lib/python2.7/dist-packages/PIL',
 '/usr/lib/python2.7/dist-packages/gtk-2.0',
 '/usr/lib/pymodules/python2.7',
 '/usr/lib/python2.7/dist-packages/IPython/extensions']
```
这只是个列表，如果你知道你需要导入的模块是什么，可调用`append()`方法。从另一方面看，你可能有一个模块的很多拷贝，这时，解释器会使用沿搜索路径顺序找到的地一个模块。

使用`sys.modules`可以找到当前导入了那些模块和它们来自什么地方。和`sys.path`不同，`sys.modules`是一个字典，使用模块名作为键(key),对应物理地址作为值(value)

**名称空间**`Namespaces`:是名称(标志符)到对象的映射。向名称空间添加名称的操作过程涉及到绑定标志符到指定对象的操作(以及给该对象的引用计数加1)。改变一个名字的绑定叫做重新绑定，删除一个名字叫做解除绑定。(changing the mapping to a name is called rebinding[, and] removing a name is unbinding).
程序执行期间有三个活动的名称空间，分别是局部名称空间，全局名称空间和内建名称空间。Python解释器首先加载内建名称空间。它由`__builtins__`模块中名字构成。随后加载执行模块的全局名称空间，它会在模块开始执行后变为活动名称空间。这样我们就有了两个活动名称空间。
core note: `__builtins__`模块和`__builtin__`模块不要混淆，`__builtins__`模块包含内建名称空间中的内建名字的集合。其中大多数(如果不是全部的话)来自`__builtin__`模块，该模块包含内建函数，异常以及其他属性。

名称空间与变量作用域的比较： 名称
