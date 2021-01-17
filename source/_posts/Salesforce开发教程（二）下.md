---
title: Salesforce开发教程（二）下
catalog: true
date: 2019-05-04 10:30:32
top: 970
tags: 
- Salesforce
categories:
- Salesforce
---
## 2.6 Trigger
&emsp;&emsp;Salesforce中触发器的使用，能够帮助开发人员在用户保存、修改、删除、恢复数据的时候，执行一些Apex代码；在传统开发过程中，数据库中触发器的编写容易引发一些意想不到的问题，所以通常会避开直接编写触发器，更常用的做法是将要在触发器写的逻辑写到Controller层；那么在Salesforce开发过程中，为什么会提到触发器开发呢?原因在于Salesforce的Model层与Controller层是集成的，比如用户点击保存按钮（系统标准按钮）后，开发人员无法获取到从保存开始到数据保存到数据库的上下文，这时候我们想在保存某条记录的时候，需要做一些逻辑判断，如果没有触发器，这些需求是没法实现的。接下来我们来讲讲触发器开发。
### 2.6.1 Trigger类型
&emsp;&emsp;Trigger大体可以分为两类：
&emsp;&emsp;1、Before Triggers，Before Triggers是在DML（insert、update、delete、merge、upsert、undelete）数据之前，也就是说数据还没有Commit到数据库，我们可以在这个时间点编写Apex，常用的场景有验证数据合法性、验证业务场景等。
**注意在Before Triggers中如果删除或者修改触发该触发器的当前记录，系统会抛异常。**
&emsp;&emsp;2、After Triggers，After Trigger是在DML数据之后，指的是数据库数据已经发生变化后，执行相应的Apex，常用的场景有关联对象更新、调用异步方法等。
&emsp;&emsp;我们在Salesforce写的Trigger默认是批处理的，比如Dataloader批量导入数据也会触发对应的触发器，所以在写代码逻辑的时候，应当考虑处理多条数据的情况。
### 2.6.2 Trigger语法
&emsp;&emsp;声明Trigger的包含四部分信息：
&emsp;&emsp;1、使用关键字Trigger（What）
&emsp;&emsp;2、命名自己Trigger的名称，并指明这个Trigger作用于哪个对象ObjectName(Who)
&emsp;&emsp;3、定义执行代码的时间点（When）,比如before insert、before update、before delete、after insert、after update、after delete、after undelete等
&emsp;&emsp;4、编写触发器Apex代码
```
trigger TriggerName on ObjectName (trigger_events) {
   code_block
}
```

### 2.6.3 举例说明
&emsp;&emsp;下面我们将写一个作用于Account的触发器，该触发器会检测新插入的Account是否包含Description信息，如果该值为空，则系统自动更新为New。按照Trigger语法的介绍。我们至少应该先定义以下内容：
```
trigger MyFirstTriggerForAccount on Account(trigger_events){
   code_block
}
```
&emsp;&emsp;接下来我们思考的事情可能是这里的trigger_events，我想在做这件事情之前，先来看一下Salesforce的执行顺序：
![Salesforce执行顺序](ExecuteOrder.png)

&emsp;&emsp;在这里，注意一下**Apex(before) triggers**、**Apex(after) triggers**这两个事件。在这两个事件中间，数据会被保存到数据库（并没有committed)，在after triggers事件之后，数据将被Commit到数据库。上面MyFirstTriggerForAccount要定义为before event还是after event?假设我们定义为after event，那么数据库操作将会执行两次，第一次保存我们新建的Account的基本信息，第二次将会修改我们的Description信息。如果用before event呢，数据库只会执行一次保存操作，所以我们将采用before insert，因为只在插入数据的时候才触发该逻辑块。我们的触发器将进一步完善。
```
trigger MyFirstTriggerForAccount on Account(before insert){
   code_block
}
```
&emsp;&emsp;code_block编写完成后，将会是下面的样子：
```
trigger MyFirstTriggerForAccount on Account(before insert){
    for(Account a : Trigger.New) {
        if(a.Description == null || a.Description == ''){
            a.Description =  'New';
        }
    }   
}
```
## 2.7 这一节总结
&emsp;&emsp;这一节分为三篇文章，持续介绍了初识Salesforce、Apex的语法与使用、Visualforce Page的开发、以及Trigger的使用等内容。基本覆盖了在Classic模式下面开发Salesforce的常见内容。相比较于传统Java、Python开发，在自定义Salesforce开发过程中，可能不会有复杂的技术与算法，但是总有一些问题会让你抓耳挠腮。更多的场景可能来自于Salesforce天生的一些特性。作为一个多租户平台系统，在资源有限的情况下，能够平稳高效的服务于全球各行业、各领域的上万家客户，给我的个人感受是Salesforce一直在进步，除了在不断完善自己的产品和开发生态外，还能够紧随市场变化创造新的产品。这样一个有生命力的平台，纵然它有诸多的限制，我想还是值得我们去拥抱并且热爱它。