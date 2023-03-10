# 向出站消息和 Web 服务添加字段

> 原文：<https://dev.to/rachelsoderberg/salesforce-outbound-messages-part-3---adding-fields-to-an-outbound-message--web-service-2an3>

欢迎来到我的 Salesforce 出站消息系列的第 3 部分！在本教程中，我将教你如何在出站消息和 Web 服务中添加更多的字段，以防你已经建立了连接，并意识到你实际上需要比原来的 WSDL 包含更多的字段。这很重要，因为我们使用 svcutil 将我们的接口类的内容替换为我们的 Salesforce WSDL 的内容，而不是在 Visual Studio 中导入它。这意味着我们没有 Web 或服务引用，必须手动更新我们的接口类，谢天谢地这并不太难。

此时，我将假设您在 Salesforce 中设置了出站消息，sales force 是一个. NET WCF Web 服务，可以编译，并且当您使用 PutsReq bucket 与它们连接时，有一个“真”Ack notificationsResponse。

* * *

第一步是用您的新字段更新 Salesforce。导航到您的 Salesforce 出站消息，单击编辑，并从可用字段中添加您需要的字段。完成后，单击保存。

[![Salesforce Outbound Message](img/b6cb9d0d58db20f25bb380ae30236ae4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CUPMnE6M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8c2fnnk3fos3fo7i3fm7.PNG)

使用该链接生成新的 WSDL(无需保存)。现在，您应该看到您的新字段和原始 AccountId 元素，您将需要注意它们是否是 nillable 以及它们的类型是什么，因为我们将在服务中使用这些值。

[![WSDL Elements](img/c73b997382c05454bb9e49c4a0446e5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6t4m5zYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8mize5jmgj9x34uj6od.PNG)

打开 Web 服务项目，将新字段添加到接口类，其中部分类 Quote 继承自 sObject。它将类似于下面的代码:

[![Interface Class Code](img/6c642fe9841d0c1bae85ab6a18f82330.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uKOofciZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/66trj3e7ur7qjby0ui0i.PNG)

为您的出站消息端点 URL 创建或使用现有的 PutsReq 存储桶，然后在 Salesforce 中创建新报价。如果您的工作流规则检查订单 XML 字段中的更改，请记住更新该字段！出站消息仍然会失败，因为我们没有真正的连接，但是 PutsReq 会给我们 POST Soap 信封，我们可以以两种方式之一使用它:

1.  将整个 Soap 信封粘贴到 SOAP UI 请求中，并单击 Submit ![SOAP Request](img/777b0458b9e5b7b26aecc92879fe2eab.png)
2.  将 Soap 信封中的每个字段粘贴到您的 Web 服务 WCF 测试客户端![WCF Test Client Request](img/bb99c6abc330cb57a15853b9a616c13b.png)

无论您选择哪种方法，您都应该收到真正的确认通知响应，这样 Salesforce 才会满意。

* * *

这个过程的最后一步是最个性化的——您需要使您的 Web 服务在线，并使它可以从您组织的安全和防火墙之外访问。您还必须将该功能构建到您的 Web 服务中，以便根据您的用途来使用和操作您传入的字段。将不会有第 4 部分涵盖这一部分；我把这种努力留给你自己的好奇心。编码快乐！