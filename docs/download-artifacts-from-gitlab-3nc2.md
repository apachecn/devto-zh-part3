# 从 gitlab 下载工件

> 原文：<https://dev.to/c33s/download-artifacts-from-gitlab-3nc2>

从 [gitlab](https://gitlab.com/) 为工件调试[下载功能](https://docs.gitlab.com/ce/user/project/pipelines/job_artifacts.html#downloading-the-latest-artifacts)可能会非常麻烦，因为你必须自己创建它们，并且你没有容易的自动生成的链接，你可以简单地复制和粘贴。

根据获取工件最新 zip 文件的文档，你必须使用如下链接:

```
https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/master/download?job=build 
```

要从工件中获取一个文件，您必须(在本例中为`yaml-convert.phar`)使用类似于
的链接

```
https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/master/raw/yaml-convert.phar?job=build 
```

注意你必须使用准确的任务名称，首先我有一个阶段`build`，但是有两个任务`tag`和`master`，所以我的任务名称是`build:master`和`build:tag`，如果你使用`https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/master/download?job=build`，结果是 404

我没有尝试使用`build:master`作为下载 url 的作业名称，我将我在`.gitlab-ci.yml`中的作业重命名为`build-master`，并将 url 更改为`https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/master/download?job=build-master`，这在没有 404 的情况下也能工作。

在重构了`.gitlab-ci.yml`文件后，我移除了第二个任务，并在一个构建任务中处理了所有的东西，以获得更好的 URL:

```
# zip file download
https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/master/download?job=build

# zip file download from tag
https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/2.0.0/download?job=build

# single file download
https://gitlab.com/c33s-group/yaml-convert/-/jobs/artifacts/master/raw/yaml-convert.phar?job=build 
```

链接:

*   gratisography 的封面图片[https://www . pexels . com/photo/person-looking-searching-clean-2371/](https://www.pexels.com/photo/person-looking-searching-clean-2371/)