# AWS Lambda 和 DynamoDB -一些初步措施

> 原文：<https://dev.to/gtanyware/lambda-and-dynamodb-the-first-steps-228>

当开始一项新技术时，第一步总是最困难的。当你回头再次做同样的动作时，一切都显得那么简单，很难记得第一次是多么艰难的任务。在我的例子中，任务是用 AWS Lambdas 做一些事情，以 Node.js 的形式使用 JavaScript。因为这只是大约一周前，我仍然清楚地记得我遇到的所有问题，所以我认为这是一个很好的时间来写我仍然处于初学者水平的经历。

关于如何设置 AWS 帐户，有无数的资源可供参考，所以我没有必要详细介绍，特别是因为细节往往会随着时间的推移而变化，所以详细的文档很快就会过时。最重要的是确保你从“免费层”开始，这意味着你一年内不用支付任何费用，除非你产生了惊人的流量。即使过了这段时间，一些 AWS 功能仍然免费，但使用率很低；“低”对于刚开始学习这个系统的人来说是相当慷慨的资源分配。创建帐户后，转到 AWS 管理控制台，您会看到一长串服务。我将在这里使用其中的 4 个。

## 任务

我给自己设定的任务是创建几个 REST 端点；一个用于向数据库写入内容，另一个用于检索内容。这些是静态网站无法提供的主要元素(因为它需要服务器代码)，因此将它们作为可以从任何地方调用的独立函数提供可能会很有用。我越来越多地建立 100%客户端(JavaScript)网站，因此解决存储问题非常重要。

任务的概要是编写几个 AWS `Lambda`函数来处理`DynamoDB`数据库；一个向它发送数据，另一个从它那里获取数据。结果是另外两个 AWS 服务也将被调用，它们是`API Gateway`和`IAM`，所以我将介绍一下这四个服务是如何组合在一起的。我将从那些对外界依赖最小的人开始；那是`DynamoDB`和`IAM`。

## DynamoDB

`DynamoDB`是一个 NoSQL 数据库，这意味着它不谈论 SQL。然而，它的 JavaScript 接口非常简单，您很快就会看到。这项服务的控制台非常简单。我建议在开始编码之前，花一点时间创建一些表，用测试数据手工填充它们，并进行扫描和/或查询来检索数据。所有这些都在 AWS 文档中有深入的介绍，管理界面本身是 AWS 中更友好的界面之一。

我希望我的两个端点尽可能通用，这样它们可以连接到许多不同的表。我将使用的许多表格都有相当相似的结构；每个记录都有一个主分区键和一组任意的属性。在我的端点处理程序中，表名和主键名都是变量。例如，一个表可能包含以唯一名称为关键字的 HTML 片段，而另一个表包含关于特定关键字的数据，其中值包含第一个表中某个片段的名称。因此，第一个表的分区键可能是“name”，第二个表的分区键可能是“keyword”。数据也是如此；第一个表称之为“脚本”，第二个表称之为“值”。当您在第二个表中查找一个关键字时，您将得到它的一大块数据，包括描述它的页面名称，允许您搜索第一个表来检索 HTML。有点做作，但很有效。这些表在结构上是相似的，所以同一个`Lambda`函数应该能够处理它们中的任何一个。

## IAM

这就是我现在要说的关于数据库的全部内容，让我们继续。第二个服务是`IAM`，或*身份和访问管理*。您可能已经在设置您的帐户时遇到过，因为 AWS 会建议您创建一个用户，而不要在 root 帐户中做任何事情。这里您需要做的主要事情是设置一个“角色”，这是一个允许其他服务完成其工作的权限块。

在“角色”菜单项下，您会发现一个创建新角色的按钮。给它起个类似`GenericEndpoint`的名字。您需要添加两组权限；一个是`AmazonDynamoDBFullAccess`，另一个是`AWSLambdaBasicExecutionRole`。两者都应该是不言自明的(或者很快就会是)。

## λ

