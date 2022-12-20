# PHPStorm 接错了 index.php

> 原文：<https://dev.to/ygerasimov/phpstorm-picking-up-wrong-indexphp-465l>

最近我在用 docksal 设置 Drupal 开发环境。我使用传统的 drupal composer 设置([https://github.com/drupal-composer/drupal-project](https://github.com/drupal-composer/drupal-project))，所以 drupal 位于服务器上的/web 文件夹中。

我已经在 PHP Storm 中映射了主根文件夹

[![default mapping](img/68fe0269d49564669e2b28312c6e3963.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vnJaUiX1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q0wpkxd1vmcquzvtxta1.png)

因此，当我开始调试会话并发现一些奇怪的文件正在启动时，我真的很惊讶。

[![surprise](img/1ece29bfdb88f5be2eb21fc0f13e447d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--68wDVGG4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ut2pafm07oyw4e10jecf.png)

看起来 PHP Storm 试图找到它能找到的第一个 index.php，然后像被调试一样开始会话。

解决这个问题的方法是映射文件夹“web”和实际的 index.php，这样 PHP Storm 可以更容易地找到合适的文件。

[![proper mapping](img/a9c05bae9dd363e1e099259387d782fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UtUTULr2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lug8293x9lrv6km7yjpd.png)

[![explicit index.php](img/b06a552a64d366fddf4a95e65874e718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--khwsgKy0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d5jpd5uesjslt6vqrxw9.png)

因此，正确的 index.php 显示为正在调试。

[![and now nice debugging](img/bad2bbcfdf073ad0bc556f3b322e0bcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ilhf9RM0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/huwhmn34zp3sz19s57jy.png)

调试愉快！