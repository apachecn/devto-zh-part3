# 如何运行自己的 docker 注册表与密码，SSL 和 S3 后端

> 原文：<https://dev.to/chen/how-to-run-your-own-docker-registry-with-password-ssl-and-s3-backend-268b>

我记得我开始用 Docker 的那一天。简单得令人无法抗拒。我查阅了非常好的官方文档，并查找了一些 101 教程来开始。我成功地制作了我的第一张照片。现在我有了一个，我想在我的服务器上部署它。第一个选择是 [DockerHub](http://www.dockerhub.com) ，但是一个免费的账户让你有义务分享这些图片。如果你需要保密，这项服务需要付费。

或者，您可以只运行自己的 *[Docker 注册表](https://docs.docker.com/registry/deploying/)* ，并灵活地:

*   密码保护它
*   将图像存储在本地或云中(S3/Azure/谷歌云)
*   使用您的自签名 SSL 证书

> "注册表是一个无状态的、高度可伸缩的服务器端应用程序，它存储并允许你分发 Docker 图像."

官方 Docker 文档深入介绍了这一主题。你应该读一读它，了解它是什么以及如何运行它。在这篇文章中，我将解决我在部署注册服务器时遇到的问题。我将解释如何实现上面提到的三个项目。对于这篇文章的其余部分，我假设你已经阅读了官方网站上的[注册表概述](https://docs.docker.com/registry/)和[了解 Docker 注册表](https://docs.docker.com/registry/introduction/)。( *~3min* )。

# 让注册表运行

注册表应用程序本身运行在一个容器中。它有一个前端组件(可选)，允许您从浏览器访问注册表数据。每个组件都在自己的容器中运行，这就形成了应用程序堆栈。*应用程序堆栈是一组应用程序(堆栈),它们链接在一起执行特定的任务。*我们可以使用 *[docker-compose](https://docs.docker.com/compose/)* 来运行应用堆栈，而不是单独运行每个容器。

下面是我们开始使用的 docker-compose.yml 文件:

```
version: '2'
registry:
    image: registry:v2
    restart: always
    ports:
      - "5000"
    environment:
      REGISTRY_STORAGE_DELETE_ENABLED: 'true'
      REGISTRY_HTTP_ADDR: 0.0.0.0:5000
registry-frontend:
    image: konradkleine/docker-registry-frontend:v2
    restart: always
    environment:
      ENV_DOCKER_REGISTRY_HOST: 'registry'
      ENV_DOCKER_REGISTRY_PORT: 5000
    links:
      - registry
    ports:
      - "8080:80"
    expose:
      - 80 
```

Enter fullscreen mode Exit fullscreen mode

让我们简单地分解一下，因为它只是简单的配置(没有密码，S3 或 SSL)。

*注册表*和*注册表前端*是我的堆栈中的容器名。你可以随便给它们起什么名字。环境变量*REGISTRY _ STORAGE _ DELETE _ ENABLED*允许你从注册表中删除图片， *REGISTRY_HTTP_ADDR* 绑定监听地址。

*registry-frontend* 容器使用 *ENV_DOCKER_REGISTRY_HOST* 和 *ENV_DOCKER_REGISTRY_PORT* 作为其连接的地址。因为我们使用 docker-compose，所以我们可以通过*名称*使用它。[链接](https://docs.docker.com/compose/compose-file/#links)配置将容器链接到另一个服务，在我们的例子中是链接到*注册表*。

你不好奇[端口](https://docs.docker.com/compose/compose-file/#/ports)和[曝光](https://docs.docker.com/compose/compose-file/#/expose)设置有什么区别吗？不同之处在于*端口*使得主机以及组合文件中的其他服务可以访问定义的端口，而 *expose* 实际上只向其他服务**公开端口，而**不会将它们发布到主机。在这种情况下，只能指定内部端口。

*端口*也**在外部端口(在主机上定义)和容器指定端口之间链接**。对于`registry`容器，端口 5000 可以从任何地方访问。另一方面， *registry-frontend* 将端口 80 *仅*暴露给文件中定义的其他服务，而从外部访问它则被绑定到端口 8080。

为了运行服务，我们在 *docker-compose.yml* 文件所在的目录中使用`docker-compose up`。
您可以使用`docker ps`来验证服务是否正在运行。如果容器启动了，您现在可以浏览 [http://localhost:8080](http://localhost:8080) 来查看您的注册表数据。

# 密码时间

作为拥有我的私人注册的一部分，我想增加一些安全措施。我想用密码保护它。一旦设置完毕，客户端第一次登录注册表时需要提供密码。
这非常简单，有一篇很棒的文章介绍了这一点。检查介质上的[“私有 Docker 注册表第 2 部分:让我们添加基本认证”，如果你也需要的话进行配置。](https://medium.com/@cnadeau_/private-docker-registry-part-2-lets-add-basic-authentication-6a22e5cd459b)

[![Photo by Liam Tucker on Unsplash](img/b818c8e61ceab41db0d8535f46f2f6dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lSr9Busq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/toglazr7ykc9udlk9kq1.jpg)

# 私有 SSL 证书

安全性始终是一个问题。这是我们在 POC 过程中容易忘记的事情。我们想让事情先运转起来，而且要快。它需要额外的步骤，我们倾向于把它作为最后的任务。

我给你的建议是- *只要你能使用 SSL，就使用它。你投入的额外时间是值得的，而且没有缺点。
docker 客户端默认使用 HTTPS，所以在我看来我们有 3 个选项:*

*   让客户端使用 HTTP 而不是 HTTPS
*   使用*不安全注册表*参数配置客户端信任我们的注册表
*   将我们的证书添加到受信任的证书中，无论是 docker 引擎还是操作系统

我发现第三个选项是最适合我的解决方案。我将为我的服务器使用我自己的证书。

我假设您已经为您的服务器生成了证书(cert.crt、cert.pem 和 ca-certificate)。将公钥和私钥放在 */var/lib/docker/certs* 中。为了让注册中心使用它们，我们需要在我们的配置中添加以下内容，在*注册中心*的*环境*下:

```
 REGISTRY_HTTP_TLS_CERTIFICATE: /certs/registry_gnosis.crt
    REGISTRY_HTTP_TLS_KEY: /certs/registry_gnosis_key.pem
  volumes:
    - /var/lib/docker/certs:/certs 
```

Enter fullscreen mode Exit fullscreen mode

好的，我们的服务器端现在使用我们的证书。太好了！但是我们还没有完成。
为了让客户能够连接注册中心，我们需要他们的工作站*信任*我们的 CA。否则，**连不上。**如果你的客户端机器已经信任你的 CA 证书，你就大功告成了。否则，您需要*将您的 CA 证书复制到机器上。*你可以把它复制到*/etc/docker/certs . d*docker 会自动信任它，或者你需要操作系统信任它(在 Ubuntu 系统上，在其他操作系统上可能有所不同):

1.  将其复制到/usr/local/share/ca-certificates/
2.  sudo 更新-ca-证书

完成后，请确保在客户端上重新启动 docker 服务。

现在我们的注册中心已经有了基本的身份验证和 SSL 加密支持，让我们继续我们的旅程，进入最后的配置步骤。在我解释了使用 *S3 作为后台之后，我将展示如何连接到注册中心。*

# S3 存储后端

登记处提供我们的图像。我们可以将图像拉入或推出。所以，这些图像一定在某个地方的磁盘上。默认情况下，它们驻留在运行注册表的 docker 主机上。你可以用不同的卷显式地挂载它，但是你可以做一些更酷的事情。我使用了 AWS S3 存储服务作为我的后端。这意味着，我上传到注册表的每张图片都保存在 S3 的一个专用桶里。

这具有以下优点:

1.  我不需要关心备份。
2.  我的存储是无限的。
3.  灾难恢复，万一我的注册表运行的机器烧坏了什么的，我可以部署一个新的服务，并将其连接到 S3 桶。所有数据都会保留。

再读一遍第三项。这是无价的。一旦我在 *docker-compose.yml* 文件中配置了 *S3 存储*,我就可以在世界上任何地方启动另一个注册表容器，从而获得对我所有图像的访问权。

为了配置存储，我们需要在*注册表*组件
的*环境*部分中的这个代码片段

```
 REGISTRY_STORAGE: s3
    REGISTRY_STORAGE_S3_ACCESSKEY: <api access key>
    REGISTRY_STORAGE_S3_SECRETKEY: <api secret>
    REGISTRY_STORAGE_S3_BUCKET: <bucket name>
    REGISTRY_STORAGE_S3_REGION: <region>
    REGISTRY_HEALTH_STORAGEDRIVER_ENABLED: false 
```

Enter fullscreen mode Exit fullscreen mode

很简单，但是最后一个。*注册表健康存储驱动启用*很重要。
在我补充之前，我遇到了问题。我不记得确切的错误，但在谷歌搜索了一段时间后，我发现如果你用一个空桶(我的桶是空的)运行注册表，这个健康检查就会失败，服务也无法启动。所以我不得不关掉它，然后一切都正常了。
*上传东西后，可以启用此项检查。我决定我真的不关心这个，所以我把它禁用了。*

# 客户端连接

为了使用我的新注册表，我需要连接客户端，这样它就可以从/向它拉/推图像。记住，我们的注册中心使用 SSL，默认情况下 docker 客户端也是如此，所以我需要*信任我的 CA，就像上面的[解释的那样。](#%20Deploy%20private%20SSL%20certificates)*

要连接，请执行`docker login -u <user> <url:port>`(不带 https 前缀)。

现在，在我的 Ubuntu 机器上，当我这样做时，我遇到了另一个错误:
*“保存凭证错误:存储凭证错误-错误:退出状态 1，out:没有 X11 $显示无法自动启动 D-Bus”*

为了解决这个问题，我需要从操作系统中删除一个包:`apt remove golang-docker-credential-helpers`
让它工作。登录到注册表后，默认情况下，证书保存在隐藏目录 *~/中的一个文件中。docker/config.json* 。显然这不是最佳实践，你可以在这里找到更好的替代方案[。](https://docs.docker.com/engine/reference/commandline/login/#credentials-store)

## 推拉

要拉或推图像，我们只需参考我们的注册表它的地址，`docker pull our-registry.com:<port>/image`。
如果您没有域名，也可以使用该 IP。

# 检查注册表内容的 API

我将使用一个假域名`registry.gnosis.org:5000`作为我的注册服务器。
一旦我运行了容器，我就可以浏览到 http://gnosis.example.com 的[并看到注册中心、存储库、图片和标签的内容。如果你决定不使用前端，或者你需要在一个应用程序中使用它，你可以使用它的 API 访问注册表。](http://gnosis.example.com)

我将展示一些有用的例子，

```
# list of the repositories
chen@gns:~$ curl -ksS -u admin https://gnosis.example.com/v2/_catalog
Enter host password for user 'admin':
{"repositories":["sample_image","nginx"]}

# list of tags for an image
chen@gns:~$ curl -ksS -u admin https://gnosis.example.com/v2/sample_image/tags/list
Enter host password for user 'admin':
{"name":"sample_image","tags":["0.1", "0.2", "latest"]} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

我完成了部署注册表的过程。我知道我没有完全覆盖它，但这不是这篇文章的目的。如果你需要一个私有注册中心，建立一个是非常容易的。有很多教程可以教你怎么做。我与您分享了我在部署注册中心时遇到的问题，以及我应用的配置。

应用密码和 SSL 作为安全项目很重要，*别忘了*。如果你可以使用云提供商来存储你的图像，它可以帮你省去很多麻烦。但是如果你做不到，这也不是世界末日，你只需要照顾好我列出的这些项目。

下面是完成的`docker-compose.yml`的样子:

```
version: '2'
registry:
    image: registry:v2
    restart: always
    ports:
      - "5000"
    environment:
      REGISTRY_STORAGE_DELETE_ENABLED: 'true'
      REGISTRY_HTTP_ADDR: 0.0.0.0:5000
      REGISTRY_HTTP_TLS_CERTIFICATE: /certs/registry_gnosis.crt
      REGISTRY_HTTP_TLS_KEY: /certs/registry_gnosis_key.pem
      REGISTRY_STORAGE: s3
      REGISTRY_STORAGE_S3_ACCESSKEY: <api access key>
      REGISTRY_STORAGE_S3_SECRETKEY: <api secret>
      REGISTRY_STORAGE_S3_BUCKET: <bucket name>
      REGISTRY_STORAGE_S3_REGION: <region>
      REGISTRY_HEALTH_STORAGEDRIVER_ENABLED: false volumes:
    - /var/lib/docker/certs:/certs
registry-frontend:
    image: konradkleine/docker-registry-frontend:v2
    restart: always
    environment:
      ENV_DOCKER_REGISTRY_HOST: 'registry'
      ENV_DOCKER_REGISTRY_PORT: 5000
    links:
      - registry
    ports:
      - "8080:80"
    expose:
      - 80 
```

Enter fullscreen mode Exit fullscreen mode