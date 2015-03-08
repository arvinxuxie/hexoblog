---
layout: post
title: apue_process
date: 2014-10-27
category: c/c++
tags: [apue, c]
mathjax: true
---

##进程环境
本章主要讲解：程序执行时，其`main`函数是如何调用的;命令行参数是如何传递给执行程序的;典型的存储器布局是什么样的;如何分配另外的存储空间;进程如何使用环境变量;各种不同进程终止方式等。说明`longjmp`和`setjmp`函数及它们与栈的交互作用。
<!--more-->
### `main`函数
`main`函数原型
```cpp
int main(int argc, char *argv[]);
```
其中，`argc`是命令行参数的数目，`argv`是指向参数的各个指针所构成的数组。

当内核执行C程序时(使用`exec`函数)，在调用`main`之前先调用一个特殊的启动例程。可执行程序文件将此启动例程指定为程序的起始地址-这是由连接编辑器设置的，连接编辑器由C编译器调用。启动例程从内核取得命令行参数和环境变量值。上述方式为调用`main`函数做好安排。
###进程终止
有8种方式使进程**终止**(_termination_)，其中5种为正常终止，它们是
1. 从`main`返回
2. 调用`exit`
3. 调用`_exit`或`_Exit`
4. 最后一个线程从启动例程返回
5. 最后一个线程调用`pthread_exit`
异常终止有3种方式，它们是
6. 调用`abort`
7. 接到一个信号并终止
8. 最后一个线程对取消请求请求作出响应
#### exit函数
有三个函数用于正常终止一个程序
```cpp
#include <stdlib.h>
void exit(int _status_);
void _Exit(int _status_);
#include<unistd.h>
void _exit(int _status_);
```
`exit`函数总是执行一个标准I/O库的清理关闭操作：为所有打开的流调用`fclose`函数。
`main`函数返回一整型值与调用`exit`是等价的及`return (0);` 和 `exit(0);`
#### atexit函数
按照ISO C的规定，一个进程可以登记多大32个函数，这些函数将由`exit`自动调用。我们称这些函数为终止处理程序_exit handler_，并调用`atexit`函数来登记这些函数。
![exit函数]({{BASE_PATH}}/image/apue/exit.png)
```cpp
#include <stdlib.h>
int atexit(void (*func)(void));
```
其中，`atexit`的参数是一个参数地址，当调用此函数时无需向它传递任何参数，也不期望它返回一个值。`exit`调用这些函数的顺序与它们登记时候的顺序相反。同一函数如若登记多次，则也会被调用多次。
注意，内核使用程序执行的唯一方法是调用一个`exec`函数。进程自愿终止的唯一方法是显式或隐式地(调用`exit`),调用`_exit`或`_Exit`。进程也可非自愿地由一个信号使其终止。
```cpp
#include <stdio.h>
#include <stdlib.h>
static void my_exit1(void);
static void my_exit2(void);
int
main(void)
{
    if(atexit(my_exit2) != 0)
    {
        printf("can't register my_exit2\n");
    }
    if(atexit(my_exit1) != 0)
    {
        printf("can't register my_exit1\n");
    }
    if(atexit(my_exit1) != 0)
    {
        printf("can't register my_exit1\n");
    }
    printf("main is done\n");
    return 0;
}

static void
my_exit1(void)
{
    printf("first exit handler\n");
}

static void
my_exit2(void)
{
    printf("second exit handler\n");
}
```
### 环境表
每个程序都会接收到一张环境表，与参数表一样，环境表也是一个字符指针数组，其中每个指针包含一个以`null`结束的C字符串地址。全局变量`environ`则包含了该指针数组的地址：
```cpp
extern char **environ;
```
例如，如果该环境包含5个字符串，如图所示。其中，每个字符串的结尾处都显式地有一个`null`字符。我们称`environ`为环境指针(*environment pointer*),指针数组为环境表，其中各指针指向的字符串为环境字符串。
![environ]({{BASE_PATH}}/image/apue/environ.png)
历史上，大多数UNIX系统支持`main`函数带有三个参数，其中第三个参数就是环境的地址：
```cpp
int main(int _argc_, char *argv[], char *envp[]);
```
因为ISO C规定`main`函数只有两个参数，而第三个参数与全局变量`environ`相比也没有带来更多好处，所以POSIX.1也规定使用`environ`变量。通常用`getenv`和`putenv`函数来访问特定的环境变量，而不是用`environ`变量。如果要看整个环境，则必须使用`environ`指针。
### C程序的存储空间布局
* 正文段(Text segment): 这是由CPU执行的机器指令部分。通常，正文段是可共享的，所以即使是频繁执行的程序(文本编辑器，C编译器和Shell等)在存储器也只有一个副本，另外，正文段常常是只读的，以防止程序由于意外而修改其自身的指令。

