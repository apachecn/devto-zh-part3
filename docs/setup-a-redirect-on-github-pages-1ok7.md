# 在 Github 页面上设置重定向

> 原文：<https://dev.to/steveblue/setup-a-redirect-on-github-pages-1ok7>

有几个静态站点生成器，甚至还有一个名为 gh-pages 的 cli，使您能够轻松发布 Github pages 站点。有时实现一个定制的 html 页面就像实现一个重定向一样简单，但是当你不总是部署到 Github 页面时，很容易忘记如何实现。Github 页面有很多资源，但我找不到任何教程来明确定义从头开始并进行重定向所需的几个步骤，所以我们开始吧！

下面是将 index.html 部署到 Github 页面上的用户帐户页面的步骤。这个 index.html 包括一个重定向，从位于[https://username . github . io](https://username.github.io)的用户账户页面重定向到另一个站点([https://example.com/](https://example.com/))。

在下面的例子中，用你的 Github 用户名替换“username ”,用你希望用户重定向到的 url 替换[https://example.com/](https://example.com/)。

## 设置新的存储库

要创建一个用户账号页面，在 Github 上新建一个库，设置名称为 username.github.io，如果库为空也可以。

同样值得注意的是，由于这是一个用户帐户页面，Github pages 将从主分支上的根目录中提取内容，这与项目或组织页面相反，项目或组织页面可以配置为使用“docs”目录作为根目录，或者使用“gh-pages”分支中的根目录。

在您的本地计算机上，克隆存储库:

```
git clone https://github.com/username/username.github.io.git 
```

Enter fullscreen mode Exit fullscreen mode

将目录切换到您刚刚克隆的 git 存储库中。

```
cd username.github.io.git 
```

Enter fullscreen mode Exit fullscreen mode

## 样板文件

接下来创建所有样板文件。在根目录下创建两个新文件:_config.yml 和 index.html。

```
touch _config.yml
touch index.html 
```

Enter fullscreen mode Exit fullscreen mode

### index.html

该文件是您希望在 username.github.io 托管的页面的模板。在本例中，我们在 index.html 文件中设置了一个重定向。

以下是你为什么想在 Github 页面上设置重定向的一些原因。

*   username.github.io 将用户重定向到托管在域中的个人站点
*   项目文档托管在另一个 repo 中的 Github 页面上，您希望 username.github.io 重定向到那里
*   项目已移动，您希望将用户重定向到新地址

重定向是用一个`<meta>`标签完成的。通过将`http-equiv`属性设置为`refresh`，将`content`属性设置为`0; URL=https://example.com/`，我们将把用户重定向到“[https://example.com/](https://example.com/)”。

通过告诉搜索引擎这两个网站本质上是一样的，都有一个带有规范 url 的`<link>`标签，从而改善 SEO。

```
<!DOCTYPE html>
<meta charset="utf-8">
Redirecting to https://example.com/
<meta http-equiv="refresh" content="0; URL=https://example.com/">
<link rel="canonical" href="https://example.com/"> 
```

Enter fullscreen mode Exit fullscreen mode

### _config.yml

为了构建，我们仍然需要包括配置。该配置通常与 Github 页面的预建模板一起使用。这看起来很尴尬，因为我们定义了一个自定义的 index.html，但是这是一个需求。这是大多数教程遗漏的一步。

Edit _config.yml 并将主题设置为 jekyll-theme-cayman。

```
theme: jekyll-theme-cayman 
```

Enter fullscreen mode Exit fullscreen mode

主题也可以在 github.com 的存储库设置页面中设置。如果你选择在主分支设置主题，Github 会更新主分支。

提交这些更改并将提交推送到主分支。

```
git commit -a -m "initial files" 
```

Enter fullscreen mode Exit fullscreen mode

## 发布

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

第一次部署可能需要大约 10 分钟才能完成，但是我看到 Github 页面几乎可以立即更新。如果一切顺利，你的重定向应该在'[https://username.github.io/](https://username.github.io/)'直播。

在接下来的几周里，请关注我以获取更多提示！