# 免费托管静态网站

> 原文：<https://dev.to/pranavpandey/host-static-websites-for-free-1in3>

[![Header](img/cfea240a6af7abbb4005456e9fc3cce0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wfhSZJLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/85rl3w77hejgk7t2qxrz.png)

拥有自己的网站可能有利于获得更好的机会，或者让我们在这个过度拥挤的市场中脱颖而出，但很少有人愿意花钱创建一个网站并保持运营。如果我说**没有必要支付额外的钱**来完全控制你的个人网站或投资组合，你会怎么说？请阅读以下内容，了解更多信息以及如何实现这一目标。

## 先决条件

*   托管你的网站。你可以在这里注册一个 GitHub 账户[。](https://github.com)
*   在 GitHub 上推送你的网站。你可以根据你的平台(Linux，Mac 或者 Windows)从[这里](https://git-scm.com/download)安装 git。
*   建立或编辑网站的好方法。你可以根据你的平台下载 [VS 代码](https://code.visualstudio.com)、 [Atom](https://atom.io) 或者 [Notepad++](https://notepad-plus-plus.org) 。
*   我们将要主办的一次会议。这是可选的，您可以在以后开发或更新网站。

### 自定义域

您想要连接到网站的一个`domain`。这一步是可选的，只有当您想使用自己的个性化域时才需要。你可以在 [GoDaddy](https://godaddy.com) 、 [Namecheap](https://www.namecheap.com) 或[其他网站](https://www.google.com/#q=buy+domain+name)上购买域名。请不要购买他们的托管服务，因为我们将在 GitHub 上免费托管网站。

## 背景

上面我已经讨论过了`git`，它是一个版本控制系统，用来维护代码或者源代码。在这里，我们将使用它的一些命令，但它是一个非常有用和强大的工具，使每个软件行业的开发人员的生活更容易。如果你想了解更多关于 git 的知识，你可以在 [Udacity](https://in.udacity.com/course/how-to-use-git-and-github--ud775) 上一门写得非常好的免费课程。

在这里，我写了`static website`，到底是什么意思？静态网站是简单的`HTML`页面，不能执行服务器端操作。所以，你不能用这种方法托管你的`PHP blog, Node.js or web app`，你可以用 [Jekyll](https://jekyllrb.com) 写博客，或者用这个[生成器](https://wordpress.org/plugins/simply-static)把你的 WordPress 博客转换成一个静态网站。

所以，有一些限制，但我认为我们可以接受，因为我们仍然可以做足够多的静态网站的工作。还是不确定？请看[我的个人网站](https://pranavpandey.com)，这是一个完全用[聚合物](https://pranavpandey.com)搭建的静态网站，可以用这种技术托管。

## 准备网站

首先，我们需要`initialize the website`构建一个可以放在 GitHub 账户上的结构。为此，您可以使用任何根目录中有一个`index.html`页面的静态网站。

如果你还没有创建网站，只需按照下面的步骤创建一个测试网站，你可以在以后用实际的网站更新它。

*   创建一个新的文件夹或目录，比如说`website`。
*   在这个文件夹中创建一个`index.html`文件。
*   将下面的代码放入其中并保存。

```
 <head>
      My website
    </head>
    <body>
      <h1>My website, Coming Soon!</h1>
    </body> 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个知识库

我们已经成功地创建了我们的网站，现在我们需要在 GitHub 上`place or push`这个名为`source code`的代码集合。为此，我们首先需要用我们的注册账户`create a repository or repo`。

*   使用您的 [GitHub 帐户](https://github.com/login)登录。
*   创建一个名为`username.github.io`的[新存储库](https://github.com/new)。这里，`username`是你的 GitHub 账号用户名。
*   现在，我们已经成功地创建了一个回购，它只是一个服务器上的地方，将存储我们的网站，为全球服务。

## 推送网站

现在，我们将在 GitHub 上推送我们的网站。为此，我们首先需要`commit the code`局部。请按照以下步骤进行首次提交。

*   在网站文件夹中打开终端或命令提示符。
*   通过运行以下命令，添加临时区域中的所有文件。

```
 git add -A 
```

Enter fullscreen mode Exit fullscreen mode

*   现在，键入提交消息并提交。

```
 git commit -m "Initial commit" 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您是第一次运行 git，那么您需要`setup your git username and email`来进行第一次提交。这可以通过使用下面的命令来完成。

```
 // Configure name
    git config --global user.name "Your Name"

    // Configure email
    git config --global user.email "email@example.com" 
```

Enter fullscreen mode Exit fullscreen mode

我们已经成功提交了我们的网站代码。现在，我们将添加 origin，这样我们就可以将这段代码推送到我们的 GitHub repo 中。要添加来源，请转到您的 GitHub repo 页面并点击`Clone or download`按钮。它会显示一个类似于`https://github.com/username/username.github.io.git`的 url。复制这个 url 并在你的网站目录中运行下面的命令来添加原点。

```
 // Add origin
    git remote add origin https://github.com/username/username.github.io.git 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经添加了源代码，并准备将代码推送到服务器上。在原点的主分支中运行下面的命令到`push the code`。

```
 git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

它会将我们的本地代码推送到 GitHub repo，您可以通过访问浏览器中的 repo 页面来检查。它将显示您提交的所有文件。

## 见其运行

现在，我们完成了，您可以通过以下链接检查您的网站运行情况:

`https://username.github.io`

你喜欢吃吗?免费让一个网站活起来是最简单快捷的方法！现在，一个问题出现了，如果我想使用我自己的名为`custom domain`的域呢？答案很简单，用 GitHub 也可以，而且你不需要额外付费。听起来很棒？让我们试着整合你的自定义域，给它一个更个性化的感觉。

## 连接自定义域

要将我们的网站与自定义域名连接，我们需要修改我们的`website files and DNS configurations`。

*   首先，转到你的网站文件夹，创建一个新文件`CNAME`。请确保不要给它任何扩展。
*   现在，编辑这个文件，粘贴你的域名并保存。

`yourdomain.xyz`

*   再次提交代码并将其推送到 GitHub。

```
 git add -A
    git commit -m "Add CNAME"
    git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经修改了我们的网站，以便它可以指向我们的自定义域。同样，我们需要修改 DNS 配置，以便它可以指向 GitHub 服务器。为此我们需要在 DNS 配置中`add A records and CNAME`。

*   用你的域名注册账户(GoDaddy，NameCheap 等)登录。).
*   选择您的域并转到 DNS 管理或配置。
*   删除任何先前的 A 记录，并添加这两个 A 记录。你可以在这里阅读更多关于这个[的内容。](https://help.github.com/en/articles/setting-up-an-apex-domain)

```
 // 1st A record
    type: A 
    name: @ 
    IPv4: 185.199.108.153
    Automatic TTL

    // 2nd A record
    type: A 
    name: @ 
    IPv4: 185.199.109.153
    Automatic TTL

    // 3rd A record
    type: A 
    name: @ 
    IPv4: 185.199.110.153
    Automatic TTL

    // 4th A record
    type: A 
    name: @ 
    IPv4: 185.199.111.153
    Automatic TTL 
```

Enter fullscreen mode Exit fullscreen mode

*   保存并完成。
*   这里，我们只是通过使用 GitHub 服务器的 IP 地址将我们的域指向它们。

现在，在您的自定义域中检查网站。它应该工作或等待 2-3 分钟。

## 结论

太好了！我们已经成功地在 GitHub 上托管了我们的网站，没有任何额外的费用。请记住，这不是唯一的方法，还有其他几种可能性，比如我稍后将讨论的托管的 [Firebase。我们也可以通过使用](https://firebase.google.com/docs/hosting) [Cloudflare](https://www.cloudflare.com) 免费 SSL 来部分保护我们的网站。请在评论区提出问题或分享您的反馈，以使它变得更好。

> 这不是结束，继续探索，找出更好的可能性，并保持关注，以了解更多关于这种战术的网页！