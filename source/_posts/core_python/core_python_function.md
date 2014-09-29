---
layout: post
title: functions and functional programming
category: python
tags: python
---
## 函数
函数会向调用者返回一个值，而实际编程中函数更接近过程，不显示的返回任何东西。把过程看待成函数的语言通常对于“什么都不返回”的函数设定了特殊的类型或者值的名字。python中，对应的返回对象类型是none
```
In [23]: def hello():
   ....:     print 'hello world'
In [24]: res = hello()
hello world
In [25]: print res
None
```
许多静态类型的语言主张一个函数的类型就是其返回值的类型。python中，由于是动态的确定类型而且函数能返回不同类型的值，所以没有进行直接的类型关联。因为重载并不是语言特性，程序员需要使用`type()`函数作为代理，来处理有着不同参数类型的函数的多重声明以模拟类C语言的函数重载。(以参数的不同选择函数的多个原型)

## 调用函数(Calling Functions)
### 关键字参数(keyword arguments)
关键字参数的概念仅仅针对函数的调用，这种理念是让调用者通过函数调用中的参数名字来区分参数，这样规范允许参数缺失或者不按顺序，因为解释器能通过关键字来匹配参数的值。
标准调用 foo(): foo(42) foo(‘bar’) foo(y)
关键字调用 foo(): foo(x=42) foo(x=’bar’)foo(x=y)

**参数组**(Grouped Arguments)
python同样允许程序员执行一个没有显式定义参数的函数，相应的方法是通过一个把元组(非关键字参数)或字典(非关键字参数)作为参数组传递给函数。你可以将所有的参数放进一个元组或字典中，仅仅用这些装有参数的容器来调用一个函数，而不必显式地将它们放在函数调用中：

    func(*tuple_grp_nonkw_args, **dict_grp_kw_args)
实际上，你也可以给出形参`formal arguments`。这些参数包括标准的位置参数和关键字参数，所以python中允许的函数调用的完整语法为：

    func(positional_args, keyword_args, *tuple_grp_nonky_args, **dict_grp_kw_args)
该语法中所有的参数都是可选的---从参数传递到函数的过程来看，在单独的函数调用时，每个参数都是独立的。这可以有效的取代apply()内建函数。
```python
Randomly chooses numbers and an arithmetic function, displays the question and verifies the results. shows answer after three wrong tries and does not continue until the user enter the correct answer.

#!/usr/bin/env python
from operator import add, sub
from random import randint, choice

ops = {‘+’:add, ‘-’: sub}
MAXTRIES = 2

def doprob():
    op = choice(‘+-’)
    nums = [randint(1, 10) for i in range(2)]
    nums.sort(reverse=True)
    ans = ops[op](*num)
    pr = ‘%d %s %d = ’ % (nums[0], op, nums[1])
    oops = 0
    while True:
        try:
            if int(raw_input(pr)) == ans:
                print ‘correct’
                break
            if oops == MAXTRIES:
                print ‘answer\n %s%d’ % (pr, ans)
            else:
                print ‘incorrect ... try again’
                oops += 1
        except (KeyboardInterrupt, EOFError, ValueError):
            print ‘invalid input... try again’

def main():
    while True:
        doprob()
        try:
            opt = raw_input(‘Again? [y]’).lower()
            if opt and opt[0] == ‘n’:
                break
            except (KeyboardInterrupt, EOFError):
                break

if __name__ == ‘__main__’:
    main()
```
## 创建函数`Creating Functions`
### 前向引用(Forward References)
和其他高级语言类似，Python不允许在函数未声明之前，对其进行引用或者调用。
我们可以在函数bar()前定义函数foo():
```
In [26]: def foo():
   ....:     print 'in foo()'
   ....:     bar()
In [27]: def bar():
   ....:     print 'in bar()'
In [28]: foo()
in foo()
in bar()
```
这段代码正确因为foo()对bar()进行的调用出现在bar()的定义之前，但foo()本身不是在bar()声明之前被调用的。

