# 在不到半小时的时间内从 Typeform 创建 Salesforce 销售线索

> 原文：<https://dev.to/typeform/create-a-salesforce-a-lead-from-typeform-in-less-than-half-an-hour-lmg>

在 Typeform，我们喜欢用我们的产品来运行东西。正如你可能已经在[的上一篇文章](https://www.typeform.com/blog/inside-story/21-ways-typeform-uses-typeforms/)中读到的，我们使用类型表来评价我们每天的午餐，收集关于即将推出的功能的反馈，甚至运行我们的内部 FIFA 竞赛。使用 Typeform 的方法有很多:)

我们也是 Salesforce 的大量用户:我们使用它来保持我们的合作伙伴计划有条不紊。有兴趣与 Typeform 集成的公司通过这个 partner  联系我们，但是之后什么也没有发生。如果我们认为一个销售线索可以产生有趣的对话，我们必须在 Salesforce 中手动创建销售线索*。*

 *我可以听到开发人员在说，“应该有更好的方法！”🤔

你说得对！通过使用引人入胜的 Typeform UI 来收集数据，以及使用强大的 Salesforce 漏斗将数据组织成销售线索，我们应该能够同时享受这两种功能。

这就是我要在这里解释的。我将我们的 typeform 连接到我们的 Salesforce 组织，节省了我们团队的宝贵时间。

# 先决条件🛒

要学习本教程，您需要:

*   一个 [Salesforce](https://www.salesforce.com/) 组织
*   一个打字账户- [免费创建一个](https://www.typeform.com/)
*   半小时的空闲时间

系好安全带，我们开始吧！🛫

# 原则📖

这个黑客依赖于 Typeform webhooks。您可以将一个或多个 webhooks 附加到一个类型表单上，这样当提交新的响应时，您会在您选择的 URL 上得到通知。

Webhooks 是实时发生的，所以它们允许你快速反应并保持一切同步。

在销售队伍方面，我们将依靠他们的[在线销售线索](https://www.salesforce.com/products/guide/lead-gen/web-to-lead/)解决方案。这是一种从 web 表单生成销售线索的快捷方式。

为了从 Typeform 接收 webhook 详细信息并将它们发送到 Salesforce，我们使用了 [Glitch](http://glitch.com) 。 [Glitch](http://glitch.com) 既是在线 IDE 又是服务器。该应用程序是自动部署的，所以没有必要担心服务器。

你也可以使用其他无服务器解决方案，如 [AWS Lambda](https://aws.amazon.com/lambda) 、 [Google Cloud functions](https://cloud.google.com/functions/) 、 [Azure](https://azure.microsoft.com/en-us/services/functions) 和 [Stdlib](https://stdlib.com) 。你的选择！😉

# 实施解决方案👩‍💻

## 准备字体

我们的类型包含了我们想问的所有问题。每个问题都由一个`id`和一个`ref`属性标识。两者都是唯一的并且是自动生成的，但是`id`值不能被改变。[表单定义示例](https://gist.github.com/picsoung/1b2665b6083876c949bdf6fe09d1ed7d)。

<figure>

[![details of field definition](img/2cc9a9cc5fc60bd4d60dae9bd5e62b89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n_GcuIzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.evernote.com/l/ACVug-zrn3ZNGJd2rtG7SxpwUtYs8_C-RXYB/image.png)

<figcaption>details of field definition</figcaption>

</figure>

我们*可以*更新`ref`值，使代码更具可读性。我将使用[类型表单编辑块引用应用](https://tf-edit-ref.glitch.me)来修改我们的类型表单中字段的`ref`属性。

<figure>

[![screenshot of edit ref app](img/cd9fde599c2a6ea611b936c23a2a88c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ckykOzEb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.evernote.com/l/ACVnWFu2hplJMpEK3nRSwVGc0jPT0YDDZiIB/image.png)

<figcaption>screenshot of edit ref app</figcaption>

</figure>

现在问题有了像`first_name`、`last_name`这样的`ref`值，而不是随机字符。👍

<figure>

[![After edit result](img/18b7fb4aedc96cba3c9446edc2c1e74a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GFOgCoVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.evernote.com/l/ACUecZQAfoNOQrgagEGrSU5epgSh9qRG4BYB/image.png)

<figcaption>Looks better, no? 😉</figcaption>

</figure>

## 获取您的 Salesforce 组织 ID 🗄️

要将销售线索发送到我们的 Salesforce 组织，我需要通过将我们的`oid`(组织 ID)作为参数传递来标识它。

您可以在您的 Salesforce 仪表板中找到`OID`。点击⚙️图标，你会在`Company Information`部分的*设置*下找到它。

<figure>

[![sc](img/d7f6f9b1faf33afa79df663ca122cd3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YU94CP2L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.evernote.com/l/ACUtchZh8oZF7pDNQ3Oa3guTw9_HdNisZRYB/image.png)

<figcaption>Where to find your Salesforce OID</figcaption>

</figure>

将`OID`放在手边！你以后会需要它的。😉

### 咱们码吧🎉

我知道你想尽快到这一步！所以让我们把手弄脏吧。😈

如前所述，我们的函数将从 Typeform webhook 有效负载中提取详细信息，然后调用 Salesforce API 来创建销售线索。

```
app.post('/hook', function(request, response) {
  response.sendStatus(200) // let's answer back quickly to typeform 👌
  let { form_response } = request.body
  let { answers } = form_response

  console.log('received from Typeform webhook', form_response)

  // build the lead object
  let lead_data = {
    first_name: answers.find(a => a.field.ref === 'first_name').text,
    last_name: answers.find(a => a.field.ref === 'last_name').text,
    email: answers.find(a => a.field.ref === 'email').email,
    company: answers.find(a => a.field.ref === 'company').text,
    oid: process.env.OID,
    debug: request.query.debug===1 ? 1 : 0 // salesforce debug mode
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

正如您在这个片段中看到的，您将创建一个`/hook`路由，除了一个`POST`请求。然后，您将处理传入的请求并提取相关数据来构建`lead_data`对象。

出于安全考虑，不要将`OID`值直接存储在代码中。相反，将其存储为环境变量。

我还添加了一个调试参数，这样您就可以在不损害您的实际组织的情况下进行测试。

剩下要做的唯一一件事就是调用 Salesforce 在线潜在客户 API 并创建一个潜在客户。

这是要添加的缺失部分:

```
axios({
    method: 'POST',
    url: 'https://webto.salesforce.com/servlet/servlet.WebToLead?encoding=UTF-8',
    data: qs.stringify(lead_data),
    config: { headers: {'Content-Type': 'application/x-www-form-urlencoded' }}
    })
    .then(function (response) {
        console.log('res',response.status, response.data);
    })
    .catch(function (err) {
        console.log('err',err);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

这就是你把事情联系在一起所需要的。🎊

# 现场试用

要快速尝试这种集成，请遵循以下步骤:

1.  [重新混合 Glitch 上的项目](https://glitch.com/~tf-webtolead)
2.  将您自己的`OID`添加到`.env`文件中
3.  在`server.js`的第 21-28 行删除、添加或修改 Typeform webhook 有效负载的数据提取
4.  点击`Show`以查看 Glitch 上托管的项目的 URL(并为下一步保留它)
5.  在类型表单中，选择您的销售线索生成类型表单，并在`Connect > Webhooks`下添加您的 Glitch 应用程序 URL。URL 格式应为`https://{random_name}.glitch.me/hook`
6.  通过填写并提交您的销售线索生成类型表进行测试-提交后，您应该会在您的 Salesforce 组织中看到一个新的销售线索
7.  庆祝一下！🎉

<figure>

[![new lead in Salesforce](img/471e3996bad5e5f2e1c32b6698803e74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YqTimXxZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.evernote.com/l/ACXSHV_zi6VJp4bDmEO8MFpF6iSZjVyUTrQB/image.png)

<figcaption>Succesfully created a lead in Salesforce</figcaption>

</figure>

[https://glitch.com/embed/#!/embed/tf-webtolead?path=index.html](https://glitch.com/embed/#!/embed/tf-webtolead?path=index.html)

# 超越🗺️

这是一个连接 Typeform 和 Salesforce 的简单方法。为了使它更完整，您可以将更多字段从您的 lead 对象映射到您的 typeform 问题。它甚至适用于自定义 Salesforce 字段。

如果您对将 Typeform 与其他服务连接起来感到好奇，请查看我们的[开发者文档](https://developer.typeform.com)并构建您自己的解决方案！😉

要了解此类项目的最新进展，请订阅我们的[开发者简讯](https://developerplatform.typeform.com/to/DlIzNU)。*