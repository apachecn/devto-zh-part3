# 使用 Nodejs 和 Minio 将数据加载到 Postgresql

> 原文：<https://dev.to/dviejopomata/loading-data-into-postgresql-using-nodejs-and-minio-1l4c>

我最近向 PostgreSQL 加载了很多文件，我需要在没有很多资源的情况下快速完成。

我的要求如下:

1.  占用空间小，不会一次读取文件的所有行
2.  易于加载数据
3.  使用“复制到”语句有效地将数据写入 Postgres

有了这些需求，我想到了在 NodeJS 中处理流的想法，因为:

1.  很有效率
2.  很简单

假设我有下面的文件

```
first_name, last_name, country
Karie,Zorn,Ukraine
Thebault,Flicker,France
Danya,Atcock,Canada
Lorne,Westmorland,Russia
Page,Greeve,Canada
Rene,Riccardini,Serbia
Adair,Mullin,Philippines
Della,Gumb,China
Charlie,Swadlinge,Cambodia
Celka,Karlowicz,Canada 
```

以及 PostgreSQL 中的下表:

```
CREATE EXTENSION "uuid-ossp";   
CREATE TABLE PERSON(
    id uuid default uuid_generate_v4(),
    first_name varchar(30) NOT NULL,
    last_name varchar(30) NOT NULL,
    country varchar(30) NOT NULL
); 
```

因为我们将在 PostgreSQL 语句中使用 curl 来获取文件，所以我们将使用以下内容扩展 Postgresql Docker 图像:

```
FROM postgres:10.4
RUN apt-get update && apt-get install -y curl 
```

我将这个文件命名为 postgresql。Dockerfile，当你有了这个文件后，我们将使用下面的命令让它运行起来:

```
docker build -f postgresql.Dockerfile -t postgres-with-curl .
docker run --restart always --name postgres --network host -d postgres-with-curl
docker run --network host -e MINIO_ACCESS_KEY=user -e MINIO_SECRET_KEY=password --restart  always --name minio -d minio/minio:RELEASE.2018-06-22T23-48-46Z server /data 
```

让我们开始吧，首先我们需要为 PostgreSQL 和 Minio 创建连接:

```
const pg = require("pg")
const minio = require("minio")

const minioClient = new minio.Client({
    accessKey: "user",
    secretKey: "password",
    secure: false,
    endPoint: "localhost",
    port: 9000,
    region: "us-east-1",
})
const pgPool = new pg.Pool({
    user: "postgres",
    password: "postgres",
    host: "localhost",
    port: 5432,
    database: "postgres",
}) 
```

然后我们要做三件事:

1.  阅读文件
2.  上传到迷你
3.  在 PostgreSQL 实例中执行 SQL

```
const fs = require("fs")
const path = require("path")

const TABLE_NAME = "public.people"

async function main() {
    const input = fs.createReadStream(path.join(__dirname, "people.csv"), {
        encoding: "utf-8",
    })
    const bucketName = "tmp"
    const objectName = "new.csv"
    await minioClient.putObject(bucketName, objectName, input)
    const csvUrl = await minioClient.presignedGetObject(
        bucketName,
        objectName,
        10000, // duration in seconds of the url
    )
    const copyDataSql = `
  COPY ${TABLE_NAME} (FIRST_NAME, LAST_NAME, COUNTRY)
    FROM PROGRAM 'curl "${csvUrl}"'
    WITH (FORMAT CSV , HEADER, QUOTE '"', DELIMITER ',' );`
    const client = await pgPool.connect()
    await client.query(copyDataSql)
    await client.release()
    await pgPool.end()
}

main() 
```

如果我们在 nodejs 中执行下面的代码，我们应该会看到数据库中包含以下查询的行:

```
SELECT * FROM PUBLIC.PEOPLE; 
```

该脚本可能由于以下原因而失败:

*   迷你没起来
*   Postgresql 未启动
*   尚未创建 Postgresql 表
*   Minio 存储桶“tmp”尚未创建

当我们无法控制 postgres 中的磁盘，并且必须执行批处理操作时，这种技术会很有用。

感谢阅读。