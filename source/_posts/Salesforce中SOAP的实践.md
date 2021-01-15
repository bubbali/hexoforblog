---
title: Salesforce中SOAP的实践
catalog: true
date: 2020-05-04 17:01:50
tags:
- SOAP Integration
categories:
- Salesforce
---
>这篇SOAP相关文章是[Salesforce开发教程（三）](https://www.jianshu.com/p/2cf0213f7c1b)的补充，了解到不少同学在系统集成中会遇到各种各样的问题，之前写过一篇关于REST的相关文章[使用Postman对Salesforce进行接口测试](https://www.jianshu.com/p/2f0face794f1)，今天来简单介绍一下Salesforce中SOAP的实践。
# 基础知识
使用SOAP集成，必不可少的过程是需要服务端提供WSDL文件，消费端通过定义了Web Service的WSDL文件，进而与服务端发生通信；比如说现在有第三方应用通过SOAP方式访问某个Salesforce 组织的资源，可能会引申出下面三个问题：

- SF的WSDL文件在哪里?从哪可以获取？
- 系统的配置如果发生变化，需要及时更新WSDL文件么？
- WSDL文件定义了SF端的Web Service，是否意味着拥有了WSDL文件就可以任意操作SF了呢？

让我们逐一来解释一下，首先，SF提供了两种WSDL文件，分别对应不同业务场景，Enterprise WSDL是强类型的，不同组织的Enterprise WSDL文件是不同的，Enterprise WSDL代表的是具体Instance的资源（对象、字段、Web service等）；而Partner WSDL是弱类型的，不同SF组织的Partner WSDL文件是相同的。在大部分集成项目中，大家都会使用Enterprise WSDL文件来和指定的SF组织做集成，如果需要和多个SF组织做集成，使用Partner WSDL文件。那么回到上面例子提到的第一个问题，因为要和指定SF组织做集成，所以我们选择了Enterprise WSDL文件；接下来看第二个问题，既然WSDL文件定义了SF组织的对象、字段等配置，显然如果这些配置SF端发生变化并且在代码逻辑中引用了，那应该及时的更新WSDL文件，否则第三方应用程序会出现异常。
前两个问题已经解决了，接下来看最后一个问题，想象一下，如果我们拿到了某个SF组织的WSDL文件，就可以对SF进行任意操作，这将是多么可怕的一件事情；其实在调用SF端服务之前，需要SF系统管理员提前设置好API User，那么在系统交互的过程中，Session Id可以保证会话是安全的，更为重要的是，我们可以通过Profile的设置，限制API User的权限，所以对第三个问题的担忧是多余的。想必大家对SF中SOAP的使用有了初步的了解，接下来看一下具体在项目中的实际操作。
# SOAP UI的测试
前提准备：

- WSDL文件下载
![Generate Enterprise WSDL](https://upload-images.jianshu.io/upload_images/14975804-4f8a6bd1579ad077.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果还没有可以用于测试的SF Organization的同学，请先注册一个[Developer Edition](https://developer.salesforce.com/signup)。
- [SOAP UI](https://www.soapui.org/)安装

准备工作完成后，接下来看一下怎样利用SOAP UI调用SF的服务，大体分为三个步骤：

1. 新建SOAP项目，引入刚才下载的WSDL文件；
![New SOAP Project](https://upload-images.jianshu.io/upload_images/14975804-828243720594ea1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![SOAP Binding](https://upload-images.jianshu.io/upload_images/14975804-bdb30772d64f72a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

展开左边的下拉列表，可以看到一系列WSDL定义的操作，随意打开其中某个文件，内容包含了请求的地址和参数，接下来将调用这些操作。

2. 按照上面所说的，整个SOAP交互过程需要用到Session Id，这一步会通过用户名和密码来获取Session Id；
浏览上述下拉列表，找到Login操作，将Request Body修改为以下内容:
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:urn="urn:enterprise.soap.sforce.com">
   <soapenv:Header>
   </soapenv:Header>
   <soapenv:Body>
      <urn:login>
         <urn:username>Username</urn:username>
         <urn:password>Password + Security Token</urn:password>
      </urn:login>
   </soapenv:Body>
</soapenv:Envelope>
```
注意这里需要用到Security Token，可以在[个人设置](https://help.salesforce.com/articleView?id=user_security_token.htm&type=5)中Reset Token。按照上面的请求体发送后，会得到下面的Response Body:
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns="urn:enterprise.soap.sforce.com" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   <soapenv:Body>
      <loginResponse>
         <result>
            <metadataServerUrl>https://xxxxxxx.my.salesforce.com/services/Soap/m/48.0/00D7F000006Mp0t</metadataServerUrl>
            <passwordExpired>false</passwordExpired>
            <sandbox>false</sandbox>
            <serverUrl>https://xxxxxxx.my.salesforce.com/services/Soap/c/48.0/00D7F000006Mp0t/0DF7F000000L9uA</serverUrl>
            <sessionId>xxxxxxx</sessionId>
            <userId>0057F000002IwR4QAK</userId>
            <userInfo>
               <accessibilityMode>false</accessibilityMode>
                ...
            </userInfo>
         </result>
      </loginResponse>
   </soapenv:Body>
</soapenv:Envelope>
```
注意Session Id参数，当我们拿到Session Id，随后与SF的交互都需要这个Session Id，下面将尝试通过SOAP在SF端新建一个Account。
3. 利用上一步获得的Session Id，调用Create方法，在SF端新建Account。

Request Body:
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:urn="urn:enterprise.soap.sforce.com" xmlns:urn1="urn:sobject.enterprise.soap.sforce.com">
   <soapenv:Header>
      <urn:SessionHeader>
         <urn:sessionId>056D7F000006Mp0t!AQUAQD__D71qXu54WOcHCtEHsi.F._QAi4eXSvs_ihHJn69dqAa3nVnBUhOgs_kO</urn:sessionId>
      </urn:SessionHeader>
   </soapenv:Header>
   <soapenv:Body>
      <urn:create>
         <!--Zero or more repetitions:-->
         <urn:sObjects xsi:type="urn1:Account" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <!--Zero or more repetitions:-->
            <Name>Test Account From SOAP</Name>
            <Accountnumber>000000</Accountnumber>
            <Phone>123456789</Phone>
         </urn:sObjects>
      </urn:create>
   </soapenv:Body>
</soapenv:Envelope>
```

Response Body:
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns="urn:enterprise.soap.sforce.com">
   <soapenv:Header>
      <LimitInfoHeader>
         <limitInfo>
            <current>16</current>
            <limit>15000</limit>
            <type>API REQUESTS</type>
         </limitInfo>
      </LimitInfoHeader>
   </soapenv:Header>
   <soapenv:Body>
      <createResponse>
         <result>
            <id>0017F00002TfEMtQAN</id>
            <success>true</success>
         </result>
      </createResponse>
   </soapenv:Body>
</soapenv:Envelope>
```
通过Response状态，可以看到创建成功了，并且返回了Account的Id；让我们来回顾一下上面做了哪些事情，首先通过Login方法获取到了SF端的Session Id和Server Url，然后再调用Create方法创建了一个Account；通常我们会用SOAP UI对web service进行测试，然后再把WSDL文件提供给消费者，当消费者拿到WSDL文件后怎么使用呢？接下来看一下怎样在Java程序中调用WSDL定义的方法。
# Java通过SOAP连接SF
这个配置工作可以参考SF[官方文档](https://developer.salesforce.com/docs/atlas.en-us.salesforce_developer_environment_tipsheet.meta/salesforce_developer_environment_tipsheet/salesforce_developer_environment_overview.htm)，下面着重提到两个部分。

- WSDL file生成Java package
1. 生成Java包的过程中，需要用到[Web Services Connector](https://mvnrepository.com/artifact/com.force.api/force-wsc)，注意保证wsc的版本与你所生成的wsdl文件的版本保持一致，比如这里我会选择48.0的版本。
2. 执行生成jar包命令的过程中，可能会遇到的异常。

2.1. Permission Denied: /Users/xxx/xxx.jar
```
sudo chmod 777 /Users/xxx/xxx.jar
```
2.2. 缺少antlr.jar
```
Issue Message:
Exception in thread "main" java.lang.NoClassDefFoundError: org/antlr/runtime/CharStream
```
这个时候需要引入[Antlr.jar](https://www.antlr.org/download.html)。 
到现在为止呢，我们将会用到下面5个文件：
![准备工作](https://upload-images.jianshu.io/upload_images/14975804-e3559cea7a6a3a5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

控制台执行以下命令：
```
Input:
> java -classpath /Users/Bubba/Desktop/SOAPTest/antlr-4.8-complete.jar:/Users/Bubba/Desktop/SOAPTest/rhino-1.7.12.jar:/Users/Bubba/Desktop/SOAPTest/ST-4.3.jar:/Users/Bubba/Desktop/SOAPTest/force-wsc-48.0.0.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/tools.jar com.sforce.ws.tools.wsdlc /Users/Bubba/Desktop/SOAPTest/SFEnterprise.wsdl  /Users/Bubba/Desktop/SOAPTest/SFEnterprise.jar

Output:
[WSC][wsdlc.main:71]Generating Java files from schema ...
[WSC][wsdlc.main:71]Generated 956 java files.
[WSC][wsdlc.main:71]Compiling to target 1.8...
[WSC][wsdlc.main:71]Compiled 960 java files.
[WSC][wsdlc.main:71]Generating jar file ... /Users/Bubba/Desktop/SOAPTest/SFEnterprise.jar
[WSC][wsdlc.main:71]Generated jar file /Users/Bubba/Desktop/SOAPTest/SFEnterprise.jar
```
可以看到SFEnterprise.jar文件已经成功生成，怎么能够保证这个jar包是可用的呢？让我们来参考一下官网提供的[例子](https://developer.salesforce.com/docs/atlas.en-us.salesforce_developer_environment_tipsheet.meta/salesforce_developer_environment_tipsheet/salesforce_developer_environment_verify_wsdl.htm) 。 
- 验证SFEnterprise.jar

注意将wsc和生成的SFEnterprise.jar都引入到Java项目中。
![SFSOAPTest Project](https://upload-images.jianshu.io/upload_images/14975804-ffb1977cc0922c62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
SFSOAPTest.java
```
package wsc;

import com.sforce.soap.enterprise.Connector;//from SFEnterprise.jar
import com.sforce.soap.enterprise.EnterpriseConnection;
import com.sforce.ws.ConnectionException;// from wsc.jar
import com.sforce.ws.ConnectorConfig;

public class SFSOAPTest {
    static final String USERNAME = "YOUR DEVORG USERNAME";
    static final String PASSWORD = "YOUR DEVORG PASSWORD AND SECURITY TOKEN";
    static EnterpriseConnection connection;
    public static void main(String[] args) {

        ConnectorConfig config = new ConnectorConfig();
        config.setUsername(USERNAME);
        config.setPassword(PASSWORD);
        try {

            connection = Connector.newConnection(config);
            // display some current settings
            System.out.println("Auth EndPoint: "+config.getAuthEndpoint());
            System.out.println("Service EndPoint: "+config.getServiceEndpoint());
            System.out.println("Username: "+config.getUsername());
            System.out.println("SessionId: "+config.getSessionId());
        } catch (ConnectionException e1) {
            e1.printStackTrace();
        } 
    }
}

```
执行这段代码后，如果能够看到类似于下面的输出，表示成功的拿到Session Id，那么意味着我们成功的踏出了第一步，接下来就可以用Session Id来和SF进行交互。
```
Auth EndPoint: https://login.salesforce.com/services/Soap/c/48.0/0DF7F000000L9uA
Service EndPoint: https://brave-moose-421893-dev-ed.my.salesforce.com/services/Soap/c/48.0/00D7F000006Mp0t/0DF7F000000L9uA
Username: xxxbrave-moose-421893.com
SessionId: 00D7F000006Mp0t!AQUAQD__D71qXu54WOcHCtEHsi.F._QAi4eXSvs_ihHJn69dqAa3nVnBUhOgs_kOyIg4BPJ4ur0o_o9YfVliYpfGAZC5Wxxx
```
# 总结
通过本篇文章，希望对大家在Salesforce中SOAP的实践过程中有所帮助，文中有不足的地方，多多指教。