* 初始化数据段(Initialized data segment): 通常将此段称为数据段，它包含了程序中需要的明确地赋初值的变量。例如，C程序中出现在任何函数之外的声明：
```cpp
int maxcount = 99;
```
此变量带有其初值存放在初始化数据段中。
* 非初始化数据段(Uninitialized data segment): 通常将此段称为`bss`段，这一名称来源于早期的汇编运算符，为`block started by symbol`(由符号开始的块)，在程序开始执行之前，内核将此段中的数据初始化为`0`或空指针。出现在任何函数外的C声明,此变量存放在非初始化数据段中。
```cpp
long sum[1000];
```
* 栈(Stack): 自动化变量以及每次函数调用时所保存的信息都存放在此段中。每次调用函数时，其返回的地址以及调用者的环境信息(例如某些机器寄存器的值)都存放栈中。最近被调用的函数在栈上为其自动和临时变量分配空间。通过以这种方式使用栈，可以递归调用C函数。递归函数每次调用自身时，就是用一个新的栈帧，因此一个函数调用实例中的变量集不会影响另一个函数调用实例中的变量。
* 堆(Heap): 通常在堆中进行动态存储分配。历史上的惯例，堆位于非初始化数据段和栈之间。
![memery]({{BASE_PATH}}/image/apue/memery.png)
图中这些段是一种典型安排,这是程序的逻辑布局。
    a.out中还有若干其他类型的段，例如，包含符号表的段、包含调试信息的段以及包含动态共享库链接表的段等等。这些部分并不装载到进程执行的程序镜像中。
需要注意的是为初始化数据段的内容并不存放在磁盘上的程序文件中。原因是，内核在程序开始运行前将他们都设置为0。需要存放在程序文件中的段只有正文段和初始化数据段。
size命令报告正文段、数据段和bss段的长度。
```
> $ size /usr/bin/cc /bin/sh                                                                                               ⬡ 0.1
   text	   data	    bss	    dec	    hex	filename
 570546	   3864	   7208	 581618	  8dff2	/usr/bin/cc
 102565	   1784	  11272	 115621	  1c3a5	/bin/sh
```
### 共享库
共享库使得可执行文件不再包含共用的库例程(the common library routines)，而只需在所有进程都可引用的存储区中维护这种库例程的一个副本。这样可以减小可执行文件的大小，但增加了运行时间的开销，当程序第一次执行或共享库函数第一次被调用时。共享库的另一个优点是可以用库函数的新版本代替老版本，而无需对使用该库的程序重新连接编辑。
```
#阻止gcc使用共享库
$ cc -static hello.c -o hello1
$ size hello1
  text	   data	    bss	    dec	    hex	filename
  700395	   3600	  12520	 716515	  aeee3	hello1
#编译程序使用共享库，则可执行文件的正文和数据段长度显著减小：
$ cc hello.c -o hello2
$ size hello2
   text	   data	    bss	    dec	    hex	filename
   1184	    568	      8	   1760	    6e0	hello2
```
### 存储器分配
ISO说明了三个用于存储空间动态分配的函数。
1. `malloc`分配指定字节的存储区。此存储区的初始值不确定。
2. `calloc`为指定数量指定长度的对象分配存储空间。该空间中的每一位都初始化为_0_
3. `realloc`更改以前分区的长度(增加或减少)。当增加长度时，可能需将以前分配区的内容移到另一个足够大的区域，以便在尾端提供增加的存储区，而新增区域内的初始值不确定。
```cpp
#include <stdlib.h>
void *malloc(size_t _size_);
void *calloc(size_t _nobj_, size_t _size_);
void *realloc(void *_ptr_, size_t _newsize_);
                                  #以上三个返回成功返回非空指针，错误返回NULL
void free(void *_ptr_);
```
以上三个函数返回的指针确保是适当对齐的`guaranteed to be suitably aligned`,这样可以适用于所有数据对象。
因为这三个`alloc`函数返回一个通用指针`generic void * pointer`,所以在程序中包含`#include <stdlib.h>`(以获得函数原型)，那么当我们将这些函数返回的指针赋予一个不同类型的指针时，就不需要显示地执行类型强制转换。
函数`free`释放`ptr`指向的存储空间，被释放的空间通常被送入可用存储区池(the pool of available memery),以后可用于在分配。
`realloc`函数使我们可以增、减以前分配区的长度。如果该存储区后有足够的空间可供扩充，则可在原存储区位置上向高地址方向扩充，无需移动任何原先的内容，并返回传送给它的同样的指针值。如果原存储区后没有足够的空间，则`realloc`分配另一个足够大的存储区，将现有元素内容复制到新分配的存储区，然后释放原存储区，返回新的指针。因为该存储区可能会移动位置，所以不应当使用任何指针指到该区中。

