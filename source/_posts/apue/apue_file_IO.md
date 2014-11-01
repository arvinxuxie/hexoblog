---
layout: post
title: apue_file_I/O
category: c/c++
tags: [apue, c]
---
<!--more-->
## 文件描述符`File Descriptors`
对于内核来说，所有打开的文件都通过文件描述符来引用，一个文件描述符是一个非负整数。当我们打开一个已存在的文件或是创建一个新的文件，内核返回一个文件描述符给这个进程。当读或是写一个文件时，使用`open`或是`creat`返回的文件描述符标识该文件，将其作为参数传递给`read`或`write`。

通常，Unix系统shell将文件描述符0和进程的标准输入`standard input`相关联，文件描述符1和标准输出`standard ouput`相关联，文件描述符2和标准出错输出`standard error`相关联。这种惯例在`shell`中及其他很多应用中被使用，并不是Unix内核的特性。
在依从POSIX的应用程序中，幻数`magic numbers` 0, 1, 2应当替换成符号常量`symbolic constants`: `STDIN_FILENO, STDOUT_FILENO, STDERR_FILENO`来增加可读性。这些常量`constants`被定义在`<unistd.h>`头文件里。
文件描述符的变化范围是`0~OPEN_MAX`。早期每个进程允许打开文件最多是20个，后来很多系统将其增加到63。

## `open`和`openat`函数
文件被打开或是创建通过调用`open`函数或是`openat`函数
```c
#include <fcntl.h>
int open(const char *path, int oflag, .../*mode_t mode*/);
int openat(int fd, const char *path, int oflg, .../*mode_t mode*/);
           /*Both return: file descriptor if OK, -1 on error*/
```
我们将第三个参数写为`...`, ISO C用这种方法表明余下参数的数量及其类型根据具体的调用会有所不同。对于`open`函数而言，进当创建新文件时才使用第三个参数。
参数`path`是被打开或是创建的文件名称，这个函数有多个选项，被`oflag`参数标识。用下列一个或多个常量进行“或”`ORing`运算，这些参数被包含在`<fcntl.h>`头中。
* `O_RDONLY`:  Open for reading only
* `O_WRONLY`:  Open for writing only
* `O_RDWR`:  Open for reading and writing
**大多数实现将`O_RDONLY`定义为0, `O_WRONLY`定义为1, `O_RDWR`定义为2,与早期的程序兼容。**
上述三个常量中必须指定一个且只能指定一个，下列常量则是可选择的：
* `O_APPEND`: 每次写时都能追加到文件得尾端
* `O_CREAT`: 若此文件不存在，则创建它。使用此选项时，需要第三个参数`mode`，用其指定该新文件的访问权限位。
* `O_EXCL`: 如果同时指定了`O_CREAT`,而文件已经存在，则会出错。用此可以测试一个文件是否存在，如果不存在则创建此文件，这使测试和创建两者成为一个原子操作。
* `O_TRUNC`: 如果此文件存在，而且为只写或者读写成功打开，则将其长度截断为0
* `O_NOCTTY`: 如果`path`指的是一个`FIFO`、一个块特殊文件或一个字符特殊文件，则此选项为文件的本次打开操作和后续得I/O操作设置非阻塞模式。
下面三个标志也是可选的，它们是`Single UNIX Specification(POSIX.1)`中同步输入和输出选项的一部分。
`O_DSYNC`: 使每次`write`等待物理I/O操作完成，但是如果写操作并不影响读取刚写入的数据，则不等待文件属性被更新
`O_RSYNC`: 使每一个以文件描述符作为参数得`read`操作等待，直至任何对文件同一部分进行得未决写操作都完成

参数`fd`对于`openat`函数和`open`函数有所不同，有一下三种可能
1. 参数`path`指定的是绝对路径，这种情况参数`fd`会被忽略函数`openat`和`open`就有相同得行为
2. 参数`path`指定的是相对路径并且`fd`是一个文件描述符即`pathname`相对计算的文件系统的开始位置(The pah paramenter specifies a relative pathname and the fd parameter is a file descriptor that specifies the starting location in the file system where the relative pathname is to be evaluted. The fd parameter is obtained by opening the directory where the relative pathname is to be evaluated).
3. The `path` parameter specifies a relative pathname and the `fd` parameter has the special value `AT_FDCWD`. In this case, the pathname id evaluted starting in the current working directory and the openat function behaves like the open function.

