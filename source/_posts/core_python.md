---
layout: post
title: core_python
category: python
tags: [python]
mathjax: true
---
# python基础
## 起步
### 输入输出
交互式编程变量输出
在解释器中(`_`)代表最后计算的表达式。
```python
>>> myString = ‘Hello World’
>>> print mystring
    Hello World
>>> _
   Hello World
```
`>>`被用于重定向输出
下面将输出重定向到标准错误输出
```python
import sys
print >> sys.stderr, ‘Fatal error: invalid input!’
```
将输出重定向到文件中
```python
logfile = open(‘/tmp/mylog.txt’, ‘a’);
print >> logfile, ‘Fatal error: invalid input!’
logfile.close()
```
### 注释
同在Unix-shell语言中用**#**作为注释
```python
>>> #one comment
    print ‘Hello World’ # another comment
    Hello World!
```
`doc strings`文档型注释，用来加在模块、类、函数的开始
```python
def foo():
    “This is a doc string.”
    return True
```
不同于常规的注释`doc strings`可以在程序运行时被访问，也可用来自动生成文档
python 可以用`obj.__doc__`方法来访问`obj`的`doc string`其中`obj`可以是模块类或函数的名称

### 变量和赋值
python支持5种基本的数值类型
* int (signed integers)
  * long (long integers)
  * bool (Boolean values)
* float (floating point real numbers)
* complex(complex numbers)
“Multuple” Assignment(多元赋值)
```python
>>> x, y, z = 1, 2, ‘a string’
>>> x
1
>>> y
2
>>> z
‘a string’
```
交换变量，可以不用临时变量
```python
#swapping variables in Python
>>> x, y = 1, 2
>>> x
1
>>> y
2
>>> x, y = y, x
>>> x
2
>>> y
1
```

字符串类型可以用`’’’`三个引号去消除特殊字符的转意，还支持**[:]**的分片
```python
>>> pytstr = ‘Python’
>>> pystr[0]
‘P’
>>> pystr[2:5]
‘tho’
>>> pystr = ‘’’python
    is cool’’’
>>> pystr
‘python\nis cool’
>>> print pytstr
python
is cool
```
专用下划线标志符
_ \_xxx 不能被导入 `from module import *`
_ \_\_xxx\_\_ 系统定义的名称
_ \_\_xxx 类中私有成员的名称


### 实用的函数
| Function                     | 描述                                                                                    |
| -------------                | --------------------------------                                                        |
| dir([obj])                   | 显示**对象**的属性或为空显示全局变量                                                    |
| help([obj])                  | 显示**对象**文档的方法属性                                                              |
| int(obj)                     | 将对象转化成整型                                                                        |
| len(obj)                     | 返回对象的长度                                                                          |
| open(fn, mode)               | 以**mode**模式打开**fn**(‘r’ = read, ‘w’ = wirte)                                       |
| range([[start,]stop[,step]]) | 返回一个以`start`开始(默认是0)，以`stop`结束(左闭右开)的步长为`step`(默认是1)的整型数列 |
| str(obj)                     | 将`object`转化成字符串                                                                  |
| type(obj)                    | 返回对象的类型                                                                          |

### 模块结构布局
1. startup line (Unix)
2. module documentation
3. module imports
4. varialbe declarations
5. class declarations
6. function declarations
7. “main” body
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*- #startup line
“this is a test module”    #Module documentation

import sys    #Module imports
import os

debug = True    #(Global) Variable declarations

class FooClass (object):    #Class declarations
    “Foo class”
    pass

def test():               #Function declarations
    “test function”
    if debug:
        print ‘ran test()’

if __name__ == ‘__main__’:    #”main” body
    test()
