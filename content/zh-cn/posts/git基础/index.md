---
title: "Git基础"
date: 2015-05-08
categories: 
  - "软件工具"
---

**如何开始用Git呢？**

? ? ?可以通过命令行和界面使用Git，但推荐命令行。掌握了命令行，界面也就基本掌握了，反之就不行。

  

**如何创建一个Git仓库呢？**

? ? ?有两种方式：

? ? ?第一种针对本地的某个项目，还未被Git记录过，那么cd到对应项目的根目录下，执行

? ? ?$ git init

? ? ?然后执行

? ? ?$ git add \*.c

? ? ?$ git add LICENSE

? ? ?$ git commit -m 'initial project version'

? ? ?后面三句先不解释含义，后面会逐步说明。只说明第一句git init，表示在当前目录下，生成.git仓库来记录该目录下的项目。

? ? ?第二种针对远端的某个项目，有对应项目的公开链接和权限，执行

? ? ?$ git clone?[https://github.com/libgit2/libgit2](https://github.com/libgit2/libgit2)

? ? ?这句命令会在当前目录新建一个名为libgit2的目录，并在其中放置远端同步过来的项目。如果想让目录叫别的名字，执行

? ? ?$ git clone?[https://github.com/libgit2/libgit2](https://github.com/libgit2/libgit2)?mylibgit

? ? ?会让目录叫mylibgit。

  

**如何确定成功初始化了一个Git仓库呢？**

? ? ?进入对应项目跟目录下，执行

? ? ?$ git status

? ? ?如果返回On branch master之类的信息，则表明初始化成功。

  

**如何开始用Git记录文件？**

? ? ?Git能够识别目录中的文件是已记录（tracked）还是未记录（untracked）。开始记录某个文件，需执行

? ? ?$ git add <filename>

? ? ?执行这句命令会将未记录的文件放入跟进区，文件变为已记录并且处于已跟进状态，等待被提交。

  

**如何跟进已修改的文件？**

? ? ?如果一个已记录的文件被修改了，那么跟进的部分仍旧是未修改前的，要想让修改被跟进，那么也需要执行

? ? ?$ git add <filename>

  

**如何查看已记录文件的修改后未跟进与已跟进之间的差异？**

? ? ?执行

? ? ?$ git diff

  

**如何查看已记录文件已跟进部分与前一次提交文件之间的差异？**

? ? ?执行

? ? ?$ git diff --staged

? ? ?或者执行

? ? ?$ git diff --cached

  

**当执行git status时候，Git总把一些不相关的文件列在未记录的文件中，怎么样能够不看到这些文件，这些文件都不会被记录？**

? ? ?Git提供了配置忽略文件（Ignoring Files）的方法，即配置.gitignore文件。该文件的每一行为一项，#开头的行会被当作注释。一行一般被用来配置要屏蔽的某一类文件，语法上支持通配符。[https://github.com/github/gitignore](https://github.com/github/gitignore)中有很多很好的例子可以做参考。配置好的.gitignore文件放在项目根目录下即可。

  

**如何提交修改？**

? ? ?执行

? ? ?$ git commit

? ? ?会弹出编辑器让编辑提交的信息。或者直接用-m，即

? ? ?$ git commit -m 'this is the message'

? ? ?需要注意的是提交的修改都必须是已跟进的内容，如果想跳过跟进的步骤，直接加入-a选项，即

? ? ?$ git commit -a -m 'this is the message'

? ? ?不过需要明白的一点是，这句命令中被提交的都是已经有过记录的文件，如果是未记录文件，那么是无法提交的。

? ? ?至此git init部分的后三句命令应该都清楚含义了。

  

**如何删除一个文件呢？**

? ? ?可以执行

? ? ?$ rm <filename>

? ? ?这种方式仅会从磁盘上删除掉文件，但删除的操作并未被提交，还需要手动跟进提交一次。更容易的方法是执行

? ? ?$ git rm <filename>

? ? ?会直接删除文件并自动做跟进。

  

**如果只是想不跟进这个文件，并不像从磁盘上删除该怎么办？**

? ? ?$ git rm --cached <filename>

  

**怎么对跟进的文件重命名呢？**

? ? ?$ git mv <before\_name> <after\_name>

? ? ?等价于执行

? ? ?$ mv <before\_name> <after\_name>

? ? ?$ git rm <before\_name>

? ? ?$ git add <after\_name>

  

**提交的流程已经清楚，但要想查看提交历史怎么办？**

? ? ?$ git log

  

**?执行这条命令仅能够看到每次提交的校验码、作者、时间、修改的信息，怎么能够看到修改的概况？**

? ? ?$ git log --stat

  

**这条命令确实可以看到每次提交删除和新增的概况，不过要看具体的差异怎么办？**

? ? ?$ git log -p

  

**要是只想看最近两次的提交呢？**

? ? ?$ git log -2

  

**提交历史虽然是按照时间的倒序排列的，但似乎无法知道各个提交之间的继承关系？**

? ? ?$ git log --graph

? ? ?实际上git log还有查找特定字段--grep，自定义输出格式--pretty，显示单行--oneline等等一些更加复杂的命令，具体情况可参考[Git的帮助手册](http://git-scm.com/docs)。

  

**有时候查看提交历史后才想起来，有些文件忘记提交了，想覆盖最后一次提交的历史怎么办？**

? ? ?$ git commit --amend

? ? ?执行这句命令后，可以重新提交并覆盖最后一次的提交历史，实际上等了解了rebase后可以随意修改自己提交的历史。

  

**有时候提交时错误跟进了一些文件，怎么样能够取消跟进？**

? ? ?$ git reset HEAD <file>

  

**有时候想直接放弃某个文件的修改怎么办？**

? ? ?$ git checkout -- <file>

  

**到目前似乎都只是在讲本地的一些操作，怎么样能够知道远端仓库的信息？**

? ? ?$ git remote

? ? ?可以显示有哪些远端的分支

? ? ?$ git remote -v

? ? ?可以显示分支的具体URL，关于分支的概念，在Git分支中会有更详细的说明。

  

**如果有多个远端仓库，怎么添加？**

? ? ?$ git remote add <branch\_name> <url>

  

**如何获取添加的远端分支的代码？**

? ? ?$ git fetch <branch\_name>

? ? ?这个操作，仅仅将远端代码同步到本地，不能编辑和修改，也不会自动并入到本地的任何一个分支。如果有某个本地分支在跟踪这个远程分支，直接切换到相应分支，执行

? ? ?$ git pull

? ? ?或者

? ? ?$ git pull -r

? ? ?-r表示以rebase的方式并入本地分支，主要是将远端的提交记录与本地的提交做次序上的先后调整。

  

**如何将本地的代码推送到远端分支？**

? ? ?$ git push <remote\_branch> <local\_branch>

? ? ?或者当前的分支跟踪了远程的分支，直接执行

? ? ?$ git push

  

**前面显示远端分支时候最多就是名字和URL，怎么显示更加详细的信息？**

? ? ?$ git remote show <remote\_branch>

  

**怎么对远端分支重命名？**

? ? ?$ git remote rename <old\_name> <new\_name>

  

**怎么删除一个远端分支？**

? ? ?$ git remote rm <remote\_branch>

  

**打标签是记录软件里程碑的重要功能，Git是怎么支持这个特性的？**

? ? ?$ git tag

? ? ?可以查看已经打过的标签。标签分含附注的标签和轻量级的标签，打含附注标签的命令是

? ? ?$ git tag -a <tag\_version> -m <tag\_message>

? ? ?打轻量级的标签的命令是

? ? ?$ git tag <tag\_version>

? ? ?查看某个标签的具体情况的命令是

? ? ?$ git show <tag\_version>

? ? ?以上打标签的方法都是针对最新一次提交，对历史的某次提交打标签，只需要指定上检验码就好了。另外这些标签在本地打完，并不会被push命令推送到远端，除非显式的指出

? ? ?$ git push <remote\_brach> <tag\_version>

  

**Git中有些命令似乎很长，但又用得很频繁，有没有办法可以简化？**

? ? ?Git提供了异名机制，可以自定义命令，例如

? ? ?$ git config --global alias.co checkout

? ? ?$ git config --global alias.br branch

? ? ?$ git config --global alias.ci commit

? ? ?$ git config --global alias.st status

? ? ?$ git config --global alias.unstage 'reset HEAD --'

? ? ?$ git config --global alias.last 'log -1 HEAD'

  

这段文字改写自《Pro Git》一书的第二版第二章Basics部分。这里的改写仅用作回顾，不推荐直接学习，原书中有很多图示的说明更加直观。第一章请参考[Git初识](http://mjm1990.com/?p=112)。
