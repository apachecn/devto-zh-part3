# 在 C#中添加 Salesforce 对象记录

> 原文：<https://dev.to/rachelsoderberg/salesforce-outbound-messages-part-5-adding-a-salesforce-object-record-in-c-3nlo>

欢迎阅读本系列的第 5 部分！我并不期望在本系列中增加更多内容，但是随着我在工作中不断提高我的 Salesforce 技能，我会继续寻找我想分享的其他主题。

本周我将向您展示如何在 C#应用程序中自动向 Salesforce 对象添加新记录。例如，当您的应用程序遇到一个系统中不存在但在其他地方(如您的数据库)有现有信息的联系人时，以编程方式添加新记录的能力就很方便。不要期望创建新 RMA 的人创建一个新联系人或潜在的许多新联系人，您可以简单地要求他们输入电子邮件地址并运行查询来使用或创建与之匹配的联系人。

在这一点上，我假设你有某种形式的 C#应用程序。这不需要与我之前的示例完全相同，甚至根本不需要有 web 服务/出站消息关系，但需要设置它以使用绑定登录、查询和更改 Salesforce。如果你还没有这个功能，你可以参考我以前在这个系列中的文章来完成大部分工作，我强烈推荐这个文档来设置 [Salesforce 登录](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_calls_login.htm)。

## 确定 Salesforce 中是否已存在联系人

