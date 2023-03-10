# 以 5 美元的价格与全世界分享您的应用程序

> 原文：<https://dev.to/zeerorg/sharing-your-apps-with-the-world-for-5-1p80>

毫无疑问，Raspberry Pi 在与完整的服务器竞争方面有点力不从心，但我们的许多爱好应用程序从未见过互联网的光芒，或者我们的测试项目尚未为云世界做好准备，是这个小小奇迹的最佳匹配。你学到了很多关于服务器如何工作的知识，并以尽可能低的价格在互联网上托管了一个项目。

## 我在我的树莓派上运行什么

我目前有一个 [OpenFaas](https://docs.openfaas.com/) 实例运行在我的 Raspberry Pi 上，为两个应用服务，首先是 Github Webhook 端点，其次是基于 [EFF 列表](https://www.eff.org/dice)的密码生成器。OpenFaas 在 Docker swarm 上运行，我使用一个也作为 Docker 容器运行的 [Portainer](https://www.portainer.io/) ui 来管理 Docker swarm。我用[glasses](https://nicolargo.github.io/glances/)服务器监控资源使用情况。我计划添加一个 [Jupyter Hub](https://jupyterhub.readthedocs.io/en/stable/) 实例，并将所有这些放在一个 NGINX 代理服务器后面，可能带有 auth。

是的，我的设置对于像 raspberry pi 这样的小机器来说有点太过了，但是它教会了我许多关于许多不同软件的事情和这个小怪物的可能性。

您可以通过此[链接](https://home.zeerorg.site:3333/)访问密码生成器。

## 先决条件

1.  5 美元树莓派
2.  域名(可选，但强烈建议 https 使用)

## 图表时间

[![Home server setup](img/68fbbfb4d327934adda8fb8d6dd53358.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aHLqhAnR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zeerorgprocessedblog.blob.core.windows.net/photos/rpiserver.jpg)

## 反向代理——因为，安全

建立一个反向代理服务器可能看起来有点痛苦，但它提供了一个安全的网关，因为在互联网上暴露您的家庭网络和应用程序总是有风险的。如果您有域，也可以使用它来设置 SSL。我更喜欢 Nginx，因为它有很多指南，并且设置简单(Apache 的配置让我害怕)。简单 HTTP 反向代理:

```
server {
  listen 443;
  location / {
    proxy_pass http://locahost:[:PORT]/;
  }
} 
```

正在部署:

```
sudo nano /etc/nginx/sites-enabled/default
# Copy the config then Ctrl+x to save
sudo nginx -s reload 
```

另请参见:[使用 nginx 的 SSL](https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04)

## 正确启动您的应用程序

在忽略安全性之后，你可能犯的第二大错误是假设你的 raspberry pi 大部分时间都在运行，如果它重新启动，你将再次启动你的服务。不要犯这个错误，建立一个自我启动的服务是微不足道的，这是你为了内心的平静所能做的最好的事情。参考[这个官方的 Raspbian 指南](https://www.raspberrypi.org/documentation/linux/usage/systemd.md)关于建立自助服务。

app.service

```
[Unit]
Description=My Applicaiton
After=network.target

[Service]
ExecStart=/usr/bin/python3 -u main.py
WorkingDirectory=/home/pi/applicaiton
Restart=always
User=pi

[Install]
WantedBy=multi-user.target 
```

复制并启用服务

```
sudo cp app.service /etc/systemd/system/app.service
sudo systemctl start app.service
sudo systemctl enable app.service 
```

## 手臂走过了漫长的道路

人们在去托管他们的服务之前的另一个担心是，为不同的架构构建将是一件痛苦的事情，或者他们将不得不屈服于撒旦来让他们的应用程序运行。几年前是这样，但现在不是了！为不同的架构构建您的应用程序就像为编译语言提供一个构建时标志，或者安装解释语言运行时一样简单。像 python 和 javascript 这样的解释语言可以使用现有的包管理器轻松安装。

静态编译的语言可以在你当前的机器上为 ARM 交叉编译。例如:

*   Golang: `env GOOS=linux GOARCH=arm GOARM=5 go build`
*   。网络核心:`dotnet publish -r linux-arm`