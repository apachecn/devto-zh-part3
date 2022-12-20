# 在 Github 页面上托管 Harp

> 原文：<https://dev.to/megazear7/hosting-harp-on-github-pages-4oig>

Harp 是我最近在学习的一个静态站点生成器。像往常一样，我开始甚至最小的项目与托管和部署的想法。我研究了托管 Harp 的最佳方式，并考虑了 Firebase 和 Heroku。然而，我意识到，因为 Harp 是一个静态站点生成器，所以不使用 Firebase 或 Heroku 上的三个空闲层插槽中的一个，而是使用 Github 页面可能是有意义的。在这篇博文中，我将讨论在 Github 页面上托管 Harp。如果你还没有读过，回头看看之前在 Harp 上发表的名为[开始使用 Harp](https://www.alexlockhart.me/2018/12/getting-started-with-harpjs.html) 的博客文章可能会有所帮助。

## 第一步:不使用项目初始化

相反，只需创建以下项目目录结构:

*   我的项目/
    *   src/
    *   文档/
    *   README.md

将以下内容添加到 README.md 文件中:

```
## My Project Name

### Setup
'''
npm install -g harp
'''

### Development
'''
harp server src
'''

Go to localhost:9000

### Deployment
'''
harp compile src docs
git add .
git commit -m "Recompiled."
git push origin master
''' 
```

(注意:为了让这个自述文件显示为 markdown 而不是 rendered-markdown，我必须用单引号替换反勾号。复制和粘贴时请记住这一点。)

在 src 文件夹中，创建一个包含以下内容的“index.ejs”文件:

```
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <h1>Hello, World!</h1>
  </body>
</html> 
```

现在运行以下命令:

```
harp compile src docs
git init
git add .
git commit -m "Init commit." 
```

## 第二步:创建 Github 远程仓库

转到[github.com/new](https://github.com/new)并创建您的存储库，输入名称，但不要选择任何其他选项。这样做之后，请按照“从命令行推送现有存储库”的说明进行操作。

## 第三步:设置 Github 页面

转到新创建的 github 存储库的设置，并滚动到“Github 页面”部分。在 source 下拉列表中，您应该有一个“主分支/文档文件夹”选项。选择此选项并选择保存。您的 Harp 静态站点现在应该可以在 Github 提供的 url 上找到，该 URL 位于设置页面的“Github Pages”部分的顶部。

## 第四步:提供自定义域

这显然是可选的，需要你拥有一个域名。我还假设你会把网站放在一个子域的领域。

转到您的 DNS 管理器，添加一个 CNAME 记录，其中包含您要使用的子域的“名称”和“. github.io”的“值”。您的网站现在应该可以在您的域名下提供的子域。例如，我在[jacksfamilypizza . alexlockhart . me](https://jacksfamilypizza.alexlockhart.me/)上有一个 Harp 网站。

就是这样！现在，您应该可以使用 Harp 开发一个静态网站，并实时查看您的变化。请记住，我们在项目的自述文件中添加了开发和部署步骤。

查看我的博客,了解更多我对技术和其他各种话题的思考。