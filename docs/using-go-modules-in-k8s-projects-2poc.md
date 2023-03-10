# 在 K8S 项目中使用 go 模块

> 原文：<https://dev.to/wallyqs/using-go-modules-in-k8s-projects-2poc>

在我维护的项目中，开始越来越多地采用`go mod`而不是其他工具，但出于某种原因，我不断遇到以下问题:[https://github.com/kubernetes/client-go/issues/551](https://github.com/kubernetes/client-go/issues/551)

一些解决方案被写进了那期杂志，更多的注释被添加到了 client-go 中，但是到目前为止，对我来说似乎有效的是下面的 _(ツ)_/

```
module github.com/wallyqs/my-k8s-module

go 1.12

require (
    k8s.io/api kubernetes-1.13.4
    k8s.io/apimachinery kubernetes-1.13.4
    k8s.io/client-go v10.0.0
    k8s.io/kubernetes v1.13.4
) 
```

Enter fullscreen mode Exit fullscreen mode