# 使用 Gatsby 和 GraphQL 重写静态网站-第 3 部分

> 原文：<https://dev.to/laurieontech/rewriting-a-static-website-using-gatsby-and-graphql---part-3-4ho9>

**最初发布在[十里广场的博客上。](https://tenmilesquare.com/blog/)T3】**

如果在我使用 [GatsbyJS](http://gatsbyjs.com) 和 [GraphQL](http://graphql.com) 重建我的个人[网站](http://laurieontech.com)时，你一直在关注这一系列帖子，请继续关注。如果没有，我推荐回读第一个和第二个部分[。至此，我已经从静态 Yaml 文件中迁移了所有数据，使用 GraphQL 查询了数据，并使用 Gatsby 和 JXS 渲染了页面。我已经删除了我在 Jekyll 代码中使用的液体模板语言的所有引用，网站处于正常工作状态。现在我将注意力转向图像。](https://tenmilesquare.com/rewriting-a-static-website-using-gatsby-and-graphql/)

### 图像处理

我的网站实际上在一个干净的设计中使用了很多图片。我使用的大多数邮件头中都有一张图片，我的简历中也有一张图片，我的每一次演讲都有图片。那么从哪里开始呢？

让我们从我的简历照片开始吧。它是我的登录页面主体中的一次性图像，如下所示。

[![](img/e57ce074836e07ac77cd3c920cec552a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L96AA4dV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tenmilesquare.com/wp-content/uploads/2019/02/Screen-Shot-2019-02-15-at-3.20.35-PM-1024x817.png)

有一种简单的方法来处理这个图像。我可以导入图像文件，并在主页的 JSX 代码中直接引用它。大概是这样的:

```
import headshot from '../assets/headers/headshot.jpg'

<img className="headshot" src={headshot}/> 
```

Enter fullscreen mode Exit fullscreen mode

headshot 类处理图像漂亮的圆形显示以及它在页面上的居中对齐。看起来棒极了！然而，它并没有被优化。因为优化是使用 Gatsby 的主要好处之一，所以让我们看看如何做到这一点。在这个过程中，我将处理一个稍微复杂一点的用例。

### 盖茨比形象

Gatsby 中的图像优化是由一个名为 `gatsby-image` 的插件提供的，它的性能令人难以置信。为了利用它，我将从使用 npm 安装该插件及其相关依赖项开始。

```
npm install gatsby-image gatsby-transformer-sharp gatsby-plugin-sharp 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我想将新安装的插件添加到我的 gatsby-config.js 文件中。我们的配置文件最终看起来像这样(为了简单起见，我们已经在使用的其他插件已经从这个代码片段中删除了)。请注意，一旦安装了 `gatsby-image` ,它就不需要包含在 gatsby-config.js 文件中。

```
plugins:[
    `gatsby-transformer-sharp`,
    `gatsby-plugin-sharp`
] 
```

Enter fullscreen mode Exit fullscreen mode

### Yaml 中的图像

现在我们开始处理更复杂的用例，我的演讲页面。在我的 Jekyll 站点实现中，我的每个演讲约定都有一个相关的图像，如下所示。

[![](img/700bc19811a7e0e556189d9e5548da1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RAWyqDlI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tenmilesquare.com/wp-content/uploads/2019/02/Screen-Shot-2019-02-15-at-3.50.22-PM-1024x436.png)

这些图像文件都存储在标记为 speaking 的文件夹中。为我的演讲页面定义数据的 yaml 文件引用了每个图像的文件名。这样，当我在每个演讲约定中循环时，文件名前面会加上演讲文件夹的路径，页面会呈现图像。

那么在《盖茨比》中我是如何做到这一点的呢？我将使用 GraphQL 来查询图像。现在，图像文件名与每个演讲约定的数据一起被引用。因此，要实现这一点，需要正确查询图像，并确保引用的数据与路径正确关联，以便可以找到并处理文件本身。

实际上，我将首先解决第二个问题。老实说，弄清楚这一点是一个奇怪的挑剔的过程。事实证明这是一堆不同事情的组合，但我会尝试用我找到的解决方案来完成它。

请记住，从关于这个主题的第一篇博文开始，Gatsby 可以看到的内容范围是由` Gatsby-source-file system`插件定义的。在我的例子中，它被定义为公开 src/data 。所以我会先把我的演讲文件夹放在这个范围内，里面装满了我演讲的所有照片。

[![](img/62035f0b82bed11bc3c0565ed053b1f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dZ23x9gI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tenmilesquare.com/wp-content/uploads/2019/01/Screen-Shot-2019-01-08-at-12.29.58-PM.png)

在那里，我需要确保在 speaking.yaml 中定义的文件名与适当的路径匹配，以便 GraphQL 可以找到图像文件。为了实现这一点，我实际上稍微修改了我的 yaml 文件中的数据。我没有只引用文件名，而是放了一个相对路径。图像的路径是相对于 speaking.yaml 文件的位置的(不是定义的文件源路径，这个让我犯了个错误)。

```
image: speaking/kcdc.jpg 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以把注意力转向 GraphQL 了。现在，图像只是一个字符串。我可以这样查询。` ` graph QL { allSpeakingYaml(sort:{ fields:[index]，order:desc }){ edges { node { conference year URL date image } } } ` `

但是，以上并没有做到我想要的。返回相对路径的字符串，例如 "speaking/kcdc.jpg" 。然而，我真的很喜欢我可以查询图像作为说话数据本身的一部分。我想保持这种行为。事实证明，我可以。

我可以在查询中使用 gatsby-image 特性。当查询运行时，相对路径将指向图像文件的位置，结果查询将该文件作为图像进行处理以供显示。

```
{  allSpeakingYaml  (sort:  {fields:  [index],  order:  DESC})  {  edges  {  node  {  conference  year  url  date  image  {  childImageSharp  {  fluid  {  ...GatsbyImageSharpFluid  }  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我用 JSX 地图遍历我的语音数据时，这些对象中的每一个都有一个图像，而不是一个字符串。所以我想用 JSX 来获取这些图像。因为事实证明` g atsby-image` 有我可以使用的自己的标签，所以我将导入它。

```
import Img from "gatsby-image"; 
```

Enter fullscreen mode Exit fullscreen mode

我的第一直觉是写这样的东西。

```
<Img className="selfie" fluid={node.image} alt={node.conference}/> 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这不起作用。在图像应该在的地方呈现了一个图标的页面。出于某种原因，我花了一分多钟才破解，但答案相对简单。

在我们的许多 GraphQL 查询中，查询的结构基于 yaml 数据的结构。所以返回的对象结构看起来与 yaml 文件大致相同。当我们添加节点和边缘对象来访问 yaml 结果的第一层时，我们看到了该规则的一个例外。这是一回事，我只是没注意到而已。实际处理的图像在...盖茨比 imagesharpfuild级别。我用 node.image 访问的不是经过处理的图像。所以最终成功的代码是

```
<Img className="selfie" fluid={node.image.childImageSharp.fluid}
alt={node.conference}/> 
```

Enter fullscreen mode Exit fullscreen mode

### 单一图像查询

现在我想回过头来优化“简单”的用例。首先要做的是删除文件的导入，并将其设置为 GraphQL 查询，该查询通过 gatsby-image 处理运行。这看起来很像我为一系列说话的图像所做的。

```
export const query = graphql`
  query {
   <strong> file(relativePath: { eq: "headers/headshot.jpg" }) {
      childImageSharp {
        <strong> fixed(width: 125, height: 125) {
          ...GatsbyImageSharpFixed
        }
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件事需要注意。根据我之前的代码，我希望我需要的相对路径是代码所在文件的相对路径，在本例中是 index.js。相对路径实际上是基于我们放在` Gatsby-source-file system`配置中的代码行，它指向 src/data 。我花了一点时间才意识到。

查询中需要注意的另一点是，我们使用的是 GatsbyImageSharpFixed 而不是 fluid。老实说，这也应该是一个流体图像，在我的最终网站将是这样。然而，为了测试图像插件提供的所有特性，我想两个都试试。在《T2》中,《盖茨比形象》,流动的图像是指那些不依赖于屏幕而具有有限尺寸的图像，而其他图像是固定的。

在弄清楚所有这些小特质之后，我终于可以用 JSX 展示这张图片了。这和我为了显示我的演讲数据所做的差不多。唯一的区别是，我选择将图像处理为固定的，而不是流动的，所以我需要这样引用它。

```
<Img className="headshot" fixed={data.file.childImageSharp.fixed}
alt="headshot"/> 
```

Enter fullscreen mode Exit fullscreen mode

### 长宽比

这是快速切入正题的好时机。在尝试设计我的图片的过程中，我注意到了惊人的复杂性。事实证明，每个图像都有一些隐含的样式，这些样式是由处理器放在它周围的包装器提供的。这打乱了我尝试使用的所有 CSS。这可能是另一个完整的帖子，但我发现了一个巧妙的技巧，作为我永不停歇地搜索答案的一部分。该插件支持你可以设置长宽比的尺寸。这可以用于固定或流体处理的图像，没关系。

```
<Img sizes={{...data.banner.childImageSharp.fluid, aspectRatio: 21/9}}/> 
```

Enter fullscreen mode Exit fullscreen mode

### 静态查询

我想做的下一件事是处理我的标题图像。在我之前的站点中，我有一个 Header.js 文件，它包含在我的布局中，并呈现在我的所有页面上。所以我想在这里有同样的可重用组件。我将开始使用相同的代码，我用来渲染我的头像以上。嗯，那没用。事实证明，这是因为 GraphQL 在非页面组件上的功能受到限制。

解决这个问题的方法是使用静态查询。我需要做的第一件事是改变我的 Header.js 组件的结构。

```
export default () => (
 <StaticQuery 
    query={graphql`
    query {
      file(relativePath: { eq: "headers/default.jpg" }) {
        childImageSharp {
          fixed(width: 125, height: 125) {
            ...GatsbyImageSharpFixed
          }
        }
      }
    }
  `}
    render={data => (
      <section id="header">
         <h2>LAURIE BARTH</h2>
         <Img fixed={data.file.childImageSharp.fixed} />
      </section>
    )}
  />
) 
```

Enter fullscreen mode Exit fullscreen mode

我不得不在 JXS 代码中直接使用静态查询，然后引用它，而不是查询常数和引用结果的数据。注意，查询语言没有改变， Img 标记语法也没有改变，唯一的变化是查询的位置和包装它的 StaticQuery 标记的用法。

### 多重查询和别名

我需要弄清楚的最后一个用例是如何处理在同一个文件/页面中有多个查询的情况。在最终的网站中，我可能需要也可能不需要，但这是一个值得的练习。

在这种情况下，我想查询我的 speaking.yaml 文件中的所有数据，并想分别查询我的头像。这个问题的答案是使用别名，但我发现大多数关于这个主题的文章解释了这个概念，但忽略了一些问题。首先要知道的是，别名是为查询指定一个名称。下面是一个简单的例子。

```
talks:  allSpeakingYaml(sort:  {fields:  [index],  order:  DESC})  {  edges  {  node  {  conference  year  url  date  image  {  childImageSharp  {  fluid  {  ...GatsbyImageSharpFluid  }  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当你这样做时，你已经在你的 JXS 中改变了对该对象的引用。虽然它以前被称为

```
{data.allSpeakingYaml.edges.map(({ node }) => ()) 
```

Enter fullscreen mode Exit fullscreen mode

给它一个别名并不会增加响应对象的复杂性，它只是替换了它。所以你最终得到的结构和

```
{data.talks.edges.map(({ node }) => ()) 
```

Enter fullscreen mode Exit fullscreen mode

数据的顶级对象名是隐式的。这很重要，因为当我向其中添加多个查询时，我仍然只传递数据对象

```
const SpeakingPage = ({ data}) => {} 
```

Enter fullscreen mode Exit fullscreen mode

其他所有内容都是从顶级返回名引用的。

有了这样的理解，我就可以组合两个查询并使用别名来区分它们。

```
{  allSpeakingYaml  (sort:  {fields:  [index],  order:  DESC})  {  edges  {  node  {  conference  year  url  date  location  image  {  childImageSharp  {  fluid  {  ...GatsbyImageSharpFluid  }  }  }  talks  {  title  video  }  }  }  }  banner:  file(relativePath:  {  eq:  "headers/default.jpg"  })  {  childImageSharp  {  fluid  {  ...GatsbyImageSharpFluid  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我决定不需要为第一个查询起别名。这是允许的；不要求所有的查询都使用别名。所以我引用语音数据数组的方式和以前一样。

```
{data.allSpeakingYaml.edges.map(({ node }) => ()) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我使用我的别名 banner 访问我的图像。

```
<Img fluid={data.banner.childImageSharp.fluid} /> 
```

Enter fullscreen mode Exit fullscreen mode

### 结束了

原来如此。我现在已经优化了我所有的图片。这篇文章包括了许多不同的可能用例，所以不要觉得你需要探索它们。选择适用于您的实施的示例和提示。

在我的情况下，我的网站现在应该以更高的分数通过了 Lighthouse 的审核，这个博客系列到此结束。希望这些帖子对那些和我遇到同样小问题的人有所帮助。我的下一个挑战是去掉我使用的起始模板，做一个更具响应性的设计。所以下次见！

### 额外误差

当我回去把我的图像从固定的改成流动的时，我收到了一个错误。

[![](img/84ed61e72f44a849e8fc9b7f8a0ee0c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lxN45CBL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tenmilesquare.com/wp-content/uploads/2019/02/Screen-Shot-2019-02-18-at-11.46.03-AM-1024x212.png)

尽管看起来如此，解决这个问题实际上并不需要刷新任何类型的缓存。实际上，它与不兼容的引用有关。我触发它是因为我已经改变了我的查询，将图像处理为流体，但是 JSX 键仍然设置为固定。