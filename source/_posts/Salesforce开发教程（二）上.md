---
title: Salesforce开发教程（二）上
catalog: true
date: 2018.11.23 10:59:45
top: 990
tags:
- Salesforce
categories:
- Salesforce
---
# 1 开发预览
&emsp;&emsp;当你刚开始接触Salesforce开发，可能会有一些困惑，之前传统开发过程中遇到的问题（服务器配置、Jar包版本控制、程序驱动的安装等）仿佛都不见了，取而代之的是新鲜的困惑，暂时先不考虑这些；让我们先梳理一下，在Salesforce平台上开发时，当把代码提交给Salesforce的时候，服务器端到底发生了什么事情？
![Salesforce开发流程示意图](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzE0OTc1ODA0LTgyNWEyZmZiZmRiNzgwMmY?x-oss-process=image/format,png)
&emsp;&emsp;针对开发人员而言，请仔细观察上图Developer User终端，所有由开发人员编写的代码都将以元数据的形式存储在数据库中，然后等待用户触发执行。这个过程中有两点需要注意：1、开发人员编码后（不管是在Salesforce的Console或者是在本地的IDE），需要及时提交代码由Application Server进行审查代码是否正确；如果是基本语法错误，比如数据类型不对，变量命名重复等，IDE会及时检查并提醒报错；如果是因为表名、字段名称不存在等需要服务器端验证，则需要提交服务器端进行编译验证代码是否正确；还有一种错误就是运行代码的时候报错，比如空指针等，这属于运行时错误；那么在将代码存储在数据库中之前，必须保证代码能够通过服务器验证，否则代码保存失败；2、因为数据库只能存储一份编译后的代码，而在实际项目开发过程中，可能同时会由多个开发人员进行操作，所以如果这时大家都提交代码，则可能会覆盖别人的代码，为了避免这种情况发生，建议大家在提交代码前，先及时同步服务器端最新版本的代码，然后在提交自己的代码。
&emsp;&emsp;了解到Salesforce开发时需要将自己的代码Commit到服务器中，大家先别着急写代码；接下来需要知道的是自己编写的代码可以在云平台上实现哪些功能；相对于熟悉的传统开发做比较，一般而言会在MVC层做出符合自己意愿的功能，那么想象一下像Salesforce这种将前后台集成在一块且复杂的框架，也是B/S架构，所以它肯定脱离不了在这三层做出一些功能改变。除了通过代码实现自己的需求（开发），Salesforce还用提供了一种更便捷的方式供我们只需要简单的点击就可以实现功能（配置）。下图展示了Salesforce开发和配置的种类
![Salesforce开发与配置分类](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC1hZDdkZTdhYWEzMmY0NGFmLnBuZw?x-oss-process=image/format,png)
&emsp;&emsp;先别纠结上图，简单来看呢，在Salesforce平台下进行编程，大部分情况下可以像传统开发一样对MVC层进行自定义修改，包括但不限于扩充数据库表、更改业务逻辑代码、定制自己喜欢的前端代码等，本篇内容会围绕以上涉及的内容一一展开！
&emsp;&emsp;接下来请准备好你的开发账号，让我们开始Salesforce开发之旅吧。
# 2 让我们输出Helloworld
## 2.1 登陆Salesforce
&emsp;&emsp;还记得你注册的账号么？请登陆[开发环境](https://login.salesforce.com)输入你的用户名\密码，点击登陆，如果你看到的是下面这个界面，恭喜你已经拥有了自己的开发环境，尽情在这里施展自己的才华吧，因为本期内容暂不考虑Lightning内容，所以按照以下操作切换至Classic界面![Salesforce Lightning主页](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC01ZTk1MmZlOTczN2MwZjVjLnBuZw?x-oss-process=image/format,png)
![切换Classic界面](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC0zNTY0ZDA2Y2NmZTcxZDBkLnBuZw?x-oss-process=image/format,png)
看到以下这个页面就代表一切准备工作都ok了
![Classsic界面](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC05NTExZTdlMTk1MGUzMjY5LnBuZw?x-oss-process=image/format,png)
## 2.2 代码执行
工具：Salesforce Console
示例：输出Helloworld
### 2.2.1 脚本运行
- 打开SF控制台窗口
  ![控制台窗口](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC0xZTA4NjFjZWFlNGFmOWYwLnBuZw?x-oss-process=image/format,png)

- 点击Debug选项卡，然后选择异步执行窗口，如下图所示
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC1iOWNlNDlkNjU5NjlmMzMzLnBuZw?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC1hYmJlMzY1OTcxZDcxYmQ5LnBuZw?x-oss-process=image/format,png)

