---
layout: post
title: functions and functional programming
category: python
tags: [core_python]
---
## 函数
<!--more-->
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
```
def dictVarArgs(arg1, arg2=’defaultB’, **theRest):
    ‘display 2 regular args and keyword variable args’
    print ‘formal arg1:’, arg1
    print ‘formal arg2:’, arg2
    for eachXtrArg in theRest.keys():
        print ‘Xtra arg %s: %s’ % (eachXtrArg, str(theRest[eachXtrArg]))

In [59]: run test.py
formal arg1 : 1220
formal arg2: 740.9
Xtra arg c: grail
```
**调用带有可变长参数对象函数**`Calling Functions with Variable Argument Objects`
如何使用这些符号来把任意类型任意个数的参数传递给任意对象.
**函数式编程**:函数式编程的另外一个有用的应用出现在调试和性能测量方面。
```python
testit() invokes a given function with its arguments, returning TRue packaged with the return value of the function on success or False with cause of failure
#!/usr/bin/env python
def testit(func, *nkwargs, **kwargs):
    try:
        retval = func(*nkwargs, **kwargs)
        result = (True, retval)
    except Exception, diag:
        result = (False, str(diag))
    return result

def test():
    funcs = (int, long, float)
    vals = (1234, 12.34, ‘1234’, ‘12.34’)
    for eachFunc in funcs:
        print ‘-’ * 20
        for eachVal in vals:
            retval = testit(eachFunc, eachVal)
            if retval[0]:
                print ‘%s(%s) = %’(eachFunc.__name__, `eachVal`), retval[1]
            else:
                print ‘%s(%s) = FAILED: ’ % (eachFunc.__name__, `eachVal`), retval[1]
if __name__ == ‘__main__’:
    test()

In [63]: run testit.py
--------------------
int(1234) =  1234
int(12.34) =  12
int('1234') =  1234
int('12.34') = FAILED: invalid literal for int() with base 10: '12.34'
--------------------
long(1234) =  1234
long(12.34) =  12
long('1234') =  1234
long('12.34') = FAILED: invalid literal for long() with base 10: '12.34'
--------------------
float(1234) =  1234.0
float(12.34) =  12.34
float('1234') =  1234.0
float('12.34') =  12.34
```

## 函数式编程`Functional Programming`
Python不是也不大可能成为函数式编程语言，但它支持很多有价值的函数式编程建构。
**核心笔记**:lambda表达式返回可调用的函数对象，它们可被传入给其他函数，用额外的引用别名化，作为容器对象以及作为可调用的对象被调用(如果需要的话，可以带参数)。当被调用的时候，如果给定相同的参数，这些对象会生成一个和相同表达式等家的结果，它们和那些返回等价表达式计算值相同的函数不能区分。
```
In [66]: a = lambda x, y = 2: x+y
In [67]: a(3)
Out[67]: 5
```
补充：虽然lambda看起来是一个函数的单行版本，但它不等同于c++的内联语句，内敛语句处于性能原因，在调用时绕过函数的栈分配。lambda表达式运作起来就像一个函数，当被调用时创建一个框架对象。

**内建函数`apply() filter() map() reduce()`**
`apply(func[, nkw][, kw])`   用可选的参数来调用func，nkw为非关键字参数， kw关键字参数，返回值是函数调用的返回值，此函数逐渐废弃。
`filter(func, seq)`   给定一个对象的序列和一个”过滤”和函数，每个序列元素都通过这个过滤器进行筛选，保留函数返回为真的对象。filter函数为已知的序列每个元素调用给定布尔函数，每个filter返回的非零(true)值元素添加到一个列表中。返回的对象是一个从原始队列中”过滤”后的队列。
```
In [68]: from random import randint
In [69]: def odd(n):
   ....:     return n % 2
In [70]: allNums = []
In [71]: for eachNum in range(9):
   ....:     allNums.append(randint(1, 99))
In [72]: print filter(odd, allNums)
[59, 35, 69, 7, 33]
```
`map()`:   map()与filter()相似，但map()将函数调用”映射”到每个序列的元素上，并返回一个含有返回值的列表。
```
In [73]: map(lambda x, y: x + y, [1,3,5],[2,4,6])
Out[73]: [3, 7, 11]
```
`reduce()`:使用一个二元函数(一个接收带两个值作为输入，进行一些计算然后返回一个值作为输出)可以形象化等同下面:
`reduce(func, [1, 2, 3] = func(func(1, 2), 3))`
```
In [1]: print 'the total is:', reduce((lambda x,y: x+y), range(5))
the total is: 10
```

