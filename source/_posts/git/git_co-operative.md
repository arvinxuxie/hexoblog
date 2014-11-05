---
layout: post
title: git多人协作
date: 2014-11-1
category: git
tags: [git]
---
本文参照[廖雪峰Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013760174128707b935b0be6fc4fc6ace66c4f15618f8d000)廖老师教程写的很简明，自己希望通过笔记快速掌握git一些基本操作，方便工作。关于其中的原理有时间在慢慢研究。
<!--more-->
从远程仓库克隆时，实际Git自动把本地`master`分支和远程的`master`分支对应起来，可查看
```
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```
**推送分支**
就是把该分支上的所有本地提交推送到远程仓库，推送时要指定本地分支
```
$ git push origin master
```
并不是一定要把本地分支往远程推送
* `master`分支是主分支，因此时刻与远程同步;
* `dev`分支是开发分支，团队所有成员都需要在上面工作，也需要远程同步;
* `bug`分支只用于本地修复bug，没有必要远程同步;
* `feature`分支是否推送到远程，取决于你是否和你的伙伴合作在上面开发;

**抓取分支**
多人协作时，大家都会往`master`和`dev`分支上推送各自的修改。
当你的小伙伴从远程库clone时，默认情况下只能看到`master`分支
```
$ git branch
* master
```
现在，如果要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，这个命令创建本地`dev`分支:
```
$ git checkout -b dev origin/dev
Switched to a new branch 'origin/dev'
```
现在，可以在`dev`上修改，然后时不时的把`dev`分支`push`到远程
```
$ git commit -m "add readme file"
$ git push origin dev
```
如果`git push`失败`error: src refspec dev does not match any.`可能是没有指定本地`dev`分支与远程`origin/dev`分支的链接。建立链接后再push
```
$git branch --set-upstream dev origin/dev
$ git push origin dev
```
如果遇到冲突了，我们先用`git pull`把最新的`origin/dev`抓下来，然后本地合并，解决冲突再推送:
```
$git pull
```
如果`git pull`失败了，原因是没有指定`dev`分支与远程分支`origin/dev`分支的链接，可以设立链接同上
```
$ git branch --set-upstream dev origin/dev
Branch dev set up to track remote branch dev from origin.
```
如果`git pull`成功但没有合并冲突，需要手动解决，解决方法和分支管理中的[解决冲突]()完全一样,解决后提交，再`push`。
```
$git commit -m “merge & fix readme”
$git push origin dev
```


**多人工作模式是这样**:
1. 首先用`git push origin branch-name`推送自己的修改;
2. 推送失败，先查看是否建立链接，否则是远程分支比你的本地分支更新，先用`git pull`合并;
3. 如果合并有冲突，则解决冲突，并在本地提交;
4. 冲突解决后，再用`git push origin branch-name`推送就能成功;

如果`git pull`提示`no tracking information`用`git branch --set-upstream branch-name origin/branch-name`建立链接。
