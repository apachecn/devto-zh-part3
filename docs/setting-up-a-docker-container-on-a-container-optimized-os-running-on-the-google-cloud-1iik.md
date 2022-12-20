# 在 Google Cloud 上运行的容器优化操作系统上设置 Docker 容器

> 原文：<https://dev.to/baens/setting-up-a-docker-container-on-a-container-optimized-os-running-on-the-google-cloud-1iik>

哇，这个标题看起来像满嘴是不是？那么为什么要这样做呢？不是有运行 Docker 容器的 Kubernetes 吗？嗯，有时我只需要一个容器运行，我真的不想增加维护另一个 Kubernetes 集群的开销。蹩脚的借口，但这对于我不需要完整的 Kubernetes 集群的小型安装来说非常有效。

到底什么是容器优化操作系统(COS)？这个操作系统是由 Google 构建的，它被优化为 Kubernetes 集群的基础操作系统，甚至只是一个简单的 Docker 容器。它建立在 Chromium 操作系统之上，从一开始就内置了许多安全特性。例如，根文件系统以只读方式挂载。所以系统上的二进制文件不会改变，也不能改变。请继续阅读所有相关内容，这是一个伟大的项目。

以下是我将在这篇博文中向你展示的内容。在这篇博文中，我们将使用一个基本的 Nginx Docker 映像作为我们想要运行的容器。我们将首先使用`gcloud`命令提供的一些快捷方式在这个图像上运行一个容器。然后，我们将讨论从私有存储库中获取映像并运行所需的条件。我们开始吧！

所有的源代码都在 github 上，所以如果你不想要的话，你不必重新输入。

# 额外津贴

