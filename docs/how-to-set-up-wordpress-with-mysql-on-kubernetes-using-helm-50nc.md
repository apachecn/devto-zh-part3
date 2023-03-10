# 如何使用 Helm 在 Kubernetes 上设置带有 MySQL 的 WordPress

> 原文：<https://dev.to/digitalocean/how-to-set-up-wordpress-with-mysql-on-kubernetes-using-helm-50nc>

### 简介

随着越来越多的开发人员在分布式环境中工作，像 [Kubernetes](https://kubernetes.io/) 这样的工具已经成为在动态构建和生产环境中保持应用程序组件标准化的核心。随着应用程序生态系统越来越复杂，Kubernetes 越来越受欢迎，帮助管理 Kubernetes 集群中资源的工具变得必不可少。

[Helm](https://helm.sh/) 是 Kubernetes 的开源包管理器，它简化了在 Kubernetes 集群上部署和升级应用程序的过程，同时还提供了一种方法来查找和共享打包为 *Kubernetes 图表*的现成应用程序。

在本教程中，我们将使用 Helm 在 Kubernetes 集群上设置 [WordPress](https://wordpress.com) ，以创建一个高度可用的网站。除了利用 Kubernetes 固有的可伸缩性和高可用性，这种设置通过 Helm 提供简化的升级和回滚工作流程，有助于保证 WordPress 的安全。

我们将使用外部 MySQL 服务器来抽象数据库组件，因为它可以是独立集群或托管服务的一部分，以实现扩展的可用性。完成本教程中描述的步骤后，您将在 Kubernetes 管理的容器化集群环境中拥有一个功能完整的 WordPress 安装。

## 先决条件

为了完成本指南，您需要具备以下条件:

*   启用了基于角色的访问控制 (RBAC)的 Kubernetes 1.10+集群。
*   安装在本地机器或开发服务器上的`kubectl`命令行工具，配置为连接到您的集群。请参见【Kubernetes 官方文档获取如何设置的说明。
*   安装在本地机器或开发服务器上的 [Helm](https://helm.sh/) 包管理器，以及安装在集群上的 Tiller，如本教程所述:[如何使用 Helm 包管理器](https://www.digitalocean.com/community/tutorials/how-to-install-software-on-kubernetes-clusters-with-the-helm-package-manager?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)在 Kubernetes 集群上安装软件。
*   具有 SSH 访问权限的外部 MySQL 服务器，以及 **root** MySQL 密码。要设置这个，你可以遵循我们的一个 MySQL 教程，比如:[如何在 Ubuntu 18.04 上安装 MySQL](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)。

在继续之前，请确保您能够登录到您的 MySQL 服务器，并且能够连接到您的 Kubernetes 集群。如果在您的`kubectl`配置文件中设置了多个集群，您应该通过从本地机器或开发服务器运行以下命令来确保您连接到了正确的集群:

```
kubectl config get-contexts 
```

Enter fullscreen mode Exit fullscreen mode

这是一个输出示例:

```
CURRENT   NAME                        CLUSTER                     AUTHINFO                          NAMESPACE
*         do-sfo2-wordpress-cluster   do-sfo2-wordpress-cluster   do-sfo2-wordpress-cluster-admin   
          minikube                    minikube                    minikube 
```

Enter fullscreen mode Exit fullscreen mode

星号(*)表示哪个集群当前是默认上下文。如果您需要更改当前上下文，请运行:

```
kubectl config use-context context-name 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该准备好遵循指南的其余部分。

## 步骤 1 —配置 MySQL

首先，我们将为 WordPress 创建一个专用的 MySQL 用户和一个数据库，允许来自外部主机的连接。这是必要的，因为我们的 WordPress 安装将位于 Kubernetes 集群中的一个单独的服务器上。如果你已经为 WordPress 设置了一个专用的 MySQL 用户和数据库，你可以跳到下一步。

在 MySQL 服务器上，使用以下命令登录 MySQL:

```
mysql -u root -p 
```

Enter fullscreen mode Exit fullscreen mode

系统会提示您提供首次安装该软件时为 **root** MySQL 帐户设置的密码。登录后，MySQL 会给你一个命令提示，你可以用它来创建 WordPress 所需的数据库和用户。

**注意:**在本教程中，我们将创建一个名为`wordpress`的数据库和一个名为`wordpress_user`的用户，由密码`password`标识。请注意，这些是不安全的示例值，您**应该在本指南中相应地修改**。

要创建数据库，可以使用以下语句:

```
CREATE DATABASE wordpress; 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们为这个数据库创建一个专用的 MySQL 用户:

```
CREATE USER wordpress_user IDENTIFIED BY 'password'; 
```

Enter fullscreen mode Exit fullscreen mode

用户`wordpress_user`已创建，但它还没有任何访问权限。以下命令将给予这个用户从本地和外部网络对 **wordpress** 数据库的管理访问权(所有特权):

```
GRANT ALL PRIVILEGES ON wordpress.* TO wordpress_user@'%'; 
```

Enter fullscreen mode Exit fullscreen mode

要更新管理访问权限的内部 MySQL 表，请使用以下语句:

```
FLUSH PRIVILEGES; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用以下命令退出 MySQL 客户端:

```
exit; 
```

Enter fullscreen mode Exit fullscreen mode

要测试更改是否成功，您可以再次登录 MySQL 命令行客户端，这一次使用新帐户`wordpress_user`进行身份验证:

```
mysql -u wordpress_user -p 
```

Enter fullscreen mode Exit fullscreen mode

您应该使用在用`CREATE_USER`语句创建这个 MySQL 用户时提供的相同密码。要确认您的新用户可以访问`wordpress`数据库，您可以使用以下语句:

```
show databases; 
```

Enter fullscreen mode Exit fullscreen mode

预期输出如下:

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| wordpress          |
+--------------------+
2 rows in set (0.03 sec) 
```

Enter fullscreen mode Exit fullscreen mode

在确认结果中包含了`wordpress`数据库之后，您可以使用以下命令退出 MySQL 命令行客户端:

```
exit; 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了一个 WordPress 专用的 MySQL 数据库，以及在其中使用的有效访问凭证。因为我们的 WordPress 安装将在一个单独的服务器上，我们仍然需要编辑我们的 MySQL 配置以允许来自外部主机的连接。

仍然在 MySQL 服务器上，使用您选择的命令行编辑器打开文件`/etc/mysql/mysql.conf.d/mysqld.cnf`:

```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf 
```

Enter fullscreen mode Exit fullscreen mode

在该文件中找到`bind-address`设置。默认情况下，MySQL 只监听`127.0.0.1`(本地主机)。为了接受来自外部主机的连接，我们需要将该值更改为`0.0.0.0`。您的`bind-address`配置应该是这样的:

```
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

完成这些更改后，保存并关闭文件。您需要使用以下命令重启 MySQL:

```
sudo systemctl restart mysql 
```

Enter fullscreen mode Exit fullscreen mode

要测试您是否能够远程连接，请从本地计算机或开发服务器运行以下命令:

```
mysql -h mysql_server_ip -u wordpress_user -p 
```

Enter fullscreen mode Exit fullscreen mode

记得将`mysql_server_ip`更改为您的 MySQL 服务器 IP 地址或主机名。如果您能够正确连接，现在就可以继续下一步了。

## 步骤 2——安装 WordPress

现在我们有了连接 MySQL 数据库的必要信息，我们可以继续使用 Helm 安装 WordPress。

默认情况下，WordPress 图表将 [MariaDB](https://mariadb.org/) 安装在集群内部的一个单独的 pod 上，并将其用作 WordPress 数据库。我们希望禁用这种行为，并将 WordPress 配置为使用外部 MySQL 数据库。这个和其他配置选项(比如默认的 WordPress admin 用户和密码)可以在安装时设置，或者通过命令行参数，或者通过一个单独的 YAML 配置文件。

为了使事情有条理并易于扩展，我们将使用一个配置文件。

从您的本地计算机或开发服务器上，为您的项目设置创建一个新目录，并导航到该目录:

```
mkdir myblog-settings
cd myblog-settings 
```

Enter fullscreen mode Exit fullscreen mode

接下来，使用您选择的文本编辑器创建一个名为`values.yaml`的文件:

```
nano values.yaml 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件中，我们需要设置一些变量来定义 WordPress 如何连接到数据库，以及一些关于你的站点和初始管理员用户的基本信息，以便在安装完成后登录到 WordPress。

我们将基于来自 [WordPress Helm chart](https://github.com/bitnami/charts/blob/master/upstreamed/wordpress/values.yaml) 的默认`values.yaml`文件进行配置。 ***博客/网站信息*** 部分包含你的 WordPress 博客的一般选项，比如博客的名字和初始用户证书。该文件的 ***数据库设置*** 部分包含连接远程 MySQL 服务器的设置。MariaDB 在最后一节被禁用。

将以下内容复制到您的`values.yaml`文件中，用您的自定义值替换高亮显示的值:

```
## Blog/Site Info
wordpressUsername: sammy
wordpressPassword: password
wordpressEmail: sammy@example.com
wordpressFirstName: Sammy
wordpressLastName: the Shark
wordpressBlogName: Sammy's Blog!

## Database Settings
externalDatabase:
  host: mysql_server_ip
  user: wordpress_user
  password: password
  database: wordpress

## Disabling MariaDB
mariadb:
  enabled: false 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚配置了以下选项:

*   **WordPress username**:WordPress 用户的登录。
*   **WordPress password**:WordPress 用户的密码。
*   **WordPress email**:WordPress 用户的邮件。
*   **Wordpress first name**:Wordpress 用户的名字。
*   **Wordpress last name**:Wordpress 用户的姓氏。
*   **wordpressBlogName** :网站或博客的名称。
*   **主机** : MySQL 服务器 IP 地址或主机名。
*   **用户** : MySQL 用户。
*   **密码** : MySQL 密码。
*   **数据库** : MySQL 数据库名。

完成编辑后，保存文件并退出编辑器。

现在我们已经完成了所有的设置，是时候执行`helm`来安装 WordPress 了。下面的命令告诉`helm`以`myblog`的名字安装 WordPress 图表的最新稳定版本，使用`values.yaml`作为配置文件:

```
helm install --name myblog -f values.yaml stable/wordpress 
```

Enter fullscreen mode Exit fullscreen mode

您应该会得到类似如下的输出:

```
NAME:   myblog
LAST DEPLOYED: Fri Jan 25 20:24:10 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Deployment
NAME              READY  UP-TO-DATE  AVAILABLE  AGE
myblog-wordpress  0/1    1           0          1s

==> v1/PersistentVolumeClaim
NAME              STATUS   VOLUME            CAPACITY  ACCESS MODES  STORAGECLASS  AGE
myblog-wordpress  Pending  do-block-storage  1s

==> v1/Pod(related)
NAME                               READY  STATUS   RESTARTS  AGE
myblog-wordpress-5965f49485-8zfl7  0/1    Pending  0         1s

==> v1/Secret
NAME               TYPE    DATA  AGE
myblog-externaldb  Opaque  1     1s
myblog-wordpress   Opaque  1     1s

==> v1/Service
NAME              TYPE          CLUSTER-IP     EXTERNAL-IP  PORT(S)                     AGE
myblog-wordpress  LoadBalancer  10.245.144.79  <pending>    80:31403/TCP,443:30879/TCP  1s

(...) 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，一个名为 **myblog-wordpress** 的服务将在您的 Kubernetes 集群中创建，但是可能需要几分钟容器才准备好，并且`External-IP`信息可用。要检查此服务的状态并检索其外部 IP 地址，请运行:

```
kubectl get services 
```

Enter fullscreen mode Exit fullscreen mode

您应该会得到类似如下的输出:

```
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
kubernetes         ClusterIP      10.245.0.1      <none>          443/TCP                      20h
myblog-wordpress   LoadBalancer   10.245.144.79   203.0.113.110   80:31403/TCP,443:30879/TCP   3m40s 
```

Enter fullscreen mode Exit fullscreen mode

该命令为您提供了关于在您的集群上运行的服务的详细信息，包括服务的名称和类型，以及这些服务使用的 IP 地址。从输出中可以看到，WordPress 安装在外部 IP 地址`203.0.113.110`上被用作`myblog-wordpress`。

**注意:**如果您使用`minikube`来测试这个设置，您将需要运行`minikube service myblog-wordpress`来暴露容器 web 服务器，以便您可以从您的浏览器访问它。

你的 WordPress 安装现在可以运行了。要访问管理界面，请使用从`kubectl get services`的输出中获得的公共 IP 地址，然后在 web 浏览器中使用`/wp-admin`:

```
http://203.0.113.110/wp-admin 
```

Enter fullscreen mode Exit fullscreen mode

[![Login screen](img/98acd43c8ff8ca4f467f949d45b26493.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0T66Z4ca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.digitalocean.com/articles/wordpress_kubernetes/admin_login.png)

你应该使用你的`values.yaml`文件中定义的凭证登录并开始配置你的 WordPress 站点。

## 第三步——升级 WordPress

因为它的流行，WordPress 经常成为恶意攻击的目标，所以保持它的更新是很重要的。我们可以用命令`helm upgrade`升级头盔。

要列出所有当前版本，请从本地计算机或开发服务器运行以下命令:

```
helm list 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到类似如下的输出:

```
NAME        REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
myblog      1           Fri Jan 25 20:24:10 2019    DEPLOYED    wordpress-5.1.2   5.0.3     default 
```

Enter fullscreen mode Exit fullscreen mode

从输出中可以看到，我们目前的 WordPress 版本是`5.0.3` (app 版本)，而 chart 版本是`5.1.2`。如果您想要将某个版本升级到新版本的图表，请先使用以下内容更新您的 Helm 存储库:

```
helm repo update 
```

Enter fullscreen mode Exit fullscreen mode

您可以预期以下输出:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈ 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以检查是否有新版本的 WordPress 图表可用:

```
helm inspect chart stable/wordpress 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似如下的输出:

```
apiVersion: v1
appVersion: 5.1.1
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 5.9.0 
```

Enter fullscreen mode Exit fullscreen mode

正如你从输出中看到的，有一个新的图表可用(版本 5.9.0)，带有 WordPress **5.1.1** (应用程序版本)。每当你想升级你的 WordPress 版本到最新的 WordPress 图表，你应该运行:

```
helm upgrade -f values.yaml myblog stable/wordpress 
```

Enter fullscreen mode Exit fullscreen mode

该命令将产生与`helm install`非常相似的输出。提供我们第一次安装 WordPress 图表时使用的配置文件是很重要的，因为它包含了我们为设置定义的自定义数据库设置。

现在，如果您再次运行`helm list`，您应该会看到关于您的版本的更新信息:

```
NAME    REVISION    UPDATED                     STATUS      CHART           APP VERSION     NAMESPACE
myblog  2           Fri May  3 14:51:20 2019    DEPLOYED    wordpress-5.9.0   5.1.1         default 
```

Enter fullscreen mode Exit fullscreen mode

您已经成功地将您的 WordPress 升级到最新版本的 WordPress 图表。

### 回滚一次发布

每次你升级一个版本，Helm 就会创建一个新的 ***版本*** 。修订版设置了一个固定的*检查点*，如果事情没有按预期进行，您可以回到那里。它类似于 Git 中的*提交*，因为它创建了一个可以比较和恢复的变更历史。如果在升级过程中出现问题，您可以使用`helm rollback`命令回滚到给定 Helm 版本的先前版本:

```
helm rollback release-name revision-number 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我们想要撤销升级并回滚我们的 WordPress 版本到它的第**个**版本，我们将使用:

```
helm rollback myblog 1 
```

Enter fullscreen mode Exit fullscreen mode

这将把 WordPress 安装回滚到它的第一个版本。您应该会看到以下输出，表明回滚成功:

```
Rollback was a success! Happy Helming! 
```

Enter fullscreen mode Exit fullscreen mode

再次运行`helm list`应该会显示 WordPress 被降级回 5.0.3，图表版本 5.1.2:

```
NAME        REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
myblog      3       Mon Jan 28 22:02:42 2019    DEPLOYED    wordpress-5.1.2   5.0.3         default 
```

Enter fullscreen mode Exit fullscreen mode

请注意，回滚一个版本实际上会基于回滚的目标修订创建一个新的修订。我们名为`myblog`的 WordPress 新闻稿现在的修订号是**三**，它是基于修订号**一**的。

## 结论

在本指南中，我们使用命令行工具 Helm 在 Kubernetes 集群上安装了带有外部 MySQL 服务器的 WordPress。我们还学习了如何将 WordPress 版本升级到新的图表版本，以及如何在升级过程中出错时回滚版本。

作为额外的步骤，你可以考虑用 Cert-Manager 设置 Nginx 入口，以启用基于名称的虚拟主机，并为你的 WordPress 站点配置 SSL 证书。你也应该检查一下我们在本指南中使用的 WordPress 图表的[推荐生产设置](https://github.com/helm/charts/tree/master/stable/wordpress#production-and-horizontal-scaling)。

如果您想了解更多关于 Kubernetes 和 Helm 的信息，请查看我们社区页面的 [Kubernetes](https://www.digitalocean.com/community/tags/kubernetes?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019) 部分。

* * *

[![CC 4.0 License](img/4662fd03838b335557c183fd57c6b02c.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

*本作品根据[知识共享署名-非商业性使用-类似共享 4.0 国际许可证](http://creativecommons.org/licenses/by-nc-sa/4.0/)* 进行许可