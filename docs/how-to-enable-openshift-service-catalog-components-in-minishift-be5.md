# 如何在 mindshift 中启用 openshift 服务目录组件

> 原文：<https://dev.to/craicoverflow/how-to-enable-openshift-service-catalog-components-in-minishift-be5>

原帖:[https://endaphelan . me/guides/open shift/how-to-enable-open shift-components-in-minishift](https://endaphelan.me/guides/openshift/how-to-enable-openshift-components-in-minishift)

**注意:这只适用于 OpenShift 版本> = 3.10.x**

有两种方法可以在 Minishift 中启用 OpenShift 服务目录组件。

推荐的方法是在集群已经运行时运行`minishift openshift component add`命令:

```
$ minishift openshift component add service-catalog
$ minishift openshift component add automation-service-broker
$ minishift openshift component add template-service-broker 
```

您也可以在`minishift start`期间启用组件。这仍然是 Minishift 的[实验特性](https://github.com/minishift/minishift/blob/master/docs/source/using/experimental-features.adoc)之一，所以你将需要设置环境变量`MINISHIFT_ENABLE_EXPERIMENTAL`以便它工作:

```
$ export MINISHIFT_ENABLE_EXPERIMENTAL=y 
```

然后使用额外的标志运行`minishift start`来启用目录组件:

```
$ minishift start --extra-clusterup-flags "--enable=*,service-catalog,automation-service-broker,template-service-broker" 
```