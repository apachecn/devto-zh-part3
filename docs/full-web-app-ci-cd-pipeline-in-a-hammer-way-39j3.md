# 全 Web App + CI/CD 管道以锤子的方式！🔨🔥🛠

> 原文：<https://dev.to/nickitax/full-web-app-ci-cd-pipeline-in-a-hammer-way-39j3>

带有 NodeJS 后端+ CI/CD 管道的 VueJS 应用程序:Hammer way🔨🛠

* * *

在本教程中，我将解释如何用 NodeJS 后端+完整的 CI/CD 管道构建一个简单的全面的 Web 应用项目。

我称之为**“锤道”**，因为它没有使用 Docker 或者任何架构...这不是很花俏，但它的作品 _(ツ)_/

在本教程的最后，我们将能够让我们的应用程序运行在服务器上，完全自动化的管道将建立，测试和部署在每一个推 git 仓库！

这两个项目都已在此处提供:

后端=> [这里](https://github.com/NickitaX/dev_to_tutorial_backend)
前端= > [和这里](https://github.com/NickitaX/dev_to_tutorial_frontend)

(请随时提交 PRs 进行改进)

你准备好了吗？

我们走吧！🔥

* * *

# 第一部分:我们的节点后端💻

* * *

为了简单起见，我们将有一个超级简约的 NodeJS 后端，它将做唯一的事情:服务前端。
项目结构如下:

```
./backend
├── package-lock.json
├── package.json
├── circle.yml
├── scripts
│   └── deploy.sh
└── server.js 
```

我们来看看 server.js:

```
const express = require('express');

const path = require('path');

const http = require('http');

const app = express();

app.use(express.static('/home/deploy/frontend/'));

app.get('/', (req, res) =>  res.sendFile(path.join('/home/deploy/frontend/index.html')));

http.createServer(app).listen(8080); 
```

上述代码使用 express 库，该库加载静态文件夹中引用的**index.html**文件，并在端口 **8080** 上提供服务。不是火箭科学，但我们需要从某个地方开始...

deploy.sh 呢？

```
#!/usr/bin/env bash

ssh-keyscan -H "$1" >> ~/.ssh/known_hosts
ssh "deploy@$1" rm -rf ~/backend/*
scp -r ./server "deploy@$1:~/backend/"
scp -r ./package.json "deploy@$1:~/backend/"
scp -r ./package-lock.json "deploy@$1:~/backend/"
ssh $1 "cd /home/deploy/backend
                                   npm i
                                   forever stopall
                                   forever start server.js
                                   “ 
```

这个 shell 脚本扮演自动化部署系统的角色。

在我们浏览 shell 代码之前，需要澄清一些服务器设置:

在我们的服务器上，我们将专门为此目的创建 **deploy** 用户，并为我们的 CI/CD 管道生成一对 SSH 密钥(将很快完成)。

我们还将安装`forever js`，它将在后台运行 NodeJS 进程。

主目录下会有两个文件夹:**前端**和**后端**。这些是 CI 服务器将文件复制到的位置。

> 对于那些不熟悉 shell 变量的人来说:`$1`意味着我们将把服务器主机名作为参数传递。(它将被执行为:`bash deploy.sh my.awesome.website`，这里将放置`my.awesome.website`而不是`$1`)。
> 简而言之，该脚本删除旧版本的服务器文件，从 Git 存储库中复制新文件，并重启 server.js 的后台运行进程

但是我们如何与 CircleCI 整合呢？下面是我们的 magic circle.yml 配置文件:

```
version: 2
jobs:
  build:
    working_directory: ~/backend
    docker:
      - image: circleci/node:4.8.2
    steps:
      - checkout
      - restore_cache:
          key: dependency-cache-{{ checksum "package.json" }}
      - run:
          name: Install npm dependencies
          command: npm install
      - save_cache:
          key: dependency-cache-{{ checksum "package.json" }}
          paths:
            - node_modules
  test:
    docker:
      - image: circleci/node:4.8.2
    steps:
      - checkout
      - run:
          name: Test
          command: npm run test

  deploy:
    docker:
      - image: circleci/node:4.8.2
    steps:
      - checkout
      - run:
          name: Deploy
          command: bash ./scripts/deploy.sh my.awesome.website

workflows:
  version: 2
  build_and_test:
    jobs:
      - build
      - test:
          requires:
            - build
          filters:
            branches:
              only: master
      - deploy:
          requires:
            - build
            - test
          filters:
            branches:
              only: master 
```

上面的配置文件声明了我们从 Git 仓库中提取的工作目录，测试和部署步骤将运行测试，并执行我们之前讨论的 shell 脚本来复制新文件。

它还包含环境描述，如 docker 容器上安装的节点版本。

厉害！我们完成了后端！雅虎！🎉

现在，将您的项目推送到您的 Git 存储库吧！

* * *

# 第二部分:快速服务器配置🔑

* * *

正如我所承诺的，这里有更多关于 Linux 服务器配置的内容:

我们需要安装 node.js

[= >节点 JS](https://github.com/nodesource/distributions/blob/master/README.md)

之后，我们应该安装`forever.js`进行后台处理(可能有更好的选择，请随意试验):

[= >永远的 JS](https://github.com/foreverjs/forever)

最后，一对 SSH 密钥:

```
ssh-keygen -t rsa -C “amazing@dev.to" 
```

您将很快需要一个私钥来允许 CircleCi 在实例上执行 SSH 操作。

* * *

# 第三部分:前端！😍

* * *

这是一个 VueJS 前端的时代！

从 VueJS 开始的最好方法是使用 VueCLI。安装后，您可以通过运行:
来创建新的应用程序

```
vue create frontend 
```

(此处可关注官方文档:

生成一个项目可能需要一些时间，最后我们会有类似的结构:

```
./frontend
├── README.md
├── babel.config.js
├── node_modules
├── package-lock.json
├── package.json
├── public
└── src 
```

> 不要忘记运行`npm install`来安装所有的依赖项。

要测试您的 awesome 模板网站，请运行:`npm run serve`。

你能看见它吗？太神奇了，成功了！干得好！

现在，让我们添加一些魔法…🔮

让我们在`src`文件夹中创建一个`scripts`目录，并将`deploy.sh`文件放在那里，看起来像这样:

```
#!/usr/bin/env bash
ls -l
ssh-keyscan -H "$1" >> ~/.ssh/known_hosts
ssh "deploy@$1" "rm -rf ~/frontend/*"
scp -r ./dist/static "deploy@$1:~/frontend/"
scp ./dist/index.html "deploy@$1:~/frontend/"
scp ./dist/service-worker.js "deploy@$1:~/frontend/" 
```

长得差不多吧？这个脚本将删除旧的前端，并复制我们的 CircleCi 创建的新文件！

这里是我们的`circle.yml`文件，我们将在项目的根目录下创建:

```
version: 2
jobs:
  deploy:
    docker:
      - image: circleci/node:latest
    steps:
      - checkout
      - run:
          name: Install dependencies
          command: npm i
      - run:
          name: Package
          command: npm run build
      - run:
          name: Deploy
          command: bash ./scripts/deploy.sh my.awesome.website 

workflows:
  version: 2
  build_and_deploy:
    jobs:
      - deploy:
          filters:
            branches:
              only: master 
```

正如您所看到的，它看起来与之前的服务器几乎一样。然而，这个版本首先构建我们的前端项目，然后执行部署。

最终的项目结构将如下所示(使用我们的新文件):

```
./frontend
├── README.md
├── babel.config.js
├── node_modules
├── circle.yml
├── scripts
│   └── deploy.sh
├── package-lock.json
├── package.json
├── public
└── src 
```

我们做到了！雅虎！

现在是最后一部分:CI 配置(本例中是 CircleCi)

* * *

# 第四部分:CircleCi🔄

* * *

一旦您使用 BitBucket 登录到 [CircleCi](https://circleci.com) ，请按照以下步骤添加您的项目:

[![Follow project](img/11da6644e65ff55c078a4a1a1a4e9fe4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h3wRcQkU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sgexwlzepavaaho7d282.png)

你还记得我们在服务器上生成的 SSH 密钥吗？现在我们需要使用它！

转到两个项目各自的设置，导航到`SSH Permissions`选项卡，将生成的私钥复制/粘贴到那里:

[![SSH keys](img/5d1db4af5eb4ca220843e893a79806bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IES3RZFS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6wjr8v25u43npn0olm0d.png)

现在尝试将任何更改推送到前端或后端项目= >这将触发一个构建，您的 Web 应用程序将自动更新:

[![First build](img/c0632dcd832f6fd5d4be6a37a46a05a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZceYJb0X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ytocgd2gfpwbt9x3u5hg.png)

我们成功了！🎉🎉🎉

[![Victory](img/4769cb7a82cbfe0b6b8d911cec0354c2.png)](https://i.giphy.com/media/lnlAifQdenMxW/giphy.gif)

* * *

# 结论🍕

* * *

这种管道适合小型个人项目，或者只是为了熟悉部署思想。确定的事情:几乎本教程的每个部分都可以升级和增强:)如果你有任何想法或发现了一个 bug(我敢肯定他们很少-不要犹豫 rase PRs！)