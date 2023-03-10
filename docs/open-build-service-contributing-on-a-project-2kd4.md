# 开放构建服务-参与项目

> 原文：<https://dev.to/foursixnine/open-build-service-contributing-on-a-project-2kd4>

在 SUSE 这里，我们大量使用[开放构建服务](http://openbuildservice.org)，并且经常在合作一个项目时(在我的例子中，是 [openQA](https://openqa.opensuse.org) )，一个人必须时不时地添加一个新的包作为依赖项，或者必须对一个旧的 SLE 或 openSUSE Leap 版本进行反向移植

它归结为以下步骤，在这种情况下，我想更改项目以针对 SUSE:SLE-12-SPX:Update 而不是 SUSE:SLE-12-SPX:GM 进行构建，SUSE:SLE-12-SPX:GM 是一个[构建目标](https://en.opensuse.org/openSUSE:Build_Service_supported_build_targets)，它将获得更新，而 GM 没有，所有这些，因为我想将 openvswitch 添加到项目中，以便我们可以在 openQA 部署中使用新功能。

要做到这一点，在设置 obs 帐户后，可以归结为:

1-分支您的项目 2- [链接](https://en.opensuse.org/openSUSE:Build_Service_Concept_project_linking)您的包 2-如果需要，修改项目元数据 3-如果出现与多个选择相关的错误，修改项目配置(PostgreSQL 肯定会在那里！)4-在等待建造
的时候，喝杯咖啡/茶/水，读读[的书](https://en.opensuse.org/openSUSE:Build_Service_Tips_and_Tricks)

```
# Branch the project
osc branch devel:openQA:SLE-12

# Link the new package
osc linkpac openSUSE:Factory openvswitch
# More and more packages will say that their dependencies cannot be resolved, this is
# you might spend some time here adding bunch of dependencies :)
osc linkpac openSUSE:Factory dpdk devel:openQA:SLE-12
osc linkpac openSUSE:Factory python-six devel:openQA:SLE-12 
```

此时，您可能会在 webUI 上看到错误消息，指出:

```
$a_package: have choice for $conflicting_package needed by $a_package: $options 
```

举个例子，你可能会在那里看到 postgres-server，有 postgres96-server 和 postgres94-server 作为`$options`，你必须选择你的命运！。

当你发现这一点，是时候编辑项目配置:

```
# Since
osc meta prjconf devel:openQA:SLE-12 -e
# An editor will open and you will be able to change stuff
# Remember that you need write permissions on the project!
...
Prefer: postgresql96-devel
Prefer: postgresql96-server
Prefer: python-dateutil
... 
```

修改项目元数据以使用:Updates 而不是:GM，如果需要的话，更改架构。

```
# same as before: An editor will open, and you will be able to edit stuff
 osc meta prjconf devel:openQA:SLE-12 -e
  <repository name="SLE_12_SP4">
    <path project="SUSE:SLE-12-SP4:Update" repository="standard"/>
     <arch>x86_64</arch>
    <arch>aarch64</arch>
    <arch>ppc64le</arch>
  </repository> 
```

在这之后，一个项目重建将会发生，坐下来，给一些更多的阅读:)