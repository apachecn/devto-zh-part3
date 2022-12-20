# 让我们谈谈 web 应用程序

> 原文：<https://dev.to/smoogie/let-s-talk-with-web-application-19j7>

许多公司在客户服务和内部支持方面采用聊天机器人。这些解决方案中的大多数都与一些消息平台相关，如脸书·梅桑格、Whats App、Telegram 等。同时还有很棒的语音助手(像 Alexa，Google assistant 等。)由非公司用户私人使用。不久前，聊天机器人只是一些应用程序(主要是 web 应用程序)的另一个界面，但现在它们有了自己的身份、幽默、知识库和个性。它们变得更像助手，而不是问答界面，为您的系统收集和呈现数据。对人类来说，语音界面比任何视觉解决方案都更自然。这是否意味着不再使用标准网站？不会。在大多数情况下，聊天机器人和网页会相互补充，在更复杂的情况下，你会在你的 web 应用程序中重用你的聊天机器人，这将改善用户体验。此外，越来越多的注意力放在可访问性上，这导致了用户体验的新趋势。语音界面是这一运动的一部分。

创建好的聊天机器人并不容易。创建好的语音助手更难，但真正的挑战是将其与 web 应用程序集成。有许多你需要做的事情。首先，你的 app 需要倾听用户，用 *<音频>* 和 *getUserMedia()* 或者其他浏览器中可用的音频 API 可以实现什么。当音频被捕获时，将其传输到后端。你可以用 websocket 和 node.js 来做，后端需要根据用户所说的话来创建音频块，但在此之前，你需要将背景噪音从录音中有价值的部分分离出来。当你得到录音的特定部分时，你需要使用语音识别软件来获得用户所说的文字记录。有现成的图书馆或服务可供使用。当你需要文本时，使用 NLP 来理解文本。同样，有很多图书馆和服务。最后，当你知道用户说了什么，选择正确的动作并传递用户提供的参数。现在你可以使用一些基本的代码来获取数据，进行计算等。完成后，将数据发送到前端，并在可视化中进行更改。还有，语音助手要说点什么，你就用语音合成。

大多数都需要大量定制代码。当然，你可以在前端做很大一部分软件，并简化一点，但这需要大量的硬件资源。此外，如果您使用一些语音识别和 NLP 服务，您不希望任何人都可以使用您的私钥和密码。这就是为什么您将用户音频发送到您的后端并向那里的服务发出请求。

有很多东西要涵盖，很多东西要学习。如何开始？如何在不阅读大量资源和编写大量代码的情况下直接进入主题？有没有可能用很小的努力来测试一些这种技术？有没有可能创造概念证明，让投资者或你的老板感兴趣？网络语音 API 来拯救！

* * *

先说简单的。我想创建一个简单的助手，这将有助于我的演讲演示。在谈话过程中，我喜欢边走边做手势。为了在幻灯片和代码之间切换，或者更换幻灯片，我需要回到电脑前点击几下。这让我很不舒服。我想只在现场编码的时候离电脑近一点，说话的时候自由一点。让我们做一个语音助手，它将为我们做所有这些事情。我们将使用:Web 语音 API 来接收语音命令并进行助理对话；用于 NLP 的 Dialogflow 和用于演示的 reveal.js。

在本例中，我们将创建一个简单的演示文稿，语音助手将切换幻灯片。它将教你基础知识，使更复杂的解决方案。请记住，我想介绍几项技术，而不是教你如何使你的应用程序安全以及如何正确配置服务器。我将帮助您开始语音应用领域的旅程，并掌握此类应用的一般概念。在应用程序的生产过程中，你应该包括一些额外的东西，如:

*   把很多东西放在一些配置文件中或者从数据库中读取
*   处理错误和异常
*   与后端的 dialogflow 通信
*   确保所有凭据的安全

