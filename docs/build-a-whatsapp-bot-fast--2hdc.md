# Whatsapp Bot API 构建一个 Whatsapp Bot，快速⚡

> 原文：<https://dev.to/jajoosam/build-a-whatsapp-bot-fast--2hdc>

几个月前，我开始在 [Telegram](https://t.me) 上制作聊天机器人——我看到了一些 WhatsApp 的 API，但它们都不是官方的，有可能让你的号码被屏蔽📱 ❌

前阵子看到 [Twilio](https://twilio.com) 有官方的 WhatsApp API。30 分钟后，我在 WhatsApp 上做了一个[维基百科机器人](https://wikibot.4ty2.fun)👇

[![](img/3ddcf599475364e773293164d5e54419.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rOxVTVOx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/Untitled-b9da3f92-94c0-4f97-8afb-787110d8a9d3.png)

这是一个帮助你在 WhatsApp 上制作你自己的聊天机器人的教程——这些机器人可以立即被超过 20 亿用户使用，并且有很多可能的事情🎓

我等不及要看你做什么了！现在，我们开始吧🏃‍♂️

## 🔑帐户和密钥

首先，注册参加[Twilio](https://www.twilio.com/try-twilio)——这是免费的，你不需要信用卡💳

[![](img/a0b354acd23f4cc9f452566e071bd69d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vGgUVy53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/screely-1535885763017-fc654067-9557-4bf7-98b5-4337911ff4ba.png)

验证完电话号码后，选择 Procuts >可编程 SMS，然后继续命名您的项目。

[![](img/7e3f54aa9bccb642edb26a58dbe63769.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uNYFoiUW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/screely-1535885937977-c5a924ec-8cc3-4430-9345-9b5e1dc74ef3.png)

随意跳过添加队友的步骤——你现在不需要。

现在，您必须注意构建 WhatsApp bot 所需的一些认证密钥👇

[![](img/df89c550ef62189e08d21ff7a48c8232.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v3ICO9qO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/screely-1535886250966-f68b6cfb-c104-4adf-80e7-4e3f9bd15b5b.png)

最后一步——设置你的 WhatsApp 沙盒[这里](https://www.twilio.com/console/sms/whatsapp/sandbox)——选择任意数字，按照页面上的说明加入你的沙盒。

[![](img/dbba229b5d00ae1d843d46f9cc55d85b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2vIgik1Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/screely-1535886798623-1dac1ba9-c362-4e49-87ab-7bbb6138e8c7.png)

Aaaaaand 您就完成了凭证设置！别担心，这是本教程中最难的部分😛

## 🚀入门指南

为了不在设置上花费太多时间，我创建了一个环境(用 [repl.it](https://repl.it) ！)可以在浏览器中使用。把头伸到[这里](https://repl.it/@jajoosam/wikibot-start)，等几秒钟叉开。

接下来，[创建一个`.env`文件](https://repl.it/site/docs/repls/secret-keys)，并在`1`和`2`T5 行输入你的账户 SID 和 Auth Token

```
SID=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX # Account SID
KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX # Auth Token 
```

您可以看到，这个环境已经安装了依赖项，并且设置了一个`express`服务器。不过，我们仍然需要给 Twilio 一个 URL 来接收传入的消息🔗

让我们回到 [WhatsApp 沙箱](https://www.twilio.com/console/sms/whatsapp/sandbox)，并放入一个 webhook URL 用于接收消息。

[![](img/ce901feb98f5aec7ab3a9389362d5705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fUZlJXWe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/Untitled-3ed5263b-c6d8-492b-ba08-b4644ab502cf.png)

这个 URL 必须是你在 [repl.it](http://repl.it) 项目+ `/incoming`的预览面板上看到的

[![](img/a3fa4e7c816f271896f6875a6df08661.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T5V9GXgk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/Untitled-1779b21f-9100-4942-b732-320dc48c5f76.png)

我们现在终于可以读取发送给机器人的消息了。导航到`index.js`，然后在 webhook 处理程序中添加一个简单的`console.log()`👇

```
app.post('/incoming', (req, res) => {
  console.log(req.body)
}); 
```

当您向 bot 发送消息时，您应该能够在 repl 控制台中看到类似这样的内容👨‍💻

[![](img/5ef80570923f67b5cc75d874db3afde1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w5gOY1YZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikibot.surge.sh/Untitled-163eb09e-e6ab-4910-badb-d8aa0aa789f7.png)

构建一个 echo bot 看起来会像这样，使用`twiml`来编写消息👇

```
app.post('/incoming', (req, res) => {
    const twiml = new MessagingResponse();
    twiml.message(req.body.Body);
    res.writeHead(200, {'Content-Type': 'text/xml'});
  res.end(twiml.toString());
}); 
```

但是，由于我们实际上是在尝试构建一个有用的机器人——让我们使用信息丰富的 API 吧！

## 🌐获取信息

DuckDuckGo 有一个惊人的免费即时回答 API。它接受一个查询，并从维基百科等返回一个摘要。

几个例子👉[维基百科](https://api.duckduckgo.com/?skip_disambig=1&format=json&pretty=1&q=WikiPedia)， [Macbook Air](https://api.duckduckgo.com/?skip_disambig=1&format=json&pretty=1&q=MacBook%20Air) ， [Twilio](https://api.duckduckgo.com/?skip_disambig=1&format=json&pretty=1&q=Twilio)

我花了一些时间创建了一个不错的解析器，它通常从这个 API 返回信息。试着将这段代码粘贴到你的 [repl.it](http://repl.it) 项目中，你的[控制台](https://dsh.re/f7477c)中应该有关于 Trump 的内容😛

```
var base = 'https://api.duckduckgo.com/?skip_disambig=1&format=json&pretty=1&q=';
var query = 'Donald Trump';

request(base + query, function (error, response, body) {
    body = JSON.parse(body)    
    if(body["Abstract"] == ""){
        body["Abstract"]= body["RelatedTopics"][0]["Text"]
      }   
    var msg = body["Heading"]+"\n\n"+body["Abstract"];
  console.log(msg)
  }); 
```

很直接，对吧？😄

## 🛠️把这一切联系在一起

要制作我们实际的机器人，我们需要做的就是从我们的请求中获取查询——我们可以用`req.body.Body`来获取——并使用`twmil`来发送我们在`msg`
中收集的数据

```
app.post('/incoming', (req, res) => {
    const twiml = new MessagingResponse();
    var base = 'https://api.duckduckgo.com/?skip_disambig=1&format=json&pretty=1&q=';
    var query = req.body.Body;

    request(base + query, function (error, response, body) {
        body = JSON.parse(body)  

        if(body["Abstract"] == ""){
            body["Abstract"]= body["RelatedTopics"][0]["Text"]
          }   

        var msg = twiml.message(body["Heading"]+"\n\n"+body["Abstract"]);
            res.writeHead(200, {'Content-Type': 'text/xml'});
          res.end(twiml.toString());
      });

}); 
```

你现在有了一个全功能的 WhatsApp 机器人！发送任何你想知道的关于你的机器人的信息🤖你应该会看到它反应超快💬 ⚡

添加欢迎消息和一点格式化非常简单，看看最终的 [repl](https://repl.it/@jajoosam/wikibot) 就知道我是怎么做的了👨‍💻

[https://repl.it/@jajoosam/wikibot?lite=true](https://repl.it/@jajoosam/wikibot?lite=true)

## 🔗共享机器人

要让其他人使用这个机器人，他们需要先加入你的沙箱，然后像你之前做的那样发送一条消息👉`join <two-words>`

你也可以用这个文本创建链接——例如，这个链接可以让你加入我的机器人👇

```
https://wa.me/14155238886?text=join ultramarine-tapir 
```

`14155238886`是我的 bot 的编号，而`ultramarine-tapir`是沙盒短语。

## ⚡下一步是什么？

现在你已经知道如何在 WhatsApp 上构建一个机器人，试着给自己发送通知，并构建更多有用的工具！Twilio 也有大量的其他媒介来传递信息！

我的维基机器人的所有代码都在 [Github](https://github.com/jajoosam/wikibot) ⬇️
上

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[wiki bot](https://github.com/jajoosam/wikibot)

### WhatsApp 的维基百科机器人

<article class="markdown-body entry-content container-lg" itemprop="text">

# WhatsApp WikiBot

由 Twilio WhatsApp API 和 DuckDuckGo 即时搜索 API 提供支持<g-emoji class="g-emoji" alias="robot" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f916.png">🤖</g-emoji>

不

</article>

[View on GitHub](https://github.com/jajoosam/wikibot)

我是一个 15 岁的制造商👨‍💻想做更多酷的东西，想了解我的进展，请注册[我的时事通讯📧](https://buttondown.email/jajoosam)