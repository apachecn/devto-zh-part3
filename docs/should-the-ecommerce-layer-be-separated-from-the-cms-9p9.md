# 电子商务层应该从 CMS 中分离出来吗？

> 原文：<https://dev.to/tkalejaiye/should-the-ecommerce-layer-be-separated-from-the-cms-9p9>

伙计们，还有一个问题。因此，我试图决定我将使用什么工具来为客户建立一个电子商务网站。目前，我已经决定使用 Gatsby.js 和 Shopify 来创建它，用于产品创建/库存和所有电子商务的东西。我还计划使用 Netlify CMS 进行网站内容管理(博客帖子、位置信息等)。)

我最初也想使用 Netlify CMS 来处理产品创建，但我不知道如何做到这一点并整合库存管理。当然，我可以通过 CMS 创建产品，但是如果商品被购买或者缺货，我该如何更新产品数量呢？我读过一个关于 Contentful 的教程，他们使用 Contentful 进行产品创建，使用 Commerce 层进行库存管理。然后，该应用程序对商业层进行 api 调用，以更新产品数量。这是一种正常的模式吗？还是在同一个地方管理产品创建和库存管理更好/更容易？