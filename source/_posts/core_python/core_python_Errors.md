---
layout: post
title: Errors and Exceptions
category: python
tags: python
---
## 检测和处理异常
`try`语句主要有两种形式:`try-except`和`try-finally`。一个try语句可以对应多个except子句。或复合语句`try-except-finally`。你可以添加一个可选的`else`子句处理没有探测到异常的执行的代码。
### try-except语句
try-except语句定义了进行异常监控的一段代码，并且提供了处理异常的机制。
```
try：
    try_suit    #watch for exceptions here监控这里的异常
except Exception, [, reason]:
    except_suite    #exception-handling code 异常处理代码
```
**Core Note**:忽略代码，继续执行，和向上移交(skiping code, continuation, and upward propagation)
`try`语句块中异常发生点后的剩余语句永远不会执行，一旦一个异常被引发，就必须决定控制流下一步到达的位置。剩余代码将被忽略，解释器将搜索处理器，一旦找到，就开始执行处理器中的代码。
如果没有找到合适的处理器，那么异常就向上移交给调用者去处理，这意味着堆栈框架立即回到之前的那个。如果上层调用者也没有找到对应的处理器，该异常会继续被向上移交，直到找到合适处理器。如果到达最顶层仍没有找到对应的处理器，那么就认为这个异常是未处理的，Python解释器会显示出跟踪返回消息，然后退出。

### 封装内建函数(Wrapping a Built-in Function)
处理多个异常的except语句
```python
def safe_float(obj):
    try:
        retval = float(obj)
    except ValueError:
        retval = ‘could not convert non-number to float’
    except TypeError:
        retval = ‘object type cannot be converted to float’
    return retval
```
我们还可以在一个except子句里处理多个异常。except语句在处理多个异常时要求异常被放在一个元组里
```
def safe_float(obj):
    try:
        retval = float(obj)
    except (ValueError, TypeError):
        retval = ‘argument must be a number or numeric string’
    return retval
```
捕获所有异常
有些异常不是由错误条件引起的，它们是`SystemExit`:由于当前Python应用程序需要退出和`KeyboardInterupt`:用户按下CTRL-C想要关闭Python。pyhton2.5对异常结构做了一些新变化，增加了BaseException,把Keyboardinterupt和Systemexit从Exception里移出来，和Exception平级。
```
——BaseException
   |——Keyboardinterupt
   |——SystemExit
   |——Exception
      |——(all other current built-in exceptions)
```
这样当有了一个Exception处理器后，不必为这两个异常类创建额外的处理器。
```
try:
    :
except Exception, e:
    #handle real errors
```
如果确实需要捕获所有异常，你就得使用BaseException:
```
try:
    :
except BaseException, e:
    #handle all erros
```
**核心风格**:不要处理并忽略所有错误
一个不正确的使用方法就是把它作为一个大绷带“绑定”到一大片代码上，`try-except`的作用是提供一个可以提示错误或处理错误的机制，而不是错误过滤器。

在应用中使用我们封装的函数(cardrun.py)
```
We use safe_float() to process a set of credit card transactions given in a file and read in as string, A log file tracks the processing.
#! /usr/biin/env python
def safe_float(obj):
    ‘safe version of float()’
    try:
        retval = float(obj)
    except (ValueError, TypeError), diag:
        retval = str(diag)
    return retval

def main():
    ‘handles all the data processing’
    log = open(‘cardlog.txt’, ‘w’)
    try:
        ccfile = open(‘carddata.txt’, ‘r’)
    except IOError, e:
        log.write(‘no txns this month\n’)
        log.close()
        return
    txns = ccfile.readlines()
    ccfile.close()
    total = 0.00
    log.write(‘account log:\n’)

    for eachTxn in txns:
        result = safe_float(eachTxn)
        if isinstance(result, float):
            total += result
            log.write(‘data ... processed\n’)
        else:
            log.write(‘ignored: %s’ %result)
    print ‘$%.2f(new balance)’ % (total)
    log.close()

if __name__ == ‘__main__’:
    main()
```
finally子句：下面是`try-except-else-finally`语法的示例
```
try:
    try_suit
except Exceptional:
    suit_for_Exceptional
except (Exception2, Exception3, Exception4):
    suit_for_Exceptionas_2_3_and_4
except Exception5, Argument5:
    suite_for_Exception5_plus_argument
except (Exception6, Exception7), Argument67:
    suit_for_Exception6_and_plus_argument
except:
else:
    no_exceptions_detected_suite
finally:
    always_execute_suite
```
### 上下文管理(Context Management Protocol)
**with**语句的目的在于从流程图中把try, except和finally关键字和资源分配释放相关代码统统去掉，基本用法如下：
with context_expr[as var]:
    with_suite
