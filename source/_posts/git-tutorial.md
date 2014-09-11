---
本文参照[git 是什么](http://blog.csdn.net/jackystudio/article/details/12249303)和[Git - Tutorial](http://www.vogella.com/tutorials/Git/article.html), [Git Manual Page](https://www.kernel.org/pub/software/scm/git/docs/)
[git-scm](http://git-scm.com/)
# Git
## 本地版本仓库和操作(local repository and operations)
当克隆或是新建一个仓库后用户就有一个完整的仓库拷贝。
## 远程仓库
Git允许用户本地仓库和远程仓库的同步(synchronize)。用户可以用`push`命令把本地的改变同步到远程仓库，也可以用`pull`命令将其他仓库的改变同步到本地仓库。
## 分支和合并(Branching and merging)
## 工作树 (Working tree)

![直接上张工作图吧]({{BASE_PATH}}/image/git-tutorial/img1.jpg)
##术语(Terminology)
1. **分支(Branches)**
一个分支
分支可以称为指向提交版本的指针，选择一个分支在git术语中称为`checkout a branch`。当你工作在特定分支下，一个新的提交使这个指针指向新的版本。一个分支意味着它是一个独立拥有自己历史版本的代码线。每个提交的知道它的父指针。一个分支默认的名称是**master**。

2. **提交**(Commit)
当你提交改变到仓库里，会在Git仓库创建一个新的提交对象。这个提交对象会有一个新版的唯一标识。这个版本可以被检索，如果你想要看老版本的源代码。每个提交对象包含提交者和修改者，这样可以确定谁做了变更。

3. **头**(HEAD)
头是一个标识性参照,常常指向当前选择分支

4. **索引**(Index)
索引是暂存区`staging area`的另一种术语。

5. **仓库**(Repository)
仓库包含了随着时间推移的不同版本、不同分支和不同标签的历史。在Git中每一个仓库的副本都是一个完整的信息库。如果该仓库不是纯仓库`bare repository`,你可以在你的工作树选取任意一个版本。

6. **修订**(Revision)
修订代表源代码的一个版本，Git用提交对象实现修订。Git通过SHA-1算法生成的ID来标志不同的版本，SHA-1 ID是160位长的16进制标识的字符串。

7. **暂存区**(Staging area)
暂存区是可以用来保存在工作树中的改变在提交之前。暂存区包含工作树中改变(changed or new files)的快照`snapshot`，用于生成下一次提交存储它们的模型(file type, executable bit)

8. **标识**(Tags)
标识指的是某个分支某个特定时间点的状态，通过标识可以很方便的切换到标识时的状态。

9. **URL**
决定仓库所在位置

10. **工作树/区**(Working tree)
工作树包含仓库工作文件的集合，你可以改变内容并作为新的提交到仓库中。

![工作区，暂存区和版本库的关系]({{BASE_PATH}}/image/git-tutorial/imag2.jpg)

**文件状态**
一个文件在Git仓库中的工作树中可以有以下状态。
* untrack: 文件没有被Git仓库追踪到，也表示没有被添加到暂存区`staged`。i.e. 加到暂存区没有被提交
* tacked: 提交了但没有被添加到暂存区`staged`
* staged: 添加到暂存区用于下一次的提交
* dirty/modified: 文件已被修改但改变没有添加到暂存区

## Git 安装
Debian下是
```
sudo apt-get install git
```
设置用户名和Email
```
$ git config --global user.name “Your name”
$ git config --global user.email “YourEmail@example.com”
```
--global表示这台机器上所有的仓库都使用此配置。
如果希望对单个仓库使用不一样的配置，修改`.git/config`
```
[user]
    name = Yourname
    email = YourEmail@example.com
```
推送配置(Push configuration)
```
#set default so that only the current branch is pushed
git config --global push.default simple
#alternatively configure Git to push all matching branches
git config --global push.default matching
```
拉取版本时阻止合并提交(Avoid merge commits for pulling)
如果你从远程仓库拉取改变内容，Git默认创建合并如果拉取的不同。
```
#set default so that you avoid unnecesary commits
git config --global branch.autosetuprebase always
```

语法高亮Color Highligting
```
git config --global color.ui true
git config --global color.status auto
git config --global color.branch auto
```
设置默认编辑器
```
#set vim as default editor for Git
git config --global core.editor vim
```

设置默认的合并工具
```
#setup kdiff3 as default merage tool(linux)
git config --global merge.tool kdiff3
```
[更多的设置](https://www.kernel.org/pub/software/scm/git/docs/git-config.html)

**查询Git的设置**
查询本地仓库设置
```
git config --list
```
查询全局设置
```
git config --global --list
```
## 设置规则忽略文件或是目录
创建一个`.gitignore`在你的仓库里
```
#Create a ~/.gitignore in your user directory
cd ~/
touch .gitignore
#Exclude bin and .metadata directories
echo “bin” >> .gitignore
echo “.metadata” >> .gitignore
#ignore all files ending with ~
echo “*~” >> .gitignore
#Configure Git to use this file
#as global .gitignore
git config --global core.excludesfiles ~/.gitignore
```
**可以创建本地仓库忽略规则**
编辑`.git/info/exclude`文件，这些规则不会提交仓库中，它们不会分享给其他人。

# Git开始
创建Git仓库
```
git init
```
每一个Git仓库存储在`.git`文件目录里，这个目录包括完整的仓库历史，`.git/config`文件包含仓库的配置信息。

生成内容
```
$touch datafiles/data.txt
$ls > test01
$echo “bar” > test02
$echo “foo” > test03
```

查看当前目录状态
```
debian:~/dailytest/git % git status                                                                        (master)* [11:54PM]
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use “git add <file>...” to include in what will be committed)
#
#   datafiles/
#   test01
#   test02
#   test03
nothing added to commit but untracked files present (use “git add” to track)
</file>)
```

添加文件到暂存区`staging area`
在提交改变到Git仓库中，你需要标记那些需要被提交的文件。这一步添加新文件或是改动的文件到暂存区，创建一个快照对于那些涉及到的文件。
Note： 当你改变一个文件时需要将它添加到暂存区后再提交
```
# add all files to the index of the git respository
$ git add .
```

**提交到git仓库中**
```
#commit your file to the local repository
git commit -m “Initial commit”
```
### 查看结果
上面的操作已经在`.git`文件中建立了本地Git仓库，提交后将所有文件增加到仓库中。
```
#show the Git log for the change
$ git log

commit 9ead0f9ac850a553e28e667babe2a15f4aca1bdc
Author: yourname <yourname@localhost.localdomain>
Date:   Wed Sep 10 09:15:55 2014 +0800

    Initial commit
```
### 删除文件及相应的提交
```
# create a file and commit it
touch nonsense2.txt
git add .
git commit -m “more nonsense”

#remore the file and record the deletion in Git
git rm nonsense2.txt
#commit the removal
git commit -m “Removes nonsense2.txt file”
```
另一种删除方法利用`-a`或`-A`参数，这个标志使文件的改动被Git仓库知道，包括删除信息被提交的暂存区。
```
% rm nonsense.txt
% git add .                                                                         (master)* [ 9:44PM]
# On branch master
# Changes not staged for commit:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	deleted:    nosense.txt
#
no changes added to commit (use "git add" and/or "git commit -a")

# commit the remove with the -a flag
% git commit -a -m “File nonsense.txt is now removed”
```

**删除暂存区文件**
```
# create a file and add to staging area
touch unwantedstaged.txt
git add unwantedstaged.txt

#remove it from the staging area
git reset unwantedstaged.txt

#to cleanup , delete it
rm unwantedstaged.txt
```

**正确最后提交的修补**
`git --amend`并推送一个git仓库的分支，只是基于已经存在的提交改变的一个`commit ID`
```
# assuming you have something to commit
git commit -m “message with a tpyo here”

git commit --amend -m “More changes -now correct”
```

**移除文件基于`.gitignore`的变化**
```
# remove dirctory .metadata from gi repo
git rm -r --cached .metadata
# remove file test.txt from repo
git rm --cached test.txt
```
## 远程仓库(remote repositories)
远程仓库用来把Gitv仓库托管到互联网，本地网或是本机中
```
#1点表示当前目录，2个点表示父目录
$ git clone --bare . ../remote-repository.git
Cloning into bare repository '../remote-repository.git'...
done.
```
父目录下有个文件夹`remote-repository.git`里包含了原来库中的.git文件夹。
一个标准的git仓库包含了源代码和历史信息，我们可以在上面直接修改代码，但是远程仓库只有历史信息，并没有源代码。

**添加远程仓库**
我们可以用`URL`来表示一个远程仓库，但推送很麻烦，可以在添加远程仓库的时候可以取个名字。
```
# add ../remote-repository.git with the name origin
git remote add origin ../renote-repository.git
```

**同步远程仓库**
```
#do some changes
echo “I added a remote repo” > test02
git commit -a -m “This is a test for the new remote origin”

git push origin
```

**显示存在的远程仓库**
```
# show the details of the remote repo called origin
git remote show origin
```

显示远程仓库的详细信息
```
#show the existing defined remotes
git remote
#show details about the remotes
git remote -v
```
## 克隆远程仓库推送和拉取
更多参看[Git push manpage](https://www.kernel.org/pub/software/scm/git/docs/git-push.html)
克隆你的仓库
```
mkdir repo02
cd repo02
git clone ../remote-repository.git
```

推送改变到另一个仓库
```
# make some changes in the first repository
cd git
# make some changes in the file
echo “Hello, hello. Turn your radio on” > test01
echo “Bye, bye. Turn your radio off” > test02t
git commit -a -m “Some changes”
#push the changes
git push ../remote-repository.git
```
**拉取改变(Pull changes)**
```
# switch to second directory
cd ../repo2
# pull in the latest changes of your remote repository
git pull
# make changes
echo “A change” > test01
git commit -a -m “A change”
#push changes to remote repository
#origin is automatically created as we cloned original from this repository
git push origin
```
你可以拉取改变在第一个仓库
```
cd ../git
git pull ../remote-repository.git/
#check the changes
git status
```

**在线远程仓库**(Online remote repositories)
Git支持一些端口协议去连接其他Git仓库，基本的是git协议
Git协议默认端口是`9148`
```
# switch to a new directory
mkdir online
cd online
# clone online repository
git clone git://github.com/vogella/gitbook.git
```
可以用`ssh`协议去连接Git仓库
```
# clone online reposity
git clone ssh://git@github.com/vogella/gitbook.git
```
可以通过`http`协议去克隆远程仓库
```
# the following will clone via HTTP
git clone http://github.com/vogella/gitbook.git
```
**添加更多远程仓库**
你可以推送改变到`origin repository`通过`git push`，`origin`是默认的远程仓库。你可以添加新的远程仓库
```
# add the HTTPS protocol
git remote add github_http https://vogella@github.com/vogella/gitbook.git
```
远程仓库改名
```
git remote rename github_http github_testing
```

**远程操作通过HTTP和代理**
看不懂日后整理
```
export http_proxy=http://proxy:8080
export https_proxy=https://proxy:8443

git clone http://dev.eclipse.org/git/org.eclipse.jface/org.eclipse.jface.snippets.git
```
git的配置设置
```
# set proxy for git globally
git config --global http.proxy http://proxy:8080
# to check the proxy settings
git config --get http.proxy
# just in case you need to you can also revoke the proxy settings
git config --global --unset http.proxy
```

## 分支(branches)
列出分支变量
```
# lists available branches
git branch

# list all branches including the remote branches
git branch -a

# list branches and tags in the remote repository called origin
git ls-remote origin
```
创建一个新的分支
```
# syntax : git branch <name> <hash>
# <hash> in the above is optional
git branch testing
# create branch and switch to it
git checkout -b bugreport12
# creates a new branch based on the master branch
# without the last commit
git checkout -b mybranch master~1
```

**选择分支**(Checkout branch)
开始在一个分支工作时你需要去选取一个分支，你选取`checkout`一个分支，头指针`HEAD pointer`会移动这个分支最后一次提交，
```
# switch to your new branch
git checkout testing
#do some changes
echo “Cool new feature in this branch” > test01
git commit -a -m “new feature”
#switch to the master branch
git checkout master
# check that content of the test01 file is the old one
cat test01
```

重命名分支
```
# rename branch
git branch -m [old_name] [new_name]
```
删除分支
```
#delete branch testing
git branch -d testing
#force delete testing
git branch -D testing
#check if branch has been deleted
git branch
```
**推送一个分支的改变到远程仓库**
你可以在当前分支推送改变到远程仓库目标分支，如果目标不存在此分支则创建此分支
```
# push current branch to a branch called “testing” to remote repository
git push origin testing
# switch to the testing branch
git checkout testing
#some changes
echo “News for you” > test01
git commit -a -m “new feature in branch”
#git push all includiing brach
git push
```

比较分支间的不同
```
# show the differences between
# current head of master and testing
git diff master testing
```

## 标签(Tags in Git)
Git支持两种不同类型的标签，轻量级`lightweight`和带解释型`annotated`的标签
轻量级标签`lightweight tag`是一个指向提交的指针没有附加其它信息。
带解释型标签`annotated tag`包含一些关于标签的信息，例如`name`和`email`
```
# list tags
git tag

# search by pattern for a tag
git tag -l <pattern>

# Creating lightweight tags
git tag 1.7.1
# see the tag
git show 1.7.1

# Create tag
git tag 1.6.1 -m ‘Release 1.6.1’
# show the tag
git show 1.6.1

# you can alse creat tags for a certain commit id
git tag 1.5.1 -m ‘version 1.5’ [commit id]

#选取标签
git checkout <tag_name>

#push a tag or branch called tagname
git push origin [tagname]

# to explicitly push a tag and not a branch
git push origin tag <tagname>

# delete tag locally
git tag -d 1.7.0

# delete tag in remote repository
# called origin
git push origin : refs/tags/1.7.0
```

## 分析仓库的的改变
查看工作区的变化
```
# see the current status of your repository
# (which files are changed / new /deleted)
git status
```

使用Git Log
```
# show the history of commits in the current branch
git log

# uses shortend but unique SHA-1 values for the commit objects
git log --abbrev-commit

#show the history of commits in one line with a shortend version of the commit id
#--online is a shortend for “--pretty=oneline --abbrev-commit”
git log --oneline

# show the history as graph includiing branches
git log --graph --oneline

# see all commits of a certain user
git log --author=lvogel
```

查看`unstaged changes`没有提交到暂存区的和最后一次提交的改变
```
echo “This is a change” > test01
echo “and this is another change” > test02
git diff
git diff [path]
```

比较暂存区和最后一次提交的不同
```
# make some changes to the file
git diff --cached
```

显示指定版本的改变
```
git show <commit_id>
```

查看两次提交的不同
```
git diff HEAD~1 HEAD
```

查看提交时文件的改变，`name-only`显示`only the names of the files`
```
git diff-tree --name-only -r <commit_id>
```

显示文件中每一行的改变
```
# git blame shows the author and commit per line of a file
git blame [filename]

# the -L option allows to limit the selection
# for example by line number

# only show line 1 and 2 in git blame
git blame -L 1,2 [filename]
```

git shortlog命令`-s` 显示提交次数，`-n`根据作者提交次数排序
```
# gives a summary of the changes by author
git shortlog
# compressed summary
git shortlog -sn
```

**贮存提交的改变**(Stashing committed changes with git stash)
Git提供`git stash`命令允许你记录当前工作目录的状态，贮藏区域`staging area`及回溯到最后提交的修订版本
```
# create a stash with uncommitted changes
git stash

# do changes to the source, e.g. by pulling
# new changes from a remote repo
# afterwards, re-apply the stashed changes
# and delete the stash from the list of stashes
git stash pop
```


