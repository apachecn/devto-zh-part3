# 通过 Netlify 无服务器函数使用 MailChimp API

> 原文：<https://dev.to/raymondcamden/using-the-mailchimp-api-with-netlify-serverless-functions-45bk>

我第一次写 Netlify 的新功能是在今年一月([“向 Netlify 静态站点添加无服务器功能:](https://dev.to/raymondcamden/adding-serverless-functions-to-your-netlify-static-site-41om))。从那时起，Netlify 对他们的平台进行了多次更新，其中最重要的(imo)是 [Netlify Dev](https://dev.to/scottw/netlify-dev-3je3-temp-slug-5267792) 。我可能有点夸张，但 Netlify Dev 对我来说绝对是他们平台的改变者。我会解释如何，但首先让我开始描述我想要建立什么。

在那篇早期的博客文章中，我描述了我如何使用一个无服务器函数来获取我和我的好友 Brian 一起运营的 [CodaBreaker](https://codabreaker.rocks) 时事通讯的问题列表。实际上，我最终删除了那个功能，并使用了一个构建脚本，但是我能够重用 99%的代码，所以这仍然是一次很好的学习经历。

我想添加一个新的无服务器功能，将处理添加订阅时事通讯，并保持他们在网站上。MailChimp 的注册表格还不错，但是如果我可以自己完成，为什么不呢？MailChimp 的 API 很容易支持这一点，不仅如此，它还支持添加电子邮件地址，而不关心它们是否已经存在。你只需要向`https://us6.api.mailchimp.com/3.0/lists/LISTID/members/`发送一个`PUT`请求，其中`LISTID`是你的列表的 ID。

我在创建第一个函数时遇到的挫折之一是构建过程。我会写我的代码，提交到 GitHub，迅速要求 Netlify 重建(它会自动，但我不耐烦)，测试，诅咒，并重复这个过程。

Netlify Dev 改变了这一切。它允许您在本地机器上完全运行 Netlify 平台。简单来说，它允许您做一些事情，比如在本地测试重定向特性。我大量使用它，因为我有大量的内容，并多次迁移我的博客。从更复杂的角度来看，它使测试功能变得容易得多。

我开始使用 CLI 来搭建函数:

```
netlify functions:create 
```

这提示你从 100 个左右的函数中选择一个(好吧，不是 100 个，这是一个很大的数目，我想他们可能想精简一下)，但我只是选择了一个简单的 hello-world 模板。一旦完成，我开始编码，该死的，它只是简单的工作。如果你在推特上关注我，你会知道我遇到了一些麻烦，但都是我的错。我遇到的唯一无法纠正的问题是，我在 Netlify dashboard 中为站点设置的环境变量没有传递到代码中。(你可以在我的[论坛帖子](https://community.netlify.com/t/should-netlify-dev-functions-have-access-to-env-variables/1171)追踪这个问题)。我快速编写了以下代码。它不是最好的代码，但它是我的，我喜欢它。

```
const axios = require('axios');
var crypto = require('crypto');

const apiRoot = 'https://us6.api.mailchimp.com/3.0/lists/d00ad8719a/members/';

exports.handler = async (event, context) => {
  try {
    const email = event.queryStringParameters.email;
    if(!email) {
      return { 
        statusCode: 500, 
        body: 'email query paramter required' 
      };
    }

    // https://gist.github.com/kitek/1579117
    let emailhash = crypto.createHash('md5').update(email).digest("hex");

    return axios({
      method: 'put',
      url: apiRoot + emailhash,
      data:{
        email_address:email,
        status:'subscribed'
      },
      auth: {
        'username': 'anythingreally',
        'password': process.env.MC_API
      }
    }).then(res => {
      return {
        statusCode:200, 
        body: JSON.stringify(res.data)
      }
    })
    .catch(err => {
      console.log('returning from here', err.response.data.detail);
      return { statusCode: 500, body: JSON.stringify(err.response.data) };
    });

  } catch (err) {
    return { statusCode: 500, body: err.toString() };
  }

}; 
```

然后我写了一些简单的 Vue 代码来攻击我的函数(终点在`/.netlify/functions/newsletter-signup`)就这样，你现在可以在网站上看到:[https://codabreaker.rocks/](https://codabreaker.rocks/)

所以当我开始写这篇博文时，我想象它会更详细一点。老实说，刚刚成功了。很简单。(还是那句话，忽略那些主要是我自己的错的问题。)我真的很震惊，这个平台运行得如此之好！

*un splash 上由 [Charles PH](https://unsplash.com/photos/D-vDQMTfAAU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的标题照片*