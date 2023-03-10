# Gridsome v0.5

> 原文：<https://dev.to/gridsome/gridsome-v05-1i61>

Gridsome 是一个 Vue.js 驱动的现代站点生成器[Gridsome.org](https://gridsome.org)。

我们真的很兴奋能发布 Gridsome 0.5。迄今为止最大的更新。它有许多重要的特性，使得数据处理更加容易和灵活。它打开了一个全新的世界，你可以用 Gridsome 来构建它。轻松构建任何数据的分类页面和连接。

## 过滤 GraphQL 数据

GraphQL 模式中的每个内容类型集合都有一个新的`filter`参数，可以用来过滤结果。您可以通过`id`、`title`、`slug`、`path`或任何自定义字段进行过滤。

阅读关于[过滤数据](http://gridsome.org/docs/filtering-data)的更多信息

## 创建分类页面

GraphQL 模式中的所有引用都是单向关系。这个版本为所有内容类型引入了一个名为`belongsTo`的新字段。该字段将列出引用当前节点的所有节点，这对于创建分类页面等非常有用。该字段也可以接受与集合相同的参数。并且可以使用`@paginate`指令自动对结果进行分页。

例如，这可用于:

*   为作者列出博客文章。
*   列出标签或类别的文章。

我们在 **Gridsome 博客**上为**作者**使用这个功能。这里的作者只是一个列出作者的 YAML 文件，然后 Gridsome 神奇地将它们连接起来。

阅读更多关于[分类的信息](http://gridsome.org/docs/taxonomies)

## 页面中的变量查询

以前，模板中只有`$path`和`$page`可以作为`page-query`的变量。但是您现在可以使用`$id`、`$title`、`$slug`、`$date`或当前`node`中的任何自定义字段。深层对象和数组也可以用作变量。

*   `$id`解析为`node.id`
*   `$value`解析为`node.fields.value`
*   `$object__value`解析为`node.fields.object.value`
*   `$array__3__id`解析为`node.fields.array[3].id`

阅读有关模板中[查询数据的更多信息](http://gridsome.org/docs/querying-data#query-data-in-templates)

## 环境变量

有时，您需要与生产版本中不同的配置值。Gridsome 现在利用 [dotenv](https://www.npmjs.com/package/dotenv) 包来简化这个过程。特别感谢 [isoppp](https://github.com/isoppp) 实现了这个功能😄

阅读关于[环境变量](http://gridsome.org/docs/environment-variables)的更多信息

## 新增插件库

[插件](/plugins)页面现在可以从 NPM 目录中获得插件。任何带有关键字`gridsome-plugin`的插件都会在这里列出。您也可以使用搜索栏进行过滤。

[![Plugin page](img/9503c994a385141379b05db7a669f33b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--US2FT8pd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://gridsome.org/assets/static/plugins.cbab2cf.e38d753.png)

## 其他功能和修复

*   更新为 [Vue 2.6](https://medium.com/the-vue-point/vue-2-6-released-66aa6c8e785e) 。
*   在任何节点字段中创建引用。[阅读更多](http://gridsome.org/docs/data-store-api#storecreatereferencetypename-id)。
*   用于配置开发服务器的挂钩。[阅读更多](http://gridsome.org/docs/server-api#apiconfigureserverfn)。
*   自定义默认`index.html`模板(通过 [K3TH3R](https://github.com/K3TH3R) )。
*   添加自定义 CSS 加载器选项(由 [maoberlehner](https://github.com/maoberlehner) )。

## 突发变化😅

没有突破性的 API 更改，但有些更改可能会带来一些后果:

*   外部图像 URL 先前已被转换为对象。这要求您在一个`imageField.src`属性中获得实际的 URL。但是这些字段不再转换为对象。
*   要解析和处理的前端 matter 或 markdown 中的文件路径必须以`./`或`../`开始。这意味着`image.png`不会被处理，但是`./image.png`会被处理。

## 接下来是什么

*   下载和处理外部图像。
*   页面过渡。
*   更平滑的图像加载`<g-image>`(淡入)。
*   GraphQL 模式拼接。
*   完全 PWA 支持。
*   性能优化。
*   将[gridsome.org](https://gridsome.org)存储库移动到核心存储库中，以便于贡献。
*   创建一个入门库，任何人都可以在其中提交入门。
*   文档文档文档！

感谢迄今为止对文档、特性、错误修复和插件的所有贡献！

[mouafa](https://github.com/mouafa) 、[【ekereanto】](https://github.com/ekoeryanto)、[【stefanverna】](https://github.com/stefanoverna)、[【jankal】](https://github.com/jankal)、[【valentine vie】T9】、](https://github.com/valentinvieriu)[【Andrew HL】](https://github.com/andrewhl) [simonswiss](https://github.com/simonswiss) 、[jimyat](https://github.com/JimmyAppelt)、[、【html byjoe】](https://github.com/HTMLbyJoe)、[【seahken】](https://github.com/seahken)、[【stur sby】](https://github.com/stursby)、[【kimihito】](https://github.com/kimihito) [EOS power](https://github.com/eostrom)、[【tonyspiro】](https://github.com/tonyspiro)、[【qoyyuum】](https://github.com/Qoyyuum)、[【tanc】](https://github.com/tanc)、[【dapd 007】](https://github.com/dapd007)、[【塞缪尔戈达德】](https://github.com/samuelgoddard)💪

阅读[变更日志](https://github.com/gridsome/gridsome/blob/master/gridsome/CHANGELOG.md)了解所有新特性和错误修复。