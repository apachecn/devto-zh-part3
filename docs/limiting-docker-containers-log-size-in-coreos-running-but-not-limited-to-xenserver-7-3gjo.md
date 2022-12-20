# 在 CoreOS 中限制 docker 容器的日志大小(运行但不限于 XenServer 7)

> 原文：<https://dev.to/bong0/limiting-docker-containers-log-size-in-coreos-running-but-not-limited-to-xenserver-7-3gjo>

我遇到了一个问题:我的 gitlab docker 容器生成了超过 6GiB 的 json-log ( `/var/lib/docker/containers/<ID>/*.json`)来填满我的磁盘。

我在安装了容器管理补充包的 Citrix XenServer 7 中运行 CoreOS。

配置 Coreos 虚拟机的方法是通过/Cloud-Config/。有多种方法可以在 CoreOS 中配置 docker 服务来设置一些全局日志驱动选项。

CoreOS 的默认驱动是 **json-file** 没有限制。(截至 2018 年 12 月)

*   关闭 CoreOS 虚拟机
*   在 XenCenter 中，按如下方式编辑 Cloudconfig:要将日志文件限制设置为最大 60 MIB/容器和每个容器 1 个日志文件，请在 *write_files* 部分下面插入:

```
write_files:
  - path: "/etc/docker/daemon.json"
    permissions: "0644"
    owner: "root"
    content: |
      {
        "log-driver": "json-file",
        "log-opts": {
          "max-size": "60m",
          "max-file": "1"
        }
      } 
```

*   再次启动虚拟机

**重要的**:对这个全局设置的更改**只适用于新创建的容器**，所以你需要重新设置你想要应用设置的每个容器。如果不这样做，编辑`hostconfig.json`将没有帮助，它会在容器重启时被覆盖。

* * *

可选:

验证文件写入工作正常:

```
cat /etc/docker/daemon.json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "60m",
    "max-file": "1"
  }
} 
```

验证容器已经应用了日志设置(作为 root):
*/var/lib/docker/containers//host config . JSON*

```
{"Binds":["/srv/gitlab/config:/etc/gitlab","/srv/gitlab/logs:/var/log/gitlab","/srv/gitlab/data:/var/opt/gitlab"],
"ContainerIDFile":"",
"LogConfig":{"Type":"json-file","Config":{"max-file":"1","max-size":"60m"}},
"NetworkMode":"public"..... 
```

请参见上面的重要通知，如果这不能反映您看到的主机配置，请重新查看**日志配置**部分。