```
* 如果是被导入则`__name__`为被导入名称
* 如果程序是直接执行则`__name__`为`__main__`
可以方便的用于模块测试

### 内存管理
引用计数器

为了在内存中追踪对象，Python用了一个简单的`reference counting`引用计数的机制。当一个对象创建时会增加该对象的一个引用,对象的新引用可以称为它的变量的别名`aliases`。
* 创建一个对象
```python
x = 3.14
```
* 为创建的对象生成一个别名
```python
y = x
```
* 被作为参数传递给函数(新的本地的引用)
```python
foobar(x)
```
* 当变量被赋值给另一个对象时，原对象的引用计数自动减1
```python
foo = ‘xyz’
bar = foo
foo = 123
```
del 语句语法
```
del obj1[, obj2[,...objN]]
```
执行`del y`会产生两个结果
1. 从现在的名字空间中删除y
2. x的引用计数减一
注意，执行`del x`删除该对象最后一个引用时，即该对象的引用计数减为0,会导致该对象“无法访问”。该对象成为垃圾回收机制的对象。但任何追踪或调试程序会给一个对象增加一个额外的引用，这会推迟回收时间

**总结对象的引用减少的情况**
1. 局部引用超出其作用域。像`foobar()`执行完后的局域引用变量
2. 别名被显示的销毁 `del y`
3. 别名被赋予一个新的对象
4. 显示的从一个对象容器中移除
```
myList.remove(x)
```
### 垃圾回收(Garbage Collection)
python的垃圾回收器实际是一个引用计数器和循环垃圾收集器的组合。当一个对象引用计数变为0时，解释器会暂停释放掉这个对象和仅由这个对象可访问的其它对象。垃圾收集器也会留心被分配的总量很大(及未通过引用计数销毁的那些)的对象，解释器暂停试图清理所有未引用的循环。
### 相关的模块和开发工具
* Debugger: pdb
* Logger: logging
* Profilers: profiles, hotshot, cProfile

### Python 对象
所有的python对象有以下三个特征:*identity*标识，*type*类型，*value*值
identity: 每个对象有自己的唯一标识，他可以通过调用`id()`内建函数来获取。这个值可以被认为是该对象的内存地址。
type: 对象的类型标志着这个对象可以存储什么值，可以进行什么样的操作，可以使用内建函数`type()`可以查看对象类型,因为类型也是对象所以`type()`返回的是对象，而不仅是字符串。
value: 对象表示的数据项。
这三个特征只有前两个都是只读的，如果一个对象支持更新操作他的值会改变。

**标准类型**
* Numbers
  * Integer
    * Boolean
    * Long integer
  * Floating point real number
  * Complex number
* String
* List
* Tuple
* Dictionary
**其他内建类型**
* Type
* Null object (None)
* File
* Set/Frozenset(集合/固定集合)
* Function/Method(函数/方法)
* Module
* Class
对象类型
```python
>>> type(42)
<type ‘int’>
```
让我们仔细看一下，它的返回类型。
```python
>>> type(type(42))
<type ‘type’>
```
`None`是pyton的Null对象
python有一个特别的类型就是`Null object`空对象或是`None Type`空类型。它没有任何操作`None`类型和C语言中的`void`相似，`None`的值和C语言中的`NULL`相似。
**Core Note** 布尔类型的值
对象有隐式的`True`或`False`值。当对象为空或者数值为0时为`False`
在python中下面定义为`false`
* None
* False (Boolean)
* Any numberic zero:
  * 0 (integer)
  * 0.0 (float)
  * 0L(long integer)
  * 0.0 + 0.0j(complex)
* “” (empty string)
* []\(empty list\)
* () (empty tuple)
* {} (empty dictionary)

**内部类型**
`代码对象(Code Objects)`：是python编译(`byte-compiled`)后的可执行的代码片段。通过调用`compile()`**BIF**得到代码对象。代码对象可以被`exec()`或是`eval()`**BIF**来执行。
代码对象本身不包含任何执行环境信息，它是用户自定义函数的核心，在被执行时动态获得上下文。(事实上代码对象是函数的一个属性),一个函数除了有代码对象属性以外，还有一些其他函数必须的属性，包括函数名，文档字符串，默认参数，及全局命名空间。

`帧对象(Frame Objects)`： 帧对象表示python的执行栈帧。帧对象包含Python解释器在运行时所需要知道的所有信息。它的属性信息包含指向上一栈帧的链接，正在被执行的代码对象，本地及全局名字空间字典以及当前指令等。每次函数调用产生一个新的帧，每一个帧对象都会相应创建一个C栈帧。用到的帧对象的一个地方是跟踪记录对象。

`跟踪记录对象(Traceback Objects)`： 当python运行中产生错误时会产生一个异常。如果异常没有被捕捉或处理，解释器就会退出并产生一些出错信息。当异常发生时，一个包含针对异常的栈跟踪信息的跟踪记录对象被创建。如果一个异常有自己处理程序，处理程序就可以访问这个跟踪记录对象。

`切片对象(Slice Objects)`: 当使用python扩展的切片语法时就会创建切片对象。扩展的切片语法允许对不同的索引切片操作，包括步长切片(stride indexing)，多维切片(multi-dimensional indexing), 省略切片(Ellipsis type indexing)。多维切片：`sequence[start1: end1, start2: end2]`.省略切片：`sequence[..., start1: end1]`。切片对象也可以通过调用`slice()`**BIF**产生。
步长切片需要第三个参数步长：`sequeuece[starting_index: ending_index: stride]`。
```python
>>> foostr = ‘abcde’
>>> foostr[::-1]
‘edcba’
>>> foostr[::-2]
‘eca’
>>> foolist = [123, ‘xba’, 342.23, ‘abc’]
>>> foolist[::-1]
[‘abc’, 342.23, ‘xba’, 123]
```

**`省略对象(Ellipsis Objects)`**: 省略对象用于扩展切片语法中，起记号作用。这个对象用于切片语法用(...)表示。类似Null对象的`None`，省略对象有唯一的名字`Ellipsis`它的布尔值始终是`True`。

**`xrange 对象`**: Xrange对象被`xrange()`**BIF**创建。`xrange()`用于内存有限制或是`range()`无法完成的超大数据集。

**标准类型运算符**

对象值比较：比较操作是针对对象值进行的，而不是对象本身。
不同于其他语言多个比较可以放在同一行中，运算顺序是从左到右。
```python
>>> 3 < 4 < 7     #same as (3 < 4) and (4 < 7)
True
>>> 4 > 3 == 3     #same as (4 > 3) and (3 == 3)
True
>>> 4 < 3 < 5 != 2 < 7
False
```

**对象身份比较(Object Identity Comparison)**

每个对象都天生具有一个计数器，记录它自己的引用次数，表示有多少个变量指向该对象。python提供`is`和`is not`运算符来测试两个变量是否指向同一对象。
```
a is b
等价于
id(a) == id(b)
```
实践
```
>>> a = 1024
>>> b = a
>>> a is b
True
>>> b = 1024
>>> a is b
False
```

`Core Note`: 整数对象和字符串对象是不可变的对象，所以Python会很高效的缓存它们。这就会造成我们认为python应该创建新对象时，它却没有创建新对象的假象。
```python
>>> a = 1
>>> id(a)
8402824
>>> b = 1
>>> id(b)
8402824
>>> c = 1.0
>>> id(c)
8651220
>>> d = 1.0
>>> id(d)
8651204
```
Python仅缓存简单的整数，因为他认为这些小数会在程序中经常用到。python2.3中决定在预定义缓存字符串表之外的字符串如果不再有任何引用指向它则这个字符串将不会被缓存。

**标准内建函数**

* `type()`：python2.2之后它变成一个“工厂函数(factory function)”，我们还可以将它当作一个**BIF**来使用。它得到对象的类型，并返回相应的`type`对象。
* `cmp()`: 用于比较两个对象，如果两个对象不是标准类型`standard type`而是用户自建对象`user-created class`，`cmp()`会调用该类的特殊方法`__cmp__()`。
```python
>>> a, b = ‘abc’, ‘xyz’
>>> cmp(a, b)
-1
>>>cmp(b, a)
1
>>> b = ‘abc’
>>> cmp(a, b)
0
```
* `str()` 和 `repr()` (及\`\` 运算符)：其中`repr()`和\`\`相同但后者不鼓励使用，它们返回的是一个对象的**官方**`official`的字符串表示，(大多数情况)能够被求值表达式`eval()`**BIF**重新得到该对象。但`str()`致力于生成可读性好的字符串表示，返回结果无法用`eval()`求值，适合用`print`输出。
```python
>>> a = ‘abc’
>>> str(a)
‘abc’
>>> repr(a)
“‘abc’”
>>> b = eval(repr(a))
>>> b
‘abc’
```
* `type()` 和 `isinstance()`: python不支持函数方法重载，所以你自己检查`instrospection`对象是正确的。我们可以用`type`来帮助我们。
```python
>>> type(‘’)
<type ‘str’>
>>> class Foo: pass
...
>>> foo = Foo()
>>> type(Foo)
<type ‘classobj’>
>>> type(foo)
<type ‘instance’>
>>> class Bar(object): pass
...
>>> bar = Bar()
>>> type(Foo)
<type ‘classobj’>
>>> type(bar)
<class ‘__main__.Bar’>
```
下面看一下`isinstance()`的用法
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

def displayNumType(num):
    print num, ‘is’,
    if isinstance(num, (int, long, float, complex)):
        print ‘a number of type: ’, type(num).__name__
    else:
        print ‘not a number at all!’
displaynumtype(-69)
displaynumtype(9999999999999999999999999999999999999L)
displaynumtype(98.6)
displaynumtype(-5.2+1.9j)
displaynumtype(‘xxx’)

python typechk.py
-69 is a number of type: int
99999999999999999999999999999999999 is a number of type: long
98.6 is a number of type: float
(-5.2+1.9j) is a number of type: complex
xxx is a number at all!
```

