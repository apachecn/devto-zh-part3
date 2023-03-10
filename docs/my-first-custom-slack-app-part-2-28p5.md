# 我的第一个定制 Slack 应用程序-第 2 部分

> 原文：<https://dev.to/nabheet/my-first-custom-slack-app-part-2-28p5>

# 博客系列

| 部分 | 描述 |
| --- | --- |
| one | [设置后端](https://dev.to/nabheet/my-first-custom-slack-app-part-1-4o6j) |
| Two | [设置 API 服务器与后端和前端对话](https://dev.to/nabheet/my-first-custom-slack-app-part-2-28p5) |
| three | [设置前端与 API 服务器对话](https://dev.to/nabheet/my-first-custom-slack-app-part-3-4ahh) |
| four | 将应用程序部署到永久的地方 |
| five | 部署到 slack 应用商店的研究 |

# 到目前为止我们做了什么？

在我们之前的[博客](https://dev.to/nabheet/my-first-custom-slack-app-part-1-4o6j)中，我们已经建立了我们的数据库[谷歌电子表格](https://www.google.com/sheets/about/)，并允许 API 与之交互。因此，现在让我们构建我们的 [NodeJS](https://nodejs.org/en/) 应用程序，它将与前端和后端对话。虽然这一部分利用了下一个博客的一些设置，但这是一个松散的设置，让我们坚持到第三部分出来。
[![](img/1444832551bd55c6e686d2bdc5f83fe3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mdj8xr-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpkh6ds1d9j4qenbakil.png)

# 设置 NodeJS 应用程序所需的模块

我们的 NodeJS 应用程序将需要一些包来与 Slack 以及电子表格进行对话。所以幸运的是 slack 为我们提供了它的 npm 包* *[@ slack/bolt](https://www.npmjs.com/package/slack-bolt)* * Google spread sheet 为我们提供了** [google-spreadsheet](https://www.npmjs.com/package/google-spreadsheet) **。让我们首先初始化一个节点项目并安装两个包

```
npm init
npm i @slack/bolt
npm i google-spreadsheet 
```

# 让我们做一些编码

现在我们已经设置好了一切，让我们创建一个 index.js 文件并使用这些模块。

```
const App               = require('@slack/bolt');
const GoogleSpreadsheet = require('google-spreadsheet'); 
```

一旦我们有了模块，让我们添加我们的 excel 电子表格 id 以及凭证文件和 slack 应用程序令牌/秘密(坚持这一点，我们将在下一部分)。当然，令牌可以用更好的方式处理，我们一直在寻找一种快速的解决方案:)

```
const creds = require('./cred.json');
const spreadsheet = new GoogleSpreadsheet(<Spreadsheet id got in previous blog>);
const app = new App({
  signingSecret: "<secret1>",
  token: "<token>",
}); 
```

所以现在我们已经创建了两个对象 **app** 用于与 slack 交互，以及**电子表格**用于与后端交互。展望未来，我们将使用这些对象的方法来完成我们的工作。

Slack 使用了一个叫做 as command 的概念，使用它你可以创建动态的东西，比如我们的投票，如下所示。
[![](img/df750bda760d0ed015ef3f62e7644edf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--lv01woTN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/518hqp2jc2dz705lsnu6.png)

每当这个命令从 Slack 发出时，我们将能够在我们的 API 中监听到它，所以添加代码来处理 command /ourpoll。这将返回一个响应，该响应将使用如下所示的方法显示为类似上面的轮询。say 方法有一个适当的结构，在这个结构中，我们需要像传递响应和按钮那样传递值，而这些只是动作。我们可以使用 [slack bot kit builder](https://api.slack.com/block-kit) 来构建我们的响应并查看相应的结构格式。

```
app.command('/ourpoll', async ({
  command,
  ack,
  say
}) => {
  // Acknowledge command request
  ack();
  say({
    blocks: [{
        "type": "section",
        "text": {
          "type": "mrkdwn",
          "text": command.text
        }
      },
      {
        "type": "actions",
        "elements": [{
            "type": "button",
            "text": {
              "type": "plain_text",
              "text": "Yes",
              "emoji": true
            },
            "action_id": "Yes"
          },
          {
            "type": "button",
            "text": {
              "type": "plain_text",
              "text": "No",
              "emoji": true
            },
            "action_id": "No"
          }
        ]
      }
    ]
  });

}); 
```

一旦我们将响应反馈给 slack，我们还将调用**spread sheet . useserviceaccountauth**将数据保存在我们的 google 电子表格中。

```
 spreadsheet.useServiceAccountAuth(creds, function (err) {
   spreadsheet.addRow(1, {
      Question: command.text,
      Yes: ' ',
      No: ''
    }, function (err) {
      if (err) {
        console.log(err);
      }
    });
    if (err) {
      console.log(err)
    }
  }); 
```

现在我们知道了如何处理 slack 中的命令，但是我们在响应中添加的两个按钮或动作是/否又是怎么回事呢？当用户提供他/她的决定，我们应该能够处理它。也就是说，应用程序对象已经为此提供了方法操作。
让我们先处理行动是。代码可以以更好的方式增强，我打算很快这样做:)。如果您注意到我们正在读取 excel 的行，然后用决策更新相关的行。

```
app.action('Yes', ({
    body,
    ack,
    say
}) => {
    ack();
    spreadsheet.useServiceAccountAuth(creds, function(err) {
        spreadsheet.getRows(1, function(err, rows) {
            var filteredRows = rows.filter(function(el) {
                return el.question == body.message.blocks[0].text.text;
            });
            var sayConcatenated, yescount;
            filteredRows.forEach(row => {
                console.log('Row count is ' + row.yescount);
                if (row.yescount == "") {
                    row.yescount = 1;
                } else {
                    row.yescount = Number(row.yescount) + 1
                }
                yescount = row.yescount;
                if (row.yes == "" || row.yes == " ") {
                    row.yes = body.user.id;
                } else {
                    row.yes = row.yes + ',' + body.user.id;
                }
                if (row.yesusername == "") {
                    row.yesusername = body.user.name;
                } else {
                    row.yesusername = row.yesusername + ',' + body.user.name;
                }
                row.save();
                let users = row.yes.split(",");
                const say = users.map(user => '<@' + user + '>');
                sayConcatenated = say.reduce((acc, sayone) => acc + sayone);
            });
            say(`${sayConcatenated} ${yescount} geeks said Yes for ${body.message.blocks[0].text.text}`);
        });
        if (err) {
            console.log(err)
        }
    });
}); 
```

现在我们处理动作号

```
app.action('No', ({
    body,
    ack,
    say
}) => {
    ack();
    spreadsheet.useServiceAccountAuth(creds, function(err) {
        spreadsheet.getRows(1, function(err, rows) {
            console.log(body.message.blocks);
            var filteredRows = rows.filter(function(el) {
                return el.question == body.message.blocks[0].text.text;
            });
            var sayConcatenated, nocount;
            filteredRows.forEach(row => {
                if (row.nocount == "") {
                    row.nocount = 1;
                } else {
                    row.nocount = Number(row.nocount) + 1
                }
                nocount = row.nocount;
                if (row.no == "" || row.no == " ") {
                    row.no = body.user.id;
                } else {
                    row.no = row.no + ',' + body.user.id;
                }
                if (row.nousername == "") {
                    row.nousername = body.user.name;
                } else {
                    row.nousername = row.nousername + ',' + body.user.name;
                }
                row.save();
                let users = row.no.split(",");
                const say = users.map(user => '<@' + user + '>');
                sayConcatenated = say.reduce((acc, sayone) => acc + sayone);
            });
            say(`${sayConcatenated} ${nocount} geeks said No for ${body.message.blocks[0].text.text}`);
        });
        if (err) {
            console.log(err)
        }
    }); 
```

# 接下来是什么？

现在我们有了 API，它既可以与后端对话，也可以与前端对话。在下一篇博客中，我们将设置 slack 部分，以便 Slack 可以与我们的 NodeJS 应用程序对话，而 NodeJS 应用程序又可以与后端对话。当然，我们也将有一个演示视频:)