- 输入以下代码

```
System.debug('Helloworld!');
```
点击执行按钮（疑惑怎么没有预想输出的结果？）

- 找到输出的结果，双击刚刚执行代码的日志
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC0xMDYyM2VmZTYxYWE5MmFhLnBuZw?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC0wMmVkMWM1Y2I3NjlmOGMwLnBuZw?x-oss-process=image/format,png)

&emsp;&emsp;点击Debug，是为了将上述语句的输出信息，从log日志中筛选出来，ok，到这一步，我们已经写了一行Apex代码，且看到了执行结果。这是在Salesforce平台上执行脚本的方式（类似与Java的本地控制台程序，现在输出的Helloworld和你在自己电脑上控制台输出没什么区别），下面我们来看一下怎样在网页面中显示出来。
### 2.2.2 静态Page执行
- 新建VisualForce Page，输入Apex Page名称（不支持中文命名）
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC0zODIzNzhiMTIwMjczYWQ1LnBuZw?x-oss-process=image/format,png)
- 输入以下代码块
```
<apex:page> 
    <h1>Hello World</h1> 
</apex:page>
```
在<apex:page>标签里面，除了可以写Visual page中的标签，也可以写普通Html中的标签

- 结合上述说的内容，该页面已经存储在服务器端，那么怎么能够运行呢，大家都知道，既然是一个网页，那么url在哪呢?访问vf页面url格式为：
>*'Instanceurl/apex/'* + **page name**

Instanceurl为以下格式
>https://xxxx.salesforce.com

那么访问该Helloworld页面的地址就是：
>https://xxxx.salesforce.com/apex/Helloworld

![Helloworld运行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC0zMzI3YzgxZDIzYmNkNmQ0LnBuZw?x-oss-process=image/format,png)
&emsp;&emsp;这是在Classic页面下访问方式，还有一种简便的方式直接点击控制台的预览按钮。
如果是在Lightning页面下，需要打开浏览器控制台，执行以下语句就可以看到VF Page了：
```
$A.get("e.force:navigateToURL").setParams(
    {"url": "/apex/Helloworld"}).fire();
```
&emsp;&emsp;ok，现在我们简单的创建了一个类似于Html的页面，且可以在浏览器中访问，但是细心的你会发现，页面不仅仅有你写的Helloworld，还有Salesforce的模板，可以去掉么？将你的代码稍微修改成下面的样子，重新运行看一下，是不神奇的事情就发生了？
```
<apex:page showHeader="false"> 
    <h1>Hello World</h1> 
</apex:page>
```
提示一下：
&emsp;&emsp;如果你打开了[开发模式](https://salesforce.stackexchange.com/questions/86417/how-to-enable-development-mode-in-developer-account)，则直接可以在浏览器端修改调试页面代码
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC1mNGYyMjBhZDRjZjk4OGI3LnBuZw?x-oss-process=image/format,png)

&emsp;&emsp;刚才演示的是一个静态的页面，那么可以加载出从数据库中取出来的动态元素么？当然可以！Salesforce提供了全局变量和controller两种方式，实现动态的渲染前端页面数据，其中controller包含自定义controller和标准controller，标准controller包含一些标准的方法（保存、删除等）；如果自己需要别的方法就得自定义controller方法或者继承标准controller，这样就能够为前端VF Page提供更多灵活可变的数据和操作，下面看一个页面动态加载数据库数据的例子，
### 2.2.3 动态Page执行
- 新建Apex类，打开控制台，新建MyAccountController
```
public class MyAccountController{
    public List<Account> getMyAccounts(){
       List<Account> accList = new List<Account>([select Id,AccountNumber,Name from account where OwnerId =:userinfo.getUserId()]);
       return accList;
    }
}
```
- 新建VF页面
```
<apex:page controller="MyAccountController">
    <apex:form>
        <apex:pageBlock>
            <apex:pageBlockTable value = "{!myAccounts}" var = "account">
                <apex:column  value = "{!account.AccountNumber}"/>
				<apex:column  value = "{!account.Name}" />              
            </apex:pageBlockTable>
        </apex:pageBlock>
    </apex:form>
</apex:page>
```
- 点击预览，查看自己所有的客户
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC1mNTA3YWY4YTdhZmQyNmU3LnBuZw?x-oss-process=image/format,png)