现在我们知道可以做些编码了。转到 Lambda 服务并创建一个新的*函数*。这是一段代码，当有人点击你的端点时，它将被调用，完成它的工作，然后再次消失。没有代码会一直运行，所以当它不活动时，你不会有任何损失。您可以用各种语言创建 Lambdas，但我在这里将使用 Node.js。如果需要帮助，请再次查阅标准文档。

在`Lambda`仪表板的底部附近是*执行角色*的下拉列表。在这里您选择您在`IAM`中创建的角色。您的代码现在拥有了运行和与`DynamoDB`交互所需的所有权限。

再往上是一个代码编辑器，让我们放一些代码进去。

## 发布端点

```
const AWS = require(`aws-sdk`);
AWS.config.update({region: `eu-west-2`});
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = (event, context, callback) => {

    const params = JSON.parse(event.body);
    const TableName = params.table;
    const Item = {};
    Item[params.kName] = params.kValue;
    Item[params.vName] = params.vValue;

    dynamo.put({TableName, Item}, function (err, data) {
        if (err) {
            console.log(`error`, err);
            callback(err, null);
        } else {
            var response = {
                statusCode: 200,
                headers: {
                    'Content-Type': `application/json`,
                    'Access-Control-Allow-Methods': `GET,POST,OPTIONS`,
                    'Access-Control-Allow-Origin': `https://my-domain.com`,
                    'Access-Control-Allow-Credentials': `true`
                },
                isBase64Encoded: false
            };
            console.log(`success: returned ${data.Item}`);
            callback(null, response);
        }
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

在顶部，我们创建了一个数据库客户机实例，然后我们有一个 POST 请求的处理程序。`event`参数携带发送的数据，这些数据都在`body`元素中。在这里，表被命名为。接下来的位创建了一个由一个已命名的键及其值组成的`Item`。因为我想处理多个表，所以键的名称不会总是相同的，所以我没有对所有内容进行硬编码，而是将表的名称、键的名称和数据放入提交的参数中。键的名称作为`kName`传递，其值作为`kValue`传递。类似地，value 键的名称取自`vName`，其值取自`vValue`。

例如，让我们假设这个表叫做`mytable`，它的主键是`name`，它的数据在一个叫做`script`的*属性*(就像 SQL 中的一个列)中，我们正在编写的项的名称是`about`，它的内容是`This is my script content`。在这种情况下，发布数据将是

```
{  "table":"mytable",  "kName":"name",  "kValue":"about",  "vName":"script",  "vValue":"This is my script content"  } 
```

Enter fullscreen mode Exit fullscreen mode

如果这看起来有点复杂，作为比较，下面是只处理一个表时要使用的代码。在这个版本中，表名和键都是硬编码的:

```
 const TableName = `mytable`;
    const Item = {
            name: body.name,
            script: body.script
        } 
```

Enter fullscreen mode Exit fullscreen mode

其中表名为`mytable`，主键为`name`，数据为`script`。下面是对应的帖子数据:

```
{"name":"about","script":"This is my script content"} 
```

Enter fullscreen mode Exit fullscreen mode

对`DynamoDB`的调用接受表名和项目，并返回一个错误或潜在的一些数据。后者被打包成合适的响应并返回给调用者。重要提示:请参见下面关于 CORS 的注释，如果出现错误，这是相关的。

## 到达终点

GET 端点有一个类似的脚本:

```
const AWS = require(`aws-sdk`);
AWS.config.update({region: `eu-west-2`});
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = (event, context, callback) => {
    const TableName = event.queryStringParameters.table;
    const Key = {};
    Key[event.queryStringParameters.key] = event.queryStringParameters.value;

    dynamo.get({TableName, Key}, function(err, data) {
        if (err) {
            callback(err, null);
        } else {
            var response = {
                statusCode: 200,
                headers: {
                    'Content-Type': 'application/json',
                    'Access-Control-Allow-Methods': 'GET,POST,OPTIONS',
                    'Access-Control-Allow-Origin': `https://my-domain.com`,
                },
                body: JSON.stringify(data.Item),
                isBase64Encoded: false
            };
            callback(null, response);
        }
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里的区别在于包含查询参数的`event`中的元素，以及使用了`Key`而不是`Item`。在这种情况下，返回我们刚才写的值的查询字符串是