`with`语句能工作于支持上下文管理协议(context management protocol)的对象。支持该协议的对象：
* file
* decimal.Context
* thread.LockType
* threading.Lock
* threading.RLock
* threading.Condition
* threading.Semaphore
* threading.BoundedSemaphore
```
with open(‘passwd’, ‘r’) as f:
    for eachLine in f:
        #...do stuff with eachLine or f...
```
这个代码试图打开一个文件，把文件对象赋给f，然后用迭代器遍历文件。无论在这一段代码的开始，中间，还是结束时发生异常，会执行清理的代码，此外文件仍会被自动的关闭。
因为已经从你手边拿走了一堆细节，所以实际上只是进行了两层处理：
1. 发生用户层----和in类似，你所需要关心的只是被使用的对象
2. 在对象层,既然这个对象支持上下文管理协议，它干的也就是“上下文管理”
**上下文管理协议**(Context Managment Protocol)

**触发异常**(Raising Exceptions)
`raise`语句用法`raise [SomeException [, args[, traceback]]]`
第一个参数，SomeException，是触发异常的名字，它必须是一个字符串，类或实例。
第二个符号为可选的args，来传给异常。这可以是一个单独的对象也可以是一个对象元组。
最后一个参数，traceback，同样是可选的
`raise`语句的用法
`raise exclass`    触发一个异常，从`exclass`生成一个实例(不含任何异常参数)
`raise exclass()`    同上，除了现在不是类;通过函数调用操作符(function calloperator:”()”)作用与类名生成一个新的exclass实例，同样也没有异常参数
`raise exclass, args`   同上，但同时提供的异常参数args，可以一个参数也可以元组
`raise exclass(args)`   同上
`raise exclass, arg, tb`   同上但提供一个追踪(traceback)对象tb使用
`raise exclass, instance`   通过实例触发异常(通常是exclass的实例);如果是exclass的子类的实例，那么这个新异常的类型会是子类的类型(而不是exclass);如果实例既不是exclass的实例也不是exclass子类的实例，那么会复制此实例为异常参数去生成一个新的exclass实例。
`raise instance`   通过实例触发异常;异常类型是实例的类型;等价于raise instance.\_\_class\_\_, instance(同上)

**断言**(assertions)
可以理解为(raise-if-not语句),测试一个表达式，如果返回值是假，触发异常。
```
In [8]: assert 1 == 0
---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
<ipython-input-8-34d369bab8db> in <module>()
----> 1 assert 1 == 0
AssertionError:
```
### 标准异常(Standard Exceptions)
Python内建异常
`BaseException`   所有异常的基类
`SystemExit`    python解释器请求退出
`KeyboardInterupt`   用户中断执行
`Exception`     常规错误基类
`StopIteration`   迭代器没有更多的值
`GeneratorExit`   生成器(generator)发生异常来通知退出
`StandardError`   所有内建标准异常的基类
`ArithemticError`   所有的数值计算错误的基类
`FloatingPointError`   浮点计算错误
`OverflowError`    数值运算超出最大限制
`ZeroDivisionError`   除(或取模)零
`AssertionError`    断言语句失败
`AttributeError`   对象没有这个属性
`EOFError`   没有内建输入，到达EOF标记
`EnvironmentError`   操作系统错误的基类
`IOError`   输入输出错误的基类
`OSError`   操作系统错误
`WindowsError`   Windows系统调用失败
`ImportError`   导入模块/对象失败
`LookupError`   无效数据查询的基类
`IndexError`   序列中没有此索引
`KeyError`   映射中没有这个键
`MemoryError`   内存溢出错误(对于python解释器不是致命的)
`NameError`   未声明/初始化对象(没有属性)
`UnboundLocalError`   访问未初始化的本地变量
`ReferenceError`   弱引用(weak reference)试图访问已经垃圾回收的对象
`RuntimeError`   一般的运行时的错误(Generic default error during execution)
`NotImplementedError`   尚未实现的方法(Unimplemented method)
`SyntaxError`   python语法错误(Error in Python syntax)
`IndentationError`   缩进错误(Improper indentation)
`TabError`    Tab键和空格键混合
`SystemError`   一般的解释器系统错误(Generic interpreter system error)
`TypeError`    对类型无效的操作
`ValueError`   传入无效的参数
`UnicodeError`   Unicode相关错误
`UnicodeDecodeError`   Unicode解码时的错误
`UnicodeEncodeError`   Unicode编码时错误
`UnicodeTranslateError`    Unicode转换时错误
`Warning`    警告的基类
`DeprecationWarning`   关于被弃用的特征的警告
`FutureWarning`   关于构造将来语义会有改变的警告
`PendingDeprecationWarning`   关于特征性将会被废弃的警告
`RuntimeWarning`   可疑的运行时行为的警告(runtime behavior)
`SyntaxWarning`   可疑的语法的警告
`UserWarning`   用户代码生成的警告

