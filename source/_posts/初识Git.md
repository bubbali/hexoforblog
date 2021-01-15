---
title: 初识Git
catalog: true
date: 2019-10-03 11:07:52
tags:
- Git
categories:
- Git
---
# Git的介绍
Git是目前最流行的分布式管理系统，分布式管理是相对于传统的集中式管理；集中式管理的版本库在一台远程中央服务器中，开发者每次开发，都需要联网与远程库同步，代码版本库严重依赖于中央服务器，一旦宕机，严重影响工作；而分布式管理的版本库，除了远程的服务器（方便不同开发者同步代码更新），开发者的本地机器也会存在这个版本库，处处都有版本库，所以不必担心代码版本库会依赖于某一台机器，再加上Git强大的分支管理，深受广大开发者喜欢，今天我会介绍一下怎么用Git来管理文件。
# Git安装
- Git安装
   Linux、MAC、Window不同的环境，安装步骤有所区别，可以参考这篇[博客](https://linode.com/docs/development/version-control/how-to-install-git-on-linux-mac-and-windows/)。
- Git配置
为了在分布式管理中区分不同的开发者，每台本地电脑需要配置用户名信息；Git的配置信息有三个层级，从高到低分别是system、global、local级别，低级别的配置信息可以覆盖高级别的配置信息，意味着local级别可以配置任意的用户名信息。以配置当前用户global级别的配置信息为例使用以下命令：
```
git config --global user.name "USERNAME"
git config --global user.email " EMAIL"
git config --global  --list //system级别 git config --system --list local级别 git config --local  --list
```
# Git文件操作
- 新建版本库
在日常工作用，你可能会遇到文件保存丢失的情况，想象一下，如果有个照相机给文件拍了一个快照，那就可以根据这个快照恢复文件；版本库的目的就是记录你对文件所做的编辑修改，方便回溯历史。
切换到一个空文件夹，使用以下命令:
```
git init
```
![操作步骤](https://upload-images.jianshu.io/upload_images/14975804-ab87ee632c8688a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
通过上图你会发现**git init**命令会在当前文件夹中生成**.git**的文件夹。这个文件夹包含了以下信息：
![.git 文件信息](https://upload-images.jianshu.io/upload_images/14975804-46036f49f4665086.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
先不用着急了解这个文件夹，只要记住你的所有操作记录会被记录在这里就可以了。感兴趣的可以参考一下[从.git文件夹探析git实现原理](https://www.cnblogs.com/gscienty/p/7904518.html)。
到现在为止，这个**TestRepo**文件夹成了一个可以被管理的git仓库。下面咱们来添加一个文件到该文件夹下。
- 文件修改
首先我添加了一个文件**MyFirstFile**到该文件夹下：
![文件内容](https://upload-images.jianshu.io/upload_images/14975804-468f57f2cf88451f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Git中有工作区和暂存区的概念：
工作区：简单理解为你的工作目录，比如TestRepo文件夹，所有的操作都会在这个文件夹下进行。
暂存区：上面的**git init**命令生成了**.git**的文件夹，可以裂解为Git的版本库，在这个库中有个暂存区和分支，工作区完成的修改，想要添加到Git中管理，需要先使用**git add**命令添加到暂存区，然后**git commit**提交到当前操作的分支。为什么会有暂存区这个东西呢？简单理解就是暂存区记录的文件，在工作区可能还需要修改，最终一次性提交文件。
查看当前状态：
```
git status
```
![文件状态修改](https://upload-images.jianshu.io/upload_images/14975804-97abfde72a162447.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需要注意这里master分支，这是git自动生成的唯一一个master分支；当前文件操作是在master分支上，所以任何操作commit都会被提交到这个分支上。按照上图提示，我们应该用以下命令添加文件：
```
git add  //. 表示当前所有修改 如果有多个文件 用空格隔开即可
```
![添加到暂存区](https://upload-images.jianshu.io/upload_images/14975804-e113e41d3fecd537.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意区分一下添加前后的状态变化，下面我们提交到master分支上：
```
git commit -m 'comment'
```
![提交文件到master](https://upload-images.jianshu.io/upload_images/14975804-b183af739931c258.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到提交文件后，暂存区是空的，而且工作区和当前分支内容是一致的。接下来我们来看一下怎么通过Git操作来恢复文件。
1. 查看当前文件内容
![当前文件内容](https://upload-images.jianshu.io/upload_images/14975804-aa335cecf1e80139.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 修改文件
![修改后文件](https://upload-images.jianshu.io/upload_images/14975804-7e965a64c39f3ff9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 提交文件
![提交文件](https://upload-images.jianshu.io/upload_images/14975804-18df34db1f7f987f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 回退到上一个版本
使用**git reset**命令，可以回退工作区版本，下面我们来看一下怎么回退刚才的文件修改：
```
git log //查看提交历史记录
```
![查看历史提交](https://upload-images.jianshu.io/upload_images/14975804-511094193c43539e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
你会发现这里有两次提交记录，那一大长串的数字**681eedc4e3126768bbb868a93c99431739308913**是有Git自动生成的commit id, 回退的时候需用到这个id，接下来我们使用**git reset**命令来恢复文件：
```
 git reset --hard commitid //commit id 不需要写写全，比如这里只需要写681eedc4前几位
```
![回退文件](https://upload-images.jianshu.io/upload_images/14975804-9e22498104f179f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
你会发现这时已经成功的回退到了上个版本；通常的你可以使用**git reset --hard HEAD^**回退上个版本，**HEAD**表示当前操作的最新提交版本，也就是**commit 9043e07387edc724198bd495816c33b02c8fbaaf (HEAD -> master)**提交记录，**HEAD^**表示上一个提交版本，上上个版本表示**HEAD^^**,以此类推，所以上面的命令也可以表示为：
```
git reset --hard HEAD^//回退到上一个版本
```
使用Git，工作区、暂存区的内容也可以回退，这样以后就再不用怕文件误操作了。
```
git checkout -- file //撤销工作区的修改
```
```
git reset HEAD file //撤销暂存区的修改
```
# 总结
这篇着重介绍了一下使用Git进行文件操作（仓库初始化、文件新增、文件修改、回退历史记录），下一篇会从Git的分支管理来介绍，感谢支持！