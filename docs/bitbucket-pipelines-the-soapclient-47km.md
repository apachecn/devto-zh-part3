# 比特桶管道和 SoapClient

> 原文：<https://dev.to/jaejamesdev/bitbucket-pipelines-the-soapclient-47km>

## 你好，世界！

这是我在 Dev 社区的第一个帖子，这只是一个我觉得可能会帮助别人的小技巧！

我非常喜欢 BitBucket 管道，对于很多 Laravel 项目，我可以使用普通的 PHP docker 映像，但是，这不包括我在一个项目中需要的东西 SoapClient。我在管道中需要 SoapClient 的原因是，PHPUnit 针对 WSDL API 运行测试，以确保我们不会得到垃圾返回，并且 API 和解析器客户端工作正常。

#### 片段

```
- apt-get update -y && apt-get install -y libxml2-dev && apt-get clean -y && docker-php-ext-install soap 
```

你所需要做的就是将上面的代码片段添加到**脚本:**下的 **bitbucket-pipelines.yml** 文件中！真的就这么简单！然后，您的 PHP docker 映像将可以访问 PHP SoapClient。