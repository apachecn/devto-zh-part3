# 使用 Travis CI 将 React 应用程序部署到您的服务器

> 原文：<https://dev.to/harshadranganathan/deploy-react-app-to-your-server-with-travis-ci-3212>

最初发表于[rharshad.com](https://rharshad.com/deploy-react-app-travis/)

Travis CI 是一个托管的分布式持续集成服务，用于构建和测试托管在 [GitHub](https://github.com) 的软件项目。

我们可以使用 Travis CI 测试和部署托管在 Github 上的 react 项目。

## 先决条件

*   设置一个 [Travis CI 账户](https://travis-ci.com)
*   [向 Travis CI 注册您的存储库](https://docs.travis-ci.com/user/tutorial/)

一旦您将托管在 Github 上的 react 项目注册到 Travis CI，我们就可以继续下一步，使用 Travis CI 运行测试并将其部署到您的服务器上。

## 安装 Travis CLI

我们需要允许 Travis SSH 到您的服务器，以便它可以部署构建工件。

但是，我们不能将私钥存储在 github repo 中，因为任何人都可以使用它登录到服务器。

向 Travis CI 注册的每个存储库都有自己的密钥对，其中私钥只有 Travis CI 知道，公钥对任何人都可用。

我们可以使用这个公钥来加密您的 SSH 密钥，并提交给 github。因此，只有 travis 能够解密它，并使用解密的 SSH 私有密钥登录到您的服务器。

该功能是通过 travis cli 提供的。因此，我们将通过运行下面的命令来安装它。

```
# travis cli is ruby based so we need to install ruby and other dev libraries
sudo yum install ruby -y

sudo yum install gcc g++ make automake autoconf curl-devel openssl-devel \
zlib-devel httpd-devel apr-devel apr-util-devel sqlite-devel -y

sudo yum install ruby-rdoc ruby-devel -y

# install travis cli
gem install travis 
```

如果您正在使用 git bash 在 windows 系统中设置 Travis CLI，请注意这些问题

*   [从命令行挂起 github 登录](https://github.com/travis-ci/travis-ci/issues/8250)
*   [Windows 上的文件解密失败(错误的最终块长度)](https://github.com/travis-ci/travis-ci/issues/4746)

## 生成并加密私钥

现在，我们将生成一个 SSH 密钥，并通过运行以下命令使用 travis cli 对其进行加密。

```
# travis login by providing your gihub username and password
travis login --com

# clone your react project and change directory to it
cd react-app

# create .travis.yml file
touch .travis.yml

# generate new key called "travis_rsa"
ssh-keygen -t rsa -N "" -C "React App" -f travis_rsa

# encrypt the file using your repo's public key and add it to your .travis.yml file
# must be run within your project directory
travis encrypt-file travis_rsa --add --com

# remove the key
rm travis_rsa

# stage .travis.yml and travis_rsa.enc files
git add .travis.yml
git add travis_rsa.enc 
```

如果，当你在你的项目目录中运行`encrypt-file`命令并且你的 repo 没有被自动检测，你可以如下传递 repo 名称

```
# use -r flag e.g. -r owner/project
travis encrypt-file travis_rsa --add --com -r HarshadRanganathan/react-app 
```

您的. travis.yml 文件将如下所示

```
before_install:
- openssl aes-256-cbc -K $encrypted_xxxxxx_key -iv $encrypted_xxxxxx_iv
  -in travis_rsa.enc -out travis_rsa -d 
```

您可以在 Travis CI 的项目设置下找到存储为`Environment Variables`的`encrypted_xxxxxx_key`和`encrypted_xxxxxx_iv`。

复制文件`travis_rsa.pub`中的公钥，我们接下来会用到它。

## 在您的服务器上为 Travis 创建一个新用户

既然我们已经在 repo 中存储了您的加密私钥，我们需要在您的服务器中为 Travis 创建一个新用户来部署构建工件。

我们将把之前生成的公钥添加给这个用户，这样 travis 就可以使用解密的私钥 SSH 到服务器。

例如，我们可以使用以下命令在 Cent OS 中创建新用户。

```
# create a new travis user
sudo adduser travis

# delete the password for the user
sudo passwd -d travis

# change to travis user
su - travis

# create a new directory called .ssh and restrict its permissions
mkdir .ssh
chmod 700 .ssh

# open a file in .ssh called authorized_keys 
# copy the public key which we had previously generated
# enter :x then ENTER to save and exit the file
vi .ssh/authorized_keys

# restrict the permissions of the authorized_keys file
chmod 600 .ssh/authorized_keys

exit 
```

## Setup .travis.yml

我们将告诉 travis CI 如何处理`.travis.yml`文件。我们用以下内容更新文件。

```
 sudo: true
language: node_js
node_js:
- node
git:
  quiet: true
cache: npm
before_install:
- openssl aes-256-cbc -K $encrypted_xxxxxx_key -iv $encrypted_xxxxxx_iv
  -in travis_rsa.enc -out travis_rsa -d
- chmod 600 travis_rsa
- mv travis_rsa ~/.ssh/id_rsa
- cat server.pub >> $HOME/.ssh/known_hosts
after_success:
- bash ./deploy.sh 
```

这是文件内容的解释-

*   `sudo: true`我们要求 travis 在具有`root`访问权限的虚拟机中运行构建
*   由于我们的项目需要 Node.js，我们告诉 travis ci 在安装了 Node.js 的基础设施上运行构建
*   `node_js: - node`指定使用最新的稳定 Node.js 版本
*   `cache: npm`指定缓存`node_modules`目录
*   `before_install`我们希望在安装过程之前运行的任何命令
*   `mv travis_rsa ~/.ssh/id_rsa`将解密的密钥移动到默认密钥位置
*   一旦测试通过，构建成功完成，我们要求 travis 运行我们的部署脚本。

当我们指定`language: node_js`时，travis 将在安装生命周期中运行`npm install`,在脚本生命周期中运行`npm test`。

阅读 [Node.js](https://docs.travis-ci.com/user/languages/javascript-with-nodejs/) 语言指南和[作业生命周期](https://docs.travis-ci.com/user/job-lifecycle/)了解更多详情。

### 向 SSH 添加已知主机

Travis CI 可以向~/添加条目。如果有来自 github.com、gist.github.com 或 ssh.github.com 以外的域的 git 子模块，这是必要的。

通过运行这个命令
获得服务器的公钥

```
ssh-keyscan (domain/ip_address) 
```

将公钥添加到名为`server.pub`的文件中，并推送到您的 repo。

在. travis.yml 文件中，我们指定了这个命令`cat server.pub >> $HOME/.ssh/known_hosts`,它将把您的服务器的公钥添加到 travis ci 创建的虚拟机中的已知 hosts 文件中。

## 部署脚本

现在将下面的部署脚本添加到您的项目报告中，它执行以下操作

*   仅当构建是针对主分支或 PR 时才执行脚本
*   `eval "$(ssh-agent -s)"`启动 ssh 代理会话
*   `ssh-add`添加默认键~/。ssh/id_rsa 到 ssh 身份验证代理中，以便使用 SSH 实现单点登录
*   `npm run build`生成 JS、index.html 文件的生产版本
*   `rsync`远程同步构建工件。如果已经存在，我们删除所有文件，如果不存在，我们创建父目录。

这里的`$TRAVIS_BUILD_DIR/public`表示生成构建工件(JS，index.html 文件)的位置。

```
#!/bin/bash
set -xe

if [ $TRAVIS_BRANCH == 'master' ] ; then eval "$(ssh-agent -s)"
  ssh-add
  npm run build
  rsync -rq --delete --rsync-path="mkdir -p react-app && rsync" \
  $TRAVIS_BUILD_DIR/public travis@<ip>:react-app
else echo "Not deploying, since this branch isn't master."
fi 
```

### 安全列表 Travis IP 地址

如果您在防火墙规则中设置了 SSH 限制，那么您必须将 travis 的 ip 地址列入安全列表，以便 travis 能够使用 SSH 并在您的服务器中部署工件。

参考 [Travis IP 地址](https://docs.travis-ci.com/user/ip-addresses/)列表。

因为我们已经在. travis.yml 文件中指定了`sudo: true`，所以我们必须列出`Sudo-enabled Linux` IP 地址的安全列表。

此外，建议您自己订阅通知，因为这些 IP 地址会定期更改。

现在我们已经万事俱备了。每当您更新`master` branch 时，Travis CI 将生成生产构建工件，并在测试通过的情况下将它们部署到您的服务器上。