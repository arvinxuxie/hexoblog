---
layout: post
title: core_python_file
category: python
tags: [python]
mathjax: true
---

# 文件
**文件内建函数**(open()和file())
文件对象的访问模式
`r`     只读方式打开
`rU/ Ua`    以读方式打开，同时提供换行符的支持
`w`     以写方式打开
`a`     以追加方式打开
`r+`     以读写方式打开
`w+`     以读写方式打开
`a+`     以读写方式打开
`rb`     以二进制读方式打开
`wb`     以二进制写方式打开
`ab`     以二进制追加方式打开
`rb+`     以二进制读方式打开
`wb+`     以二进制写方式打开
`ab+`     以二进制追加方式打开

`open()`和`file()`函数功能相同，可以任意替换，一般建议使用`open()`来读写文件，在你想说明在处理文件对象时使用`file()`,例如`if instance(f, file)`

通用换行符支持(UNS):不同平台下行结束符是不一样的，当你使用‘U’标志打开文件的时候，所有行分割符通过Python的输入方法(例如`read*()`)返回时都被替换成NEWLINE字符(\n)。如果文件刚被打开，程序还没遇到行结束符，那么文件的`newlines`为`None`。
在编译Python的时候UNS默认是打开的，可以使用`--without-universal-newlines`开关关闭它，自己处理行结束符。

**输入**
`read()`方法用来直接读取字节到字符串中，最多读取给定数目个字节。如果没有给定size参数(默认值是-1)，文件将被读取到末尾。
`readline()`方法读取打开文件的一行(读取下一个行结束符之前的所有字节)。和`read()`相同，也有一个可选的size参数，默认是-1。
`readlines()`方法会读取所有的(剩余的)行然后把他们作为一个字符串列表返回。可选参数`sizhint`代表返回自大字节大小。如果它大于0,返回的所有行应该大约有`sizhint`字节(可能稍微大于这个数字，因为需要凑齐缓冲区大小)

**输出**
`write()`方法与`read()`和`readline()`相反。他把含有文本数据或二进制数据块的字符串写入到文件中去。
`writelines()`接受一个字符串列表作为参数，将他们写入文件，行结束符不会自动加上。
注意没有writeline()方法，因为它等价于使用行字符串调用`write()`方法。

**文件内移动**
`seek()`方法可以在文件中移动文件指针指到不同的位置。`offset`字节代表相对于某个位置偏移量。位置默认是0,代表从文件开头算起(及绝对偏移量)，1代表从当前位置算起，2代表从文件末尾算起。
`text()`方法对`seek()`的补充，它告诉你当前文件指针在文件中的位置-从文件开始算起，单位为字节。

**文件迭代**(File Iteration)
`for eacLien in f:`:文件迭代更为高效。

**其它**
`close()`: 通过关闭文件来结束对他的访问，Python垃圾收集机制也会在文件对象的引用计数降至零的时候自动关闭文件。这在文件只有一个引用时发生，良好的编程习惯要求在重新赋另一个文件对象前关闭这个文件。如果你不显示的关闭文件，那么可能丢失输出缓冲区的数据。
`fileno()`: 返回打开文件的描述符，这是一个整数，可以用在os模块(os.read())的一些底层操作上。
`flush()`: 会直接把内部缓冲区中的数据立刻写入文件。
`isatty()`: 当文件是一个类tty(tty-like)设备返回true，否则返回false
`truncate()`: 将文件截取到当前文件指针位置或者到给定size，以字节为单位
`tell()`:  返回当前文件指针位置

**有助于跨平台开发的os模块属性**
`linesep`:    用于文件中分隔行的字符串
`sep`：       用来分隔文件路径名的字符串
`pathseq`:    用于分隔文件路径的字符串
`curdir`:     当前工作目录的字符串名称
`pardir`:     (当前工作目录的)父目录字符串名称
不管使用什么平台，只要导入os模块，这些变量会自动设置为正确的指。
这是linux下的输入
```
In [29]: import os
In [30]: os.linesep
Out[30]: '\n'
In [31]: os.sep
Out[31]: '/'
In [32]: os.pathsep
Out[32]: ':'
In [33]: os.curdir
Out[33]: '.'
In [34]: os.pardir
Out[34]: '..'
```

**实例**
```
filename = raw_input(‘Enter file name:’)
fobj = open(filename, ‘w’)
while True:
    aLine = raw_input(“Enter a line(‘.’) to quit: ”)
    if aLine != ‘.’:
        fobj.write(“%s%s” % (aLine, os.linesep))
    else:
        break
fobj.close()

#使用seek()方法在文件内部移动，使用tell()方法展示我们移动的过程
In [2]: f = open('test.txt', 'w+')
In [3]: f.tell()
Out[3]: 0
In [4]: f.write
In [4]: f.write('test line 1\n')
In [5]: f.tell()
Out[5]: 12
In [6]: f.write('test line 2\n')
In [7]: f.tell()
Out[7]: 24
In [8]: f.seek(-12, 1)
In [9]: f.tell()
Out[9]: 12
In [10]: f.readline()
Out[10]: 'test line 2\n'
In [11]: f.seek(0, 0)
In [12]: f.readline()
Out[12]: 'test line 1\n'
In [13]: f.tell()
Out[13]: 12
In [14]: f.readline()
Out[14]: 'test line 2\n'
In [15]: f.tell()
Out[15]: 24
In [16]: f.close()
```

**文件内建属性**
`file.closed` :  True表示文件已经关闭，否则为false
`file.encoding`:   文件所使用的编码-当Unicode字符串被写入数据时，它们自动使file.encoding转化为字节字符串;若file.encoding为None时使用系统默认编码。
`file.mode`    文件打开时使用的访问模式
`file.newlines`    未读取到行分隔符时为None，只有一种行分隔符时作为一个字符串，当文件有多种类型的行结束符时，则作为一个包含所有当前所遇到的行结束符的列表
`file.softspace`     为0表示在输出一数据后，要加上一个空格符，1表示不加。这个属一般程序员用不着，由程序内部使用。

**标准文件**
一般来说当程序一启动这三个标准文件便可以被访问到，这三个标准文件是标准输入`standard input`(通常是键盘)，标准输出`standard output`(到屏幕的缓冲输出buffered output to the monitor or display)，标准错误`standard error`(到屏幕的非缓冲区输出unbuffered output to the screen)。这里的“缓冲区”和“非缓冲区”(buffered and unbuffered)是指open()函数的第三个参数。这些文件沿用C语言中的命名规则，
