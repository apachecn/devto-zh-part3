# 从 GitHub 迁移到 Gitea

> 原文：<https://dev.to/jmarhee/migrating-from-github-to-gitea-26ei>

出于各种原因，我最近从公共 Github 配置文件迁移到了私有 git 基础设施。我尝试的解决方案之一是 Gitea，这是一个开源的自托管解决方案，具有类似 Github 的 UI 和非常易于访问的 API。

Gitea 在 UI 中有导入存储库的工具，但是我有很多 repos 要重新创建，并且希望快速迁移，所以我编写了一个脚本来使用两端的 API:

将 Github python 客户端、`os`、`re uests`和`json`包导入到您的脚本中，并插入您的 Github 和 Gitea 凭证以及端点(服务器地址)信息:

```
from github import Github
import os
import json
import requests

#Github GH_ACCESS_TOKEN = os.environ['GH_ACCESS_TOKEN']
#Gitea GITEA_ACCESS_TOKEN = os.environ['GITEA_ACCESS_TOKEN']
GITEA_USER = ""
GITEA_PASS = ""
TARGET_HOST = "https://git.yourserver.co"
MIGRATE_URI = "/api/v1/repos/migrate"
ENDPOINT = "%s%s" % (TARGET_HOST, MIGRATE_URI)

g = Github(GH_ACCESS_TOKEN)

EXCLUDE = [] 
```

在`EXCLUDE`中，输入您不想迁移的 Github 中以逗号分隔的 repo 名称列表，即:

```
EXCLUDE = ["repo1", "repo2"...] 
```

我们第一个函数将只是建立一个你的回购清单，以及一些与 Gitea 相关的信息，需要在最后创建一个新的回购，大部分只是名称，描述，是否是私有的，以及从中克隆的 URL:

```
def getRepos(g):
    repos = []
    for repo in g.get_user().get_repos():
            r = {}
            r['name'] = str(repo.name)
            r['url'] = str(repo.url)
            r['description'] = str(repo.description)
            r['private'] = str(repo.private)
            repos.append(r)
    return repos 
```

这将返回一个字典列表，结构如下:

```
{"name": "repo_name", "url": "https://github.com/...", ... } 
```

我们将启动的下一个函数将接收一个如上所示的对象，以创建一个新的存储库:

```
def createRepo(source_url,name,description,private):
    headers = { "accept": "application/json", "content-type": "application/json" }
    headers["Authorization"] = "token %s" % (GITEA_ACCESS_TOKEN)

    migrate_data = { "mirror": "false",  "uid": 1 }
    migrate_data["auth_password"] = "%s" % (GITEA_PASS)
    migrate_data["auth_username"] = "%s" % (GITEA_USER)
    migrate_data["description"] = "%s" % (description)
    migrate_data["repo_name"] = "%s" % (name)
    migrate_data["private"] = "%s" % (private)
    migrate_data["clone_url"] = "%s" % (source_url)
... 
```

上面是创建所需的认证头，然后是带有我们在`getRepos`函数中指定的回购数据的请求体。我们将通过发送以下请求来完成此功能:

```
...

    try:
        r = requests.post(url=ENDPOINT, data=json.dumps(migrate_data), headers=json.dumps(headers))

        if r.status_code != 200:
            return "Non-OK Response: %s" % (r.status_code)
        else:
            return "Done: %s" % (source_url)
    except Exception as e:
        return e 
```

现在，为了将这两个任务连接在一起，我们将创建一个处理程序来创建存储库列表，然后实例化我们的第二个函数来从中创建新的 repo:

```
def runMigration(r,x):
    exclude_repos = x
    for repo in r:
        if repo not in exclude_repos:
            print "Working on %s" % (repo['name'])
            print createRepo(repo['url'],repo['name'],repo['description'],repo['private'])
        else:
            print "Excluding %s" % (repo['name'])
    return "Done" 
```

您将看到这将迭代回购列表，检查您没有排除它，然后创建新的回购。

将所有这些放在一起，它将看起来像这样，并准备运行:

[Github 迁移脚本](https://bitbucket.org/snippets/jmarhee/Eea5gn)

对于任何给定的 git 解决方案，类似这样的脚本都可以类似地处理来自 Github 的数据，并在新主机上创建新的 repos(如果它有 API，您的`createRepo`函数的类似流程可能就足够了！但是，您可以使用这些数据，并使用像 [GitPython](https://pypi.org/project/GitPython/) 这样库在本地创建新的克隆，等等。)-这有助于更轻松地迁移到分散平台！