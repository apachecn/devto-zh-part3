# AKS 和应用程序网关:在入口资源中公开多个服务

> 原文：<https://dev.to/cmendibl3/aks-and-application-gateway-expose-more-than-one-service-in-an-ingress-resource-4cf0>

如果你为你的 AKS 集群安装了 Azure 应用网关入口控制器，你可能想要通过同一个公共 IP 公开多个服务，只需要改变 url 路径。为了实现这一点，您必须使用[后端路径前缀](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/annotations.md#backend-path-prefix)注释。

在下面的示例中，我创建了一个具有以下行为的入口:

1.  对**http://【Waf 公共 IP 或 DNS】/库存**的调用被发送到**库存**服务
2.  对**http://【Waf 公共 IP 或 DNS】/订单**的调用被发送到**订单**服务

```
--------
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: backend-ingress
  annotations:
    # Service are serving content in this path.
    appgw.ingress.kubernetes.io/backend-path-prefix: "/"
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /inventory/*
        backend:
          serviceName: inventory
          servicePort: 80
      - path: /orders/*
        backend:
          serviceName: orders
          servicePort: 80 
```

Enter fullscreen mode Exit fullscreen mode

请注意，**appgw.ingress.kubernetes.io/backend-path-prefix**的值被设置为: **/** ，这意味着规则中指定的路径在发送到您的服务时被重写为这个值。

希望有帮助。