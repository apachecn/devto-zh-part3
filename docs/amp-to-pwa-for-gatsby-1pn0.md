# 盖茨比的放大器到 PWA

> 原文：<https://dev.to/tomoyukikashiro/amp-to-pwa-for-gatsby-1pn0>

**这篇文章最初是在 [AMP to PWA 为盖茨比](https://blog.tomoyukikashiro.me/post/amp-to-pwa-for-gatsby)发表的。**

## 什么是 AMP to PWA

它也被称为[从你的 AMP 页面](https://www.ampproject.org/docs/integration/pwa-amp/amp-to-pwa)预载你的渐进式网络应用。

> 一个好的策略是使你的网站的入口点成为一个 AMP 页面，然后在幕后预热 PWA 并切换到它以继续前进:

更多详情请查看 [AMP 官方文章](https://www.ampproject.org/docs/integration/pwa-amp/amp-to-pwa)。

## 本文的目标

我将解释如何使用 Gatsby 为 blog 实现它。

## 目录

*   准备博客
*   更改文章路径
*   准备放大器生成
*   在非 AMP 页面的 AMP 中添加维修工人安装程序
*   在测试中添加放大器验证(可选)
*   建设

## 准备博客

```
$ npm install -g gatsby-cli
$ gatsby new my-blog-starter https://github.com/gatsbyjs/gatsby-starter-blog
$ cd my-blog-starter/ 
```

## 改变文章路径

默认情况下，博客文章位于根目录下(如`/hello-world`、`/hi-folks`)。我们要发布非 AMP 文章和 AMP 文章，所以让我们像这样分开目录。

*   非放大器:`/posts`
*   放大器:`/amp/posts`

首先，更改非 AMP 发布路径。

*   在 [gatsby-node.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c29e8be855a88a1a5b29879e0647d6cb3bd1be89#diff-dab0f592402461060a5ef23fcb717452) 更改帖子的构建目标

## ——在 [src/components/seo.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c29e8be855a88a1a5b29879e0647d6cb3bd1be89#diff-8f355b4311b3bd58787dcd954140e366) 的`canonical`等元标签中更改路径

在目录处更改链接路径

*   [src/pages/index.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c29e8be855a88a1a5b29879e0647d6cb3bd1be89#diff-5fb7300a15156cb7af405410a26e9364)
*   [src/templates/blog-post . js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c29e8be855a88a1a5b29879e0647d6cb3bd1be89#diff-7cb5d5854f562d9d4aa64433022ad9da)
    *   在 [gatsby-config.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c29e8be855a88a1a5b29879e0647d6cb3bd1be89#diff-0fbddf38e100e847d3a54e99e91f204b) 更改 RSS 中的路径

## 准备 AMP 生成

为了从 HTML 生成 AMP 页面，我使用了 [gatsby-plugin-html2amp](https://www.gatsbyjs.org/packages/gatsby-plugin-html2amp/?=amp) 。这个插件允许你使用普通 HTML 生成 AMP 页面，所以你所要做的就是安装它并添加配置！

```
$ npm install --save gatsby-plugin-html2amp 
```

并添加配置。

*   在 [gatsby-config.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/5126d9cb6f07b71e91478f4e0fdfeec5d4407bd9#diff-0fbddf38e100e847d3a54e99e91f204b) 添加插件配置
*   在 [src/components/seo.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/5126d9cb6f07b71e91478f4e0fdfeec5d4407bd9#diff-8f355b4311b3bd58787dcd954140e366) 添加`amphtml` meta 标签
*   在 [static/gaConfig.json](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/5126d9cb6f07b71e91478f4e0fdfeec5d4407bd9#diff-5226fffa574632e33a3d3c4d2391a8fd) 添加 AMP 的 Google analytics 设置

## 在 AMP 中为非 AMP 页面添加维修工人安装程序

这是这个战略最重要的关键点。一旦用户访问 AMP 页面，该网站将开始安装服务人员来缓存非 AMP 页面的资源。

安装后，用户点击链接到非 AMP 页面的链接，页面立即加载，因为大多数资源被缓存。[amp-install-service worker](https://www.ampproject.org/docs/reference/components/amp-install-serviceworker)就是为此准备的 AMP 组件。

幸运的是， [gatsby-plugin-html2amp](https://www.gatsbyjs.org/packages/gatsby-plugin-html2amp/?=amp) 已经为它提供了集成，所以您需要做的只是添加配置！！

*   在[static/amp-install-service worker . html](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c26c038e71162394118f0e4d4635c1c7d22da76d#diff-7959d8932d44e96d61e4af13cbba1c42)处创建服务工作者安装程序页面(新文件)
*   在 [gatsby-config.js](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/c26c038e71162394118f0e4d4635c1c7d22da76d#diff-0fbddf38e100e847d3a54e99e91f204b) 添加 gatsby-plugin-html2amp 配置

## 在测试中添加放大器验证

这是可选的。让我们添加测试来检查 AMP 页面的验证。

```
$ npm install --save-dev amphtml-validator 
```

*   在[package . JSON](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/pull/1/commits/db59726ff5b1326a1ad5d6a562a96007ccfaa908#diff-10bdf593d5a857c6c669e7974b210504)添加测试命令
*   通过以下命令运行测试

```
$ npm test 
```

## 既造

注意 gatsby-plugin-html2amp 只在构建过程中生成 amp，所以当您运行`npm run develop`时，您不能浏览 AMP 页面。当您检查非 AMP 和 AMP 页面时，运行以下命令。

```
$ npm run build
$ npm run serve 
```

## 样品

我在这里做了样本项目[。](https://github.com/tomoyukikashiro/gatsby-pwamp-blog-samples/tree/master/amp-to-pwa)