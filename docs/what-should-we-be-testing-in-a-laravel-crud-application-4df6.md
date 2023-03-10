# 我们应该在一个[laravel] CRUD 应用程序中测试什么？

> 原文：<https://dev.to/djug/what-should-we-be-testing-in-a-laravel-crud-application-4df6>

这是我即将出版的电子书《拉韦尔测试 101》的节选。如果您还没有阅读前一章(此处免费提供:[向您的 Laravel CRUD 应用程序添加测试:从哪里开始？](https://youghourta.com/2018/11/27/laravel-testing-101-where-to-start/))，请先这样做再看这篇。

现在你明白了你应该从不同的角度来看测试，我们应该主要测试控制器，你可能会问，”...但是我到底应该测试什么呢？”

在这一章，我们将回答这个问题，并描述我们将在电子书的其余部分测试什么。

但是首先，让我们获得本书所基于的演示应用程序。

克隆以下回购

`https://github.com/djug/laraveltesting101`

并在完成自述文件中的所有步骤后运行该应用程序。

启动服务器/应用程序后，您应该会看到这个首页:

[![](img/fd2145ab85d5ae146909d996435b539d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1lR1nX2e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c46rkhgrre7g67w1afxl.png)

到目前为止，您可以在应用程序中执行以下操作:

*   点击“登录”或“注册”查看登录和注册页面。

*   点击“所有文章”,你会看到/articles，其中包含了由种子生成的所有文章。

[![](img/46b8a3c71436aa9c45357f6680eb369b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lOGULxbr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/crlsgmwtg1jniy1v1w9x.png)

*   点击“写一篇新文章”(`/new-article`)，你会被重定向到注册页面(因为添加一篇新文章需要注册)。

*   在“/articles”页面，您可以点击文章名称(例如`/articles/15`)或用户名(`/users/5`)来查看文章或其作者简介。

[![](img/1e5bd32f85b4f5e51862c741031ea6df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V26ClJDc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ibzri70hg6mb635nvyrx.png)

注册后，你将能够查看`/new-article`路线(当我们之前尝试不登录时，它被重定向到注册页面)

[![](img/b0a07e905ec6637789d986e18fff78e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rzMNegrJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x2xpbcvqbwsbvytgtd32.png)

保存您的文章后，您可以查看它，因为这是您自己的文章，您会在页面末尾看到“编辑文章”按钮(您不会在您不拥有的文章上看到它)。

[![](img/d3f9b1bd76c364144fecbb9217090c28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L1IqiXYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6pgyt99yi85bomdb69ns.png)

当点击这个按钮时，你得到文章的编辑表单，在这里你可以编辑和保存文章或者删除它。

[![](img/80bf4772532bde2482cc6fae115ca2d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mE05OPbm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3nqq4gbbld31s5xcs9eu.png)

当更新文章时，你会再次被重定向到该文章，并显示一条成功消息。

[![](img/480bfb360bee8629d96852460ad4432a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6PCR-yy_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kpvlfslqp41g46456m4k.png)

但是如果你删除它，你会被重定向到“所有文章”页面，并显示一条不同的成功消息。

如果你访问的文章不是你自己创作的，你不会看到“编辑”按钮，但是如果你试图直接通过 URL(例如:`articles/16/edit`)访问你不拥有的文章的编辑页面，你会得到 403 页面。

[![](img/bd2d2970a31f42ca83c753671882598d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7J_mMK61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9tsjywt1sgdp25yzk2xi.png)

让我们回到“写一篇新文章”页面，尝试添加一篇短标题和/或短正文(少于 10 个字符)的新文章。

您将得到一条错误消息。而剪辑的时候也会发生同样的事情。

[![](img/f9dd8d6ab7485fc31442dcaf78751ce1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eK9KpEJC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bs8hl1c0ribc2ynglyke.png)

因此，即使应用程序很小，我们也需要测试许多东西来确保应用程序按预期工作。

这是清单:

1.  客人可以访问并获得注册页面

2.  客人可以访问并获得登录页面

3.  客人在访问/文章时可以看到所有文章

4.  客人可以看到用户简档

5.  客人不能写新文章，而是被重定向到注册页面

6.  用户(即注册用户)可以写文章

7.  用户可以访问她自己文章的编辑页面

8.  用户可以保存对自己文章的修改，并在编辑成功消息后重定向到文章页面

9.  用户可以删除自己的文章，并通过成功消息重定向到所有文章页面

10.  用户不应该在她不拥有的文章上看到编辑按钮

11.  用户不能编辑她不拥有的文章

12.  用户不能删除她不拥有的文章

13.  创建文章时，文章不能有短标题或正文

14.  更新文章时，文章不能有短标题或正文

因此，如果我们设法自动化测试上面的列表，我们就可以确信，每次我们添加一个新的特性或者改变一个代码，应用程序都会像预期的那样工作。

在下一章，我们将开始编写与客人用户相关的功能测试。

如果你想继续下去，并得到任何关于这本书进展的通知(例如新的免费章节)，请在这里注册:[https://laraveltesting101.com/](https://laraveltesting101.com/)