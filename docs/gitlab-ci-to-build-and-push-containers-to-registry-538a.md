# [ 🇬🇧 ] Gitlab CI 构建容器并将其推送到注册表

> 原文：<https://dev.to/bzinoun/gitlab-ci-to-build-and-push-containers-to-registry-538a>

我们都知道 Gitlab CI build 使用 docker 映像来完成这项工作，但是您曾经尝试过在 gitlab CI build 内部构建 docker 映像吗？

正如我们所知，gitlab CI 始于 docker 容器。因此，当我们想要在 gitlab CI build 中构建 docker 映像时，它是 docker in docker (DinD)

[![](img/f871d3e515a1cae99918625af24b3567.png)](https://i.giphy.com/media/m1UTexVjvh2WA/giphy.gif)

* * *

没有过渡，让我们看一下`.gitla-ci.yml`文件:

```
 image: docker:latest
    variables:
     DOCKER_DRIVER: overlay2
    stages:
      - build
      - push
    services:
      - docker:dind
    before_script:
      # docker login needs the password to be passed through stdin for security
      # we use $CI_JOB_TOKEN here which is a special token provided by GitLab
      - echo -n $CI_JOB_TOKEN | docker login -u gitlab-ci-token --password-stdin $CI_REGISTRY
      - docker version
      - docker info

    after_script:
      - docker logout registry.gitlab.com
    Build:
      stage: build
      script:
        - docker pull $CI_REGISTRY_IMAGE:latest || true
        - >
          docker build
          --pull
          --cache-from $CI_REGISTRY_IMAGE:latest
          --tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
          .
        - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    Push_When_tag:
      stage: push
      only:
        # We want this job to be ran on tags only.
        - tags
      script:
        - docker pull $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
        - docker tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME
        - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME 
```

### 步骤 1 -图片&服务

```
 image: docker:latest
    variables:
     DOCKER_DRIVER: overlay2
    stages:
      - build
      - push
    services:
      - docker:dind 
```

亚 ml

我们首先定义 GitlabCI 构建将使用的 docker 映像。在我们的例子中，我们使用了最新的 docker 图像。

```
image: docker:latest 
```

> 以生产中的⚠️为例，我不推荐使用`latest`或`stable`版本。出于许多原因...
> 其中一个原因是可重复性，另一个原因是我们希望我们的管道在 10 个月或 10 年内投入使用。如果需要新功能，则计划进行升级。

```
 variables:
     DOCKER_DRIVER: overlay2 
```

yaml
当使用`docker:dind`时，Docker 使用`vfs`存储驱动程序，它在每次运行时复制文件系统。这是一个非常磁盘密集型的操作，如果使用不同的驱动程序可以避免，例如`overlay2`

### 第二步-脚本前后

```
 before_script:
      # docker login needs the password to be passed through stdin for security
      # we use $CI_JOB_TOKEN here which is a special token provided by GitLab
      - echo -n $CI_JOB_TOKEN | docker login -u gitlab-ci-token --password-stdin $CI_REGISTRY
      - docker version
      - docker info

    after_script:
      - docker logout registry.gitlab.com 
```

yaml
这一步没什么特别的:

*   连接到 Gitlab 注册表
*   检查 docker 守护程序和配置
*   从 docker 注册表注销

### 步骤 3 -构建和推送

```
 Build:
      stage: build
      script:
        - docker pull $CI_REGISTRY_IMAGE:latest || true

        # notice the cache-from, which is going to use the image we just pulled locally
        # the built image is tagged locally with the commit SHA, and then pushed to 
        # the GitLab registry
        - >
          docker build
          --pull
          --cache-from $CI_REGISTRY_IMAGE:latest
          --tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
          .
        - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

We pull the last pushed image on the registry ; the `|| true` assure that the pipeline will not fail if no image was found . 

After pulling the last image , this one will be used for the cache when building a new image using the `--cache-from` . 

Then we push to registry with the image flagged with `$CI_COMMIT_SHA` that contains the commit SHA . 
```

雅姆

### 第四步-标签管理

```
 Push_When_tag:
      stage: push
      only:
        - tags
      script:
        - docker pull $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
        - docker tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME
        - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME 
```

我们希望让 Git 标签和 Docker 标签保持同步。这在调试和试图重现特定版本错误时很有帮助。

如果您还没有实现这一点的自动化，那么您可能会发现自己正处于这样一种情况:“这个图片是哪个 git 标签？”。

⚠️只有在创建标签时才会触发此阶段。