# 使用 AWS Kubernetes EKS 集群进行记录

> 原文：<https://dev.to/simplytunde/logging-with-aws-kubernetes-eks-cluster-1m5l>

## 日志

EKS 是 AWS 提供的托管 kubernetes 产品，它可以减轻您管理自己的控制平面的压力，并减少一些控制，如您控制的内容。该功能在服务正式上市时不可用，但最近才可用。以下是它提供的日志类型；

*   API 服务器组件日志:您知道您的集群中验证请求、提供 api rest 端点等等的组件吗？这些是来自 apiserver 的日志，当试图诊断诸如为什么你的 pods 没有创建，许可控制器问题等等时，这些日志非常重要。

```
E0523 03:27:22.258958 1 memcache.go:134] couldn't get resource list for metrics.k8s.io/v1beta1: the server is currently unable to handle the request 
```

*   审计日志:有人在您的集群中进行了更改，您想知道是谁、在什么时候、做了什么。这个日志给了你这样的能力。

```
{
    "kind": "Event",
    "apiVersion": "audit.k8s.io/v1beta1",
    "metadata": {
        "creationTimestamp": "2019-05-23T02:08:34Z"
    },
    "level": "Request",
    "timestamp": "2019-05-23T02:08:34Z",
    "auditID": "84662c40-8d4f-4d3e-99b2-0d4005e44375",
    "stage": "ResponseComplete",
    "requestURI": "/api/v1/namespaces/default/services/kubernetes",
    "verb": "get",
    "user": {
        "username": "system:apiserver",
        "uid": "2d8ad7ed-25ed-4f37-a2f0-416d2af705e9",
        "groups": [
            "system:masters"
        ]
    },
    "sourceIPs": [
        "::1"
    ],
    "userAgent": "kube-apiserver/v1.12.6  (linux/amd64)  kubernetes/d69f1bf",
    "objectRef": {
        "resource": "services",
        "namespace": "default",
        "name": "kubernetes",
        "apiVersion": "v1"
    },
    "responseStatus": {
        "metadata": {},
        "code": 200
    },
    "requestReceivedTimestamp": "2019-05-23T02:08:34.498973Z",
    "stageTimestamp": "2019-05-23T02:08:34.501446Z",
    "annotations": {
        "authorization.k8s.io/decision": "allow",
        "authorization.k8s.io/reason": ""
    }
} 
```

*   认证器日志:EKS 用这个叫做 aws-iam-authenticator 的东西猜猜是什么？使用 AWS 凭据和角色对 EKS 集群进行身份验证。这些日志包含来自这些活动的事件

```
time="2019-05-16T22:19:48Z" level=info msg="Using assumed role for EC2 API" roleARN="arn:aws:iam::523447765480:role/idaas-kubernetes-cluster-idauto-dev-masters-role" 
```

*   控制器管理器:适用于那些熟悉 kubernetes 对象(如部署、副本等)的人；这些由 kubernetes 控制器管理器附带的控制器管理。要想知道这些控制器在做什么，你需要这些。

```
E0523 02:07:55.486872 1 horizontal.go:212] failed to compute desired number of replicas based on listed metrics for Deployment/routing/rapididentity-default-backend: failed to get memory utilization: unable to get metrics for resource memory: unable to fetch metrics from resource metrics API: the server is currently unable to handle the request (get pods.metrics.k8s.io) 
```

*   调度器:控制平面的这个组件顾名思义，在考虑了许多约束和可用资源后，将 pod 放在正确的节点上。要查看有关该组件如何做出决策的信息，请检查这些日志。

```
E0523 02:07:55.486872 1 horizontal.go:212] failed to compute desired number of replicas based on listed metrics for Deployment/routing/rapididentity-default-backend: failed to get memory utilization: unable to get metrics for resource memory: unable to fetch metrics from resource metrics API: the server is currently unable to handle the request (get pods.metrics.k8s.io) 
```

# 启用日志

您可以在 EKS 集群控制台中轻松启用日志，AWS 会更新您的集群，以便将这些日志发送到 cloudwatch。相应的 cloudwatch 日志组将显示在您的控制台中。对于那些使用 terraform 来配置集群的人来说，您只需传入您想要配置的日志类型，并创建日志组。

```
resource "aws_eks_cluster" "my_cluster" {
  depends_on = ["aws_cloudwatch_log_group.eks_log_group"]
  enabled_cluster_log_types = ["api", "audit"]
  name                      = "${var.cluster_name}"
  # ... other configuration ...
} 
```