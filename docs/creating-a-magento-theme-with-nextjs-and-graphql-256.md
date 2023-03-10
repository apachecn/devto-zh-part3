# 用 Next.js 和 GraphQL 创建 Magento 主题

> 原文：<https://dev.to/restoreddev/creating-a-magento-theme-with-nextjs-and-graphql-256>

2018 年底，Magento 发布了他们的电子商务框架的 2.3.0 版本，采用了新的 GraphQL API。传统上，Magento 主题是用 PHP 模板创建的，类似于 WordPress。然而，通过新的 GraphQL API 和 [PWA Studio](https://magento-research.github.io/pwa-studio/) ，可以在 React 和 Vue 等 JavaScript 前端框架中创建新的主题。为了试验这个 API，我使用 React、Next.js 和 Apollo 创建了一个名为 [Basil](https://github.com/restoreddev/basil) 的开源主题。这里是【Magento 样本目录的主题预览。主题是非常实验性的，所以它需要代码清理和响应样式，但它允许我探索如何以这种方式创建主题。它没有使用任何 PWA Studio 组件，因为我想看看从头开始构建一个前端需要什么。以下是我对整个过程的想法和印象。

### 决定一个框架

前端 JavaScript 框架有很多选择，所以我试图用两个要求来缩小我的选择:它必须有简单的服务器端 SEO 渲染(对电子商务至关重要)和它必须支持 GraphQL。最后试了三个框架 Nuxt.js (Vue)、Ember with Fastboot 和 Next.js (React)。Ember 实际上有我最喜欢的开发经验。代码生成 CLI 和简单的模板是一个很大的吸引力，但是，它的 GraphQL 支持是不够的。最好的 Ember GraphQL 插件不支持加载状态。Nuxt.js 的 GraphQL 集成也有所欠缺。在服务器上渲染时，很难加载 GraphQL 数据。最终我选择了 Next.js，因为 Apollo 出色的 React 集成，也因为 Next 的服务器渲染是一流的。在服务器上输出 HTML 之前，Next 将用必要的 GraphQL 数据呈现整个 React 组件树。

### [T1】我喜欢 GraphQL](#i-like-graphql)

Basil 是我第一次认真使用 GraphQL，开发体验非常棒。这些查询真的很容易编写，我喜欢你可以把它们存储在文件中以便重用。它确实简化了数据检索，因为您可以在 GraphQL 客户端中编写查询，如[失眠症](https://insomnia.rest/)，并且字段将在您键入时根据 Magento 返回的模式自动填充。在 React 中，Apollo 通过提供一个查询组件简化了 GraphQL，您可以使用该组件来包装您的 JSX，并且您的模板将被自动提供查询结果。这里有一个例子:

```
<Query query={PRODUCT_QUERY}>
    {(response) => {
        if (response.error) return <div>Error</div>;
        if (response.loading) return <div>Loading...</div>;

        let product = response.data.product;
        return (
            <div class="product__name">{product.name}</div>
        );
    }}
</Query> 
```

### 溃败是一种痛苦

Magento 有一个独特的路由系统，因为根目录下的任何路径都可以映射到类别、产品或内容页面。例如，在 Magento 网站上打开`/backpack`会带你到一个名为 Backpack 的产品，一个背包类别或者一个关于背包的内容页面。这完全由 Magento 数据库中的目录配置决定。为了在前端客户端上允许这种路由，我必须设置一个特殊的通配符路由，它将接受任何 URL 路径，然后查询 Magento 的 [urlResolver](https://devdocs.magento.com/guides/v2.3/graphql/reference/url-resolver.html) 来判断页面是否应该显示产品、类别或内容组件。不幸的是，这意味着必须为每个目录页面运行两次查询。像 Gatsby 这样的框架可能是小型目录的更好选择，因为它可以在构建步骤中为每个 URL 生成一个前端页面。

### 我希望单元测试 React 更容易

我喜欢 Ember 的一个特性是它附带了一个单元测试设置和一个执行集成测试的框架。Next 和 React 有很多关于如何设置 Jest(测试运行程序)的例子，但是很少有关于集成测试呈现 HTTP 结果的组件的好方法的文档。我设置了 [Enzyme](https://airbnb.io/enzyme/) 来帮助组件渲染和分析，但是仍然没有建立一种方法来模拟 GraphQL 服务器进行更广泛的测试。

### 对 Magento 的生存能力

不幸的是，我不认为是时候让 Magento 这样做了。Magento 的 GraphQL 端点是缺乏的，直到今年晚些时候才会被充实。目前，要构建购物车和结帐，您必须使用旧的 REST API。我认为最好等到 GraphQL API 有了更好的前端覆盖。即使它覆盖了收银台，它仍然会缺少一些较小的 Magento 功能，如相关产品和愿望清单。Magento 仍然致力于他们的前端解决方案， [PWA 工作室](https://magento-research.github.io/pwa-studio/)。当特性完成后，PWA Studio 将成为 JavaScript 前端的另一个选择。

### 最后的想法

新的 GraphQL 支持是令人兴奋的，因为它将允许 Magento 开发人员进行现代和独特的前端体验。然而，在投入生产之前，还有许多开发工作要完成。虽然我会怀念 PHP 模板的简单性，但是 JavaScript 框架提供的性能和功能会让它们受到许多商家的欢迎。此外，它将使向 Magento 生态系统介绍前端开发者变得更加容易。

如果你对我制作罗勒的经验有任何疑问，请告诉我。另外，查看一下 [GitHub 回购](https://github.com/restoreddev/basil)和[在线预览](https://basil.andrewdavis.me/)，让我知道你的想法！