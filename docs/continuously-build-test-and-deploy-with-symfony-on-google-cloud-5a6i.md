# 使用 Symfony 在 Google Cloud 上持续构建、测试和部署

> 原文：<https://dev.to/_mertsimsek/continuously-build-test-and-deploy-with-symfony-on-google-cloud-5a6i>

嗨，朋友们，我要谈谈 Symfony 在 Google Cloud 上的构建、测试和部署。通过这种方式，我们将能够实现每个过程的完全自动化。我们对任何人为错误的容忍度都会降低。为了提供这个，我们应该安装 **gcloud** 。你可以看这份文件，这是小菜一碟。之后，您应该运行所有这些命令。

```
gcloud init
gcloud components install app-engine-php
gcloud auth login
gcloud config set project [GC-PROJECT-ID] 
```

从现在开始，我们的 gcloud sdk 被认证到我们的 Google Cloud 项目。如果你有任何困难，你也可以按照谷歌的文件。

好吧，让我们用 Composer 创建一个 Symfony 项目。运行这些命令来运行 Symfony 应用程序。

```
composer create-project symfony/skeleton mertblog -vvv && cd mertblog
composer require dotenv annotations template log google/cloud -vvv
composer require web-server-bundle maker tests --dev -vvv
bin/console make:controller 
```

控制器的内容如下。我只改变了路线。

```
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;

class DefaultController extends AbstractController
{
    /**
     * @Route("/", name="default")
     */
    public function index()
    {
        return $this->render('default/index.html.twig', [
            'controller_name' => 'DefaultController',
        ]);
    }
} 
```

就是这样！我们有所有的图书馆和文件，我们将需要进一步。为了部署到 Google Cloud App Engine 中，我在项目的根目录下创建了 **app.yaml** 。

```
runtime: php
env: flex

env_variables:
  APP_ENV: 'prod'
  DOCUMENT_ROOT: '/app/public' 
```

不久之后，我运行这个命令来部署。

```
gcloud app deploy 
```

该命令自动读取该文件并采取措施。可能要花一点时间。然而，一切都很好，进展顺利，不用担心。完成后，运行此程序，您将看到我们在应用引擎生产中的默认控制器响应。

```
gcloud app browse 
```