这篇文章将假设你有一个谷歌云帐户所有设置和谷歌云项目已经划分出来。如果您还没有遵循[快速入门教程](https://cloud.google.com/compute/docs/quickstart-linux)，请回来。

警告:我将展示在谷歌云环境中创建资源的命令。这意味着真正的资源需要真正的金钱。如果你不想被指控，就要小心。

# 让 gcloud 做所有的工作

让我们做一些非常基本的事情，我们将使用`gcloud`命令启动一个容器，在 COS VM 之上运行一个容器。下面是这个`gcloud`命令的样子:

```
gcloud compute instances create-with-container nginx-vm --container-image nginx:1.15.8 --tags http-traffic 
```

记下运行该命令后显示的外部 IP 地址。我们将在下一步中需要它。

接下来，我们需要使用防火墙规则来公开该实例:

```
gcloud compute firewall-rules create allow-http-traffic --target-tags http-traffic --allow tcp:80 
```

完成后，继续尝试找到该映像的外部 IP 地址。您现在应该会看到“欢迎使用 nginx！”屏幕。

在下一部分之前，如果您想删除该图像，请继续运行`gcloud compute instances delete nginx-vm`。

这看起来不难，不是吗？我们可以从 docker hub 获取图像并上传，然后，我们就有了一个在云中运行的 docker 容器。然而，如果我们想要自己的代码，或者不在 Docker hub 上托管的代码，会发生什么呢？让我们创建一个私有 docker 映像，并向您展示如何认证和下载该映像。

# 设置自定义 Docker 图像

让我们建立一个私有 docker 映像，我们希望在 COS 映像上运行它。我们将使用基本的 nginx 映像，并使用自定义索引页面对其进行修改，以展示我们可以构建和部署自定义映像。

这是您需要的两个文件，首先是新的`index.html`文件:

```
<!DOCTYPE html>
<html>
<head>
Welcome to nginx!
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to My Custom nginx!</h1>
</body>
</html> 
```

下面是`Dockerfile`的样子:

```
FROM nginx:1.15.8

COPY index.html /usr/share/nginx/html 
```

如果你想在本地测试，运行`docker build -t test . && docker run --rm -p 8080:80 test`并将浏览器指向 [http://localhost:8080](http://localhost:8080) ，你应该会看到“欢迎使用我的定制 nginx！”横幅。

现在让我们把我们的图像放到私有存储库中。这有点超出了这篇博文的范围，但是继续下去，选择一个你可以上传 docker 图片的地方，这个图片可以通过公共互联网访问。我用过 gitlab，但是任何注册表都可以。只要确保公众可以接触到它。如果你在这方面需要帮助，请联系我，我会尽力帮助你。

# 向注册中心认证

既然我们已经在注册表中建立了我们的映像，我们需要某种方法在我们的虚拟机上通过注册表进行身份验证。如果您是在本地做这件事，我敢打赌您会在某个时候运行一个`docker login`命令来获得注册中心的认证。嗯，这正是我们要对这台虚拟机做的事情。

那么我们如何运行这个命令呢？COS 安装了一个名为 [cloud-init](https://cloudinit.readthedocs.io/en/latest/) 的工具包。`Cloud-init`是一套管理云图像的工具。这些帮助提供了创建启动脚本的方法，让您的云映像完全按照您想要的方式运行。大多数云提供商都使用它们，大多数发行版都有可以使用的挂钩或工具。对于这种特殊情况，我们将对提供可以在启动时运行的用户脚本感兴趣。为此，我们将提供一个`user-data`变量来提供一个`cloud-config`块。该块将创建一个用户、一个服务定义，并运行一个启动脚本来启动该服务。听起来很简单，对吗？好吧，我们走吧！

让我们在这里看一下这个`cloud-config`配置文件可能是什么样子:

```
#cloud-config

users:
- name: cloudservice
  uid: 2000

write_files:
- path: /etc/systemd/system/myservice.service
  permissions: 0644
  owner: root
  content: |
    [Unit]
    Description=My Service
    Requires=docker.service network-online.target
    After=docker.service network-online.target

    [Service]
    Environment="HOME=/home/cloudservice"
    ExecStartPre=/usr/bin/docker login %REGISTRY% -u %USERNAME% -p %PASSWORD%
    ExecStart=/usr/bin/docker run --network=host --rm --name=myservice %DOCKER_IMAGE%
    ExecStop=/usr/bin/docker stop myservice
    Restart=on-failure
    RestartSec=10

    [Install]
    WantedBy=multi-user.target

runcmd:
- iptables -A INPUT -p tcp -j ACCEPT
- systemctl daemon-reload
- systemctl enable --now --no-block myservice.service 
```

该配置文件用作模板，将为我们的`cloud-config`流程生成一个配置文件。让我们指出一些更重要的线，然后我会解释变量是什么:

**第 1 行**:将这个文件标记为云配置文件。如果你没有这个，系统就不会去捡，不会去处理。这是非常重要的事情。

**第 3 - 5 行**:设置专门运行该服务的用户。这将创建一个安全沙箱，因为除了运行该容器之外，该用户不会被分配任何权限。

**第 7 - 26 行**:这是`/etc/systemd/system/myservice.service`的文件内容。正如路径所示，这是一个将运行我们的 docker 容器的`systemd`服务文件。本节包含有关文件权限的信息，以及此配置中嵌入的文件的实际内容。

**第 12 - 15 行**:设置服务的描述以及服务需要运行的任何需求。在这种情况下，我们希望在该服务启动之前，网络处于在线状态，docker 处于运行状态。

**第 17 - 23 行**:设置服务如何开始和停止。这是一些更重要的部分。首先，我们将主目录设置为我们之前创建的用户。这种沙箱和隔离运行的地方。接下来的`ExecStartPre`是这种魔力开始发挥作用的地方。这是执行`docker login`命令的地方。所有的参数目前都是变量，可以替换，但这就是神奇的地方。接下来，`ExecStart`实际上运行了`docker run`命令。需要注意的一件重要事情是`--network=host`旗。我们希望 docker 容器使用主机的网络接口，而不是创建普通的隔离网络堆栈。通过这种方式，容器可以像网络上的主机一样工作，并且无需任何额外的魔法就可以暴露所有端口。

**第 22 - 23 行**:这将服务设置为在失败时重启，并在每次重试之间等待 10 秒钟

**第 28 - 31 行**:一旦配置被读取且所有其他部分完成，该部分运行。这就像一个启动脚本。首先，我们使用`iptables`设置内部防火墙来接受 TCP 连接。然后我们重新加载`systemd`守护进程来读取我们的配置文件，然后我们启用并排队启动我们的服务。值得注意的是，`--now --no-block`命令在这里很重要，因为我们希望服务现在启动，但我们也希望它在`systemd`进程中排队，以便它等待 Docker 服务和网络连接在线。否则，我们的容器可能无法正确启动，因为 Docker 没有准备好，或者我们无法通过互联网获取我们的容器。

现在，所有这些变量是干什么的:

**%REGISTRY%** :定义注册表所在的根域。例如，在测试中，我使用了 gitlab，所以注册表应该是`registry.gitlab.com`。

**%USERNAME%** :这是登录时使用的用户名。按照 gitlab 的主题，我使用了一个部署令牌，用户名是 gitlab 给我的。

**%PASSWORDD%** :这是可以用来登录的密码或令牌。

**%DOCKER_IMAGE%** :这是 DOCKER 镜像路径。即`registry.gitlab.com/myimage/test:1`。

现在，我该怎么做呢？那么，用正确的值替换所有的变量，然后运行这个命令:

```
gcloud compute instances create test-nginx --image cos-stable-72-11316-136-0 --image-project cos-cloud --tags http-traffic --metadata-from-file user-data=cloud-init-config.yml 
```

如果使用该命令输出 IP 地址，应该会看到“欢迎使用我的定制 nginx！”横幅。如果没有，请通读故障排除部分。如果一切正常，恭喜你！

# 故障排除注意事项

好吧，网站没出现。现在怎么办？好吧，让我们先把 SSH 放进盒子，这样我们就可以开始调查发生了什么。要做到这一点，让我们通过运行这个命令来确保防火墙是打开的:`gcloud compute firewall-rules create allow-ssh-traffic --allow tcp:22`。这将打开防火墙，允许您通过执行下面的命令`gcloud compute ssh <box name, i.e. test-nginx>`SSH 到机器中。

现在你在箱子上，你到底在找什么？对于我们的 docker 示例，我们希望确保 docker 容器启动并运行，因此我们将发出一个`docker container ls`命令来验证它启动并运行。如果它已经启动并运行，那么您可能遇到了防火墙问题。访问[控制台](//console.cloud.google.com)，检查网络接口，查看入口设置是否正确。我们希望看到的是端口 80 打开并正常工作。如果是的话，可能`iptables`没有按预期运行，所以在 VM 实例本身上验证你可以做类似`curl localhost`的事情。如果有效，验证`iptables`是否设置正确。

但是如果容器甚至没有运行呢？我从那里去哪里？我们需要调查日志并开始筛选可能发生的事情。为此，`sudo journalctl`命令是您开始探索的地方。这将为您提供机器启动时的所有日志。我们示例中的另一个命令是`sudo systemctl status myservice`,它向您显示服务的原始状态，是启动还是关闭，以及服务现在可能正在发生什么。

我遇到的另一件事是首先检查云初始化脚本是否运行正确。检查的一个好方法是验证我们期望的文件是否存在。在这个例子中，我们期望在`/etc/systemd/system/myservice.service`看到一个文件。如果没有，通常问题是第一行没有准确地读出`#cloud-config`。仔细检查您的配置，并查阅日志中的`user-data`解析和检索。通常会有一条日志消息，指出文件无法从变量中理解，并且忽略了数据。

# 结论

希望我已经让您体验了如何在 Google Cloud 上使用容器优化操作系统来设置单个 Docker 容器实例。当我不需要 kubernetes 实例的所有强大功能时，我发现这是一个有用的特性。这种用于几个集装箱的重量较轻的基础设施提供了有用性和成本效益。但也要注意，这些实例显然不会以任何方式复制，所以如果一个出现问题，所有问题都会出现。在云中要小心，因为每个堆栈都应该建立在云资源可以随意进出的假设之上。

所有这些代码和例子都存放在我的 github 账户上。