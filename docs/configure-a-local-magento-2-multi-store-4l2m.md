# 配置本地 Magento 2 多商店

> 原文：<https://dev.to/rafaelcg/configure-a-local-magento-2-multi-store-4l2m>

当我们必须在您的本地机器上配置一个多商店时，您可以配置本地 NGINX 或 Apache 配置来使其工作，或者您可以快速更改您的`index.php`来测试、修复问题并且不提交此更改。

在同样的情况下，为了使用 Magento 1 进行测试，我们改变了*中的`$mageRunCode`和`$mageRunType`。htaccess* 或 *httpd.conf* 。
在 Magento 2 中要快速测试你可以把那个代码用到你的`index.php` :

```
$params = $_SERVER;
$params[\Magento\Store\Model\StoreManager::PARAM_RUN_CODE] = 'website_code';
$params[\Magento\Store\Model\StoreManager::PARAM_RUN_TYPE] = 'website';
$bootstrap = \Magento\Framework\App\Bootstrap::create(BP, $params); 
```

Enter fullscreen mode Exit fullscreen mode