**偏函数应用**(Partial Function Application)
`currying`的概念将函数式编程的概念和默认参数以及可变参数结合在一起，一个函数带n个参数，`curried`的函数固化地一个参数为固定参数，并返回另一个带n-1个参数函数对象，分别类似于LISP的原始函数`car`和`cdr`的行为。`Currying`能泛化成为偏函数应用`partial function application(PFA)`。这种函数将任意数量(顺序)的参数的函数转化成为另一个带剩余参数的函数对象。
```
In [4]: from operator import add, mul
In [5]: from functools import partial
In [6]: add1 = partial(add, 1)
In [7]: mul100 = partial(mul, 100)
In [8]: add1(10)
Out[8]: 11
In [9]: mul100(10)
Out[9]: 1000
```
下个例子使用int()内建函数并将base固定为2来指定二进制字符串转化。
```
In [10]: baseTwo = partial(int, base=2)
In [11]: baseTwo.__doc__='Convert base 2 string to an int'
In [13]: baseTwo('10010')
Out[13]: 18
```
警惕关键字，如果你创建了不带`base`关键字的偏函数，比如,baseTwoBAD = partial(int, 2), 这可能会让参数以错误的顺序传入`int()`，因为固定参数的总放在运行时刻参数的左边。
```python
This a more useful example of partial function application, or more accurately, “partial class instantiation” in this case why?
#! /usr/bin/env python
from functools import partial
import Tkinter

root = Tkinter.Tk()
MyButton = partial(Tkinter.Button, root, fg=’white’, bg=’blue’)
b1 = MyButton(text=’Button 1’)
b2 = MyButton(text=’Button 2’)
qb = MyButton(text=’QUIT’, bg=’red’, command=root.quit)
b1.pack()
b2.pack()
qb.back(fill=Tkinter.X, expand=True)
root.title(‘PFAs!’)
root.mainloop()
```
## 变量作用域`Variable Scope`
**global语句**
```
In [35]: is_this_global = 'xyz'
In [36]: def foo():
   ....:     global is_this_global
   ....:     this_is_local = 'abc'
   ....:     is_this_global = 'def'
   ....:     print this_is_local + is_this_global
In [37]: foo()
abcdef
In [38]: print is_this_global
def
```
**闭包`Closures`**
闭包是将内部函数自己的代码和作用域以外及外部函数的作用结合起来，闭包词法变量`lexical variables`不属于全局命名空间或是局部命名空间，属于其他命名空间，带着流浪的作用域`carry an ‘on the road’ kind of scope`。(注意不同与对象那些变量存在于对象作用域，而闭包变量存在于函数的空间中)。

`Closures`对于安装计算`setting up calculations`,隐藏状态`hiding state`,及在函数对象和作用域中随意切换很容易`move around function objects and scope`。`Closures`在GUI或API支持的回调函数的事件驱动编程中很有用处(event-driven programming where a lot of APIs support callbacks)。以绝对相同的方式，应用于获取数据库行和处理数据`retrieving database rows and processing the data`。回调就是函数，闭包也是函数，但他们能携带一些额外的作用域或一些特征(carry some additional scope with them)
```
In [41]: def counter(start_at=0):
   ....:     count = [start_at]
   ....:     def incr():
   ....:         count[0] += 1
   ....:         return count[0]
   ....:     return incr

In [44]: count = counter(5)
In [45]: print count()
6
In [46]: print count()
7
In [47]: count2 = counter(100)
In [48]: print count2()
101
In [49]: print count()
8
```
现在很多情况下，类是最适合使用的。闭包更适合需要一个必须有自己的作用域的回调函数情况，尤其是回调函数是很小巧而且简单的，通常也很聪明。通常情况对代码进行注释或文档字符串来解释是个不错的主意。

