---
title: 使用Postman对Salesforce进行接口测试
catalog: true
date: 2019-10-04 10:17:09
tags:
- Salesforce
categories:
- Salesforce
---
在开发过程中，难免会与其他系统集成，那么当自定义的Service完成后，常常会用到第三方工具测试；今天我们来看一下怎么通过Postman进行接口测试。这个过程大概分为三个步骤：
# 新建Connect App
我们用的是[OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3)，简单而言就是用户允许第三方应用访问存储在服务器的资源（通过Acess Token），不需要每次访问服务器都需用户的用户名和密码，对OAuth 2.0想了解更深入的，可以参考[OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749)；为什么我们需要新建Connect App？针对OAuth2.0 需要一个Client（代要表访问资源的第三方应用程序），Salesforce作为资源所有者，允许第三方应用程序就是下面新建的Client，访问Salesforce；通过新建Connect App，系统管理员指定允许访问哪些资源；接下来来看一下怎么新建这个Client:
1. 设置 -> 新建 -> 应用程序，找到Connect App，点击新建按钮
2. 填写相应的表单信息
![Connect App信息](https://upload-images.jianshu.io/upload_images/14975804-dd1bf2861e62d8d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们采取的以下的[认证方式](https://tools.ietf.org/html/rfc6749#section-4.3)，接下来的测试，我们会分为获取AccessToken和使用AccessToken两个步骤，回调地址可以任意填写。
![用户密码认证](https://upload-images.jianshu.io/upload_images/14975804-d2e2d04e1b906faf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 点击保存，可能需要等待几分钟。
![保存](https://upload-images.jianshu.io/upload_images/14975804-04e27e74fb65c539.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 点击继续，获取到Client  Id与Client Secret。
![Client Id 与Client Secret](https://upload-images.jianshu.io/upload_images/14975804-37b4833e0382b5a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*Client Id: 3MVG9d8..z.hDcPLOcRPNcBaX20Z8.cBmihfkG1NQYewpjQ6f.gGOpXidF7yGTDzv3dFdl*
*Client Secret: 96E94B43FFD1B1CBD8C2F6C99406108B0F0F735AD627B0181E19AED10E80XX*

# 获取Access Token
这一步，我们来看看怎么用Postman拿到Access Token，需要以下信息：
- 请求方式：Post
- 请求URL
https://*login.salesforce.com/services/oauth2/token?grant_type=password&client_id={ClientId}&client_secret={ClientSecret}&username={Username}&password={Password}*
- Postman中新建Post请求：
![获取Access Token](https://upload-images.jianshu.io/upload_images/14975804-7f25d0dc103c60cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意**access_token**和**instance_url**这两个参数，下一步骤的时候会用到。
# 获取服务资源
现在我要通过上一步骤拿到的access token访问Salesforce的客户对象：
- 请求方式：Get
- 请求URL
    https://{instanceurl}/services/data/v46.0/sobjects/account
- 请求参数：
Accept -> application/json
Content-Type ->  application/json
Authorization -> Bearer AccessToken
如下图所示：
![请求资源](https://upload-images.jianshu.io/upload_images/14975804-5df94d9b8611ef1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面我们来看一下怎么将第二个步骤和第三个步骤合并为一个步骤，需要做以下调整：
- 第一步新建的Connect App的回调地址更改为：
*https://www.getpostman.com/oauth2/callback*
- 修改第三步获取资源的请求设置：
![修改Authorization为OAuth 2.0](https://upload-images.jianshu.io/upload_images/14975804-a1751b73afbb0fef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 点击Get New Access Token按钮，填写表单信息，如下图所示:
*Token Name*:填写任意值
*Auth Url*:https://login.salesforce.com/services/oauth2/authorize
*Access Token Url*：https://login.salesforce.com/services/oauth2/token
![获取Access Token](https://upload-images.jianshu.io/upload_images/14975804-f81e4843768563c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 点击Request Token按钮，输入用户名密码，认证成功之后，Salesforce会返回Postman一个Access Token。
![返回Access Token](https://upload-images.jianshu.io/upload_images/14975804-f0e43c1c5c249e26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 将该Access Token运用到第三个步骤的请求中，Add Token To选择到Header中，点击Use Token，接下来你会发现Header中包含了Access Token，接下来发送请求就可以了。

# 总结
通过这篇内容，希望大家能够对OAutht 2.0有个简单理解，项目开发过程中，如果需用用到接口测试的，强烈推荐大家使用Postman。