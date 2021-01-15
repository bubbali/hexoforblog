---
title: Github配置SSH Key
catalog: true
date: 2019-12-31 11:14:37
tags:
- Git
categories:
- Git
---
#为什么要用SSH
日常代码开发过程中，本地机器需要不断的和远程Github进行交互，如果要将代码下载到本地有两种方式：
- Https
- SSH（Secure Shell）

使用**Https**方式，需要不断的输入用户名密码信息，严重的降低了开发效率；**SSH**方式是建立在应用层的安全网络协议，采用了非对称加密技术（RSA）保证传输数据的安全性，更多的信息可以参考 [ssh原理以及与https的区别](https://www.cnblogs.com/dzblog/p/6930147.html)。通过SSH协议，本地机器可以建立与远程服务器（Github）的连接，从而访问服务端资源；配置过程很简单，好处是每次访问服务器资源不需要频繁的输入用户名密码。接下来我们看看怎么将SSH key到Github账号中。
# 配置过程
-  生成SSHkey

为了避免不必要的麻烦，请先使用一下命令查看一下当前机器是否已存在SSH Key:
```
$ ls ~/.ssh
```
假定你是第一次配置，使用**ssh-keygen**命令生成SSH Key:
```
$ ssh-keygen -t rsa -b 4096 -C "your_email@company.com"
```
接受默认的配置，回车完成所有操作，然后查看一下**~/.ssh**，其中包含*id_rsa.pub（公钥）*、*id_rsa（私钥）*文件，正如前面提到，SSH协议采用非对称加密，下面配置需要用到这两个文件。
-  添加到SSH代理中

当本地与Github进行交互时， SSH-Agent会帮助我们完成与Github的校验，所以这一步需要将私钥添加到SSH-Agent，
```
// 1. 启用代理服务
$ eval "$(ssh-agent -s)" 
> Agent pid 30819

//2. 修改~/.ssh/config文件
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa

// 3. 将私钥添加到代理
$ ssh-add -K ~/.ssh/id_rsa
```
到此为止本地的机器配置就完成了，下一步需要将公钥添加到Github账户中。
-  将公钥到Github

还记得第一步生成的*id_rsa.pub*文件么，复制文件的内容，然后配置到Github账户下。
```
// 复制文本内容
$ pbcopy < ~/.ssh/id_rsa.pub
```
![Settings.png](https://upload-images.jianshu.io/upload_images/14975804-ccdb86402579061b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![New SSH Key.png](https://upload-images.jianshu.io/upload_images/14975804-8ec055ef64f49ca4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Add SSH Key.png](https://upload-images.jianshu.io/upload_images/14975804-25cf926daafd703c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将公钥复制到**key**中，Title可以随便命名，尽量通俗易懂，能够区分出是本地机器即可。
配置完成后，通过以下命令查看一下是否可以登录成功：
```
$ ssh -T git@github.com
```
本地机器可能会管理Github与Gitlab账号，这样就会存在两套公钥与私钥。需要注意一下两点：
- 生成SSH Key的时候指定具体文件名
```
$ ssh-keygen -t rsa -b 4096 -C "test"                  
> Generating public/private rsa key pair.
> Enter file in which to save the key (~/.ssh/id_rsa): ~/.ssh/is_rsa_test //指定具体的rsa文件，否则会覆盖之前的key
```

- 修改~/.ssh/config文件

```
// Gitlab Config
Host gitlab.com
    HostName gitlab.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa

// Github Config
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
```
# 总结
通过上述配置，SSH代理可以根据Host加载不同的秘钥进行校验。如果想要撤销SSH登录，只需要在Github端删除SSH Key；至此我们通过SSH方式登录到Github，十分有利于日常的开发，不过要注意保护好自己的秘钥文件。