**文件名和路径名截断**`Filename and Pathname Truncation`
在`POSIX.1`中，常量`_POSIX_NO_TRUNC`决定了是要截断过长得文件名或路径名，还是返回一个出错。若`_POSIX_NO_TRUNC`有效，则在整个路径名超过`PATH_MAX`,或路径名中的任一文件名超过`NAME_MAX`时，返回出错状态，并将`errno`设置为`ENAMETOOLONG`。

## `creat`函数
一个新的文件可以被创建通过调用函数`creat`
```c
#include <fcntl.h>
int creat(const char *path, mode_t mode);
    /*Returns: file descriptor opened for write-only if Ok, -1 on error*/
```
这个函数等价于
`open(path, O_WRONLY | O_CREAT | O_TRUNC, mode);`
`creat`的一个不足之处是它以只写方式打开所创建得文件。

## `close`函数
可以调用一个`close`函数关闭打开得文件
```c
#include <unistd.h>
int close(int fd);
    /*Returns: 0 if Ok, -1 on error*/
```
当一个进程终止时，内核自动关闭它所打开得文件，很多程序都利用了这一点而不显示得调用`close`关闭打开文件。

## `lseek`函数
每个打开的文件都有一个与其相关联的“当前文件偏移量”`current file offset`，它通常是一个非负整数，用以度量从文件开始计算得字节数。通常读、写操作都是从当前文件偏移量处开始，并使偏移增加所读写得字节数。按系统默认得情况，当打开一个文件时，除非指定`O_APPEND`选项，否则该偏移量被设置为0。可以调用`lseek`显示地为一个打开的文件设置其偏移量。
```c
#include <unist.h>
off_t lseek(int filedes, off_t offset, int whence);
            /*Returns: new file offset if OK, -1 on error*/
```
对参数`offset`的解释与参数`whence`的值有关。
* 若`whence`是`SEEK_SET`,则将该文件的偏移量设置为距文件开始处`offset`个字节
* 若`whence`是`SEEK_CUR`,则将该文件得偏移设置为其当前值加`offset`, `offset`可为正或负
* 若`whence`是`SEEK_END`,则将该文件得偏移量设置为文件长度加`offset`,`offset`可为正或负
若`lseek`成功执行，则返回新的文件偏移量，为此可以用下列方式确定打开文件的当前偏移量：
```
off_t currpos;
currpos = lseek(fd, 0, SEEK_CUR);
```
这种方法也可以用来确定所涉及的文件是否可以设置偏移量。如果文件描述符引用的是一个管道、FIFO或网络套接字，则`lseek`返回`-1`，并将`errno`设置为`ESPIPE`。
**三个符号常量`SEEK_SET`为0(绝对偏移量)、`SEEK_CUR`为1(相对与当前位置的偏移量)`SEEK_END`为2(想对于文件尾端的偏移量)**

```c
#include <stdio.h>
#include <unistd.h>
int main(void)
{
    if(lseek(STDIN_FILENO, 0, SEEK_CUR) == -1)
    {
        printf(“cannot seek\n”);
    }
    else {
        printf(“seek OK\n”);
    }
    return 0;
}

[~/dailytest/apue]> ./lseek < /etc/motd
seek OK
┌[xjh021@debian] [/dev/pts/2] 
└[~/dailytest/apue]> cat < /etc/motd |./lseek 
cannot seek
```
通常，文件得当前偏移量应当是一个非负整数，但是，某些设备也可能允许负的偏移量。但对于普通文件，则其偏移量必须是非负值。因为偏移量可能是负值，所以在比较`lseek`的返回值时应当谨慎，不要测试它是否小于0,而要测试它是否等于-1。

## read函数
调用`read`函数从打开文件中读数据
```c
#include <unistd.h>
ssize_t read(int filedes, void *buf, size_t nbytes);
        /*Returns: new file offset if Ok, -1 on error*/
```
如果`read`成功，则返回读到的字节数。如果已达文件结尾，则返回0
有很多情况可使实际读到得字节少于要求读的字节数:
* 读普通文件时，在读到要求字节数之前已达到文件尾端。
* 当从终端设备读时，通常一次最多读一行
* 当从网络读时，网络中缓冲机构可能造成返回值小于所要求读的字节数。
* 当从管道或FIFO读时，如若管道包含得字节少于所需的数量，那么`read`将只返回实际可用的字节。
* 当从某些面向记录得设备(例如磁带)读时，一次最多返回一个记录
* 当某一信号造成中断，而已经读了部分数据量时

