# 固定 NPM 软件包的 4 个技巧

> 原文：<https://dev.to/elie222/techniques-for-fixing-npm-packages-4ko6>

如果你是一个 Javascript，你可能会使用许多 npm 包。有时候一个包有一个 bug 或者不完全符合你的要求。这篇文章详细介绍了解决这个问题的几种不同方法。

我经常发现自己浪费时间试图做一行修复，所以我希望这里列出的策略能对其他人有所帮助。提到的许多工具在开发包时也很有用。

[![npm](img/16b495488e08a9f022762e7527b20946.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--96Ai1UZm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ANzKbzPiOaAKE6iHxgvAF0g.png)

### 方案

我们从最明显的开始。解决问题最基本的方法就是使用不同的软件包。如果它是一小块功能，你可以自己重写，避免使用软件包。

这有时是可行的，但是如果包包含大量代码，这种解决方案可能不实用。除了从头开始完全重写之外，您还可以将包的内容复制并粘贴到您的本地项目中，并根据需要进行编辑。这种方法的缺点是，您将无法轻松地跟踪其他人将来对包所做的更新。在许多情况下可能不是最佳解决方案。

也许在尝试自己修复一个包之前，你应该做的第一件事就是在 GitHub repo 中发布一个问题。您试图添加的功能可能已经存在，或者您在误用这个库，包的维护者可以为您指出正确的方向。如果这真的是一个 bug，包的维护者可能是最适合修复它的人。

### 局部修复

#### NPM/纱线链接

修复一个包的第一种方法是在本地派生和克隆它。然后您可以使用`npm link`或`yarn link`在您的项目中使用它。您对分叉包所做的任何更改都将反映在您的项目中。

您需要运行两个命令来链接。[使用纱线](https://yarnpkg.com/lang/en/docs/cli/link/):

```
yarn link # in the root of the forked package
yarn link forked-package # in the root of your project 
```

您还需要在分叉的包上运行`yarn install`,并运行任何构建/预发布步骤。在过去，我有时发现构建/预发布步骤很烦人。您的里程可能会有所不同，这取决于包装是如何写的。

这个解决方案的另一个问题是它只创建了一个包的本地符号链接。这意味着你只解决了本地的问题，而不是团队成员的问题。

一旦您在本地修复了问题，您将希望将它发布到 GitHub，或者依赖您的包的分叉版本，向主包发出一个 pull 请求，或者以您的名义将包发布到 npm。

#### yalc

Yalc 类似于 npm link，但是它在本地发布分叉包，而不是创建符号链接。我发现它通常比 npm link 工作得更好，因为它处理一个包维护者用来发布到 npm 的完整构建过程，包括`npm pack`。

使用 [yalc](https://github.com/whitecolor/yalc) 类似于使用 npm/yarn link:

```
yalc publish # in the root of the forked package
yalc add forked-package # in the root of your project 
```

我在使用 yalc 时遇到的一个问题是，当你想修复一个依赖于另一个包的包时。关于这种情况的更多讨论，请看这一期。

您可以将 yalc 更改提交给 git，以便与其他团队成员共享修复。这有助于快速解决问题，尽管不应该作为长期解决方案。

#### 补丁包

另一个解决方案是使用[补丁包](https://github.com/ds300/patch-package)。为了利用它，你不需要分叉 bug 包。你可以简单地用你想要的改变编辑你的`node_modules`目录。

要使用修补程序包:

```
# add to your package.json:
 "scripts": {
   "postinstall": "patch-package"
 }

# fix the broken file in your project:
# e.g. node_modules/some-package/brokenFile.js 
```

您可以将补丁添加到 git repo 中，以便与团队的其他成员共享这些更改。修补程序包通过创建包含更改的修补文件，将这些更改应用到项目本身。

然而，补丁包也有一些问题。当包作者将项目发布到 npm 时，许多原始文件会在此过程中丢失。

例如，如果项目是用 TypeScript/ES6+编写的，那么原始文件可能没有发布到 npm，而只是最终的 transpiled 版本。如果`package.json`包含`files`字段，或者项目包含一个`.npmignore`文件，就是这种情况。那么并不是项目中的所有文件都会下载到您的`node_modules`文件夹中。通常这是一件好事，因为它减少了需要下载的数据量，但这使得使用补丁包变得困难。

你仍然可以使用补丁包来编辑最终的 ES5 或者缩小版，但是这通常是一种不舒服的体验。

此外，即使 src 文件夹已经发布到 npm，您仍然需要在本地运行构建步骤。如果构建项目所必需的一些文件已经被排除，这将是不可能的，除非您从 GitHub 中获取丢失的文件。在这个阶段，在本地的一个单独的文件夹中克隆这个包可能更容易。

除此之外，你对补丁包所做的任何修改都不会与更广泛的社区和任何使用这个包的人共享，而这些人可能会从你的修改中受益。

#### GitHub 上的叉子

另一个解决方案是做出你需要的修改，并发布到 GitHub/GitLab(或者你使用的任何其他源代码控制平台)。

这通常可以与上述步骤一起完成。您可以首先使用 *npm/yarn link* 或 *yalc* 进行修复，检查所有工作是否符合预期，然后将更改推送到您自己的 Git repo。然后，您可以运行以下操作之一，将该包添加到您的项目中，并使其他人可以访问它(根据需要更改 URL):

```
yarn add https://github.com/fancyapps/fancybox [remote url]

yarn add https://github.com/fancyapps/fancybox#3.0  [branch/tag]

yarn add https://github.com/fancyapps/fancybox#5cda5b529ce3fb6c167a55d42ee5a316e921d95f [commit] 
```

如果你需要做任何自动化的构建，你可以看看这个 [StackOverflow 帖子](https://stackoverflow.com/questions/48287776/automatically-build-npm-module-on-install-from-github)。

此时，您还可以向主 repo 发出一个 Pull 请求，为使用该包的每个人合并变更。一旦更改被合并到中，您就可以返回使用原来的包。

按照这些思路，您也可以在您自己的 npm 帐户下重新发布该软件包，然后像安装任何 npm 软件包一样安装它。

### 其他方案

如果您使用的是 monorepo 设置，比如 Lerna 或 Rush，那么您可以在本地克隆这个包，并像在项目中使用其他 Lerna 包一样使用它。

最终可用的解决方案是猴子补丁。你可以在 Node.js [阅读更多关于 monkey 补丁的内容。](https://johncoder.com/monkey-patching-in-node-js/)

### 最后的话

以上解决方案都不完美。在上面列出的解决方案中，要么是补丁包，要么是 yalc，然后将修改提交给 GitHub，这是我最喜欢的解决方案。但是所有的解决方案都有问题。这么多年过去了，我们仍然没有找到简单包修复的圣杯。

如果你知道我错过的一些解决方案，并且知道解决问题的更好方法，我很乐意在下面的评论中知道！

如果你喜欢这篇文章，一定要给我喜欢，并在未来类似的内容:)。你可以在 Twitter 上关注我: [elie2222](https://twitter.com/elie2222) 。