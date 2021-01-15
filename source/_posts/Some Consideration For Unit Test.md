---
title: Some Consideration For Unit Test
catalog: true
date: 2019-11-25 17:37:49
tags:
- Unit Test
categories:
- Salesforce
---
I wrote an article about the [Unit Test for Salesforce](https://www.jianshu.com/p/f9e10ce1cc1f). If you haven't read this. Recommend you strongly. Today. Let's see some scenarios in the Unit Test. This article can be a supplement for the previous article.
# Common Scenarios
## How can access the private variable or method of tested Class
As we all known. The class or method or variable has the access modifiers(private、public、global). Here is class A. 
```
public class A {
    public static Integer subtraction(Integer a, Integer b){
        return addition(a,-b);
    }
    private static Integer addition(Integer a, Integer b){
        return a + b;
    }
}
```
And the subtraction method is a public modifier. The addition method is a private modifier. There is a question. if I don't update the modifier. How can I test the addition method in Unit Test? Salesforce supply a annotate **TestVisible**, if you annotate the private method or variable with Test Visible. You can access these attributes or methods in UT. Before you add this annotation. if you want to invoke this private method. you will get the below error:
> Method is not visible: Integer A.addition(Integer, Integer)

After we add the annotation like this:
```
@TestVisible
private static Integer addition(Integer a, Integer b){...}
```
```
@isTest
private class ATest {
    @isTest
    static void  additionTest() {
        Integer sum = A.addition(2,5);
        system.assert(7==sum,'Error Returned!');
    }
}
```
We can run the test method successfully. Your code shouldn't include many of this annotation.  Unfortunately. if you are in this situation. Please consider refactoring your code.
## Which organization's data can be accessed in the test method
It's better to prepare our own test data in the Unit Test. That meant that the test data shouldn't depend on the specific organization. But everything has an exception. you might do the below things:
- Access the Org's profile
- SOQL the Object's record type.

Do I need to create my own profile or record type? The answer is no. Firstly. you can't create these objects in code context. Secondly. You can use the existed data. By default. In your unit test. These metadata of your organization can be accessed.
- User、Profile、Organization、RecordType
- AsyncApexJob、CronTrigger
- ApexClass、ApexTrigger、ApexComponent、ApexPage
So if you want to use the above data. Just use it directly. I want to remind you that when you using the user data in UT. Please be careful. Your unit test may be failed in another environment. An alternative solution is that you could use the  **System.runas()**. if you are interested in this method. please see the detail by this [link](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_tools_runas.htm)
## New Custom setting data in the test method
When you want to cache some data in Salesforce. The custom setting is your solution. There are two types of custom settings. 
- List custom settings
![List Test](https://upload-images.jianshu.io/upload_images/14975804-722ee94d70b30fff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- Hierarchy custom settings
![Hierarchy Test](https://upload-images.jianshu.io/upload_images/14975804-cb47be5352510a4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


The main difference is that whether your data various for specific profiles and users.  Now. I have set a value for the custom setting. Let's  access the data in the Unit Test:
```
@isTest
static void customSettingTest(){
    Map<String,ListTest__c> listTestData = ListTest__c.getAll();
    system.debug(listTestData.size());
    HierarchyTest__c hierarchyTestData = HierarchyTest__c.getOrgDefaults();
    system.debug(hierarchyTestData);
}
```
>DEBUG|0
DEBUG|HierarchyTest__c:{}

As you can see. There is no data response. So you can use the below statement to insert custom setting data in UT:
```
@testSetup 
static void setUpCustomSetting() {
    // 1. List Custom Setting
    ListTest__c listCTTest= new ListTest__c ();
    listCTTest.Name= 'Test';
    insert listCTTest;
    //2. Hierarchy Custom Setting 
    HierarchyTest__c  hierarchyTest= HierarchyTest__c.getOrgDefaults();
    hierarchyTest.Name__c= 'Test';
    upsert hierarchyTest HierarchyTest__c.Id;     
}
```
Let's execute the UT again:
>DEBUG|1
DEBUG|HierarchyTest__c:{Id=a0A3i000001xPmr...IsDeleted=false,Name__c=Test}
# Conclusion
When we write a Unit Test. There are may some unexpected results. Whether it is a DML exception or the data is null. Please try to follow the [best unit test practice](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_best_practices.htm). Today. I list some cases in daily development. Hopefully. It can help you. Also. Welcome your advice and comment.