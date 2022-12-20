# 使用 SuperGloo 安装 Istio 并路由流量

> 原文：<https://dev.to/peterj/using-supergloo-to-install-istio-and-route-traffic-3e3a>

**最初发布于[学习 Istio 博客](https://www.learnistio.com/blog/using_supergloo_to_install_istio_and_route_traffic)T3】**

有一段时间，在安装 Istio 时，类似“我如何包含 Grafana？”或者“如何启用 mTLS？”会弹出，这些问题的答案是一个特定的变量，您必须在安装 Istio Helm 图表时设置，或设置值，然后渲染模板并安装它。有太多的设置、控制杆和按钮可以推、拉或配置。幸运的是，Istio 有几个[内置的配置文件](https://istio.io/docs/setup/kubernetes/additional-setup/config-profiles/)可以用来安装它，这样会好一点。

SuperGloo 是来自 [solo.io](https://solo.io) 的一个开源项目，承诺简化你的服务网格的安装、管理和操作。

## 安装 SuperGloo

您可以使用下面的命令下载并安装 SuperGloo CLI。本文使用的这个 SuperGloo 版本是 0.3.15。

```
curl -sL https://run.solo.io/supergloo/install | sh 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，`supergloo`二进制文件位于`$HOME/.supergloo/bin`下。你可以按照安装后的说明把它添加到你的路径中，但是我通常只是创建一个指向`/usr/local/bin`文件夹的符号链接，就像这样:

```
ln -s $HOME/.supergloo/bin/supergloo /usr/local/bin/supergloo 
```

Enter fullscreen mode Exit fullscreen mode

安装完 CLI 后，您想做的第一件事是运行`supergloo init`命令。该命令将创建服务帐户、集群角色和绑定以及三个部署，所有这些都在`supergloo-system`名称空间中。

> 注意:在运行命令之前，确保将上下文切换到正确的 Kubernetes 配置/集群。

您可以运行`kubectl get pods -n supergloo-system`命令来检查是否以及何时所有 pod 都启动并运行:

```
$ kubectl get po -n supergloo-system
NAME                             READY   STATUS    RESTARTS   AGE
discovery-68df78f9c-rk2w4        1/1     Running   0          58s
mesh-discovery-bc9fbcb7f-z84r9   1/1     Running   0          58s
supergloo-59445698c5-p2fvm       1/1     Running   0          58s 
```

Enter fullscreen mode Exit fullscreen mode

## 用 SuperGloo 安装 Istio

使用 SuperGloo 安装 Istio 有两种选择——一种是使用 CLI 并指定配置设置，第二种是使用名为 install 的自定义资源来定义配置。

这里有不同的标志(从版本`0.3.15`开始)你可以设置安装 Istio:

```
--auto-inject                     enable auto-injection? (default true)
--egress                          enable egress gateway?
--grafana                         add grafana to the install?
--ingress                         enable ingress gateway?
--installation-namespace string   which namespace to install Istio into? (default "istio-system")
--jaeger                          add jaeger to the install?
--mtls                            enable mtls? (default true)
--prometheus                      add prometheus to the install?
--version string                  version of istio to install? available: [1.0.3 1.0.5 1.0.6] (default "1.0.6") 
```

Enter fullscreen mode Exit fullscreen mode

我们将从这个设置开始——super gloo(`1.0.6`)中支持的最新 Istio，打开自动注入，启用 mTLS 和 ingress。稍后，我们将尝试添加/启用附加功能。

运行以下命令，使用 SuperGloo 默认值安装 Istio】

```
$ supergloo install istio --name istio --ingress
+---------+------------+---------+---------------------------+
| INSTALL |    TYPE    | STATUS  |          DETAILS          |
+---------+------------+---------+---------------------------+
| istio   | Istio Mesh | Pending | enabled: true             |
|         |            |         | version: 1.0.6            |
|         |            |         | namespace: istio-system   |
|         |            |         | mtls enabled: true        |
|         |            |         | auto inject enabled: true |
|         |            |         | grafana enabled: false    |
|         |            |         | prometheus enabled: false |
|         |            |         | jaeger enabled: false     |
|         |            |         | ingress enabled: true     |
|         |            |         | egress enabled: false     |
+---------+------------+---------+---------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

> 还有一种交互模式，SuperGloo CLI 会带您浏览所有选项，并询问您想要哪个选项。您可以通过添加如下的`-i`标志来运行它:`supergloo install istio -i`

要检查安装进度，您可以运行`kubectl get po -n istio-system`，一旦 pod 上的状态为正在运行或已完成，网格就成功安装。

最后，让我们用`istio-injection=enabled`标记`default`名称空间，这样我们就不需要手动注入 sidecar 代理:

```
kubectl label namespace default istio-injection=enabled 
```

Enter fullscreen mode Exit fullscreen mode

您可以运行下面的命令来检查哪些名称空间启用了自动注入:

```
$ kubectl get namespace -L istio-injection
NAME               STATUS   AGE    ISTIO-INJECTION
default            Active   121m   enabled
istio-system       Active   69m
kube-public        Active   121m
kube-system        Active   121m
supergloo-system   Active   100m 
```

Enter fullscreen mode Exit fullscreen mode

## 用 SuperGloo 进行交通路由

启用自动注入后，您可以首先部署 Hello Web(这个简单的 Web 站点只是调用底层的欢迎服务并返回问候):

```
cat << EOF | kubectl apply -f -
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
    name: helloweb
    labels:
        app: helloweb
        version: v1
spec:
    replicas: 3
    template:
        metadata:
            labels:
                app: helloweb
                version: v1
        spec:
            containers:
                - image: learnistio/hello-web:1.0.0
                  imagePullPolicy: Always
                  name: web
                  ports:
                      - containerPort: 3000
                  env:
                      - name: GREETER_SERVICE_URL
                        value: 'http://greeter-service.default.svc.cluster.local:3000'
--------
kind: Service
apiVersion: v1
metadata:
    name: helloweb
    labels:
        app: helloweb
spec:
    selector:
        app: helloweb
    ports:
        - port: 3000
          name: http
EOF 
```

Enter fullscreen mode Exit fullscreen mode

让我们也部署 2 个版本的欢迎服务:

```
cat << EOF | kubectl apply -f -
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
    name: greeter-service-v1
    labels:
        app: greeter-service
        version: v1
spec:
    replicas: 3
    template:
        metadata:
            labels:
                app: greeter-service
                version: v1
        spec:
            containers:
                - image: learnistio/greeter-service:1.0.0
                  imagePullPolicy: Always
                  name: svc
                  ports:
                      - containerPort: 3000
--------
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
    name: greeter-service-v2
    labels:
        app: greeter-service
        version: v2
spec:
    replicas: 3
    template:
        metadata:
            labels:
                app: greeter-service
                version: v2
        spec:
            containers:
                - image: learnistio/greeter-service:2.0.0
                  imagePullPolicy: Always
                  name: svc
                  ports:
                      - containerPort: 3000
EOF 
```

Enter fullscreen mode Exit fullscreen mode

和 Kubernetes 服务:

```
cat << EOF | kubectl apply -f -
kind: Service
apiVersion: v1
metadata:
    name: greeter-service
    labels:
        app: greeter-service
spec:
    selector:
        app: greeter-service
    ports:
        - port: 3000
          name: http
EOF 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们为 Hello Web 部署一个网关和一个虚拟服务，这样我们就可以从外部访问这个服务:

```
cat <<EOF | kubectl create -f -
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
    name: gateway
spec:
    selector:
        istio: ingressgateway
    servers:
        - port:
              number: 80
              name: http
              protocol: HTTP
          hosts:
              - '*'
--------
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
    name: helloweb
spec:
    hosts:
        - '*'
    gateways:
        - gateway
    http:
        - route:
              - destination:
                    host: helloweb.default.svc.cluster.local
                    port:
                        number: 3000
EOF 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以访问网关，您将看到来自 v1 或 v2 pods 的响应，因为我们尚未创建任何路由规则。

我们可以为 greeter 服务部署一个 Istio 虚拟服务和一个目的地规则来完成这项工作，但是我们将使用 SuperGloo 来完成这项工作。SuperGloo 引入了一个名为 RoutingRule 的定制资源。这个资源的好处在于，无论您使用哪种底层网格(无论是 Istio 还是 Linkerd)，SuperGloo 都会相应地转换规则。

要应用路由规则，请在交互模式下运行以下 SuperGloo 命令——使用该模式，CLI 会向您提出一系列问题，并根据您的回答创建规则:

```
$ supergloo apply routingrule trafficshifting -i
? name for the Routing Rule:  greeter-service-v2
? namespace for the Routing Rule:  default
? create a source selector for this rule?  [y/N]:  N
? create a destination selector for this rule?  [y/N]:  y
? what kind of selector would you like to create?  Upstream Selector
choose upstreams for this selector
? add an upstream (choose <done> to finish):  supergloo-system.default-greeter-service-3000
? add an upstream (choose <done> to finish):  <done>
? add a request matcher for this rule? [y/N]:  N
? select a target mesh to which to apply this rule supergloo-system.istio
select the upstreams to which you wish to direct traffic
? add an upstream (choose <done> to finish):  supergloo-system.default-greeter-service-v2-3000
? add an upstream (choose <done> to finish):  <done>
? choose a weight for {default-greeter-service-v2-3000 supergloo-system} 100 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以在没有交互模式的情况下应用完全相同的规则:

```
supergloo apply routingrule trafficshifting \
    --name greeter-service-v2 \
    --dest-upstreams supergloo-system.default-greeter-service-3000 \
    --target-mesh supergloo-system.istio \
    --destination supergloo-system.default-greeter-service-v2-3000:100 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在尝试访问网关 URL，您将只收到来自迎宾服务 v2 的响应。

## 用 SuperGloo 启用 Grafana 和 Jaeger

如果你记得从本文开始，我们挑选了一个 Istio 网格的准系统安装。这是一个很好的开始方式——安装最小的特性集，尝试它们，然后在需要的时候扩展(如果你需要的话)。

让我们看看如何更新现有的由 SuperGloo 管理的 Istio 安装，以启用 Prometheus、Grafana 和 Jaeger。

要更新 Istio 安装，您可以使用`--update`标志并提供一组新的选项。请注意，您需要提供所有想要更改的选项，否则 SuperGloo 将恢复为默认值。例如:如果您最初启用了 ingress，但在更新安装时没有启用它，ingress 将被禁用。

您可以使用以下命令将 Grafana、Prometheus 和 Jaeger 添加到现有的 Istio 安装中:

```
$ supergloo install istio --update \
                          --name istio \
                          --ingress \
                          --jaeger --prometheus --grafana 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令更新了现有的 Istio 安装——您可以通过查看`istio-system`名称空间中的窗格来监视这些变化。

## 结论

[SuperGloo](https://github.com/solo-io/supergloo) 更容易处理 Istio 安装。更新现有安装就像运行 CLI 命令一样简单。不过 SuperGloo 还有更多——我们只看了安装和简单的流量路由——super Gloo 还支持安装 Gloo API 网关、Linkerd mesh 并管理它们。在即将到来的一篇文章中，我们将深入了解 SuperGloo 的其他功能。