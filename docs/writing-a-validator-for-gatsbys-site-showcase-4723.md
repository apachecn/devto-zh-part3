# 为 Gatsby 的站点橱窗编写一个验证器

> 原文：<https://dev.to/lannonbr/writing-a-validator-for-gatsbys-site-showcase-4723>

在过去的这个周末，我做了一个简单的节点脚本来运行 GatsbyJS 的站点展示，我对最终结果非常满意，并想谈谈整个过程。

# 为什么要这样做？

随着 Gatsby 的不断发展，目前在 showcase 中有超过 450 个站点作为展示使用 Gatsby 的站点的地方，能够维护并偶尔看到这些站点仍然存在并使用 Gatbsy 是一个很好的健全检查。

同样，这可以很好地添加到 CI 流程中，这样当有人向 showcase 提交站点时，检查可以自动进行，而不需要手动检查它是否确实是 Gatsby 站点。

# 深挖代码

完整的源代码位于我的 GitHub 上:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [兰农布尔](https://github.com/lannonbr) / [盖茨比-站点-展示-验证器](https://github.com/lannonbr/gatsby-site-showcase-validator)

### 用于验证 Gatsby Site Showcase 上链接的节点脚本实际上是 Gatsby 站点

<article class="markdown-body entry-content container-lg" itemprop="text">

# Gatsby 站点展示验证程序

**注意**:此回购已经合并到 Gatsby 的核心回购中，在此存档:【https://github.com/gatsbyjs/gatsby/tree/master/. T2】github/actions/盖茨比-site-showcase-validator

一个简单的节点脚本，为 Gatsby 访问并检查[站点 Showcase](https://www.gatsbyjs.org/showcase/) 中的所有站点。

## 说明

1.  克隆存储库
2.  使用 npm 或 yarn 安装依赖项
3.  运行`node index.js`

## 在码头运行

您也可以使用以下命令在 Docker 中运行它:

```
docker run --rm lannonbr/gatsby-site-showcase-validator:1.0.0 
```

</article>

[View on GitHub](https://github.com/lannonbr/gatsby-site-showcase-validator)

开始时，我从 Gatsby 的 repo 中拉出 [sites.yml](https://github.com/gatsbyjs/gatsby/blob/master/docs/sites.yml) 文件，该文件用于构建 showcase，并用 [js-yaml](https://www.npmjs.com/package/js-yaml) 解析它。现在我有了一个站点数组，我遍历每个站点并获取每个站点的 main_url。

然后，当我从一个单独的站点获得 HTML 时，我将它传递给 [cheerio](https://cheerio.js.org/) ，这是一个 NodeJS 的 DOM 解析器，它有一个类似 jQuery 的 API。

```
let $ = cheerio.load(siteHtml); 
```

代码的主要部分是检查它是否有一个 id 为“__gatsby”的元素。这是作为 Gatsby 的一部分构建的默认容器。

```
let gatsbyContainer = $("#___gatsby")

if (gatsbyContainer.length !== 0) {
  // The page is a gatsby site
} else {
  // The page is not a gatsby site, print out an error message
} 
```

除此之外，这个文件总共只有大约 70 行代码，但是总体来说做得相当不错。

# 结果

所以从统计数据来看，只有大约 20 个网站要么没有加载，要么已经脱离了 Gatsby 框架。计算一下，低于 5%是一个很低的百分比，所以它表明网站 showcase 在显示那些声称自己是 Gatsby 网站的网站实际上是有信誉的。

有些情况下，为什么有一些网站返回无效可能是因为该网站曾经是一个盖茨比网站，但因为被重写，然后被忘记从展示中删除。现在有了这样的工具，这个数字可以进一步减少，并保持在相当低的水平。维护一个大型站点展示是一项艰巨的任务，但是通过一点自动化和脚本，这项任务可以变得更加容易。