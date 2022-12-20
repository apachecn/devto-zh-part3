# 使用 docker 机器供应远程 Docker 主机

> 原文：<https://dev.to/zac_siegel/using-docker-machine-to-provision-a-remote-docker-host-1267>

本文起源于[zsiegel.com](https://zsiegel.com/2019/04/06/using-docker-machine-to-provision-a-remote-docker-host)

像许多开发人员一样，我的主要机器是笔记本电脑，在开发应用程序时，我通常使用 Docker 容器来提供数据库、缓存、消息队列和其他支持服务的本地实例。

当通过 Docker 运行这些服务时，这经常会对我的机器造成严重破坏，导致它变慢，即使有大量的 ram 和 CPU。我开始探索如何获得 Docker 的易用性和灵活性，而是使用远程计算资源。我发现 Docker 有一个很好的工具来实现这种类型的工作流，其中容器运行在不同的主机上，但您仍然可以访问它们，就像它们在您的机器上一样。

## 对接机

Docker 有一个名为`docker-machine`的内置工具，它允许你提供许多不同类型的远程机器作为 docker 主机。为了让它运行，你只需要一个你可以 SSH 访问的远程系统。Docker 随后可以提供远程系统，您的本地`docker`工具可以代替您的本地机器与之交互。

这允许您在远程机器上而不是在本地机器上运行各种较重的服务。让我们看看如何在家庭服务器上配置一个简单的 docker 主机。

## 码头工人机器司机

浏览一下[文档](https://docs.docker.com/machine/drivers/)，找到适合你的驱动。它们为远程运行 docker 实例提供了许多云驱动程序。在这种情况下，我们将在我的家庭办公室运行的 ubuntu 服务器上提供一个[通用](https://docs.docker.com/machine/drivers/generic/)主机。

我继续安装 Ubuntu 18.04 LTS，我有一个基本的根帐户设置。我们需要做的第一件事是启用 SSH 密钥认证。

## SSH 密钥认证

我们将首先生成一组密钥，用于使用 [ssh-keygen](https://www.ssh.com/ssh/keygen/) 进行认证。我将我的密钥命名为`zdocker`,因为这是我办公室中将要连接的服务器的主机名。

一旦生成了这些密钥，您就可以利用一个名为 [ssh-copy-id](https://www.ssh.com/ssh/copy-id) 的工具将公钥信息自动传输到您的服务器。在我的例子中，命令看起来像下面这样。

```
ssh-copy-id -i ~/.ssh/${PUBLIC_KEY} ${USERNAME}@${IP_ADDRESS} 
```

在上面的命令中，您可能希望用服务器的`public_key, username, and ip_address`替换上面的占位符。传输后，您现在可以通过公钥认证登录到您的服务器。然后，我继续通过 SSH 禁用密码登录，因为我们允许登录的唯一方式是使用密钥。

```
nano /etc/ssh/sshd_config 
```

```
# Turn off PasswordAuthentication by setting the value to no 
# and making sure it is not commented out like below
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no 
```

现在我们有了 SSH 密钥认证，我们需要为我们想要登录的用户启用无密码 sudo 特权。这是 docker-machine 安装程序所要求的。

### 编辑 sudoers 文件

```
sudo visudo 
```

```
# Insert a new entry at the bottom that enables passwordless sudo
${USERNAME} ALL=(ALL) NOPASSWD: ALL 
```

您需要用您在机器上创建的用户替换`username`。

## 调配一台 Docker 主机

既然我们已经设置了 SSH 公共密钥认证和 root 访问，是时候让`docker-machine`配置我们的服务器了。

```
docker-machine create \
    --driver generic \
    --generic-ip-address=$IP_ADDRESS \
    --generic-ssh-user $USERNAME \
    --generic-ssh-key ~/.ssh/$PRIVATE_KEY \
    zdocker 
```

您可能想要用`ip_address, username, private_key, and machine_name`替换上面的占位符，在我的例子中是`zdocker`。

请注意，当您运行`docker-machine ls`时，`machine_name`将显示在您的 CLI 工具中。在选择使用哪个 docker 主机来运行您的服务时，您将如何引用这个远程 docker 实例。

如果调配成功，您将会看到类似于下面的一些输出。

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.1.10:2376"
export DOCKER_CERT_PATH="/path/to/a/cert"
export DOCKER_MACHINE_NAME="zdocker"
# Run this command to configure your shell: 
# eval $(docker-machine env zdocker) 
```

接下来在当前 shell 中运行 eval 命令，将您的`docker` CLI 指向远程主机。

```
eval $(docker-machine env zdocker) 
```

您现在已经将`docker`指向了一个远程 docker 主机。在这个外壳中，所有 docker 命令都将利用远程系统来完成其工作，您的笔记本电脑硬件可以稍微轻松一点了！