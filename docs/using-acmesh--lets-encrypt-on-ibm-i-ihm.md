# 使用 acme.sh &让我们在 IBM i 上加密

> 原文：<https://dev.to/worksofbarry/using-acmesh--lets-encrypt-on-ibm-i-ihm>

我最近在 IBM i 上部署了一个 Node.js 应用程序，并希望对我们的证书使用 Let's Encrypt。我们一路上遇到了一些颠簸。这篇文章将介绍安装 acme.sh 和实际生成证书的过程。

## 什么是 acme.sh

Acme.sh 是一个开源 shell 脚本，可以自动调用 Let's Encrypt 来生成一个证书，供您在应用程序中使用。GitHub 存储库上的引用是“这可能是自动发布和更新来自 Let's Encrypt 的免费证书的最简单和最智能的 shell 脚本。”。回购可以在[https://github.com/Neilpang/acme.sh/](https://github.com/Neilpang/acme.sh/)找到。

首先，您应该用 SSH shell 安装 acme.sh。提醒一下:

1.  您可以在 5250 命令行上用`STRTCPSVR SERVER(*SSHD)`启动 SSH 守护进程。
2.  然后，您可以从您的机器终端使用`ssh user@youribmi`登录到 IBM i。
3.  您将希望在 bash shell 中运行这些命令。

要安装:

1.  克隆回购:`git clone https://github.com/Neilpang/acme.sh.git`
2.  运行安装脚本:

```
cd acme.sh
./acme.sh --install --force
alias acme.sh=~/.acme.sh/acme.sh 
```

要使用 acme.sh，您还需要将您的域 DNS 设置设置为指向您的 IBM i。

## 无法初始化 api(错误代码:77)

当我这样做的时候，我正在跟随一些亚伦巴特尔写的旧幻灯片。当我第一次尝试安装证书时，我得到一个 libcurl 错误。

```
bash-4.2$ export CURL_CA_BUNDLE=~/certs/cacert.pem
bash-4.2$ acme.sh --issue -d website.com -w /mywebsite/public
Please refer to https://curl.haxx.se/libcurl/c/libcurl-errors.html for error code: 77
Can not init api. 
```

我想我得到这个错误是因为我引用的文件(cacert.pem)不存在。经过一番谷歌搜索，[我在 IbmiToolkit 仓库上发现了这个 GitHub 问题](https://github.com/zendtech/IbmiToolkit/issues/46)。在这个主题上有两个重要的注意事项。

1.  默认情况下，Linux 发行版通常会提供所讨论的证书包，但 IBM i 自然不会这样做。/s
2.  phpdave [分享这个关于寻找本地 CA 的伪代码](https://gist.github.com/phpdave/8d594cfcfd6c11e704af)。

phpdave 的脚本实际上在第 19 行包含了一个重要的链接:[http://curl.haxx.se/ca/cacert.pem](http://curl.haxx.se/ca/cacert.pem)。我想:如果我下载这个证书并让 curl 使用它，那么我想知道它是否能解决我的问题。

```
bash-4.2$ wget http://curl.haxx.se/ca/cacert.pem
bash-4.2$ export CURL_CA_BUNDLE=/home/USER/cacert.pem
bash-4.2$ acme.sh --issue -d website.com -w /mywebsite/public
[Thu Jan 24 15:04:02 EST 2019] Creating domain key
.... 
```

## 我们如何对您的服务器进行加密认证

请注意，在 ache.sh 命令中，我们包含了一个指向我们网站的 URL 和一个指向公共文件夹的路径。我们这样做是因为 acme.sh 将

1.  在公共文件夹中创建一个目录:`/mywebsite/public/.well-known/acme-challenge`
2.  在`acme-challenge`文件夹中创建一个随机文件，其中包含一个密钥
3.  告诉 Let's Encrypt 将您在`website.com/.well-known/acme-challange/randomkey`的密钥与来自 acme.sh 脚本的密钥进行比较
4.  如果成功，你的密钥就会生成。

这确实意味着“public”文件夹应该可以在您的 web 服务器上访问。例如，如果您正在使用 Express 构建 Node.js 应用程序，并且它具有以下文件夹结构:

```
mywebsite/
    index.js
    node_modules/
    public/
        index.html 
```

您的 index.js 会告诉 web 服务器公共目录是用于静态文件的:`app.use(express.static('public'))`

因此，当 acme.sh 生成`/mywebsite/public/.well-known/acme-challenge/randomkey`时，可以通过`website.com/.well-known/acme-challange/randomkey`访问它

## 使用生成的密钥

如果运行 acme.sh 时一切都成功，它应该会为您生成一个密钥和证书:

```
bash-4.2$ acme.sh --issue -d website.com -w /mywebsite/public/
…
Your cert is in  /home/USER/.acme.sh/website.com/website.com.cer
Your cert key is in  /home/USER/.acme.sh/website.com/website.com.key
The intermediate CA cert is in  /home/USER/.acme.sh/website.com/ca.cer
And the full chain certs is there:  /home/USER/.acme.sh/website.com/fullchain.cer 
```

然后，您可以将这些密钥复制到对您有用的位置:`cp /home/USER/.acme.sh/website.com/ /mywebsite/letsencrypt`。在 Node.js Express 应用程序中，您可以在创建 HTTPS 服务器时引用密钥和证书:

```
var express = require('express');
var https = require('https');
var http = require('http');
var fs = require('fs');

// This line is from the Node.js HTTPS documentation.
var options = {
  key: fs.readFileSync(path.join('letsencrypt', 'website.com.key')),
  cert: fs.readFileSync(path.join('letsencrypt', 'website.com.cer'))
};

// Create a service (the app object is just a callback).
var app = express();

// Create an HTTP service.
http.createServer(app).listen(80);
// Create an HTTPS service identical to the HTTP service.
https.createServer(options, app).listen(443); 
```

## `ERR_SSL_VERSION_INTERFERENCE`在铬

我注意到，当我们在应用程序中获得证书时，网站无法在 Chrome 中加载，并出现`ERR_SSL_VERSION_INTERFERENCE`错误。经过一些研究(值得信赖的谷歌！)看起来 Node.js 8 不支持 TLS 1.3(正如这里的[第二段所提到的)。](https://nodesource.com/blog/node-js-security-release-summary-august-2018/)

幸运的是，我们在 IBM i 上也有 Node.js 10——所以只需卸载 Node.js 8 并安装 node . js 10(当然还有 yum！)我们可以绕过这个问题。如果升级到 Node.js 10，可能还需要更新一些包。