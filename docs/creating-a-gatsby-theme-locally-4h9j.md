# [预稳定版本]开始我们的第一个盖茨比主题

> 原文：<https://dev.to/ekafyi/creating-a-gatsby-theme-locally-4h9j>

> ⚠️**2019 年 7 月 7 日更新:**这篇文章是在主题稳定之前*写的。这篇文章中的一些 API 和代码现在可能不准确。小心行事。⚠️*

* * *

🧐 *要了解更多关于盖茨比的主题，请查看我在这个系列中的[第一](https://dev.to/ekafyi/learning-gatsby-themes-introduction-1gje)和[第二](https://dev.to/ekafyi/using-gatsby-themes-in-a-non-theme-starter-54c7)帖子。*

一个盖茨比**主题**是一个盖茨比站点的*可重用块，可以*共享、扩展和定制* ( [源](https://youtu.be/SV2zu9FvUTw?t=406))。它使我们能够分离我们站点的功能，以模块化的方式在多个站点中共享、重用和修改。*

在这篇文章中，我们通过*构建一个基本的、基本的主题*开始主题化，这个主题叫做`gatsby-theme-hello-world`。我们正在本地创建这个*，这意味着文件在我们的本地机器上，我们还不打算发布主题。为了检查主题是否可用，我们还将创建一个新的空白站点，添加主题，并从我们的站点添加内容。*

这主要是基于约翰·奥坦德和杰森·伦斯托夫制作的视频的前 24 分钟，稍作修改。

我们走吧！

[![the band Ramones on stage with text Hey Ho, Let's Go!](img/5e76689a96627ca1f4e1bfd96ef7c832.png)](https://i.giphy.com/media/5HMukcn0XDp5K/giphy-downsized.gif)

**目录**

1.  创建文件夹
2.  在根、主题和站点中创建`package.json`
3.  向主题添加 devDependencies 和 peerDependencies
4.  向主题添加依赖项
5.  创建索引页面并在主题中发布
6.  向网站添加依赖项(包括主题)
7.  在网站中创建帖子
8.  添加重影(可选)

> ⚠️·盖茨比的主题目前还处于实验阶段。主题 API 将来可能会改变。⚠️

* * *

### 1。创建我们的文件夹

我们正在创建我们的项目文件夹，其中包含分别用于主题和站点的两个文件夹。因此，我们谈论的是三个地点的*:*

*   项目(工作区)根——我称它为`eka-site-with-theme`，可以随意使用任何其他名称
*   主题— `eka-site-with-theme/gatsby-theme-hello-world`
*   站点— `eka-site-with-theme/example-site`

```
# create and go to our project folder
mkdir eka-site-with-theme
cd eka-site-with-theme

# make our theme and site folders
mkdir gatsby-theme-hello-world example-site 
```

如果是和[这个视频](https://www.youtube.com/watch?v=PS2784YfPpw)比较，项目根是`gatsby-themes`，主题文件夹是`gatsby-themes/packages/gatsby-theme-livestream`，站点文件夹是`gatsby-themes/site`。您可以随意排列和命名文件夹。请确保在下一步中正确引用子文件夹！

**注意:**您不必从头开始创建站点部件。或者，你可以像往常一样[使用`gatsby new`命令](https://www.gatsbyjs.org/tutorial/part-zero/#create-a-gatsby-site)，或者使用你的*现有* Gatsby 站点的主题。想了解更多关于后者的内容，请看我下面的帖子。

[![ekafyi image](img/c93ebdb7f76a26e4e235feb2075f96ee.png)](/ekafyi) [## [预稳定版本]在非主题启动器中使用 Gatsby 主题

### eka May 4 ' 1912 分钟读取

#gatsby #beginners #webdev](/ekafyi/using-gatsby-themes-in-a-non-theme-starter-54c7)

### 2。创建`package.json`

我们将通过在*的所有三个*文件夹中运行`yarn init`来创建`package.json`。您可以填写或跳过大多数默认值，但请确保填写下面的*必填*内容。

#### 2a)根

```
// eka-site-with-theme/package.json
{
  "name": "eka-site-with-theme",
  "private": true,
  "workspaces": [
    "example-site",
    "gatsby-theme-hello-world",
  ]
} 
```

如你所见，我们在`workspaces`数组中添加了我们的*站点*和*主题*文件夹。这使我们能够在本地使用主题——在本例中只有一个主题。如果你想了解更多，请阅读[这篇关于纱线工作空间的帖子](https://github.com/gatsbyjs/gatsby/blob/beacac6215e7a527a937a121a482f0b60bd69126/docs/blog/2019-04-01-setting-up-yarn-workspaces-for-theme-development/index.md)。

`workspaces`字段接受通配符。例如，如果您有多个本地主题，您可以将它们放在一个名为`packages`的文件夹中，并将`"packages/*"`添加到数组中，而不是一个接一个地列出主题文件夹。

#### 2b)地点(`example-site`)

```
// eka-site-with-theme/example-site/package.json
{
  "name": "example-site",
  "version": "1.0.0",
  "private": "true"
} 
```

这个文件夹不打算发布，所以我们可以将`private`设置为`true`并省略入口点(`main`)。

#### 2c)主题(`gatsby-theme-hello-world`)

```
// eka-site-with-theme/gatsby-theme-hello-world/package.json
{
  "name": "gatsby-theme-hello-world",
  "version": "1.0.0",
  "license": "MIT",
  "main": "index.js"
} 
```

我们必须在我们的主题包中提供一个入口点(`main`)，以便 Gatsby/Node 尝试解析这个包。不要忘记在这个文件夹中创建一个空的`index.js`；否则，我们将无法使用这个主题从我们的网站。省略`license`不会导致错误，但会抛出警告。

现在我们已经创建了所有三个包文件，回到根文件夹并运行`yarn workspaces info`。您的命令行应该会显示如下内容:

```
# eka-site-with-theme/gatsby-theme-hello-world
**yarn workspaces v1.15.2**
{
  "example-site": {
    "location": "example-site",
    "workspaceDependencies": [
      "gatsby-theme-hello-world"
    ],
    "mismatchedWorkspaceDependencies": []
  },
  "gatsby-theme-hello-world": {
    "location": "gatsby-theme-hello-world",
    "workspaceDependencies": [],
    "mismatchedWorkspaceDependencies": []
  }
} 
```

在步骤 3 到 5 中，我们将在*主题*文件夹中工作，在步骤 6 和 7 中，我们将在*站点*文件夹中工作。

### 3。在主题中安装`devDependencies`和`peerDependencies`

我们正在安装以下软件包作为`devDependencies`和`peerDependencies`:

*   盖茨比（姓）
*   反应
*   反应范围

```
# eka-site-with-theme/gatsby-theme-hello-world
yarn add gatsby react react-dom -D -P 
```

[`devDependencies`](https://yarnpkg.com/en/docs/dependency-types#toc-devdependencies) 是*开发*(创建)主题所需要的依赖项，而 [`peerDependencies`](https://yarnpkg.com/en/docs/dependency-types#toc-peerdependencies) 是为了确保用户安装我们的主题时使用这些依赖项的相同副本。

### 4。向主题添加依赖项

我们正在添加以下依赖项:

*   盖茨比插件页面创建者
*   gatsby-mdx，@mdx-js/mdx，@mdx-js/tag，@mdx-js/react

#### 4a) `gatsby-plugin-page-creator`

默认情况下，Gatsby 从`src/pages`文件夹中的组件创建页面。这对于非主题的站点来说很好，但是当我们使用主题时，我们希望我们的站点也包含主题的`src/pages`文件夹。为了实现这一点，我们使用`gatsby-plugin-page-creator`。运行下面一行来安装，然后创建一个新的`gatsby-config.js`文件并在那里注册插件。

```
# eka-site-with-theme/gatsby-theme-hello-world
yarn add gatsby-plugin-page-creator 
```

```
// eka-site-with-theme/gatsby-theme-hello-world/gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: "gatsby-plugin-page-creator",
      options: {
        path: `${__dirname}/src/pages`
      }
    }
  ]
}; 
```

#### 4b) `gatsby-mdx`

[MDX](https://mdxjs.com/) 是一种新的格式，可以让你在减价文件中使用 JSX。我们将添加这个插件及其依赖项，以便 Gatsby 可以读取 MDX 文件。像前面的依赖项一样，我们安装软件包，打开配置文件，并将`gatsby-mdx`添加到`plugins`数组中。

```
# eka-site-with-theme/gatsby-theme-hello-world
yarn add gatsby-mdx @mdx-js/mdx @mdx-js/tag @mdx-js/react 
```

```
// eka-site-with-theme/gatsby-theme-hello-world/gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: "gatsby-plugin-page-creator",
      options: {
        path: `${__dirname}/src/pages`
      }
    },
    `gatsby-mdx`
  ]
}; 
```

📝**注:** `gatsby-mdx`只是一个例子；如果你熟悉其他源和转换器插件(例如`gatsby-source-filesystem`和`gatsby-transformer-remark`，请随意替换它们。

### 5。将页面添加到主题

主题是常规的 Gatsby 站点，所以我们可以像往常一样开发它。

#### 5a)首页

首先，让我们创建`src/pages`文件夹，并创建导出一个呈现标题文本的简单组件的`index.js`。这是使用 React 组件创建页面的标准方式。

```
# eka-site-with-theme/gatsby-theme-hello-world
# create src/pages folder
mkdir src && mkdir src/pages 

# create index.js 
touch src/pages/index.js 
```

```
// eka-site-with-theme/gatsby-theme-hello-world/src/pages/index.js

import React from "react";
export default () => <h1>Theme Home Page</h1>; 
```

#### 5b)“你好-主题”页面

第二，在`src/pages`中制作一个名为`hello-theme.mdx`的文件，并添加一些文字。这里，我们可以使用 MDX 格式，因为我们已经安装了`gatsby-mdx`。在实际用例中，您可以自由选择最适合您需求的。

<figure>

```
# Hello world from theme

This content is created from the `gatsby-theme-hello-world` theme. 
```

<figcaption>eka-site-with-theme/gatsby-theme-hello-world/src/pages/hello-theme.mdx</figcaption>

</figure>

现在让我们作为一个常规站点运行我们的主题，检查是否一切正常。

```
# eka-site-with-theme/gatsby-theme-hello-world
gatsby develop 
```

请访问 http://localhost:8000 并查看我们的索引页面。然后去[http://localhost:8000/Hello-theme](http://localhost:8000/hello-theme)看看我们的“Hello world”帖子。

[![Page with title Theme Home Page](img/e702639af13b020f87ce4470b6a56b18.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--rVdWQSb4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vu7og9uonprv7i8cjrsy.png) [ ![Page with title Hello World from Theme](img/cf0c60b5f4386add6c8c4a3f0eeb4fcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s59EhPQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pl2ih5grvgr4vh03ll7w.png)

#### 🌮号外:`yarn workspace`方式

除了像往常一样使用来自*主题*文件夹的`gatsby develop`，我们还可以使用来自工作区根目录的`yarn workspace`命令。为此，我们需要将`gatsby develop`命令添加到主题的`package.json`文件中。

```
// eka-site-with-theme/gatsby-theme-hello-world/package.json
{
  "name": "gatsby-theme-hello-world",
  // other things
  "scripts": {
    "develop": "gatsby develop"
  }
} 
```

现在转到工作区根文件夹并运行这个命令。

```
# from workspace root (eka-site-with-theme)
yarn workspace gatsby-theme-hello-world develop

# is equal to this
# in theme folder (eka-site-with-theme/gatsby-theme-hello-world)
yarn develop 
```

这也适用于任何其他的`yarn`命令。

现在我们已经有了一个工作主题，是时候创建我们的网站并使用这个主题了！

### 6。向网站添加依赖项(包括主题)

转到我们的*站点*文件夹，然后安装我们的依赖项:

*   盖茨比（姓）
*   反应
*   反应范围

```
# eka-site-with-theme/example-site
yarn add gatsby react react-dom 
```

我们还添加了主题`gatsby-theme-hello-world`，作为一个依赖项。因为我们使用的是本地主题，我们可以像这样把它添加到`package.json`中:

```
// eka-site-with-theme/example-site/package.json
{
  "name": "example-site",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "gatsby": "^2.4.2",
    "react": "^16.8.6",
    "react-dom": "^16.8.6",
    "gatsby-theme-hello-world": "*"
  }
} 
```

就像步骤 4 中的插件依赖一样，我们也以类似的方式将我们的主题添加到`gatsby-config.js`中。首先在我们的*站点*文件夹中创建文件，并添加以下内容:

```
// eka-site-with-theme/example-site/gatsby-config.js
module.exports = {
  __experimentalThemes: [`gatsby-theme-hello-world`]
}; 
```

现在尝试运行应用程序。一旦应用程序运行，你应该能够访问 [http://localhost:8000](http://localhost:8000) 和[http://localhost:8000/hello-theme](http://localhost:8000/hello-theme)，就像我们从主题中做的那样。

### 7。在网站中创建帖子

作为一个用户，我们想要添加我们自己的网站内容。我们可以通过在`src/pages`中创建一个文件来做到这一点——姑且称之为`hello-user.mdx`。这一步类似于上面的第 6 步，但是我们现在是从*站点*文件夹中进行的。

<figure>

```
# Hello world from user

This content is created from the example user site. 
```

<figcaption>eka-site-with-theme/example-site/src/pages/hello-user.mdx</figcaption>

</figure>

现在再次运行应用程序，并前往[http://localhost:8000/hello-user](http://localhost:8000/hello-user)。你可以看到，除了主页和“hello-theme”页面之外，我们还有一个只存在于我们网站中的新页面。

[![Page with title Hello World from User](img/3fdbca6e7c6952cf9da880421a0ab50b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BGlZTphN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6ntqqnqntibxmtmj8hz.png)

### 8。添加重影(可选)

为了方便用户，主题开发者可以集成任何必要的组件，因此用户不必自己安装和配置组件。(而且，这真的只是一个使用我非常着迷的 miukimiu/react-kawaii 库的借口。)

返回到`gatsby-theme-hello-world`文件夹，运行`yarn add react-kawaii`安装软件包。

打开用户发布文件`hello-user.mdx`并从`react-kawaii`添加一个组件。

<figure>

```
import { Ghost } from "react-kawaii";

# Hello world from user

This content is created from the example user site.

<Ghost size={160} mood="happy" color="#f0f0f0" /> 
```

<figcaption>eka-site-with-theme/example-site/src/pages/hello-user.mdx</figcaption>

</figure>

现在再次运行 *example-site* app，进入[http://localhost:8000/hello-user](http://localhost:8000/hello-user)。你应该看到你的帖子下面有一个鬼插图。

[![Same page as Hello World from User above, but with ghost illustration](img/4d3aedd855a1ba43fc91e08cd9adf7e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T415oxWw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e139aup77dhjhyr95512.png)

因此，我们已经成功地构建了一个主题，并在我们的新站点中使用了它。耶！🎉

* * *

## 📝笔记和想法

和往常一样，我在分享我的(主观！)关于我在这篇文章中所创造的东西的想法。如果你有什么要补充的，请随便插话。

🤔 ***这不是工程过度吗？**T3】*

如果你*只*需要创建一个“Hello World”页面，是的，绝对需要！然而，我确实发现潜在的用例——你可以在我的第一篇文章的[链接中找到——是合理的。我做这种基本的实现是为了在进入更复杂(和更现实)的用法之前探索原理。](https://dev.to/ekafyi/learning-gatsby-themes-introduction-1gje)

❓ ***与步骤 3 有关——我仍然不明白主题的对等依赖性。**T3】*

如果我现有的站点使用 Gatsby 2 . 0 . 0 版本，但是主题使用 2.1.1 作为 devDependency *和* peerDependency，该怎么办？它会迫使我的整个网站运行主题的盖茨比副本吗？还是只用主题的部分？如果我的网站使用另一个主题，例如使用 2.0.0 作为 peerDepencency，该怎么办？

我在这篇文章中使用的设置用于[直播-盖茨比主题](https://github.com/jlengstorf/livestream-gatsby-themes/blob/master/packages/gatsby-theme-livestream/package.json)和[盖茨比主题-碳](https://github.com/carbon-design-system/gatsby-theme-carbon/blob/master/packages/gatsby-theme-carbon/package.json)。但是，其他主题使用不同的设置，如[盖茨比-主题-最小化](https://github.com/ChristopherBiscardi/gatsby-starter-theme/blob/master/gatsby-theme-minimal/package.json)(仅`peerDependencies`)和[盖茨比-主题-示例-组件-扩展](https://github.com/johno/gatsby-theme-example-component-extending)(既不是`dev`也不是`peerDependencies`)。如果有人能解释其中的原理，我将不胜感激。

* * *

伙计们，就这样吧。

接下来:我将继续开发主题，演示[组件隐藏](https://www.gatsbyjs.org/blog/2019-04-29-component-shadowing/)，并使用`gatsby-node.js`以编程方式创建页面。

敬请关注，感谢阅读！🙌🏾