### 函数的属性(Function Attributes)
你可以获得每个python模块，类，和函数中任意的名字空间。函数的属性是python另外一个使用了句点属性标识并拥有名字空间的领域。
```
In [35]: def foo():
   ....:     'foo() -- properly created doc string'
In [36]: def bar():
   ....:     pass
In [37]: bar.__doc__ = 'Oops, forgot the doc str above'
In [38]: bar.version = 0.1
In [39]: foo.__doc__
Out[39]: 'foo() -- properly created doc string'
In [40]: bar.version = 0.1
```
我们可以在函数运行时刻访问它，然而你不能在函数的声明中访问属性。及在函数声明中没有`self`这样的东西让你可以进行诸如`__dict__[‘version’]=0.1`的赋值。这是因为函数体还没有被创建，但之后有了函数对象，就可以用这样的方法访问它的字典。(another free namespace)

**内部/内嵌函数**(Inner or Nested Functions)
```python
def foo():
    def bar():
        print ‘bar() called’
    print ‘foo() called’
    bar()

>>> foo()
>>> bar()
foo() called
bar() called
Traceback (most recent call last):
File "inner.py", line 11, in ?
bar()
NameError: name 'bar' is not defined
```

**函数(与方法)装饰器**`*Function(and Method) Decorators`
装饰器背后的主要动机源自python面向对象的编程。装饰器是在函数调用之上的修饰，这些修饰仅是当声明一个函数或者方法的时候，才会应用的额外调用。

装饰器的语法以@开头，接着是装饰器函数的名字和可选参数。装饰器的产生是什么，使用装饰器可以替换调用`staticmethod()`函数转化为静态方法。
```
class MyClass(object):
    def staticFoo():
        pass
    staticFoo = staticmethod(staticFoo)
#替换后
class MyClass(object):
    @staticmethod
    def staticFoo():
```
此外，装饰器可以如函数调用一样堆叠`stacked`。
```
@deco2
@deco1
def func(arg1, arg2, ...):
    pass
```
这和创建一个组合函数是等价的。
```
def func(arg1, arg2, ...):
    pass
func = deco2(deco1(func))
```
函数组合数学来定义就像这样：(g.f)(x)=g(f(x))。

**有参数和无参数的装饰器**
```
@decomaker(deco_args)
def foo():pass
```
需要自己返回以函数作为参数的装饰器，及`decomaker()`用`deco_args`做了些事并返回函数对象，而该函数对象正是以foo作为其参数的装饰器
`foo = decomaker(deco_args)(foo)`
含有多个装饰器的例子
```
@deco1(deco_arg)
@deco2
def func():pass
#等价于：
func = deco1(deco_arg)(deco2(func))
```
**什么是装饰器**
装饰器实际就是函数，他们接受函数对象。一般来说，当你包装`wrap`一个函数的时候,你最终会调用它。最棒的是我们能在包装的环境下在合适的时机调用它。我们在执行函数之前，可以运行些预备代码，如`post-morrem`分析，也可以在执行代码之后做清理工作。所以一个装饰器函数代码里面定义了某个函数并在定义内的某处嵌入了对目标函数的调用或者至少一些引用，从本质上看，这些特征引入了java开发者称为`AOP(Aspect Oriented Programming)`面向方面的编程,你可以在装饰器中置入通用功能的代码来降低程序的复杂度。例如，可以用装饰器来：
* 引入日志
* 增加计时逻辑来检测性能
* 给函数加入事务的能力
```python
This demonstration of a decorator(and closures) shows that it is merely a “wrapper” with which to “decorate” (or overaly) a function, returning the altered function object and reassigning it to the original identifier, forever losing access to the original function object.
#! /usr/bin/env python
from time import ctime, sleep
def tsfunc(func):
    def wrappedFunc():
        print ‘[%s] %s() called’ %(ctime(), func.__name__)
        return func()
    return wrappedFunc

@tsfunc
def foo():
    print ‘into foo()’

foo()
sleep(4)
for i in range(2):
    sleep(1)
    foo()
```