## `write`函数
调用write函数向打开的文件写数据
```c
#include <unistd.h>
ssize_t write(int filedes, const void *buf, size_t nbytes);
            /*Returns: number of bytes read, 0 if end of file, -1 on error*/
```
其返回值通常参数`nbytes`的值相同，否则表示出错。`write`出错的一个常见原因：磁盘已满，或者超过了一个给定进程得文件长度限制。
对于普通文件，则每次写操作之前，将文件偏移设置在文件的当前结尾处。在一次成功写之后，该文件偏移量增加实际写的字节数。

## `I/O`效率
程序是用`read`和`write`函数复制一个文件，关于该程序注意下列各点:
* 它从标准输入读，写至标准输出，这就假定在执行本程序之前，这些标准输入、输出已由shell安排好。所用常用得UNIX系统shell都提供一种方法，它在标准输入上打开一个文件用于读，在标准输出上创建(或重写)一个文件。这使得程序不必自行打开输入输出文件
* 很多应用程序假定标准输入是文件描述符0, 标准输出是文件描述符1。本例中在<unistd.h>中定义的两个名字`STDIN_FILENO`、`STDOUT_FILENO`
* 考虑到进程终止时，UNIX系统内核会关闭该进程的所有打开的文件描述符，所以此示例并不会关闭输入输出文件
* 对UNIX系统内核而言，文本文件和二进制文件代码并无区别，所以示例对两种文件都能工作
```c
#include <stdio.h>
#include <unistd.h>
#define BUFFSIZE 4096
int main(void)
{
    int n;
    char buf[BUFFSIZE];
    while((n=read(STDIN_FILENO, buf, BUFFSIZE)) > 0)
    {
        if(write(STDIN_FILENO, buf, n) != n)
        {
            printf("write error\n");
            return -1;
        }
    }
    if(n < 0)
    {
        printf("read error\n");
        return -1;
    }
    return 0;
}
```
系统CPU时间的最小值出现在`BUFFSIZE`为4096处，继续增加缓冲区长度对此时间几乎没有影响。
大多数文件系统为改善其性能都采用某种预读`read ahead`技术，当检测到正在进行顺序读取时，系统就试图读入比应用程序所要求得更多数据，并假想应用程序很快就会读这些数据。

## 文件共享`File Sharing`
Unix操作系统支持文件共享在不同得进程之间，在描述`dup`函数之前，我们先来讲述这种共享。我们需要介绍内核用于所有I/O的数据结构。
内核使用三种数据结构来描述`represent`一个打开的文件，它们之间的关系决定了在文件共享方面一个进程对另一个进程可能产生的影响。
1. 每个进程在进程表里都有一个记录项，记录项中包含有一张打开文件描述符表，可将其视为一个矢量，每个描述符占用一项。(Every process has an entry in the process table. Within each process table entry is a table of open file descriptors, which we can think of as vector, with one entry per descripter.)与每个文件描述符相关连得是：
  * 文件描述符标志`The file descriptor flags`(`close_on_exec`)
  * 指向一个文件表项的指针`A pointer to a file table entry`
2. 内核为所有打开文件维持一张文件表。(The kernel maintains a file table for all open files)。文件表项包含:
  * 文件状态标志(读、写、添加、同步和非阻塞等)`The file status flags for the file, such as read, write, append, sync and nonblocking`
  * 当前文件偏移`The current file offset`
  * 指向该文件v节点表项的指针`A pointer to the v-node table entry for the file`
