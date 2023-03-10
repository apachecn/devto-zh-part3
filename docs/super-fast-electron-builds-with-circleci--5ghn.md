# 用 CircleCI 制造超快电子🚀

> 原文：<https://dev.to/kontrollanten/super-fast-electron-builds-with-circleci--5ghn>

最近我从[特拉维斯](https://travis-ci.org)切换到 [CircleCI](http://circleci.com) ，因为我在让我的[特拉维斯赛段](https://docs.travis-ci.com/user/build-stages/)准确工作时遇到了一些问题。结果是压倒性的。

# 简单的 Docker 配置

由于 CircleCI 任务可以在 Docker 容器中运行，配置 T2 电子制造商多版本变得非常简单

```
# Travis
script:
  - |
    if [ "$TRAVIS_OS_NAME" == "linux" ]; then
      docker run --rm \
        --env-file <(env | grep -iE 'DEBUG|NODE_|ELECTRON_|YARN_|NPM_|CI|CIRCLE|TRAVIS|APPVEYOR_|CSC_|_TOKEN|_KEY|AWS_|STRIP|BUILD_') \
        -v ${PWD}:/project \
        -v ~/.cache/electron:/root/.cache/electron \
        -v ~/.cache/electron-builder:/root/.cache/electron-builder \
        electronuserland/builder:wine \
        /bin/bash -c "yarn --link-duplicates --pure-lockfile && yarn release --linux --win"
     fi

# CircleCI
deploy-linux:
  docker:
    - image: electronuserland/builder:wine
  steps:
    - run: yarn --link-duplicates --pure-lockfile
    - run: yarn release --linux --win 
```

Circle 配置更容易阅读和维护，因为一切都是基于 Docker 的，你不需要传递你想在容器中使用的环境变量。

# 全能调试支持

有了 Docker，您可以在本地运行相同环境下的作业，这使得一切都更容易调试。如果这还不够，您还可以将 SSH 放入构建容器。

# 超快

在推送提交后的 4 分钟内，我收到了来自

*   林挺
*   反应单元测试
*   电子单元测试
*   e2e 光谱测试

十分钟后，我也将部署 Linux 和 MacOS 版本，并准备下载。不再需要排队(至少目前如此),不再需要等待。由于 CI 环境应该支持我作为一名开发人员，所以我认为反馈要快，我永远不必等待超过 5-10 分钟，直到我得到回应。一个很大的好处是，我再也不用在我的版本中看到骄傲的旗帜了。🥳

要看我的整个圈配置，你可以在这里查看。