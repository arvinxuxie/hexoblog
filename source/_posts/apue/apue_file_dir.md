---
layout: post
title: apue files and directories
date: 2014-10-20
category: c/c++
tags: [apue, c]
---
上一章我们说明了执行I/O操作的基本函数，其中的讨论是围绕普通文件I/O进行的——打开一个文件，读或写一个文件。本张将描述文件系统的其他特征和文件性质。
<!--more-->

## `stat`、`fstat`和`lstat`函数
本章讨论的中心是四个`stat`函数以及它们所返回的信息
```c
#include <sys/stat.h>
int stat(const char *restrict pathname, struct stat *restrict buf);
int fstat(int filedes, struct stat *buf);
int lstat(const char *restrict pathname, struct stat *restrict buf);
int fstatat(int fd, const char *restrict pathname, struct stat *restrict buf, int flag);
                /*All four return: 0 if OK, -1 on error*/
```
一旦给出`pathname, stat`函数就返回与此命名文件有关的信息结构。`fstat`函数获取已在描述符`filedes`上打开文件有关信息。`lstat`函数类似于`stat`,但是当命名的文件是一个符号链接时，`lstat`返回该符号链接的有关信息，而不是由该符号链接引用文件的信息。第二个参数是`buf`指针，它指向一个我们必须提供的结构。这些函数填写由buf指向的结构。该结构的实际定义可能随实现有所不同，但其基本形式是：
```c
struct stat{
mode_t st_mode;     /*file type & mode (permissons)*/
ino_t st_ino;       /*i-node number (serial number)*/
dev_t st_dev;       /*device number (file system)*/
dev_t st_rdev;      /*device number for special files*/
nlink_t st_nlink;    /*number of links*/
uid_t st_uid;        /*user ID of owner*/
gid_t st_gid;       /*group ID of owner*/
off_t st_size;          /*size in bytes, for regular files*/
time_t st_atime;    /*time of last access*/
time_t st_mtime;        /*time of last modification*/
time_t st_ctime;        /*time of last file status change*/
blksize_t st_blksize;       /*best I/O block size*/
blkcnt_t st_blocks;         /*number of disk blocks allocated*/
};
```

## 文件类型`File Types`
文件类型包括如下几种：
1. 普通文件`regular file`: 这是最常用的文件类型，这种文件包含了某种形式的数据。至于这种数据是文本还是二进制数据对于UNIX内核而言并无区别。对普通文件内容的解释由处理该文件的应用程序进行。
**一个值得注意的例外是二进制文件。为了执行程序，内核必须理解其格式。所有二进制可执行文件都遵循一种格式，这种格式使内核能够确定程序文本和数据的加载位置。**
2. 目录文件`directory file`: 这种文件包含了其他文件的名字以及指向与这些文件有关信息的指针。对一个目录文件具有读取权限的任一进程都可以读该目录的内容，但只有内核可以直接写目录文件。进程必须使用本章说明的函数才能更改目录。
3. 块特殊文件`block special file`:  这种文件类型提供对设备(例如磁盘)带缓冲的访问，每次访问以固定长度为单位进行。
4. 字符特殊文件`character special file`:  这种文件类型提供对设备不带缓冲的访问，每次访问长度以固定长度为单位进行。
5. `FIFO`:  这种类型文件用于进程间通信，有时也将其称为命名管道`named pipe`
6. 套接字`socket`:  这种文件类型用于进程间网络通信。套接字也可以用于在一台宿主机上进程之间的非网络通信。
7. 符号链接`symbolic link`:  这种文件类型指向另一个文件。

文件类型信息包含在`stat`结构的`st_mode`成员中
![<sys/stat.h>中的文件类型宏]({{BAST_PATH}}/image/apue/imag4.png)

POSIX.1允许实现将进程间通信`interprocess communic`(IPC)