3. 每个打开文件(或设备)都有一个v节点(v-node)结构。v节点包含了文件类型和对此文件进行各种操作得函数指针。对于大多数文件，v节点还包含了该文件的i节点(i-node, 索引节点)。这些信息是在打开文件时从磁盘上读入内存的，所以所有关于此文件的信息都是快速可供使用的。例如，i节点包含了文件的所有者、文件长度、文件所在的设备、指向文件实际数据块在磁盘上所在位置的指针等等。
**linux没有使用v节点，而是使用了通用i节点结构。虽然两种实现有所不同，但在概念上，v节点与i节点是一样的。两者都指向文件系统特有的i节点结构。**
下图显示了一个进程的三张表之间的关系。该进程有两个不同的打开文件：一个文件打开为标准输入(文件描述符0)，另一个打开为标准输出(文件描述符为1)。从Unix系统的早期以来，这三张表之间的基本关系一直保持至今。这种安排对于在不同进程之间共享文件的方式非常重要。
![打开文件的内核数据结构]({{BASE_PATH}}/image/apue/imag1.png)
如果两个独立进程各自打开同一个文件，则有下图所示,我们假定第一个进程在文件描述符3上打开该文件，而另一个进程则在文件描述符4上打开该文件。打开该文件的每个进程都得到一个文件表项，但对一个给定的文件只有一个v节点表项。每个进程都有自己的文件表项的一个理由是：这种安排使每个进程都有它自己的对该文件的当前偏移量。
给了这些数据结构，我们现在更有必要指出我们上面描述的具体的操作。
* 在完成每个`write`后，在文件表项中的当前文件偏移量即增加所写的字节数。如果这使当前文件偏移量超过了当前文件长度，则在i节点表项中的当前文件长度被设置为当前文件偏移量(也就是该文件加长了)
* 如果用`O_APPEND`标志打开了一个文件，则相应标志也被设置到文件表项的文件状态标志中。每次对这种具有添写标志的文件执行写操作时，在文件表项中的当前文件偏移量首先被设置为i节点表项中的文件长度。这就使得每次写的数据都添加到文件的当前尾端处。
* 若一个文件用`lseek`定位到文件当前的尾端，则文件表项中的当前文件偏移量被设置为i节点表项中的当前文件长度。(这与用`O_APPEND`标志打开文件是不同的)
* `lseek`函数只修改文件表项中的当前文件偏移量，没有进行任何I/O操作
![两个独立进程各自打开同一个文件]({{BASE_PATH}}/image/apue/imag2.png)
可能有多个文件描述符项`file descriptor entry`指向同一个文件表项`file table entry`,在讨论`dup`函数时就可以看到这一点，在`fork`后也会发生同样的情况，此时父、子进程对于每一个打开得文件描述符共享同一个文件表项。
**注意**文件描述符标志`file descriptor flags`和文件状态标志`file status flags`在作用域方面的区别。前者只用于一个进程得描述符，而后者则适用于指向该给定文件表项的任何进程中的所有描述符。本节上面所述的一切对于多个进程读同一个文件都能正确工作。每个进程都有它自己的文件表项，其中也有它自己的当前文件偏移量。但是，当多进程写同一个文件时，则可能产生预期不到的结果。

## 原子操作`Atomic Operations`
**添写至一个文件**`Appending to a File`
UNIX系统提供了一种方法使这种操作成为原子操作，该方法是在打开文件时设置`O_APPEND`标志。正如前一节所述，这就使内核每次对这种文件进行写之前，都将进程的当前偏移量设置到该文件的尾端处，于是在每次写之前就不需要调用`lseek`。
**`pread`和`write`函数**
Single Unix Sepcification包括了XSI扩展，该扩展允许原子性地定位搜索`seek`和执行I/O
```c
#include <unistd.h>
ssize_t pread(int filedes, void *buf, size_t nbytes, off_t offset);
                /*Returns: number of bytes read, 0 if end of file, -1 on error*/
ssize_t pwrite(int fd, const void *buf, size_t nbytes, off_t offset);
                /*Returns: number of bytes writen if OK, -1 on error*/
```
调用`pread`相当于顺序调用`lseek`和`read`,但是`pread`又与这种顺序调用有下列重要区别：
* 调用`pread`时，无法中断其定位和读操作
* 不更新文件指针
调用`pwrite`相当于顺序调用`lseek`和`write`，但也与它们有着类似的区别。

**创建一个文件`Creating a File`**
在对`open`函数的`O_CREAT`和`O_EXCL`选项进行说明时，我们已见到另一个有关原子操作得例子，我们曾提及检查该文件是否存在以及创建该文件两个操作是作为一个原子操作执行的。一般而言，原子操作(atomic operation)指的是由多步组成的操作。如果该操作原子地执行，则要么执行完所有步骤，要么一步也不执行。

