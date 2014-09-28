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

