# 将现有城际安装升级到新的安装程序

> 原文：<https://dev.to/firmhouse/upgrading-an-existing-intercity-installation-to-the-new-installer-1cfk>

两天前，我发布了一个更新的更简单的安装方法，我将它发布到了 Intercity 的主分支:[更新了 installing Intercity 到一个命令](https://dev.to/firmhouse/updated-installing-intercity-to-a-single-command-3ah)。新的命令在全新的 LTS Ubuntu 上运行良好。🕺💃

然而，如果你已经通过前面的 [docs/installation.md](https://github.com/intercity/intercity-next/blob/ba483a3ddbb63555834963abbff9efc88b04922e/doc/installation.md) 中描述的`intercity-server`命令安装了 Intercity，你将不得不执行一些额外的步骤，因为数据库设置是不同的。您必须将当前的数据库迁移到新安装的数据库中。你肯定不想失去你所有珍贵的应用程序配置设置和秘密！

你要做的事情如下。我将在下面详细解释每个步骤。

1.  通过`pg_dump`导出当前城际数据库。
2.  停止您当前的城际安装。
3.  通过新的安装程序安装新的城际列车。
4.  将先前安装的数据库导入新安装的数据库。

在执行上述步骤之前:确保您有服务器的备份。例如，通过使用您的 VPS 提供商或云的备份/快照功能。

您也可以将目录`/var/intercity/shared/postgres_data`复制到`/var/intercity/shared/postgres_data_backup`中，例如:

```
$ sudo cp -r /var/intercity/shared/postgres_data /var/intercity/shared/postgres_data_backup 
```

好了，我们开始吧:

## 导出当前数据库

查找您当前城际安装的 Docker 容器 ID:

```
$ sudo docker ps 
```

您应该会看到类似这样的内容:

```
db76d2869d40    local_intercity/app "/sbin/boot" 
```

“db76d2869d40”是您当前城际安装的容器 ID。在接下来的几个命令中使用它来访问正在运行的容器中的 shell，导出数据库，并将其带回您的主机系统:

```
$ sudo docker exec -it db76d2869d40 bash
(container) # su intercity
(container) $ cd /home/intercity
(container) $ pg_dump -U intercity -d intercity -f intercity.sql
(container) $ exit
(container) # cp /home/intercity/intercity.sql /shared
(container) # exit 
```

现在，您已经成功地将数据库从当前的城际环境导出到主机系统上的文件`intercity.sql`中。我们将在接下来的步骤中使用该文件导入到您的新城际安装中。

## 停止当前安装

您可以使用在前面步骤中获取的容器 ID，通过以下命令安全地停止当前的城际:

```
$ sudo docker stop db76d2869d40 
```

## 通过新的安装程序安装城际

```
$ mkdir intercity
$ wget https://raw.githubusercontent.com/intercity/intercity-next/master/scripts/bootstrap.sh
$ sudo bash bootstrap.sh 
```

几分钟后，您的安装正在运行，“创建您的第一个用户”屏幕应该在您配置的域名上可见。请确认您看到了“创建您的第一个用户”屏幕，以确保您的新安装完全启动！

## 导入您的城际数据库

现在我们将把`intercity.sql`数据库导入到新的城际安装中。

为此，请运行以下命令:

```
$ sudo -s
# cat /var/intercity/shared/intercity.sql | docker-compose exec db psql -U postgres -d intercity 
```

您应该会在终端中看到很多输出。这表示正在导入数据库。您可能会在这个输出中看到很多错误，告诉您关系或索引已经存在。这很好，因为来自新的城际安装的干净引导数据库已经创建了这些。该命令现在只导入数据。

检查导入是否成功:转到您的城际安装所在的 URL。您现在应该再次看到常规登录屏幕，而不是“创建您的第一个用户”屏幕。如果您看到登录屏幕:导入成功！

* * *

厉害！我希望这个程序对你有用。如果没有，或者如果你得到了错误:让我知道！