[![alt text](img/0288013c97c775ce5a952769dd632240.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b5ax2QLe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ixrwhaod6swd07ap8rli.png)

完美！从现在开始，我可以自动创建一个 GitHub 库，并将 Symfony 应用程序推送到这个库中。如你所见，我的仓库已经准备好了。

[![alt text](img/6a7df10c3d54bac96516c91c60fb7a2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d-CvByjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nm8inzglomqckave06lp.png)

现在，我们必须集成 GitHub 和 Google Cloud Build。为此，你可以去这个页面。

[https://github.com/marketplace/google-cloud-build](https://github.com/marketplace/google-cloud-build)

你会遇到这种情况，点击**设置计划**按钮并继续。它需要一个存储库来运行。我选择了我刚才创建的存储库。

[![alt text](img/3a66fbf6871f995edb20d5637e23daab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0dtQoEZd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xpl62opfaozuvvbypw8.png)

[![alt text](img/4057e5378b5314e9195645da8125f094.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EX0kfYG---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qoy4no370d2iyykzflsh.png)

通过这种方式，Google Cloud Build 和 GitHub 将顺利集成运行。现在，我创建一个 **Dockerfile** 和 **Docker ignore file** 来在项目的根目录下创建一个容器。我将使用这个容器测试我的应用程序。

```
FROM gcr.io/google-appengine/php 
```

这里是**。dockerfile** 。

```
.gcloudignore
.git
.gitignore
vendor/
var/
Dockerfile
.dockerignore 
```

没有了，我们没有任何手动流程。为了自动化我的过程，我在项目的根目录下创建了一个 **cloudbuild.yaml** 文件。这个文件有三个部分。首先，Google Cloud Build 正在构建我们的应用，并根据我们的配置进行测试。如果这些步骤成功，它将部署到**应用引擎**。

```
steps:
  # Build
  - name: 'gcr.io/cloud-builders/docker'
    args: [ 'build', '--build-arg', 'COMPOSER_FLAGS=--dev', '-t', 'gcr.io/$PROJECT_ID/$REPO_NAME:$COMMIT_SHA', '.' ]
  - name: 'gcr.io/cloud-builders/docker'
    args: [ 'push', 'gcr.io/$PROJECT_ID/$REPO_NAME:$COMMIT_SHA' ]

  # Test
  - name: 'gcr.io/$PROJECT_ID/$REPO_NAME:$COMMIT_SHA'
    args: [ 'bin/phpunit' ]
    dir: /app
    env:
      - 'APP_ENV=test'

  # Deploy
  - name: 'gcr.io/cloud-builders/gcloud'
    args: ['beta', 'app', 'deploy', '--quiet', '--image-url', 'gcr.io/$PROJECT_ID/$REPO_NAME:$COMMIT_SHA', '--version', '$BRANCH_NAME', '--no-promote'] 
```

但是没有部署，我们需要做一些事情。例如，让我们创建一个测试类。

```
bin/console make:functional-test IndexControllerTest 
```

给您

```
<?php

namespace App\Tests;

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class IndexControllerTest extends WebTestCase
{
    public function testSomething()
    {
        $client = static::createClient();
        $crawler = $client->request('GET', '/');

        $this->assertSame(200, $client->getResponse()->getStatusCode());
        $this->assertContains('Hello Mertblog', $crawler->filter('h1')->text());
    }
} 
```

不错！我们检查 HTTP 状态代码和页面标题。我们无法通过这个测试，因为我们的标题不是 **Hello Mertblog** 。我不会改变，因为我希望看到失败的构建。好吧，我们必须授予谷歌云建立应用引擎的权限。如您所见，我已经将这些权限设置为 Google Cloud Build。

[![alt text](img/0c5c15ee849871da2c94768bbaab751c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FP8abLK4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffx5gzrycko92d3t76mq.png)

除此之外，你最好不要忘记添加我们刚刚在 Git 上添加的新文件。我已经把我的新文件推送到 GitHub，它会自动建立在谷歌云上。如果你去谷歌云建设页面，你会看到它是失败的，因为我们的测试。HTTP 响应代码是 200，但是标题页不是我要求的。它不能通过。顺便说一下，部署没有运行。因为，为了部署，其他步骤应该成功。

[![alt text](img/f5f36050d9901afed6e96c360597637d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d1MTuSrf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rqhxgg3qbxo2ugu9trck.png)

我们可以检查一下细节。

[![alt text](img/599bbd18d32a8a2d1654e29281ef4a3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nwvHVG5o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/du32bjynvts04xuxn4bc.png)

你也可以在 GitHub 上查看。

[![alt text](img/7974a6f2cf7e1b01eca83bccb63b5891.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Zv3bEx5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncm7fu47453jr20b1kns.png)

让我们在页面上修改一些东西，比如我们的标题。我把标题改成了 **Hello Mertblog** ，并推出了我的新代码 GitHub。

从现在开始，每一步都是完美的。为了在案件中取得成功。

[![alt text](img/d78085fa03881906a087ef901bc32343.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Idh_xwQ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dqmjan3ho0sgeyvr0wem.png)

现在，只要你进入 **App Engine** 就会看到两个版本在运行。而**主**版本是我们的主要版本，是通过 Google Cloud Build 从 Github 部署的。如果我们需要在我们的存储库上看到变化，我们必须改变流量。您可以停止旧版本，点击此页面上的**分流**按钮。并给予 100%的流量。像这样。

[![alt text](img/0e895601c48ad3d2ade9d67fbdb93bb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UcywO9yA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mvi1ij4qm1duvx1q6xsb.png)

让我们检查一下我们的主页。如你所见，我最后的代码很明显被部署了，标题被改了。

[![alt text](img/37b7721ea86720bf463bce0e8a530009.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fR5udAzb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/012c7d9qn6hlh9tvat4j.png)

除此之外，我还要去看看 GitHub。我可以看到没有任何错误或失败的东西。干得好！

[![alt text](img/3575137975de496b6b89ff44c7411e97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MhBRhsBL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9oqif594u2w6vjl082q.png)

# 总而言之

如果我们聚集在一起，将 Google Cloud Build、Google Cloud App Engine 和 Symfony 一起使用会有很大的好处和机会。通过这种方式，所有的过程都是自动的，我们可以远离错误。只要你有任何麻烦或问题，不要犹豫，保持联系。