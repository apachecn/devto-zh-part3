# 🍝用 Next.js (React)、GraphQL、Strapi 和 Stripe 制作一个 Deliveroo 克隆🍔菜品清单(第 3/7 部分)

> 原文：<https://dev.to/ryanaz/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----dishes-list-part-37-5eh>

[![Strapi Next.js tutorial](img/6ae09f2771166462103198b0d9c7ca44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bD-CV-5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iudm6at0xp8v1o7f1f86.png)

本教程是用 Next.js (React)、GraphQL、Strapi 和 Stripe 制作 Deliveroo 克隆教程系列的一部分。

**目录**

*   [设置](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----setup-part-17-ked)(第 1 部分)
*   🏠[餐馆](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----restaurants-list-part-27-10ce)(第二部分)
*   🍔[菜肴](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----dishes-list-part-37-5eh/)(第三部分)**-当前**
*   🔐[认证](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----authentication-part-47-eho)(第四部分)
*   🛒 [购物车](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----shopping-cart-part-57-2h1e)(第五部分)
*   💵[订购和结账](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----order-and-checkout-part-67-fph)(第六部分)
*   🚀[奖励:部署](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----bonus-deploy---part-77-1i8e)(第七部分)

*注:* *源代码* *在 GitHub 上有**:[https://GitHub . com/strapi/strapi-examples/tree/master/nextjs-react-strapi-deliver oo-clone-tutorial *](https://github.com/strapi/strapi-examples/tree/master/nextjs-react-strapi-deliveroo-clone-tutorial*)。**

## 🍔菜肴清单

恭喜您，您成功显示了餐厅列表！这是重要的第一步。

### 定义内容类型

每个餐馆出售的菜肴也必须存储在数据库中。所以，我们现在需要一个新的内容类型，显然命名为`dish`。既然您已经知道如何创建它，我将只给出它的属性:

*   `name`同类型`String`。
*   `description`同类型`Text`。
*   `image`同类型`Media`。
*   `price`同类型`Number`(整数)。
*   `restaurant`带类型`Relation`:这个更具体一点。我们在这里的目的是告诉 Strapi，每一道菜都可以与一家餐馆相关联。为此，创建一个一对多关系，如下所示。

[![Strapi relation](img/1de74c9004d1401287e08243aa2de416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UvOrkH8g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/um51cjveo5xweophtze9.png)

以下是最终结果:

[![Dishes fields](img/3d9744ac7e75389510a5f8e1c31f1ac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T48_-kXp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ozxel8tagpyq9xfjc0a0.png)

### 添加一些条目

然后，从内容管理器添加一些菜:[http://localhost:1337/admin/plugins/Content-Manager/dish](http://localhost:1337/admin/plugins/content-manager/dish)。确保他们都有一个形象，并链接到一家餐馆。

### 展示菜肴

[![Dishes list](img/d8d2aeee6f68db00d54bda9526e180fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---EhRQbOG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u4hzy41jvhczwkmi5bte.gif)

我们将为`/restaurants`使用一个新的路由，它将接收餐馆的 ID 并返回该餐馆的菜肴列表:

```
cd ..
cd ..
cd pages
touch restaurants.js 
```

Enter fullscreen mode Exit fullscreen mode

路径:`/frontend/pages/restaurants.js`