&emsp;&emsp;通过以上例子大家先有一个感性的认识，了解到通过Salesforce的平台，不仅仅可以运行脚本，还可以像传统网站开发一样进行操作；其中Visualforce Page负责了页面显示(View)，Controller负责了业务逻辑处理（Controller），Controller层通过与Salesforce数据库(Model)交互，最终返回用户想要的数据。举了这么多例子，到现在为止，大家还没好好的了解Salesforce开发语言，是时候介绍一下在Salesforce平台下开发的专属语言Apex了。
 
## 2.3 Apex
&emsp;&emsp;Apex的语言是为Force.com量身定做的一门语法上类似Java的强类型面向对象语言，主要可以通过Apex在Force.com上创建Web Service，编辑复杂的商业逻辑和整合多个Force.com的模块等。APEX主要以两种方式执行：其一是以单独脚本的形式，按照用户的需要执行（参考上面脚本执行例子）。其二是以页面访问或者触发器的形式，当一个特定的数据处理事件发生的之前或者之后，与这个事件绑定的Apex代码将会被执行(参考上面页面执行例子)。而且所有Apex代码将会以Metadata的形式存储在Metadata表内。当一段Apex代码被调用的时候，Apex的翻译器（runtime interpreter）将会从Metadata Cache读取编译之后的Apex代码执行，而且能够同时被多个租户共享以提升效率（参考Salesforce开发流程示意图）。      
### 2.3.1数据类型
&emsp;&emsp;Apex语言数据类型大体分为三类：
&emsp;&emsp;1、基本数据类型，比如整型（Integer）、浮点数(Double、Float、Double)、字符串(String)、ID、布尔值(Boolean)、日期/日期时间(Date\Datetime)等
&emsp;&emsp;2、集合类，比如链表（List）、集合（Set）、映射（Map）
&emsp;&emsp;3、对象类型，Object包含用户自定义的类、对象、Sobject（Salesforce Object）等
&emsp;&emsp;着重描述一下ID类型，ID可以理解为数据记录的在Salesforce数据库中的唯一标识（基于Base-62的15位的区分大小写的字符串，包含0-9数字，a-z小写字母，A-Z大写字母），有时候需要将Salesforce的数据导出到本地进行处理，处理工具可能不区分大小写，所以Salesforce在15位ID后面又加了三位字符后缀，这样18位的字符串是不区分大小写的，更利于区分数据记录。下图直观的展示了ID的具体内容与作用，感兴趣的同学可以多了解一些。
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xNDk3NTgwNC1iZWFiNDljMGYzMjI0Y2IzLnBuZw?x-oss-process=image/format,png)
### 2.3.2 语法特点
&emsp;&emsp;和Java语言语法基本一致，略微有些差异：
&emsp;&emsp;1、不区分大写，下面这条语句在控制台执行成功。
```
String S = 'Helloworld';
system.debug(s);
```
&emsp;&emsp;2、字符串需要用单引号标识，比如上面定义的S字符串变量。
### 2.3.3 开发工具
&emsp;&emsp;除了这个教程中使用的Salesforce自带的控制台工具，还可以使用VSCode、Eclipse、Sublime等工具进行Salesfoce开发。
&emsp;&emsp;[Salesforce Extensions for VS Code](https://developer.salesforce.com/tools/extension_vscode)
&emsp;&emsp;[Eclipse Install Force.com Plugin](https://developer.salesforce.com/docs/atlas.en-us.eclipse.meta/eclipse/ide_install.htm)
&emsp;&emsp;[Subime Install HaoIDE](https://packagecontrol.io/packages/haoide)
&emsp;&emsp;[Sublime Install MavesMate](http://www.oyecode.com/2014/01/mavensmate-forcecom-ide-for-salesforce.html)
待续。。。