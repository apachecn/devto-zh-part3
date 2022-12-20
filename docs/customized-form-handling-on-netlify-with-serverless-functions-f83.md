# 具有无服务器功能的网络定制表单处理

> 原文：<https://dev.to/raymondcamden/customized-form-handling-on-netlify-with-serverless-functions-f83>

几天前，我在 Netlify 上写了一篇关于无服务器功能的文章([“向您的 Netlify 静态站点添加无服务器功能”](https://dev.to/raymondcamden/adding-serverless-functions-to-your-netlify-static-site-41om))，今天我想看一下该功能的一个特殊方面——将功能与 [Netlify 事件](https://www.netlify.com/docs/functions/#event-triggered-functions)联系起来。

根据文档，您可以为以下 Netlify 事件编写定制的逻辑:

*   当部署开始构建、构建成功、构建失败、锁定或解锁时
*   当激活、停用或修改分割测试时
*   当用户注册或试图登录时
*   当然，当提交一个网络控制的表单时

所以要明确的是，Netlify 不关心你网站上的表单，除非你明确告诉它。这在[表单文档](https://www.netlify.com/docs/form-handling/)中有所涉及，但是基本上，你可以在你的表单标签中添加`netlify`或`data-netlify="true"`来通知 Netlify 你希望他们处理提交。开箱即用，您可以获得垃圾邮件保护、验证码和提交重定向等功能，还可以通过 Zapier 与第三方工具集成。你应该先试验一下，看看你是否需要通过无服务器功能进行定制，因为你的需求可能已经得到满足。

假设您非常确定您*确实*想要编写一些定制的逻辑，让我们来看看这是如何完成的。首先，这里有一个我为测试构建的简单表单:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    Contact Form
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>

    <form action="/thankyou.html" method="post" name="Contact Form" data-netlify="true">
    <p>
        <label for="name">Name</label>  
        <input id="name" name="name" value="Raymond Testing">
    </p>
    <p>
        <label for="email">Email</label>
        <input type="email" id="email" name="email" value="raymondcamden@gmail.com">
    </p>
    <p>
        <label for="comments">Comments</label><br/>
        <textarea id="comments" name="comments">Default value</textarea>
    </p>
    <p>
        <input type="submit" value="Send Comments">
    </p>
    </form>

</body>
</html> 
```

如果你愿意，你可以在 https://codabreaker.netlify.com/contact.html 看这个。我只提供了默认值，以便让我的测试更容易一些。这与网络生活支持或类似的东西没有任何关系。

好——所以你需要做的第一件事是创建一个名为`submission-created.js`的函数。每个 Netlify 站点和事件只能有一个处理程序，但是当您的函数被传递有关事件的信息时，您肯定可以添加对多个源的支持。从我看到的受支持的事件列表来看，表单可能是您唯一关心的地方。

像其他 Netlify 无服务器函数一样，您的基本函数签名如下:

```
exports.handler = (event, context, callback) => {
    //logic
}; 
```

虽然可以跳过`callback`的论证。在我的测试中，无论有无错误，调用回调对表单提交或其他任何事情都没有影响。

访问表单数据可以通过`event.body`来完成，它是一个 JSON 字符串，在那里您可以访问`payload`值。所以比如:

```
let payload = JSON.parse(event.body).payload; 
```

有效载荷是什么样的？这里有一个例子:

```
{
  "number": 24,
  "title": "Raymond Testing",
  "email": "raymondcamden@gmail.com",
  "name": "Raymond Testing",
  "first_name": "Raymond",
  "last_name": "Testing",
  "company": null,
  "summary": "<strong>Raymond Testing</strong> Default value",
  "body": "Default value",
  "data": {
    "name": "Raymond Testing",
    "email": "raymondcamden@gmail.com",
    "comments": "Default value",
    "ip": "76.72.11.11"
  },
  "created_at": "2019-01-15T22:00:51.691Z",
  "human_fields": {
    "Name": "Raymond Testing",
    "Email": "raymondcamden@gmail.com",
    "Comments": "Default value"
  },
  "ordered_human_fields": [
    { "title": "Name", "name": "name", "value": "Raymond Testing" },
    { "title": "Email", "name": "email", "value": "raymondcamden@gmail.com" },
    { "title": "Comments", "name": "comments", "value": "Default value" }
  ],
  "id": "5c3e5813f203baba9782ba13",
  "form_id": "5c3a051bdbfb660008114ddb",
  "site_url": "http://codabreaker.netlify.com",
  "form_name": "Contact Form"
} 
```

是的，这是一大堆数据。你可以看到这里发生了一些有趣的事情。首先，如果您只关心表单数据，那么您可以在`data`块中找到它。请注意，自动添加了一个`ip`值。

其次，Netlify 似乎试图对表单进行一些基本的解析。请注意，它是如何通过简单地拆分我的输入来获取名和姓的。它记下了电子邮件地址。它还提供了表单字段的“人类”版本，我猜它可能也会进行基本的解析。我把`email`改名为`email_address`，Netlify 仍然称人形为`email`。我想如果你有很大的、丑陋的、命名不好的表单，这可能是有用的。

最后，注意它识别表单的名称，站点，并且这是第 24 次提交。所有你可以用在逻辑中的东西。

下面是一个完整的例子，它同时使用了 SendGrid 和我过去为 OpenWhisk 编写的代码:

```
const SG_KEY = process.env.SENDGRID;

const helper = require('sendgrid').mail;

exports.handler = (event, context, callback) => {
    console.log('submission created error testing');

    let payload = JSON.parse(event.body).payload;

    // note - no validation - booooo
    let from_email = new helper.Email(payload.data.email);
    let to_email = new helper.Email('raymondcamden@gmail.com');
    let subject = 'Contact Form Submission';

    let date = new Date();
    let content = `
Form Submitted at ${date}
--------------------------------
`;

    for(let key in payload.data) {
        content += `
${key}: ${payload.data[key]}
`;
    }

    let mailContent = new helper.Content('text/plain', content);
    let mail = new helper.Mail(from_email, subject, to_email, mailContent);
    let sg = require('sendgrid')(SG_KEY);

    let request = sg.emptyRequest({
        method: 'POST',
        path: '/v3/mail/send',
        body: mail.toJSON()
    });

    sg.API(request, function(error, response) {
        if(error) {
            console.log(error.response.body);
        }
    });

}; 
```

注意，我是基于表单提交动态构建内容的，这对于 Netlify 和多个表单来说都很好，但是您也可以在这里硬编码一组键值对。

基本就是这样。我有一个关于如何更进一步的有趣想法，但是在我尝试之前，我正在等待 IBM 解锁我的 dang 开发者帐户。如果你有任何问题，请在下面留言告诉我！