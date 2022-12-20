# 构建盖茨比主题-第二部分

> 原文：<https://dev.to/billyjacoby/building-a-gatsbyjs-theme-part-two-7c7>

## 设计和配置我们的布局

这是从头开始构建您自己的 Gatsby 主题的多部分教程系列的第二部分。

如果你还没有看过这个系列的[介绍](https://billyjacoby.dev/intro-to-gatsby-themes)和[第一部分](https://billyjacoby.dev/building-a-gatsbyjs-theme-part-one)，因为我们将从第一部分结束时停下的地方继续。

这部分教程的完成代码可以在我的 github repo [这里找到。](https://github.com/billyjacoby/gabsty-theme-tutorial/tree/part-two)

这是我们将在这部分教程中解决的问题:

*   创建布局组件
*   使我们的布局组件能够跨所有页面工作
*   创建获取站点元数据的头组件
*   将我们的标题放在布局组件中，以便在所有页面上显示

### 创建布局组件

因此，设计主题的第一步是构建一个主布局组件。我们将采用与您在 Gatsby 中构建任何其他布局组件相同的方式来实现这一点。我们将在它们自己的文件夹中构建我们的样式，并将它们导入我们的组件。你也可以这样做，或者在 JS 中使用 CSS。

在你的主题目录中，在`gatsby-theme-tutorial/src/components/`目录中创建一个名为`layout.js`的文件。

Gatsby 中的布局组件接受一个`{children}`对象，围绕这个对象构建它们的布局，并呈现传递给布局组件的子元素。这就是在 gatsby 的布局组件中进行一些基本样式设计的方式:

***所有的风格只是为了展示这是如何工作的，我们将在后面的教程*** 中清理所有这些

**`/packages/gatsby-theme-tutorial/src/styles/layout.css`**T3】

```
.container {
  margin: -8px;
  padding: 8px;
  background-color: tomato;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  width: 100%;
}

main {
  display: flex;
  height: 100vh;
  justify-content: center;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

**`/packages/gatsby-theme-tutorial/src/components/layout.js`**T3】

```
import React from "react";
import "../styles/layout.css";

const Layout = ({ children }) => {
  return (
    <div className="container">
      <main>{children}</main>
    </div>
  );
};

export default Layout; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在一个普通的 Gatsby 项目中使用布局组件和主题的主要区别在于，我们希望这种布局可以用于所有可能构建的页面，而不仅仅是我们的主题附带的页面。为了演示这个想法，让我们从将这个布局文件导入到我们的主题目录中的 index.js 文件开始。

**`/packages/gatsby-theme-tutorial/src/pages/index.js`**T3】

```
import React from "react";
import Layout from "../components/layout";

export default () => (
  <Layout>
    <h1>Hello World!</h1>
  </Layout> ); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加一个关于网页到我们的网站目录。

**`/site/src/pages/about.js`**T3】

```
import React from "react";

const AboutPage = () => {
  return (
    <div>
      <h2>About page!</h2>
    </div>
  );
};

export default AboutPage; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们启动我们的开发服务器，看看我们的页面看起来怎么样。

`yarn workspace site develop`

在我们导航到`localhost:8000`后，您应该会看到我们的布局已经生效！

然而，如果我们导航到`localhost:8000/about`，你会注意到布局没有被渲染。这是因为我们还没有将布局组件导入到该页面中。

### 配置我们的布局在所有页面上工作

现在可以将我们的布局组件作为导出添加到我们的主题包中，这将使用户能够将我们的布局导入到所有必要的页面中。但是我们想建立一个盖茨比主题，让开发尽可能简单！

相反，我们可以做的是使用 Gatsby 的`wrapRootElement`包装在我们的布局组件中呈现的每个页面的根元素。这将把我们的布局组件添加到创建的每个页面中，而不必将其导入到我们的页面文件中。

继续操作，将您的`index.js`恢复为一个 H1 标签。

**`/packages/gatsby-theme-tutorial/src/pages/index.js`**T3】

```
import React from "react";

export default () => <h1>Hello World!</h1>; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将在我们的根主题目录中再创建两个文件。

`touch packages/gatsby-theme-tutorial/gatsby-browser.js`

`touch packages/gatsby-theme-tutorial/gatsby-ssr.js`

虽然这看起来有点重复，但是我们需要在这两个文件中放置相同的代码。这样做的原因是因为这段代码将运行在服务器端，或者在浏览器中运行，不管它们在哪里运行，我们仍然希望我们的根元素被包装。

所以我们的`gatsby-browser.js`和`gatsby-ssr.js`文件都应该包含以下代码:

**T2`/packages/gatsby-theme-tutorial/gatsby-browser.js`**

和

**`/packages/gatsby-theme-tutorial/gatsby-ssr.js`**T3】

```
import React from "react";
import Layout from "./src/components/layout";

export const wrapPageElement = ({ element, props }) => {
  // props provide same data to Layout as Page element will get
  // including location, data, etc - you don't need to pass it
  console.log(props);
  return <Layout {...props}>{element}</Layout>; }; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们检查我们的索引和关于页面，我们应该看到我们的布局已经呈现！

我们现在可以开始构建一个站点范围的标题和导航，它将显示在每个页面上，而不必将布局组件导入每个页面。

### 创建一个使用站点元数据的头组件

让我们创建一个简单的站点标题，显示站点名称和到主页的链接。

为此，让我们在主题的`gatsby-config.js`中添加一些站点信息。

**`/packages/gatsby-theme-tutorial/gatsby-config.js`**T3】

```
module.exports = {
  siteMetadata: {
    title: `Gatsby Theme Tutorial`,
    description: `A tutorial for building a GatsbyJS theme from scratch!`
  },
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

现在，在我们的 Header 组件中，我们需要查询我们的站点标题。我们将使用 Gatsby 提供的`useStaticQuery`钩子来做这件事。然后，我们可以获取这些信息并在组件中使用它们。

**`/packages/gatsby-theme-tutorial/src/components/header.js`**T3】

```
import React from "react";
import { useStaticQuery, graphql, Link } from "gatsby";

import "../styles/header.css";

const Header = () => {
  const data = useStaticQuery(graphql`
    query {
      site {
        siteMetadata {
          title
          description
        }
      }
    }
  `);

  return (
    <div className="header">
      <Link to="/" className="no-style">
        <h1 className="site-heading">{data.site.siteMetadata.title}</h1>
        <h4>{data.site.siteMetadata.description}</h4>
      </Link>
    </div>
  );
};

export default Header; 
```

Enter fullscreen mode Exit fullscreen mode

**`/packages/gatsby-theme-tutorial/src/styles/header.css`**T3】

```
.header {
  margin: -8px;
  padding: 1.5rem;
  width: 100%;
  align-self: center;
  text-align: center;
  background-color: purple;
  font-family: Arial, Helvetica, sans-serif;
  border-bottom: solid black;
}

.site-heading {
  font-size: 3rem;
}

.no-style {
  color: inherit;
  text-decoration: inherit;
} 
```

Enter fullscreen mode Exit fullscreen mode

这太棒了！注意，我们可以从 header 组件中的`gatbsy-config.js`文件访问我们的站点元数据。当我们在站点文件夹中添加和更改这个文件时，真正的奇迹发生了。

**`/site/gatsby-config.js`**T3】

```
module.exports = {
  siteMetadata: {
    title: `New Gatsby Site`,
    description: `A website using our Gatsby theme!`
  },
  __experimentalThemes: ["gatsby-theme-tutorial"]
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在标题将包含在我们的站点文件夹而不是主题文件夹中配置的数据！

现在，让我们将标题添加到布局组件中。

### 将页眉添加到布局中

我们所要做的就是将我们的`header.js`文件导入到我们的`layout.js`组件中，并相应地放置它。

**`/packages/gatsby-theme-tutorial/components/layout.js`**T3】

```
import React from "react";
import "../styles/layout.css";

import Header from "./header";

const Layout = ({ children }) => {
  return (
    <div className="container">
      <Header />
      <main>{children}</main>
    </div>
  );
};

export default Layout; 
```

Enter fullscreen mode Exit fullscreen mode

我们的页眉现在将显示在站点文件夹或主题文件夹中创建的每个页面上。

### 包装完毕

在我们的 Gatsby 主题中构建组件允许我们构建完全样式化的组件，同时让主题的消费者轻松控制内容。

我们只是在构建这个盖茨比主题的表面，但是你已经可以看到完全定制的潜力！

如果有些事情不像你预期的那样工作，请随意将你的代码与教程[这一部分的报告中的代码进行比较。](https://github.com/billyjacoby/gabsty-theme-tutorial/tree/part-two)

如果您有任何问题，或者对未来教程的内容有任何建议，请随时发表评论或发微博给我。

### 接下来是什么

在本教程的第三部分，我们将开始为主题的不同部分(博客、商店等)构建不同的风格...)，以及如何覆盖使用我们主题的站点的样式和整个组件。

当最终用户定制盖茨比主题时，可能性是无穷无尽的。我们还将看到，我们需要多么少的样板代码来构建一个完整的盖茨比主题的网站！