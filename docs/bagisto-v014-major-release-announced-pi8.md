# Bagisto v0.1.4 主要版本发布

> 原文：<https://dev.to/pathaksaurav/bagisto-v014-major-release-announced-pi8>

开源 Laravel 电子商务框架、 [Bagisto](https://bagisto.com/en/) 宣布了他们的第一个主要版本，其中包含了大量的改进和更新。你可以从这里下载最新的版本【https://bagisto.com/en/download/ T4】

新版本中的主要更新:

## 支持多厂商市场:

最令人期待的[多供应商市场](https://bagisto.com/en/laravel-multi-vendor-marketplace/)也已经发布，并支持 Bagisto v0.1.4。该市场使您更容易将您的在线商店转换为多供应商市场，从而允许多个卖家在您的网站上销售。

**Laravel 多厂商市场演示**:【https://demo.bagisto.com/marketplace/ T2】

市场为客户提供了充分的选择，让他们可以在一个页面上从不同的供应商那里选择合适的产品。

## 增强多仓库库存管理

多仓库库存是每个电子商务商家都在关注的，为此他们支付了高昂的定制费用，以便在他们各自的在线商店框架中实现这一点。

[![Bagisto Multi-Warehouse Inventory](img/3ba88c7ae5373e2820758873c6bf8e2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BiUDT80g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bagisto.com/wp-content/uploads/2018/08/msi.png)

Bagisto 提供了开箱即用的精致功能。在新功能中，管理多个来源的库存得到了增强，使您可以在发货时轻松管理库存。

## GUI 安装程序

随着 GUI 安装程序的引入，安装变得简单多了。通过提供基本的数据库凭证和您的商店详细信息开始这个过程，剩下的过程将由安装程序负责。

[![Bagisto GUI Installer](img/61c0feead7444d9793fd44ebb1ab01e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7UaUULgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bagisto.com/wp-content/uploads/2019/02/GUI-Installer.png)

但是，您仍然可以按照此处的指南使用 composer 安装项目:[Bagisto Installation via Composer](https://webkul.com/blog/laravel-ecommerce-website/)

## 实现产品扁平化

Bagisto 使用 [EAV(实体-属性-值)](https://bagisto.com/en/technical-requirements-for-an-ecommerce-website/)数据库模式在多个表中存储产品数据，由于这种模式，有时处理速度会变慢。

在新版本中，我们实现了 product flat，在这个平台中，会自动创建一个新表来存储产品数据。

随着产品扁平化的实现，为产品创建了一个健壮的子系统，使得店面上的搜索、排序、过滤等变得更快

除了上述四个主要更新，还有许多重要的错误修复，旨在使在线商店的设置过程更加容易，并为您的客户提供流畅的浏览体验。