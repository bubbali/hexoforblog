---
title: Salesforce开发教程（三）
catalog: true
date: 2019-10-05 11:44:25
top: 960
tags: 
- Salesforce
categories:
- Salesforce
---
今天这篇是延续[Salesforce开发教程（一）](https://www.jianshu.com/p/2744b94850bc)、[Salesforce开发教程（二）](https://www.jianshu.com/p/af63b92f3b93)的内容，对之前没涉及到的主题进行的补充。主要包括Unit Test、Batch、Schedule Job、Rest和Soap相关内容。
#测试类
当我们写完某个功能的时候，怎么能够保证自己写的代码正常运行起来呢？大家可能会说测试一下就可以了，那是不是这样子呢？其实测试分为单元测试、集成测试、功能测试，从前到后分别对应着三个阶段，这里的测试类指的是单元测试，为了能够让集成测试和功能测试顺利的运行起来，作为开发者，应该能够保证单元测试正常运行，保证函数方法是没问题的，后面的集成、功能测试才能正常顺利进行；换句话说，之所有单元测试这么重要，是为了能够将代码问题提前暴露出来，尽早的解决。所以我们应该除了将功能开发完成后，还需要通过测试类来验证功能是否完整；除此以外Salesforce会要求代码的单元测试覆盖率达到75%以上，所以单元测试的重要性就不言而喻了。下面我们来看看在Salesforce中怎样进行单元测试？
单元测试的语法很简单：
```
@isTest 
static void testName() {
    // code
}
```
这是一个单元测试方法，需要将这个方法放到下面的Class中：
```
@isTest
private class MyTestClass {
    @isTest 
     static void testName() {
        // code
    }
}
```
简单可以简测试类的过程分为三个步骤：
- 准备数据
这里的数据指的是运行测试类数据，每当测试类运行完成后，数据库的数据会被回滚，不必担心测试类中修改的数据影响到数据库。这里注意提倡测试类用到的数据应该避免依赖于某个组织（不建议使用SeeAllData=true标注）；推荐你使用**@testSetup**申明**setup**[方法](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_testsetup_using.htm)准备数据；另外需要注意的是，Salesforce有一些系统限制是难以避免的（比如Soql  101 错误），出现这样的错误后，需要优化一下Soql和DML操作。
- 调用要测试的类
实例化需要测试的Class，调用相应的方法就可以了；注意的是除了正向测试，还应该进行反向功能测试，从而提高代码的健壮性。
- 断言方法输出的内容是否和预期的一致
每次运行测试类方法都应该进行**assert**，从而保证要验证的方法是没有问题的；建议每个测试方法至少包含一个**assert**语句。

今天给大家准备了一个写Unit Test的模板，可以参考使用：
```
@isTest
private class MyTestClass {//Class申明为Private且名字应该能够识别出测试的类
    @testSetup
    static void setUp(){
        //code 准备数据，DML操作应该在这个方法里面完成
    }
    @isTest 
    static void myMethodTest() {//Method名称应该能够识别出测试的功能
        Test.startTest();
        System.assert(xxx);
        Test.stopTest();
        System.assert(xxx);//每个测试方法照烧包含一个assert语句
    }
}
```
如果你对UT还想有更多的思考和实践，希望我之前写过的一篇[Mock Framework For Unit Test](https://www.jianshu.com/p/f9e10ce1cc1f)可以帮助到你。
#Batch和Schedule Job
针对Batch相信大家不会陌生，一句话概括呢，就是用来处理数据量大的场景；为什么我们要用Batch，而不使用普通的类呢，总结了一下，Batch大概有下面三个优点：
- 可以提高Salesforce的系统限制，普通Class的Soql的每个事务查询次数是100次，而用Batch可以达到200次；其次是数据量的大小，普通Class的Soql查询数据上限是50 000，Batch可以达到50 000 000。
- Batch可以批量处理数据，在每次执行**execute**的方法时候，可以接收任意条数大小的数据量，每个Batch最多可以处理200条数据，意味着你可以将数据量分为200一个批次执行。
- Batch可以和定时任务结合在一块，做一些日常工作，比如系统定期生成任务或者清理数据。

下面我们来看一下怎么写一个Batch，实现**Database.Batchable**接口就可以了:
这个接口有三个方法需要实现：
1. 准备数据
*global (Database.QueryLocator | Iterable<sObject>) start(Database.BatchableContext bc) {}*
这个方法可以返回**QueryLocator**或者**Iterable**对象，注意如果用**Iterable**的话，Soql查询上限是50 000。
2. 执行逻辑
*global void execute(Database.BatchableContext BC, list< sObject> scope){}*
每个Batch执行的时候都会调用这个方法。
3. Finish方法
*global void finish(Database.BatchableContext BC){}*
当所有的Batch执行完后会调用，可能会进行其他的操作，比如生成日志或者发送邮件等操作。

一个简单Batch的实现：
```
global class MyFirstBatch implements Database.Batchable<sObject> {
    public String query;
    global AddPunishmentInformationOpp(String query) {
        this.query =query;
    }

    global Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator(query);
    }

    global void execute(Database.BatchableContext BC, list<Sobject> scope) {
        //执行语句
    }

    global void finish(Database.BatchableContext BC) {
        //发送邮件
    }
}
```
当一个Bacth执行的时候如果需要用到多个Sobject对象怎么处理呢？这时候可以尝试使用** Iterable**定义数据范围，具体实现步骤有三个：
1. 定义迭代器
```
global class CustomIterable implements Iterator<SObject>{    
   List<SObject> sobjectList {get; set;} 
   Integer i {get; set;} 
   public CustomCalculateIterable(){ 
       sobjectList = new List<SObject>();
       List<Account> accList = new List<Account>([select id,name from Account]);
       List<Opportunity> oppList = new List<Opportunity>([select id,name from Opportunity]);
       sobjectList.add(accList);
       sobjectList.add(oppList);
       i = 0; 
   }   
   global boolean hasNext(){ 
       if(i >= sobjectList.size()) 
           return false; 
       else 
           return true; 
   }    
   global SObject next(){ 
   	   // 8 is an arbitrary 
       // constant in this example
       // that represents the 
       // maximum size of the list.
       if(i == 8){ i++; return null;} 
       i=i+1; 
       return sobjectList[i-1]; 
   } 
}
```
2. 定义一个Class返回可迭代对象
```
global class CustomSobjectIterable implements iterable<SObject>{
	 global Iterator<SObject> Iterator(){
      	return new CustomIterable();
     }
}  
```
3. 定义Batch实现类，调用上面的CustomSobjectIterable类
```
global class MyFirstBatch implements Database.Batchable<SObject>{
    global Iterable<Sobject> start(Database.batchableContext info){ 
       	return new CustomSobjectIterable();
    }                   
    global void execute(Database.BatchableContext bc,List<SObject> scope){    
     	for(SObject so : scope){ 
            String objectType = String.valueof(so.getSObjectType());
            if(objectType == 'Opportunity'){
                //code 
            }
            if(objectType = 'Account'){
                //code 
            }
        }
    }
    global void finish(Database.BatchableContext bc){           
    }
}
```
到现在为止，我们可以通过实现Batch做了一些事情，那么怎么执行Batch呢？首先明白的是Batch是异步执行的，那么Bacth执行完后，可能不会很快的看到结果，不过你可以通过**Apex Jobs**来查看执行状态；Batch执行可以在控制台调用下面的语句：
```
MyFirstBatch b = new MyFirstBatch();
database.executeBatch(b);
```
正如你所看到的，我们可以通过上述语句来调用Batch，那么我们同样的可以在Trigger、普通的类方法中来调用Batch；下面我想着重说一下在定时任务中调用Batch，实际项目中常常会有定时发邮件、生成日志、清理数据的工作，那么这些任务常常伴随的数据量是比较大的，在Salesforce中只能通过Batch来操作，那怎样定时来执行这个操作呢？有三个步骤：
1. 定义Scheduler
想要一个任务定时执行，必须得先实现一个Scheduler类，这个类需要实现**Schedulable**接口，如下所示：
```
global class MyFirstScheduler implements Schedulable {
    global void execute(SchedulableContext sc) {
        //code         
    }
}
```
2. Scheduler中调用Batch
```
global class MyFirstScheduler implements Schedulable {
    global void execute(SchedulableContext sc) {
        Database.executeBatch(new MyFirstBatch);
    }
}
```
3. 定义Scheduler执行频率
Scheduler的执行可以通过配置来实现，也可以通过**system.schedule**方法来实现。

到现在为止，我们已经知道实现Bacth和Scheduler，并且也在Scheduler中调用了Batch，这种方式在实际项目中用到的也比较多，希望你能会有所收获。
# REST和SOAP
为了能够与其他系统进行集成，Salesforce提供了Rest和Soap两种API。那么什么是REST?什么是SOAP呢？
- REST: The Representational State Transfer，这是轻量级的系统集成模式，所有服务抽象为可访问资源，调用者通过URL就可以和系统发生交互（增、删、改、查）。
- SOAP: The Simple Object Access Protocol, 看字面意思是数据交互协议，用XML定义数据格式；在使用Web Service的过程中，需要获取到服务资源的WSDL(Web Service Description Language)文件，然后通过POST请求获取服务器应答。

这两种方式的比较可以参考[WebService的两种方式Soap和Rest比较](https://www.cnblogs.com/yourshj/p/5968871.html)。如果你想对Salesforce的API想有更多的了解，推荐大家做一下[Lightning Platform API Basics](https://trailhead.salesforce.com/content/learn/modules/api_basics)模块；当你开发完接口之后，需要用第三方工具进行测试，之前我写过一篇用[使用Postman对Salesforce进行接口测试](https://www.jianshu.com/p/2f0face794f1)，如果要测试SOAP的话，可以使用[SOAP UI](https://www.soapui.org/)进行验证，参考[Salesforce中SOAP的实践](https://www.jianshu.com/p/1074d1f1bd68)。

至此，Salesforce开发教程系列终于完成了，从第一篇对Salesforce的入门了解，第二篇的开发实践，今天这篇内容算是对第二篇的补充；希望通过这个开发教程系列可以帮助到你，文章中如有问题请尽情指出。再次感谢你的阅读，感谢支持！