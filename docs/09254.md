# Docker 用 nginx 或 Caddy 编写本地 HTTPS

> 原文：<https://dev.to/hugo__df/docker-compose-local-https-with-nginx-or-caddy-5emk>

通过利用 HTTPS 反向代理和 [mkcert](https://github.com/FiloSottile/mkcert) 在本地使用 TLS/SSL 在 Docker 上为您的 Docker 化应用程序提供服务的解决方案。

Caddy 和 nginx 的例子有:[github.com/HugoDF/docker-compose-local-https](https://github.com/HugoDF/docker-compose-local-https)

## 为什么要在本地与 HTTPS 合作开展你的项目？

在大多数情况下，您的生产站点将启用 TLS/SSL，即。在 HTTPS 上空。这往往与您的应用程序无关，即 SSL 端接是在入口/反向代理/负载平衡器级别处理的。

然而，您的应用程序可能依赖于它运行在 SSL 连接上的假设。例如，`Set-Cookie`头有一个`Secure`属性，这意味着 cooke 将只通过 TLS/SSL 连接发送。这意味着在本地，你需要代码来支持通过非 TLS/SSL 连接发送 cookie，换句话说就是`if environment = local => don` `'` `t enable Secure`。

## 由 12 因素应用程序支持的方法

SSL/TLS/HTTPS 与应用程序无关。这是部署平台的问题。

[“开发/生产平价”](https://12factor.net/dev-prod-parity)是我们关心的 12 因素原则。

它归结为“我们的生产应用程序运行在 HTTPS 上(使用 SSL/TLS)，我们应该运行在本地，开发和 HTTPS 上的所有托管环境上(使用 SSL/TLS)”。在某个地方有一个开关说“这是本地的，通过 HTTP 运行”违反了[“开发/生产奇偶校验”](https://12factor.net/dev-prod-parity)原则。

换句话说，假设我们托管在 Heroku/Dokku 这样的 PaaS 上，在 nginx/Caddy 这样的反向代理或生产中的负载平衡器产品(Google Cloud Load Balancing，AWS ELB)后面。这些系统为我们处理证书和 SSL 终端，本地我们应该有代码/基础设施来做同样的事情。

## 为什么我们不能用让我们加密

“让我们加密”的官方说法是

> [对于本地开发部分]最佳选择:生成您自己的证书，可以是自签名的，也可以是由本地根用户签名的，并在您的操作系统的信任存储中信任它。然后在本地 web 服务器上使用该证书。详见下文。
> 
> 参见[letsencrypt.org/docs/certificates-for-localhost/](https://letsencrypt.org/docs/certificates-for-localhost/)

Let's Encrypt 旨在为托管应用程序的域自动生成可信证书。要做到这一点，您必须证明您控制了域…这对于 localhost 来说是不可能的(或者说是不可能的)。

## 本地信任证书的 mkcert

是“一个简单的零配置工具，可以用你喜欢的任何名字创建本地可信的开发证书。”。

`mkcert`的前提是，由于您控制着您的机器，您可以连接到系统根(受信任的根证书颁发机构)存储，添加一个证书颁发机构(只存在于本地),并使用它来生成您的机器将信任的证书。

> 使用来自真实证书颁发机构(ca)的证书进行开发可能是危险的或不可能的(对于像`localhost`或`127.0.0.1`这样的主机)，但是自签名证书会导致信任错误。管理自己的 CA 是最好的解决方案，但通常涉及到晦涩难懂的命令、专业知识和手动步骤。
> 
> mkcert 自动在系统根存储中创建并安装本地 CA，并生成本地信任的证书。
> 
> 从 [mkcert 文档](https://github.com/FiloSottile/mkcert#mkcert)

要安装`mkcert`，请根据您的平台查看[github.com/FiloSottile/mkcert#installation](https://github.com/FiloSottile/mkcert#installation)。

一旦安装，你应该运行:
`mkcert -install`

## 跳到代码

### 先决条件

*   [坞站桌面](https://www.docker.com/products/docker-desktop)(或坞站+复合坞站)
*   [mkcert](https://github.com/FiloSottile/mkcert)
*   修改您的`/etc/hosts`文件，使其具有以下条目(`foo.test`是我们将通过 HTTPS 为其提供流量服务的域)。

```
127.0.0.1 foo.test 
```

### 关于 Caddy 和 nginx

> Caddy 是带有自动 HTTPS 的 HTTP/2 web 服务器。
> [https://caddyserver.com/](https://caddyserver.com/)
> 
> nginx 是一个 web 服务器，也可以用作反向代理、负载均衡器、邮件代理和 HTTP 缓存。
> 维基百科

Caddy 的卖点是它有很好的默认设置，并且很容易配置。这是一个 HTTP web 服务器，默认为 HTTP/2 和 HTTPS，这是现代的最佳实践。它可以使用 Let's Encrypt 自动为您生成证书。

nginx 更成熟，性能上 edges 稍逊一筹。然而，它的配置语法可能是一个痛苦，它的缺省值不是最先进的:SSL 终端(HTTPS)和 HTTP/2 必须手动启用，Let's Encrypt 集成必须配置。

对于本地开发来说，这两者都可以，我们不太可能处于荒谬的负载之下，而且 Caddy 和 nginx 都有基于 [docker-gen](https://github.com/jwilder/docker-gen) 的代理解决方案，可以从 docker 容器标签生成配置文件，而不必编写我们自己的`nginx.conf`和`Caddyfile`

在下面几节中，我们将看到如何用 Caddyfile 设置 Caddy，以及基于 docker-gen 的解决方案。

### 一个虚拟的 Dockerized 节点应用程序服务于 HTTPS

我们将要反向代理的应用程序是一个简单的节点应用程序，您可以在[github . com/Hugo df/docker-compose-local-https/tree/master/caddy/app](https://github.com/HugoDF/docker-compose-local-https/tree/master/caddy/app)、[github . com/Hugo df/docker-compose-local-https/tree/master/caddy-gen/app](https://github.com/HugoDF/docker-compose-local-https/tree/master/caddy-gen/app)和[github . com/Hugo df/docker-compose-local-https/tree/master/nginx/app](https://github.com/HugoDF/docker-compose-local-https/tree/master/nginx/app)找到。

这是一个使用 Node.js 的`http`模块(`index.js` ):
的单个文件“Hello World”

```
const http = require('http')

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello world');
});

server.listen(process.env.PORT) 
```

`Dockerfile`同样简单。这是最佳实践的混合，比如设置一个`WORKDIR`，使用`node:10`将`NODE_ENV`投入生产(不是 Alpine😛因为这在你依赖 C/C++)的时候就爆炸了)，还有不太好的做法，比如在 docker 文件中硬编码`PORT`(至少在应用程序中没有硬编码)。

```
FROM node:10

WORKDIR /node/app

COPY ./index.js /node/app

ENV NODE_ENV production

ENV PORT 8080
EXPOSE 8080

CMD ["node", "index.js"] 
```

使用`PORT=5000 node index.js`和`curl`运行应用程序，结果如下:

```
$ curl http://localhost:5000
Hello world 
```

目标是现在能够运行`curl https://foo.test`并得到相同的响应。

我们将在所有示例中使用同一个应用程序。

### 用 Caddy 和 Caddyfile

#### 证书

Caddy 希望`domain.tld`的证书在文件名`domain.tld.pem`下(对于公钥，即。证书)和`domain.tld-key.pem`(对于私钥，即。钥匙)。

我们将手动编写 Caddy `tls`指令，因此格式并不太重要，但是我们将遵循 Caddy 存储证书的惯例。有关本指令的文件，请参见 https://caddyserver.com/docs/tls。

这些是`mkcert`生成它们时使用的名称，因此我们可以直接复制它们。假设`mkcert foo.test`已经成功运行，在`init.sh`脚本中我们有以下部分:

```
cp ./foo.test.pem ./caddy/certs
cp ./foo.test-key.pem ./caddy/certs 
```

#### 根据主机转发请求的 Caddyfile

> [Caddyfile](https://caddyserver.com/tutorial/caddyfile) 是一个配置 Caddy 的文本文件。它被设计成易于打字并且不易出错。
> 
> [https://caddyserver.com](https://caddyserver.com)

由于 Caddyfile 是一种为开发人员工效学设计的格式，我们将看看如何将我们的 Caddy 服务器设置为具有 SSL 终端的反向代理(即在应用程序不知道的情况下通过 HTTPS 提供服务)。

这个文件如下，你可以在[github . com/Hugo df/docker-compose-local-https/blob/master/caddy/caddy file](http://github.com/HugoDF/docker-compose-local-https/blob/master/caddy/Caddyfile)找到

`proxy / http://web:8080`块告诉 Caddy 将代理流量反向发送到端口 8080 上的`web`主机。`web`将是我们所谓的应用服务，Docker Compose 根据通过 Docker Compose 运行的其他服务的名称来处理名称解析。

```
foo.test {
    log stdout
    # Mkcert - https://github.com/FiloSottile/mkcert
    tls /root/certs/foo.test.pem /root/certs/foo.test-key.pem

    proxy / http://web:8080 {
        transparent
        header_upstream Host  "foo.test"
    }
} 
```

#### 坞站复合配置

这是完整的 Docker Compose v3 文件，使用我们的配置和证书，让我们的节点应用程序作为反向代理在 Caddy 后面运行。

需要注意的 3 个重要步骤是:

1.  在`volumes`中，将`certs`安装到`/root/certs`上，这是我们在 Caddyfile 文件中指向的位置
2.  在`volumes`中，将`Caddyfile`安装到`/etc/Caddyfile`，这样我们的 Caddyfile 作为主配置被加载
3.  在`ports`中，将主机端口 443 转发到 2015。端口 2015 是默认端口球童监听 HTTPS 交通，我们正在绑定我们的机器的

```
version: '3'
services:
  caddy:
    image: "abiosoft/caddy:latest"
    volumes:
      - ./certs:/root/certs # to sync mkcert certificates to Caddy
      - ./Caddyfile:/etc/Caddyfile  # to mount custom Caddyfile
    ports:
      - "443:2015"
    depends_on:
     - web

  web:
    build: ./app 
```

### 关于 docker-gen

> `docker-gen`是一个使用 docker 容器元数据渲染模板的文件生成器。

对于我们的用例，这意味着我们可以使用目标容器上的`labels`字段来配置 Caddy 和 nginx，以配置我们的“虚拟主机”。这意味着少摆弄 nginx 和 Caddy 配置和更多的运输软件。

关于 Caddy 如何做到这一点的更多信息，请参见[github.com/wemake-services/caddy-gen#usage](https://github.com/wemake-services/caddy-gen#usage)，对于 nginx，请参见[github.com/jwilder/nginx-proxy#docker-compose](https://github.com/jwilder/nginx-proxy#docker-compose)。

### 带 Caddy 和 docker-gen(无 Caddyfile)

#### 证书

Caddy 希望`domain.tld`的证书在文件名`domain.tld.pem`下(对于公钥，即。证书)和`domain.tld-key.pem`(对于私钥，即。钥匙)。

与 Caddyfile 示例中一样，我们将手动设置 Caddy 的`tls`指令，但我们将遵循约定(mkcert 也使用该命名约定生成文件)。

```
cp ./foo.test.pem ./caddy-gen/certs
cp ./foo.test-key.pem ./caddy-gen/certs 
```

**虚拟主机用 docker-gen 转发使用标签**
使用 [caddy-gen](https://github.com/wemake-services/caddy-gen) 镜像意味着我们不需要 Caddyfile，我们只是通过 Docker Compose 使用标签，这里是`docker-compose.yml`(参见[github . com/Hugo df/Docker-Compose-local-https/blob/master/caddy-gen/Docker-Compose . yml](https://github.com/HugoDF/docker-compose-local-https/blob/master/caddy-gen/docker-compose.yml))。

这里的 3 个要点是:

1.  在`caddy`容器的`volumes`中，我们将我们的主机`docker.sock`挂载到容器的`docker.sock`中，这是一个能够读取 docker 事件(例如标签的变化)的 docker-gen 约定
2.  在`caddy`容器的`volumes`中，我们将`certs`目录挂载到`/root/certs`
3.  在`web`容器的`labels`中，我们设置了`virtual.host=foo.test`、`virtual.port=8080`和`virtual.tls-email=/root/certs/foo.test.pem /root/certs/foo.test-key.pem`。

在`web`容器配置上设置的标签允许 docker-gen 填充一个`Caddyfile`。`virtual.host`填充块的主机，`virtual.port`填充目标的端口，目标服务的主机是从容器名推断出来的。
`virtual.tls-email`作为`tls`指令注入。我做了一个合并的拉请求(见[github.com/wemake-services/caddy-gen](https://github.com/wemake-services/caddy-gen/pull/19))来增加设置`virtual.tls`的能力，但是更新的图像还没有被推送到 Docker 注册表。

```
version: '3'
services:
  caddy:
    image: "wemakeservices/caddy-gen:latest"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro # needs socket to read events
      - ./certs:/root/certs # to sync mkcert certificates to Caddy
    ports:
      - "443:2015"
    depends_on:
     - web

  web:
    build: ./app
    labels:
      - "virtual.host=foo.test"
      - "virtual.port=8080"
      - "virtual.tls-email=/root/certs/foo.test.pem  /root/certs/foo.test-key.pem" 
```

### 带 nginx 和 docker-gen

#### 证书

Nginx-proxy image 期望`domain.tld`的证书在文件名`domain.tld.cert`下(对于公钥，即。证书)和`domain.tld.key`(对于私钥，即。钥匙)

这些是**而不是**`mkcert`生成它们时使用的名字，所以我们在复制时必须重命名它们。假设`mkcert foo.test`已经成功运行，在`init.sh`脚本中我们有以下部分:

```
cp ./foo.test.pem ./nginx/certs/foo.test.crt
cp ./foo.test-key.pem ./nginx/certs/foo.test.key 
```

#### 虚拟主机转发用 docker-gen 使用标签

使用 jwilder/nginx-proxy 映像允许我们使用目标容器上的标签来设置代理选项。
详见[github . com/Hugo df/docker-compose-local-https/blob/master/nginx/docker-compose . yml](https://github.com/HugoDF/docker-compose-local-https/blob/master/nginx/docker-compose.yml)的`docker-compose.yml`。

这与 caddy-gen 的例子非常相似。

1.  在`nginx-proxy`容器的`volumes`中，主机的`docker.sock`被挂载为容器的`docker.sock`，这是一个 docker-gen 约定，能够读取 docker 事件(例如标签的更改)
2.  在`nginx-proxy`容器的`volumes`中，证书被安装到`etc/nginx/certs`，这是 nginx-proxy 期望得到`domain.tld.crt` / `domain.tld.key`格式证书的地方(如果看到这样的文件，它会自动启用 HTTPS)
3.  在`nginx-proxy`容器的`ports`中，我们转发主机→容器、`80:80`和`443:443`(`443`转发对于能够监听 TLS 上的流量很重要)
4.  在`web`容器的`environment`中，我们设置了`VIRTUAL_HOST=foo.test`和`VIRTUAL_PORT=8080`，这意味着当 nginx-proxy 看到主机`foo.test`的流量时，它会将其转发到`web`容器端口 8080

注意，nginx-proxy 会自动获取 SSL/TLS 证书，并使用`environment`而不是标签。

```
version: '3'
services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    container_name: nginx-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - ./certs:/etc/nginx/certs
    depends_on:
      - web

  web:
    build: ./app
    environment:
      - VIRTUAL_HOST=foo.test
      - VIRTUAL_PORT=8080 
```

## 比较 Caddy 和 nginx 的例子

所有的设置都需要权衡定制配置的能力、设置的简易性和冗长性。

### 可定制

带有完整 Caddyfile 的设置显然在这里占了上风，因为它提供了对所有 Caddy 指令和规则的访问。有一个明显的地方可以放置任何自定义规则。

nginx-proxy 版本紧随其后，因为它记录了如何使用 nginx 配置向代理添加全局和每个站点的规则。

### 易于设置

nginx-proxy 版本名列前茅，因为它根据正确名称的证书/私钥对来推断是否启用 SSL/TLS，文档内容丰富，并且是由 docker-gen 的创建者编写的。选择使用环境变量意味着可以根据`VIRTUAL_HOST_PORT`环境变量来设置`web`容器监听的端口。

Caddy + Caddyfile 设置足够可靠，主要是因为简单地利用了 Caddy 的良好配置格式+ Docker/Docker Compose 卷挂载。

由于命名不准确的标签 caddy-gen 排在最后，当你不得不通过挖掘模板来确定是否可以在`virtual.tls-email`指令中注入除电子邮件以外的东西时，这不是一个好兆头。

### 冗长

caddy-gen 解决方案实际上是最不冗长的。仅仅通过代码量。

它也是最不可定制的。

我个人认为这三种解决方案都不错。在我的项目中，我可能会选择 nginx-proxy 或者 Caddyfile 解决方案。

[从 Markus Spiske 免费下载高分辨率照片](https://unsplash.com/@markusspiske?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)