我们将把大部分逻辑放在前端。代码将是定制的，基于我的 Chrome 设置，你可能需要改变一点点，使其与你的浏览器兼容。我在桌面上使用最新版本的 Chrome 浏览器，但在 Firefox 上并非所有功能都能正常运行。

首先，我们将使用 [reveal.js](https://revealjs.com/) 构建简单的演示。它将包含一些幻灯片和一些虚拟文本。我们从用 ubuntu 16 和 apache 运行[流浪盒](http://www.vagrantup.com/)开始。项目文件夹映射到 */var/www* ，这将是应用程序的根文件夹。现在必须添加一些 vhost 和 ssl，后面会用到。为了尽可能简单，登录到 travel box 并创建密钥，证书放在*/home/travel*目录:

```
> openssl genrsa -out slides.key 2048
> openssl req -new -x509 -key slides.key -out slides.cert -days 3650 -subj /CN=slides 
```

我们转到*/etc/Apache 2/sites-available*并为虚拟主机创建配置。同样，为了加快速度，我们使用默认值—复制一个默认配置并更改它。我们将使用 *[https://slides](https://slides)* 作为我们应用程序的 URL:

```
<VirtualHost *:443>
 SSLEngine on
 ServerName slides
 SSLCertificateFile /home/vagrant/slides.cert
 SSLCertificateKeyFile /home/vagrant/slides.key
 ServerAdmin webmaster@localhost
 DocumentRoot /var/www/public
 <Directory /var/www/public>
  Options Indexes FollowSymlinks MultiViews
  AllowOverride All
  Order allow,deny
  allow from all
 </Directory>
</VirtualHost> 
```

现在我们在根目录下创建公共文件夹，在那里我们添加随机文本的 index.html。这样，我们可以确保一切都按预期运行。我们还需要启用站点，将 ssl 模块添加到 apache 并重新启动它。它需要更多的命令:

```
sudo a2ensite *
sudo a2enmod ssl
sudo service apache2 restart 
```

最后一件事是在主机上添加虚拟主机。我使用 Windows，所以我转到*C:\ Windows \ System32 \ drivers \ etc \ hosts*并添加带有我的虚拟机和幻灯片域的 IP 的行。现在在 Chrome 上，当我们将要打开 *[https://slides](https://slides)* 时，我们在虚拟机上看到来自 */var/www/public* 的哑页面。服务器准备好了，现在让我们写一些代码。

在公共文件夹中，我们从[https://github.com/hakimel/reveal.js](https://github.com/hakimel/reveal.js)添加文件夹(css，js，lib，plugin)。在 *public/lib/js/* 中，我们添加了来自[https://github.com/kjur/jsrsasign](https://github.com/kjur/jsrsasign)的 jsrsasign-all-min.js 和 *public/js/* 文件夹中的几个空文件:google_token.js，slides.js，voice.js .我们将 index.html 改为:

```
<!DOCTYPE html>
<html class="sl-root decks export loaded ua-phantomjs reveal-viewport theme-font-montserrat theme-color-white-blue">
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    deck: Slides
    <meta name="description" content="Slides">
    <link rel="stylesheet" href="/css/reveal.css">
    <link rel="stylesheet" href="/css/theme/white.css">
</head>
<body>
<div class="reveal">
    <div class="slides">
        <section>Some first slide</section>
        <section>Single Horizontal Slide</section>
        <section>
            <section>Vertical Slide 1</section>
            <section>Vertical Slide 2</section>
        </section>
    </div>
</div>
<script src="lib/js/jsrsasign.min.js"></script>
<script src="js/google_token.js"></script>
<script src="js/reveal.js"></script>
<script src="js/slides.js"></script>
<script src="js/voice.js"></script>
</body>
</html> 
```

在 *public/js/slides.js* 中，我们初始化 reveal.js 幻灯片引擎:

```
Reveal.initialize({
    controls: true,
    controlsLayout: 'edges',
    progress: true,
    transition: 'convex',
    backgroundTransition: 'convex',
    history: true
}); 
```

在 Chrome 中，我们去 [https://slides](https://slides) 。当我们第一次打开页面时，Chrome 会通知我们证书问题，但我们打算接受风险，打开页面。之后，我们应该看到第一张幻灯片，并手动在它们之间切换。如果你跟着我到了这一步，我们可以说基本设置已经完成，让我们开始语音识别。

在我们继续之前，我们需要将麦克风插入电脑。当我们准备好了，让我们更改 *public/js/voice.js* 文件。首先，我们创建语音识别对象，并将其设置为连续运行。它会捕捉声音，并尝试将其与模式匹配。如果我们不设置它连续运行，它会在第一场比赛后关闭。此外，我们将语言设置为*美国* :

```
var SpeechRecognition = SpeechRecognition || webkitSpeechRecognition;
var speechRecognizer = new SpeechRecognition();
speechRecognizer.continuous = true;
speechRecognizer.lang = "en-US"; 
```

我们的监听器已经准备好激活了，所以我们写了一个函数来激活它:

```
function startListener() {
    speechRecognizer.start();
} 
```

现在，我们的应用程序将会监听，但不会对它所听到的内容做任何事情。我们需要定义当事情发生时该做什么。我们可以通过为 SpeechRecognition 事件设置回调来实现这一点。首先，我们希望记录所有启动/停止事件。我们希望在停止事件时重新启动监听器。您可能会问“如果我们将语音识别设置为连续工作，为什么要重新启动它？”这是个好问题。你看，在这种情况下,“持续地”意味着“不要在第一个被认出的句子后停下来，尽可能长时间地听，当你抓住什么东西时，把它投入到事件中去”。一段时间后，Chrome 关闭 listener，你的语音识别将停止工作。此外，如果发生一些错误，语音识别将被关闭，在这种情况下，我们还想再次打开它。下面是代码:

```
speechRecognizer.onaudiostart = function(event) {
    console.log('onaudiostart');
};
speechRecognizer.onaudioend = function(event) {
    console.log('onaudioend');
};
speechRecognizer.onend = function(event) {
    console.log('onend');
    speechRecognizer.start(); 
};
speechRecognizer.onnomatch = function(event) {
    console.log('onnomatch');
};
speechRecognizer.onsoundstart = function(event) {
    console.log('onsoundstart');
};
speechRecognizer.onsoundend = function(event) {
    console.log('onsoundend');
};
speechRecognizer.onspeechstart = function(event) {
    console.log('onspeechstart');
};
speechRecognizer.onspeechend = function(event) {
    console.log('onspeechend');
    speechRecognizer.stop();
};
speechRecognizer.onerror = function(event){
    console.log(event)
}; 
```

我们需要对捕获的声音和识别的语音做些什么。我们将在结果为事件时监听*,该事件在演讲者识别器创建抄本时被触发。这里有一点很重要——语音识别在解析语音的过程中会不断改变结果。您将在流程结束之前获得结果，以构建最终成绩单。我们需要检查结果，只有当它是最终的，我们才会使用它。抄本是简单的文本，所以我们可以像操作文本输入一样操作它。我们将记录所有捕获的响应，如果它与我们的操作匹配，我们将运行它。请记住，我们是在字符串上操作，所以任何空格，字符大小(小写/大写)将改变它。我们创建这样的代码:* 

```
speechRecognizer.onresult = event => {
    for(var i=event.resultIndex; i<event.results.length; i++){
        var transcript = event.results[i][0].transcript;
        if(event.results[i].isFinal){
            transcript = transcript.trim().toLowerCase();
            console.log(transcript);
            switch (transcript) {
                case "right":
                    Reveal.right();
                    break;
                case "left":
                    Reveal.left();
                    break;
                case "down":
                    Reveal.down();
                    break;
                case "up":
                    Reveal.up();
                    break;
            }
        }
    };
}; 
```

现在我们可以运行一个小测试——我们打开 [https://slides](https://slides) 。在 Chrome 中，我们看到，在 navbar(靠近我 url)中出现了一些图标，这表明 web 应用程序试图访问麦克风，作为用户，我需要设置网页的权限。我只能为特定的 url 设置权限，如果我的应用程序是通过 https 提供的，我可以永久设置它。如果没有使用 ssl 进行保护，应用程序每次请求访问麦克风时都会要求接受。我们设置了 vhosts，添加了 ssl，这样就可以设置一次就忘了。所有设置完成后，我们刷新页面，开始通过麦克风发出语音命令。如果你跟着我，创造了所有需要的代码，你应该能够改变像“右”，“左”，“下”，“上”简单的词幻灯片。如果有一些问题，打开控制台，看看是否捕捉到任何警告或错误，并尝试说命令稍微慢一点或大声一点。不要给 fast 几个命令，因为系统将创建一个抄本，它代表由几个单词组成的句子。如果解析时间太长，试着调整一下麦克风的设置，减少背景噪音。

我们成功了……但是有点不对劲。我们可以说“右”、“左”、“下”、“上”，它会切换幻灯片，但我们想说“将幻灯片切换到下一张”或“下一张”或其他任何东西。使用当前的解决方案，我们将需要在 switch 中编写所有选项…如果我的语音识别将创建错误的抄本，该怎么办。“app”而不是“up”？我的母语不是英语，语音识别也不完善。试图涵盖所有可能的情况，我最终会有数千行代码。我需要一个真正的聊天机器人解决方案。

* * *

让我们继续并添加一些 NLP。我提到把你的 NLP 放在后端，但是这里我们将在前端连接 dialogflow。首先，我们需要在 dialogflow 中配置一个代理。我们打开[https://dialogflow.com/](https://dialogflow.com/)，用我们的谷歌账户登录，我们创建新的代理。在其中，我们创建了一个有 4 个条目的新实体“slide”。每个条目都有一些值(类似于 id)和同义词(特定条目的附加指针)。看起来是这样的:

[![](img/ed6af2267f208444bb197c073f9cf1dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OEfdXZmr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z04ig8aswnmmdlo7yfg9.png)

现在我们需要意图，它代表对话选项。为此，我们添加了新的意图 *chng_slide* 。我们添加了三个训练短语“下一张幻灯片”、“获取下一张幻灯片”、“请为下一张幻灯片更换幻灯片”，并且我们将单词“下一张”与实体“幻灯片”连接起来(当您选择文本的一部分时，将显示带有选择实体选项的弹出窗口)。我们删除文本有效载荷，并用 json 添加自定义有效载荷(使用“添加响应”按钮并选择“自定义有效载荷”):

```
{
  "action": "change_slide",
  "params": {
    "new": "$slide"
  }
} 
```

我们的自定义有效负载将作为 API 调用的响应发送。训练短语将用作文本比较器的文本示例。文本和实体之间的连接通知 NLP 处理器我们的示例的哪一部分是动态的，并且可以用作定义实体的参数。配置如下所示:

[![](img/7ec80f597c145ffa1a6ee0e5855596ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Eh6ld3Vw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6c0ze7cvd911c552iuuu.png)

现在我们的机器人已经配置好了，我们需要把它和前端连接起来。为此，我们需要访问使用 OAuth 的 dialogflow REST API。让我们一起度过这个难关。首先，我们需要创建一个服务帐户，从谷歌云服务访问我们的机器人。在代理配置选项的 dialogflow 中，在“Google 项目”部分，我们有“项目 Id”和“服务帐户”——记在某个地方，我们稍后会用到。

[![](img/2cd71c09c18c1629b367237b6b81038a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OVm_jgO9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vm9g3milpk3njamodieh.png)

服务帐户已链接到 google 项目设置-打开它。在谷歌云平台管理的新窗口(从“服务帐户”链接打开的页面)，我们需要添加新的服务帐户。为此，我们需要设置角色:dialogflow 客户端。我们可以在左栏找到 dialogflow，在右栏选择 client，或者直接开始输入“dialogflow API client”。

[![](img/a790fb36df3e7c6bb005247ef0e7f292.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ah0zF635--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1bpxtrjd01awp5hxq2d3.png)

在底部，有两个复选框，选择第一个选项以共享新的私钥，并选择 JSON 作为密钥类型。

[![](img/5b0ecc0dce2ce0cbe20ebcaca573ce38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JtXQG3gl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eazz0sdd1tvx5dbx1cf7.png)

当我们保存它时，我们还将获得 json 文件，其中包含访问我们服务的数据。让我们假设我们的 json 看起来像这样:

```
{
  "type": "service_account",
  "project_id": "my_project_id",
  "private_key_id": "some_long_private_key",
  "private_key": "-----BEGIN PRIVATE KEY-----\nLOTOFCHARINHERE==\n-----END PRIVATE KEY-----\n",
  "client_email": "slides@my_project_id.iam.gserviceaccount.com",
  "client_id": "my_client_id",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://accounts.google.com/o/oauth2/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/slides%40my_project_id.iam.gserviceaccount.com"
} 
```

现在我们回到我们的代码。为了与 REST API 通信，我们需要 OAuth 令牌，我们可以通过向[https://www.googleapis.com/oauth2/v4/token](https://www.googleapis.com/oauth2/v4/token)发出请求并添加 JWT 令牌来实现。对于 JWT 令牌，我们使用来自 JSON 文件的数据，这是我们在 google 云平台中创建服务帐户时获得的。收到的 OAuth 令牌将被全局存储。这种方法不安全，不要在生产中使用。

接收 token 所需的所有代码都将保存在*public/js/Google _ token . js*中。首先，我们用 JSR sign 8 . 0 . 12 创建 JWT 令牌。根据我为 JSON key 提供的代码，我们的 js 应该是这样的:

```
const unix = Math.round(+new Date()/1000);
const privatekey = "-----BEGIN PRIVATE KEY-----\nLOTOFCHARINHERE==\n-----END PRIVATE KEY-----\n";
const jwtHeader = {
    alg: "RS256",
    typ: "JWT"
};
const jwtPayload = {
    iss: "slides@my_project_id.iam.gserviceaccount.com",
    scope:"https://www.googleapis.com/auth/cloud-platform",
    sub: "slides@my_project_id.iam.gserviceaccount.com",
    aud: "https://www.googleapis.com/oauth2/v4/token",
    iat: unix,
    exp: unix + 3600
};
const jwtToken = KJUR.jws.JWS.sign('RS256',jwtHeader, jwtPayload, privatekey); 
```

现在我们创建 post 数据，并通过请求将它发送给 google API。在全局变量中，我们保存 OAuth 令牌，它是从响应中获得的。收到 dialogflow 令牌后，我们开始监听用户语音。这样，我们可以确保在没有 Oauth 令牌的情况下不会对 dialogflow 进行任何调用。这是我们的代码:

```
var gapiToken = 0;
var tokenRequestBody = "grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer&assertion="+jwtToken;
fetch('https://www.googleapis.com/oauth2/v4/token', {
    method: 'POST',
    body: tokenRequestBody,
    headers: {
        'content-type': 'application/x-www-form-urlencoded'
    }
})
.then(response => response.json())
.then(jsonData => {gapiToken=jsonData.access_token; startListener()})
.catch(err => {}); 
```

很好，我们可以和 API 交流。所以让我们从一个结果动作
开始修改 *public/js/voice.js* 中的代码

```
speechRecognizer.onresult = event => {
    for (var i = event.resultIndex; i < event.results.length; i++) {
        var transcript = event.results[i][0].transcript;
        if (event.results[i].isFinal) {
            transcript = transcript.trim().toLowerCase();
            analizeData(transcript);
        }
    }
}; 
```

在函数*analysis data*中，我们调用了 dialogflow API。我们需要向特定的 url 发送请求，其中包括项目 id 和会话 id:[https://dialog flow . Google APIs . com/v2/projects/your _ project _ id/agent/sessions/yuor _ session _ id:detect intent](https://dialogflow.googleapis.com/v2/projects/your_project_id/agent/sessions/yuor_session_id:detectIntent)。项目 id 可以从 dialogflow 项目设置中获取(您记得我们几分钟前注意到它)。*会话*是会话 id，由我们创建，它必须对我们的每个客户都是单独的，因为 dialogflow 像上下文一样保存在会话数据中。出于测试目的，我们将硬编码会话 id:

```
function analizeData(text) {
    console.log(text)
    let query = {
        "queryInput": {
            "text": {
                "text": text,
                "languageCode": "en-US"
            }
        }
    };
    fetch('https://dialogflow.googleapis.com/v2/projects/my_project_id/agent/sessions/test_session:detectIntent', {
        method: 'POST',
        body: JSON.stringify(query),
        headers: {
            'content-type': 'application/json',
            'Authorization': 'Bearer ' + gapiToken
        }
    }).then(response => response.json()).then(serveResponse);
} 
```

必须解析来自 dialogflow 的响应。在此基础上，我们将在应用上有所作为。我们对自定义有效负载感兴趣，它包含动作名称及其参数。我们使用 action 作为函数名，params 作为参数(同样，当您在生产应用程序上工作时，请确保检查响应、验证响应并正确解析响应):

```
function serveResponse(data) {
    data.queryResult.fulfillmentMessages.forEach(fullfilment => {
        if (fullfilment.payload != undefined){
            window[fullfilment.payload.action](fullfilment.payload.params);
        }
    });
}
 function change_slide(params) {
     Reveal[params.new]();
 } 
```

现在当你测试它，它仍然工作，但你可以使用更多的短语来激活行动。此外，当您登录到 dialogflow 时，请查看培训部分。在那里你可以找到所有收到的信息，你可以用它们来“教”你的机器人。您可以为现有的意图分配新的短语，并创建新的意图来做更多的事情或对传入的消息做出正确的反应。

* * *

还有一件事，我们想补充一下。正如你所记得的，我们想创造一个助手，它不仅能执行你的命令，还能和你说话。我们现在不会做任何花哨的事情——我们只会给我们的应用程序添加语音。任何时候来自 dialogflow 的响应都会包含文本有效载荷，我们希望听到它。

首先，我们需要初始化语音合成并创建要阅读的文本。为此，我们稍微修改一下*服务器响应*函数:

```
function serveResponse(data) {
    data.queryResult.fulfillmentMessages.forEach(fullfilment => {
        if(fullfilment.text != undefined) {
            fullfilment.text.text.forEach(text => {
                let utterThis = new SpeechSynthesisUtterance(text);
                utterThis.voice = voice;
                speechSynthesis.speak(utterThis);
            })
        }
        if (fullfilment.payload != undefined){
            window[fullfilment.payload.action](fullfilment.payload.params);
        }
    });
} 
```

现在每次我们收到文本，它都会被阅读。我使用的是 Windows，所以当我测试语音合成时，我在本地使用默认的系统声音。我想用英语语音，听起来更自然。首先，我需要知道我可以使用什么，为此我记录所有可用的声音，这些声音是通过*speech synthesis . get voices()*获得的。当我知道我有什么声音时，我可以测试它们并选择一个…我建议你也这样做。

我选择了“谷歌英国英语女性”。我们知道可用选项现在不会改变，所以我们可以硬编码它的名称。在我们的 public/js/voice.js 的顶部我们添加了几行:

```
var voice = undefined;
var voices = undefined;
function getVoices() {
    if (typeof speechSynthesis === 'undefined') {
        return;
    }
    voices = speechSynthesis.getVoices();
    if(voices.length > 0) {
        selectVoice('Google UK English Female');
    }
}
function selectVoice(name) {
    for (i = 0; i < voices.length; i++) {
        if (voices[i].name === name) {
            voice = voices[i];
        }
    }
}
getVoices();
if (typeof speechSynthesis !== 'undefined' && speechSynthesis.onvoiceschanged !== undefined) {
    speechSynthesis.onvoiceschanged = getVoices;
} 
```

很好。现在我们可以和我们的演示文稿聊天了。我们可以轻松地添加 assistant 提供的更多功能——我们只需要在 js 中添加新方法，在 dialogflow 中创建更多意图。此外，我们可以教我们的机器人识别新的短语，而不改变我们的应用程序代码。

* * *

下一步是什么？您可以从将与 NLP 的通信转移到后端开始，清理代码，添加错误处理。接下来，您可以添加更多的动作，如:改变背景，开始/停止，全屏，阅读幻灯片文本。

等你会玩完 app，你就可以去想更有用的项目了。当然，你需要记住，这个解决方案仅限于特定的浏览器和特定的语言(目前)。它不是很快，在识别语音方面有一些问题，但它足以创建原型并向客户展示您的想法。有了优秀的开发团队，你可以为语音应用程序创建自己的解决方案。为了让你的应用程序可以在其他浏览器上使用，你需要将音频流传送到后端，并在那里进行解析。

我希望这篇文章对您有用，并帮助您开始 web 语音应用程序之旅。我希望你能测试其他的可能性，并按照你的想法创造有趣的解决方案，这将使互联网更加互动，也希望你的工作能激励他人。

* * *

另外，让我们通过一些例子来描述我们什么时候可以使用这种解决方案。

Olga 是网上服装店的老板，在脸书上有很多粉丝。她为脸书·梅桑格添加了聊天机器人，它提供的功能很少:根据与客户(聊天机器人造型师)的交流，建议一些衣服并创造完全的风格化，可以选择下单或查看订单状态。当她的商店变得受欢迎时，她创建了基于 WebAR 的在线更衣室，虚拟造型师基于语音识别和聊天机器人。现在，用户可以打开她的网页，激活更衣室应用程序，在这个程序中，他们可以与虚拟造型师交谈，并尝试一些衣服，甚至完全的风格化，稍后可以通过简单的语音订单订购。

让我们想想其他的用例，例如 Bob 的建筑公司，它建造了获奖的建筑。前段时间 Bob 为他的团队创建了专门的 web 应用，基于 WebVR、WebAR 和语音识别。在 WebGL 模块的帮助下，建筑师可以创建项目，供营销团队在使用 WebVR 模块进行演示时使用。此外，施工队使用 WebAR 模块来比较现实与计划，并找到墙壁，门，电等。该设备具有标准输入和语音识别接口，使虚拟助手的实现成为可能。基于建筑师提供的语音命令的系统变更项目。它也用于在投资者面前演讲时，当演讲者改变灯光，家具，墙壁装饰等。只有请助手来做。施工队不需要任何花哨的遥控设备或操纵杆来操作 AR，他们只需与助手交谈，如“窗户应该在哪里”，“这里应该立什么样的墙”，“我应该在哪里放电缆”等。

想想路易斯拥有的汽车工厂。他用 AR、图像识别和语音助手创建了花哨的 web 应用程序。他看了看引擎盖下面，拉了拉一些旧电缆，但他不确定该把它连接到哪里。他可以问“这条电缆应该连接到哪里？”他的助手给他指示，而 app 可视化电缆应该连接在哪里。

让我举最后一个例子——城市指南页面，它为残疾人和盲人提供语音支持。语音识别和机器人负责根据语音命令浏览页面，而语音合成则向用户呈现页面内容。

[媒体上的原始帖子](https://medium.com/front-end-weekly/lets-talk-with-web-application-684214d34bdc)