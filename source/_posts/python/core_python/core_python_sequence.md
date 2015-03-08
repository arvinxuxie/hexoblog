---
layout: post
title: core_python_sequence
date: 2014-09-10
category: python
tags: [core_python]
---
## 序列：字符串，列表，元组
切片操作(Slice[], [:], [::])
```python
>>> s = ‘abcdefgh’
>>> s[::-1]
‘hgfedcba’
>>> s[::2]
‘aceg’
```
<!--more-->
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
所有的方法都支持Unicode有一些专门用于Unicode。
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

这个简单的脚本将Unicode字符串写入到硬盘并读出它进行展示。
```python
# !/usr/bin/env python
‘’’
An example of reading and writing Unicode strings: Writes
a Unicode string to a file in utf-8 and reads it back in.
’’’
CODEC = ‘utf-8’
FILE = ‘unicode.txt’

hello_out = u”Hello world\n”
bytes_out = hello_out.encode(CODEC)
f = open(FILE, “w”)
f.write(bytes_out)
f.close()

f = open(FILE, “r”)
bytes_in = f.read()
f.close()
hello_in = bytes_in.decode(CODEC)
print hello_in,

$ python uniFile.py
Hello World
```
**只要遵循一下规则可避免90%的错误**
* 程序中出现字符串时一定要加个前缀`u`
* 不要使用`str()`函数，用`unicode()`函数代替。
* 不要用过时的`string`模块--如果传给它non-ASCII字符会崩溃(blows up)
* 避免不必要的在你的程序中编解码Unicode字符串，只在你要写入文件或数据库或网络时才调用`encode()`函数;相应地，只需把数据读回来时才调用`decode()`函数。
python标准库里大部分模块都兼容Unicode，除了`pikle`模块。

如果你建立的数据库的web应用程序读写Unicode，你必须保证一下支持Unicode
* Database server(MySQL, PostgreSQL, SQL Server, etc)
* Data adapter(MySQLdb )
* Web framework (mod_python, cgi, Zope, Plane, Django, etc)

**从现实中的教训**(Real-Life lessons Learned)
失误1： 你在有限的时间内写一个大型应用，但产品经理没有说明要支持外国语言，直到项目快要结束你没有考虑Unicode兼容，现在需要花费很大的代价去支持它。

失误2： 在源码中使用`string`模块或`str()`和`chr()`函数
结果： 通过全局变量查找替换把`str()`和`chr()`替换成`unicode()`和`unichr()`,但是这样就不能在用`pickle`模块，要用只能把所有要pickle处理的数据存成二进制形式，这样以来必须修改数据库的结构，而修改数据库结构意味着全部推到重来。

失误3: 不确定所有的辅助系统都支持Unicode
结果： 不得不为那些系统打补丁，但你或许并不知道他们的源码。到处修复Unicode的bug降低代码的可靠性，常常引入新的bug

