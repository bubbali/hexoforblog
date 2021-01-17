---
title: Salesforce开发教程（二）中
catalog: true
date: 2018-12-17 14:50:00
top: 980
tags:
- Salesforce
categories:
- Salesforce
---
&emsp;&emsp;接着上一节的内容继续开始吧！
## 2.4 Page
&emsp;&emsp;Visual Page类似于普通Web Page，包含的内容不限于HTML、CSS、JS等资源。我们写的Visualforce Page存储在服务器端，当用户通过url访问的时候，会被渲染成普通的Web Page,供需求用户访问。
&emsp;&emsp;这里大家可能会想个问题，为什么不直接使用HTML呢，其实细想一下，HTML是静态资源，而当页面需要动态加载数据的时候，就会不好使了，所以你看Visualforce Page更像Java开发中的JSP（Java Server Pages）。在这种情况下，页面需要由服务器端进行编译转换然后提供Web Page。接下来的问题就是动态的数据或者屏幕触发方法是从哪里来的呢？Salesforce中提供的方案是一个页面需要绑定一个Controller Apex 类，除此以外，还可以通过继承父类获取更多的属性，下面我们来看一下Page运行原理的示意图。

### 2.4.1 Page运行原理
![VisualforcePage](VFPage.png)
&emsp;&emsp;上图为Page的加载过程，大体可以分为四个步骤：
&emsp;&emsp;1、客户端发起URL请求，.../apex/MyPage
&emsp;&emsp;2、Salesforce会根据请求地址执行相应的页面记录（这里需要注意的是，SF作为云服务平台，那么它是怎么找到当前用户访问的资源呢，原因就在于申请的Organization是有Id记录，所以通过OrgId过滤可以命中自己所需的资源）
&emsp;&emsp;3、上面提到当访问Visualforce Page时候，后台服务器会进行编译，所以当服务器看到下面这句话的时候
```
<apex:page controller="MyPageController">
```
服务器且会根据页面绑定的Apex类执行相应的逻辑，包括但不限于数据库DML操作、访问Web资源等操作。
&emsp;&emsp;4、当以上操作执行完后，后台服务器会Print一个普通的HTML页面，供用户浏览。
### 2.4.2 Page 组成部分
&emsp;&emsp;基本的前端元素是不可少的（HTML、CSS、JS），Salesforce还提供了预制的[标准组件](https://developer.salesforce.com/docs/atlas.en-us.216.0.pages.meta/pages/pages_compref.htm)供开发人员使用，然后当用户访问的时候，这些组件会被解释为基本的前端元素。
### 2.4.3 Page Controller
&emsp;&emsp;Visualforce Page开发是传统的MVC模式，页面通过与绑定的Controller进行数据、方法交互，系统内所有的标准对象与自定义对象都有一个标准的Controler，标准的Controller包含基本的保存、删除等方法，在开发过程中，如果你不仅仅需要标准Controler中的方法，更多的时候需要一些自定义的方法，那么可以通过继承标准类来添加个性化的方法。
&emsp;&emsp;一般情况下我们自定义自己的类和页面来满足业务需求。类中包含页面所需的字段信息、函数方法供页面调用。
### 2.4.4 Page 表达式
&emsp;&emsp;Visualforce Page可以显示从数据库、Web Service等源检索出的数据，这些动态的数据可以通过页面标签加载出来，分为全局变量、计算表达式以及页面属性等，统称为Viualforce 表达式，语法形式如下：
```
{! expression }
```
&emsp;&emsp;可以将以上的表达是理解为值的引用*{!reference}*，当页面加载的时候，服务器端会自定替换该变量（空格不计）。引用的值可以是基本数据类型（数值、字符串、布尔、日期\日期时间等类型）、SObject（标准对象、自定义对象）、自定义类（MyClass）、Controller方法名等。
&emsp;&emsp;其中常用的全局变量包含：Action（Salesforce标准动作，比如跳转对象主页、创建、编辑、删除记录等）、Label、Profile、User（当前登陆用户信息等）。详细列表参见：[全局变量](https://developer.salesforce.com/docs/atlas.en-us.216.0.pages.meta/pages/pages_variables_global.htm)，注意语法使用，比如显示登陆用户信息：
```
{!$User.FirstName}
```
&emsp;&emsp;针对计算表达式，支持[表达式运算符号](https://developer.salesforce.com/docs/atlas.en-us.216.0.pages.meta/pages/pages_variables_functions.htm)更方便的操作数据，Visualforce Page支持字符串拼接、数据函数表达式及日期时间显示等[函数功能](https://developer.salesforce.com/docs/atlas.en-us.216.0.pages.meta/pages/pages_variables_functions.htm)，除此以外，还提供了条件表达式*（Conditional formula Expression）*,比如下面的IF表达式，包含包含三个参数IF(logical_test, value_if_true, value_if_false)
检查条件是否为真，如果条件为 TRUE 则返回一个值；如果条件为 FALSE 则返回另一个值:
```
<p>{! IF( CONTAINS('salesforce.com','force.com'), 
     'Success', 'Failed') }</p>
<p>{! IF( DAY(TODAY()) < 18, 
     'Before the 18th', 'The 18th or after') }</p>
```
&emsp;&emsp;如果上面的功能还满足不了需求，就需要将页面与Controller绑定，由Controller处理自定义逻辑来提供前端需要的数据。
&emsp;&emsp;总之呢，VisualForce表达式将包含字面量、变量、表达式运算、内置函数的一个集合解析成单一的数值，供前端用户使用。
### 2.4.5 Page的Controller方法
&emsp;&emsp;Controller属性和方法是供Visualforce Page使用，页面的元素无非可以分为三类：显示数据、输入数据、执行操作，那么映射到Controller中分别对应：Gettter方法、Setter方法、Action方法，下面我们来简单说明一下这三者的使用说明：
&emsp;&emsp;Getter方法会返回一个数值给到Page页面，比如在[上一节](https://www.jianshu.com/p/af63b92f3b93)提到的动态Page执行的例子，当页面解析到下面这条语句的时候，其实会去调用与这个页面绑定的Controller中的getVariable方法，这个例子中*{!}*中的Variable是myAccounts，所以其实调用的是MyAccountController中的getMyAccounts方法（不区分大小写），如果找不到这个方法，会提示你找不到该变量信息。
```
<apex:pageBlockTable value = "{!myAccounts}" var = "account">
```
&emsp;&emsp;与Getter方法相对应的方法是Setter方法，Setter方法允许用户在页面填写的表单信息赋值Controller中的变量中，一旦检测到用户页面的动作发生变化（比如保存、删除数据等操作），该方法会自动先执行，Controller中的方法名称为setVariable，如果get、set方法仅仅是简单的赋值，可以简单的用以下方式申明：
```
public  Account acc{set; get;}
```
&emsp;&emsp;除了变量的获取和赋值，还必须满足事件的触发执行，让莪们先来修改一下上节中的动态Page：
```
public class MyAccountController{
    public Account account { get; private set; }
    public MyAccountController() {
        Id id = ApexPages.currentPage().getParameters().get('id');
        account = (id == null) ? new Account() : 
            [SELECT Name,Phone,Industry FROM Account WHERE Id = :id];
    }
     public PageReference save() {
        try {
            upsert(account);
        } catch(System.DMLException e) {
            ApexPages.addMessages(e);
            return null;
        }
        //  After successful Save, navigate to the default view page
        PageReference redirectSuccess = new ApexPages.StandardController(Account).view();
        return (redirectSuccess);
    }    
    public List<Account> getmyAccounts(){
       List<Account> accList = new List<Account>([select Id,AccountNumber,Name from account where OwnerId =:userinfo.getUserId()]);
       return accList;
    }
}
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;MyAccountController.cls
```
<apex:page controller="MyAccountController">
    <apex:form>
        <apex:pageBlock>
            <apex:pageMessages/>
            <apex:pageBlockSection>
                <apex:inputField value="{!Account.name}"/>
                <apex:inputField value="{!Account.industry}"/>
                <apex:inputField value="{!Account.Phone}"/>                       
            </apex:pageBlockSection>
            <apex:pageBlockButtons location="bottom">
                <apex:commandButton value="Save" action="{!save}"/>
            </apex:pageBlockButtons>
        </apex:pageBlock>
        <apex:pageBlock>
            <apex:pageBlockTable value = "{!myAccounts}" var = "account">
                <apex:column  value = "{!account.AccountNumber}"/>
                <apex:column  value = "{!account.Name}" />              
            </apex:pageBlockTable>
        </apex:pageBlock>
    </apex:form>
</apex:page>
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;MyAccountPage.page
&emsp;&emsp;之前的功能仅是显示了所有人是当前登陆用户的客户，现在添加了一个PageBolck供用户在该页面添加客户：
![预览结果](ShowResult.png)
&emsp;&emsp;当用户输入客户姓名、行业等信息之后，点击保存按钮（事件触发），执行Controller中的save方法，跳转到刚创建的客户记录详细页面，这个过程有两个问题值得注意：
1、当用户点击Save按钮后，执行了后台的Controler方法，那么表单什么时候提交的？其实在你点击Save方法时，sf会先执行Set方法，然后再执行你调用的方法。
2、VF页中支持执行后台方法的标签有哪些呢？
&emsp;&emsp;提交表单、表单计算等操作使用<apex:commandButton> 
```
<apex:commandButton action="{!save}" value="Save" id="theButton"/>
```
&emsp;&emsp;刷新页面、导航栏指引等使用<apex:commandLink>
```
<apex:commandLink action="{!refresh}" value="Refresh" id="theCommandLink"/>
```
&emsp;&emsp;需要在JS中调用Controller方法时，使用<apex:actionFunction> ,JS中调用的方法名称是*name*的值。
```
 <apex:actionFunction name="clearList" action="{!clearlist}" reRender="form" />
```
### 2.4.6 举例说明
&emsp;&emsp;结合[上一节](https://www.jianshu.com/p/af63b92f3b93)的内容，其实我们已经可以通过SF平台做一些自己喜欢的事情了。接下来我们来完善一下上面的例子，第一阶段呢我们在浏览器端展示了自己的客户，第二阶段我们可以创建自己的客户，并且保存到了数据库，这一阶段呢我们将需要在输入客户信息点击保存Save按钮后，刷新客户列表，并且将填写的表单置空。
&emsp;&emsp;1、改造Save方法，保存数据后，执行数据库插入动作，并且需要将account变量重新赋值为空。
```
     public void save() {
        try {
            upsert(account);
            account = new Account();
        } catch(System.DMLException e) {
            ApexPages.addMessages(e);
        }        
    }  
```
&emsp;&emsp;2、局部刷新第二个PageBlock内容列表，修改VF页面如下：
```
        <apex:pageBlock>
            <apex:pageMessages/>
            <apex:pageBlockSection>
                <apex:inputField value="{!Account.name}"/>
                <apex:inputField value="{!Account.industry}"/>
                <apex:inputField value="{!Account.Phone}"/>                       
            </apex:pageBlockSection>
            <apex:pageBlockButtons location="bottom">
                <apex:commandButton value="Save" action="{!save}" reRender = "myAccounts"/>
            </apex:pageBlockButtons>
        </apex:pageBlock>
        <apex:pageBlock id = "myAccounts">
            <apex:pageBlockTable value = "{!myAccounts}" var = "account">
                <apex:column  value = "{!account.AccountNumber}"/>
                <apex:column  value = "{!account.Name}" />              
            </apex:pageBlockTable>
        </apex:pageBlock>
```
&emsp;&emsp;着重强调一下reRender 属性，reRender作用是刷新局部表单数据，局部指的是DOM中的Id，通过传递给reRender参数Id，然后起到局部刷新的作用，比如上面的例子，定义了第二个PageBlock IdmyAccounts，所以当我执行完Save方法后，会自动触发该模块数据更新（实则利用Ajax重新调用了一遍getmyAccounts方法）,与这个属性可能还经常一块出现的是Rendered 、renderAs，感兴趣的同学可以参考： [Difference between Render, reRender and renderAs](https://sfdcpanther.wordpress.com/2017/10/04/difference-between-render-rerender-and-renderas/)
## 2.5 SOQL&DML
&emsp;&emsp;在开发过程中，不可缺少的一个过程是程序需要和数据库进行交互操作（CRUD），在Salesforce平台上，查询数据常用的是SOQL(Salesforce Object Query Language)
![SOQL提取数据的过程](SOQLRetrieve.png)
&emsp;&emsp;语法与SQL类似，支持Where、Group、Order by、Like等语法，示例：
```
SELECT Id, Name
FROM Account
WHERE Name  Like '%Sandy' 
```
&emsp;&emsp;但是SQL的高级语法不支持，比如左连接、右连接等。其实如果你了解Salesforce[表之间的关系](https://help.salesforce.com/articleView?id=overview_of_custom_object_relationships.htm&type=5)，SQOL自身的子查询、父查询可以满足大部分业务。
![SOQL Is Powerful](SOQLISPowerful.png)
&emsp;&emsp;注意上图分为三个部分，目前只需要看Apex + SOQL部分，通过Apex与SOQL结合，可以查询返回逻辑层需要的所有数据（包括业务数据、配置数据等）。
#### 2.5.1 语法说明
```
SELECT fieldList [subquery][...]
[TYPEOF typeOfField whenExpression[...] elseExpression END][...]
FROM objectType[,...] 
    [USING SCOPE filterScope]
[WHERE conditionExpression]
[WITH [DATA CATEGORY] filteringExpression]
[GROUP BY {fieldGroupByList|ROLLUP (fieldSubtotalGroupByList)|CUBE (fieldSubtotalGroupByList)} 
    [HAVING havingConditionExpression] ] 
[ORDER BY fieldOrderByList {ASC|DESC} [NULLS {FIRST|LAST}] ]
[LIMIT numberOfRowsToReturn]
[OFFSET numberOfRowsToSkip]
[FOR {VIEW  | REFERENCE}[,...] ]
      [ UPDATE {TRACKING|VIEWSTAT}[,...] ]
```
&emsp;&emsp;Select、From、Where、Order by、Limit
&emsp;&emsp;比如查找客户以及客户下面所有的联系人：
```
SELECT Account.Name, (SELECT Contact.LastName FROM Account.Contacts)
FROM Account
```
&emsp;&emsp;注意以上例子返回两个字段：客户名称、客户下面联系人的Lastname，这里用到了子查询；如果添加过滤条件，使用*where*，常用到的[比较运算符](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_comparisonoperators.htm)有*=、!=、>、<、Like*（支持模糊匹配）等以及逻辑符号*And、OR*等；比如上述例子想要返回客户名称为Bubba的列表：
```
SELECT Account.Name, (SELECT Contact.LastName FROM Account.Contacts)
FROM Account
WERE NAME = 'Bubba'
```
&emsp;&emsp;在实际开发中，可能还会用到给SOQL传递一个变量(Inline SOQL)，要将返回的数据存到到List<Account> accList中：
```
String myName = 'Bubba'
List<Account> accList = new List<Account>([SELECT Account.Name, (SELECT Contact.LastName FROM Account.Contacts)
FROM Account
WERE NAME =:myName]);
```
&emsp;&emsp;其他常用到的语句还有Order By、Limit等，通过Order By FieldNameList 来控制返回的结果数据顺序，多个Order By的字段名称用逗号分隔开：
```
SELECT Name
FROM Account
ORDER BY Name DESC,INDUSTRY NULLS LAST
```
&emsp;&emsp;最后的NULLS LAST指示的是如果字段为空的话，在列表的末端返回。
**注意如果查找的字段是下拉列表类型，则返回的值是api名称**。
#### 2.5.2 Group By以及其他
&emsp;&emsp;下面的例子是使用Group By语句，查询系统内名称重复的客户：
```
SELECT Name, Count(Id)
FROM Account
GROUP BY Name
HAVING Count(Id) > 1
```
&emsp;&emsp;上述例子其实也可以不需要Group By，通过单独查询客户返回到accList中，然后通过代码对accList中进行循环处理；但是用Group By之后，我们就不需要进行复杂逻辑处理，最重要的是还可以进行一些[聚合函数](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_agg_functions.htm)计算，常用的有SUM()、Count()、AVG()等。
&emsp;&emsp;除此以外，在SOQL数据查询中，经常会遇到处理时间类型的字段，Salesforce支持时间字面量进行筛选比如*CreatedDate > 2018-12-16T10:00:00-08:00*，还内置了[时间函数](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_date_functions.htm)方便开发人员。
```
SELECT Name FROM Account WHERE CreatedDate > 2018-12-216T10:00:00-08:00
SELECT Amount FROM Opportunity WHERE CALENDAR_YEAR(CreatedDate) = 2018
```
&emsp;&emsp;下一部分将介绍Trigger相关内容，待续。。。