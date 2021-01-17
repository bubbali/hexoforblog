---
title: Mock Framework For Unit Test
catalog: true
date: 2019-09-16 15:23:31
tags:
- Unit Test
categories:
- Salesforce
---
# Why we need Unit Test
Unit Test executes your logic code with known input parameters and evaluates the output for expected results. 
Different from the Integration Test And Functional Test. They are only testing the isolation method. Focused on the smallest possible bit of code that can be tested. Unit Test ahead of integration testing. And make sure the logic code can running successfully based on the test data. Discover problems in advance. Another thing your code average coverage should above 75%.
# How to write Unit Test 
![Positive Unit Test Step](positiveut.png)
![Negative Unit Test Step](negativeut.png)

- Prepare Test Data
  1. Unit Test Data is temporary. When you Unit Test finished. And Data will rollback
  2. Your Test Data shouldn't rely on Organization Data.
  3. Different Unit Test should have their own-self Test Data
  4. You can prepare Test Data by Apex Operation or Static Source
- Call Test.StartTest() And Test.StopTest()
This resets governor limits and isolates your test from the test setup.
Execute the code you’re testing.

- Make assertions about the output of your tested code. You might assert a record exists, or that a field is set to an expected value.

## Write Unit Test Step
1. @isTest Annotation Class And @isTest Method
2. Prepared your test data. sometimes. Maybe you can @testSetup. if you have many methods use similar data.

## Problem
1. Mixture the Unit Test And Integration Test.If the related method failed. And your Unit Test will be failed.
2. There are many unrelated methods runs. And will hit the governor limits easily.
3. Unit Test running time will increase.

These Unit Test Not really Unit Test. Highly recommend Unit Test should more isolate and don't reply on other class or the database data.

## Dependency Injection
When the Business Running. And the run real logic code. When the environment is running Test context. Should invoke our pre-define method or mock object.
-  [Stub API](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_stub_api.htm) And [Mock Frameworks](https://github.com/financialforcedev/fflib-apex-mocks/)
   1. Stub API Introduction
   2. Mock Frameworks
   3. Examples
   4. Limitations(other workarounds)

- [Utilize Other Frameworks](https://github.com/mattaddy/SObjectFabricator)
   1. Set Up Test Data with formula
   2. Set Up Test Data with relationship
   3. Example
```
sfab_FabricatedSObject fabricatedAccount = new sfab_FabricatedSObject(Account.class);
fabricatedAccount.setField(Account.Id, 'Id-1');
fabricatedAccount.setField(Account.Name, 'Bubba');
fabricatedAccount.setField(Account.LastModifiedDate, Date.newInstance(2017, 1, 1));

Account acct = (Account)fabricatedAccount.toSObject();

// Account:{LastModifiedDate=2017-01-01 00:00:00, Id=Id-1}
System.debug(acct);
// (Opportunity:{Id=OppId-1}, Opportunity:{Id=OppId-2})
System.debug(acct.Opportunities);

sfab_FabricatedSObject fabricatedOpportunity = new sfab_FabricatedSObject(Opportunity.class);
fabricatedOpportunity.setField(Opportunity.Id, 'OppId-3');
fabricatedOpportunity.setField(Opportunity.Name, 'TestOpp');
fabricatedOpportunity.setParent('Account', fabricatedAccount);
fabricatedOpportunity.setField(Opportunity.LastModifiedDate, Date.newInstance(2017, 1, 1));

Opportunity opp = (Opportunity)fabricatedOpportunity.toSObject();
system.debug(opp.Account);
system.debug(opp);
system.debug(opp.Account.Name);
```

- Advantage
   1. Implements the isolates Unit Test.
   2. Prepare Test Data Fast and more efficient for UT 
   3. Reduce the database interaction and Unit Test Time 

# Suggested Practice
1. The test method function names and class name should be on behalf of the method.
2. A function should be smaller. if there are too many lines. You should consider refactor.
3. All test class methods should be marked private and the class itself should be marked private.
4. The test class needs to be self-contained - we shouldn't create any utility test classes for setting up data.
5. The test methods should utilize the fflib framework and sfab frameworks to set up objects instead of raw JSON in the test methods.
6. The test classes should avoid any DML and SOQL operations.