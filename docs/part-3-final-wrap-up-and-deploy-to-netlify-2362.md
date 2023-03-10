# [第 3 部分]最后总结并部署到 netlify

> 原文：<https://dev.to/hyper_yolo/part-3-final-wrap-up-and-deploy-to-netlify-2362>

[![hero-image](img/45c66ca9f6e55743b89e374fc9b057ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vhcj941o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d63hpoll0toxrq5kqfke.png)

在这个系列的最后一篇文章中，我们将把我们的 Vuepress 博客部署到 [Netlify](https://www.netlify.com/) 上。我已经通过添加更多的顺风类对模板进行了一些修饰，你可以[在 Github](https://github.com/amiechen/vuepress-blog) 上查看回购。

## 设置本地存储库和 Netlify 帐户

注册 Netlify(如果你还没有注册),并将其链接到你的 github/gitlab 帐户。

然后我们必须安装一个本地 Vuepress 作为依赖项。这很重要！由于整个教程都是用 1x-alpha 构建的，我们准备安装最新的。

```
npm install vuepress@next //mine is on 1.0.0-alpha.37 
```

Enter fullscreen mode Exit fullscreen mode

然后给你的 **package.json** 加一个`npm script`T3】

```
 "scripts": {
    "build": "vuepress build"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

我个人喜欢在把所有东西都推到 netlify 之前测试构建。在您的项目根目录中，运行构建命令:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

如果构建成功，我们可以安全地将所有东西部署到 netlify。

## 部署设置

在您的 netlify 仪表板上，点击右上角的 **New Site from Git** 按钮。选择您的 repo，您应该会看到一个部署设置屏幕。添加一些类似下面截图的配置:
[![netlify-deployment](img/6e18e8be9643a5e704739641640a6d94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bdazHYGs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w7wsgj578j3490tpd2tl.png)

基本上，我们告诉 Netlify 使用我们随 repo 一起安装的 alpha vuepress 版本来运行构建命令，然后输出到生成的**。dist** 文件夹。如果你像文档一样把所有东西都放在一个**文档**文件夹下，你的构建命令应该是`vuepress build dev`，目标文件夹应该是`docs/.vuepress/dist`。

点击部署按钮，你应该看到你的网站建设！我的部署在[这里](https://stupefied-mcclintock-f9c943.netlify.com/)。

就是这样！如果你有任何问题，请随时给我写信，:D，谢谢你的关注。

* * *

这是一个来自我的网站的交叉帖子。查看原文和更多内容！