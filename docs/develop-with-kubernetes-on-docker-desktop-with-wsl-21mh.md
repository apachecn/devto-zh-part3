# 用 kubernetes 在 Docker 桌面上用 WSL 开发

> 原文：<https://dev.to/zeerorg/develop-with-kubernetes-on-docker-desktop-with-wsl-21mh>

Docker Desktop for Windows 自带 kubernetes。它在你的 Windows 机器上预配置了很多东西，比如下载 kubectl，并把所有东西都放在 PATH 变量中，这样就可以从 powershell 访问它。但是许多开发人员已经喜欢上了 WSL，它提供了一个更加 linux 本地的开发环境。这篇文章将介绍为 wsl 设置 kubernetes 的步骤。

您应该安装 docker for desktop 并启用 WSL。

步骤:

1.  **启用 kubernetes** 如果您还没有这样做的话。进入 docker 桌面设置- > Kubernetes 标签- >勾选“启用 Kubernetes”设置。在状态显示“kubernetes 正在运行”之后，在 powershell 中运行以下命令。

```
kubectl get nodes 
```

如果它显示`docker-for-desktop`,则意味着 kubernetes 运行正常。

[![Kubernetes Setting](img/04dd9f90fc586672cf2deb0dc35dddee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NBeD8TTP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zeerorgprocessedblog.blob.core.windows.net/photos/enable-k8s-docker-desktop.png)

1.  打开 WSL 控制台，使用以下命令安装 kubectl

```
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl 
```

1.  设置 kubeconfig 文件:这是神奇的一步，kubernetes 在你的主机上的 6443 端口上运行，要访问它，你需要一个 kubeconfig 文件。对于 windows 环境，docker 已经完成了这项工作，但是对于 WSL，您需要获取配置文件。事实证明这很简单。只需在 bash 中运行以下命令，并用您的 windows 用户名(不是 WSL 用户名)替换{username}

```
cp /mnt/c/Users/{username}/.kube/config ~/.kube/config 
```

这个命令将您的 kubeconfig 文件从 windows 复制到 wsl。

1.  **全部完成**:只需运行

```
kubectl get nodes 
```

查看一个`docker-for-desktop`节点。