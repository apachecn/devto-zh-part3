# 理解 OAuth2 从 CLI 使用 Google APIs

> 原文：<https://dev.to/uf4no/understanding-oauth2-to-use-google-apis-from-a-cli-5g6e>

到目前为止，我从未需要使用任何谷歌 API，但最近我需要获得过去五年中我乘坐的所有航班的信息，尽管我的谷歌日历中有这些信息，但应用程序不允许我提取这些信息，所以这是深入了解谷歌 API 工作方式的绝佳机会。在快速研究了[日历 API 文档](https://developers.google.com/calendar/v3/reference/ "Google calendar API documentation")之后，我的计划是在 Node.js 中构建一个小型命令行应用程序，它将:

*   向用户询问一些过滤器，如日期范围、关键字和要检索的结果数量。
*   向谷歌认证
*   应用这些过滤器在用户日历中搜索事件
*   将结果写入控制台和文件中

感谢 Google 文档中提供的例子，我能够很快地从我的日历中检索数据，但理解它是如何工作的有点棘手，所以我决定重构代码，使用 async/await 而不是回调来完全理解它在做什么，并使它更可重用。然后，我在它周围包装了一个命令行程序，以包含过滤器功能。我是这样做的😎

### Google 日历 API 文档和示例

我做的第一件事是访问[活动资源页面](https://developers.google.com/calendar/v3/reference/events "Google Calendar Events documentation")，因为这是我想从日历中检索的内容。这个资源有一些方法，幸运的是， **[**list()** 方法](https://developers.google.com/calendar/v3/reference/events/list "Events.list documentation")返回一个事件列表并接受一些查询参数，这正是我所寻找的。然后我搜索了一些用 Node.js 编写的例子，找到了[快速入门页面](https://developers.google.com/calendar/quickstart/nodejs "Calendar Node.js quickstart")，其中解释了如何用 3 个简单的步骤创建一个简单的命令行应用程序:**

 ***   启用 Google 日历 API
*   安装 googleapis Node.js 包
*   复制[代码示例](https://github.com/gsuitedevs/node-samples/blob/master/calendar/quickstart/index.js "CLI example code")并运行它

正如文档中所详述的，应用程序第一次运行时，会要求您通过访问一个 URL 来授权访问。虽然这工作正常，我得到了一个事件列表，但我不明白认证过程是如何工作的，所以我搜索了更多信息，找到了[这一节关于不同的认证方法](https://github.com/googleapis/google-api-nodejs-client#authentication-and-authorization "Google API authentication documentation") (OAuth2，服务-服务和 API 密钥)和[关于 OAuth2 中使用的 OpenID 连接规范](https://developers.google.com/identity/protocols/OpenIDConnect "OpenID Connect documentation")的链接。一旦我建立了身份验证如何工作的基础，并决定了我想要使用的方法(OAuth2)，我就准备使用文档中提供的代码示例作为参考，从头开始编写我的应用程序。

### 向谷歌认证

使用任何 Google API 时，首先要做的是进入 [Google 的开发者控制台](https://console.developers.google.com/apis/dashboard "Google's developer console")并创建一个新项目:

[![new project in Google API console](img/978d95e67d782e9586bd2fe3e8ae05fb.png)](https://www.antonioufano.com/image_uploads/new_project.png)

创建完成后，转到 Library 部分，搜索 Google Calendar API(或任何您想要使用的 API)并启用它。这意味着一旦通过身份验证，您的应用程序将能够访问选定的 API。现在转到凭证部分，创建一组新的 OAuth 客户端 ID 类型的凭证。在下一页中，它会询问您应用程序的类型。因为我想创建一个命令行程序，所以我选择了 Other 并给它起了一个名字:

[![new project in Google API console, credentials](img/73b70b3f60b24daa4d82fb480b1d96ba.png) ](https://www.antonioufano.com/image_uploads/credentials.png) [ ![new project in Google API console, credentials two](img/29e375b81cbb563bc505ee856e2db92d.png)](https://www.antonioufano.com/image_uploads/credentials2.png)

完成后，您将获得一个与您的项目相关联的 client_id 和 client_secret。您可以在一个 JSON 文件中下载它们，该文件还包含其他属性，如 token_uri(在这里我们将请求一个访问令牌)和 redirect_uri(一旦被授权就重定向到哪里，在我们的例子中，只是 localhost)。下载该文件，因为我们稍后会在 CLI 程序中用到它。

但是我们为什么需要这些 id，它们是如何使用的呢？我试图在下图中解释 oAuth2 认证过程:

[![Google API authentication flow](img/7247065682eed7c4bd42251fa4ad31c1.png)](https://www.antonioufano.com/image_uploads/AuthFlow.png)

总之，认证流程将是:

*   使用 client_id 和 client_secret 创建 OAuth2 客户端实例
*   向谷歌请求一个认证 URL
*   要求用户访问认证 URL，并接受我们的程序将访问他的日历事件(这是基于我们定义的范围，稍后解释...)
*   一旦用户接受，谷歌认证将返回一个验证码
*   验证码被手动传递给我们的 CLI 程序
*   CLI 程序请求一个访问令牌来交换验证代码
*   将访问令牌保存为 OAuth2 客户端凭据
*   将访问令牌保存到文件系统，以便在后续请求中重用

所有这些步骤都是在[谷歌快速入门指南](https://developers.google.com/calendar/quickstart/nodejs "Node.js quickstart Calendar API")中提供的代码示例中完成的，但我将其重构为使用 async/await，并将其放在一个单独的模块中(GitHub 中的 [googleAuth.js](https://github.com/uF4No/gcal-event-finder/blob/master/src/googleAuth.js "googleAuth.js in GitHub") )，这样我就可以在其他程序中重用它。此模块导出一个函数来生成经过身份验证的 OAuth2 客户端。代码如下:

```
/**
 * googleAuth.js
 * 
 * Generates an OAuthClient to be used by an API service
 * Requires path to file that contains clientId/clientSecret and scopes
 */

const {google} = require('googleapis');
const fs = require('fs');

const inquirer = require('inquirer')

const debug = require('debug')('gcal:googleAuth')

// The file token.json stores the user's access and refresh tokens, and is
// created automatically when the authorization flow completes for the first
// time.
const TOKEN_PATH = 'token.json';

/**
 * Generates an authorized OAuth2 client.
 * @param {object} keysObj Object with client_id, project_id, client_secret...
 * @param {array<string>} scopes The scopes for your oAuthClient
*/
async function generateOAuthClient(keysObj, scopes){
  let oAuth2Client
  try{
    const {client_secret, client_id, redirect_uris} = keysObj.installed
    debug('Secrets read!')
    // create oAuthClient using clientId and Secret
    oAuth2Client = new google.auth.OAuth2(client_id, client_secret, redirect_uris[0])
    google.options({auth: oAuth2Client});

    // check if we have a valid token
    const tokenFile = fs.readFileSync(TOKEN_PATH)
    if(tokenFile !== undefined &amp;&amp; tokenFile !== {}){
      debug('Token already exists and is not empty %s', tokenFile)

      oAuth2Client.setCredentials(JSON.parse(tokenFile))
    }else{
      debug('🤬🤬🤬 Token is empty!')
      throw new Error('Empty token')
    }
    return Promise.resolve(oAuth2Client)
  }catch(err){
    console.log('Token not found or empty, generating a new one 🤨')
    // get new token and set it to the oAuthClient.credentials
    oAuth2Client = await getAccessToken(oAuth2Client, scopes)

    return Promise.resolve(oAuth2Client)
  }

}

/**
 * Get and store access_token after prompting for user authorization
 * @param {google.auth.OAuth2} oAuth2Client The OAuth2 client to get token for.
 * @param {array<string>} scopes The scopes for your oAuthClient
*/
async function getAccessToken(oAuth2Client, scopes) {
  const authUrl = oAuth2Client.generateAuthUrl({
    access_type: 'offline',
    scope: scopes,
  });
  console.log('⚠️ Authorize this app by visiting this url:', authUrl);
  let question = [
    { 
      type: 'input',
      name: 'code',
      message: 'Enter the code from that page here:'
    }
  ]
  const answer = await inquirer.prompt(question)
  console.log(`🤝 Ok, your access_code is ${answer['code']}`)
  // get new token in exchange of the auth code
  const response = await oAuth2Client.getToken(answer['code'])
  debug('Token received from Google %j', response.tokens)
  // save token in oAuth2Client
  oAuth2Client.setCredentials(response.tokens)
  // save token in disk
  fs.writeFileSync(TOKEN_PATH, JSON.stringify(response.tokens))

  return Promise.resolve(oAuth2Client)

}

module.exports = {generateOAuthClient} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了一个具有有效访问令牌的 OAuth2 客户机，我们就可以用它来查询日历 API。

### 从日历中检索事件

为了与日历 API 进行交互，我创建了另一个模块( [calendarService.js](https://github.com/uF4No/gcal-event-finder/blob/master/src/calendarService.js "calendarService.js in GitHub") 在 GitHub 中),该模块导出一个函数 **getEvents()** ,该函数接收 OAuth2 客户端(已经通过身份验证)和一个过滤器对象作为参数。然后通过添加 calendarId 构建 filterBy 对象，转换日期范围，添加 orderBy 和 maxResults 等其他值，最后调用 **events.list()** 方法。

```
/**
 * calendarService.js
 * 
 * Methods to interact with the Google Calendar API
 * 
 */
const {google} = require('googleapis');
const debug = require('debug')('gcal:calendarService')

/**
 * creates a Google Calendar instance using the OAuth2 client and call the list events with the filter
 * @param {google.auth.OAuth2} auth The OAuth2 client already authenticated
 * @param {object} filter Properties to filter by
 */
async function getEvents(auth, filter){
  try{

    const calendar = google.calendar({
      version: 'v3',
      auth
    })

    const filterBy = {
      calendarId: 'primary',
      timeMin: (new Date(filter.timeMin).toISOString()) || (new Date('2014-01-01')).toISOString(),
      timeMax: (new Date(filter.timeMax).toISOString())  || (new Date()).toISOString(),
      maxResults: filter.maxResults ,
      singleEvents: true,
      orderBy: 'startTime',
      q:filter.keyword
    }
    debug('Searching with filter %j', filterBy)
    const events = await calendar.events.list(filterBy)
    debug('found events: ', events)
    return events
  }catch(err){
    debug('🤬🤬🤬 Captured error in getEvents: %s', err)
    console.log(err)
  }

}

module.exports = {getEvents} 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**如果我想用多个函数扩展这个模块来调用 API 的不同方法，我可以从任何函数中提取日历客户端的创建，一旦创建，就将其作为参数传递给所有函数。*

### 命令行程序

最后一步是创建一个 CLI 程序，要求用户提供一些过滤器。我用 [inquirer](https://www.npmjs.com/package/inquirer "Inquirer NPM page") 来构建它，因为它非常容易使用；您只需定义一个问题数组，并将它们传递给 prompt 方法，该方法会解析带有答案的承诺。我还创建了另一个异步函数(triggerCalendarAPI ),它首先调用 googleAuth.js 模块传递 client_d 和 secret(以获得经过身份验证的 OAuth2 客户端),然后调用 calendarService.js 模块检索事件列表。一旦我们有了事件，我们可以将它打印到控制台或写入文件。在我的例子中，我将结果写入两个不同的文件:

*   results.json 只包含检索到的事件的名称、日期和位置
*   results_raw.json 包含检索到的事件的所有属性

另一件重要的事情是，我必须定义一个简单的范围，只从日历 API 中读取。根据您想要使用的 API 和操作，您必须对其进行更改。在每个 API 文档中可以找到不同的作用域。

```
/**
 * gCal Event Finder
 * CLI program to search and extract events from the user's calendar
 * using the Google Calendar API. Requires 
 * 
 */

const fs = require('fs');
const inquirer = require('inquirer')
const figlet = require('figlet')
const calendarService = require('./src/calendarService')
const googleAuth = require('./src/googleAuth')

const debug = require('debug')('gcal:index')

// IMPORTANT!: Define path to your secrets file, which should contain client_id, client_secret etc...
// To generate one, create a new project in Google's Developer console
const secretsFile = './keys/secrets.json'
const secrets = JSON.parse(fs.readFileSync(secretsFile));

// define the scope for our app
const scopes = ['https://www.googleapis.com/auth/calendar.readonly']

/**
 * Function that trigger calls to googleAuth and calendarService to 
 * retrieve the events from the calendar API.
 * @param {object} filter with properties maxResults, timeMin, timeMax and keyword 
 */
async function triggerCalendarAPI(filter){
  try{
    // get authenticated oAuth2 client 
    const oAuth2Client = await googleAuth.generateOAuthClient(secrets, scopes)
    debug('oAuthClient received, getting events....')
    // call the calendar service to retrieve the events. Pass secrets and scope
    const events = await calendarService.getEvents(oAuth2Client, filter)
    debug('Events are %j', events)
    // check if the are events returned
    if(events.data.items.length &gt; -1){
      //write raw results to file
      console.log(`Found ${events.data.items.length} events!`)
      await fs.writeFileSync('./results_raw.json', JSON.stringify(events.data.items))
      let res = [];
      // loop events array to filter properties
      events.data.items.forEach(event =&gt; {
        const start = event.start.dateTime || event.start.date;
        res.push({date:start,summary:event.summary, location: event.location})
      });
      //write filtered properties to another file
      await fs.writeFileSync('./results.json', JSON.stringify(res))

      console.log(`👏👏👏 - Results extracted to file results.json and results_raw.json`)
      return Promise.resolve(events)
    }else{
      throw new Error('🤯 No records found')
    }

  }catch(err){
    console.log('🤬🤬🤬 ERROR!!!' + err)
    return Promise.reject(err)
  }
}

/**
 * #########  Starts CLI program  #################
**/

console.log(figlet.textSync('gcal-finder', { horizontalLayout: 'full' }))
console.log(`Let's find some events in your calendar 🤔!`)

let filter = {};
let questions = [
{
  type: 'input',
  name: 'nResults',
  message: 'How many results do you want to retrieve? (default 100)'  
},
{
  type: 'input',
  name: 'dateFrom',
  message: 'Start date (YYYY-MM-DD)? (default 3 months ago)'  
},
{
  type: 'input',
  name: 'dateTo',
  message: 'End Date (YYYY-MM-DD)? (default today)'  
},
{
  type: 'input',
  name: 'keyword',
  message: 'Search by keyword? (just one 😬  default all)'  
},
]

inquirer.prompt(questions).then(answers =&gt; {
  const today = new Date();
  const temp = new Date()
  temp.setMonth(temp.getMonth() -3)
  const monthsAgo = temp.toISOString();
  filter = {
    maxResults: answers['nResults'] || 100,
    timeMin: answers['dateFrom'] || monthsAgo,
    timeMax: answers['dateTo'] || today,
    keyword: answers['keyword'] || undefined
  }
  debug('Searching with filter: %j ', filter)

  return triggerCalendarAPI(filter);

}).catch(err =&gt; {
  console.log('🤬🤬🤬 Error retrieving events from the calendar' + err)
}) 
```

Enter fullscreen mode Exit fullscreen mode

**重要提示:**secrets . JSON 文件包含我们应用程序的 client_id、client_secret 和 project_id(以及其他属性)。你可以从 [Google API 开发者控制台](https://console.developers.google.com/apis/dashboard "Google API developer console")的凭证部分下载完整的 json 文件。如果我们正在构建一个 web 应用程序，我们可以使用 redirect_uri 属性将用户发送到我们项目的特定 URL。

### 结论

这是我第一次为我个人需要的东西使用一个产品的 API，这真的让我想到了这种 API 给我们带来的所有可能性。我们可以将产品的原始功能扩展到我们或我们确定的市场需求。

我想分享这个命令行程序，人们可以使用 NPM 在全球范围内安装，但这意味着我必须将我自己项目的 client_id 和 secret 上传到 repo，所以，我没有这样做，而是将代码上传到 GitHub 中的[这个 repo，如果你想运行它，你只需在你自己的 Google 开发者控制台中生成一个新的 client_id 和 secret，将它们放在 secrets.json 文件中，你就可以开始了。](https://github.com/uF4No/gcal-event-finder "gcal-event-finder in GitHub")

希望这对你有用。

编码快乐！

* * *

*本文原载于[我的网站](https://www.antonioufano.com)。如果你喜欢，你可能会在[我的博客](https://www.antonioufano.com/blog)* 中找到有趣的往期文章**