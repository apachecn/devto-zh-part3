# 在 Craft CMS 中设置多语言网站

> 原文：<https://dev.to/epicosity/setting-up-a-multi-language-site-in-craft-cms-2lbp>

我最近用 Craft CMS 建立了一个多站点，因为我是一个信息迷，我想为什么不把它写下来。

这个多站点设置将用于 Craft CMS(版本 3)平台上的多种语言。

1.  设置好主站点后，您可以通过导航至“设置>站点>+新站点”来设置第二个(或更多)站点。在这个例子中，我们的第二个站点将是一个名为“Espanol”的西班牙语站点(抱歉，我甚至没有尝试在站点名称或句柄中使用。)。

[![](img/7b59001f8239d33a495a8b16a590d48b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2xKUAIVc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/985/1%2A46DM8-uWtP65MMDcr2oM5A.jpeg) 

<figcaption>两个站点都列在 Craft CMS</figcaption>

的站点面板中

1.  新站点的设置如下——设置新站点名称(西班牙语)、句柄(西班牙语)、语言(西班牙语)和基本 URL (web/es)

[![](img/71fa05a7a2f79b3b97c7de592b54782e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wSCDdlFf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/635/1%2A8kHsEu1VD-0US8nP7kMx1g.jpeg) 

<figcaption>我们用于新西班牙语网站的设置</figcaption>

1.  一旦建立了第二个站点，您将需要在每个单独的站点部分启用该新站点。

导航到“设置>部分”—启用新站点并输入条目 URI 格式和模板(您可以使用与主站点相同或不同的模板)

需要设置的一个更容易混淆的属性是“**将条目传播到所有启用的站点？**

如果您选中此框，所有条目以及您在这些条目中分配的任何字段都将被复制到第二个站点。如果启用了此功能，并且您在英文网站上创建了一个新条目，那么在西班牙语网站上将会创建一个相应的/相同的条目。您可以独立于每个网站更改该条目的内容，但是如果您从一个网站添加或删除字段，它们将随后从所有网站添加或删除。如果你做的是直接 1:1 的翻译，并且你希望两个站点几乎完全相同，这是很有用的。

如果未选中此项，您将必须手动添加条目以及手动添加任何字段。如果条目的结构在多个站点之间有所不同，您需要禁用此设置。

[![](img/4ef5471cbeb62611826c2467ee46e6f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4NX9iaAo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5DzrDA8dijapbmd5o8KJ8w.jpeg)

1.  接下来，您需要更新“设置>字段”下的设置。您可以为每个字段设置翻译方法。—每种语言=每种语言的每个网站的不同内容(您可能有多个使用相同语言的网站，并希望根据这些语言来创建内容)—每个网站=每个网站的不同内容(尽管每个网站设置了语言，但您希望根据网站本身来创建不同的内容)—每个网站用户组=每个网站的不同内容对网站进行分组(您可能希望对网站进行分组并根据该组创建内容)—不可翻译=所有网站/语言的内容都相同

但是，有些字段不能设置转换方法:

**矩阵** —只能按矩阵内的每个块设置，选择每个站点的基础将覆盖任何设置为

**Neo** 的单个块—根本不能设置，必须在每个站点的基础上管理这些块(也就是在每个站点的每个条目上重新添加适当的块，这意味着它可能会也可能不会反映主站点，取决于您如何重新添加块)

[![](img/4aa5e10172d6b5bee3478519f7aaa10f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2T7jT5oH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/682/1%2ABjtZ0GfoXkISeMxnupbMkg.png)

1.  在“条目”视图的顶部有一个选择框，您可以在其中选择要编辑条目的站点。更改此下拉列表将会更改下面显示的条目的结构。我相信当您注销并重新登录时，该设置也将持续，这是一个需要注意的问题，当您重新进入仪表板并开始编辑条目时，您可能正在第二个站点而不是您想要编辑的主站点上编辑条目。

[![](img/2e2d08d5103cbcb7098e210649103116.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QIP7OIck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Am6qF71FNd7853lC1H1Ammg.jpeg)

1.  当您编辑一个条目时，您还可以通过该条目顶部的下拉菜单在站点之间切换。这个下拉菜单将显示条目的可用版本。

[![](img/bf528b1c991fc4ad44beac1dc12e970d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wjXIJLyi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtdAhmTKGifffINSuOMnp2g.jpeg)

1.  在多站点设置中，可以在其他站点上翻译的每个字段旁边都会出现一个图标。

[![](img/6afdcd29e29b475cb82276d4cd410057.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fc_8_WWp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/918/1%2AC70u5qquRSDWWFCIJsPVKw.png)

1.  似乎不需要设置其他任何东西来运行它，尽管我在网上找到的所有东西都希望你为每个新站点创建一个新的 web 文件夹，并使用一个新的 index.php。只要你不需要为每个新站点定制任何模板，这看起来没有那个功能。Craft 根据为该网站设置的 url 来决定使用哪些内容。

2.  您可以对所有内容使用相同的模板，为每个站点设置单独的模板，或者对每种语言的条件使用相同的模板。总而言之，它非常灵活。

* * *