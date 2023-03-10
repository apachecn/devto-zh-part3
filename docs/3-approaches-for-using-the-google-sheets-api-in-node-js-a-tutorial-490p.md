# 使用 Google Sheets 作为数据库:将 API 连接到 Node.js 的 3 种方法

> 原文：<https://dev.to/stephsmithio/3-approaches-for-using-the-google-sheets-api-in-node-js-a-tutorial-490p>

这篇文章最初发表在我的博客上，我经常在那里写关于学习编码、远程工作和科技女性的文章。

# Node.js 中使用 Google Sheets API 的 3 种方法:教程

在学习编码一年后，我最近发现自己进入了异步 Javascript 的领域。

在过去的几个月里，我一直在努力学习这门语言的这一部分，并且不得不重温我已经学过很多次的课程的部分内容。即使这样做了，我仍然感到很失落，不知道如何获取信息并将其转化为可操作的东西。

在我最近对这些材料的回顾中，我意识到唯一能让它真正坚持下去的方法就是用它来创造一些东西。因此，我决定通过开发一个名为 [Progression Page](https://progression.page) 的工具来测试我的知识。在创建这个应用程序时，我确实觉得能够更好地利用异步工具箱中的工具。

Progression Page 是一个帮助人们跟踪和可视化他们学习编码的日常进展的工具，所以本着帮助他人学习的精神，我决定分享这个教程，以激励他人使用 Google Sheets 作为他们的数据库来创建迷你应用程序。

## 使用 Google Sheets 作为数据库

我在谷歌表单上追踪我做的每一件事。我说的一切，就是一切。

我跟踪我锻炼的频率，我看了多少本书，我学习编码的频率，等等。我这样做是为了让自己负责任，因为我相信量化进展是“success"﹣not”的重要前兆，不仅在我的“工作生活”中，而且在所有方面。

由于跟踪我的努力对我的个人进步至关重要，当别人不这样做时，我总是感到惊讶，尤其是当缺乏跟踪往往会导致这样的结果时:

[![An all to frequent outcome of people's journey learning to code.](img/85a8aba8cf10014300cdd47f3924bc30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ehfdvYA---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/WIh9jTdWd5cXryInEeRc-x8mCKBq83dI3HzlYNyxvKGVZi1vTkINBVXUWPQnV7mR2TW-iIkeTrA0RPpmlI7F1xkLVHFI_X077ne4OeQ6CilsUHX9Z2twrH7nbCsmIZplhFKvIl46)

我希望有一个地方，人们可以很容易地公开记录他们的编码进度，这样人们就可以对他们负责。这正是[前进页面](http://progression.page)所做的；它允许每个成员在谷歌表单中记录他们的日常进展，包括他们正在学习的内容、学习时间和学习频率，以及他们的技能、目标等。这些信息直接从表单中提取并显示在网页上。

[![Example of Progression Page output, with all data pulled directly from a Google sheet.](img/200119754e5993cc62df5c3c9792e3fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6KMQ0IKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.stephsmith.io/conteimg/2019/05/image-4.png)

Progression Page 是人们使用 Google Sheets 作为数据库的众多例子之一。例如， [Sheet2Site](https://www.sheet2site.com/) 利用工作表作为从事件到产品到工作公告板等网站的数据库。尽管使用工作表作为数据库肯定有缺陷。它的美妙之处在于，您(和其他人)可以根据需要轻松查看、编辑和缩放信息。考虑到这一点，我迫不及待地想看看人们用这项技术在指尖创造了什么。

## 前言

在开始之前，我想先说明一下，我的代码可能不是“最佳”的，或者没有按照它应该的那样进行重构。记住，我还是个 n00b(我们不都是吗？).

作为一个开发新手，在担心应用程序的可伸缩性或完美性之前，我专注于创建有用的东西。

我还应该提到，这篇文章是专门为那些使用 Node.js 的人创建的，尽管我确信它可以为其他后端量身定制。我不会从头开始介绍设置 Node.js/Express.js 应用程序的步骤，因此它实际上是为那些熟悉基础设施的人准备的。

本文将讨论的内容:

*   如何在 Node.js 中认证 OAuth2
*   如何通过 3 种方法连接 Google Sheets API with Node 来创建 Google 电子表格数据库
*   如何将 API 中的数据集成到 Express.js 应用程序中

本文将**而非**讨论的内容:

*   如何设计或部署网页
*   如何从头开始使用 Node.js 或 Express.js
*   如何跨其他后端语言使用 API

## 入门

首先，您应该有一个初始化了 npm 的基本节点应用程序。

从这里开始，您需要安装将用于该应用程序的软件包:

*   快速(用于呈现动态页面)
*   请求(用于发出 HTTP 请求)
*   fs(用于 Google API)
*   readline(用于 Google API)
*   Google API(用于 Google API)

    ```
    npm install express ejs request gs readline googleapis@39 --save 
    ```

确保在索引文件的顶部正确地要求了这些依赖项，如下所示。

```
var express     = require("express"),
fs              = require('fs'),
readline        = require('readline'),
{google}        = require('googleapis'),
request         = require('request'),
app             = express(); 
```

从这里开始，您还应该将视图引擎设置为 ejs，这样程序就可以识别正在呈现的 EJS 文件，而不需要每次都定义文件扩展名(即:您可以轻松地调用`res.render('test')`而不是`res.render('test.ejs')`)。

```
app.set("view engine", "ejs"); 
```

### 在 Node.js 中设置路线

由于这个特定的应用程序将把提取的数据发送到一个网页，我们首先需要设置为这个应用程序服务的路由。例如，这可以是一个标准路径，我们将把它定向到`test`子目录，呈现`test.ejs`文件。我将为教程的每一部分编辑这个端点。

```
app.get("/test", function(req, res){
  res.render('test')
}); 
```

### 设置渲染页面

您需要创建一个视图目录来存放您的 EJS 文件。我还设置了一些我在节点应用程序中使用的其他东西，这些东西对于入门并不重要，但是对于本教程之后的其他步骤是必需的，比如添加样式。

*   我添加了页眉和页脚部分，尽管这些并不是每个应用程序都需要的。如果您不知道它们是什么，只需确保您的整个 express 文件拥有所有需要的信息，就像普通的 HTML 文件一样，比如 DOCTYPE 和`<head>`部分。
*   我添加了一个公共目录来存放支持站点的样式和其他文件。为了让应用程序识别这个目录，在任何路由之前需要下面一行。

```
app.use(express.static(__dirname+"/public")); 
```

一旦你采取了这些步骤，你的目录应该看起来像这样！

[![Example directory set up.](img/a378af9ed83618e514fd9ba43072930a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FXpXXM5h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.stephsmith.io/conteimg/2019/05/image-3.png)

此时，您应该已经为我们的节点应用程序准备好了核心依赖项，所以我们可以开始使用 API 了！

## 将 Node.js 链接到 Google 工作表

本教程的大部分内容将是如何连接 Google API。有多种方法可以连接到 API，我将分享三种方法:

*   通过 V3 的 Google Sheets HTTP 请求
*   带有 OAuth2 的 Google Sheets API v4
*   谷歌开发者控制台和谷歌电子表格包

欢迎您使用下面的任何方法，并跳到对您最有帮助的方法。在开始之前，我应该提到我会鼓励大多数应用程序使用 v4，原因如下:

使用 v3 HTTP 方法的原因:

*   它更容易设置

使用 v4 的原因:

*   v3 最终会被弃用
*   使用 v3 方法需要发布电子表格
*   使用 v3 方法只允许读取，不允许写入
*   在 v3 方法中，您依赖于制表符的顺序
*   在 v3 方法中，您的请求灵活性较差
*   v4 中的输出更清晰

### 方法 1:通过 V3 的 HTTP 请求

迄今为止，可以说访问 API 最简单的方式是通过 [v3](https://developers.google.com/sheets/api/v3/) 。

这个版本提供了一个端点，只需访问指定的 URL，就可以返回单个 JSON 中的所有信息。要使用这种方法，只需执行以下步骤:

1.  转到您的电子表格，然后单击文件→发布到 Web

[![Setting your spreadsheet to be public.](img/814db09a2db34127c9ea59e9d2f4e6ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5CnKnRWJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/5ZM6NmIRriJNcPHjyobrKr-PgTANdHZRSwZ2pF9OAFnzAqdRDQy8WEMUvYIoFGGKFHsvc93RlbVuPNhV4B6xjn-L6dt3FUAeKHjMTrzWsfYUqtn4bhfMp0_FCCvdO3mRJITZKY-U)

1.  选择要从中提取数据的文档部分。这些**必须被发布**以便通过该方法拉取数据。
2.  利用这个 URL 结构访问数据，用工作表中的正确信息替换`SPREADSHEET_ID`和`TAB_NUMBER`:
    *   [https://spreadsheets . Google . com/feeds/cells/spread sheet _ ID/TAB _ NUMBER/public/values？alt=json](https://spreadsheets.google.com/feeds/cells/1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I/1/public/values?alt=json)
    *   例如，下面的 URL 将返回第一个图像中的信息，第二个图像中描述了一个 JSON:[https://spreadsheets . Google . com/feeds/cells/1 uiv 4 rkox 8 kjk 2 zqyig 0 klh 5 _ f 8 fcodwiwv 8 YF 2 yf8 I/2/public/values？alt=json](https://spreadsheets.google.com/feeds/cells/1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I/1/public/values?alt=json)
    *   如果您从末尾删除`?alt=json`,您将收到 XML 格式的数据。

[![Example data input in Google Sheets.](img/e18d6b30dc8afcd44bb75b33bca5eb92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H3cSJdeX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/xp8NP1SN0hlTlCYLsGuNu5QaVJilstp8aOOeDR_aHzw3EwXs-iD-ESOeL8m0b-QfMbh_29bw7kL_I3c1asPN7qW3UC4Sgq-pfqcTcVy7qZT8lls2Dwdr7O65LeLCZ0slUaXD3vdm)

[![Example data output when accessing the v3 endpoint.](img/23e354988be237b1c1cf2fda3a3a5ccd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fje4jeVk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/WiCQ2DfFnvfYz5io4DAjjs5B1tb3IU6oe_IOjBEkyvIZE8QiHHi-FQmMBMOOH2sGfVy2yiwBw-kjVbQx8mefU5t4oofgV_PhCriElpuqBDyJ9jemcq20PdQU0Tv6R_1yGO4NpEua)

如果我们想通过节点而不是浏览器发出请求，我们可以利用本教程开始时安装的请求包。如果你从未使用过请求包，你可以[在这里](https://www.npmjs.com/package/request)找到文档。下面是将在 URL 处的节点中运行的代码示例:domain.xyz/v3,呈现页面并传入可在 express“test”文件中使用的响应对象。

```
app.get("/v3", function(req, res){

  // Defining the request URL
  var options = {
      url: 'https://spreadsheets.google.com/feeds/cells/1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I/2/public/values?alt=json'
  }

  // Using the request package to pull the information using the options object defined above
  request(options, callback)

  // Callback function logging the request body in the console if it was successful
  function callback(error, response, body){
    if (!error && response.statusCode == 200) {
      console.log(body);

      // Rendering test express file while passing in the response object to be used.
      res.render('test', {response: response})
    } else {
      console.log(error)
    }
  }

}) 
```

回到前面概述的限制，我应该提到，尽管 v3 API 很容易使用，但我建议继续学习如何利用 API 的 v4。

除了 v3 要求您的数据是公开的、不允许对电子表格进行任何写入以及您的请求灵活性较低之外，最引人注目的问题可能是 v3 将在未来的某个时候被 Google 弃用，因此现在转移到更可靠的解决方案是值得的。

### 方法二:Node.js OAuth2 带 V4

让我们从 v4 开始吧！这一节将教你通过 Google 提供的方法来使用 v4(没有任何第三方包)。

Google 已经为如何开始与 Sheets API 的 v4 集成提供了很好的文档。他们提供了大多数后端语言的快速入门指南，包括 PHP、Python、Ruby、.NET，Go，Java，Go，当然还有 [Node.js](https://developers.google.com/sheets/api/quickstart/nodejs#step_3_set_up_the_sample) 。

您将在快速入门部分看到需要遵循的四个步骤。你可以直接按照谷歌提供的[页面](https://developers.google.com/sheets/api/quickstart/nodejs#step_3_set_up_the_sample)中的说明进行操作，但是我在下面留了一些指导说明。

#### 第一步:打开 Google Sheets API

*   一旦您启用了 API，您将获得一个客户机 ID 和客户机秘密，以后可以在 API 控制台中对其进行管理。出于这种认证的目的，您只需要保存在您的工作目录中的`credentials.json`文件(即:运行 app.js 或 index.js 的任何地方)。

#### 第二步:安装客户端库

*   如果您遵循了本教程前面的步骤，您将已经安装了依赖项。

#### 第三步:设置样本

*   如果您遵循了本教程前面的步骤，您将已经需要在您的`app.js`或`index.js`文件顶部的依赖项。现在，您可以将代码从快速入门页面复制到工作节点文件中。关于该准则的一些说明:

##### 认证

函数`fs.readFile()`实际上通过调用`authorize()`函数，使用您的`credentials.json`文件中的信息来启动认证过程。

测试代码被设置为在认证时调用`listMajors()`作为回调函数，这一点我们稍后会更改。

```
fs.readFile('credentials.json', (err, content) => {
  if (err) return console.log('Error loading client secret file:', err);
  // Authorize a client with credentials, then call the Google Sheets API.
  authorize(JSON.parse(content), listMajors);
}); 
```

为了执行身份验证，有一组实际运行该过程的函数。`authorize()`和`getNewToken()`函数是预定义的，可以移动到节点文件的底部，甚至可以从另一个文件中导出。没有必要把这段代码弄乱，它实际上可以放在你的路由之外，以便清理你的代码。

```
/**
 * Create an OAuth2 client with the given credentials, and then execute the
 * given callback function.
 * @param {Object} credentials The authorization client credentials.
 * @param {function} callback The callback to call with the authorized client.
 */
function authorize(credentials, callback) {
  const {client_secret, client_id, redirect_uris} = credentials.installed;
  const oAuth2Client = new google.auth.OAuth2(
      client_id, client_secret, redirect_uris[0]);

  // Check if we have previously stored a token.
  fs.readFile(TOKEN_PATH, (err, token) => {
    if (err) return getNewToken(oAuth2Client, callback);
    oAuth2Client.setCredentials(JSON.parse(token));
    callback(oAuth2Client);
  });
}

/**
 * Get and store new token after prompting for user authorization, and then
 * execute the given callback with the authorized OAuth2 client.
 * @param {google.auth.OAuth2} oAuth2Client The OAuth2 client to get token for.
 * @param {getEventsCallback} callback The callback for the authorized client.
 */
function getNewToken(oAuth2Client, callback) {
  const authUrl = oAuth2Client.generateAuthUrl({
    access_type: 'offline',
    scope: SCOPES,
  });
  console.log('Authorize this app by visiting this url:', authUrl);
  const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout,
  });
  rl.question('Enter the code from that page here: ', (code) => {
    rl.close();
    oAuth2Client.getToken(code, (err, token) => {
      if (err) return console.error('Error while trying to retrieve access token', err);
      oAuth2Client.setCredentials(token);
      // Store the token to disk for later program executions
      fs.writeFile(TOKEN_PATH, JSON.stringify(token), (err) => {
        if (err) return console.error(err);
        console.log('Token stored to', TOKEN_PATH);
      });
      callback(oAuth2Client);
    });
  });
} 
```

#### 测试节点中的 API 调用

Google 提供的最后一个函数`listMajors()`实际上将被用来定义使用 API 操纵什么信息。

ListMajors 目前从 Google 设置的预定义电子表格(id = ' 1 bximvs 0x ra 5 nfmdkvbdbzjgmuuqptlbs 74 ogve 2 upms ')中提取。在这种情况下，代码被设置为从范围 A2:E 中提取，您会注意到在 response.data.values 对象中返回的数据。

listMajors 函数(或者你把它改成的名字)是对原始认证函数的回调，这个函数将是你存放你的`res.render()`和发送数据到你的网页的地方。稍后将详细介绍。

```
/**
 * Prints the names and majors of students in a sample spreadsheet:
 * @see https://docs.google.com/spreadsheets/d/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms/edit
 * @param {google.auth.OAuth2} auth The authenticated Google OAuth client.
 */
function listMajors(auth) {
  const sheets = google.sheets({version: 'v4', auth});
  sheets.spreadsheets.values.get({
    spreadsheetId: '1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms',
    range: 'Class Data!A2:E',
  }, (err, res) => {
    if (err) return console.log('The API returned an error: ' + err);
    const rows = res.data.values;
    if (rows.length) {
      console.log('Name, Major:');
      // Print columns A and E, which correspond to indices 0 and 4.
      rows.map((row) => {
        console.log(`${row[0]}, ${row[4]}`);
      });
    } else {
      console.log('No data found.');
    }
  });
} 
```

#### 第四步:运行样本

*   在节点应用程序的命令行中，运行`node .`，这将提示您授权访问。在授予访问权限时，您可能会注意到在您的目录中创建了一个 token.json 文件。不要删除这个文件，因为它是用来记住这个特定的应用程序已被验证。

现在，您应该能够通过在终端中运行 app.js 文件来测试一切是否设置正确。当这种情况发生时，您应该看到从`listMajors()`打印到控制台的数据如下。

[![Example output from the listMajors() function.](img/f9eb04160e5ed5b8e4cd6841cdfc4248.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M3NycAtO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/vhxQnGKXWhFvkXIMlNeZYhKMo5Npe5RAyidjFrDj7sCghXo2LHmFPaGNQxE9ou7JbotsHiA1TD66IeX-9UPGUDWSPX9SCBSRskezhUv5EcBOsmZH7FgLZVVQWZUANJdZEK4H-0RZ)

#### 自定义 API 调用

现在您已经在应用程序中设置了身份验证层，是时候实际使用 API 了！如前所述，这意味着您将定制当前为`listMajors()`的回调函数。

为了提取数据，您将继续使用`sheets.spreadsheets.values.get()`，或者如果您希望提取多个部分，您可以使用`sheets.spreadsheets.values.batchGet()`功能。这两种方法的文档可以在以下位置找到:

*   [spreadsheets.get](https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets/get)
*   [spreadsheets . values . batch get](https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets.values/batchGet)

先说前者。

##### 获取数据

在这种情况下，您将只编辑 listMajors 函数中的一些内容，因为它已经用`get()`方法设置好了。为了清楚起见，我将其重新命名为`datapull()`,并做了以下调整:

1.  将电子表格 ID 更改为我的电子表格 ID
2.  编辑区域以仅提取我想要的单元格(' tab2！A1:本例中为“A10”)
3.  设置我命名为“rows”的响应对象
4.  通过 res.render 调用将数据从 rows 对象传递到 express 文件中

```
app.get("/v4-get", function(req, res){

  // Authorization
  fs.readFile('credentials.json', (err, content) => {
    if (err) return console.log('Error loading client secret file:', err);
    // Authorize a client with credentials, then call the Google Sheets API.
    authorize(JSON.parse(content), datapull);
  });

  // Callback function pulling data
  function datapull(auth) {

  const sheets = google.sheets({version: 'v4', auth});

  // Pulling the data from the specified spreadsheet and the specified range 
  var result = sheets.spreadsheets.values.get({
    // (1) Changed spreadsheet ID
    spreadsheetId: '1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I',
    // (2) Changed the range of data being pulledd
    range: 'tab2!A1:A10',
  }, (err, response)=>{
    if (err) return console.log('The API returned an error: ' + err);

    // (3) Setting data for daily tracking
    const rows = response.data.values;

    // (4) Rendering the page and passing the rows data in
    res.render('test', {rows: rows})
  });
  } 
```

##### 获取批量数据

如果您需要从电子表格中提取多个部分，您可以保持大部分代码不变，用`.batchGet()`替换`.get()`，同时对请求进行以下更改:

1.  将功能更新为`batchGet()`功能
2.  将“范围”更新为复数“范围”，同时输入要抓取的一系列单元格
3.  将每个范围的输出设置为常数
4.  将输出数据组织成两个数组
5.  在传递两个数组时呈现 express 文件(在本例中为“rows”和“data”)

```
app.get("/v4-batch-get", function(req, res){

  fs.readFile('credentials.json', (err, content) => {
    if (err) return console.log('Error loading client secret file:', err);
    // Authorize a client with credentials, then call the Google Sheets API.
    authorize(JSON.parse(content), datapull);
  });

  function datapull(auth) {
  const sheets = google.sheets({version: 'v4', auth});

  // (1) Updating function to use batchGet()
  var result = sheets.spreadsheets.values.batchGet({
    spreadsheetId: '1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I',

    // (2) Updating "range" to "ranges" and inputting the series of cells to grab within the array
    ranges: ['tab2!A2:A12', 'tab2!B2:B12', 
             'tab3!A2:A', 'tab3!B2:B']
  }, (err, resp)=>{
    if (err) return console.log('The API returned an error: ' + err);

    // (3) Pulling first two ranges into arrays - data format changes from.values to .valueRanges[range defined above].values
    const rows1 = resp.data.valueRanges[0].values;
    const rows2 = resp.data.valueRanges[1].values;

    // Pulling last two ranges into arrays- same data format as above
    const data1 = resp.data.valueRanges[2].values;
    const data2 = resp.data.valueRanges[3].values;

    // (4) Organizing the data output into two arrays: rows and data
    const rows = [rows1, rows2]
    const data = [data1, data2]

    // (5) Rendering the page and passing both the rows and data arrays through
    res.render('test', {rows: rows, data:data})
  });
  }
}); 
```

很明显，希望在 v4 中使用`.get()`和`.batchGet()`方法能够让您对希望发出的请求有更多的控制。Google API 通过 v4 支持许多其他方法，包括通过 [update()](https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets.values/update) 和 [batchUpdate()](https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets.values/batchUpdate) 方法编写的能力。记住这一点，让我们进入使用 Google API 的最后一种方法，它利用了第三方包。

### 方法三:Node.js OAuth2 - Google 开发者控制台和 NPM Google-电子表格包

最后一种方法利用了 google 开发人员控制台和一个名为 google-spreadsheet 的第三方 npm 包，可以说它比 Google 文档中概述的功能要干净得多。为了利用这种方法，第一步是安装 google-spreadsheet，并在您的文件顶部需要这些模块:

```
npm install google-spreadsheet 
```

```
var express     = require("express"),
fs              = require('fs'),
readline        = require('readline'),
{google}        = require('googleapis'),
request         = require('request'),
GoogleSpreadsheet = require('google-spreadsheet'),
creds             = require('./client_secret.json'),
app               = express(); 
```

通过这种方法实现身份验证需要几个管理步骤。

1.  转到[谷歌开发者控制台](https://console.developers.google.com/apis/dashboard)并导航到 API 部分。你应该会看到一个仪表板。
2.  单击“启用 API”或“库”,这将带您进入可以连接的服务库。搜索并启用 Google Sheets API。
3.  转到凭据并选择“创建凭据”。
4.  选择“服务帐户”并继续创建此服务帐户。你想叫它什么都可以。
5.  在“角色”下，根据您要授予的访问权限级别，选择“项目”>“所有者”或“编辑”。
6.  选择 JSON 作为密钥类型，然后单击“创建”。这应该会用您的凭证自动下载一个 JSON 文件。
7.  将这个凭证文件重命名为`client_secret.json`，并将其复制到您的工作目录中。
8.  最后的管理步骤是**超级重要的**！获取您的凭证文件中的“客户电子邮件”,并在您工作的表单中授予对该特定电子邮件的访问权限。你可以点击电子表格左上方的“分享”，然后将该电子邮件粘贴到字段中，启用“可以编辑”。如果不这样做，在尝试提取数据时会出现错误。

为了测试代码是否有效，我们可以开始使用 API！下面是一些示例代码，它们有效地验证并从所标识的电子表格的第二个选项卡中获取数据。我已经记下了身份验证发生的位置、请求的内容以及响应返回的位置。

在测试时，请确保查看控制台中的输出，它将记录错误或响应对象。如果您得到一个 403 错误，这意味着在设置身份验证时可能出错了。

```
app.get("/google-spreadsheet", function(req, res){

  // Identifying which document we'll be accessing/reading from
  var doc = new GoogleSpreadsheet('1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I');

  // Authentication
  doc.useServiceAccountAuth(creds, function (err) {

  // Getting cells back from tab #2 of the file
  doc.getCells(2, callback)

  // Callback function determining what to do with the information
  function callback(err, rows){

    // Logging the output or error, depending on how the request went
    console.log(rows)
    console.log(err)

    // Rending the test page while passing in the response data through "rows". Can access specific data points via: rows[i]._value
    res.render('test', {rows:rows})
  }
  });  
}); 
```

有了这个包，开始写电子表格也变得更加容易。例如，您可能希望每天向电子表格中写入您的进度。这里有一个你可以如何做的例子:

```
app.get("/google-spreadsheet", function(req, res){

  // Identifying which document we'll be accessing/reading from
  var doc = new GoogleSpreadsheet('1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I');

  // Authentication
  doc.useServiceAccountAuth(creds, function (err) {

  // Adding a row in tab #4 with the date and the number 1
  doc.addRow(4, { date: "=today()", progress: "1" }, callback)

  function callback(err) {
    if(err) {
      console.log(err);
    } else {
      console.log('You added your progress for the day.') 

      // Rendering test page
      res.render('test')
    }
  }

  });  
}); 
```

从这里，您可以利用 [google 电子表格包](https://www.npmjs.com/package/google-spreadsheet)的文档中的功能。

### 连接 Google Sheets 数据库来表达

希望最后几节有助于确定可以用来连接、验证和从 Google API 提取数据的不同方法。利用数据库生成实时使用该信息的网站/应用程序的最后一步是将数据传递给渲染的 EJS 文件。

此时，您应该已经设置了一些路由，这些路由从 API 获取您需要的数据，并将这些数据传递到您选择的 express 文件(通过您的`res.render()`调用)。在 express 文件本身中，您可以通过 [EJS(嵌入式 javascript)标签](https://ejs.co/)来利用这些数据。

这些标签允许您以内联方式或者在 HTML 旁边呈现 Javascript。例如，如果你从一个将数据作为“数据”和“行”传递给 express 文件的`batchGet()`函数中提取数据，你可以使用 EJS 标签循环遍历你的数据并直接打印到页面上。

输入(从[拉取该文件](https://docs.google.com/spreadsheets/d/1UIV4RkOx8KJK2zQYig0klH5_f8FCOdwIWV8YF2VyF8I/edit?usp=sharing) ):

```
<% include partials/header %>

<div class="container">

    <h2>This is a tutorial for using the Google Sheets API!</h2>

    <div>This data is pulling from the second tab:</div>

    <!--Looping through the 'rows' array and printing the output within the EJS tags-->
    <% for(i=0; i<10; i++) { %>
    <div >
        Rows data: <%= rows[0][i] %>, <%= rows[1][i] %>
    </div>
    <% } %>    

    <br>
    <hr>
    <br>

    <div>This data is pulling from the third tab:</div>

    <!--Looping through the 'data' array and printing the output within the EJS tags-->
    <% for(i=0; i<10; i++) { %>
    <div >
        On <%= data[0][i] %>, I made the following progress: <%=data[1][i]%>
    </div>
    <% } %>    

</div>

<% include partials/footer %> 
```

输出(从 EJS 文件渲染):

[![Output from the rendered EJS file, displaying the data from the rows and data variables passed through.](img/f47e9ed33ab7b5fcb25dc00c9ec2a625.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zh-Y8ZR4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/IA2rn_WrwNihFHSpmXJXS3fRm2JOZmd5SA2M-1Rfbn4up9B7qMmEDc46IrAnf8qnfIqvfatm-CZUg7xwKA6AB1pQWPQe303kq70ihUNhJqnLGdbhCn_YguKbllpUA9rZudjND3ZA)

一旦你建立了正确的数据库链接，你就可以自由地设计页面风格和添加额外的功能，就像任何其他的 HTML 页面一样，因为 EJS 本质上是嵌入了 Javascript 的 HTML。

### 完了，完了

我希望所提供的信息为您理解如何发出请求以及如何在 Node/Express 应用程序中嵌入请求提供了足够的基础。您将不得不考虑建立您可能需要的数据结构的个人方法，但是方法仍然是相同的。

如前所述，Google API 提供了多种从 Google 电子表格中访问数据的方法，我鼓励您使用最适合您需求的方法。我还鼓励您深入研究文档，了解更多可用的解决方案。我真的很兴奋看到你创造的东西，一如既往，如果你在学习过程中有任何问题，请随时给我发消息。

PS:来[在 Twitter 上讨论](https://twitter.com/stephsmithio/status/1127584436130701312)或者[订阅我的博客](https://mailchi.mp/3e320512ed0b/stephsmith)。