**类型工厂函数(Type Factory Function)**
下面的熟悉的工厂函数也是内建函数
* int(), long(), float(), complex()
* str(), unicode(), basestring()
* list(), tuple()
* type()
以前没有工厂函数的其他类型，现在也都有了工厂函数。支持新风格的类的全新数据类型也添加了相应的工厂函数。
* dic()
* bool()
* set(), frozenset()
* object()
* classmethod()
* staticmethod()
* super()
* property()
* file()

**标准类型的分类**

* “Basic(基本)”: 是那些python提供的标准或是核心类型
* “Built-in(内建)”: 这些类型是python默认提供的
* “Data(数据)”: 它们用于一般数据的存储
* “Object(对象)”： 对象类型是对数据和功能默认的抽象
* “Primitive(原始)”: 它们提供最底层粒度的数据存储(lowest-level granularity of data storage)
* “Types”: 他们就是数据类型

`存储模型(Storage Model)`: 第一种分类方法是看这种类型的对象能存储多少对象。python的类型像其他语言一样能保存单个或多个值。一种类型能保存单个文字对象`literal object`,我们称之为原子`atomic`或标量`scalar`存储。那些能保存多个对象称之为容器存储`container storage`(Container Object容器对象有时在其他文档中成为复合对象`composite or compound ojects`,但是这些这些不仅仅指类型还包括对象实例`class instances`)。容器类型有一个问题是不同类型对象的存储，python中可以存储不同类型的对象。