首先要做的是确保我们在 Salesforce 中没有联系人-没有人喜欢重复的数据！如果您不熟悉查询对象，请使用上面的圆圈或此处查看本系列的第 4 部分:[检索和更新 Salesforce 对象字段](https://dev.to/rachelsoderberg/salesforce-outbound-messages-part-4-retrieving-and-updating-salesforce-object-fields-with-your-web-service-52gj)。一旦您从数据库中获得了您的唯一信息(在本例中为电子邮件地址)，您就可以使用该信息查询 Salesforce。

顺便说一下:在一次处理多个潜在联系人的情况下，我更喜欢将他们作为逗号分隔的列表添加到字符串中，因此我只使用一个查询来访问 Salesforce，在我的查询中使用“in()”而不是“= ”,如下所示:

```
string contactQuery = $"SELECT Id, Name, Email, Account.Name, Account.Id, Account.identify_industry__c " +
$"FROM Contact WHERE Contact.Email IN ({CommaSeparatedShippingContactEmails(shippedContactsFromDB)})";

private string CommaSeparatedShippingContactEmails(List<string> shippedContactsFromDB)
{
    foreach (string email in shippedContactsFromDB)
    {
        if (_emailCsv == "")
            _emailCsv = $"'{email}'";
        else
            _emailCsv += $", '{email}'";
        }

    return _emailCsv;
} 
```

如果我不清楚我在那里做了什么，我从我从数据库收集的列表中取出每个电子邮件地址，并将其添加到字符串中，同时在每封电子邮件的第一封和最后一封之间插入逗号，最后在我的 Salesforce 查询中返回列表。当 Salesforce 收到最终结果时，它将类似于以下内容:

```
string contactQuery = $"SELECT Id, Name, Email, Account.Name, Account.Id, Account.identify_industry__c " +
$"FROM Contact WHERE Contact.Email IN ('bob@bobthebuilder.com', 'fred@awesomecomputers.com', 'sue@gmail.com')"; 
```

这将提供当前 Salesforce 组织中与查询中的电子邮件地址匹配的所有联系人的结果集。您可以将此结果集中的电子邮件与原始列表中的电子邮件进行比较，以确定哪些联系人不存在，应该添加。可能有更好的方法来执行此任务，但这是我获取联系人列表以添加到 Salesforce 的方式:

```
private void GetListOfContactsNotInSalesforce(List<string> shippedContactsFromDB)
{
    foreach (var shipped in shippedContactsFromDB)
    {
        bool match = false;

        foreach (var sfItem in contactsRetrievedFromSalesforce)
        {
            if (sfItem == shipped)
                    match = true;
        }

        if (!match)
            contactsToAddToSalesforce.Add(shipped);
    }
} 
```

我从数据库中传入我的联系人电子邮件地址的原始列表，对于每个电子邮件地址，我将“match”设置为 false，以表明当我循环通过在 Salesforce 中找到的联系人列表时还没有找到它。如果电子邮件通过，但没有匹配 Salesforce 联系人列表中的单个项目，这表明它不在 Salesforce 中，它会被添加到新列表中。

## 向 Salesforce 添加新联系人

现在我们有了一个不在 Salesforce 中的联系人列表，我们可以像更新字段一样使用我们的绑定，但是我们将使用 [binding.create()](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_calls_create.htm) 而不是使用 [binding.update()](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_calls_update.htm) 。如果您想了解有关绑定的更多信息，请单击 Salesforce 提供的文档的链接。

该流程与更新 Salesforce 字段一样简单，并且看起来非常相似:

```
foreach (var contact in contactsToAddToSalesforce)
{
    ObjectResult<sp_BSYNC_GetShipmentsForServiceCloud_Result> incomingShipments = context.sp_BSYNC_GetShipmentsForServiceCloud();

    foreach (var ship in incomingShipments)
    {
        if (ship.conEmail == contact && !alreadyCreated.Contains(ship.conEmail))
        {
            try
            {
                Contact newContact = new Contact
                {
                    FirstName = ship.FirstName,
                    LastName = ship.LastName,
                    Email = ship.conEmail,
                    Phone = ship.conPhone,
                    MailingStreet = ship.SH_ShipToAddress,
                    MailingCity = ship.SH_ShipToCity,
                    MailingState = ship.State,
                    MailingPostalCode = ship.SH_ShipToPostalCode,
                    MailingCountry = ship.SH_ShipToCountry,
                    AccountId = GetAccountIdFromSalesforce(ship.SH_ShipToName)
                };

                SaveResult[] results = Form1.ProcessMonitor.binding.create(new sObject[] { newContact });

                alreadyCreated.Add(ship.conEmail);

            }
            catch (Exception e)
            {
                UpdateProcessingMonitorText(
                                $"{DateTime.Now}: Error in Contact creation process for {ship.conEmail}{Environment.NewLine}{e}");
            }
        }
    }
} 
```

这里发生了很多事情，所以让我解释一下:我首先循环通过我创建的联系人列表中的每个电子邮件地址，以添加到 Salesforce。因为开始时我只存储了数据库中的电子邮件地址，所以我必须使用存储过程再次检索数据库联系人的完整列表(也可以使用表来代替)。遍历存储过程提供的联系人列表，我可以将每个电子邮件地址与我的 Salesforce 列表中的电子邮件地址进行比较，并且只使用与我当前使用的电子邮件地址匹配的记录。

一旦我确认了我的 Salesforce 列表和存储过程结果之间的匹配，我就使用我收集的所有信息在 Salesforce 中创建新联系人，并在新联系人上调用 binding.create()方法以将其添加到 Salesforce。与使用 Id 更新字段以创建关系类似，我检查任何与我的联系人的公司名称匹配的帐户，并返回 Salesforce Id，以向我的联系人提供与其对应帐户的关系(如果存在)。

注意:为避免在 Salesforce 中意外创建任何重复条目，我保留了一个辅助联系人列表，该列表在我创建新的推荐人时填充，并在创建每个新联系人之前与它进行比较。

创建后不久，您应该会在 Salesforce 中看到您的新联系人。如果你选择添加关系的话，验证关系也是一个好主意。

* * *

我希望你喜欢我的 Salesforce 系列，我也希望当我分享我在工作中所做的事情时，你能和我一起学习一点。如果您有任何 Salesforce 主题希望我在本系列的下一篇中介绍，请不要犹豫，尽管提出来！我喜欢挑战，并且发现我非常喜欢使用 C#应用程序与 Salesforce 一起工作。感谢阅读！