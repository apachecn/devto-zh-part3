# 通过杜库部署凤凰号

> 原文：<https://dev.to/mplatts/deploying-phoenix-via-dokku-1gip>

[![cover](img/fe49500d20c62374be23d0fabfcd8779.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o9gtyKXQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdka3etm4kj12jwkloq5.png)

尽早部署，经常部署。

我喜欢制作有潜力成为真正的创业公司的 MVP。显然我想节省成本。非科技企业家通常会在 Amazon EC2 上安装一台服务器，附带一个 Postgres 实例，并最终每月支付 100 多美元。这是因为他们雇佣了一个不在乎成本的开发人员来做这件事。

你应该只在你的创业得到认可并且有吸引力的时候支付这么多。在此之前，你基本上是在寻找产品市场适合的模式。不太可能有很多人以这种模式使用你的网站，你最好是每月 5 美元的 VPS。一旦你达到牵引力，你可以很容易地升级-出口你的数据库，并升级到亚马逊或 GCP。如果你的应用不太复杂的话，每月 5 美元的 Phoenix 可以处理大量的流量。

我不太喜欢开发运营工作，所以我打算使用 Dokku，它基本上模仿 Heroku，因为它充当 PaaS，您只需进行 git 远程推送来部署您的代码。我用我的 [Phoenix 样板模板](https://petal.build)构建的所有项目都使用这个。

# 在 Vultr 上部署凤凰

请注意，这可能适用于任何 VPS(如数字海洋)。我使用 Vultr 是因为它有一个澳大利亚位置选项。

使用他们的[安装指南](https://hexdocs.pm/phoenix/installation.html#content)创建一个空白的 phoenix 应用程序。

运行服务器`mix phx.server`

[![Phoenix hello world screen](img/d8cc1b454760cda2aa2af92c36146cb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oRw84Z_Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tb37uiccylyhdwjabxmy.png)

让 Ubuntu 在云中的某个地方运行起来。对于 Vultr，只需注册并点击蓝色大加号按钮。

1.  服务器位置:选择一个离你或你的目标受众最近的。
2.  服务器类型:Ubuntu 16.04(可能会尝试更新的版本——由你决定)
3.  服务器大小:不妨从每月 5 美元开始
4.  附加功能:全部未选中
5.  启动脚本:留空
6.  ssh 密钥:添加您的计算机的 SSH 密钥(`cat ~/.ssh/id_rsa.pub`)来查看它。或者，如果没有 SSH 密钥，可以查找它们。
7.  服务器主机名和标签:任何你想要的

安装完成后，点击它并获取 IP 地址和密码，这样我们就可以登录了。

我的是 45.76.112.118。

在您的终端登录:

```
ssh root@45.76.112.118

Are you sure you want to continue connecting (yes/no)? yes
root@45.76.112.118's password: 
```

Enter fullscreen mode Exit fullscreen mode

复制并粘贴您的密码。

既然我们已经进入了 Ubuntu 实例，我们应该首先确保所有的 linux 包都是最新的。

```
sudo apt update
sudo apt upgrade 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以安装 [dokku](http://dokku.viewdocs.io/dokku/) 了。请务必检查这些文件，因为版本可能比我正在使用的版本更高。

```
wget https://raw.githubusercontent.com/dokku/dokku/v0.20.4/bootstrap.sh;
sudo DOKKU_TAG=v0.20.4 bash bootstrap.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在，在你的浏览器中，复制并粘贴你的服务器的 IP 地址到地址栏中，然后按照 web 安装程序进行操作。

[![Dokku web installer](img/d53a753f1d9ea66e95975c1a79ea702b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u-Lg5sMK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lq5hj0uj2edxobqz9jw5.png)

就像之前一样添加你的 ssh 密钥- `cat ~/.ssh/id_rsa.pub`。剩下的我就默认了。单击完成。

既然安装了 Dokku，我们就可以开始使用它的命令行界面(CLI)来设置数据库并将其链接到我们的应用程序。

仍然在您的远程服务器上运行这些命令(注意，我将我的应用程序称为卡跟踪器)。

```
# Create a dokku app called card-tracker
dokku apps:create card-tracker

# Dokku has lots of plugins - this one helps us create a postgres db
dokku plugin:install https://github.com/dokku/dokku-postgres.git

# Use the pluging to create a db instance called 'db'
dokku postgres:create db

# Linking just creates a global ENV config variable in card-tracker called 'DATABASE_URL`
dokku postgres:link db card-tracker 
```

Enter fullscreen mode Exit fullscreen mode

您的 dokku 应用程序具有可设置的全局环境配置变量。您使用这些来设置您不想提交到 git 中的东西——重要的东西，如您的数据库凭证、第三方 api 密钥等。

您可以使用`dokku config:export card-tracker`检查当前设置的环境变量。应该只有一套——`DATABASE_URL`。这是你跑`dokku postgres:link db card-tracker`时设定的。

回到你的 phoenix 应用代码库，看看文件`prod.secret.exs`:

[![Prod secret file](img/9a48fd599a9928fb2793cae881ed373d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sEnXBsrE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xcr0jjgc4p28w09583q.png)

我们可以看到，默认情况下，Phoenix 会寻找一个名为`DATABASE_URL`的 ENV 变量，并设置您的数据库使用它作为凭证。方便。

端点配置被奇怪地拆分为`prod.exs`和`prod.secret.exs`(当您编写`config :app, AppWeb.Endpoint, blah`时，它只是添加到现有的配置，而不是覆盖它)。

为了简单起见，我最终删除了`prod.exs`中的端点配置，并将其完全放在了`prod.secret.exs`中。

```
# I deleted this from prod.exs:
config :app, AppWeb.Endpoint,
  url: [host: "example.com", port: 80],
  cache_static_manifest: "priv/static/cache_manifest.json" 
```

Enter fullscreen mode Exit fullscreen mode

并且在文件`prod.secret.exs` :

```
config :app, AppWeb.Endpoint,
  cache_static_manifest: "priv/static/cache_manifest.json",
  http: [port: String.to_integer(System.get_env("PORT") || "4000")],
  url: [
    scheme: "https",
    host: System.get_env("WEB_HOST"),
    port: 443
  ],
  force_ssl: [rewrite_on: [:x_forwarded_proto]],
  secret_key_base: secret_key_base 
```

Enter fullscreen mode Exit fullscreen mode

现在回到我们的远程服务器，我们可以设置`WEB_HOST`全局环境变量:

```
dokku config:set --no-restart card-tracker WEB_HOST=45.76.112.118 
```

Enter fullscreen mode Exit fullscreen mode

还回头看了下底部的`prod.secret.exs`:

```
secret_key_base =
  System.get_env("SECRET_KEY_BASE") ||
    raise """
    environment variable SECRET_KEY_BASE is missing.
    You can generate one by calling: mix phx.gen.secret
    """

config :app, AppWeb.Endpoint,
  http: [:inet6, port: String.to_integer(System.get_env("PORT") || "4000")],
  secret_key_base: secret_key_base 
```

Enter fullscreen mode Exit fullscreen mode

我们应该通过运行`mix phx.gen.secret`来生成一个`SECRET_KEY_BASE`。在本地计算机(不是远程计算机)上的控制台中执行该操作。

然后在遥控器上设置:

```
dokku config:set --no-restart card-tracker SECRET_KEY_BASE="TGISHf+6hJiQgEuroRY29k8IWnmY9MzggnPY86x16AYkJnMoPZDBcRuVgiUkT/Zu" 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你再次运行`dokku config:export card-tracker`，你应该有环境变量:

*   数据库 _URL
*   秘密密钥库
*   WEB _ 主机

现在回到 phoenix 应用程序中的本地终端。我们需要添加我们的服务器作为远程推送。

```
# Add a remote called dokku. In the dokku setup it created a user called dokku
git remote add dokku dokku@45.76.112.118:card-tracker 
```

Enter fullscreen mode Exit fullscreen mode

尽管如此，我们仍然没有做好部署的准备。就像我说的，Dokku 就像 Heroku——甚至 Heroku 也不支持所有的语言和框架。相反，它依赖于“构建包”，这基本上是不同环境的安装脚本。凤凰有[一个。如果你提供静态内容(比如 JS/CSS)，那么你还需要一个](https://github.com/HashNuke/heroku-buildpack-elixir)[凤凰静态构建包](https://github.com/gjaldon/heroku-buildpack-phoenix-static)。

有几种方法可以告诉 Dokku 使用什么构建包，但是我们将使用在 Phoenix 项目的根目录下创建一个`.buildpacks`文件的方法。

在你身上。buildpacks 文件添加:

```
https://github.com/HashNuke/heroku-buildpack-elixir.git
https://github.com/gjaldon/heroku-buildpack-phoenix-static 
```

Enter fullscreen mode Exit fullscreen mode

elixir buildpack 允许您设置 elixir 和 erlang 版本。为此，在 Phoenix 项目的根目录下创建另一个名为`elixir_buildpack.config`的文件，并添加内容:

```
elixir_version=1.9.0
erlang_version=21.1.1 
```

Enter fullscreen mode Exit fullscreen mode

截至 2019 年 7 月 23 日，上述版本对我有效——也许谷歌最新版本，或者用`brew info erlang`和`brew info elixir`检查你的版本。尽管我发现如果我使用最新版本，部署过程会失败。因此，如果您得到随机部署失败，然后将版本移回，直到它的工作。

最后，在根目录下创建另一个名为`Procfile`的文件。在它里面加上:

```
web: ./.platform_tools/elixir/bin/mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

在这里阅读 Procfiles [。他们是英雄，也是杜库的荣耀。我真的不知道为什么我们需要添加这个，但我在某个论坛上找到了它，因为没有它我的服务器就无法工作。elixir buildpack 说“如果你的应用没有 Procfile，默认的 web 任务混合 run - no-halt 将会运行。”所以，也许那个`mix run --no-halt`已经不够好了。](https://devcenter.heroku.com/articles/procfile)

无论如何，提交这 3 个文件，然后让我们尝试部署！

```
git add -A
git commit -m 'Add dokku config files'
git push dokku master 
```

Enter fullscreen mode Exit fullscreen mode

它将打印出一堆安装日志——可能需要一段时间。它应该以类似于:
的内容结束

```
=====> Application deployed:
       http://45.76.112.118:31894 
```

Enter fullscreen mode Exit fullscreen mode

将该 URL 粘贴到您的浏览器中(如果在 iterm2 中，则按下 CMD 键),您应该会看到您的应用程序在线。

## 迁徙

默认情况下，我们的 elixir buildpack 不运行 Phoenix 迁移。要运行它们，我们需要使用 Dokkus post deploy hook。为此，我们在 Phoenix 文件夹的根目录下添加了另一个名为`app.json` :
的文件

```
{  "scripts":  {  "dokku":  {  "postdeploy":  "mix ecto.migrate"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

提交并部署，您应该会在日志中看到该命令正在运行。

## 域

默认情况下，Dokku 只是使用 IP 地址和端口向世界展示你的应用程序。

你可以添加另一个应用程序`dokku apps:create blah`,它会在另一个端口上运行。

[http://45.76.112.118:31894](http://45.76.112.118:31894)=>追踪者
T3】http://45.76.112.118:32939=>布拉

总之，我在 Godaddy 上注册了 cardtracker.com.au，我希望这个域名指向我的 IP 地址。

为此，我进入 Godaddy 中的 DNS 设置，确保没有 A 记录，只有一个除外:

类型:A
名称:@
值:45.76.112.118

这意味着 cardtracker.com.au 现在指向我的远程服务器。因为我希望 www 也指向那里，所以我可以创建一个 cname 记录来指向我的根记录:

类型:CNAME
名称:www
数值:@

现在，在远程服务器上将域添加到 Dokku:

```
dokku domains:add card-tracker cardtracker.com.au
dokku domains:add card-tracker www.cardtracker.com.au 
```

Enter fullscreen mode Exit fullscreen mode

等待一段时间，让更改传播开来。你可以用`host -a cardtracker.com.au`查看终端发生了什么。

```
Trying "cardtracker.com.au"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 20867
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;cardtracker.com.au.        IN  ANY

;; ANSWER SECTION:
cardtracker.com.au. 599 IN  A   45.76.112.118
cardtracker.com.au. 3599    IN  NS  ns69.domaincontrol.com.
cardtracker.com.au. 3599    IN  NS  ns70.domaincontrol.com.
cardtracker.com.au. 3599    IN  SOA ns69.domaincontrol.com. dns.jomax.net. 2019050807 28800 7200 604800 600 
```

Enter fullscreen mode Exit fullscreen mode

记得用新主机更新 WEB_HOST 环境变量。

```
dokku config:set card-tracker WEB_HOST=cardtracker.com.au 
```

Enter fullscreen mode Exit fullscreen mode

## 将 www 重定向到 root

当有人点击[www.cardtracker.com.au](http://www.cardtracker.com.au)时，我们希望响应是 301 重定向到 cardtracker.com.au。我们需要一个服务器来给出这种响应——幸运的是 Dokku 有一个[插件](https://github.com/dokku/dokku-redirect)来为我们管理这个。

```
dokku plugin:install https://github.com/dokku/dokku-redirect.git
dokku redirect:set card-tracker www.cardtracker.com.au cardtracker.com.au 
```

Enter fullscreen mode Exit fullscreen mode

## SSL

SSL 是当今的标准。Dokku 通过 [dokku-letsencrypt 插件](https://github.com/dokku/dokku-letsencrypt) :
让一切变得简单

```
dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
dokku config:set --no-restart card-tracker DOKKU_LETSENCRYPT_EMAIL=your_email@mail.com

# Free SSL for 90 days
dokku letsencrypt:enable card-tracker

# Add a monthly CRON job to refresh your free SSL certificate each month
dokku letsencrypt:cron-job --add

# View your SSL status
dokku letsencrypt:ls 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够使用 https 查看您的站点了。请确保您在 Chrome 隐姓埋名检查-我的不工作在我的正常铬，可能是由于一些缓存问题。

## 读取数据库

您可以使用任何数据库工具连接到远程数据库。我用[表加](https://tableplus.com/)。

运行这些命令:

```
dokku postgres:expose db
dokku postgres:info db 
```

Enter fullscreen mode Exit fullscreen mode

在输出中你可以找到这些行:

```
Exposed ports:       5432->24321  
postgres://postgres:xxxxxxxxxxxxxxxx@dokku-postgres-db:5432/db 
```

Enter fullscreen mode Exit fullscreen mode

格式如下:

```
postgres://USERNAME:PASSWORD@dokku-postgres-db:5432/DATABASE_NAME 
```

Enter fullscreen mode Exit fullscreen mode

所以总的来说，我们有这样的信息:

*   IP 地址:您的服务器的 IP 地址
*   端口:24321
*   用户名:postgres
*   密码:xxxxxxxxxxxxxxxxxxx
*   数据库名称:db

只要把它插入你的数据库应用程序，你就可以走了。

## 将数据库备份到 S3

注册亚马逊。
创建一个桶“卡-追踪器-备份”-没有公共访问。
转到 IAM，创建一个对 S3 具有编程访问权限和完全访问权限的新用户。
抓住访问键和秘密。

```
dokku postgres:backup-auth db AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY 
```

Enter fullscreen mode Exit fullscreen mode

测试将`db`服务备份到 AWS 上的`BUCKET_NAME`桶。

```
dokku postgres:backup db BUCKET_NAME

=> 2021-05-06-00-59-06: The backup for xxx finished successfully. 
```

Enter fullscreen mode Exit fullscreen mode

计划备份。CRON_SCHEDULE 是一个 crontab 表达式，例如“0 3 * * *”表示每天凌晨 3 点

```
dokku postgres:backup-schedule db "0 3 * * *" BUCKET_NAME 
```

Enter fullscreen mode Exit fullscreen mode

## 文件上传

我发现如果你允许文件上传，那么你需要在 nginx 配置中增加文件上传的最大限制。你可以登录到服务器并运行:

```
echo 'client_max_body_size 50m;' > /home/dokku/<APP NAME>/nginx.conf.d/upload.conf

dokku ps:restart <APP NAME> 
```

Enter fullscreen mode Exit fullscreen mode

所以对我来说:

```
echo 'client_max_body_size 50m;' > /home/dokku/card-tracker/nginx.conf.d/upload.conf

dokku ps:restart card-tracker 
```

Enter fullscreen mode Exit fullscreen mode

## 安全

注意:请自行承担以下风险。我建议你自己研究安全性，而不是相信这个。

看看 VPS Harden T1，它将使你的服务器更难被黑客破解。

我在远程服务器上运行了安装命令:

```
sudo git clone https://github.com/akcryptoguy/vps-harden.git && cd vps-harden && sudo bash get-hard.sh 
```

Enter fullscreen mode Exit fullscreen mode

当被询问时，我创建了一个非 root 用户，并禁用了密码登录和 root 访问。我将 SSH 端口保持在 22，因为我担心它可能会干扰将代码推送到 dokku。

后来，我换了新的登录名`su - newuser`。
然后我添加了我的 ssh 公钥:

```
mkdir ~/.ssh && touch ~/.ssh/authorized_keys
sudo chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
sudo vim ~/.ssh/authorized_keys 
```

Enter fullscreen mode Exit fullscreen mode

你可以在本地用`cat ~/.ssh/id_rsa.pub`获得你的 ssh 密钥。

然后运行这个命令来重启 ssh:

```
sudo systemctl restart sshd 
```

Enter fullscreen mode Exit fullscreen mode

保持此选项卡打开，现在尝试从您的本地计算机 ssh 进入。`ssh newuser@ipaddress`。如果失败，请继续排除故障，直到您确定可以登录...因为如果你关闭了另一个打开的标签，你就永远失去了访问服务器的权限，因为你禁用了密码登录。

一旦您可以 ssh 到您的新用户，然后您可以用`su -`切换回根用户并运行 dokku 命令。

## 服务器监控

如果你想用 netadata 监控你的服务器:[https://www . vultr . com/docs/installing-net data-on-Linux-multiple-distros](https://www.vultr.com/docs/installing-netdata-on-linux-multiple-distros)