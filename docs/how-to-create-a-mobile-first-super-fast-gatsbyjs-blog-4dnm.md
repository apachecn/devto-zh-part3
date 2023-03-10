# 如何创建一个移动第一的超快的 Gatsby.js 博客

> 原文：<https://dev.to/anamritraj/how-to-create-a-mobile-first-super-fast-gatsbyjs-blog-4dnm>

最近，我为自己创建了这个惊人的、超快的、移动优先的、超灵敏的网站，我对它的性能感到惊讶。我花了一些时间才把它做好，但这个过程并不困难，一旦我完成了，我想我会把它记录下来，这样其他人也能受益。

我的博客: [https://anamritraj.tech](https://anamritraj.tech)

## 我用了什么？

整个博客是一个服务器端渲染的静态网站，由 [Gatsby.js](https://www.gatsbyjs.org/) 提供支持。如果你过去几个月没有生活在岩石下，你一定听说过它。js 是一项了不起的技术，它让你用最少的代码创建超快的网站。

它利用 React.js、web pack 和现代 JavaScript 和 CSS 的力量来构建强大的 web 应用程序。作为后端，你可以使用任何东西，对我来说，我将使用简单的降价文件。Gatsby.js 会自动(神奇地读成*)将你的 markdown 文件转换成 HTML 并作为博客文章加载。还有大量的插件可以用来为你的网站增加额外的功能。例如，有一些插件可以将你的网站转换成一个渐进式的网络应用程序，而不需要你做任何事情。*

 *Gatsby.js 将生成静态资产，这些资产可以通过任何能够处理 HTML、CSS 和 JavaScript 的服务器提供服务。这意味着你可以免费使用 Github Pages 托管你的网站，*永远*。有其他*免费*的方式来托管一个 Gatsby.js 网站，但我会涵盖我为这个可怕的网站所使用的。

## 我为什么选择 Gatsby.js？

它简单、易于部署，而且*现在很热门*！除此之外，它是超级快速，搜索引擎友好，响应迅速，并提供了一个开箱即用的 PWA，而不必做太多。

[![Also this!](img/634af7f7f17ee95bd758fd42baf4b092.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A_WmIjQo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/anamritraj/anamritraj.tech/blob/master/src/pages/metrics-25-05-2018.PNG%3Fraw%3Dtrue)

你可以在谷歌灯塔审计工具中看到相当高的数字。这些都是很高的分数，对搜索引擎优化和移动很好。

## 如何打造自己的？

所以你想这样建立自己的博客？你来对地方了！让我们覆盖它！

我假设你已经有了以下的东西，如果没有，请在网上搜索如何得到它们。

1.  饭桶
2.  GitHub 账户
3.  文本编辑器(使用 VS 代码)
4.  特拉维斯-CI 账户。(这是可选的)

### 第一步:克隆

克隆[这个仓库](https://github.com/anamritraj/anamritraj.tech.git)

```
git clone https://github.com/anamritraj/anamritraj.tech.git 
```

### 第二步:进行修改

现在您已经克隆了这个库，使用 cmd/terminal 进入那个文件夹并运行

```
yarn
# or if you are an npm user
npm install 
```

这将在您的计算机上安装所有项目依赖项。现在，让我们来测试一下..

```
yarn dev 
# or 
npm run dev 
```

这将在 [http://localhost:8000](http://localhost:8000) 上启动开发服务器。

转到`/src/pages/`并创建一个新文件。留意你的浏览器。只要你点击保存，文章就会在浏览器中反映出来。这是热重装，也是我最喜欢的功能之一。这是迄今为止我最喜欢的 Gatsby.js 的特色。

现在，在部署一个上面有我的名字和照片的网站之前(我很喜欢，但你可能不想)，让我们做一些改变。

我会列出你可能需要修改的地方。

```
README.md
package.json
script/deploy-to-gh-pages.sh
gatsby-config.js
src/components/Bio.js
src/templates/blog-post.js
static/CNAME 
```

**注意**:如果你不打算使用自定义域，删除那个`static/CNAME`文件。否则，在那里添加您的域。

我想谈谈一个特别的变化。如果你检查`/gatsby-config.js`文件。有一个参数叫`pathPrefix`。如果您不打算使用自定义域名，那么您需要将其设置为`/REPO_NAME`。这将告诉 Gatsby.js 在浏览网站时添加必要的链接路径。

这些都是您需要进行更改以自定义名称和一些变量的文件。我会把具体的变化留给你去探索。

### 第三步:部署！

现在您已经对您的站点*进行了更改。让我们把它展示给全世界。为此，我们将使用 Github 页面。*

如果你没有 Github 帐号，现在创建一个也许是个好主意。

现在让我们在 Github 上创建一个新的存储库。
去[https://github.com/YOUR_USERNAME?tab=repositories](https://github.com/YOUR_USERNAME?tab=repositories)

点击那个写着*新*的绿色大按钮。给你的回购命名并公开。不要选择任何其他内容，然后单击创建存储库。

从您的终端运行

```
# Rename any old origins if you have any
git remote rename origin destination
# Point to the newly created repository
git remote add origin https://github.com/YOUR_USERNAME/REPO_NAME.git
git push -u origin master
# Now the usual stuff
git add .
git commit -m "Initial commit"
git push -u origin master 
```

在 GitHub 上重新加载页面，您应该会看到代码。

现在，有两种部署代码的方法，都使用 GitHub 页面来服务静态文件。

1.  使用您的终端部署到`gh-pages`分支
2.  使用 Travis-CI 部署您的静态资产

**方式 1:简单的方式**

如果你看一下`package.json`文件，有一个`npm`脚本`git-deploy`。这将构建项目并将`/public`目录中的所有内容推送到`gh-pages`远程分支。

从那里你可以到这里的库设置[https://github.com/YOUR_USERNAME/REPO_NAME/settings](https://github.com/YOUR_USERNAME/REPO_NAME/settings)并启用 GitHub 页面使用`gh-pages`分支。

恭喜你！您已成功部署您的代码，您的网站现已上线

[https://YOUR _ username . github . io/REPO _ NAME](https://YOUR_USERNAME.github.io/REPO_NAME)

**方式 2:酷炫的方式，少了烦人，少了手动**

让我们为这个存储库设置一个 Travis-CI 帐户。转到[https://travis-ci.com](https://travis-ci.com)并注册/登录。继续用 Travis-CI 设置您的 Github 存储库。

现在您已经设置好了，转到 Travis dashboard，选择您的存储库，转到该存储库的设置，然后转到**环境变量**部分

让我们添加几个在我们的`/script/deploy-to-gh-pages.sh`脚本中使用的环境变量。
第一个是你回购的路径

```
repo_path = YOUR_GITHUB_USERNAME/REPO_NAME
# In my case it is anamritraj/anamritraj.tech 
```

第二个是 GitHub API 密钥。转到[该页面](https://github.com/settings/tokens)并点击生成新令牌。选择显示`repo`的第一个复选框。给你的令牌一个名字。现在确保你把它保存在一个安全的地方，把它当成你的密码，不要和任何人分享。

现在，回到 Travis 设置页面，添加另一个环境变量。

```
github_token = YOUR_TOKEN 
```

此外，确保构建日志中的**显示值关闭。这将防止该令牌出现在构建日志中。**

现在你所要做的就是进行修改，并将代码推送到`master`。Travis 会自动为你构建，并将代码部署到`gh_pages`分支。

这个的脚本写在`/script/deploy-to-gh-pages.sh`里。如果你愿意，你可以调整设置。

## 第四步:迭代！

这绝不是一个完整的教程，它只是你用 Gatsby.js 所能达到的最高境界。

我想在这个博客上写更多的东西，只是因为读起来感觉很爽快。我可能会写更多。

在那之前，圣诞快乐，新年快乐！*