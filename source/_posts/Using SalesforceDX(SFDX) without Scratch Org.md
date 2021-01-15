---
title: Using SalesforceDX(SFDX) without Scratch Org
catalog: true
date: 2019-06-09 11:35:42
tags:
- SFDX
categories:
- Salesforce
---
In this blog, I will explain steps on how to use SFDX in Developer, Sandbox or Production Orgs. Hope this article could help you. Let's get started.
#Prerequisite
1. [VSCode install]([https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)
)
2. [Salesforce CLI Tool]([https://developer.salesforce.com/tools/sfdxcli](https://developer.salesforce.com/tools/sfdxcli)
)
3. [Salesforce Extension Tools]([https://developer.salesforce.com/tools/extension_vscode](https://developer.salesforce.com/tools/extension_vscode)
)

Please install the above tool. if you want to continue the below steps. I also suggest you take some time on the [document]([https://forcedotcom.github.io/salesforcedx-vscode/](https://forcedotcom.github.io/salesforcedx-vscode/)
). 
#OAuth a Salesforce Org
Because of the salesforce development is not on premises. Whatever you use the eclipse or sublime. you always need to authorize an account of the SF. And the same method applies to the VSCode. How can I OAuth an Org through VSCode? You just input this command.
```
sfdx force:auth:web:login -a <setaliasname>  -r <instanceurl> 
 ```
for example. I want to authorize my developer org. I will open my terminal input the below command.
```
sfdx force:auth:web:login -a BubbaTest -r https://login.salesforce.com
```
if you have executed the auth command. And get a response like the below picture. ![success signal](https://upload-images.jianshu.io/upload_images/14975804-fe442cdc55629fd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Congratulations.  Next, we could create a project based on the OAuth process. Before entering into the next step. There are two proposals.
1. you should pay attention to the instance URL parameters. if you don't know. Please open your Org. Search my domain in setting side. 
2. When my machine authorizes an Org. The essence is that my local machine saved a access token and refresh token. It will promise communication with salesforce.if you have enough. I think you could find the file in .sfdx folder below.
![configure json file](https://upload-images.jianshu.io/upload_images/14975804-1f3b253b61f35164.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#Create Project with vs code
This step will create a sfdx project with command.
```
sfdx force:project:create -n <projectName>  -x
```
for example. I will create a project with the below command.
```
sfdx force:project:create -n TestProject -x 
```
![project file catalog](https://upload-images.jianshu.io/upload_images/14975804-f0b0373aaa351214.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Let's open it in vs code. you should pay attention to the manifest folder. There is a package.xml file. We will use it in the next step. You may confuse that I only create a local project without Salesforce Org information. How I can develop use this project. Great. Before we start to develop. You should execute set default Org info in vs code.
![set default org](https://upload-images.jianshu.io/upload_images/14975804-9fb114f22b42af7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![sfdx configure](https://upload-images.jianshu.io/upload_images/14975804-b05ea5a74c109b01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
After you have configured the Org info with the project. We can do anything on the local machine. There is no difference between on salesforce console and local machine.
#Rerieve Code from Org
This step focuses on the retrieve code from our authorization org. There are mainly three methods.
1. from the local's metadata file
as for the BubbaTest project. We could execute the command to retrieve the code.
```
sfdx force:source:retrieve -x manifest/package.xml 
```
This way may need too much time. So I always suggest you apply the below methods.
2. from local's source code
Our code exists in the force-app/main/default folder. Before we update the function. And the local code may different from remote code. So we need to refresh it.
![code location](https://upload-images.jianshu.io/upload_images/14975804-8a334aa387f291ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
sfdx force:source:retrieve -p force-app/main/default 
sfdx force:source:retrieve -p force-app/main/default/lwc
```
This way used normally when you want to refresh your local code.  But if your local code does not include the remote file. You should use the third way.
3. specific the metadata type or class name
This way show more advantage compare the above two ways. Because you specify the file. That means there needs less time.  And local will exit less code related to your development function.
```
sfdx force:source:retrieve -m ApexClas
sfdx force:source:retrieve -m ApexClass:MyApexClass
sfdx force:source:retrieve -m "ApexClass, Profile:Content Experience Profile"
```
if you have no idea on salesforce metadata type. Please reference this [document](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_meta.meta/api_meta/meta_types_list.htm)
#Deploy Code to Org
After your development complete.  When you deploy local code to remote. There are similar ways to the retrieved code.
```
sfdx force:source:deploy -m ApexClas
sfdx force:source:deploy -m ApexClass:MyApexClass
sfdx force:source:deploy -m "ApexClass, Profile:Content Experience Profile"
```
if you want to retrieve or deploy code in production. Please use the force:mdapi command.
```
sfdx force:mdapi:deploy -m ApexClas
sfdx force:mdapi:retrieve -m ApexClas
```
#Conclusion
Now. We could use the vs code to complete daily development through the command line. Including authorization a new Org、retrieve code or deploy code. And if you don't know how to create a class or create a trigger, Please reference this [document]([https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference_force_apex.htm#cli_reference_class_create](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference_force_apex.htm#cli_reference_class_create)
). Because of the Salesforce extension have integrated most of the CLI. you can just complete these operations through simple UI. Next blog will continue the development with vs code.  if you have any question about the content. Please give me comments.