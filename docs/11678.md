# 将 PostgreSQL AWS RDS 移至 Google Cloud SQL

> 原文：<https://dev.to/mfahlandt/move-postgresql-to-google-cloud-sql-5bma>

我们面临的问题是，我们必须将一个大型 postgreSQL 数据库从亚马逊的 AWS 转移到谷歌的 GCP。

## 问题出在哪里:

大型数据库:160GB+我们只有 AWS 的快照

### 将 RDS 中的快照存入存储器

为此，我们创建了一个新的计算引擎，并通过 ssh 连接到它。我们希望将转储文件直接放入存储中的新存储桶。所以我们必须启用桶作为机器的新卷:

```
gcloud init 
```

您可以登录或使用服务帐户，但是请记住，服务帐户需要创建存储桶的权限。

```
gsutil mb gs://my-new-bucket/ 
```

现在我们必须将铲斗安装到机器上。对于使用云存储保险丝的 wie，要安装它，我们需要以下步骤:

```
export GCSFUSE_REPO=gcsfuse-lsb_release -c -s
echo "deb http://packages.cloud.google.com/apt $GCSFUSE_REPO main" | sudo tee /etc/apt/sources.list.d/gcsfuse.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-get update
sudo apt-get install gcsfuse 
```

现在我们终于可以安装它了

```
gcsfuse db /mnt/gcs-bucket 
```

我们有地方存放垃圾了，接下来呢？我们必须在机器上安装与远程服务器相同的 PostgresQL 版本，以获得有效的 pg_dump

```
echo "deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main 9.5" | sudo tee /etc/apt/sources.list.d/postgresql.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
apt-get install postgresql-9.5
sudo apt-get install postgresql-9.5 
```

现在我们终于可以转储了:

```
pg_dump -h yourRDS.rds.amazonaws.com -p 5432 -F c -O -U postgres DATABASE > /mnt/gcs-bucket/db.dump 
```

根据您的数据库有多大，这将需要一段时间。接下来，在 GCP 上创建您的 SQL 实例。有一个针对 SQL 文件的导入功能，但遗憾的是没有针对转储的导入功能，所以我们不得不艰难地进行恢复。

```
pg_restore -h YourNewSQLInstanceIP -n public -U postgres-user -d DATABASE -1 /mnt/gcs-bucket/db.dump 
```

这甚至需要更长时间，请确保将计算引擎的 IP 列入白名单，以便它可以访问 SQL 实例。

## 我照你说的做了，但是我收到奇怪的错误

类似这样的？

```
pg_restore: [archiver (db)] Error while PROCESSING TOC:
pg_restore: [archiver (db)] Error from TOC entry 4198; 0 0 ACL children_of(integer) 
postgrespg_restore: [archiver (db)] could not execute query: ERROR: role "user" does not exist Command was: REVOKE ALL ON FUNCTION children_of(root_id integer) FROM PUBLIC; 
```

很容易回答，您的新数据库中缺少转储中引用的用户。

### 如何避免这种情况？

简单回答，创造用户。遗憾的是，您不能导出它们，因为一些规则 RDS 使得无法执行 pg_dumpall -g(仅限用户和角色)

```
pg_dumpall -h yourRDS.cd8cncmdv7f0.eu-central-1.rds.amazonaws.com -g  -p 5432  -U postgres > /mnt/gcs-bucket/db_roles.dump 
```

这不起作用，您将收到错误

```
pg_dumpall: query failed: ERROR:  permission denied for relation pg_authid
pg_dumpall: query was: SELECT oid, rolname, rolsuper, rolinherit, rolcreaterole, rolcreatedb, rolcanlogin, rolconnlimit, rolpassword, rolvaliduntil, rolreplication, rolbypassrls, pg_catalog.shobj_description(oid, 'pg_authid') as rolcomment, rolname = current_user AS is_current_user FROM pg_authid ORDER BY 2 
```

因为 AWS RDS 不以超级用户身份执行查询，所以您不能导出它。但是，如果您手动创建它们，它会工作得很好

下次见