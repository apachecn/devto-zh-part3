# 让 Create React App 在 GitHub 用户项目页面上使用自定义域

> 原文：<https://dev.to/brettcnelson/getting-create-react-app-to-work-with-custom-domains-on-github-user-project-pages-2hp2>

我最近将一个用 [Create React app](https://github.com/facebook/create-react-app) 制作的 App 部署到了 [GitHub Pages](https://pages.github.com/) 。我遇到了一个让构建资产得到正确服务的问题，并认为这篇快速的帖子可能会帮助遇到相同问题的人。

> tldr:将`package.json`中的`homepage`从`[username].github.io/[project]`更改为您的定制域，以便构建资产在根而不是在`/[project]`被服务

这篇文章假设你在`[username].github.io/[project]`有一个工作用户项目页面。如果你的应用在默认用户页面- `[username].github.io` -就不会有这个问题。

[GitHub 帮助文档](https://help.github.com/en/articles/using-a-custom-domain-with-github-pages)非常全面，你应该不会有太大的问题配置你的 DNS 记录或添加一个自定义域名到你的回购设置。我将自定义子域`www.[site].[domain]`添加到我的项目报告的设置中。然后，我通过添加 4 个指向 GitHub IP 地址的 [A 记录](https://help.github.com/en/articles/setting-up-an-apex-domain)和一个指向`[username].github.io`的`www`子域的 [CNAME 记录，建立了一个](https://help.github.com/en/articles/setting-up-a-www-subdomain) [apex 域和`www`子域](https://help.github.com/en/articles/setting-up-an-apex-domain-and-www-subdomain)。您可以不同地配置您的记录，但是只要您的(子)域指向您的项目页面，如果您无法获得您的资产，这篇文章的其余部分仍然是相关的。

> 注意:如果您使用 [gh-pages](https://www.npmjs.com/package/gh-pages) 包将您的构建文件夹推送到 repo 的 gh-pages 分支，它将覆盖您在设置页面上输入自定义域时添加到该分支的 CNAME 记录。我将`www.[site].[domain]`添加到主分支的`public`文件夹中的一个`CNAME`文件中。这样，Create React App 会将其添加到构建中。

我想这就是我要让我的域名正常工作所要做的一切。但是我去`www.[site].[domain]`的时候，页面加载不了。检查控制台显示我的`index.html`被服务了，所以我与 GitHub 和我的域名提供商的 DNS 记录是工作的。但是脚本标签指向了`/[project]/static`，而不仅仅是`/static`。类似地，页面在`/[project]`寻找我的图标，而不是根。为什么在添加我的自定义域名后，这个版本在`[username].github.io/[project]`被正确的服务了，只是中断了？

问题是 Create React App 创建了一个环境变量`PUBLIC_URL`，用来设置资产的路径。关键的是，它使用`package.json`中的`homepage`值来确定是否有子目录应该放在路径前面。当站点位于`[username].github.io/[project]`时，将`homepage`设置为那个 url 并不会导致问题，因为构建文件夹位于`/[project]`。例如，页面将在`/[project]/static`寻找资产，它们就在那里。

但是当站点移动到`www.[site].[domain]`时，构建文件夹变成位于根目录。页面仍然在寻找`/[project]/static`的资产，但是显然在构建文件夹中没有`/[project]`目录。

现在，将`package.json`中的`homepage`从`[username].github.io/[project]`更改为自定义域会导致页面在正确的位置查找资产，然后站点就会加载。

> 事实上，我认为您可以将`homepage`设置为任何不包含子目录的 url(假设您的构建在根目录下),并且它会工作——尽管出于其他原因，这显然不是一个好主意。

这是 GitHub 在子目录中为你的页面提供服务的另一个[怪癖](https://medium.com/@shushugah/2-customs-domains-for-2-github-pages-a8a9e77d3e57)。