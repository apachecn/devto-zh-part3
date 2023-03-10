# 我如何在 GitHub 页面上使用 Vue.js

> 原文：<https://dev.to/tiim/how-i-use-vue-js-on-github-pages-45np>

我最近读了一篇文章[在 GitHub Pages](https://blog.usmanity.com/serving-vue-js-apps-on-github-pages/) 上提供 Vue.js 应用，它启发我写下我正在做的不同。

如果你想看看这种方法的实际应用，去看看我在 [GitHub](https://github.com/Tiim/Tiim.github.io) 上的[个人网站](https://tiimb.work)

我不会解释如何设置一个 Vue 项目。如果你正在寻找这方面的教程，去看看令人敬畏的 [Vue.js 指南](https://vuejs.org/v2/guide/)。

所以你已经设置了你的超棒的 Vue 项目，并想把它放在 GitHub 页面上。按照 Muhammad 的解释，你可以使用`npm run build`构建项目，提交`dist/`文件夹和你的源文件，并将 GitHub 指向 dist 文件夹。这可能会变得非常混乱，因为您要么提交消息的唯一目的是上传 dist 文件夹，要么您同时提交代码更改，这使得如果您想再次查看您的提交，很难找到相关的更改。

那么你对此能做些什么呢？

让我们使用一个包含所有构建文件的分支。

## 步骤 1 -保持我们工作部门的清洁🛀

为了确保我们工作的分支没有任何构建文件，我们将在根目录中添加一个`.gitignore`文件。

```
# .gitignore
dist/ 
```

## 第二步-添加第二个分支🌳

如果我们打算修改代码，将它合并回主分支，我们就不会像这样分支 master。相反，我们将创建一个非常干净的新分支，只保存 dist 文件。毕竟，我们永远不需要将这两个分支合并在一起。

为此，我们在 dist 文件夹中创建了一个新的 git 存储库:

```
cd dist/
git init
git add .
git commit -m 'Deploying my awesome vue app' 
```

## 步骤 3 -部署🚚

我们将把我们的新 git 库强制推送到 GitHub 上的一个分支。这可能违背了 git 的最佳实践，但是因为我们不会签出这个分支，所以我们不必担心这个问题。

```
git push -f git@github.com:<username>/<repo>.git <branch> 
```

⚠️确保你加倍或三倍检查你的目的地分行！您不想意外地覆盖您的工作分支。使用分支`gh-pages`很可能是个好主意。

## 第四步——将 GitHub 指向正确的位置👈

现在我们差不多完成了。唯一剩下的就是告诉 GitHub 我们的资产在哪里。

转到您的 repo，在右上角导航到`Settings`并向下滚动到 GitHub 页面。启用它并将您的源分支设置为您强制推送的分支，例如`gh-pages`。

## 第五步——自动化一切😴

如果您不介意在每次想要部署时都执行整个过程(步骤 2 和 3 ),现在可以停止了。如果你像我一样懒，下面是我用一个命令部署的脚本:

```
# deploy.sh

#!/usr/bin/env sh

# abort on errors
set -e

# build
echo Linting..
npm run lint
echo Building. this may take a minute...
npm run build

# navigate into the build output directory
cd dist

# if you are deploying to a custom domain
# echo 'example.com' > CNAME

echo Deploying..
git init
git add -A
git commit -m 'deploy'

# deploy
git push -f git@github.com:<username>/<repo>.git <branch>

cd - 
```

如果你在 windows 上研究 Linus 的 Windows 子系统(WSL ),这是值得的。

如果你还在读，非常感谢。这实际上是我的第一篇文章，我真的很高兴听到任何意见和批评。
快乐编码♥