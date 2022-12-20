# Artifactory python 客户端

> 原文：<https://dev.to/pratham/artifactory-python-client-20i0>

### Py-Artifactory

[**Artifactory**](https://www.jfrog.com/artifactory) 是一个工件库管理器，它支持由不同技术创建的软件包。
它还可以与主要的 CI/CD 和 DevOps 工具集成。
本文展示了我们如何使用 python api 客户端与 artifactory 进行通信。

**先决条件:**

*   Python 2.7 或更高版本
*   libxml2/libxslt(在未来的版本中将被弃用)

```
 # Debian based
  sudo apt-get install libxml2-dev libxslt1-dev

  # RedHat based
  sudo yum install libxml2-devel libxslt-devel 
```

Enter fullscreen mode Exit fullscreen mode

**安装:**

*   在终端下面的命令中，`tag`指定了一个版本号。

```
 pip install git+https://github.com/veritasos/py-artifactory.git@<tag> 
```

Enter fullscreen mode Exit fullscreen mode

**用法:**

*   创建客户端实例

```
 from artifactory import Artifactory
  artifactory = Artifactory(
          url="http://127.0.0.1:8081",
          username="username",
          password="password",
          ) 
```

Enter fullscreen mode Exit fullscreen mode

*   列出用户

```
 user_list = artifactory.security.users.list() 
```

Enter fullscreen mode Exit fullscreen mode

*   获取用户

```
 user = artifactory.security.users.fetch("user.name") 
```

Enter fullscreen mode Exit fullscreen mode

*   创造用户

```
 user = artifactory.security.users.new()
  user.name = "first.last"
  user.password = "test"
  user.email = "first.last@testartifactory.com"
  user.groups = ["readers"]
  response = user.create() 
```

Enter fullscreen mode Exit fullscreen mode

*   还有更多......

```
 Artifactory Users
  Artifactory Groups
  Artifactory Permissions
  Artifactory Repositories
  Artifactory Repository Replication
  Artifactory LDAP
  Artifactory User Api Keys 
```

Enter fullscreen mode Exit fullscreen mode

**详细文档:**

*   这里提供了更详细的文档 [**py-artifactory**](https://github.com/VeritasOS/py-artifactory)
*   [**随时贡献回来**](https://github.com/pratz/py-openemm)