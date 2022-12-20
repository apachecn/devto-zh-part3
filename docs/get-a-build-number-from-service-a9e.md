# 从服务获取内部版本号

> 原文：<https://dev.to/liejuntao001/get-a-build-number-from-service-a9e>

最近，我需要从 Jenkins 作业的外部服务中检索内部版本号。通常，Jenkins 作业在其显示名称中使用 BUILD_NUMBER，或作为版本的一部分。
BUILD_NUMBER 只是一个序列号，和具体的作业绑定在一起。
如果为同一个项目设置了几个作业，或者是由于权限控制，或者是位于多个 Jenkins 实例中，那么最好从外部服务中检索构建号以保持版本一致。

所以我在 GitHub 上想出了两个小项目:
[Jenkins 共享库从远程服务](https://github.com/liejuntao001/jenkins-buildnumber-lib)检索构建号

[获取增量号码的服务](https://github.com/liejuntao001/buildnumber)

第一个是詹金斯用的库。获得一个简单到一行的内部版本号。

```
def build_number = buildnumber.get(server, uuid) 
```

第二个是 Go 写的简单服务。它会在每次调用时返回递增的数字。将其部署在您自己的服务器上，作为 docker，或者部署在 Kubernetes 集群中。

快速演示:

```
curl  -i  -H "Content-Type: application/json" -X POST https://buildnumber1.herokuapp.com/e9461f1c-ef78-4162-bcb7-e83da7287614 
```

我的第一篇文章。感谢阅读。