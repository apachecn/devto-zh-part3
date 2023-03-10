# 使用 Traefik 自动管理 DNS 和 SSL

> 原文：<https://dev.to/maxencehenneron/automatic-dns-and-ssl-management-with-traefik-8cc>

上个月，我们推出了名为 [upload.express](https://upload.express) 的新 SaaS 服务。这是一个完全可定制的上传者，适合经常分享文件的人。Upload.express 是完全开源的，我们的商业模式是提供托管服务。

我们很快面临第一个问题:我们如何管理所有的 DNS 记录和 SSL 证书？

我们想到的第一个解决方案是开发一个小型的微服务，它将调用我们的域名托管提供商的 API 来创建记录，最后使用 Let's Encrypt 来生成 SSL 证书。

这是我 3 年前在我的最后一个项目中实现的，我们遇到了一个大问题:让我们加密每个域的速率限制。您每周只能申请 50 个子域 SSL 证书。我们的项目有一个免费试用计划，所以我们预计每周创建 50 多个子域。

## 通配符证书

过去，通配符 SSL 证书的价格为 300 美元，但在 2018 年，当 let's encrypt 宣布支持它们时，一切都变了。通配符证书是可以在所有子域上使用的证书。
例如，如果您拥有域名“example.com ”,您可以在所有子域上使用相同的通配符证书“*.example.com”。

## 通配符 DNS 记录

除了通配符证书，大多数 DNS 提供商还支持通配符 DNS 记录。通过创建一个名为“*.example.com”的“A”条目，指向您的服务器的 IP 地址，来自您的任何子域的任何请求都将重定向到您的服务器的 IP 地址。通过这样做，您不再需要实现自己的域管理微服务。

## 使用 Traefik 作为负载平衡器

随着您的成长，您将需要不止一台服务器来处理所有流量。此外，在负载平衡器级别配置 SSL 证书更容易，因为您不必配置所有不同类型的应用程序，您只需保护用户和负载平衡器之间的传输。

在 upload.express 上，我使用 docker swarm 来管理部署。您还可以将其他集群管理系统与 traefik 一起使用，例如 kubernetes。在这个简短的教程中，我将假设您使用的是 docker swarm，但是将其应用到 kubernetes 应该不难。

首先，我创建了一个网络，让 traefik 与部署在我的集群上的服务进行通信

`docker network create --opt encrypted -d overlay webgateway`

然后，我使用下面的栈来部署 traefik。

```
version: "3.5"
services:
  traefik:
    image: traefik:1.7
    command:
      - "--api"
      - "--loglevel=debug"
      - "--docker"
      - "--docker.swarmMode"
      - "--docker.domain=upload.express"
      - "--docker.watch"
      - "--configFile=/etc/traefik/traefik.toml"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/traefik/traefik.toml:/etc/traefik/traefik.toml
      - /opt/traefik/acme.json:/etc/traefik/acme/acme.json
    environment:
      - AWS_ACCESS_KEY_ID=
      - AWS_SECRET_ACCESS_KEY=
    networks:
      - webgateway
      - traefik
    ports:
      - target: 80
        published: 80
      - target: 443
        published: 443
      - target: 8080
        published: 8080
        mode: host
    deploy:
      mode: global
      placement:
        constraints:
          - node.role == manager
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
networks:
  webgateway:
    driver: overlay
    external: true
  traefik:
    driver: overlay 
```

如您所见，我设置了两个对应于我的 AWS 凭证的环境变量。这是因为我们需要使用 DNS 验证来生成 SSL 通配符证书。因为我使用 route53 来管理我的域名，所以我必须提供我的 AWS 证书。您可以根据您的 DNS 提供商[在这里](https://docs.traefik.io/configuration/acme/#provider)看到您需要设置的环境变量列表。

最后一步是为 traefik 创建配置文件。在我的 swarm 堆栈中，我将路径配置为“/opt/traefik/traefik.toml”。您所要做的就是创建文件并粘贴以下配置。

```
defaultEntryPoints = ["https","http"]

[entryPoints]
  [entryPoints.http]
  address = ":80"
    [entryPoints.http.redirect]
    entryPoint = "https"
  [entryPoints.https]
  address = ":443"
  [entryPoints.https.tls]

[retry]

[accessLog]

[docker]
endpoint = "unix:///var/run/docker.sock"
domain = "example.com"
watch = true
exposedbydefault = false

[acme]
email = "mail@example.com "
storage = "/etc/traefik/acme/acme.json"
entryPoint = "https"
acmeLogging = true
onHostRule = true

[acme.dnsChallenge]
  provider = "route53"
  delayBeforeCheck = 0

[[acme.domains]]
   main = "*.example.com" 
```

不要忘记用您的域替换示例域。现在，通过运行以下命令启动 traefik 堆栈:

`docker stack deploy -c traefik-stack.yml traefik`

最后，对于您将在 swarm 集群中部署的每个堆栈，设置以下标签:

```
- traefik.port=4000
- traefik.enable=true
- traefik.docker.network=webgateway
- traefik.backend=service_name
- traefik.frontend.rule=Host:service_name.example.com 
```

并像这样链接我们之前创建的 webgateway 网络:

```
networks:
  webgateway:
    external: true 
```

在示例中，我使用 docker 栈来部署 upload.express 实例:

```
---
version: '3.7'
services:
  uploadexpress:
    image: uploadexpress/app
    networks:
    - webgateway
    deploy:
      placement:
        constraints:
        - node.role==worker
      labels:
      - traefik.port=4000
      - traefik.enable=true
      - traefik.docker.network=webgateway
      - traefik.backend=maxencehenneron
      - traefik.frontend.rule=Host:maxencehenneron.upload.express
      restart_policy:
        condition: on-failure
      mode: replicated
      replicas: 1
networks:
  webgateway:
    external: true 
```

就是这样！Traefik 将自动选择正确的通配符证书，您可以通过“service_name.example.com”找到您的服务