以存储模型分类python类型的对应
scalar/atom(原子类型）          数值型`Numbers`(all numeric types), 字符串`strings`(all ar literals)
Container(容器类型)             Lists, tuples, dictionaries
尽管字符串类型`strings`看起来像容器类型因为它可以包含字符`characters`。但是python中没有字符类型`character type`像`char`。所以字符串类型是自我包含的文本`self-contained literals`

`更新模型(Update Model)`: 以是否可以被更新进行分类。
可变的`Mutable`              Lists, dictionaries
不可变的`Immutable`          Numbers, strings, tuples
通过下面示例来说明对象的变化
```python
>>> x = ‘Python numbers and strings’
>>> print id(x)
16191392
>>> x = ‘are immutable What gives？’
>>> print id(x)
16191122
>>> i = 0
>>> print id(i)
77749552
>>> i = i + 1
>>> print id(i)
3349600
>>> alist = [‘ammonia’, 83, 85, ‘lady’]
>>> print id(alist)
135443480
>>> alist[2] = alist[2] + 1
>>> alist[3] = ‘stereo’
>>> print id(alist)
135443480
>>> alist.append(‘gaudy’)
>>> alist.append(alist[2] + 1)
>>> print id(alist)
135443480
```

访问模型`Access Model`: 根据访问方式进行分类
直接访问`Direct`           数值型`Numbers`
顺序访问`Sequence`         字符串`strings` ， 列表`lists`， 元组`tuples`
键值型`Mapping`            字典`Dictionaries`


无法分配的类型`Unsupported Types`: 
`char`或`byte`: Python没有char或byte类型，可以用长度为1的字符串表示字符或是8比特整数。
指针`pointer`: 因为python替你管理内存，没有必要去获取指针地址。最接近地址的是用python对象中的`id()`**BIF**方法获取。你不能控制它的值，所以没有意义，其实python中一切皆为指针。

### 数值 `Numbers`

标准整型`Standard Integers`:32位机是32位-2^31$到2^31$-1。64位系统整数有64位。
长整型`Long Integers`的限制取决于虚拟内存的`virtual memory`。长整型是标准整型的超集`superset`，在一个整型后面加个`L`表示长整型。而且整形和长整型正在统一，当整型溢出pyton自动转化为长整型。

双精度浮点数`Double Precision Floating Point Numbers`: python的浮点型`Floats`近似于C语言的`doubles`。他们占8-byte(64-bit),完全遵循IEEE754的定义(53M/11E/1S)52位表示底，11位表示指数，剩下一位表示符号位。

复数`Complex Numbers`: 
```python
>>> aComplex = -8.333 -1.47j
>>> aComplex.real
-8.333
>>> aComplex.imag
-1.47
>>> aComplex.conjugate()   #返回共轭复数
(-8.333+1.47j)
```
传统的除法(Classic Division)
```
>>> 1 / 2 #perform integer result (floor) #地板除
0
>>> 1.0 / 2.0   #returns actual quotient #真正的除法
0.5
```
真正的除法(True Division)
python未来可能标准化的除法行为
```python
>>> from __future__ import division
>>> 1 / 2         #returns real quotient
0.5
```

地板除(Floor Division)
```
>>> 1 // 2
0
>>> 1.0 // 2.0
0.0
>>> -1 // 2
-1
```

位运算符(\*Bit Operators)只能是整型
取反(~), 按位与(&), 按位或(|), 异或(^), 左移(<<), 右移(>>)

功能函数：
```
>>> abs(-1)
1
>>>abs(2+2j)
2.8282...
>>> coerce(1.3, 134L)         #将两个参数按照python内置规则统一类型并返回一个元组
(1.3, 134.0)
>>> coerce(1j, 134L)
(1j, (134+0j))
>>> divmod(10, 3)       #对于整数返回是地板除和取于操作，对于浮点数商部返回math.floor(num1/num2),复数是ath.floor((num1/num2).real)
(3, 1)
>>> divmod(10, 2.5)
(4.0, 0.0)
>>> divmod(2+1j, 0.5-1j)
(0j,(2+1j))
>>> round(3.45)  #对于浮点数进行四舍五入，后面可指定保留小数位数
3.0
>>> round(3.49999, 1)
3.5
```

仅用于整型函数
```
>>> hex(255)
‘0xff’
>>> oct(255)
‘0377’
>>> ord(‘a’)
97
>>> chr(97)
‘a’
```

相关模块
`decimal`: 十进制浮点型类Decimal
`array`: 高效的数值数组(字符，整型，浮点等)
`math/cmath`: 标准的C库数学运算函数，复数运算在`cmath`里
`operator`: 数值运算符的实现如`operator.sub(m, n)`等价于`m-n`
`random`: 多种伪随机数生成器
核心模块random
`randint()`: 放入两个整数，返回两个整数的之间的值闭区间
`randrange()`: 接收和`range()`函数一样的参数，随机返回`range([start,]stop[,step])`结果中的一项
`uniform`: 返回二者之间的一个浮点数开区间
`random`: 类似`uniform()`,但下限恒为0.0,上限恒为1.0
`choice()`: 随机返回给定序列中的一个元素。


### 序列：字符串，列表，元组
切片操作(Slice[], [:], [::])
```python
>>> s = ‘abcdefgh’
>>> s[::-1]
‘hgfedcba’
>>> s[::2]
‘aceg’
```
切片返回尾部递减字符串
```python
s = ‘abcde’
for i in [None] + range(-1, -len(s), -1):
    print s[:i]

abcde
abcd
abc
ab
a
```
序列类型转换工厂函数
`list(iter)`: 把`iter`转换成list
`str(obj)`: 把`obj`转换成string(以字符串形式打印出来)
`tuple`: 把`iter`转换成tuple

字符串string
```python
>>> import string
>>> string.uppercase
‘ABCDEGHIJKLMNOPQRSTUVWXWZ’
>>> string.lowercase
‘abcdeghijklmnopqrstuvwxwz’
>>> string.letters
‘abcdeghijklmnopqrstuvwxwzABCDEFGHIJKLMNOPQRSTUVWXYZ’
>>> string.digits
‘0123456789’
```
ID Check(idcheck.py)
```python
#!usr/bin/env python
import string

alphas = string.letters + ‘_’
nums = string.digits

myInput = raw_input(‘Identifier to test?’)

if len(myInput) > 1:
    if myInput[0] not in alphas:
        print ‘Invalid: first symbol must be alphabetic’
    else:
        for otherChar in myInput[1:]:
            if otherChar not in alphas + nums:
                print ‘invalid : remaining symbols must be alphanumeric’
                break
        else:
            print “okey as an identifier”

```

**只适用于字符串的操作符**
字符串格式化符号
`%c`      转换成字符(ASCII码值，或长度为一的字符串)
`%r`      优先`repr()`函数进行字符串转换
`%s`      优先`str()`函数进行字符串转换
`%d / %i` 转换成有符号十进制数
`%u`      转换成无符号十进制数
`%o`      转换成无符号八进制数
`%x/%X`   (Unsigned)转成无符号十六进制数(x/X代表转换后的十六进制字符的大小写)
`%e/%E`   转成科学计数法(e/E控制输出e/E)
`%f%F`    转换成浮点数(小数部分自然截断)
`%g/%G`   %e和%f/    %E和%F的简写
`%%`      输出%
格式化操作符辅助指令
`*`       定义宽度或者小数精度
`-`       左对齐
`+`       正数前面显示(+)
`<sp>`    正数前面显示空格
`#`       在八进制数前显示(‘0’),在十六进制前显示‘0x’或‘0X’(取决用的是‘x’还是‘X’)
`0`       显示的数字前面填充‘0’
`(var)`   映射变量(字典参数)
`m.n`     m显示最小总宽度，n小数后面的位数

```
>>> “%x” % 108
‘6c’
>>> “%X” % 108
‘6C’
>>> “%#X” % 108
‘0X6C’
>>> “%#x” % 108
‘0x6c’

>>> ‘%.2f’ % 1234.567890
‘1234.57’
>>> ‘%E’ % 1234.567890
‘1.2345678E+03’
>>> ‘%e’ % 1234.567890
‘1.2345678e+03’

>>> “%+d” % 4
‘+4’
>>> “%+d” % -4
‘-4’
>>> “We are at %d%%” % 100
‘We are at 100%’
>>> ‘Host: %s\tPort: %d’ % (‘mars’, 80)
‘Host: mars    Port: 80’
>>> num = 123
>>> ‘dec: %d/oct: %#o/hex: %#X’ % (num, num, num)
‘dec: 123/oct: 0173/hex: 0X7B’

>>> ‘There are %(howmany)d %(lang)s Quotation Symbols’ % {‘lang’: ‘Python’, ‘howmany’: 3}
‘There are 3 Python Quotation Symbols’
```

字符串模板`String Templates: Simpler Substitution`
```python
>>> from string import Template
>>> s = Template(‘There are ${howmany} ${lang} Quotation Symbols’)
>>> print s.substitute(lang=’Python’, howmany=3)
There are 3 Python Qutation Symbols
>>> print s.substitute(lang=’Python’)
Traceback(most recent call last):

>>> print s.safe_substitute(lang=’Python’)
There are ${howmany} Python Quotation Symbols
```

内建函数
```python
>>> str1 = ‘abc’
>>> str2 = ‘lmn’
>>> cmp(str1, str2)
-1
>>> len(str1)
3
>>> max(str1)
c
>>> min(‘ab12cd’)
‘1’

>>> s = ‘foo’
>>> for i, t in enumerate(s)
       print i, t
0 f
1 o
2 o

>>> s, t = ‘foat’, ‘obr’
>>> zip(s, t)
[(‘f’, ‘o’), (‘o’, ‘b’), (‘a’, ‘r’)]

```

字符串内建方法: String Built-in Methods
`string.capitalize()`:    把字符串的第一个字符大写
`string.center(width)`:   返回一个原字符串居中，空格填充。
`string.count(str, beg=0, end=len(string))`: 返回str在string里面出现的次数，beg和end指定范围
`string.decode(encoding=’UTF-8’, errors=’strict’)`:   以`encoding`指定的编码风格解码string，如果出错默认报`ValueError`异常，除非`errors`指定的是‘ignore’或者‘replace’
`string.endswith(obj,beg=0,end=len(string))`:   检查字符串在beg和end范围是否以`obj`结束，obj通常是字符串，如是元组，则判断string是否在元组中
`string.expandtabs(tabsize=8)`:   把字符串string中`tab`符转换为空格，默认是8
`string.find(str, beg=0, end=len(string))`: 检测str在beg和end范围是否包含在string中，是返回索引否则返回-1
`string.rfind(str, beg=0, end=len(string))`: 类似find(),从右边开始查找
`string.index(str, beg=0, end=len(string))`: 用法同find()，但str不再string中会报异常
`string.rindex(str)`: 同index(),从右边开始
`string.isalnum()`: string至少有一个字符，且所包含字符都是字母或数字返回true
`string.isalpha()`: string至少有一字符，且所包含字符都是字母返回true
`string.isdecimal()`: string只包含十进制数返回true
`string.isdigit()`: string只包含数字返回true
`string.islower()`: string至少含有一个区分大小写字符，且所有这些字符都是小写返回true
`string.lower()`: 将string字符转化成小写
`string.isupper()`: 与`string.islowe()`相反
`string.upper()`: 将string转换成大写
`string.isnumeric()`: string中只包含数字字符返回true
`string.isspace()`: string中只包含空格返回true
`string.title()`: 返回标题化的stirng，即所有单词以大写开始，其余字母均小写
`string.istitle()`: string是标题化的返回true
`string.join(seq)`: 以string作为分隔符将`seq`中所有元素合并成新字符串
`string.ljust(width)`: 返回原字符串左对齐用空格填充至长度为`width`
`stirng.rjust(width)`: 用法同ljust()
`string.lstrip()`: 截掉string左边的空格
`string.rstrip()`: 截掉string右边的空格
`string.strip()`: 在string上执行lstrip()和rstrip()，文本末换行符也可去掉
`string.partition(str)`: 把string分成一个3元组(stringPreStr, str, stringPostStr),如果string没有str则stringPreStr = string
`string.rpartition(str)`: 用法同partition()
`string.split(str=””, num=string.count(str))`: 以str为分割符切片，如果num有值则分割num个子字符串
`string.splitlines(num=string.count(‘\n’))`: 按行分隔，num制定切片个数
`string.startswidth(obj, beg=0, end=len(string))`: 检查字母是否在beg和end范围以`obj`开头，是返回true。
`string.translate(str, del=””)`: 根据`str`给出的表转换string的字符过滤掉del中的字符
`string.zfill(width)`: 返回长度为`width`的字符串，右对齐前面填充0
```pyhton
>>> quest = ‘what is your favorite color?’
>>> quest.capitalize()
‘What is your favorite color?’
>>> quest.center(40)
‘what is your favorite color?    ’
>>> quest.count(‘or’)
2
>>> quest.endwith(‘color?’)
True
>>> find(‘or’, 20, 30)
25
>>> quest.index(‘or’, 10)
16
>>> ‘:’.join(quest.split())
‘what:is:your:favorite:color?’
>>> quest.replace(‘favorite color’,’quest’)
‘what is your quest?’
```
#### Unicode
Unicode是计算机可以支持这个星球上多种语言的秘密武器，codec是COder/DECoder首字母组合，他定义了文本跟二进制值的转换方式，跟ASCII那种用一个字节把字符转换成数字的方式不同，Unicode用的是多字节，导致Unicode支持多种不同的编码方式。比如code支持ASCII，ISO8859-1/Latin-1,UTF-8和UTF-16

