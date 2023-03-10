# 使用 AWS SES 发送电子邮件

> 原文：<https://dev.to/dropconfig/sending-emails-with-aws-ses-1328>

所以在我的上一篇文章中，我提到了使用你的事件总线来发送邮件。不过，我浏览了一下实现。
我想写一篇关于如何用 SES 轻松发送电子邮件的后续文章。这是我们在 [DropConfig](https://dropconfig.com) 发送所有电子邮件的方式

## SES

AWS SES 代表简单电子邮件服务。名字还算准确。我也不打算详细介绍如何设置您的帐户。 [AWS 有一些不错的指南](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/setting-up-email.html)

不管怎样，让我们开始吧。

我们将我们的电子邮件发送分为由事件触发的两个部分。

1.  从给定事件中获取和封送电子邮件的数据。
2.  发送真正的电子邮件。

比如说。

我们可能会收到一个事件`USER_COMMENTED_ON_POST`我们做的第一件事是在我们的电子邮件触发器中查找一封电子邮件

```
const emailTriggers = {
    "USER_COMMENTED_ON_POST": {
        "to": "user.email",
        templateName: "user-commented"
    }
} 
```

我们的触发器有几个部分。我们有一个 JSON 路径，可以在事件数据中查找电子邮件的收件人。然后我们有了一个类似这样的电子邮件模板。

```
{  "Subject":  "A user commented on your post",  "Body":  "hello {{user.name}} a user commented on your post {{post.link}}"  } 
```

我们在电子邮件中使用小胡子作为模板。你也可以让你的邮件正文有 HTML 标签等等。

因此，我们在电子邮件触发列表中查找并整理电子邮件

```
const trigger = emailTriggers[event.type];
if(!trigger){
   //We don't have a trigger so we don't care
   return true
}
//Here we are fetching the template. Maybe we store these in their own DropConfig?
const template = await loadTemplateFromName(trigger.templateName);

//Using lodash here because it can lookup by a path string
const to = _.get(task.data.data, trigger.to);
if(to && template){
    const body = mustache.render(template.Body, task.data.data);
    const subject = mustache.render(template.Subject, task.data.data);

    const params = {
        Destination: {
            toAddresses: [to]
        },
        Source: "sputnik@dropconfig.com",
        Message: {
            Body: {
                Html: {
                    Data: body
                }
            },
            Subject: {
                Data: subject
            }
        }
    }
} 
```

接下来我们创建一个`send-email`事件并将参数放入其中。

```
server.createEvent("send-email", params); 
```

为什么我们不直接发邮件呢？

最大的原因是:想象你有许多不同的电子邮件发送基于一个单一的事件。如果一个用户对一篇文章发表了评论，你可能想给这篇文章的所有者发一封电子邮件。而且给其他评论者发送不同的电子邮件。现在，如果发送给所有者成功，但发送给评论者失败，我们就有一个问题。而不将电子邮件的发送分成两个不同的事件。我们将重新运行该事件，并一遍又一遍地通知我们发送成功的人(或我们重试的次数)。

如果我们把它分成两步，只有失败的邮件会被重试。

现在我们已经创建了`send-email`事件。实际发送电子邮件轻而易举。

```
 if(task.data.type === "send-email"){
    try {
      const params = task.data.data;
      const res = await ses.sendEmail(params).promise();
      return true;
    } catch(e){
      return false;
    }
  } 
```

我们不需要比这更多的逻辑！

## 把所有的东西放在一起。

```
//This is a task runner as explained in my previous post.
exports.handler = async (task, queue, sqs, server) => {
    const emailTriggers = {
        "USER_COMMENTED_ON_POST": {
        "to": "user.email",
        templateName: "user-commented"
        }
    }

    if(task.data.type === "send-email"){
        try {
          const params = task.data.data;
          const res = await ses.sendEmail(params).promise();
          return true;
        } catch(e){
          return false;
        }
      }

    const trigger = emailTriggers[event.type];
    if(!trigger){
       //We don't have a trigger so we don't care
       return true
    }
    //Here we are fetching the template. Maybe we store these in their own DropConfig?
    const template = await loadTemplateFromName(trigger.templateName);

    //Using lodash here because it can lookup by a path string
    const to = _.get(task.data.data, trigger.to);
    if(to && template){
        const body = mustache.render(template.Body, task.data.data);
        const subject = mustache.render(template.Subject, task.data.data);

        const params = {
        Destination: {
            toAddresses: [to]
        },
        Source: "sputnik@dropconfig.com",
        Message: {
            Body: {
            Html: {
                Data: body
            }
            },
            Subject: {
            Data: subject
            }
        }
        }
    }

} 
```

谢谢你读到这里。签出[https://dropconfig.com](https://dropconfig.com)为可怕的版本控制和托管您的配置文件。

如果你有任何问题让我知道。我也许能帮上忙！