**创建异常**(creating exceptions)
```
This module defines two new exceptions, FileError and NetworkError, as well as reimplements more diagnostic versions of open()[myopen()] and socket. connect()[myconnect()]. Also included is a test function[test()] that is run if this module is executed directly.
#!/usr/bin/env python
import os, socket, errno, types, tempfile

class networkError(IOError):
    pass
class FileError(IOError):
    pass

def updArgs(args, newarg=None):
    if isinstance(args, IOError):
        myargs=[]
        myargs.extend([args for arg in args])
    else:
        myargs = list(args)

    if newarg:
        myargs.append(newarg)
    return tuple(myargs)

def fileArgs(file, mode, args):
    if args[0] == errno.EACCESS and ‘access’ in dir(os):
        perms = ‘’
        permd = {‘r’:os.R_OK, ‘w’:os.W_OK, ‘x’:os.X_OK}
        pkeys = permd.keys()
        pkeys.sort()
        pkeys.reverse()

        for eachPerm in ‘rwx’:
            if os.access(file, permd[eachPerm]):
                perms += eachPerm
            else:
                perms += ‘-’
        if isinstance(args, IOError):
            myargs = []
            myargs.extend([arg for arg in args])
        else:
            myargs = list(args)

        myargs[1] = “‘%s’ %s (perms: ‘%s’)” % (mode, myargs[1], perms)
        myargs.append(args.filename)
    else:
        myargs = args

    return tuple(myargs)

def myconnect(sock, host, port):
    try:
        sock.connect((host, port))
    except socket.error, args:
        myargs = updArgs(args)
        if len(myargs) == 1:
            myargs = (errno.ENXIO, myargs[0])
        raise NetworkError, updArgs(myargs, host + ‘:’ + str(port))

def myopen(file, mode=’r’):
    try:
        fo = open(file, mode)
    except IOError, args:
        raise FileError, fileArgs(file, mode, args)
    return 0

def testfile():
    file = tempfile.mktemp()
    f = open(file, ‘w’)
    f.close()

    for eachTest in ((0, ‘r’), (0100, ‘r’), (0400, ‘w’), (0500, ‘w’)):
        try:
            os.chmod(file, eachTest[0])
            f = myopen(file, eachTest[1])
        except FileError, args:
            print “%s: %s” % (args.__class__.__name__, args)

    else:
        print file, “opened ok ... perm ignored”
        f.close()
    os.chmod(file, 0777)
    os.unlink(file)

def testnet():
    s = sock.socket(socket.AF_INET, socket.SOCK_STRREAM)
    for eachHost in (‘deli’, ‘www’):
        try:
            myconnect(s, ‘deli’, 8080)
        except NetworkError, args:
            print “%s: %s” % (args.__class__.__name__, args)

if __name__ == ‘__main__’:
    testfile()
    testnet()
```
