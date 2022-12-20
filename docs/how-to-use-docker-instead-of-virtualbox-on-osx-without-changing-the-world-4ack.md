# 如何在不改变世界的情况下，在 OSX 上用 Docker 代替 VirtualBox 使用游民

> 原文：<https://dev.to/dholth/how-to-use-docker-instead-of-virtualbox-on-osx-without-changing-the-world-4ack>

我的团队已经使用了一段时间的流浪者来管理我们的开发环境。vagger 自动提供一个 Linux 虚拟机，其中包含我们整个系统的微缩版，以及开发工具。我们甚至在部署真正的系统时重用这些脚本。它一直工作得很好，但我最近对底层 VirtualBox VM 的资源使用感到沮丧。持续的笔记本风扇。

显而易见的解决方案是尝试用一个更高效的虚拟机管理程序取代 VirtualBox，这种虚拟机管理程序不会一直假装是一台完整的 PC。OSX 的最新版本包含一个虚拟机管理程序框架，允许无特权的、符合应用商店的程序管理虚拟机。只有少数程序利用了这个框架，Docker 就是其中之一。

Docker Desktop for Mac 在 hypervisor 框架内运行一个高效的 Linux 虚拟机，然后在该虚拟机内运行您的容器。vagger 支持 docker 作为一个提供者，也支持 virtualbox、vmware 等。如果我们能弄清楚如何制作一个 Docker 容器，它的行为就像我们旧的 VirtualBox 虚拟机一样，那么我们应该能够让我们的环境在虚拟机管理程序中运行。

#### 你应该和 Docker 一起这么做吗？

此时，任何读过 docker 文档的人都会问你是否应该这样使用 docker。他们说*通过在自己的容器中运行一个应用程序来改变世界*,也许有一个编排框架。那会很酷，但是没有技术上的原因为什么你不能在一个容器中运行整个 Linux 发行版，这是我需要的，docker 很容易安装。

#### 流浪汉对集装箱有什么要求？

一旦你决定用 docker 来使用流浪者，主要的障碍就是你得提供自己的[基盒](https://www.vagrantup.com/docs/boxes/base.html)。这是一个运行 ssh 服务器的机器，有一个用流浪者的密钥对和无密码的 sudo 认证的流浪者用户。流浪者用一个 Dockerfile 文件来构建这个。在与我的流浪档案相邻的一个档案中，

```
RUN useradd vagrant \
  && echo "vagrant" | passwd --stdin vagrant \
  && usermod -a -G wheel vagrant

# allow vagrant to login
RUN cd ~vagrant \
  && mkdir .ssh \
  && echo "ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA6NF8iallvQVp22WDkTkyrtvp9eWW6A8YVr+kz4TjGYe7gHzIw+niNltGEFHzD8+v1I2YJ6oXevct1YeS0o9HZyN1Q9qgCgzUFtdOKLv6IedplqoPkcmF0aYet2PkEDo3MlTBckFXPITAMzF8dJSIFo9D8HfdOV0IAdx4O7PtixWKn5y2hMNG0zQPyUecp4pzC6kivAIhyfHilFR61RGL+GPXQ2MWZWFYbAGjyiYJnAmCP3NOTd0jMZEnDkbUvxhMmBYSdETk1rRgm+R4LOzFUGaHqHDLKLX+FIPKcF96hrucXzcWyLbIbEgE98OHlnVYCzRdK8jlqm8tehUc9c9WhQ== vagrant insecure public key" > .ssh/authorized_keys \
  && chown -R vagrant:vagrant .ssh \
  && chmod 0700 .ssh \ && chmod 0600 .ssh/authorized_keys \ && echo "vagrant ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/vagrant_user

# install sudo, sshd, scp
RUN yum -y install sudo openssh-server openssh-clients; systemctl enable sshd.service

EXPOSE 22

CMD ["/usr/sbin/init"] 
```

Enter fullscreen mode Exit fullscreen mode

另一个棘手的问题是，为了让 systemd(CentOS Linux 发行版中使用的 init 实现)在我们的容器中运行，我们必须通过将一些文件系统传递给`docker run`命令来公开它们。在流浪者文件中，

```
docker.create_args = ['--tmpfs', '/tmp:exec', '--tmpfs', '/run', '-v', '/sys/fs/cgroup:/sys/fs/cgroup:ro'] 
```

Enter fullscreen mode Exit fullscreen mode

当调用`docker run --tmpfs /tmp:exec ...`时，流浪者将使用这些参数

这告诉 Docker 给 systemd 一个 cgroup 文件系统的只读视图，以及内存中的/tmp 和/run 目录。(由于`/sys`不是 Docker Desktop 的文件共享首选项中的 OSX 文件夹之一，所以它从 Linux 盒子中暴露了真正在幕后运行 Docker 的文件夹)。如果不公开这些目录，systemd 将无法启动任何服务，包括 ssh 服务器。

一旦这些都准备好了，`vagrant up`应该能够自动构建 Dockerfile，然后通过 ssh 连接到容器来运行现有的游民配置步骤。从这一点来看，流浪者工作流是一样的，就好像它是使用一个 VirtualBox 提供者。然后，您可以通过将更多的供应步骤移动到 docker 文件中，以便更有效地重建游民暂居地。

希望这能帮到其他人用 Docker 对付流浪汉。具有这种设置和更多设置的工作配置位于[https://github.com/dholth/vagrant-docker/](https://github.com/dholth/vagrant-docker/)。