```
?table=mytable&key=name&value=about 
```

Enter fullscreen mode Exit fullscreen mode

## API 网关

拼图的最后一部分是`API Gateway`。顾名思义，这是其他 AWS 服务与外界的接口。一个网关既服务于 GET 又服务于 POST(也服务于 PUT 等)，所以给它一个与您的`Lambda`相关的名称。在仪表板中，单击*动作*按钮来创建 GET 和 POST 方法。然后再次单击并选择 CORS 操作，该操作允许您指定允许谁访问您的端点(默认为“全部”)。为每个 GET 和 POST 指定将被调用的`Lambda`，并选择*使用 Lambda 代理集成*。

不要忘记每次你改变方法时点击`Actions`中的`Deploy API`。舞台可以被命名为任何你喜欢的名字，但大多数人选择*开发*或*生产*。然后会向您显示端点 URL。

`API Gateway`有一个有用的测试特性，当事情不按预期进行时(几乎可以肯定的是，前几次就是这种情况)，它可以让你直接访问日志信息。对于 GET，您需要进入*方法请求*并设置 *URL 查询字符串参数*，对于 POST，主体参数(如上所示)必须输入到提供的框中。然后你可以点击*测试*看看会发生什么。

## CORS

从被问到的问题来看，CORS 是客户机-服务器编程中比较棘手的一个方面，然而就我所见，它实际上非常简单。然而，至少在 AWS 环境中有一些问题。

一个困扰我几天的问题是，我的 GET 端点工作正常，但是 POST 端点一直报告 CORS 错误，抱怨没有设置正确的权限。这是真的，但不是我预期的原因。原来我的参数列表中有一个打字错误，导致了`JSON.parse()`失败。这意味着对`DynamoDB`的调用实际上从未发生，我的端点返回了一个空的响应代码。这导致 CORS 错误的原因是，当使用 Lambda 代理集成时，`API Gateway`只设置了 200 响应。如果您想处理任何其他响应代码，您必须自己手动完成，否则您的浏览器将拒绝错误响应，因为它缺少所需的 CORS 标头。

## 终于

一旦在测试环境中一切正常，您可以设置 Postman 在端点抛出一些测试数据，然后检索它。使用几个段落前从`API Gateway`获得的端点 URL 对于 GET，将您的查询参数添加到其中，对于 POST，将数据放入请求体中。之后，您可以尝试从真实的网页调用您的`Lambda`,如下所示:

HTML

```
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    Lambda Test
    <script type='text/javascript' src='/testg.js'></script>
</head>

<body>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript(适当修改 URL 行)

```
window.onload = function () {
    const createCORSRequest = function (method, url) {
        const xhr = new XMLHttpRequest();
        if (`withCredentials` in xhr) {
            // Most browsers.
            xhr.open(method, url, true);
        } else if (typeof XDomainRequest != `undefined`) {
            // IE8 & IE9
            xhr = new XDomainRequest();
            xhr.open(method, url);
        } else {
            // CORS not supported.
            xhr = null;
        }
        return xhr;
    };

    const method = `GET`;
    const url = `https://k84msuyg7a.execute-api.eu-west-2.amazonaws.com/prod?table=easycoder-script&key=name&value=fileman`;
    const request = createCORSRequest(method, url);
    request.setRequestHeader(`Content-Type`, `application/json; charset=UTF-8`);
    request.send();

    request.onload = function () {
        const content = request.responseText;
        console.log(content);
    };

    request.onerror = function () {
        const error = request.responseText;
        console.log(error);
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就是我对 AWS 的快乐的简短总结的结尾，在痛苦仍然新鲜的时候捕获的。我肯定有很多不准确的地方；我很高兴听到他们的意见，并会做出适当的修改。我希望 AWS 的其他新手会发现这篇文章很有用。

扬·安东宁·科拉尔在 [Unsplash](https://unsplash.com/search/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的标题照片