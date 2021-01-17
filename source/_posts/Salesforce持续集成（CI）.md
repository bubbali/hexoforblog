---
title: Salesforce持续集成（CI）
catalog: true
date: 2019-12-30 17:46:10
tags:
- CI/CD
categories:
- Salesforce
---
# 持续集成(CI)
在日常开发过程中，代码常常会根据业务变更、功能异常等情况发生变化；那么当代码完成后，必不可少的步骤需要将代码部署到服务器端；频繁、重复的部署操作，一方面任何人也不想耗费时间在机械的工作上，另一方面，需要让开发人员更关注业务代码；这样一来，代码部署自动化的需求越来越大，简而言之呢，之前如果修改了一个系统Bug，除了要对业务代码进行修改推送到CVS，还需要部署到服务器上；现在呢，只需要关心您的代码，部署的事情交给另外一台机器帮您完成。回归到Salesforce系统中，目标部署服务器就是具体的Organization，那代码修改完需要自动部署目标Organization中。下面我们来看一下，自动部署是怎样实现与Salesforce建立连接。
# JWT配置
持续集成环境是自动化的，不支持Web-Based Flow，不熟悉该流程的同学，参考一下这篇文章[Using SalesforceDX(SFDX) without Scratch Org](https://www.jianshu.com/p/74aacac5ea5f)；在这里，需要用到JWT-Based Flow（JSON web tokens），这个流程需要将数字证书上传到自定义的应用中，然后再通过脚本进行服务端认证，简单来说可以分为以下两个阶段：
- 1. 本地创建自签证书、Salesforce端新建Connect app.

- 2. 脚本运行在CI环境中

## 验证前提
请在开始以下步骤之前安装OpenSSL（Open Secure Sockets Layer），使用以下命令检查环境：
```
which openSSl
```
环境没有准备好的话，如果是Mac系统，使用Homebrew进行安装
```
brew install openssl
```
Windows请下载[安装包](http://slproweb.com/products/Win32OpenSSL.html)。
## 验证过程
### 生成server.crt文件
- 1. 新建任意文件夹存储生成的文件，比如我在桌面上创建了*testJWT*文件夹。
- 2. 生成private key，存储在server.key文件
```
openssl genrsa -des3 -passout pass:SomePassword -out server.pass.key 2048
openssl rsa -passin pass:SomePassword -in server.pass.key -out server.key
```
![生成server.key](GenerateServerKey.png)
现在*testJWT*文件夹下有两个文件*server.key、server.pass.key*，*server.pass.key*文件不会再用到，可以将其删除。
-3. 生成证书签名，存储在server.csr文件中
```
openssl req -new -key server.key -out server.csr
```
![生成server.csr](GenerateServerCSR.png)
- 4. 根据server.key和 server.csr文件，生成自签名证书。
```
openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt
```
![生成server.crt](GenerateServerCRT.png)
目前为止，已经生成了JWT所需的证书，下一步新建Connect App的时候会用到。
### 创建Connect App
在Salesforce中新建Connect App应该是比较熟悉的了，不清楚的话，可以参考一下[使用Postman对Salesforce进行接口测试](https://www.jianshu.com/p/2f0face794f1)，需要说明一下两点：
- 1. 针对JWT认证方式，需要在新建App过程中，上传**server.crt**自签名证书。
![上传证书](UploadTheCRT.png)
- 2. 编辑App的OAuth Policies，修改为**Admin approved users are pre-authorized**方式
![修改认证属性](UpdateAuth.png)

随后你可以通过**Permission set**管理给App分配CI用户，方便起见，这里分配给简档**System Administrator**的用户。保存App后，需要保存一下**Consumer Key**，接下来的测试我们需要用到**Consumer Key**。
### 连接测试
现在App已经准备好，证书也上传到App，那我们是否能成功的连接到Salesforce呢？
![新建的Connect App](NewAppConnect.png)
通过以下命令测试，尝试建立连接：
```
export CONSUMER_KEY=3MVG9pe2TCoA1Pf4Sl71bGc3xBuVp.h8zly2rk_4gCeP0whCxrONIcvjPTkmTw3_.H3LNAA_QVIYuONIoXU4o //Connect App Consumer Key value
export JWT_KEY_FILE=/Users/bubba/Desktop/testJWT/server.key
export HUB_USERNAME=lj1377736@brave-bear-p3qvk9.com
sfdx force:auth:jwt:grant --clientid ${CONSUMER_KEY} --username ${HUB_USERNAME} --jwtkeyfile ${JWT_KEY_FILE}   --instanceurl https://brave-bear-p3qvk9-dev-ed.my.salesforce.com -a testJWT
```
如果能看到以下截图内容，说明已经成功连接到了Salesforce，也可以通过**sfdx force:org:list**命令来查看已认证的Organization.
![Success](Success.png)

# 下一步
相信您已利用JWT成功的建立了与Salesforce的连接，接下来还需要结合自动化部署工具（Travis、Jenkins）运行到项目中，提供两个资源模板供大家参考：
- [Travis ](https://github.com/forcedotcom/sfdx-travisci)
- [Jenkins](https://github.com/forcedotcom/sfdx-jenkins-org)

通过简单的配置脚本，让项目做到自动化部署，就可以节省开发人员时间，避免繁琐的操作。