# AWS 密钥显示在我的 heroku 网站上的照片的 URL 上

> 原文：<https://dev.to/highcenburg/aws-keys-exposed-on-url-from-photos-on-my-heroku-site-5boi>

我在 Heroku 上部署了 2 个 Django 站点，当我在一个新的选项卡上打开图片时，我注意到 AWSAccessKeyId 和 Signature 都显示在两个站点的照片 URL 上。

[![](img/8f79de4a0ef3681f0600ce6b514ba6c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rcOBXAYt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6fnrrpntpa5qovaa2xdj.png)

我认为这不正常，因为我知道这些钥匙应该放在环境中。