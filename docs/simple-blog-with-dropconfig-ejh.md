# 使用 DropConfig 的简单博客

> 原文：<https://dev.to/dropconfig/simple-blog-with-dropconfig-ejh>

DropConfig 非常通用，通过我们添加的一些新功能，它变得非常容易用于一个简单的博客，无需后端！

我将使用[这个代码笔](https://codepen.io/powerc9000/pen/KLJYLL)进行演示，你可以在那里查看。

## 先决条件

要做到这一点，你需要一个 DropConfig 组织。你可以获得 90 天的免费试用。

在您创建您的组织后，我们将需要创建一个回购来保存我们所有的帖子。

[![](img/8a5ca63ecbd92337fba9d7c1818331b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k6zk92Us--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9mzi87dticj65o07spj2.png)

### 一个模板

我们想做一个帖子模板，我们可以很容易地复制新帖子。

确保点击`Advanced Setup`并检查`Create as Template`

[![](img/2243fa91a678a1283ade58ed5466bd2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P-cSh8YW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jdbjk2cr5d8unbobch2e.png)

然后，我们将使用我们可能喜欢的字段填充模板，并保存它。

[![](img/f424ecaa5190588326ca53258111eeb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mcw2o-dm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/plnc87sinwwrhqytehc8.png)

## 创建新帖子

现在我们有了一个模板，我们可以创建一个新帖子。

在同一个 repo 中创建新配置。您可以随意命名该文档。然后在`Advanced Setup`中，我们将再次选择`blog_template`作为模板。

[![](img/2b28c9246d4510539f6ea08e3d76c5de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8y5aba01--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxhqdncnqkgmadawc96f.png)

我们可以看到它复制了我们的模板字段。现在我们可以进入表单用户界面来更容易地编辑它。

[![](img/9a3a7d8aa3169934abd8fbf07dc90d39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--btE6f1Ce--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rd4wq32vrlrryomgr4ah.png)

您可能会注意到 body 字段不是 textarea。幸运的是，我们可以用`Manage Form Fields`按钮把它换成一个。然后点击 Body 字段上方的`change to textarea`。

[![](img/17511ca2502015848d87ef0901967d0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jpplJWsv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9tthy21eyxmn49qhkq8k.png)

填写帖子到你的内容！

## 获取所有帖子。

现在我们要列出回购中的所有文件。这将允许我们在我们的博客主页上展示它们。

我们可以在列表中看到我们帖子，但是模板没有显示出来！