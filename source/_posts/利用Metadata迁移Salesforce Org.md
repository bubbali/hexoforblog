---
title: 利用Metadata迁移Salesforce Org
catalog: true
date: 2018-11-20 02:34:17
tags:
- Salesforce
categories:
- Salesforce
---
&emsp;&emsp;随着Metadata API v13的发布，在部署系统的时候，除了使用常用的更改集的方式，还可以直接利用Salesforce.com的配置信息（XML metadata files）部署到任意目标环境。

      使用工具： Force.com IDE 或者 Ant Migration Tool

      Force.com IDE操作步骤：

      1、安装Eclipse以及Force.com IDE Plug-In，参考地址：Install the Force.com IDE Plug-In

      2、新建force.com项目，连接源org，将metadata下载到本地

      3、将metadata deploy到目标org

           部署顺序（依据原则被依赖资源先部署）：

           3.1、 groups、roles、static resources、documents

           3.2、 objects、class、component、trigger、pages、quickAction

           3.3、tabs、emails、letterhead、layouts、homePagesLayouts、application

           3.4、approvalProcesses、workflows、flow

           3.5、report Type、report、dashboard、profiles

      注意事项：

      1、如果有托管包，需要手动重新安装

      2 、如果需要 local fields或者 multicurrency fields 功能，需要提前向salesforce提case，参考：Enable 'Local Name' fields


常见错误：

1、Invalid fullName, must end in a custom suffix ( for ex. __c )

解决方法：如果是标准对象，需要启用相应的功能，比如ForecastingQuota、OpportunityTeamMember等，否则需要确定自定义对象是否在部署列表中；如果需要手动在目标org添加自定义对象，只需要添加对象基本定义信息（注意目标org和源org配置保持一致，比如允许搜索是否打勾等），不需要手动添加字段，重新部署就ok了。

2、Could not resolve standard field's name.

解决方法：因为salesforce org版本信息可能不一致，导致一些标准字段发生变化，找到发生冲突的字段，在业务中没用到的情况下，需要将其在metadata中移除。

3、The global picklist cannot be resolved 或者 Picklist value: 已启用 in picklist: Status not found

解决方法：手动添加到目标org中。

4、Can't specify an external sharing model for Contact

解决方法：查看共享设置是否启用外部共享模式。

5、Custom object does not have history tracking enabled

解决方法：目标org中需要设置对象跟踪。

6、No package.xml found，Bad file:Invalid byte 1 of 1-byte UTF-8 sequence.

解决方法：在eclipse.ini末尾下增加  -Dfile.encoding=UTF-8。

7、In field: recipient - no User named  username@org.com found

解决方法：部署审批流的过程中，很容易出现该错误，修改metadata需要用目标org中用户名替换。