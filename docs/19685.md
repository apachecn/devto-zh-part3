# 从一个无头 CMS 获取数据到一个静态站点生成器！

> 原文：<https://dev.to/spences10/get-your-graphcms-data-into-gatsby-3jkl>

###### edu·劳顿在 Unsplash 上拍摄的照片

### 把你的 GraphCMS 数据导入 Gatsby

让我们设置 Gatsby 从 GraphCMS 中提取数据。

[https://www.youtube.com/embed/S9JeASI5tck](https://www.youtube.com/embed/S9JeASI5tck)

这将是在 headless CMS、GraphCMS 上设置一些基本数据，然后在 Gatsby 中查询这些数据的演练。

### 1。设置 GraphCMS

在[https://app.graphcms.com/signup](https://app.graphcms.com/signup)为自己设置一个 GraphCMS 账户，并选择开发者计划。

### 2。定义数据

创建一个新项目，并添加一些数据进行查询。

选择**创建新项目**选项，您可以随意命名，在本例中，它将是一个项目列表，因此我将其命名为*项目列表*。

在侧边栏中选择模式并创建一个模型，在本例中是**项目**。在项目模型中，我们将有一个*标题*和一个*描述*。

通过点击**字段**选项卡，从右边的托盘中选择字段，并将它们拖放到我们创建的**项目**模型中。

### 3。配置 GraphCMS 公共 API

在 GraphCMS 设置中，将**公共 API 权限**设置为**读取**向下滚动到**端点**并复制 URL 用于配置 Gatsby。

这就是 CMS 配置，现在把数据拉进我们的 Gatsby 前端！

### 4。配置盖茨比

在您的 Gatsby 项目中安装`gatsby-source-graphql`并在`gatsby-config.js`中配置它，配置应该类似于:

```
{
  resolve: 'gatsby-source-graphql',
  options: {
    typeName: 'GRAPHCMS',
    fieldName: 'graphCmsData',
    url: 'https://api-euwest.graphcms.com/v1/projectid/master',
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用 [codesandbox.io](https://codesandbox.io/dashboard/recent) 作为我们的文本编辑器，以及从 [codesandbox.io](https://codesandbox.io/dashboard/recent) 中可用的服务器模板中选择 Gatsby 时得到的 Gatsby 默认启动器

### 5。查询 Gatsby GraphiQL 中的数据

现在端点已经设置好了，我们将能够使用 Gatsby 的 GraphiQL UI 查询数据，我们可以设计我们想要用来在这里显示数据的查询。

在 [codesandbox.io](https://codesandbox.io/dashboard/recent) 中预览我们的应用程序时，如果你在 url 的末尾添加`___graphql`,就会弹出 Gatsby GraphiQL UI，在这里我们可以设定我们想要查询的数据。

如果我们打开一些花括号`{}`和 Cmd+空格，我们会看到可用的字段，我们可以从中挑选出在`gatsby-source-graphql`插件中定义的`graphCmsData`字段。

选择我们在 GraphCMS 中创建的字段，然后运行查询将显示我们定义的数据。

```
{
  graphCmsData {
    projects {
      id
      status
      title
      description
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 6。显示数据

使用`graphql` gatsby 导出从 GraphCMS 端点查询数据。

在`pages/index.js`中添加`graphql`出口到`gatsby`进口。

```
import { graphql, Link } from 'gatsby' 
```

Enter fullscreen mode Exit fullscreen mode

在最底部定义查询:

```
export const query = graphql`
  {
    graphCmsData {
      projects {
        id
        status
        title
        description
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

然后，您将能够访问`IndexPage`组件中的`data`属性，我们需要在组件的参数:
中取消`data`属性的结构

```
const IndexPage = ({ data }) => ( 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以访问传入组件的`data`，我们只需要一个可视化它的方法！幸运的是，Wes Bos 中有一个我们可以使用的方便的组件，叫做[转储](https://github.com/wesbos/dump)，所以在`components`中创建一个新的`dump.js`组件，然后将其导入到`index.js`文件中，并添加该组件以查看道具中有什么:

```
const IndexPage = ({ data }) => (
  <Layout>
    <h1>Hi people</h1>
    <Dump data={data} />
    <p>Welcome to your new Gatsby site.</p>
    <p>Now go build something great.</p>
    <div style={{ maxWidth: '300px', marginBottom: '1.45rem' }}>
      <Image />
    </div>
    <Link to="/page-2/">Go to page 2</Link>
  </Layout> ) 
```

Enter fullscreen mode Exit fullscreen mode

输出应该与我们创建的 Gatsby GraphiQL 查询的结果相同:

```
data  💩{  "graphCmsData":  {  "projects":  [  {  "id":  "cjoxa812txqoh0932hz0bs345",  "status":  "PUBLISHED",  "title":  "Project 1",  "description":  "Description 1"  },  {  "id":  "cjoxa8cctxqqj0932710u39db",  "status":  "PUBLISHED",  "title":  "Project 2",  "description":  "Description 2"  },  {  "id":  "cjoxa8pbqxqt309324z9bcddv",  "status":  "PUBLISHED",  "title":  "Project 3",  "description":  "Description 3"  },  {  "id":  "cjoxa959vxqvz0932g1jn5ss3",  "status":  "PUBLISHED",  "title":  "Project 4",  "description":  "Description 4"  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 感谢阅读🙏

如果我错过了什么，或者如果有更好的方法做某事，请让我知道。

这篇文章最初发布在[我的盖茨比博客](https://blog.scottspence.me)上，你可以在这里[查看它](https://blog.scottspence.me/get-graphcms-data-into-gatsby)如果你喜欢这篇文章，请看看我的其他内容。

在 Twitter 上关注我，或者在 GitHub 上关注 T2 问我任何问题。