这些分配例程通常用系统调用`sbrk(2) system call`实现。`sbrk`可以扩充或缩小进程的存储空间。被释放的空间可供以后再分配，但通常将他们保持在`malloc`池中而不返回给内核。
注意，大多数实现所分配的存储空间比所要求的要稍微大一些，额外的空间用来记录管理信息`record keeping`,分配块的大小，指向下一个分配块的指针等等。如果超过一个以分配区的尾端进行写操作，则会重写后一个块的管理记录，会发生灾难性错误。

释放一个已经释放了的块，调用`free`时所用的指针不和那三个`alloc`函数的返回值相等。如果一个进程调用`malloc`函数，但却忘记调用`free`函数，会造成泄漏`leakage`。

### 环境变量
环境字符串的形式通常如下：
`name = value`
UNIX内核并不查看这些字符串，它们的解释完全取决于各个应用程序。
ISO C定义了一个函数`getenv`，可以用其取环境变量值，但该标准又称环境的内容是下面实现的。
```
#include <stdlib.h>
char *getenv(const char *name);
```
除了取环境变量的值，有时也需要设置环境变量。(我们能影响的只是当前进程及调用的任何子进程的环境，但不能影响父进程的环境，这通常是一个shell进程)，但注意并不是所有系统都支持这种能力。
```
#include <stdlib.h>
int putenv(char *str);
int setenv(const char *name, const char *value, int rewrite);
int unsetenv(const char *name);
```
* `putenv`取形式为name=value的字符串，将其放到环境表中。如果name已经存在，则先删除其原来的定义。
* `setenv`将name设置为value。如果在环境中name已经存在，那么(a)若rewrite非0,则首先删除其现有的定义;(b)若rewrite为0,则不删除其现有定义。
* `unsetenv`删除name的定义。即使不存在这种定义也不出错。


##进程控制
本节主要介绍进程的控制，包括创建新进程、执行程序和进程终止。
### 进程标志符
进程ID是一个非负整型且唯一的，但是可以重用。大多数UNIX系统实现**延迟重用算法**，使得赋予新建进程的ID不同于最近终止的进程所使用的ID，防止将新进程误认为是使用同一ID的某个已终止的先前进程。

系统中有一些专有进程，具体细节因实现而异。ID为`0`的进程通常是调度进程，常常被称作**交换进程**(_swapper_)，该进程是内核的一部分，它并不执行任何磁盘上的程序，因此常被称作**系统进程**(system process)。进程ID 1通常是`init`进程，在自举(bootstrap)过程结束时由内核调用。该进程的程序文件早期版本在/etc/init,新版本在/sbin/init。此进程负责在自举内核后启动一个UNIX系统。init通常读与系统有关的初始化文件(/etc/rc\*文件或/etc/inittab文件，以及/etc/init.d中的文件),并将系统引导到一个状态(例如多用户)。init进程决不会终止。它是一个普通的用户进程(与交换进程不同，它不是内核中的系统进程)，但它是以超级
除了进程ID，每个进程还有一些其他的标志符。
```cpp
#include <unistd.h>
pid_t getpid(void);    //返回值： 调用进程的ID
pid_t getppid(void);    //返回值：调用进程的父进程ID
```

