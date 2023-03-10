# Kubernetes 入门(在家)—第 3 部分

> 原文：<https://dev.to/just_insane/getting-started-with-kubernetes-at-home-part-3-1bcc>

在本系列的前两部分中，我们研究了如何在实验室中建立一个生产 Kubernetes 集群。在本系列的第三部分中，我们将为我们的集群部署一些服务，比如[鳄梨酱](https://guacamole.apache.org/)和 [Keycloak](https://www.keycloak.org/) 。

此处提供了分步文档和更多服务示例[。](https://gitlab.com/just.insane/kubernetes/blob/master/docs/services/services.md)

## 鳄梨酱

鳄梨色拉酱是一款非常有用的软件，可以让你通过 RDP、SSH 或其他协议远程连接到你的设备。我广泛使用它来访问我的实验室资源，甚至当我在家的时候。

你可以使用这个[头盔图](https://github.com/Just-Insane/apache-guacamole-helm-chart)在你的 Kubernetes 集群上安装鳄梨色拉酱。步骤如下:

1.  从[这里](https://github.com/Just-Insane/apache-guacamole-helm-chart)克隆鳄梨酱头盔图
2.  对 [values.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/services/guacamole/values.yaml) 应用任何更改，如注释和入口设置。
3.  从`apache-guacamole-helm-chart`目录展开舵图
    *   `helm install . -f values.yaml --name=guacamole --namespace=guacamole`
4.  舵图会自动创建一个入口，您可以通过 [values.yaml](https://gitlab.com/just.insane/kubernetes/blob/master/src/services/guacamole/values.yaml) 的`hosts:`部分访问它

一旦您部署了 Helm Chart，您应该能够在 values.yaml 中指定的入口主机名处访问鳄梨色拉酱。

## 钥匙锁

Keycloak 是一个开源的单点登录解决方案，类似于微软的 ADFS 产品。你可以在我的[博客](https://homelab.blog/blog/security/keycloak-part-1-what-is-sso/)上了解更多关于 Keycloak 的信息。

在默认头盔报告中有一个稳定的键盘锁头盔图表，我们将使用它来部署键盘锁，你可以在这里找到它。

1.  将任何更改应用到[值](https://gitlab.com/just.insane/kubernetes/blob/master/src/services/keycloak/values.yaml)
2.  部署带数值的舵图表稳定/钥匙锁
    *   `helm install --name keycloak stable/keycloak --values values.yaml`
3.  创建一个[入口](https://gitlab.com/just.insane/kubernetes/blob/master/src/services/keycloak/ingress.yaml)
    *   `kubectl apply -f ingress.yaml`
4.  获取`keycloak`用户的默认密码。
    *   `kubectl get secret --namespace default keycloak-http -o jsonpath="{.data.password}" | base64 --decode; echo`

虽然这篇文章篇幅较短，但希望它能举例说明在 Kubernetes 中运行服务是多么容易。在本文中，我们主要看了预先制作的导航图，然而部署一个没有导航图的服务也同样容易。我更喜欢使用图表，因为我发现它比直接的 Kubernetes 清单文件更容易管理。

你可以在[https://gitlab.com/just.insane/kubernetes/](https://gitlab.com/just.insane/kubernetes/)查看我的公共 Kubernetes repo，了解更多信息和更多服务示例。