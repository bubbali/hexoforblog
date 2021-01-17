---
title: VS Code Common Tips for Salesforce developer
catalog: true
date: 2019-10-02 21:14:06
tags:
- Salesforce
categories:
- Salesforce
---
As we all known. The vs code tool is common during the development of Salesforce. You are must familiar with the vs code development. Today. I want to share some tips for you. if you have any questions or comment please add comments for me. There are mainly three parts of all the below content. Let's get the start.
# Auth Process
1. Auth an org for development
if you want to develop code for Salesforce. The first thing is that your local machine must have a project linked to a remote org. There are two things you need to do.
- Auth an org
- Create a project for your org
These two things don't come into anything.  you can do anything firstly. I will auth an org firstly. after auth an org successfully. We can create a project based on the specifically org. In this section.  Let's auth an org.
```
sfdx force:auth:web:login -a BubbaTest  -r https://login.salesforce.com //instance url
```
![Auth step](https://upload-images.jianshu.io/upload_images/14975804-b757e4cac7bee540.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Auth success](https://upload-images.jianshu.io/upload_images/14975804-bbf33c0136e984e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
After you auth the org successfully. you can check by the command:
```
sfdx force:org:list
```
Importantly. Not only you can develop based on the authed org,  but also you can open the org by using the command:
```
sfdx force:org:open -u alias
```
It's amazing. Why we can do this? That's because of the access token stored in our local machine. you can find the answer in the next section.
2. Local file Configure
Once we auth an org successfully. We can use it permanently unless you revoke it. Let's see what happened after the auth process. After you auth the org. The org of the definition file will be stored in the root/.sfdx direction. you can find a folder named .sfdx in your root direction. open it by vscode. 
![Org's definition](https://upload-images.jianshu.io/upload_images/14975804-82146d80855cd027.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
You should pay attention to the access token、refresh token and client id. Through this information. The local machine could request a connection with the remote server resource. for example. you can request any rest api source. and not limit development. 

# Developer Process
1. Create local project
Before we start development. The second thing is that you should create a project.
```
sfdx force:create:project  -n projectName -x //Generates a default manifest (package.xml) for fetching Apex, Visualforce, Lightning web components, Aura components, and static resources.
```
![Create project](https://upload-images.jianshu.io/upload_images/14975804-39adec5a07da5ec5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
We open it in vscode. There is no connection with the remote org currently. So we will set an org for this project.
2. Set Default Org
```
sfdx force:config:set defaultusername=BubbaTest
```
Also, you can use **command + p**. And input **> set a default org**. After this operation. you will see the below info.
![Set default org](https://upload-images.jianshu.io/upload_images/14975804-bfad2c2373d85dff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
The reparation work has completed. We can start the development next step.
3. Retrieve the source & Deploy source
- Retrieve code from remote org
```
sfdx force:source:retrieve -x package.xml location
sfdx force:source:retrieve - p code location
sfdx force:source:retrieve -m "ApexClass:myclass"
```
- Deploy code to remote org
```
sfdx force:source:deploy -x package.xml location
sfdx force:source:deploy - p code location
sfdx force:source:deploy -m "ApexClass:myclass"
```
if you don't like the command. also, you can use the shortcut integrated in the Salesforce Extension Pack.
![Development](https://upload-images.jianshu.io/upload_images/14975804-4554ec0c7dda0dd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. Unit Test code converge
Now. we have completed the development test and unit test. As for Salesforce development. The unit test coverage must above 75%. you can execute the unit test in the developer console and check the coverage. Now. an alternate way you can do it by vscode.
- Add the below statement to the workspace setting in vscode.
```
"salesforcedx-vscode-core.retrieve-test-code-coverage": true
```
![Show unit test converage](https://upload-images.jianshu.io/upload_images/14975804-f1813c1fd887a8da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- Run the test command
```
sfdx force:apex:test:run --tests MyTest.myTest --loglevel error --codecoverage
```
![Run the unit test](https://upload-images.jianshu.io/upload_images/14975804-66e768e301f88905.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Click the icon
![show coverage](https://upload-images.jianshu.io/upload_images/14975804-cc189275b235a3d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
5. Compare with the remote org's file
There are some conflicts with the remote org. So we need to compare the specific file.
 ![Compare menu](https://upload-images.jianshu.io/upload_images/14975804-c37d696e3cd3578b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Others 
1. Execute anonymous statement
- Create a file in the local project. Suffix with .apex
![Create a file with mytest.apex](https://upload-images.jianshu.io/upload_images/14975804-9c5d5cfb6df8f189.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- **command + p** and input >**execute anonymous apex**
![Execute anonymous apex](https://upload-images.jianshu.io/upload_images/14975804-a9195613555591fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. Execute SQQL command 
The operation is similar to execute an anonymous statement. 
- Create a file in the local project. Suffix with .apex or any class file
- **command + p** and input >**execute soql query**
![Execute soql query](https://upload-images.jianshu.io/upload_images/14975804-e0574156442c690c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)