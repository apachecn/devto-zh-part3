# 添加注销链接到账户菜单

> 原文：<https://dev.to/harnerdesigns/add-logout-link-to-account-menu-bigcommerce-for-wordpress-1jne>

*[最初发布于哈纳设计的博客](https://harnerdesigns.com/blog/add-logout-link-to-account-menu-bigcommerce-for-wordpress/)*

BigCommerce for WordPress (BC4WP)允许您将 BigCommerce 后端的功能与 WordPress 前端的定制相结合，以获得真正独特的电子商务体验。

他们的集成中似乎缺少了一点，那就是我的账户页面上的注销链接:
[![BC4WP Account Menu - Before](img/36c507730108bcfa08c9793e7809928a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NrX_rxYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://harnerdesigns.com/wp-content/uploads/2019/04/Before-Code.png)

这似乎是一个简单的解决办法，因为我已经开始涉足向开源软件发出拉请求，所以我决定看看。他们称之为“SubNav ”,填充在`bigcommerce-for-wordpress/src/BigCommerce/Accounts/Sub_Nav.php`中。它使用`get_option`提取存储的账户、订单、&地址页面，并用相关数据填充`$links[]`数组。

简单。

只需将注销信息添加到带有`wp_logout_url()`的`$links[]`数组中，注销链接就会出现在我的帐户页面中。

就这样，我对 BC4WP 回购的第一个[拉请求](https://github.com/bigcommerce/bigcommerce-for-wordpress/pull/150)诞生了。

## 像高中毕业舞会前被击落

我的公关被拒绝了，理由是注销 WordPress“超出了 BigCommerce for WordPress 插件的范围”。然而，[现代部落](https://tri.be/)(插件开发者)的 Jonathan 指出，已经有一个过滤器应用于生成的链接，这样网站管理员就可以向链接列表添加任何他们想要的东西:`bigcommerce/account/subnav/links`。

## 如此..？你是怎么做到的？

如果你像我一样，并且*希望*将注销链接添加到 WordPress 账户页面的 BigCommerce 子菜单中，只需将下面的代码添加到你的主题的`functions.php`文件中:

```
add_filter('bigcommerce/account/subnav/links', function ($links) {
 $links[] = [
  'url'     => wp_logout_url(),
  'label'   => __('Logout', 'bigcommerce'),
  'current' => false,
 ];
 return $links;
}, 10, 1); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在在 WordPress 的 BigCommerce 的“我的帐户”页面上将会有一个方便使用的注销链接。

## 最近的博文

*   [如何在 Illustrator 中测量角度](https://harnerdesigns.com/blog/measure-angles-in-illustrator/)
*   [优化我的星星——PHP 生成的 SVG 与 Canvas](https://harnerdesigns.com/blog/optimizing-my-stars-php-generated-svg-vs-canvas/)
*   [免费账户的无限私人 GitHub 回购](https://harnerdesigns.com/blog/unlimited-private-github-repos-for-free-accounts/)