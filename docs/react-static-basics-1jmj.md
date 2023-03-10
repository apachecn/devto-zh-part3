# 反应静态基础

> 原文：<https://dev.to/robp773/react-static-basics-1jmj>

因为我是 React 的忠实粉丝，所以我一直在寻找新的基于 React 的工具来添加到我的工具包中。然而，React 生态系统中有一个领域我一直在回避…静态站点生成器。不久前，我查看了 Gatsby 的文档，看看它是否适合我的自由职业项目用例。在浏览了所有我需要学习的新概念后，我意识到这对我来说可能是多余的，于是我选择了一个基本的 WordPress 网站并继续前进。令人欣慰的是，我最近通过 React Static 重新接触了静态站点生成器，并且对它的易用性印象深刻。更不用说有多快了。

## 什么是 React Static？

根据其 GitHub repo，React Static 是一个渐进式静态站点生成器，专注于性能、灵活性和开发人员体验。它的许多功能都是为了解决其他静态站点生成器(如 Gatsby 和 Next.js)的缺点而构建的。由于我从未使用过这两种生成器，所以我无法详细介绍所有这些生成器的不同之处，但在较高的层面上，React Static 实现了 Next.js 的易用性，同时(在某种程度上)接近于 Gatsby 的性能。React Static 确保创建静态站点不会变得乏味或过于复杂，并始终保持一种创建-反应-应用程序的感觉。这些领域是 React Static 区别于竞争对手的地方。

## 入门

React Static 的开发者体验是它的亮点之一。如果你以前使用过“创建-反应-应用”，你会喜欢他们的“反应-静态创建”，这将为你设置一个零配置的模板。运行这些命令开始。

`npm i -g react-static`
T1】

运行这些命令后，您可以从许多不同的初学者模板中进行选择。对于本文，我们将使用基本模板。为您创建模板后，打开新目录并打开 static.config.js。

## 导入数据

总的来说，这个文件的工作是将我们项目的路线与一个页面和该页面所需的数据相匹配。通读注释代码，了解发生了什么。

```
//static.config.js
export default {

  // resolves an array of route objects 
  getRoutes: async () => {

    // this is where you can make requests for data that will be needed for all
    // routes or multiple routes - values returned can then be reused in route objects below

    // starter template has a request to an endpoint that retrieves an array of fake blog posts
    const { data: posts } = await axios.get(
      "https://jsonplaceholder.typicode.com/posts"
    );

    return [
      // route object
      {
        // React Static looks for files in src/pages (see plugins below) and matches them to path
        path: "/blog",
        // function that returns data for this specific route
        getData: () => ({
          posts
        }),
        // an array of children routes
        // in this case we are mapping through the blog posts from the post variable above
        // and setting a custom route for each one based off their post id
        children: posts.map(post => ({
          path: `/post/${post.id}`,
          // location of template for child route
          template: "src/containers/Post",
          // passing the individual post data needed
          getData: () => ({
            post
          })
        }))
      },
    ];
  },
  // basic template default plugins
  plugins: [
    [
      require.resolve("react-static-plugin-source-filesystem"),
      {
        location: path.resolve("./src/pages")
      }
    ],
    require.resolve("react-static-plugin-reach-router"),
    require.resolve("react-static-plugin-sitemap")
  ]
}; 
```

然后，您可以像这样在博客页面中访问这些数据。

```
// src/pages/blog
import React from 'react'
// provides the route's data
import { useRouteData } from 'react-static'
import { Link } from 'components/Router'

// making a ul of links to all of the individual blog posts
export default function Blog() {
  const { posts } = useRouteData()
  return ( 
    <div>
      <h1>It's blog time.</h1>
      <div>
        <a href="#bottom" id="top">
          Scroll to bottom!
        </a>
      </div>
      <br />
      All Posts:
      <ul>
        {posts.map(post => (
          <li key={post.id}>
            {/* linking to individual post routes set by children property of /blog route */}
            <Link to={`/blog/post/${post.id}/`}>{post.title}</Link>
          </li>
        ))}
      </ul>
      <a href="#top" id="bottom">
        Scroll to top!
      </a>
    </div>
  )
} 
```

