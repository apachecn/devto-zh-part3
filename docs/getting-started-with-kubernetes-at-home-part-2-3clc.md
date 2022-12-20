# Kubernetes 入门(在家)—第 2 部分

> 原文：<https://dev.to/just_insane/getting-started-with-kubernetes-at-home-part-2-3clc>

在本系列的第一部分中，我们研究了在一些 CentOS 7 虚拟机中安装一个裸机 Kubernetes 集群。在这一部分中，我们将研究如何设置一些后端服务，比如负载平衡器和入口。

正确运行 Kubernetes 集群需要的一些重要的东西是一个[存储类](https://kubernetes.io/docs/concepts/storage/storage-classes/)或通过[卷](https://kubernetes.io/docs/concepts/storage/volumes/)的手动存储配置，一个[负载平衡器](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)虽然不是严格需要的，但使访问服务更加容易，以及一个[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)。

[逐步文档在这里](https://gitlab.com/just.insane/kubernetes/blob/master/docs/configuration/configuration.md)。

#### 设置舵

Helm 被称为“Kubernetes 的软件包管理器”，它是一个工具，用于以一种方便安装新应用程序的方式模板化 Kubernetes 清单文件。我在 Kubernetes 上安装所有服务时都使用 Helm，因为它简单易用。

如果您没有通过 Kubespray 启用可选的 Helm 安装，我们应该先这样做。

#### 1。下载头盔

如果你在 Mac 上，你可以通过 brew install kubernetes-helm 安装 Helm。Helm 文档中有其他安装方法的说明(解压. zip 文件并将其添加到 PATH 中)。

#### 2。用 RBAC 设置一个舵柄用户

您可以使用这个 [rbac-config.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/rbac-config.yaml) 文件来设置用户，下载它并运行以下命令:

`kubectl create -f rbac-config.yaml`

#### 3。初始化舵

现在我们必须通过部署舵柄来初始化舵。最简单的方法就是运行`helm init --service-account tiller`。由于 RBAC，我们必须指定蒂勒使用的服务帐户。

#### 持久存储

因为 Docker 以及 Kubernetes 需要存储任何持久数据，所以为 Kubernetes 提供一些存储空间是很重要的。如果您使用云提供商，这将为您处理，您将使用提供商的默认存储解决方案。在我们的例子中，我们需要告诉 Kubernetes 使用什么存储。

我们将为我们的集群使用 NFS 存储，这需要您有一个现有的 NFS 服务器。

一旦我们有了 NFS 服务器，我们将使用通过 Helm 获得的`nfs-client-provisioner`，通过 Kubernetes [存储类](https://kubernetes.io/docs/concepts/storage/storage-classes/)访问该存储。

只需运行`helm install stable/nfs-client-provisioner -name nfs-client -namespace kube-system -set nfs.server=10.0.40.5 -set nfs.path=/mnt/Shared/kubernetes`

让我们再细分一下，上面的命令执行以下操作:

1.  告诉舵安装舵图`stable/nfs-client-provisioner`
2.  给这个版本起一个名字`nfs-client`
3.  将`nfs-client`安装到`kube-system`名称空间
4.  将 NFS 服务器 IP 设置为 10.0.40.5。您应该根据需要进行更改
5.  将 NFS 路径设置为/mnt/Shared/kubernetes。您应该将它更改为 NFS 服务器上的共享路径

我们现在应该检查 nfs-client 的状态，以确保它运行正常，通过 helm status nfs-client 应该显示 ready。

#### 安装金属 LB

[MetalLB](https://metallb.universe.tf/) 是 Kubernetes 的一个[负载平衡器](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)，它允许 Kubernetes 服务在需要时检索局域网或广域网上的 IP 地址。这极大地简化了基于裸机(或虚拟机)的 Kubernetes 集群中服务的网络访问。

MetalLB 有一些我们必须遵守的要求，即我们必须有一个 Kubernetes 群集、一个受支持的群集网络配置、一些 IPv4 地址和支持 BGP 的可选硬件。前两个由 Kubespray 在第 1 节中自动处理，对于 IP 地址，您可以在与 Kubernetes 节点相同的网络上提供一个 DHCP 服务器范围之外的地址块。我没有使用 BGP 选项，因为我没有可用的 BGP 硬件。

你可以通过 Helm 用下面的命令`helm install -name metallb -f values.yaml stable/metallb`安装 MetalLB 一个示例 values.yaml 文件可以在[这里](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/metallb/values.yaml)找到。请注意`configInline`部分，它定义了 MetalLB 将分发的 IP 地址。

#### 安装 Nginx-Ingress

[Nginx-Ingress](https://github.com/kubernetes/ingress-nginx) 是 Kubernetes 的 [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) 服务。入口用于管理对集群中服务的外部访问。理解 nginx-ingress 是如何工作的很重要，所以我建议你通读一下 [GitHub 文档](https://github.com/helm/charts/tree/master/stable/nginx-ingress)。

一旦你这样做了，我们就可以通过 Helm 安装 Nginx-Ingress。你可以查看一下 [values.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/nginx-ingress/values.yaml) 作为例子，主要改变的是 Nginx 应该使用的`default-ssl-certificate`。

一旦你这样做了，你可以通过`helm install -name nginx-ingress -f values.yaml stable/nginx-ingress -namespace nginx-ingress`安装它，它会做以下事情。

1.  告诉舵安装舵图`stable/nginx-ingress`
2.  发布名称为`nginx-ingress`
3.  到`nginx-ingress`名称空间
4.  使用来自`values.yaml`的值

由于类型设置为 loadBalancer，MetalLB 将自动从提供的 IP 地址向服务提供外部 IP，您可以将 DNS 或端口转发到该 IP 地址，以便访问群集的服务。

#### 安装证书管理器

[Cert-Manager](https://docs.cert-manager.io/en/latest/) 是一项复杂的服务，允许我们从证书提供商处自动检索和更新 SSL 证书。它与 Nginx-Ingress 配合使用，确保我们的所有服务都可以通过 HTTPS 的安全连接获得。

正确设置 Cert-Manager 需要许多步骤。你应该从查看舵图文档[这里](https://github.com/jetstack/cert-manager/tree/master/deploy/charts/cert-manager)开始。

*先决条件*

1.  请单独安装 CustomResourceDefinition 资源

*   `kubectl apply -f [https://raw.githubusercontent.com/jetstack/cert-manager/release-0.7/deploy/manifests/00-crds.yaml](https://raw.githubusercontent.com/jetstack/cert-manager/release-0.7/deploy/manifests/00-crds.yaml)`

1.  为证书管理器创建命名空间

*   `kubectl create namespace cert-manager`

1.  标记证书管理器命名空间以禁用资源验证

*   `kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true`

1.  添加 Jetstack Helm 存储库

*   `helm repo add jetstack [https://charts.jetstack.io](https://charts.jetstack.io)`

1.  更新您的本地舵图表存储库缓存

*   `helm repo update`

*通过舵安装*

1.  对[值进行任何更改。yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/cert-manager/values.yaml) 尤其是`podDnsConfig/nameservers`
2.  展开舵图

*   `helm install -name cert-manager -namespace cert-manager -f values.yaml jetstack/cert-manager`

*验证安装*

这在官方文档中有所涉及，您可以在这里找到[。它让您创建一个测试发布者，并获得一个自签名证书，以确保一切正常工作。](https://docs.cert-manager.io/en/latest/getting-started/install.html#verifying-the-installation)

*创建生产发行人*

**注意**:为了简洁起见，省略了测试发布者的创建，但是请确保您首先尝试创建一个测试发布者，这样您就不会达到加密速率限制。

如果使用的是 Let's Encrypt 和 Cloudflare，可以使用这个 [letsencrypt.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/cert-manager/letsencrypt.yaml) 文件。请记住根据需要更改这些值，尤其是任何标有 changeme 的值。

准备好之后，运行`kubectl apply -f letsencrypt.yaml`来创建发行者。

*创建 Cloudflare API 密钥秘密*

现在我们需要将我们的 Cloudflare API 密钥添加到 Kubernetes secret 中。可以用 [cloudflare-secret.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/cert-manager/cloudflare-secret.yaml) 作为例子。

完成更改后，使用`kubectl apply -f cloudflare-secret.yaml`创建密码。

*创建默认证书*

我们现在可以创建默认证书，Nginx-Ingress 将使用该证书为我们的站点提供服务。参见 [certificate.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/cert-manager/certificate.yaml) 中的示例。

一旦您对文件进行了更改，关于名称和域，您可以使用`kubectl apply -f certificate.yaml`创建证书。

#### 创建入口

现在我们可以为 Kubernetes 仪表板创建一个入口，因此我们可以直接转到 https://kubernetes.domain.com 的 T2，而不是去 https://master node:6443/proxy/ect

第一步是创建仪表板入口，我们可以用 [ingress.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/configuration/dashboard/ingress.yaml) 来完成。一旦完成了更改，就可以用`kubectl apply -f ingress.yaml`来创建入口

通过 nginx-ingress 外部 IP 将域名添加到 DNS 后，您现在可以前往[https://kubernetes.domain.com](https://kubernetes.domain.com)访问您的 Kubernetes 仪表板。

现在，您已经有了一个在家里运行的完整设置的 Kubernetes 集群，您可以在其上测试您的服务，或者将其用作家庭实验室。