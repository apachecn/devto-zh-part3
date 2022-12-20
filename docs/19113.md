# 通过用本地注册表缓存 Docker 图像来节省时间和带宽

> 原文：<https://dev.to/mayeu/saving-time-and-bandwidth-by-caching-docker-images-with-a-local-registry-98b>

我经常使用 Docker🐳在 VM(流浪者)中，或者在我的本地网络的各种机器上。这导致我在一台机器上下载图像，而那些图像已经在另一台机器上下载了。除了浪费带宽，在低速或拥挤的连接上，这也是巨大的时间浪费！为了解决这个问题，我现在在我的笔记本电脑上运行一个本地注册表，自动缓存我通过 docker 请求的任何图像。

为了实现这一点，我们将以代理模式运行官方的 Docker 注册中心，然后我们将指示我们的 Docker 守护进程使用这个本地注册中心作为它的默认注册中心。

首先，我们需要创建一个文件夹，注册表将使用它来存储所有需要的图像和数据。它可以在你机器的任何地方，我个人把它放在有点标准的`/var/lib`文件夹:

```
$ sudo mkdir /var/lib/docker-registry 
```

为了确保我们拥有当前注册表版本的最新配置文件，我们可以直接从 docker 映像中提取它(同时，提取映像):

```
$ sudo docker run -it --rm registry:2 cat \
       /etc/docker/registry/config.yml > /var/lib/docker-registry/config.yml 
```

根据您的 docker 注册表映像版本，配置可能与我的略有不同。在创建这篇文章的时候，它看起来是这样的:

```
version: 0.1
log:
  fields:
    service: registry
storage:
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3 
```

要激活注册表的代理行为，我们必须在配置中有以下键:

```
proxy:
  remoteurl: https://registry-1.docker.io 
```

其中`remoteurl`是默认使用的远程注册表的 URL。这是 Docker 的官方文件。

最终的配置看起来是这样的:

```
 version: 0.1
log:
  fields:
    service: registry
storage:
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
proxy:
  remoteurl: https://registry-1.docker.io 
```

配置完成后，我们就可以启动本地注册表了。注意，我在命令中添加了一个`--restart=always`选项标志，这样每当 Docker 守护进程启动时，注册表也会自动启动。

```
$ sudo docker run --restart=always -p 5000:5000 \
         --name v2-mirror -v /var/lib/docker-registry:/var/lib/registry \
         --detach registry:2 serve /var/lib/registry/config.yml 
```

在命令中，我们使用`-v`在映像中挂载我们之前创建的注册表文件夹，我们通过`serve`启动注册表，并将配置文件作为唯一的参数。

我们可以检查它是否正在运行:

```
$ sudo docker ps
CONTAINER ID IMAGE CREATED STATUS PORTS NAMES
67425da4ea4c registry:2 32 seconds ago Up 29 seconds 0.0.0.0:5000->5000/tcp v2-mirror 
```

我们可以用`curl` :
查询空注册表的内容

```
$ curl http://localhost:5000/v2/_catalog
{"repositories":[]} 
```

现在我们已经运行了本地注册表，我们必须配置 Docker 守护进程，这样它将使用它而不是默认的。这需要改变`/etc/docker/daemon.json`文件(如`root` ):

```
{
    "registry-mirrors": ["http://localhost:5000"]
} 
```

您的系统上可能还没有这个文件和文件夹。如果是这样，您可以安全地创建它。更改之后，我们需要重启守护进程。假设您的系统使用 Systemd，它应该看起来像:

```
$ sudo systemctl restart docker 
```

我们现在可以尝试下载一个图像，看看它是否正确地使用了我们的缓存代理:

```
$ sudo docker pull redis
Using default tag: latest
latest: Pulling from library/redis
f17d81b4b692: Pull complete
b32474098757: Pull complete
8980cabe8bc2: Pull complete
e614c66c2b9c: Pull complete
6eb43ec9256b: Pull complete
394ecf5f46d4: Pull complete
Digest: sha256:f30f134bd475d451ce3207fb128bcef8ff87d0f520a39cac0c4ea285819c42a9
Status: Downloaded newer image for redis:latest

~ took 40s 
```

让我们检查注册表的内容:

```
$ curl http://localhost:5000/v2/_catalog
{"repositories":["library/redis"]} 
```

似乎我们刚刚缓存了我们的第一张图片🎉。那我们试试缓存吧！首先，我们从 docker 守护进程中删除 redis 映像:

```
$ sudo docker rmi redis
Untagged: redis:latest
Untagged: redis@sha256:f30f134bd475d451ce3207fb128bcef8ff87d0f520a39cac0c4ea285819c42a9
Deleted: sha256:415381a6cb813ef0972eff8edac32069637b4546349d9ffdb8e4f641f55edcdd
Deleted: sha256:2a5a57892da005399e6ce7166c5521cdca43a07872f23995e210bde5dae2640e
Deleted: sha256:85e1fabde4fd4d6df993de44ef3e04d15cd69f9d309c0112c6a5054a6dc8351a
Deleted: sha256:2725175b62c7479ee209454110e8293080b9711e4f0a29219e358d1afba88787
Deleted: sha256:7ae66985fd3a3a132fab51b4a43ed32fd14174179ad8c3041262670523a6104c
Deleted: sha256:bf45690ef12cc54743675646a8e0bafe0394706b7f9ed1c9b11423bb5494665b
Deleted: sha256:237472299760d6726d376385edd9e79c310fe91d794bc9870d038417d448c2d5 
```

我们再拉一次:

```
sudo docker pull redis
Using default tag: latest
latest: Pulling from library/redis
f17d81b4b692: Pull complete
b32474098757: Pull complete
8980cabe8bc2: Pull complete
e614c66c2b9c: Pull complete
6eb43ec9256b: Pull complete
394ecf5f46d4: Pull complete
Digest: sha256:f30f134bd475d451ce3207fb128bcef8ff87d0f520a39cac0c4ea285819c42a9
Status: Downloaded newer image for redis:latest

~ took 13s 
```

很好，快了 3 倍！👍下载部分几乎是瞬间完成的，而图像解压缩花费了大部分时间！

搞定了。我们现在有了一个本地缓存，用于所有 docker 映像，每次 Docker 守护进程启动时都会启动。我们可以将网络中的所有虚拟机指向它(通过监听外部)。我们现在可以享受更多的时间来关注对我们重要的事情，而不是从互联网上下载一些东西:)

这种缓存的一个好效果是中间图像也被缓存。这对于不稳定的互联网接入非常有用。因为现在，当连接在拉取过程中超时时，所有已经下载的位都将被缓存，这样您就可以从离开的地方继续拉取了！你可以试着开始拉和想象，停止，然后再开始。使用默认的没有缓存的守护进程，这将导致再次下载所有的中间映像，但不使用代理👏

*本文最初发表于 [mayeu.me](https://mayeu.me/blog/saving-time-bandwidth-caching-docker-images-with-local-registry/) 。*