```
This example shows how we can track closure variable by using a function’s func_closure variable
#!/usr/bin/env python
output = ‘<int %r id=%#0x val=%d>’
w = x = y = z = 1
def f1():
    x = y = z = 2
    def f2():
        y = z = 3
        def f3():
            z = 4
            print output % (‘w’, id(w), w)
            print output % (‘x’, id(x), x)
            print output % (‘y’, id(y), y)
            print output % (‘z’, id(z), z)
        clo = f3.func_closure
        if clo:
            print “f3 closure vars:”, [str(c) for c in clo]
        else:
            print “no f3 closure vars”
        f3()
    clo = f2.func_closure
    if clo:
        print “f2 closure vars:”, [str(c) for c in clo]
    else:
        print “no f2 closure vars:”, [str(c) for c in clo]
clo = f1.func_closure
if clo:
    print “f1 closure vars:”, [str(c) for c in clo]
else:
    print “no f1 closure vars”
f1()
```
**高级闭包和装饰器**`Advanced Closures and Decorators Example`
```
This example shows a decorator that takes an argument that ultimely determines which closure will be used. Also featured is the power off closures.
#!
```

## 生成器`Generators`
另一方面的生成器的重要因素是协程`coroutines`的概念。协程是可以运行的独立函数调用(independent function call that can run),可以被暂停或挂起,并且可以从离开的地方继续或是重新开始(be paused or suspended, and be continued or resumed where it left off),在调用者和被调用者也有通讯。例如当一个协程暂停的时候，我们可以获取一个中间值，并且当再次被调用到程序中可以传递额外的或是改变了的参数，仍能够从上次离开的地方继续，并且所有状态完整。挂起返回出中间值并多次继续的协程被称为生成器，这就是python的生成器真正在做的事(Coroutines that are suspended yielding intermediate values and resumed multiple time are called generators),在调用的生成器能挂起(返回一个结果)之前,调用生成器返回一个结果而不是阻塞等待那个结果返回。

什么是python式的生成器？从句法上讲，生成器是一个带`yield`语句的函数。一个函数或者子程序只返回一次，但一个生成器能暂停执行并返回一个中间的结果--那就是yield语句的功能，返回一个值给调用者并暂停执行。当生成器的`next()`方法被调用的时候，它会准确地从离开地方继续(当它返回[一个值以及]控制给调用者时)。(What is a generator Python-wise? Syntactically, a generator is a function with a yield statement. A function or subroutine only returns once, but a generator can pause execution and yield intermediate results that is the functionality of the yield statement, to return a value to the caller and to pause execution. When the next() method of a generator is invoked, it resumes right where it left off(when it yield [a value and] control back to the caller))

**简单的生成器特性**
```
In [17]: def simpleGen():
   ....:     yield 1
   ....:     yield '2--->punch!'
   ....:     
In [18]: myG = simpleGen()
In [19]: myG.next()
Out[19]: 1
In [20]: myG.next()
Out[20]: '2--->punch!'
```
在接下来的几章，面向对象的编程的时候，将看见生成器较简单(和无限)的版本作为类的迭代器。使用生成器最好的地方就是当你正迭代穿越一个巨大的数据集合，而重复迭代这个数据集合是一个很麻烦的事，比如一个巨大的磁盘文件，或者一个复杂的数据库查询。对于每行的数据，你希望执行非元素的操作以及处理，但当正指向和迭代过它的时候，你“不想失去你的地盘”
你想要抓取一块数据，比如将它返回给调用者来处理以及可能的对(另外一个)数据库的插入，接着你想要运行一次next()来获得下一块的数据，等等，状态在挂起和再继续的过程中是保留了的，所以你会觉得很舒服有一个安全的处理数据的环境。

**加强的生成器特性**
一些加强特性加入到生成器中，除了`next()`来获得下一个生成的值，用户可以将值回送给生成器[send()],在生成器中抛出异常，以及要求生成器退出[(close())]。
```
In [40]: def counter(start_at=0):
   ....:     count = start_at
   ....:     while True:
   ....:         val = (yield count)
   ....:         if val is not None:
   ....:             count = val
   ....:         else:
   ....:             count += 1
   ....:             
In [41]: count = counter(5)
In [42]: count.next()
Out[42]: 5
In [43]: count.next()
Out[43]: 6
In [44]: count.next()
Out[44]: 7
In [45]: count.send(9)
Out[45]: 9
In [46]: count.next()
Out[46]: 10
In [47]: count.close()
In [48]: count.next()
---------------------------------------------------------------------------
StopIteration                             Traceback (most recent call last)
<ipython-input-48-3963aa0a181a> in <module>()
----> 1 count.next()
StopIteration: 

```

