# Salesforce 字段不随 C# Web 服务更新

> 原文：<https://dev.to/rachelsoderberg/salesforce-fields-not-updating-with-c-web-service-4ga4>

本周，当我为分配给我在工作中创建的 Salesforce Asset Updater 应用程序构建一些新功能时，我做了一个~~愚蠢的~~发现。我希望通过在这里分享这一发现，我可以为其他人节省试图找出为什么他们的字段不会更新的时间！

Salesforce 有许多不同的字段类型可用于每个对象。这些范围从最简单的文本类型字段一直到与其他对象有关系的更复杂的查找字段。非文本字段(如编号、复选框、日期和选项列表)在对象初始化期间需要一个非常简单但必要的额外步骤，以向 Salesforce 表明必须对这些字段进行更改。

提醒一下，仅使用字符串字段进行更新和创建的典型 Salesforce 对象初始化如下所示:

```
Account newAccount = new Account{
    Id = GetSalesforceId(custName),
    Phone_Number__c = custPhone,
    Shipping_Address__c = custAddress,
} 
```

尝试创建或更新非字符串类型的字段(如复选框)后，必须提供所需的字段值，并更新字段的“指定”字段，如下例所示:

```
Account newAccount = new Account{
    Id = GetSalesforceId(custName),
    Phone_Number__c = custPhone,
    Shipping_Address__c = custAddress,
    /*Updating Checkbox field below*/
    Is_Premium_Customer__c = true,
    Is_Premium_Customer__cSpecified = true
} 
```

没有明确的异常或错误输出让您知道您已经忘记了这一关键步骤。您试图修改这些特殊字段中的一个而不包括指定字段的唯一迹象是该字段拒绝更新，没有任何抱怨。就我个人而言，在正确的谷歌搜索最终引导我找到这个解决方案之前，我花了近一个小时试图找出我错在哪里。