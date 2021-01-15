---
title: Salesforce Administrator考试心得
catalog: true
date: 2019-10-02 18:05:05
tags:
- Administrator
categories:
- Salesforce
---
前段时间刚考完Administrator考试，今天想给大家分享一些经验，希望能够帮助到一些人。
# 考试介绍
为了能够提高Salesforce从业人员的相关技能，Salesforce提供了丰富多彩的学习平台[Trailhead](https://trailhead.salesforce.com/)和各种考试路线；在这里，你会享受到沉浸式的学习体验，不同于传统的照本宣科；它会结合具体的业务场景把新知识技能传授给你，只要投入时间和精力，成长指日可待；针对不同的角色（开发、管理员、顾问、架构师等），Salesforce提供了[不同的学习路径](https://trailhead.salesforce.com/credentials/administratoroverview)，要准备Administrator考试的你，第一步做的事情就是注册一个[Trailhead](https://trailhead.salesforce.com/)账号，然后找到Administrator的考试认证主题，了解一下相关内容。
![各种学习路径](https://upload-images.jianshu.io/upload_images/14975804-b399fcffe0e8eb69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
要想全面的了解Salesforce的相关功能，要想顺利的通过Administrator考试，至今还没有哪个平台比Trailhead更好；针对Administrator认证呢，主要针对以后想从事Salesforce管理员工作的同学，Administrator认证的备考过程中，你会了解到Salesforce的日常配置和各个Cloud模块内容，可能不会特别深入，但无疑会对你今后的工作起到至关重要的作用。
Administrator考试分为两个阶段，第一个阶段是Salesforce Certified Administrator,当你通过初级管理员认证后，可以进入下一个阶段，认证Salesforce Certified Advanced Administrator。今天专注于初级管理员认证。下面我会从以下考试大纲介绍、备考过程两方面介绍一下相关内容。
# 考试大纲
Administrator考试包含60道选择题（包含多选题），正确率达到65%（含）以上可以通过考试，意味着39道题正确，就可以顺利拿到初级管理员证书了；Salesforce每年会发布三次新功能（winter、spring、summer），意味着每次新功能更新后考试题目会发生一些变化；不过本着万变不离其宗的原则，只要按照考试大纲进行学习备考，就可以顺利的通过考试。下面我们来看一下Administrator考试的几项内容：
- Organizational setup: 3%
- User setup: 7%
- Security and access: 13%
- Standard and custom objects: 14%
- Sales and marketing applications: 14%
- Service and support applications: 13%
- Activity management and collaboration: 3%
- Data management: 10%
- Analytics—reports and dashboards: 10%
- Workflow/process automation: 8%
- Desktop and mobile administration: 3%
- AppExchange: 2%

细心一点你会发现，这些考点可以分解为系统基础配置（对象、字段、用户等）、自定化流程（工作流、审批流等）、报表以及Salesforce各个模块应用（Sales、Service、Community）等内容；其中的大部分内容可能已经在你的项目中用到了，所以从考试大纲来看，备考过程中发现哪里不足，可以着重突击。
# 备考过程
虽说Administrator考试难度不大，但是要想顺利通过考试，如果只刷题不做基础学习训练的话，不过的风险还是蛮高的，所以正确的学习资料和合适的备考方法是考试通过的前提。下面给大家推荐三个平台，可以分别对应三个阶段：
- [Trailmix for Adminisrator](https://trailhead.salesforce.com/en/users/strailhead/trailmixes/prepare-for-your-salesforce-administrator-credential)
基础不牢，地动山摇，只有将基础知识掌握，才能轻松应对多变的考题；一定避免不看知识点就刷题考试的情况，建议备考的同学花两周左右时间看一下这个模块，最好能用思维导图整理出来，第一阶段需要将知识点梳理清楚。
- [Quizlet](https://quizlet.com/)
按照考试的惯例，刷题是不可缺少的过程；建议每天花费半个小时刷一下Quizlet的题目，刚开始可能比较慢，遇到不明白，找身边的大牛或者搜索，争取弄明白每一道你遇到过的题目，并且可以将题目映射到考纲中，及时的查缺补漏。Quizlet上的题目时常会更新，尽量多做几套题目，避免漏掉不会的知识点。推荐一下我之前做过的一套题目：[https://quizlet.com/64631223/salesforce-admin-201-flash-cards/](https://quizlet.com/64631223/salesforce-admin-201-flash-cards/)，这一阶段能够将知识点和考题结合起来，不明白的及时查知识点，刷一周左右应该就差不多了。
- [Quizizz](https://quizizz.com/join)
考试前几天，可以尝试一下Quizzizz，你会和多个在线同学同时做题竞赛得分，每道题目都有时间限制，希望你可以拿到第一名哦。

从把握基础知识到题目训练，接下来就是尽可能做更多的题目巩固知识点，每一步都会增强你对Salesforce的认识与理解；建议备考时间一个月，在备考过程中，难免会有遇拿捏不定的题目，无论你是通过实际操作、网上搜索还是询问他人解决，寻找答案的过程本身就是一件有意义的事情，抛开考试拿证，经过这样一次备考，你的知识库也会变得越来越强大；像Salesforce这种考试，运气成分还是蛮大的，希望大家都能够顺利通过Administrator考试，假设有意外，再来一次就可以了，最后赠送大家一个Administrator知识点。
Datal0ader 与 Import wizard区别
    -  Salesforce标准的Import wizard导入数据不能超过50 000条，Dataloader可以导入50 000条以上5 000 000以下。
    -  Import wizard可以导入标准对象（Account、Contact、Leads、Solution、Campaigin member）与自定义对象数据，Dataloader没有限制。
    - Import wizard导入数据时候可以选择忽略重复规则和Trigger等自动流程，Dataloader不能忽略。
    - Import wizard不能Schedule数据导入，Dataloader支持Schedule数据导入。
    - Import wizard不能导出、删除数据，Dataloader支持增删改导出操作。

提醒已经拿到考试认证的同学，别忘了每年需要三次维护哦。