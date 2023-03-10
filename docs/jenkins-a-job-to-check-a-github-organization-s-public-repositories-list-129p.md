# Jenkins:检查 Github 组织的公共库列表的工作

> 原文：<https://dev.to/setevoy/jenkins-a-job-to-check-a-github-organization-s-public-repositories-list-129p>

[![](img/b0159ef6ab54f04d9c511029efbc58a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZNr-ArAg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2016/01/Jenkins.sh-600x600-e1453134979914.png) 正在进行 Github 库检查。

回想一下:我们的想法是进行这样的检查，以防开发人员中的某些人意外地将我们项目的私有存储库作为公共存储库共享，或者创建一个公共存储库而不是私有存储库——我们会得到这样一个新存储库的松散警报。

检查和发送 Slack 通知的工具写在 Github 的[Go:checking public repositories 列表中。去切片比较。第一个 Golang 体验](https://rtfm.co.ua/en/go-checking-public-repositories-list-in-github-go-slices-comparison-the-first-golang-experience/)帖子。

在这篇文章中–将创建一个 Docker 图像和一个 Jenkin 的作业，每晚运行以执行检查。

### `Dockerfile`

创建一个`Dockerfile`。

使用`golang:alpine`和:

1.  复制实用程序的源文件
2.  安装 Go 的依赖项
3.  将二进制文件构建到`/go/bin`目录中作为`github-checker`可执行文件
4.  添加默认操作–运行`/go/bin/github-checker`

文件:

```
# alpine as mininal image
FROM golang:alpine
# git for go get
RUN apk update && apk add --no-cache git
# copy source from a current dir
COPY go-github-public-repos-checker.go .
# install deps
RUN go get -d -v
# build to /go/bin
RUN go build -o /go/bin/github-checker
# set default entrypoint
CMD ["/go/bin/github-checker"] 
```

点击> > > 了解更多`CMD` vs `ENTRYPOINT` [。](http://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/)

建立一个形象:

```
$ docker build -t projectname/projectname-github-checker:1.0 . 
```

检查一下。

设置环境变量:

```
$ export GITHUB_ORG_NAME="rtfmorg"
$ export ALLOWED_REPOS="org-repo-1-pub org-repo-2-pub"
$ export SLACK_CHANNEL="#general"
$ export SLACK_URL="https://hooks.slack.com/services/T16***WRE" 
```

用`-e` :
运行容器传递变量

```
$ docker run -ti -e GITHUB_ORG_NAME=${GITHUB_ORG_NAME} -e ALLOWED_REPOS="${ALLOWED_REPOS}" -e SLACK_CHANNEL=${SLACK_CHANNEL} -e SLACK_URL=${SLACK_URL} projectname/projectname-github-checker:1.0
Checking org-repo-1-pub
OK: repo org-repo-1-pub found in Allowed
Checking org-repo-2-pub
OK: repo org-repo-2-pub found in Allowed 
```

推送至 DockerHub:

```
$ docker push projectname/projectname-github-checker:1.0 
```

### 詹金斯

创建一个新作业，并通过*管道脚本*启动 Docker:

[![](img/15a65ab4b8b52814e188594ba4e3a52b.png "Jenkins: проверка публичных репозиториев Github-организации")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_134704.png)

剧本本身:

```
node {
    stage('Check repositories') {
        docker.image('projectname/projectname-github-checker:1.0').run("-e GITHUB_ORG_NAME=${GITHUB_ORG_NAME} \
        -e ALLOWED_REPOS=${ALLOWED_REPOS} \
        -e SLACK_CHANNEL=${SLACK_CHANNEL} \
        -e SLACK_URL=${SLACK_URL}")
    }
} 
```

添加将传递给容器的参数。

`SLACK_URL`包含令牌，将其设置为*密码参数*。

`ALLOWED_REPOS`包含一个要由 Go 在实用程序中解析的列表，所以在引号中设置:

[![](img/a8d7b6cbea562f57a849c11e8b8b46ae.png "Jenkins: проверка публичных репозиториев Github-организации")](https://res.cloudinary.com/practicaldev/image/fetch/s--tNrHRThB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_132628.png)

添加日程安排时，可以使用 [crontab.guru](https://crontab.guru/) :

[![](img/21c9b294b9a2c44e23268e728aad2cd8.png "Jenkins: проверка публичных репозиториев Github-организации")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_124841.png)

运行作业，用于测试——在`ALLOWED_REPOS`参数中没有我们的公共库之一:

[![](img/689fe83447e699c9170b34f4f1df65b2.png "Jenkins: проверка публичных репозиториев Github-организации")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_135007.png)

完成了。

### 类似的帖子

*   <small>04/13/2019</small> [Go:查看 Github 中的公共存储库列表。去切片比较。第一次 Golang 体验。](https://dev.to/setevoy/go-checking-public-repositories-list-in-github-go-slices-comparison-the-first-golang-experience-1ddm) <small>(0)</small>
*   T002/09/2017 t1t2azure:将附加驱动器连接到 VM 并迁移 Jenkins t3t
*   <small>2/24/2017</small>[【Jenkins:输油管道″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″″](https://rtfm.co.ua/jenkins-pipeline-plagin-i-trigger-bilda-cherez-github-webhook/)
*   <small>03/15/2019</small>[Jenkins:Jenkins . model . runidmigrator 迁移警告:发现意外目录 lastscriptsuccessfulbuil](https://rtfm.co.ua/jenkins-jenkins-model-runidmigrator-domigrate-warning-found-unexpected-dir-lastsuccessfulbuil/)<small>(0)</small>