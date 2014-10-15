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

名称空间与变量作用域的比较： 名称空间是纯粹意义上的名字和对象间的映射关系，而作用域还指出了从用户代码的那些物理位置可以访问到这些名字。
![名称空间和变量作用域的关系]({{BASE_PATH}}/image/core__python/imag4.png)
注意每个名称空间是一个自我包含的单元，但从作用域的观点来看，所有局部名称空间的名称都在局部作用范围内。局部作用范围以外的所有名称都在全局作用范围之内。

还要记住在程序的执行过程中，局部名称空间和作用域会随函数调用而不断变化，而全局名称空间是不变的。

**无限制的名称空间**`Namespaces for Free`
一个Python有用的特性能够让你在任何需要放置数据的地方获得一个名称空间。你可以在任何时候给函数添加属性。
```
In [209]: def foo():
   .....:     pass
In [210]: foo.__doc__ = 'Oops, forgot to add doc str above!'
In [211]: foo.version = 0.2
```
你可以把任何想要的东西放入一个名称空间里,想这样使用一个类(实例`instance`)是perfectly fine，不管名字如何，这个实例只是被用做一个名称空间。
```
In [214]: class MyUltimatePythonStorageDevice(object):
   .....:     pass

In [215]: bag = MyUltimatePythonStorageDevice()
In [216]: bag.x = 100
In [217]: bag.y = 200
In [218]: bag.version = 0.1
In [219]: bag.completed = False
```
随着深入的学习你会发现OOP是多么的有用，比如运行临时(且重要)变量的时候，正如《Python之禅》(Zen of Python)中陈述的最后一条:”Namespaces are one honking great idea ,let’s do more of those!”

### 导入模块`Importing Modules`
核心风格`Core Style`:import 语句的模块顺序
1. Python标准库模块`Standard Library modules`
2. Python第三方模块`third party modules`
3. 应用程序自定义模块`Application-specific modules`
然后使用一个空行分割这三类模块的导入语句，这将确保模块使用固定的习惯导入，有助于减少每个模块需要的import语句数目。

**载入时执行模块**`Module “Executed” When Loaded`
加载模块会导致这个模块被“执行”。也就是被导入模块的顶层代码将直接被执行，这通常包括设定全局变量以及类和函数的声明。你应该尽可能多的代码封装到函数，明确地说，只把函数和模块定义放入模块的顶层是良好的模块编程习惯。

**导入和加载**`import vs load`
一个模块只被加载一次，无论它被导入多少次，这可以阻止多重导入时代码被多次执行，加载只在第一次导入时发生。

关于`__future__`: 为了让Python程序员为新事物做好准备，Python实现了`__future__`指令。使用from-import语句导入新特性，用户可以尝试一下新特性或特性变化，以便在特性固定下来时候修改程序。

**从ZIP文件中导入模块**`Importing Modules from ZIP Files`
如果一个搜索路径中存在一个包含Python模块(.py,.pyc,.pyo文件)的.zip文件，导入时会把ZIP文件当作目录处理，在文件中搜索模块。

“新的”导入钩子`“New” Import Hooks`:Python2.3引入的新导入钩子，从而简化了这个操作。你只需编写可调用的import类，然后通过sys模块“注册”或者叫安装它`registered(or rather, “installed”)`。
你需要两个类：一个查找器`finder`和载入器`loader`。这些类的实例接受一个参数：模块或包的全名。查找器负责查找你的模块，如果它找到，那么将返回一个载入器对象。查找器可以接受一个路径用以查找子包`subpackages`。载入器会把模块载入到内存，它负责完成创建一个Python模块所需要的一切操作，然后返回模块。

这些实例被加入到`sys.path_hooks`.`sys.path_importer_cache`只是用来保存这些实例，这样就只需要访问`path_hooks`一次。最后，`sys.meta_path`用来保存一列需要在查找`sys.path`之前访问的实例，这些是为那些已经知道位置而不需要查找的模块准备的。`meta-path`已经有了指定模块或包的载入器对象读取器。

**globals()和locals()**
`globals()`和`locals()`内建函数分别返回调用者全局和局部名称空间的字典。在一个函数内部，局部名称空间代表在函数执行时候定义的所有的名字，`locals()`函数返回的就是包含这些名字的字典。`globals()`会返回函数可访问的全局名字。
`reload()`内建函数可以重新导入已经导入的模块，模块中的代码在导入时被执行，但只执行一次。以后执行import语句不会再次执行这些代码，只是绑定模块名称，而`reload()`函数不同。

