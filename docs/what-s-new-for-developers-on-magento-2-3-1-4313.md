# Magento 2.3.1 对开发者有什么新的好处

> 原文：<https://dev.to/rafaelcg/what-s-new-for-developers-on-magento-2-3-1-4313>

今天 Magento 发布了 Magento 2.3.1，在每个新的版本中，我认为对每个开发者来说重要的是哪些组件有了更多的改变。该版本包括对核心产品的 200 多个功能修复、社区贡献的 500 多个请求，以及 30 多个安全增强。这些贡献从核心代码的小清理到诸如库存管理和 GraphQL 等重要特性的开发。

# 高亮显示

## 开发者体验新闻

#### 升级过程的自动化

Magento 2.x 升级期间，一个新的 composer 插件`magento/composer-root-update-plugin`会自动更新 composer.json 中的所有依赖项。

#### 其他改进

软件包 [PWA Studio](https://magento-research.github.io/pwa-studio/) 和 [GraphQL](https://devdocs.magento.com/guides/v2.3/graphql/release-notes.html) 被更新。

## 安全新闻

我们有 30 项安全增强功能来帮助关闭:

*   跨站点脚本
*   任意代码执行
*   敏感数据漏洞

您可以将他们登记到 [Magento 安全中心](https://magento.com/security/patches/magento-2.3.1-2.2.8-and-2.1.17-security-update)。

## 业绩新闻

*   如果用户中断结账继续购物，用户在结账时输入的发货和账单数据现在会保持不变。以前，购物车更新后，结账数据会被删除。
*   来自客户地址的 UI 组件已被重写，以支持对具有 3000 个及更多地址的客户的管理。
*   管理订单创建页面现在可以处理具有 3000 个地址的客户帐户，而不会出现性能问题。
*   客户地址簿通过店面上的网格显示附加客户地址的列表。

## DevOps 新闻

*   Magento 支持弹性搜索 6.0
*   《胃底支撑》第 5.0 版
*   Magento 支持 PHP 7.2.x

## 商家工具新闻

#### 管理订单创建

管理订单创建工作流现在消除了编辑帐单和送货地址的延迟，并且这些字段在填充时就会被填充。

#### PDP 图像

由商家上传的大尺寸 PDP 图像(大于 1920 x 1200)将不再被压缩，高质量被保留。

#### 库存管理 1.1.0

现在，社区项目[多源库存](https://devdocs.magento.com/guides/v2.3/inventory/release-notes.html) (MSI)增加了多个新功能:

*   **支持 Elasticsearch 和库存管理**，当使用 Elasticsearch 时，所有网站搜索现在返回正确的产品和数量。
*   **距离优先来源选择**，使用谷歌地图应用编程接口，它将根据最近的库存位置来实现成本。
*   **库存批量转移**，库存批量转移已经优化
*   **店内提货履行选项**，商家可以为选择的货源启用店内提货。

如果你想知道更多关于所有已修复问题的细节，请查看 [DevDocs 版本](https://devdocs.magento.com/guides/v2.3/release-notes/ReleaseNotes2.3.1OpenSource.html#fixed-issues)。