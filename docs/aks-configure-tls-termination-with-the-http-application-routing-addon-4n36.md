# AKS:使用 http 应用程序路由插件配置 TLS 终止

> 原文：<https://dev.to/cmendibl3/aks-configure-tls-termination-with-the-http-application-routing-addon-4n36>

当你安装一个 AKS 集群时，你可以配置它来部署 [http 应用程序路由插件](https://docs.microsoft.com/en-us/azure/aks/http-application-routing)或者你可以更新一个现有的集群来部署它。

无论哪种方式，您最终都会在集群的 **kube-system** 名称空间中运行一个 [NGINX 入口控制器](https://github.com/kubernetes/ingress-nginx)，该控制器具有以下属性:

*   *入口类:插件-http-应用程序-路由*
*   *注释-前缀:ginx .输入. kubernetes .我*

这是否意味着您可以使用此控制器进行 TLS 终止？答案是肯定的！你也可以使用速率限制和白名单，如我的帖子[中所述，通过 NGINX 入口控制器、tls 等来保护你的 Kubernetes 服务。](https://dev.to/2018/03/20/secure-your-kubernetes-services-with-nginx-ingress-controller-tls-and-more/)

因此，要尝试一下，请遵循上一篇文章的步骤 **2** 和 **5** ，但是一定要用下面的 yaml 替换 **ingress_rules.yaml** 文件的内容(不要忘记替换 DNS 区域名):

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: dni-function
  namespace: default
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - dni-function.<YOUR CLUSTERS DNS ZONE NAME>
    secretName: tls-secret
  rules:
  - host: dni-function.<YOUR CLUSTERS DNS ZONE NAME>
    http:
      paths:
      - path: /
        backend:
          serviceName: dni-function
          servicePort: 80 
```

Enter fullscreen mode Exit fullscreen mode

注意，**kubernetes.io/ingress.class**的值必须是:**addon-http-application-routing**

一旦 tls 开始工作，继续尝试速率限制和白名单！

希望有帮助。

请在这里下载所有代码和文件[，并确保查看](https://github.com/cmendible/kubernetes.samples/tree/master/15.http-application-routing-tls)[在线文档](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md)以了解更多关于注释和其他 NGINX 特性的信息。