# 盖茨比和 Netlify CMS:一个完美的组合

> 原文：<https://dev.to/bnevilleoneill/gatsby-netlify-cms-a-perfect-pairing-1f93>

[![](img/84e3656542bdd11e4e4b0834b4b7a15b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UEDaLswF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ar7CGZP27jtJxZDkJ-f0VjA.jpeg)

JAMStack 因其更好的性能、低成本、高安全性和积极的开发者体验而越来越普遍地用于构建网站。

使用 JAMStack 构建站点的一个很好的解决方案是将 Gatsby 与 Netlify CMS 结合使用。这篇文章解释了什么是 JAMStack，Gatsby 和 Netlify CMS 是如何工作的，并提供了一个用 Gatsby 和 Netlify CMS 构建的 web 应用程序的演练。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 什么是 JAMStack？

[JAMStack](https://jamstack.org/) 指的是一个 web 应用程序，它没有服务器端组件，而是依赖于模板化的**标记**文件，客户端 **JavaScript** 通过 **API** 请求这些文件在页面上呈现内容。所有的模板化标记都是在部署之前生成的，因此不需要像动态网站那样等待页面被动态构建。

### 盖茨比和 Netlify CMS

Gatsby 是一个使用 React.js 和 GraphQL 的静态站点生成器。Gatsby 生成了一堆 JavaScript 和 CSS 文件，这些文件以这样的方式分开，即一个页面将加载尽可能少的代码，以保持加载时间最短。GraphQL 用于将标记文件中的数据查询到 React 组件文件中。

[![](img/4feeb00a0dc202e5c371d959c4bd462e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l6cMw0eE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AolZMmCFr1untF4I3.png) 

<figcaption>*盖茨比能够建立一个优化的静态网站。*</figcaption>

Netlify CMS 能够为 Gatsby 网站提供标记数据。Netlify CMS 允许用户通过直观且易于使用的界面输入内容，然后 Gatsby 将使用该界面为 web 应用程序创建适当的页面。在 Netlify CMS 上保存内容时，数据会作为降价文件保存到 web 应用程序的 git 存储库中。

[![](img/a6ab378cd9cac0709046dc0f4902ea7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HgbwuXyt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AF0uMmr7Z4VxoiNq4.png) 

<figcaption>*Netlify CMS 为一家盖茨比网站提供数据。*</figcaption>

在 Gatsby 从其构建过程中生成所有 web 应用程序文件后，web 应用程序可以部署到静态网站主机，如 [Netlify](https://www.netlify.com/) 、 [Now](https://zeit.co/now) 或[亚马逊 S3](https://aws.amazon.com/s3/) 。

[![](img/8d0941b31032292c126d95a1770a648f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a_bOPAig--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/758/0%2AIFjVut6gGbubrogn.png) 

<figcaption>*Netlify CMS 向 Gatsby 提供数据，Gatsby 随后生成一个静态网站，该网站可以部署到 Netlify、Now、亚马逊 S3 或另一个静态 web 主机。*</figcaption>

### 演练

本演练将解释用 Gatsby 和 Netlify CMS 构建的 web 应用程序的主要部分。本演练基于为一个虚构的 JavaScript meetup 组创建的示例 web 应用程序。

[![](img/f9ff88dd74dc615b255f71e74a260ec4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wQY2Jr6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AaOkW1XHCLnZM325p.png) 

<figcaption>*用 Gatsby 和 Netlify CMS 搭建的一个虚构的 JavaScript meetup 网站的登陆页面。*</figcaption>

您可以[在 Github](https://github.com/robertcoopercode/gatsby-netlify-cms) 上查看 web 应用的源代码，并且您可以通过单击自述文件中的“*部署到 netlify* 按钮来轻松部署您自己的应用实例。点击“*部署到 netlify* ”按钮，您将进入 netlify 的网站，网站会要求您连接到您的 Github 帐户，以便它可以克隆存储库，然后部署 web 应用程序的实例。如果您希望自己能够访问和探索 Netlify CMS，我建议您在跟随本演练时使用这个" *Deploy to netlify* "按钮。

[![](img/563becec73ced03747231271b65248a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fpg_cNUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ald01n5KzgjWUIrxP.png) 

<figcaption>“部署到网络”按钮可在示例存储库的自述文件中找到，并允许用户将 web 应用程序快速部署到随机生成的 URL。</figcaption>

这些是快速部署现有 web 应用程序实例的一些步骤。

[![](img/743a7510cbd3fb250c6cc5e5f236ef32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0HKbsJd_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AJZdzImdAkjizGwsn.png)

### 访问 CMS

为了能够在站点的/admin 路径访问 Netlify CMS，我们需要通过 Netlify 的 [*身份*](https://www.netlify.com/docs/identity/) 服务启用 Netlify 的 [*Git 网关*](https://github.com/netlify/git-gateway) 。Git Gateway 允许用户将贡献者添加到 CMS 中，而无需授予他们对代码库的完全访问权限。Netlify 的[身份](https://www.netlify.com/docs/identity/)服务处理所有的认证，并为用户管理提供接口。

接下来的步骤是在 Netlify 仪表板中启用*身份*，邀请自己成为用户，然后启用 *Git 网关*。

请继续并单击 Netlify 仪表板中“启用身份”按钮。

[![](img/c7c82ff8bf07f8c8247cb3bf985ddeab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5u1w69He--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2APvgzxJPswHBhfetk.png)

接下来，单击“邀请用户”按钮，邀请自己成为用户。

[![](img/cc69f634620e20026d532c562f48253d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h6pLW-Iy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AD7hGFIY9q5LaYLLy.png)

确保您“启用 Git 网关”以允许 Netlify 与您的 Github 库接口。

[![](img/98b3fea5566006597043e29f9018d55d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ueVfpmtS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AIMYmB2dPBvmQo9cT.png)

邀请自己并启用 *Git 网关*后，您应该会收到一封电子邮件，邀请您为该应用程序创建一个用户。创建用户后，您应该能够访问 web 应用程序的 Netlify CMS，方法是访问您站点的/admin 路径，然后使用您刚刚注册的电子邮件和密码登录。

如果您试图查找使用 Netlify 部署的 web 应用程序所使用的域，可以在 Netlify 仪表板的概述页面顶部找到。

在我的例子中，我已经在设置中将域定制为 Gatsby-netlify-CMS-example . netlify . CMS。因此，我访问我的应用程序管理的方式是进入[https://gatsby-netlify-cms-example.netlify.com/admin](https://gatsby-netlify-cms-example.netlify.com/admin)。

[![](img/fa47e6e1dc1d7f44c5a67621fff07665.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5n2pzcvA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A6foV2hTU0d2a3hX7.png) 

<figcaption>部署的 web 应用程序的域显示在 Netlify 仪表板的概览部分。</figcaption>

访问 web 应用程序的[https://gatsby-netlify-cms-example.netlify.com/admin](https://gatsby-netlify-cms-example.netlify.com/admin)会提示用户使用用户名和密码登录，如下所示。

[![](img/dcf87235afe2f58fdb81b47cdab0ae9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--04U3byjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AeODgVFPTARh37Jod.png)

### 探索 CMS

在认证进入 Netlify CMS 后，应该会有一个页面在左边显示网站的收藏，在右边显示最近的聚会列表。通过 CMS 输入的所有数据在保存时都将存储到 Git 存储库中。一旦有新的提交到 Git 存储库，Netlify 将触发应用的 Gatsby 构建，然后使用新内容部署应用(这称为[连续部署](https://www.netlify.com/docs/continuous-deployment/))。

[![](img/3e55025031d8400e082a78bd1d761ae5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HGisLVHX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AIgX0GDgQGUkjwNtA.png) 

<figcaption>*用户通过认证后，web app 的网络内容管理系统的布局。*</figcaption>

Netlify CMS 的一个优点是，当你在 CMS 中编辑内容时，你可以实时预览你的页面。当设置 React 代码时，设置预览需要一点思考，我也没有在上传图像后立即显示图像的运气，但考虑到显示静态站点的实时预览所涉及的复杂性，它工作得相当好。

该应用程序已经以这样一种方式编码，所有在网站上显示的内容都可以在 CMS 中配置(甚至是导航栏和页脚内容)！

这里，我们将通过 CMS 添加/编辑一个新的 meetup 条目。

[![](img/9c67bb658731f215134b9b50c1d35c28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1G3naZFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AGH6UgM_0MUF9eGXm.png)

现在，我们可以通过 CMS 编辑页脚。

[![](img/e71d96721ba4f15e731be2171d8380e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FJsCLFJi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A1fem3QG1mwM20Qn7.png)

### **项目结构**

让我们看一下代码。

该项目有两个文件夹:src/和 static/

src/目录包含所有决定应用程序 UI 的 React 组件和 Sass 文件。src/目录还保存了 markdown 文件，这些文件保存了输入到 CMS 的所有文本和数据。当数据保存在 CMS 中时，该数据以降价文件的形式添加到 src/目录中。

静态/目录包含通过 CMS 上传的图像或文件以及 CMS 配置。CMS 配置文件是一个 config.yaml 文件，它决定了可以通过 CMS 界面添加的数据类型。

例如，在 config.yaml 中，您可以指定要定义一个名为“meetup”的集合，并且每个 meetup 都应该有一个标题、日期、演示者列表以及与之关联的位置。阅读文档了解更多关于[配置文件](https://www.netlifycms.org/docs/configuration-options/)的信息。

```
...

collections:
  - name: "meetups"
    label: "Meetups"
    description: "Meetup dates, location, and presenter information."
    folder: "src/pages/meetups"
    create: true
    fields:
      - { label: "Template Key", name: "templateKey", widget: "hidden", default: "meetup" }
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Date", name: "date", widget: "datetime" }
      - {
          label: Presenters,
          name: presenters,
          required: true,
          widget: list,
          fields:
            [
              { label: Name, name: name, required: true, widget: string },
              {
                label: Presentation Title,
                name: presentationTitle,
                required: false,
                widget: string,
              },

              ... 
```

*可添加到 CMS 的数据类型由 config.yaml 文件中的数据配置指定。*

**获取 React 组件中的数据**

Gatsby 被设置为使用 GraphQL 查询 JavaScript 文件中的数据，并转换为 React 组件。当查看 React 页面模板文件时，您将在文件底部看到一个 GraphQL 查询，它查询页面所需的所有数据。

```
...

export const aboutPageQuery = graphql`
  query AboutPage($id: String!) {
    markdownRemark(id: { eq: $id }) {
      html
      frontmatter {
        title
        mainImage {
          image
          imageAlt
        }
        gallery {
          image
          imageAlt
        }
        developerGroups
        organizers {
          title
          gallery {
            image
            imageAlt
            name
          }
        }
        seo {
          browserTitle
          title
          description
        }
      }
    }
    ...LayoutFragment
  }
`; 
```

GraphQL 查询中查询数据的名称基于 config.yml 文件中提供的字段名称。但是，有些字段名称不太直观，如 frontmatter 和 markdownRemark。幸运的是，Gatsby 自带了 GraphiQL，您可以通过 Gatsby 应用程序中的 GraphiQL 查询来浏览所有可用的数据。GraphQL 还可以通过它的“文档浏览器”来发现所有 graph QL 字段的名称。在本地运行 web app(使用 yarn develop)然后访问[http://localhost:8000/_ _ _ graphql](http://localhost:8000/___graphql)即可访问 graph QL。

[![](img/d6bddeab3b3d73d85aa367e921cf9f5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oCrVQdJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhApaWYQmtLmzHJTV.png) 

<figcaption>*GraphiQL 是一个方便的工具，允许用户探索可用的 GraphiQL 查询，并查看运行不同的查询会返回什么数据。*</figcaption>

React 组件文件中使用 GraphQL 查询的数据允许通过传递给文件的导出 React 组件的对象来访问所有查询的数据。

```
...

const AboutPage = ({ data }) => {
  const { markdownRemark: page, footerData, navbarData } = data;
  const {
    frontmatter: {
      seo: { title: seoTitle, description: seoDescription, browserTitle },
    },
  } = page;

  return (
    <Layout footerData={footerData} navbarData={navbarData}>
      <Helmet>
        <meta name="title" content={seoTitle} />
        <meta name="description" content={seoDescription} />
        {browserTitle}
      </Helmet>
      <AboutPageTemplate page={{ ...page, bodyIsMarkdown: false }} />
    </Layout>
  );
};

AboutPage.propTypes = {
  data: PropTypes.object.isRequired,
};

export default AboutPage;

... 
```

*导出的`AboutPage`组件接收 GraphQL 查询提供的数据。*

上面的代码是 About 页面的 React 组件。注意上面 AboutPage 模板的 React 组件如何拥有 AboutPage template 的子组件。AboutPage 组件有一个子组件的原因是为了 AboutPageTemplate 组件可以导出并用于设置在 CMS 中显示的 About 页面的预览。这就是之前说 CMS 预览功能需要在 React 项目的设置过程中进行一些思考时所回避的问题。

### 本地开发和部署

在本地运行 web 应用程序就像安装项目依赖项(yarn)然后运行启动脚本(yarn develop)一样简单。

运行启动脚本可以在 [http://localhost:8000/](http://localhost:8000/) 访问站点。

开发时要记住的一点是，一旦通过 CMS 添加或修改了内容，您将需要在本地运行 git pull 来从 CMS 获取最新的更改。

将任何代码更新部署到动态 web 应用程序就像将新提交推送到远程 git 存储库一样简单，因为 Netlify 会自动构建应用程序(yarn build)并提供更新的代码。

部署时，最好确保构建脚本成功运行，因为如果对 CMS 预期的数据结构进行了一些修改，可能会出现一些错误。如果构建失败，您需要阅读错误消息，了解脚本构建应用程序失败的原因，然后做出相应的调整。

[![](img/ab210129b6dfcf90d47476ab75772577.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q0vMaCMK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AOGXaKCE6uev5j88j.png) 

<figcaption>*在应用程序的 git 存储库的主分支上检测到新的更改后，Netlify 将报告运行构建脚本的状态。*</figcaption>

### 结论

如果您有兴趣了解更多关于 Gatsby 和 Netlify CMS 如何协同工作的信息，您可以探索上述示例应用程序的源代码，参考 [Gatsby](https://www.gatsbyjs.org/docs/) 和 [Netlify CMS](https://www.netlifycms.org/docs/intro/) 文档，以及查看用于 Gatsby 和 Netlify CMS 的[入门模板](https://github.com/netlify-templates/gatsby-starter-netlify-cms)。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子 [Gatsby & Netlify CMS:一个完美的组合](https://blog.logrocket.com/gatsby-netlify-cms-a-perfect-pairing-d50d59d16f67/)首先出现在[博客](https://blog.logrocket.com)上。