**Unicode对python的支持**
`unicode()`: Unicode的工厂方法，同字符串操作符(u/U)的工作方式类似，接收一个string参数，返回一个Unicode字符串。
`decode()/encode()`: 内建函数接受一个字符串做参数返回该字符串对应的解码后的字符集
`Unicode类型(Unicode Type)`: Unicode字符串对象是`basestring`的子类、用`Unicode()`工厂方法或直接在字符串前面加一个u或U来创建实例。支持Unicode原始字符串，只要在你的字符串前面加一个`ur`或是`UR`就可以。
`Unicode序数(Unicode Ordinals)`: 标准的`ord()`**BIF**工作方式相同，最近升级到可支持Unicode对象了，`unichr()`**BIF**函数返回一个对应的Unicode字符(需要一个32位的值)，否则产生一个ValueError异常。
`强制类型转换(Coercion)`: 混合模式的字符串操作需要把普通字符串转换成Unicode对象。
`异常(Exceptions)`: `UnicodeError`定义在异常模块是`ValueAError`的子类。所有关于Unicode编解码的异常都要继承`UnicodeError`,详见`encode()`函数。
`标准编码`： 更加详细的完全的列表见[python document](https://docs.python.org/2/library/)

常用Unicode编码
utf-8：         变量长度为8的编码(默认编码)
utf-16：        变量长度为16的编码(大/小端)
utf-16-le：     小端UTF-16编码
utf-16-be：     大端UTF-16编码
ascii:          7-bit 7位ASCII码表
iso-8859-1：    ISO 8859-1(Latin-1)码表
unicode-escape:    (定义见Python Unicode构造函数)
raw-unicode-escape:    (定义见Python Unicode构造函数)
native:         Python用的内部格式

字符串类型相关模块

`string`：        字符串操作相关函数和工具，i.e. Template类
`re`：            正则表达式，强大的字符串匹配模块
`struct`：        字符串和二进制之间的转换
`c/StringIO`:     字符串缓冲对象，行为像文件对象
`base64`：        Base 16,32,64数据编解码
`codecs`：        解码注册和基类(Codec registry and base classes)
`crypt`：         进行单方面加密(Performs one-way encryption cipher)
`difflib`:        找出序列间的不同(Various “differs” for sequences)
`hashlib`:        多种不同安全哈希算法和信息摘要算法的API(API to many different secure hash and message digest algorithms)
`hmac`:           HMAC信息鉴权算法的python实现(Keyed-hashing for message authentication)
`md5`:            RSA的MD5信息摘要鉴权(RSA’s MD5 message digest authentication)
`rotor`:          提供多平台加解密服务(Provides multi-platform en/decryption services)
`sha`:            NIAT的安全哈希算法SHA(NIST’s secure hash algorithms SHA)
`stringprep`:     提供用于IP协议的Unicode字符串(Prepares Unicode strings for use in Internet protocols)
`textwrap`:       文本打包和填充(Text-wrapping and filling)
`unicodedata`:    Unicode数据库

#### 列表
移除List元素
```
>>> aList
[123, ‘abc’, ‘float replacer’, [‘inner’, ‘list’], (7-9j)]
# 知道确切的下标
>>> del aList[1]
>>> aList
[123, ‘float replacer’, [‘inner’, ‘list’], (7-9j)]
>>> aList.remove(123)
>>> aList
[‘float replacer’, [‘inner’, ‘list’], (7-9j)]
```
也可以用`pop()`方法移除返回特定的对象

**标准操作类型符**
当用于比较操作时，调用内建的`cmp()`函数，算法是从第一个对象开始比较知道有胜出比较结束。这种比较方法同样适用与元组
```
>>> list1 = [‘abc’, 123]
>>> list2 = [‘xyz’, 789]
>>> lsit1 < list2
True
```

**成员关系操作符**(Membership (in, not in))
可用于列表或元组，检测一个对象是否是该列表或元组的成员
```
>>> mixup_list
[4.0, [1, ‘x’], ‘beef’, (-1.9+6j)]
>>> ‘beef’ in mixup_list
True
```

**列表类型操作和列表解析式**(List Type Operators and List Comprehensions)
列表类型有属于自己的方法，列表才有的构建--列表解析式。这种方法结合了列表的[]和for循环。
```
>>> [i for i in range(8) if i %2 == 0]
[0, 2, 4, 6]
```

**内建函数**(Built-in Functions)
`cmp()`: 列表和元组等这些类型不仅包含数字和字符串，可能还包含列表、元组、字典及其他包括用户自定义的类型，在这种情况下`cmp()`函数是如何工作的呢。下面的算法元组类型也有应用：
1. 对两个列表的元素进行比较。
2. 如果比较的元素是同类型的，则比较其值，返回结果。
3. 如果两个元素不是同一类型，则检查它们是否是数字。
  * 如果是数字，执行必要的数字强制类型转换，然后比较
  * 如果有一方的元素是数字，则另一方的元素‘大’(数字是最小的)
  * 否则，通过类型名字的字母顺序进行比较。
4. 如果有一个列表首先到达末尾，则另一个长一点的列表“大”
5. 如果我们用尽了两个列表的元素而且所有的元素都是相等的，那么结果就是个平局，返回0

`max() and min()`: max()和min()函数在字符串操作里用处不太大，它们只能找出按字典排序的最大最小值，对列表和元组类型只包含数字和字符串比较有用，但对于混合结构越复杂准确性越差。

`sorted() and reversed()`: 注意字符串排序使用的是字典序而不是字母序
```
>>> s = [‘They’, ‘stamp’, ‘them’, ‘when’, “they’re”, ‘small’]
>>> for t in reversed(s):
        print,

small they’re when them stamp They

>>> sorted(s)
[‘They’, ‘small’, ‘stamp’, ‘them’, “they’re”, ‘when’]
```

`enumerate() and zip()`: 
```
>>> albums = [‘tales’, ‘robot’, ‘pyramid’]
>>> for i, album in enumerate(albums):
        print i, album
0 tales
1 robot
2 pyramid

>>> fn = [‘ian’, ‘stuart’, ‘david’]
>>> ln = [‘bairnson’, ‘elliott’, ‘paton’]
>>> for i, j in zip(fn, ln):
        print (‘%s %s’ % (i, j)).title()

Ian Bairnson Stuart Elliott David Paton
```
`sum()`:
```
>>> a = [6, 4, 5]
>>>> reduce(operate.add, a)
15
>>> sum(a)
15
>>> sum(a, 5)
20
```

`list() and tuple()`:这两个工厂函数可接受可迭代对象`iterables`(像另一个序列)作为参数，并通过浅拷贝`shallow-copied`数据来创建一个新的列表或元组。虽然字符串也是序列类型，但他们并不经常用于list()和tuple()函数。更多情况下是用于他们两种类型之间进行转换。
```
>>> aList = [‘tao’, 93, 99, ‘time’]
>>> aTuple = tuple(aList)
>>> aList, aTuple
([‘tao’, 93, 99, ‘time’], (‘tao’, 93, 99, ‘time’))
>>> aList = aTuple
False
>>> anotherList = list(aTuple)
>>> aList == anotherList
True
>>> aList is anotherList
Flase
>>> [id(x) for x in aList, aTuple, anotherList]
[139666697216080, 139666697539440, 139666697216296]
```
类表类型的内建函数
list.append(obj)       向列表添加一个对象obj
list.count(obj)        返回对象obj在列表中出现的次数
list.extend(seq)       把序列seq的内容添加到列表中
list.index(obj, i=0, j=len(list))    返回最近的下表k符合list[k]==obj并且i<=k<j，否则ValueError
list.insert(index, obj)    在索引为index位置插入`obj`
list.pop(index=-1)     删除并返回`obj`在被给予索引或默认的最后位置。
list.remove(obj)       在列表中移除`obj`对象
list.reverse()         在列表中转置对象
list.sort(func=None, key=None, reverse=False)    对列表对象进行排序

**用列表构建栈结构**

```python
This simple script uses lists as a stack to store and retrieve strings entered through this menu-driven text application using onlythe append() and pop() list methods

#!/usr/bin/env python

stack = []

def pushit():
    stack.append(raw_input(‘Enter new string:’).strip())

def popit():
    if len(stack) == 0:
        print ‘Cannot pop from an empty stack!’
    else:
        print ‘Remove[‘, stack.pop),’]’

def viewstack():
   print stack

CMDs = {‘u’:pushit, ‘o’: popit, ‘v’: viewstack}

def showmenu():
    pr = “””
    p(U)sh
    p(O)p
    (V)iew
    (Q)uit
    Enter choice:
    “””
    while True:
        while True:
            try:
                choice = raw_input(pr).strip()[0].lower()
            except (EOFError, KeyboardInterrupt, IndexError):
                choice = ‘q’

            print ‘\nYou picked: [%s]’ % choice
            if choice not in ‘uovq’:
                print ‘Invalid option, try again’
            else:
                break

        if choice == ‘q’:
            break
        CMDs[choice]()
if __name__ == ‘__main__’:
    showmenu()
```

**用列表构建队列**(queue.py)
```
This simple script uses lists as a queue to store and retrieve strings entered through this menu-driven text application, using only the append() and pop() list methods.

queue = []

def enQ():
    queue.append(raw_input(‘Enter new string:’).strip())

def deQ():
    if len(queue) == 0:
        print ‘Cannot pop from an empty queue!’
    else:
        print ‘Removed [‘,`queue.pop(0)`,’]’

def viewQ():
    print queue

CMDs = {‘e’: enQ, ‘d’: deQ, ‘v’: viewQ}

def showmenu():
    pr = “””
    (E)nqueue
    (D)equeue
    (V)iew
    (Q)uit
    ”””
    while True:
        while True:
            try:
                choice = raw_input(pr).strip()[0].lower()
            except (EOFError, KeyboardInterrupt, IndexError):
                choice = ‘q’
            print ‘\nYou picked: [%s]’ %choice
            if choice not in ‘devq’:
                print ‘Invalid option, try again’
            else:
                break

        if choice == ‘q’:
            break
        CMDs[choice]()

if __name__ == ‘__main__’:
    showmenu()
```

####元组
单元素元组，圆括号被重载了，也用作分组操作符，可以在第一个元素后面添一个逗号(,)来表明这是一个元组
```
>>> (‘xyz’,)
```
**默认集合类型**
所有的多对象的，逗号分隔，没有明确用符号定义的，这些集合默认的类型是元组。
```
>>> ‘abc’, -4.24e93, 18+6.6j, ‘xyz’
(‘abc’, -4.24e+093, (18+6.6j), ‘xyz’)
```

**相关模块**
下面列出了与序列相关的关键模块，
`array`：    特点是限制序列的类型，需要所有的元素都是统一类型
`copy`：     提供对象的浅拷贝和深拷贝`shallow and deep copies of objects`
`operator`:   包含函数调用形式的序列操作符，如operator.concat(m, n)相当于连接操作(m+n)
`re`:       Perl风格的正则表达式
`StringIO/cStringIO`:    把长字符串作为文件操作，i.e. `read(), seek()`;C版的更快一些，但是他不能被继承
`textwrap`:    用做包裹/填充文本的函数`Utility functions for wrapping/filling text fields; also has a class`
`types`:    包含python支持的所有类型
`collections`:    高性能容器数据类型(High-performance container data types)

####拷贝python对象的浅拷贝和深拷贝(Shallow and Deep Copies)
对象赋值实际是简单的对象的引用，当创建一个对象时，把它赋值给另一个变量，python并没有拷贝这个对象，而是拷贝了这个对象的引用。

例如、创建一对夫妻通用档案，名为`person`，然后分别为他们俩拷贝一份。一种利用切片操作，另一种利用工厂方法，为了区别三个不同对象可以使用`id()`显示每个对象的标志符。
```
>>> person = ['name', ['savings', 100.00]]
>>> hubby = person[:]    #slice copy
>>> wifey = list(person)     # fac func copy
>>> [id(x) for x in person, hubby, wifey]
[140095888490224, 140095888551880, 140095888605624]
```
为他们创建初始有$100的个人存款账户，用户名改为定制的名字，但当丈夫取走$50后，影响到妻子的账户。
```
>>> hubby[0] = 'jeo'
>>> wifey[0] = 'jane'
>>> hubby, wifey
(['jeo', ['savings', 100.0]], ['jane', ['savings', 100.0]])
>>> hubby[1][1] = 50.00
>>> hubby, wifey
(['jeo', ['savings', 50.0]], ['jane', ['savings', 50.0]])
```
为什么会这样，原因是我们仅仅做了一个浅拷贝。对一个对象进行浅拷贝其实是新创了一个类型跟原对象一样，其内容是原来对象元素的引用，及这个拷贝的对象本身是新的，但它的内容不是。序列类型对象的浅拷贝是默认类型拷贝，并可以有一下几种方法：
(1)完全切片操作[:]
(2)利用工厂函数，比如`list(), dict()`等
(3)使用copy模块的copy函数
注意当妻子的名字被赋值，并没有影响到丈夫的名字，因为第一个对象是不可变的，而第二个对象列表是可变的。我们在该他们的名字的时候新名字的对象的引用覆盖了原来的引用，而列表指向的对象改变一处便都改变了。
```
>>> person = ['name', ['savings', 100.00]]
>>> hubby = person[:]
>>> wifey = list(person)
>>> [id(x) for x in hubby]
[140169874652784, 140169873696672]
>>> [id(x) for x in wifey]
[140169874652784, 140169873696672]
>>> [id(x) for x in person]
[140169874652784, 140169873696672]
>>> hubby[0] = 'jeo'
>>> wifey[0] = 'jane'
>>> hubby[1][1] = 50.00
>>> hubby, wifey
(['jeo', ['savings', 50.0]], ['jane', ['savings', 50.0]])
>>> [id(x) for x in hubby]
[140169873722784, 140169873696672]
>>> [id(x) for x in wifey]
[140169873734800, 140169873696672]
```
我们要使这对夫妻分离账户，我们需要用深拷贝创建一个新的容器对象，需要用`copy.deepcopy()`函数。
```
>>> person = ['name', ['savings', 100.00]]
>>> hubby = person
>>> import copy
>>> wifey = copy.deepcopy(person)
>>> [id(x) for x in person, hubby, wifey]
[140169873752360, 140169873752360, 140169873752504]
>>> hubby[0] = 'joe'
>>> wifey[0] = 'jane'
>>> hubby, wifey
(['joe', ['savings', 100.0]], ['jane', ['savings', 100.0]])
>>> hubby[1][1] = 50.00
>>> hubby, wifey
(['joe', ['savings', 50.0]], ['jane', ['savings', 100.0]])
>>> [id(x) for x in hubby]
[140169873797848, 140169873698688]
>>> [id(x) for x in wifey]
[140169873734848, 140169873786136]
```
有一些关于对象拷贝的警告，
第一，非容器类型(比如数字，字符串，其他原子型对象`atomic objects`像代码`code`、类型`type`及`xrange`对象)不存在拷贝。
第二，浅拷贝是利用完全拷贝完成的
最后，如果元组只包含原子类型的对象，则对它进行深拷贝是不成功的，只能得到一个浅拷贝。

### 映射和集合类型(Mapping and Set Types)
哈希表是一种数据结构，哈希表中存储的每一条数据，叫做值(value),根据与它相关的一个被乘坐键(key)的数据项进行存储的。键和值在一起称为‘键-值对’(key-value pairs)。
方法keys()或values()返回一个列表，该列表是可排序的。还可以用items()的方法得到包含键、值对的元组列表来排序。

可以用`dict()`来创建字典
```
>>> fdict = dict(([‘x’, 1], [‘y’,2]))
>>> fdict
{‘y’: 2, ‘x’: 1}
```
用`fromkeys()`来创建一个默认字典，字典元素具有相同的值，没给出则为`None`
```
>>> ddict = {}.fromkeys((‘x’, ‘y’), -1)
>>> ddict
{‘y’: -1, ‘x’: -1}
>>> edict = {}.fromkeys((‘foo’, ‘bar’))
>>> edict
{‘foo’: None, ‘bar’: None}
```
用用迭代器访问类序列对象(sequeue-like objects), 比如字典和文件。
```
>>> dict2 = {‘name’: ‘earth’, ‘port’: 80}
>>> for key in dict2:
        print ‘key=%s, value=%s’ %(key, dict2[key])

key=name, value=earth
key=port, value=80
>>> ‘server’ in dict2    #dict2.has_key(‘server’)
False
>>> ‘name’ in dict2    # dict2.has_key(‘name’)
True
>>> del dict2[‘name’]    #删除键为name的条目
>>> dict2.clear()   #删除dict2中的所有条目
>>> del dict2    #删除整个dict2字典
>>> dict2.pop(‘name’)     #删除并返回键值为‘name’条目
```

####映射类型的内建函数和工厂函数
标准类型函数[type(), str(), cmp()]
![字典的比较算法]({{BASE_PATH}}/image/core_python/img1.jpg)

**映射类型相关函数**
`dict()`: 工厂函数用来创建字典。
```
>>> dict(zip((‘x’, ‘y’), (1, 2)))
{‘y’: 2, ‘x’: 1}
>>> dict([[‘x’, 1], [‘y’, 2]])
{‘y’: 2, ‘x’: 1}
>>> dict([(‘xy’[i-1], i) for i in range(1,3)])
{‘y’: 2, ‘x’: 1}

>>> dict1(x=1, y=2)
{‘y’:2, ‘x’:1}
>>> dict8 = dict1
>>> dict8
{‘y’:2, ‘x’:1}
>>> dict9 =dict(**dict8)    #不做实际用途
>>> dict9
{‘y’:2, ‘x’:1}
>>> dict9 = dict8.copy()    #常用方法效果更好
{‘y’:2, ‘x’:1}
```
`len()`:内建函数，返回键值对数目
`hash(obj)`: 用于判断某个对象是否可以做一个字典的键。会返回这个对象的哈希值。

**字典类型方法**
`dict.clear()`:     删除字典中所有的元素
`dict.copy()`:      返回字典(浅拷贝)的一个副本
`dict.fromkeys(seq, val=None)`:    创建并返回一个新字典
`dict.get(key, default=None)`:   对字典dict中的键key，返回他对应的value，如果字典中不存在返回默认值
`dict.hash_key(key)`： 判断键值是否在字典中，引入`in`和`not in`废弃
`dict.items()`:    返回字典中(键， 值)对元组列表
`dict.keys()`:    返回一个包含字典键的列表
`dict.iter()`:    方法`iteritems(), iterkeys(), itervalues()`与它们对应的非迭代方法一样，不同的是他们返回一个迭代子，而不是一个列表。
`dict.pop(key[,default])`: 和方法get()相似，如果字典key存在，删除并返回dict[key],如果key键不存在，且没有default的值，引发KeyError异常。
`dict.setdefault(key, default=None)`:   和set()相似，如果字典不存在key键，由dict[key]=default为它赋值。
`dict.update(dict2)`:   将字典dict2的键值对添加到字典dict
`dict.values()`:    返回一个包含字典中所有的值的列表
```
>>> dict2= {'host': 'earth', 'port':80}
>>> dict3= {'host': 'venus', 'server':'http'}
>>> dict2.update(dict3)
>>> dict2
{'host': 'venus', 'port': 80, 'server': 'http'}
>>> dict4 = dict2.copy()
{‘server’: ‘http’, ‘port’: 80, ‘host’: ‘venus’}
>>> dict4.get(‘host’)
‘venus’
>>> myDict={‘host’: ‘earth’, ‘poer’: 80}
>>> myDict.keys()
[‘host’, ‘port’]
>>> myDict.items()
[(‘host’, ‘earth’), (‘port’, 80)]
>>> myDict.setdefault(‘port’, ‘tcp’)
>>> {}.fromkeys(‘xyz’)
{‘y’:None, ‘x’: None, ‘z’: None}
>>> {}.fromkeys((‘love’, ‘honor’), True)
{‘love’:True, ‘honor’: True}
```
目前`keys()`,`items()`,`values()`方法返回的都是列表。数据集很大会导致很难处理，所以添加`iteritems()`,`iterkeys()`和`itervalues()`。

不允许一个键对应多个值。Python并不会因字典中的键存在冲突而产生错误。
```
>>> dict1 = {‘foo’: 789, ‘foo’: ‘xyz’}
>>> dict1
{‘foo’: ‘xyz’}
>>> dict1[‘foo’] = 123
>>> dict1
{‘foo’: 123}
```
**Dictionary Example**(userpw.py)
```
This application manages a set of users who join the system with a login name and a password. Once established. exiting users can return as long as they remember their login and password. New user cannot create an entry with someone else’s login name
#!/usr/bin/env python

db = {}

def newuser():
    prompt = ‘login desired:  ’
    while True:
        name = raw_input(prompt)
        if db.has_key(name):
            prompt = ‘name taken, try another:  ’
            continue
        else:
            break
    pwd = raw_input(‘password:   ’)
    db[name] = pwd

def olduser():
    name = raw_input(‘login:  ’)
    pwd = raw_input(‘passwd:   ’)
    if passwd == pwd:
        print ‘welcome back’, name
    else:
        print ‘login incorrect’

def showmenu():
    prompt = “””
    (N)ew User Login
    (E)xisting User Login
    (Q)uit
    Enter choice:
    ”””
    done = False
    while not done:
        chosen = False
        while not chosen:
            try:
                choice = raw_input(prompt).strip()[0].lower()
            except(EOFError, KeyboardInterrupt):
                 choice = ‘q’
            print ‘\nYou picked: [%s]’ % choice
            if choice not in ‘neq’:
                print ‘invalid option, try again’
            else:
                chosen = True
        if choice == ‘q’: done = True
        if choice == ‘n’: newuser()
        if choice == ‘e’: olduser()

if __name__ == ‘__main__’:
    showmenu()
```
#### 集合类型(Set Types)
![集合操作符和关系符号]({{BASE_PATH}}/image/core_python/img2.jpg)

集合类工厂函数
`set()`和`frozenset()`工厂函数分别用来生成可变和不可变的集合。
```
>>> frozenset(['foo', 'bar'])
frozenset(['foo', 'bar'])

>>> f = open('numbers', 'w')
>>> for i in range(5):
...     f.write('%d\n' %i)
>>> f.close()
>>> f = open('numbers', 'r')
>>> set(f)
set(['0\n', '3\n', '1\n', '4\n', '2\n'])
```
**集合类方法**
`len(s)`:    集合的基数
`set([obj])`:   可变集合的工厂函数
`frozenset([obj])`:    不可变的集合的工厂函数
`obj in s`:
`obj not in s`
`s == t`
`s != t`
`s < t`
`s.issubset(t)   s <= t`:    如果s是t的子集返回true，否则false
`s.issuperset(t) s >= t`:    如果s是t的子集返回true，否则false，及t是s的子集
`s.union(t)  s | t`:    返回一个s和t的并集
`s.intersection(t)  s & t`:    返回s和t的交集
`s.difference(t)`  s - t:    返回的差集
`s.symmetric_difference(t)   s ^ t`:    返回一个s和t并差集
`s.copy()`:    返回s的浅拷贝

**可变集合类型的方法**
`s.update(t)   s |= t`:    用t中的元素修改s,即s现在的包含s或t的成员
`s.intersectioni_update(t)   s &= t`:    s中的成员是共同属于s和t的元素
`s.difference_update(t)   s -= t`:     s中的成员是属于s但不包含在t中的元素
`s.symmetric_difference_update(t)  s^=t`:    s中的成员更新为那些包含在s或t中，但不是s和t共有的元素
`s.add(obj)`:    在集合中添加obj对象
`s.remove(obj)`:    从集合s中删除对象obj;如果obj不是s中的元素引发KeyError错误
`s.discard(obj)`:    如果obj是集合s中的元素，从集合s中删除对象obj;
`s.clear()`:    删除集合s中的所有元素

## 条件表达式和循环(Conditionals and Loops)
**`switch/case`的代替者**(Proxy for switch/case Statement)
```python
#可以用elif语句
if user.cmd == ‘create’:
    action = “create item”
elif user.cmd == ‘delete’:
    action = ‘delete item’
elif user.cmd == ‘update’:
    action = ‘update item’
else:
    action = ‘invalid choice ...try again!’

#还可以用简单的方法实现
if user.cmd in (‘create’, ‘delete’, ‘update’):
    action = ‘%s item’ % user.cmd
else:
    action = ‘invalid choice...try again!’

#我们还可以用更优雅的方法实现，利用字典映射比elif更有效率
msgs = {‘create’: ‘create item’,
        ‘delete’: ‘delete item’,
        ‘update’: ‘update item’}
default = ‘invalid choice...try again!’
action = msgs.get(user.cmd, default)
```
**条件表达式之三元运算符**(the Ternary Operator)
```
#用if-else实现
>>> x, y = 4, 3
>>> if x < y:
         smaller = x
    else:
        smaller = y

#2.5以前的版本中Python程序员(hack)
>>> smaller = (x < y and [x] or [y])[0]
>>> smaller
3
#更新后的版本
>>> smaller = x if x < y else y
>>> smaller
3
```
**for语句**

用于序列类型有三种迭代方式
`通过序列迭代`(Iterating by Sequence Item)
```
>>> nameList = [‘Walter’, ‘Nicole’, ‘Steven’, ‘Henry’]
>>> for eachName in nameList:
        print eachname, “Lim”
```
`通过序列索引迭代`(Iterating by Sequence Index)
```
>>> nameList = [‘Cathy’, ‘Terry’, ‘Joe’, ‘Heather’, ‘Lucy’]
>>> for nameList in range(len(nameList)):
        print “Liu, ”, nameList[nameIndex]
```
`使用项和索引迭代`(Iterating with Item and Index)
```
>>> nameList = [‘Donn’, ‘Shirley’, ‘Ben’, ‘Janice’, ‘David’]
>>> for i, eachLee in enumerate(nameList):
        print “%d %s  Lee” %(i+1, eachLee)
```
**xrange()内建函数**
`xrange()`和`range()`相似，不过当你有一个很大的范围列表时`xrange()`更合适，它不会在内存里创建列表的完整拷贝，它只能被用于`for`循环语句中，它的性能远高于`range()`,xrange()返回一个可迭代对象

**与序列相关的内建函数**
```
>>> albums = ('Poe', 'Gaudi', 'Freud', 'Poe2')
>>> years = (1976, 1987, 1990, 2003)
>>> for album in sorted(albums):
...     print album,
Freud Gaudi Poe Poe2
>>> for album in reversed(albums):
...     print album,
Poe2 Freud Gaudi Poe
>>> for album, yr in zip(albums, years):
...     print yr, album
1976 Poe
1987 Gaudi
1990 Freud
2003 Poe2
```
**else语法**：可以用于`while`和`for`中，`else`只会在循环完成后执行，break也会跳过else块
```python
This program displays the largest factors for numbers between 10 and 20. if the number is prime, the script will indicate that as well.
#!/usr/bin/env python
def showMaxFactor(num):
    count = num / 2
    while count > 1:
        if num % count == 0:
            print ‘largest factor of %d is %d’ % (num, count)
            break
        count -= 1
    else:
        print num, ‘is prime’

for eachNum in range(10, 21):
    showMaxFactor(eachNum)
```
**迭代器和iter()函数**
为什么要迭代器
* 提供了可扩展的迭代器接口
* 对列表迭代带来了性能上的增强
* 在字典迭代中性能提升
* 创建真正的迭代接口，而不是原来的随机对象访问
* 与所有已经存在的用户定义的类以及扩展的模拟序列和映射的对象向后兼容
* 迭代非序列集合(例如映射和文件)时,可以创建更简洁可读的代码
使用迭代器
`for`循环会自动调用迭代器的`next()`方法(以及监视StopIteration异常)
```
#序列
>>> myTuple = (123, 'xyz', )
>>> i = iter(myTuple)
>>> i.next()
123
>>> i.next()
'xyz'
>>> i.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
python还引进了三个新的内建字典方法来定义迭代：`myDict.iterkeys()`(通过keys迭代),`myDict.itervalues()`(通过values迭代),`myDict.iteritems()`(通过key/value对迭代)。
```
#字典迭代器会遍历它的键(keys).
>>>legends = {(‘Poe’, ‘authorl’):(1809, 1849,1976),
(‘Gudi’,’architect’):(1852,1906,1987),
(‘Freud’,’psychoanalyst’):(1856,1939,1990)
}
>>> for eachLegend in legends:
        print ‘Name: %s\t Occupation: %s’ % eachLegend
        print ‘Birth: %s\t Death: %s\t Album: %s\n’ % legends[eachLegend]
```
文件对象生成的迭代器会自动调用`readline()`方法。这样，循环就可以访问文件的所有行。
```
>>> myFile = open(‘config-win.txt’)
>>> for eachLine in myFile:
        print eachLine,
>>> myFile.close()
```
**可变对象和迭代器**
记住干涉一个可变得对象不是一个好想法，有一个问题在迭代器出现之前，其中一个例子是循环列表移除不适合的元素。
```python
for eachURL in allURLs:
    if not eachURL.startwith(‘http://’):
        allURLs.remove(eachURL)
```
虽然列表可以成功，但其他序列是不可变的，这样很容易出现错误.一个序列的迭代器只是记录你当前到达第多少个元素，所以如果你在迭代时改变了元素，更新会立刻反映到你所迭代的条目上。在迭代字典的key时，你决不能改变这个字典，不可变对象改变会创建新的对象。使用`keys()`方法是可以的,因为`keys()`返回一个独立于字典的列表，而迭代器与实际对象绑定在一起的，原对象发生变化它将不会执行下去。
```
>>> myDict = {'a':1, 'b':2, 'c':3, 'd':4}
>>> for eachKey in myDict:
...     print eachKey, myDict[eachKey]
...     if eachKey == 'b':
...          del myDict[eachKey]
a 1
c 3
b 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
RuntimeError: dictionary changed size during iteration
>>> myDict
{'a': 1, 'c': 3, 'd': 4}
>>> myDict.keys()
['a', 'c', 'd']
>>> for eachKey in myDict.keys():
...     print eachKey, myDict[eachKey]
...     if eachKey == 'd':
...         del myDict[eachKey]
a 1
c 3
d 4
>>> myDict
{'a': 1, 'c': 3}
```
**列表解析式**(List Comprehensions)
List comprehensions(简称‘list comps’)来自函数式编程语言(functional programming language)Haskell。它非常有用简单灵活动态创建列表。列表解析是的表达式可以取代内建的`map()`函数以及`lamba`,而且效率更高。结合if语句，可提供扩展版本的语法
[expr for iter_var in iterable if cond_expr]
这个语法在迭代时会过滤/捕获满足条件的表达式cond_expr的序列成员
实践odd()函数，我们可以使用`filter()`和`lambda`挑选出序列中的奇数，也可使用列表解析来完成操作
```
>>> seq = [11, 10, 9, 9, 10, 10, 9, 8, 23, 9, 7, 18, 12, 11, 12]
>>> filter(lambda x: x % 2, seq)
[11, 9, 9, 9, 23, 9, 7, 11]
>>> [x for x in seq if x % 2]
[11, 9, 9, 9, 23, 9, 7, 11]
#矩阵样例
>>> [(x+1, y+1) for x in range(3) for y in range(5)]
[(1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (2, 1), (2, 2), (2, 3), (2, 4), (2, 5), (3, 1), (3, 2), (3, 3), (3, 4), (3, 5)]
#计算文本中的单词个数
>>> f = open('hhga.txt', 'r')
>>> len([word for line in f for word in line.split()])
92
#快速计算文件的大小
>>> import os
>>> os.stat('hhga.txt').st_size
501
#用seek()函数回到文件开头，因为迭代器已经访问完了文件的所有行。
>>> f.seek(0)
>>> sum([len(word) for line in f for word in line.split()])
409
```

**生成器表达式**(Generator Expressions)
列表解析的一个不足就是必要生成所有的数据，用以创建整个列表。这可能对大量数据的迭代器有负面效应。生成器表达式与列表解析式非常相似，基本语法相同，但它并不真正创建数字列表，而是返回一个生成器，这个生成器在每次计算出一个条目后，把这个条目产生’yeild’出来。生成器表达式使用了“延迟计算(lazy evaluation)”,所以在使用内存上更有效。
列表解析式：[expr for iter_var in iterable if cond_expr]
生成器表达式: (expr for iter_var in iterable if cond_expr)
生成器并不会让列表解析废弃，它只是一个内存使用更有好的结构。
**磁盘文件样例**
前面我们计算文本文件中的非空白字符总和，最后的代码中使用`sum([len(word) for line in data for word in line.split()])`列表解析代码。如果这个文件的大小很大，这行代码的性能会很低，因为我们要创建很长的列表用于存放单词的长度。为了避免创建庞大的列表，我们可以使用生成器表达式来完成求和操作。它会计算每个单词的长度让后传递给`sum()`函数(它的参数不仅可以是列表，还可以是可迭代的对象，比如生成器表达式)，这样我们可以得到优化后的代码`sum(len(word) for line in data for word in line.split())`

**交叉配对例子**
```
>>> rows = [1, 2, 3, 17]
>>> def cols():
...     yield 56
...     yield 2
...     yield 1
>>> x_product_pairs = ((i, j) for i in rows for j in cols())
>>> for pair in x_product_pairs:
...     print pair
(1, 56)
(1, 2)
(1, 1)
(2, 56)
(2, 2)
(2, 1)
(3, 56)
(3, 2)
(3, 1)
(17, 56)
(17, 2)
(17, 1)
```
**重构样例**
我们通过一个寻找文件最长的行的例子来看看如何改进代码。
```
f = open(‘motd.txt’, ‘r’)
longest = 0
while True:
    linelen = len(f.readline().strip())
    if not linelen:
        break
    if linelen > longest:
        longest = linelen
f.close()
return longest

#从那时起我们认识到如果读取了所有的行，应该尽早释放文件资源，如果是一个很多进程都要用的日志文件，我们不能一直拿着它的句柄不放。
f = open(‘motd.txt’, ‘r’)
longest = 0
allLines = f.readlines()
f.close()
for line in allLines:
    linelen= len(line.strip())
    if linelen > longest:
        longest = linelen
return longest

#列表解析允许我们稍稍简化一下我们的代码。
f = open(‘motd.txt’, ‘r’)
longest = 0
allLines = [x.strip() for x in f.readlines()]
f.close()
for line in allLines:
    linelen = len(line)
    if linelen > longest:
        longest = linelen
return longest

#然而两个例子在处理大文件的时候都有问题，因为`readlines()`会读取文件的所有行。后来我们有了迭代器，不需要调用`readlines()`函数。
f = open(‘motd.txt’, ‘r’)
allLineLens = [len(x.strip()) for x in f]
f.close()
return max(allLineLens)

#这里唯一的问题是一行一行迭代f的时候，列表解析需要文件的所有行读取到内存中，然后生成列表。我们可以进一步简化代码：使用生成器表达式替换列表解析。
f = open(‘motd.txt’, ‘r’)
longest = max(len(x.strip()) for x in f)
f.close()
return longest

#最后我们可以去掉文件打开模式(默认是读取)，然后让python去处理打开的文件，但文件用于写入的时候不能这么做。
return max(len(x.strip()) for x in open(‘motd.txt’))
```