## `dup`和`dup2`函数
下面两个函数都可以用来复制一个现存得文件描述符：
```c
#include <unistd.h>
int dup(int filedes);
int dup2(int filedes, int filedes2)
            /*Both return: new file descriptor if OK, -1 on error*/
```
由`dup`返回的新文件描述符一定是当前可用文件描述符中的最小数值`lowest-numbered available file descriptor`。用`dup2`则可以用`filedes2`参数指定新描述符的数值。如果`filedes2`已经打开，则将其关闭，如若`filedes`等于`filedes2`,则`dup2`返回`filedes2`,而不关闭它。
这些函数返回得新文件描述符与参数`filedes`共享一个文件表项。
我们假定进程执行了：`newfd = dup(1);`当此函数开始执行时，假定下一个可用的描述符是3。因为两个描述符指向同一文件表项，所以它们共同享同一文件状态标志`file status flags`(`read, write, append`等)以及同一当前文件偏移量。
每个文件描述符都有它自己得一套文件描述符标志。新描述符的执行时关闭(close-on-exec)标志总是由`dup`函数清除。
![执行dup后的内核数据结构]({{BASE_PATH}}/image/apue/imag3.png)
复制一个描述符的另一种方法是是用`fcntl`函数,调用`dup(filedes)`等效于`fcntl(filedes, F_DUPFD, 0)`而调用`dup2(filedes, filedes2);`等效于`close(filedes2); fcntl(filedes, F_DUPFD, filedes2);`在后一种情况下，`dup2`并不完全等同于`close`。他们之间的区别：
1. `dup2`是一个原子操作，而`close`及`fcntl`则包括两个函数调用。有可能在`close`和`fcntl`之间插入执行信号捕获函数，它可能修改文件描述符。
2. `dup2`和`fcntl`有某些不同的`errno`

## `sync`、`fsync`和`fdatasync`函数

传统UNIX实现在内核中没有缓冲区高速缓冲区或页面高速缓存，大多数磁盘I/O都是通过缓冲进行。当数据写入文件时，内核通常先将该数据复制到其中一个缓冲区中，如果该缓冲区尚未写满，则并不将其排入输出对列，而是等待其写满或者当内核需要重用该缓冲区以便存放其他磁盘块数据时，在将该缓冲排入输出对列，然后待其到达队首时，才进行实际的I/O操作。这种输出方式被称为延迟写`delayed write`。

延迟写减少了磁盘读写次数，但是却降低了文件内容得更新速度，使得欲写到文件中的数据在一段时间内并没有写到磁盘上。当系统发生故障时，这种延迟可能造成文件更新内容得丢失。为了保证磁盘上实际文件系统与缓冲区高速缓存内容的一致性，UNIX系统提供了`sync`、`fsync`和`fdatasync`三个函数。
```c
#include <unistd.h>
int fsync(int filedes);
int fdatasync(int filedes);
            /*Return: 0 if Ok, -1 on error*/
void sync(void);
```
`sync`函数只是将所有修改过的块缓冲区排入写队列，然后就返回，它并不等待实际写磁盘操作结束。通常称为`update`的系统守护进程会周期性的(一般30秒)调用`sync`函数。这就保证了定期冲洗内核的块缓冲区。命令`sync(1)`也调用`sync`函数。
`fsync`函数只对由文件描述符`filedes`指定的单一文件起作用，并且等待写磁盘操作结束，然后返回。`fsync`可用于数据库这样的应用程序，这种应用程序需要确保将修改过的块立即写到磁盘上。
`fdatasync`函数类似于`fsync`,但它只影响文件的数据部分。而除数据外，`fsync`还会同步更新文件的属性。

## `fcntl`函数
`fcntl`函数可以改变已打开文件的性质
```c
#include <fcntl.h>
int fcntl(int filedes, int cmd, .../*int arg*/);
            /*Returns: depends on cmd if Ok (see following), -1 on error*/
```
本节的各实例中，第三个参数总是一个整数，与上面所示函数原型中注释部分相对应。第三个参数则是指向一个结构的指针。
`fcntl`函数有5种功能：
1. 复制一个现有的描述符(`cmd=F_DUPFD`)
2. 获得/设置文件描述符标记(`cmd=F_GETFD`或`F_SETFD`)
3. 获得/设置文件状态标志(`cmd=F_GETFL`或`F_SETFL`)
4. 获得/设置异步I/O所有权(`cmd=F_GETOWN`或`F_SETOWN`)
5. 获得/设置记录锁(`cmd=F_GETLK`、`F_GETLK`或`F_SETLKW`)