这就是它的全部——你引入数据，把它输入到你的路线中，然后按照你想要的方式使用这些数据。

## CMS 集成

在某些时候，您可能希望将 React 静态站点连接到 CMS。由于 React Static 关注的是开发人员的体验，我们不妨使用一个采用相同方法的 CMS—[Netlify CMS。](https://www.netlifycms.org)Netlify CMS 是基于 git 的，因此每当您的 CMS 更新时，更新的内容都会被推送到您的静态站点的 GitHub repo，这又会触发 Netlify 重建站点(更新的数据会通过 static.config.js 添加到适当的路由中)。

要设置这个，将您的静态站点 repo 推送到 GitHub，并通过单击 Netlify 仪表板内的“从 Git 新建站点”按钮将其连接到 Netlify。

输入“npm run build”作为构建命令，输入“dist”作为根目录。

然后转到“身份”选项卡，选择“启用身份服务”。

在注册首选项下，选择仅邀请。

为了方便起见，我建议在“外部提供商”下选择一个提供商。我用谷歌来做这个教程。

选择页面顶部的身份选项卡，然后单击邀请用户–邀请您的个人 gmail 帐户以授予您访问权限。通过您的电子邮件完成注册过程。

向下滚动到服务> Git 网关，然后单击启用 Git 网关。

现在回到你的项目目录的根目录，找到你的公共文件夹——里面应该已经有 robots.txt 了。在这里添加一个管理和上传文件夹。在新创建的管理文件夹中创建两个新文件——config . yml 和 index.html。

将以下代码粘贴到 config.yml 中。这将设置一个标记为 Test Collection 的集合的结构。

```
backend:
  name: git-gateway
  branch: master # Branch to update (optional; defaults to master)

media_folder: "public/uploads" # Media files will be stored in the repo under public/uploads
public_folder: "/uploads" # Folder path where uploaded files will be accessed, relative to the base of the built site

collections:
  - name: "test-collection" # Used in routes, e.g., /admin/collections/test-collection
    label: "Test  Collection" # Used in the UI
    folder: "src/test-collection" # The path to the folder where the documents are stored
    create: true # Allow users to create new documents in this collection
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}" # Filename template, e.g., YYYY-MM-DD-title.md
    fields: # The fields for each document, usually in front matter. 
    # Remove any that aren't needed for posts
      - {label: "Layout", name: "layout", widget: "hidden", default: "blog"}
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Publish  Date", name: "date", widget: "datetime"}
      - {label: "Body", name: "body", widget: "markdown"} 
```

然后将这段代码粘贴到 index.html 文件中。这将在站点的/admin 路径下设置管理员登录模式+ CMS。

```
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  Content Manager
  <script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
</head>
<body>
  <!-- Include the script that builds the page and powers Netlify CMS -->
  <script src="https://unpkg.com/netlify-cms@^2.0.0/dist/netlify-cms.js"></script>
  <script>
    if (window.netlifyIdentity) {
      window.netlifyIdentity.on("init", user => {
        if (!user) {
          window.netlifyIdentity.on("login", () => {
            document.location.href = "/admin/";
          });
        }
      });
    }
  </script>
</body>
</html> 
```

最后但同样重要的是，导航回 static.config.js 并添加以下代码。一旦我们将一个 post 添加到我们的“测试集合”集合中，这个函数将从我们的 repo 中读取它，并使它对我们的 route 对象可用。您需要运行`npm install fs klaw gray-matter`来下载依赖项。

```
//static.config.js

//...other imports

//for reading local files
import fs  from "fs";
import klaw from "klaw";
import matter from "gray-matter";

function getPosts() {
  const items = [];
  // Walk ("klaw") through posts directory and push file paths into items array //
  const getFiles = () =>
    new Promise(resolve => {
      // Check if test-collect directory exists //
      // This is the folder where your CMS collection we made earlier will store it's content. Creating a post inside this collection will add a "test-collection" directory to your repo for you.
      if (fs.existsSync("./src/test-collection")) {
        klaw("./src/test-collection")
          .on("data", item => {
            // Filter function to retrieve .md files //
            if (path.extname(item.path) === ".md") {
              // If markdown file, read contents //
              const data = fs.readFileSync(item.path, "utf8");
              // Convert to frontmatter object and markdown content //
              const dataObj = matter(data);
              // Create slug for URL //
              dataObj.data.slug = dataObj.data.title
                .toLowerCase()
                .replace(/ /g, "-")
                .replace(/[^\w-]+/g, "");
              // Remove unused key //
              delete dataObj.orig;
              // Push object into items array //
              items.push(dataObj);
            }
          })
          .on("error", e => {
            console.log(e);
          })
          .on("end", () => {
            // Resolve promise for async getRoutes request //
            // posts = items for below routes //
            resolve(items);
          });
      } else {
        // If src/posts directory doesn't exist, return items as empty array //
        resolve(items);
      }
    });
  return getFiles(); 
} 
```

在 getRoutes 中添加`const test = await getPosts()`,紧挨着我们发出博文请求的地方。这个测试变量现在将保存我们在集合中创建的任何内容的值。

创建一个新的路由对象，并将其添加到路由数组中，如下所示...

```
// add below /blog route object
      {
        path: "/test",
        getData: () => ({
          test
        }),
        children: test.map(post => ({
          // actual path will be /test/"whatever the post slug is"
          path: `/${post.data.slug}`,
          // location of template for child route
          template: "src/containers/Test-Post",
          // passing the individual post data needed
          getData: () => ({
            post
          })
        }))
      } 
```

此时，我们只需要一个页面来显示我们的内容，以确保一切正常。要快速完成这项工作，只需将 test.js 文件添加到 src/pages 中，然后粘贴这段代码。我们正在创建一个链接列表，这些链接指向我们发布的每个测试集。

```
// src/pages/test.js

import React from "react";
import { useRouteData } from "react-static";
import { Link } from 'components/Router'

export default function Test() {
  const { test } = useRouteData();
  return (
    <ul>
      {test.map((item, index)=>{
          return (
              <li key={index}>   
                <Link to={`/test/${item.data.slug}`}>{item.data.title}</Link>
              </li>
          )
      })}
    </ul>
  );
} 
```

然后通过在 containers 文件夹中创建 Test-Post.js 来添加新的帖子模板。

```
// src/containers/Test-Post.js

import React from 'react'
import { useRouteData } from 'react-static'
import { Link } from 'components/Router'
// very similar to Post.js component
export default function Post() {
  // get the post data
  const { post } = useRouteData()
  return (
    <div>
      <Link to="/test/">{'<'} Back</Link>
      <br />
      {/* print out what we want to display */}
      <h3>{post.data.title}</h3>
      <div>{post.content}</div>
    </div>
  )
} 
```

为了方便起见，在 src/App.js 的新页面中添加一个导航链接`<Link to="/test">Test</Link>`

将您的更改推送到 GitHub repo 后，Netlify 现在将重建包含 Netlify CMS 的站点。打开您的实时 Netlify url，在浏览器中将/admin 添加到您站点的根 url，以登录 Netlify CMS。一旦你进去了，写下一些内容并发布到“测试集”里面。Netlify 将再次用您添加的新内容重建您的网站。当重新构建完成时，之前创建的 test.js 页面应该会显示到您的帖子的所有链接。

这就是全部了。现在，您拥有了一个连接到现代 CMS 的闪电般快速的静态站点，它具有完全自动化的部署流程。

[最终回购](https://github.com/Robp773/article-test)

**注意**–如果您在使用 Netlify CMS 时在开发人员控制台中看到两个 404 错误，您可以放心地忽略它们。我与一些 Netlify 社区成员确认了这些是预期的错误，并且是在您不使用 Git LFS / Netlify 大型媒体设置时导致的。这一期实际上被标记为好的第一期，如果有人感兴趣的话-【https://github.com/netlify/netlify-cms/issues/2158T2】