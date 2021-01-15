---
title: Git 分支管理
catalog: true
date: 2019-12-31 18:58:31
tags:
- Git
categories:
- Git
---
# 为什么要分支管理
一直认为Git是很强大的利器，不仅仅是代码开发，平时编辑自己的文档，也可以尝试用Git来维护。今天我们来说说分支管理为什么这么受欢迎？

- 大家同时修改一个项目的代码，彼此会担心代码被覆盖么？
-  想过刚提交的代码会导致线上环境崩溃么？

上述场景任何一个问题产生都很麻烦。那Git是怎么做的呢？一个Git项目只会有一个Master时间线，在这条时间线基础之上可以任意创建属于自己的分支，不过需要遵循以下两个原则：
- 所有功能代码修改都应该在自己的分支
- 分支代码需要Merge到Master

因为代码开发都在自己的分支，所以开发不同的代码块不会有冲突，互相覆盖代码就更不存在了；当功能开发完成后，Merge到Master之前还需要代码Review，一方面保证分支代码是合乎逻辑的，另一方面新提交的代码和Master分支代码不会有冲突；如此一来，线上环境的代码会与Maser一致，只要对合并到Master的代码认真检查，线上环境就不会有问题。在我们开始实践操作之前，有一些概念需要澄清一下：

-  Master分支 初始化代码仓库的时候自动生成Master，代码开发需要Check out到自己的分支，随着分支代码Merge到Master，Master代码会逐渐增加。
-  HEAD 理解为一个指针，永远指向当前操作分支，意味着当切换到新的分支后，HEAD会指向新的分支。

如果想对Git流程有更深的理解，结合[图形](https://learngitbranching.js.org/)会更有趣。
# 常用操作
- 新建分支

当有新功能需求的时候，需要更新仓库最新的代码进行开发，建议每开发一个新功能点，新建单独的分支。
```
$ git branch //查看当前分支，可以看到当前处于Master分支
> * master
$ git branch test //新建名称为test的分支
> git checkout test // 切换到test分支
> Switched to branch 'test'
$ git branch // 再次查看分支状态
  master
* test
```
上述的新建分支分为两句操作，也可以用下面这一句来完成：
 ```
$ git checkout -b test
```
- 修改文件

比如我尝试在新建的*test*分支上添加了一个文件。
```
$ touch testFile //新建一个testFile的文件
```
![testfile内容](https://upload-images.jianshu.io/upload_images/14975804-d1a1bee5331338cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果此时你切换到Master分支，会发现并不存在这个文件，这是因为当前分支操作在*test*分支上。
```
// 将文件提交到test分支
$ git add testfile 
$ git commit -m 'Add test file by test branch'
```
- 合并操作

至此为止，合并代码是最后一个步骤，会将每个人做的变更合并到Master这条时间线上。
```
$ git merge test //合并指定分支当当前操作分支
```
- 删除分支

当功能开发完成后，需要删除功能分支。
```
$ git branch -d test //删除test分支
```
# 团队开发
项目开发中，大家需要共用一个远程仓库，比如常用的Github，这个代码托管平台可以方便开发人员及时更新最新代码，除此以外，Github还支持Code Review，以保证代码的质量。下面列一下Github项目操作与本地项目操作的区别：

- 当克隆远程项目后，执行*git remote -v*可以看到远程仓库信息，如果只是本地项目不返回任何信息。针对这一点，可以参考[Github配置SSH Key](https://www.jianshu.com/p/f6e2d1a19f4f)
- 与Github进行交互，会常常用到以下命令：
```
$ git clone RepoUrl //克隆项目
$ git pull  //将最新的代码拉取并合并
$ git fetch origin branch_name // 拉取最新远程分支代码
$ git push origin branch_name //将本地分支内容提交到远程
$ git branch --set-upstream branch-name origin/branch_name //建立本地分支与远程分支的关联
$ git push -d origin branch_name // 删除远程分支
```

初学者在使用Github的时候，难免会遇到各种异常，希望这篇文章能够帮助到您。强烈推荐大家参考[官方文档](https://git-scm.com/docs)。