我们先说明这10种`cmd`值中的前7种(后三种都与记录锁有关)。我们将涉及进程表项中各文件描述符相关联的文件描述符标志，以及每个文件表项中的文件状态标志。
`F_DUPFD`:  复制文件描述符filedes,新文件描述符作为函数值返回。它是尚未打开的各描述符中大于或等于第三个参数值(取为整型值)中各值的最小值。新描述符与`filedes`共享同一文件表项。但是，新描述符有它自己的一套文件描述符标志，其`FD_CLOEXEC`文件描述符标志被清除(这表示该描述符在通过一个`exec`时仍保持有效)
`F_GETFD`:  对应于`filedes`的文件描述符标志作为函数返回值返回。当前只定义了一个文件描述符标志`FD_CLOEXEC`
`F_SETFD`:  对于`filedes`设置文件描述符标志，新标志值按第三个参数(取整型值)设置。
`F_GETFL`:  对应于filedes的文件状态标志作为函数值返回。
`F_GETOWN`:  取当前接收`SIGIO`和`SIGURG`信号的进程ID和进程组ID.
`F_SETOWN`:  设置接收`SIGIO`和`SIGURG`信号的进程ID和进程组ID。正的`arg`指定一个进程ID，负的`arg`表示等于`arg`绝对值的一个进程组ID
`fcntl`的返回值与命令有关。如果出错，所有命令都返回-1，如果成功则返回某个其他值。下列有四个命令有特定返回值：`F_DUPFD`、`F_GETFD`、`F_GETEL`以及`F_GETOWN`。第一个返回新的文件描述符，接下来的两个返回相应标志位，最后一个返回一个正的进程ID或负的进程组ID。
程序中的第一个参数指定文件描述符，并对于该描述符打印其所选择的文件标志说明。
```c
#include <stdio.h>
#include <fcntl.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    int val;
    if(argc != 2)
    {
        printf("usage: a.out <descriptor>");
        return -1;
    }
    if((val = fcntl(atoi(argv[1]), F_GETFL, 0)) < 0)
    {
        printf("fcntl error for fd %d", atoi(argv[1]));
        return -1;
    }

    switch(val & O_ACCMODE)
    {
        case O_RDONLY:
            printf("read only");
            break;
        case O_WRONLY:
            printf("write only");
            break;
        case O_RDWR:
            printf("read write");
            break;
        default:
            printf("unknown access mode\n");
            return -1;
    }
    if(val & O_APPEND)
    {
        printf(", append");
    }
    if(val & O_NONBLOCK)
    {
        printf(", nonblockiing");
    }
#if defined(O_SYNC)
    if(val & O_SYNC)
        printf(", synchronous writes");
#endif
#if !defined(_POSIX_C_SOURCE) && defined(O_FSYNC)
    if(val & O_FSYNC)
        printf(", synchronous writes");
#endif
    putchar('\n');
    exit(0);
}

λ ~/dailytest/apue/ ./printfile 0 < /dev/tty
read only
λ ~/dailytest/apue/ ./printfile 1 > temp.foo 
λ ~/dailytest/apue/ cat temp.foo 
write only
λ ~/dailytest/apue/ ./printfile 2 2>>temp.foo 
write only, append
λ ~/dailytest/apue/ ./printfile 5 5<>temp.foo 
read write
```
子句`5<>temp.foo`表示在文件描述符5上打开文件`temp.foo`以供读和写

## `ioctl`函数
`ioctl`函数是I/O操作的杂货箱。不能用本章中其他函数表示的I/O操作通常都能用`ioctl`表示。终端I/O是`ioctl`的最大使用方面。
```c
#include <unistd.h>    /*System V*/
#include <sys/ioctl.h>   /*BSD an Linux*/
#nclude <stropts.h>     /*XSI STREAMS*/
int ioctl(int filedes, int request, ...);
            /*Returns: -1 on error, something else if OK*/
```
在此原型中，我们表示的只是`ioctl`函数本身所要求的头文件。通常，还要求另外的设备专用头文件。例如，除POSIX.1所说明的基本操作之外，终端I/O的`ioctl`命令都需要头文件`<termios.h>`
每个设备驱动程序都可以定义它自己专用的一组`ioctl`命令。系统则为不同种类的设备提供通用的`ioctl`命令。

## `/dev/fd`
较新的系统都提供名为`/dev/fd`目录，其目录项`entries`是名为0, 1, 2等的文件。打开文件`/dev/fd/n`等效于复制描述符n(假定描述符n是打开的)。
在下列函数调用中：
`fd = open(“/dev/fd/0”, mode);`

大多数系统忽略它所指定的`mode`，而另外一些则要求`mode`必须是所涉及的文件(在这里是标准输入)原先打开时所用`mode`的子集。因为上面的打开等效于：
`fd = dup(0);`




