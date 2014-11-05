---
layout: post
title: 搭建git服务器
date: 2014-11-1
category: git
tags: [git]
---
参照[廖雪峰git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)搭建git服务器，作为笔记。
<!--more-->

服务器使用的是redhat6，安装git时折腾了一会儿，忘记具体情况了。
1. 创建一个`git`用户
```
$sudo adduser git
```
可以开始时创建密码方便登录，创建目录文件不用修改所有者,虽然后面会禁用掉git用户的shell登录权限
```
$sudo passwd git
```
2. 可以切换到git用户，初始化Git仓库：
```
$git init --bare sample.git
```
Git就会创建一个裸仓，裸仓没有工作树，服务器上的Git仓库纯粹是为了共享，如果不是`git`用户创建的仓库需要修改`owner`
```
$sudo chown -R git:git sample.git
```
3. 创建证书登录：
收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。
我这里出现了问题是每次连接git服务器都需要密码，查了许多原因
  * 更改`authorized_keys`文件权限为`600`或`644`
  * 通过`ssh-keygen -t rsa`重新生成本地密钥后上传`id_rsa.pub`
  * 但是重新生成ssh-keys后需执行`ssh-add ~/.ssh/id_rsa`
4. 禁用shell登录：
编辑`/etc/passed`文件将
```
git:x:1001:1001:,,,:/home/git:/bin/bash
```
改为
```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```
6. 克隆远程仓库：
可以在自己的电脑克隆远程仓库
```
$git clone git@server:/sample.git
```