### 包`Packages`
包是一个有层次`hierarchical`的文件目录结构,它定义了一个由模块和子包组成的Python应用程序执行环境，解决如下问题。
* 为平坦的名称空间加入有层次的组织结构(Adding hierarchical organization to flat namespace)
* 允许开发者把有联系的模块组合到一起(Allowing developers to group related modules)
* 允许分发者使用目录结构而不是一大堆混乱的文件(Allowing distributors to ship directories vs. bunch of files)
* 帮助解决模块命名冲突(Helping resolve conflicting module names)
与类和模块相同，包也使用`.`属性标识来访问它们的元素，使用标准的`import`和`from-import`语句导入包中的模块。
有下面包的路径的例子
```
Phone/
    __init__.py
    common_util.py
    Voicedta/
        __init__.py
        Pots.py
        Isdn.py
    Fax/
        __init.py
        G3.py
    Mobile/
        __init__.py
        Analog.py
        Digital.py
    Pager/
        __init__.py
        Numeric.py
```
Phone是最顶层的包，Voicedta等是它的子包，我们可以这样导入子包：
```
import Phone.Mobile.Analog
Phone.Mobile.Analog.dial()

#you can use from-import
from Phone import Mobile
Mobile.Analog.dial(‘555-1212’)

from Phone..Mobile.Analog import dial
dial(‘555-1212’)
```
在我们上边的目录结构中，有很多`__init__.py`文件，这些是初始化模块，`from-import`语句导入子包时需要用到它，如果没有用到，他们可以是空文件，程序员经常忘记为他们的包的目录加入`__init__.py`文件，将导致一个`ImportWarning`信息。
**绝对导入**`Absolute Import`: 包模块会把名字相同的标准库模块隐藏掉，为此，所有的导入现在都被认为是绝对的，也就是收这些名字必须通过Python路径(`sys.path`或是`PYTHONPATH`)来访问。这个决定的基本原理是子包也可以通过`sys.path`访问，作为折中方案，Python允许通过在模块或包名称前置句点实现相对导入。

**相对导入**`Relative Import`: 因为`import`语句总是绝对导入，所以相对导入应用于`from-import`语句。下边是一些可行的导入方法
```
from Phone.Mobile.Analog import dial
from .Analog import dial
from ..common_util import setup
from ..Fax import G3.dial
```
## 模块的其他特性 `Other Features of Modules`
**自动加载模块**`Auto-Loaded Modules`:当Python解释器在标准模式下启动时，一些模块会被解释器自动导入，用于系统相关的操作。唯一一个影响你的是`__builtin__`模块，它会正常地被载入，这和`__builtins__`模块相同。
`sys.modules`变量包含一个由当前载入(完整且成功导入)到解释器的模块组成的字典，模块名称作为键，它们的位置作为值。
```
In [226]: import sys
In [227]: sys.modules.keys()
Out[227]: 
['IPython.config.re',
 'IPython.core.error',
 ...
 'matplotlib.table',
 'numpy.testing.utils']
```
**阻止属性导入**`Preventing Attribute Import`:如果你不想让某个属性被`from module import *`导入，可以在导入的属性名称前加上一个下划线`_`。不过你导入整个模块或是显示地导入某个属性(i.e. `import foo._bar`),这个隐藏数据的方法就不起作用了。
**源代码编码**`Source Code Encoding`: `ASCII`是默认的，你只要在你的Python模块头部加入一个额外的编码指示说明就可以让导入者使用指定的编码解析你的模块，编码对应的Unicode字符串。所以你使用纯ASCII文本编辑器的时候不需要担心了，你不需要把你的字符串放入“Unicode标签”里。
```
#!/usr/bin/env python
#-*-coding: utf-8-*-
```

**导入循环**`Import Cycles`


## 相关模块
* `imp`: 这个模块提供了一些底层的导入者功能。(this module gives you access to some lower-level importer functionality)
* `modulefinder`: 该模块允许你查找Python脚本所使用的所有模块，你可以使用其中的`ModuleFinder`类或是把它作为一个脚本执行，提供你要分析的(另个)Python模块的文件名。
* `pkgutil`: 该模块提供了多种把Python包打包为一个“包”文件分发的方法，类似`site`模块。它使用`*.pkg`文件帮助定义包的路径，类似`site`模块使用的`*.pth`文件。
* `site`: 和`*.pth`文件配合使用，指定包加入Python路径顺序，例如`sys.path`,`PYTHONPATH`。你不需要显式地导入它，因为Python导入时默认已经使用该模块。你可能需要使用`-S`开关在Python启动时关闭它。你也可以完成一些`site`相关的自定操作，例如在路径导入完成后在另个地方尝试。
* `zipimport`: 你可以使用该模块导入ZIP归档文件中的模块，需要注意的是该功能已经“自动”开启，所以你不需要在任何应用中使用它。
* `distutils`: 该模块提供了对建立、安装、分发Python模块和包的支持。它还可以帮助建立使用`C/C++`完成Python扩展。

