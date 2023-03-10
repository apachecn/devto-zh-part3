# 通过简单的 CI/CD 集成，使用 github 在 5 分钟内部署基于 Docker 的项目

> 原文：<https://dev.to/sunilmore690/deploy-docker-based-project-using-github-in-5-min-with-simple-ci-cd-integration-1n9n>

要使用 docker 方法设置新项目，请在 Ubuntu 上运行以下命令

```
curl https://s3-us-west-2.amazonaws.com/opt-scripts/project-setup.sh -o project-setup.sh && bash project-setup.sh
Sample Github Repo: https://github.com/sunilmore690/expressjexample\ 
```

通过运行这个脚本，它将要求以下事情

**Gihub 项目名称** : expressjexample

**Github 组织/用户名** : sunilmore690

**Git 分支**:主

**Github 访问令牌(可选)**:用于私有 Github 回购

**节点环境**:暂存/生产

**您的服务器将运行的应用端口** : 3000

脚本执行以下操作

*   安装坞站&坞站-合成
*   Install Git
*   克隆项目
*   设置合并后挂钩&如果在您提取代码时发生变化，它将执行 deployment.sh
*   如果 NODE_ENV 不是 production，它将设置 cron 来执行 git pull 以获取最新的代码
*   如果项目的根目录中不存在 deployment.sh，它将使用以下代码片段创建 deployment.sh
*   将 NODE_ENV 设置为环境变量
*   通过执行 deployment.sh 来部署项目

如果您没有在项目的根目录中添加 deployment.sh，那么脚本将在您的目录中创建 deployment.sh，代码片段如下

```
docker build -t <Github Project Name>.

docker stop <Github Project Name>-container

docker rm <Github Project Name>-container

docker run — restart always -d — name <Github Project Name>-container — env NODE_ENV=<NODE_ENV> -p 80:<PORT> <Github Project Name>:latest 
```

测试简单的 CI/CD 集成

如果你提供了 NODE_ENV 而不是 production，那么你可以把你的代码推送到 GitHub 分支，它会在 5 分钟内运行 deployment.sh

对于 NODE_ENV > >产品，您必须手动提取代码，通过提取代码，它将触发 deployment.sh

部署日志文件(deployment.log)将在项目主目录中提供。