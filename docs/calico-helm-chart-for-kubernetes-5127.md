# Kubernetes 的印花布头盔图

> 原文：<https://dev.to/joehobot/calico-helm-chart-for-kubernetes-5127>

虽然之前我在寻找 Calico helm chart，并且有一些使用`kubectl`等的部署文件在网络上浮动，但是我真的喜欢通过按一个按钮来自动化事情。因此，这是一个印花布头盔图表，需要一些爱，如果你到 Kubernetes。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ mrzobot ](https://github.com/mrzobot) / [卡利科-赫尔姆-海图](https://github.com/mrzobot/calico-helm-chart)

### 印花布舵图

<article class="markdown-body entry-content container-lg" itemprop="text">

# 印花舵轮图

我拿了 AWS/EKS Calico 安装文件，你可以在这里找到[https://docs . AWS . Amazon . com/eks/latest/user guide/Calico . html](https://docs.aws.amazon.com/eks/latest/userguide/calico.html)并把它分成几个文件，组成了整个舵图。

我还没有创建 values.yaml 文件并将其模板化，所以如果您有建议或时间，请随时更新它。我只是使用舵图来测试一些自动化部署的东西。

# 装置

克隆此存储库

然后跑

```
helm install . --name=calico --namespace=kube-system 
```

如果需要引用 tiller，只需添加-tiller-namespace = NamespaceWhereTillerIsInstalled

# 笔记

这张舵轮图非常简单，但是你可以随意叉开它或者做出改变。我将看到对值文件进行更新，并开始在模板中描绘 yaml 基础设施。

</article>

[View on GitHub](https://github.com/mrzobot/calico-helm-chart)