**传递函数**(Passing Functions)
学习C语言时，函数指针的概念是一个高级话题，但对于函数就像其他对象的python来说就不一样了。函数可以被引用(访问或者以其它变量作为其别名)`accessed or aliased to other variables`，也可作为参数传递给其他函数，以及作为列表和字典等容器对象的元素。函数有一个特性将他与其它对象分离开，那就是它可被调用。

其他参数变量也可以通过别名来引用一个函数`can be aliases to other variables`。因为所有对象通过引用来传递，函数也不例外。函数同对象一样，当对一个变量赋值时，实际将相同变量的引用赋值给这个变量，如果对象是函数的话，这个对象所有的别名是可调用的。
```python
A more realistic example of passing functions as arguments and invoking them from within the function. This script converts a sequence of numbers to the same type using the conversion function that is passed in, In particular, the test() function passes in a built-in function, int() or float() to perform the conversion.
#!/usr/bin/env python
def convert(func, seq):
    ‘conv. sequence of numbers to same type’
    return [func(eachNum) for eachNum in seq]

myseq = (123, 45.67, -6.2e8, 99999999999999L)
print convert(int, myseq)
print convert(long, myseq)
print convert(float, myseq)

In [49]: run numConv.py
[123, 45, -620000000, 9999999999999]
[123L, 45L, -620000000L, 9999999999999L]
[123.0, 45.67, -620000000.0, 9999999999999.0]
```

### 形式参数`Formal Arguments`
**默认参数**`Default Arguments`
默认参数让程序上升到极高的级别。
抓取网页`grabWeb.py`
```python
This script downloads a Web page(default to local www server) and displays the first and last non-blank lines of the HTML file.Flexibility is added due to both default arguments of the download() funcion, which will allow overriding with different URLs or specifications of a differenct processing funciton.
#!/usr/bin/env python
from urllib import urlretrieve

def firstNonBlank(lines):
    for eachLine in lines:
        if not eachLine.strip():
            continue
        else:
            return eachLine

def firstlast(webpage):
    f = open(webpage)
    lines = f.readlines()
    f.close()
    print firstNonBlank(lines),
    lines.reverse()
    print firstNonBlank(lines),

def download(url=’http://www.baidu.com’, process=firstLast):
    try:
        retval = urlretrieve(url)[0]
    except IOError:
        retval = None
    if retval:
        process(retval)

if __name__ == ‘__main__’:
    download()
```
**可变长度参数**`Variable-Length Arguments`
由于函数调用提供了关键字以及非关键字两种参数类型，python用两种方法来支持变长参数，函数调用中使用`*`和`**`符号来指定元组和字典的元素作为非关键字以及关键字参数的方法。

**非关键字可变长参数-元组**`Non-keyword Variable Arguments(Tuple)`
当函数被调用的时候，所有形参(必须的和默认的)都值将赋给了在函数声明中相对应的局部变量,剩下的非关键字参数按顺序插入到一个元组中便于访问。可能你对C中的“varargs”很熟悉(i.e.`va_list, va_arg`以及省略号[...])。Python提供了与之想等的支持--迭代过所有元组元素`over the tuple elements`和在C中用`va_arg`是相同的。或者可以简单认为在函数调用时，接受一个不定(非固定)数目的参数。
可变长的参数元组必须在位置和默认参数之后`follow all positional and defalult parameters`。
```
def function_name([formal-args,] *vargs_tuple):
    “function_documentation_string”
    function_body_suite
```
`*`操作符之后的形参将作为元组传递给函数，元组保存了所有传递给函数的”额外”的参数(匹配了所有位置和具名参数后剩余的额)。如果没有给出额外的参数，元组为空。所有的形式参数必须先于非正式的参数之前出现。

**关键字变量参数-字典**`Keyword Variable Arguments(Dictionary)`
在我们有不定数目的或者额外集合的关键字的情况中，参数被放入一个字典中，字典中键为参数名，值为相应的参数值。
```
def function_name([formal_args,][*vargst,] **vargsd):
    function_documentation_string
    function_body_suite
```
为了区分关键字参数和非关键字非正式参数，使用了双星号(`**`)。`**`为了不与幂运算`exponentiation`发生混淆。关键字变量参数应该为函数定义的最后一个参数，带`**`。


