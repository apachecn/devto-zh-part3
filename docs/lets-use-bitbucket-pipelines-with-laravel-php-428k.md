# 让我们在 Laravel/PHP 中使用 Bitbucket 管道

> 原文：<https://dev.to/jaejamesdev/lets-use-bitbucket-pipelines-with-laravel-php-428k>

## 你好，世界！

我是 Bitbucket 的长期爱好者(抱歉，是 GitHub 的粉丝)，Bitbucket Pipelines 是迄今为止我最喜欢的 CI/CD 工具之一。Bitbucket Pipelines 允许您根据存储库中的配置文件自动构建、测试甚至部署您的代码。哦，我差点忘了说，它是基于 Docker 的，为所有 Docker 迷准备的；)

那么，Jae，我需要做些什么来开始呢？

嗯...这是一个非常好的问题！即使你对 Docker 的工作原理或它是什么没有深入的了解，它的设置也非常简单(尽管我建议你阅读一下 Docker，因为它很酷)。你所需要的只是一个 Bitbucket 账户，Bitbucket 会给你一个月 50 分钟的免费构建时间，这对个人项目和兼职来说足够了。

现在，您已经创建了一个 Bitbucket 帐户，并完全加入了 Atlassian 的市场营销，让我们从有趣的事情开始吧！

### bitbucket-pipeline . yml

我们的 CI/CD 自动化快乐的心脏是我们的 *bitbucket-pipelines.yml* 文件。现在，您可以用两种方法之一来生成它；第一种方法是手动创建文件(这是我们将做的，以便我分解每个阶段)，第二种方法是使用在线生成器，打开您的存储库，单击左侧的管道，它会带您通过可视化向导设置您的 *bitbucket-pipelines.yml* 文件。

因此...唠叨够了。在项目路径中创建一个名为 *bitbucket-pipelines.yml* 的文件。现在，我们将使用以下内容填充文件，我将对其进行分解。

```
image: php:7.3

pipelines:
  default:
    - step:
        caches:
          - composer
        script:
          - apt-get update && apt-get install -y unzip
          - curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
          - composer install
          - cp .env.example .env
          - php artisan key:generate
          - vendor/bin/phpunit
          - echo "Done!" 
```

我们来看第一行:

```
image: php7.3 
```

这是我们希望 Bitbucket 管道使用的基本 Docker 映像。只要图片在 DockerHub 上公开，我们可以把它改成我们想要的任何东西。然而对我来说，我发现默认的 PHP Docker 镜像工作得很好，并且包含了我所需要的所有依赖项。如果你想使用任何其他的 PHP 版本，只需更改最后的 *7.3* ，真的就这么简单！

```
pipelines:
  default:
    - step:
        caches:
          - composer
        script:
          - apt-get update && apt-get install -y unzip
          - curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
          - composer install
          - cp .env.example .env
          - php artisan key:generate
          - vendor/bin/phpunit
          - echo "Done!" 
```

以上是我们管道的大脑。如您所见，在“管道”下，我们可以定义不同的下行管道。这是默认管道。默认情况下，只要分支被设置为使用管道，该管道就会自动运行(默认情况下是主管道)。如果您想要自动代码测试，这是很有用的。
*缓存:*创建了一个 composer 缓存，这意味着如果 composer.json 文件自上次构建以来没有更新，我们就不需要运行 composer install。
在脚本部分，这看起来应该很熟悉。这些只是典型的 Linux 命令。我们需要安装 Unzip，因为 PHP docker 映像非常简单。完成后，我们希望安装 composer 并将其移动到 */usr/local/bin* 目录中，这样我们就可以全局访问它。从那时起，我们想要复制我们的 *.env.example* 文件作为我们的*。env* 文件并生成一个专门用于 Bitbucket 管道的应用程序密钥。然后我们只需运行 PHPUnit 来运行我们的测试。这意味着如果我们的一个测试失败了，构建就会失败，如果你配置了电子邮件通知，你会在收件箱里收到一封可爱的电子邮件。

## 结论

真的就这么简单...这将自动构建和测试我们的代码。现在，我们可以更进一步，添加部署步骤，这将允许我们访问按钮，以将代码部署到各种环境中。请务必关注我，因为我将写一篇关于如何从 Bitbucket 管道部署 Forge 的帖子，作为这个小指南的继续！

如果任何人有任何问题，请留下评论，我一定会帮你解决！