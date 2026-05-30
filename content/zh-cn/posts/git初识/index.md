---
title: "Git初识"
date: 2015-05-06
categories:
  - "engineering"
tags:
  - "technique"
---

##### 为什么要进行版本控制？

我们需要记录对一些文档的编辑历史，特别是当多人合作编辑同一个文档时。有了这些编辑历史的记录，不仅能够方便查看更改，同时也能够回滚到过去的某个版本。

##### 版本控制系统有哪些应用？

版本控制多用在代码管理中，但也有其他的应用，比如Google Docs、Wikipedia History等。

##### 版本控制系统经历了怎样的演进？

简单说是从本地化版本控制系统（Local Version Control System）到中心化版本控制系统（Centralized Version Control System），再到分布式版本控制系统（Distributed Version Control System）。

最初的版本控制用于本地的文档管理，通过一个数据库来记录修改前后的差异，被称作本地化版本控制系统（Local Version Control System, LVCS）。这种系统无法支持多人间的协作，进而产生了中心化版本控制系统（Centralized Version Control System, CVCS），将版本管理的数据库移到服务器端。CVCS中，客户端程序都只能够与服务器端进行同步，一旦服务器出现故障，所有的客户端程序都无法使用，服务器端的存储结构和历史记录也都可能丢失。分布式版本控制系统（Distributed Version Control System, DVCS），通过在每个客户端都克隆一个完整的服务器存储结构，从而解决了前面的问题。

##### 每种不同的版本控制系统有哪些代表？

- LVCS：RCS
- CVCS：CSV, Subversion, Perforce
- DVCS：Git, Mercurial, Bazaar or Darcs

##### 分布式的版本控制系统是否等于本地化的版本控制系统加上中心化的版本控制系统？

功能上可以这样讲，但实现逻辑有着很大的不同。（**怎样的实现逻辑呢？**）不管是本地化还是中心化的VCS，主要记录的是修改文件之间的差异（又称补丁，differences or patches）。若要获得某个版本，就等于将很多的差异文件相加。分布式的VCS，记录的是每次提交的完整文件，常被称作快照（snapshots）。（**这两种方式各自有哪些优缺点呢？**）记录差异的优点在于磁盘空间消耗小，但获得不同的版本需要较多的运算。记录快照的优点在于获得不同的版本与对比版本差异计算都很快，缺点在于磁盘空间消耗大。由于磁盘空间现如今都不会是瓶颈，所以这个缺点基本可以忽略。分布式的版本控制系统带来的好处更多，所以越来越流行，特别是Git。

##### Git是怎样记录文件的？

Git用SHA-1算法产生的40位长的校验码（checksum）记录所有的文件。具体讲是对每个被提交过的文件执行SHA-1算法，生成校验码作为该文件的名称或者身份，Git主要管理这些校验码。实际上不仅每个文件被执行SHA-1算法，每次的提交（commit）操作也会生成一个校验码，所以只要是提交过的内容，都有办法进行恢复。

##### Git的基本结构是怎样的？

Git管理的文件会有三种状态：已提交（committed）、已修改（modified）、已跟进（staged）。已提交是指文件已经被保存在了本地的仓库中。已修改是指已提交过的文件有了新的修改但还没有提交。已跟进是准备提交前的一种状态。对应于三种状态，Git内部被分为三个区域，本地工作区（Working Directory）、跟进区又称缓存区（Staging Area）、本地仓库（Git directory）。已提交的文件记录在了本地仓库，已修改的文件仅位于本地工作区，而已跟进的文件位于跟进区。本地工作区是我们工作交互的区域，编辑修改的文件都位于这个区域，而提交前需要先做一步跟进，放在跟进区才能够提交。

##### 怎么样获取Git？

[Linux](http://git-scm.com/download/linux): sudo yum install git （Redhat系列） sudo apt-get install git （Debian系列） [Mac](http://git-scm.com/download/mac)和[Windows](http://git-scm.com/download/windows): 直接从Git网站下载安装包或者在GitHub网站下载安装包

##### 初次使用Git需要做哪些配置呢？

Git对每次的提交都会记录提交者的名字和邮箱，所以初次使用需要进行配置。此外，Git的许多命令都会调用编辑器进行交互，所以也需要配置默认的编辑器。

##### 怎么做这些配置呢？

配置前需要简单明确Git对用户的配置信息有三个层次的管理：`/etc/gitconfig`、`~/.gitconfig`、`.git/config`。三个层次后一个层次覆盖前一个，比如在`~/.gitconfig`和`/etc/gitconfig`配置了同一个属性，`~/.gitconfig`会生效。`/etc/gitconfig`的修改会影响所有系统用户，`~/.gitconfig`只影响当前用户，`.git/config`只影响当前的项目包。具体可以直接编辑这三个文件，或者通过命令行完成。举例：

```bash
$ git config --global user.name "Jon Doe"
```

配置文件中都是以键值对存储配置，例如`user.name="Jon Doe"`。`--global`表示设置在`~/.gitconfig`中，`--system`表示设置在`/etc/gitconfig`中。

##### 如何确定当前项目包的配置情况呢？

执行命令`git config --list`。

##### Git中怎么获得命令的帮助？

```bash
$ git help <verb>
```

这段文字改写自《Pro Git》一书的第二版第一章Introduction部分。阅读过程中，发现写得非常流畅，是本值得力荐的好书。这里的改写仅用作回顾，不推荐直接学习，原书中有很多图示的说明更加直观。
