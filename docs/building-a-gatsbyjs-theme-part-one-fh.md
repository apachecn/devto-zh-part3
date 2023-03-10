# 构建盖茨比主题-第一部分

> 原文：<https://dev.to/billyjacoby/building-a-gatsbyjs-theme-part-one-fh>

## 设置我们的主题开发环境

欢迎来到教程的第一部分。如果你不知道什么是盖茨比主题，或者为什么你会使用它们，你可以在这里参考本教程的第一部分。

本教程面向那些想要创建主题并发布给其他用户，或者想要创建可以在他们自己的项目中使用的主题的开发人员。

你可以在我的 github 页面上查看这部分教程的最终代码:

[盖茨比主题——第一部分](https://github.com/billyjacoby/gabsty-theme-tutorial/tree/part-one)

也就是说，在本教程的这一部分中，我们必须解决一些事情，以使我们的开发环境能够构建和运行主题:

*   初始化一个“伞状”NPM 包，包含我们的主题包，以及我们的开发网站
*   初始化一个包含所有主题文件和配置的 NPM 包
*   初始化一个 NPM 包，它将作为使用我们主题的用户站点

现在请原谅我，因为当我第一次开始构建这个主题时，这一切对我来说似乎有点令人生畏，而且重复。

出于开发目的，我们最初将把 NPM 包构建为私有包。这样我们就可以使用`yarn`的工作区功能。

### NPM Init(三次)

好的，那么我们要做的第一件事就是创建一个目录来保存我们的整个主题开发环境，然后`cd`进入这个目录。

`mkdir gatsby-theme-tutorial-development`

`cd gatsby-theme-tutorial-development`

一旦进入这个文件夹，我们将第一次运行`npm init`。你不需要担心填写 NPM 刚才问你的信息。如果您一直按 enter 键，您应该有一个类似于下面的`package.json`文件:

package.json

```
{  "name":  "gatsby-theme-tutorial-development",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "MIT"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要对这个文件做两个修改，以便配置和允许我们使用 yarn 的工作空间特性。将以下几行添加到您的`package.json`文件的底部:

/Gatsby-theme-tutorial-development/package . JSON

```
...  "private":  true,  "workspaces":  [  "site",  "packages/*"  ] 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们需要将提到的目录添加到我们的`gatsby-theme-tutorial`文件夹中。

`mkdir packages`

`mkdir site`

现在我们`cd`进入`packages/`文件夹，为我们的主题创建目录

`cd packages`

`mkdir gatsby-theme-tutorial`

到目前为止，我们的文件夹结构如下所示:

```
gatsby-theme-tutorial-development/
--packages/
  |--gatsby-theme-tutorial/
--site/
--package.json 
```

Enter fullscreen mode Exit fullscreen mode

现在在`gatsby-theme-tutorial`文件夹和`site`文件夹中再次运行`npm init`命令。我们可以在所有的提示中按回车键，稍后再考虑填写信息。这将使我们的目录结构看起来像这样:

```
gatsby-theme-tutorial-development/
--packages/
  |--gatsby-theme-tutorial/
    |--package.json
--site/
  |--package.json
--package.json 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在所有的问题都解决了，我们可以开始为我们的主题安装必要的依赖项了。我们将在开发环境的主目录中完成所有这些工作，在本教程中它将是`/gatsby-theme-tutorial-development`。

从这个目录中，将必要的依赖项添加到 Gatsby 的主题工作区中。

`yarn workspace gatsby-theme-tutorial add -D gatsby react react-dom`

完成后，您应该会在 gatsby 主题文件夹中看到一个`node_modules/`文件夹。

现在我们需要将我们的 devDependencies 复制到 gatsby 主题包. json 文件中的 peerDependencies。

完成后,`package.json`文件应该是这样的:

/Gatsby-theme-tutorial-development/Gatsby-theme-tutorial/package . JSON

```
{  "name":  "gatsby-theme-tutorial",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "MIT",  "devDependencies":  {  "gatsby":  "^2.4.2",  "react":  "^16.8.6",  "react-dom":  "^16.8.6"  },  "peerDependencies":  {  "gatsby":  "^2.4.2",  "react":  "^16.8.6",  "react-dom":  "^16.8.6"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要将主题包和 gatsby 脚本添加到我们站点的 package.json 文件中。我们将这样添加:

/Gatsby-theme-tutorial-development/site/package . JSON

```
{  "name":  "site",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  ///  NEW  LINES  "develop":  "gatsby develop",  "build":  "gatsby build"  },  "author":  "",  "license":  "MIT",  ///  NEW  LINES  "dependencies":  {  "gatsby-theme-tutorial":  "*"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们需要做的最后一件事是将 Gatsby 和 React 依赖项添加到我们的站点工作区。

`yarn workspace site add gatsby react react-dom`

我们的`site`目录的最终`package.json`现在应该是这样的:

```
{  "name":  "site",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "develop":  "gatsby develop",  "build":  "gatsby build"  },  "author":  "",  "license":  "MIT",  "dependencies":  {  "gatsby":  "^2.4.2",  "gatsby-theme-tutorial":  "*",  "react":  "^16.8.6",  "react-dom":  "^16.8.6"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

好了，深呼吸，知道我们所有的样板文件夹配置终于完成了！

接下来我们只需配置我们的`gatsby-config.js`文件，以便准备开始构建！

我们需要将`gatsby-plugin-page-creator`安装到我们的主题中。为此，请运行以下命令。

`yarn workspace gatsby-theme-tutorial add gatsby-plugin-page-creator`

在我们的`gatsby-theme-tutorial`文件夹中创建一个`gatsby-config.js`文件，如下所示:

`gatsby-theme-tutorial/gatsby-config.js`

```
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-page-creator`,
      options: {
        path: `${__dirname}/src/pages`
      }
    }
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要在这个文件夹中添加一个`index.js`文件，这样我们的站点就可以将这个文件夹识别为 NPM 包。这个文件不需要包含任何东西，除了一个注释。

`gatsby-theme-tutorial/index.js`

```
// no-op 
```

Enter fullscreen mode Exit fullscreen mode

在 out `site`文件夹中，我们需要创建一个超级简单的配置文件，告诉我们的站点使用我们的新主题！

`site/gatsby-config.js`

```
module.exports = {
  __experimentalThemes: ["gatsby-theme-tutorial"]
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在主题文件夹中创建一个基本的索引页面，看看是否一切都如预期的那样工作。

`gatsby-theme-tutorial/src/pages/index.js`

```
import React from 'react';

export default () => (
  <h1>Hello World!</h1> ); 
```

Enter fullscreen mode Exit fullscreen mode

现在来检查我们的工作...

从我们的`gatsby-theme-tutorial/`目录运行`yarn`最后一次，以确保我们所有的包都被正确安装。

然后运行`yarn workspace site develop`，你将有望在网络浏览器中看到我们的索引页面！

### 结果

现在，如果你和我一样，你可能会想“哇，我们需要做那么多工作来建立一个索引页面？”

gatsby 主题的神奇之处在于，我们可以在主题包中创建所有的样板页面和样式，任何使用该主题的 Gatsby 构建都可以访问这些文件！

此外，如果我们需要更新一个组件或一些样式，我们可以在我们的主题中这样做，然后将更新推至 NPM，任何其他使用该主题的项目只需更新我们的主题包，以便看到这些更新！

如果有些事情不像你预期的那样工作，记住你可以在我的 Github repo 上查看这个项目的第一部分的所有源代码，可以在这里找到[。](https://github.com/billyjacoby/gabsty-theme-tutorial/tree/part-one)

如果你被难住了，请随意评论这篇文章，或者在 twitter 上给我发消息！

### 接下来是什么

在本教程的第二部分，我们将开始深入研究如何设计我们主题的布局组件，并看到所有这些变化传播到使用我们主题的开发站点。

从现在开始会变得非常有趣，所以请继续关注，如果你有任何事情或任何问题，请发微博给我或留下评论！

***您可以在这里查看[教程的第二部分](https://billyjacoby.dev/building-a-gatsbyjs-theme-part-two)***