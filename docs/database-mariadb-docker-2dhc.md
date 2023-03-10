# 用 MariaDB Docker 制作简单的默认数据库

> 原文：<https://dev.to/mrchoke/database-mariadb-docker-2dhc>

#### [t1⾹对于已经编写了 app 的 Dev，有一个用于运行的默认数据库。当它使用第一个 docker 时，它会有点麻烦。](#%E0%B8%AA%E0%B8%B3%E0%B8%AB%E0%B8%A3%E0%B8%B1%E0%B8%9A-dev-%E0%B8%97%E0%B8%B5%E0%B9%88%E0%B8%97%E0%B8%B3%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B9%80%E0%B8%82%E0%B8%B5%E0%B8%A2%E0%B8%99-app-%E0%B9%81%E0%B8%A5%E0%B9%89%E0%B8%A7%E0%B8%A1%E0%B8%B5-database-%E0%B9%80%E0%B8%A3%E0%B8%B4%E0%B9%88%E0%B8%A1%E0%B8%95%E0%B9%89%E0%B8%99%E0%B8%AA%E0%B8%B3%E0%B8%AB%E0%B8%A3%E0%B8%B1%E0%B8%9A%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%97%E0%B8%B3%E0%B8%87%E0%B8%B2%E0%B8%99-%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B9%88%E0%B8%AD%E0%B8%A1%E0%B8%B2%E0%B9%83%E0%B8%8A%E0%B9%89-docker-%E0%B9%81%E0%B8%A3%E0%B8%81%E0%B9%86-%E0%B8%81%E0%B9%87%E0%B8%88%E0%B8%B0%E0%B8%A5%E0%B8%B3%E0%B8%9A%E0%B8%B2%E0%B8%81%E0%B8%AB%E0%B8%99%E0%B9%88%E0%B8%AD%E0%B8%A2%E0%B8%97%E0%B8%B5%E0%B9%88%E0%B8%95%E0%B9%89%E0%B8%AD%E0%B8%87%E0%B8%97%E0%B8%B3%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87-database-%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87-table-%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B9%88%E0%B8%AD%E0%B8%A1%E0%B8%B5%E0%B8%81%E0%B8%B2%E0%B8%A3-create-docker-container-%E0%B8%8B%E0%B8%B6%E0%B9%88%E0%B8%87%E0%B8%9B%E0%B8%81%E0%B8%95%E0%B8%B4%E0%B8%9C%E0%B8%A1%E0%B8%81%E0%B9%87%E0%B8%97%E0%B8%B3-manual-%E0%B8%95%E0%B8%A5%E0%B8%AD%E0%B8%94-%E0%B9%80%E0%B8%A5%E0%B8%A2%E0%B8%9A%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%B6%E0%B8%81%E0%B9%84%E0%B8%A7%E0%B9%89%E0%B8%AA%E0%B8%B1%E0%B8%81%E0%B8%AB%E0%B8%99%E0%B9%88%E0%B8%AD%E0%B8%A2-%E0%B8%AA%E0%B8%B3%E0%B8%AB%E0%B8%A3%E0%B8%B1%E0%B8%9A%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88%E0%B8%88%E0%B8%B0%E0%B9%84%E0%B8%94%E0%B9%89-dev-%E0%B8%81%E0%B8%B1%E0%B8%99%E0%B8%87%E0%B9%88%E0%B8%B2%E0%B8%A2%E0%B9%86)

让我们用 git pull 来研究一下

[mrchoke/mariadb _ docker _ example](https://github.com/mrchoke/mariadb_docker_example)

```
git pull [https://github.com/mrchoke/mariadb\_docker\_example](https://github.com/mrchoke/mariadb_docker_example) 
```

进入 mariadb_docker_example
中

```
cd mariadb\_docker\_example 
```

这里将包括

*   docker 合成. xml

```
version: '2'
services:
 mariadb:
 image: mariadb:10
 hostname: mariadb
 volumes:
 - ./mariadb:/var/lib/mysql
 - ./schema:/docker-entrypoint-initdb.d
 ports:
 - 3306:3306
 environment:
 - TZ=Asia/Bangkok
 - MYSQL\_ROOT\_PASSWORD=123456
 - "MYSQL\_ROOT\_HOST=%" 
```

我把两个卷装载进去，一个是 mariadb 的数据，另一个是用来启动数据库或表的架构，正如我们所设定的，以及

ports 部分，如果我们和另一个容器一起工作，它不需要绑定到外面，但是这个例子，我单独运行，可能会从外面调用它来测试我们。

“环境”部分，在这里，我们可以设置各种设置，如“密码”、“主机”等。可以从

[mariadb 坞站枢纽](https://hub.docker.com/_/mariadb/)

如果允许其他 docker 容器访问数据库，则需要设置
。

```
- "MYSQL\_ROOT\_HOST=%" 
```

或者它可以指定容器或 ip 容器的名称，但是如果 sale 更改或重新启动 ip，它必须小心，也可以更改它。

让我们看一下架构的部分。让我们开始。我做了两个示例:文件。

*   001 _ 测试. sql
*   002_mimetype.sql

我把它命名为一个序列，以便它按照文件名本身的顺序运行，但如果我们检查每个文件头上的文件数据库设计器，这是不必要的。

#### 001_test.sql

```
CREATE DATABASE IF NOT EXISTS `data` CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci;

USE `data`;

CREATE TABLE IF NOT EXISTS `test`
(
 `id` int PRIMARY KEY AUTO\_INCREMENT,
 `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4\_thai\_520\_w2,
 `owner` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4\_thai\_520\_w2,
 `created_at` TIMESTAMP DEFAULT CURRENT\_TIMESTAMP,
 `updated_at` TIMESTAMP ON UPDATE CURRENT\_TIMESTAMP,
 INDEX (name)

) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4\_unicode\_ci; 
```

第一个 file 示例，我检查是否有目标数据库。如果没有，请创建一个名为 data 的数据库。一旦创建完毕，就创建一个名为 test doo 的表。

#### 002_mimetype.sql

```
USE `data`;

CREATE TABLE IF NOT EXISTS `mimetypes`
(
 `id` int PRIMARY KEY AUTO\_INCREMENT,
 `label` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci,
 `mime_type` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci,
 `file_extension` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci,
 INDEX (label)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4\_unicode\_ci;

insert into mimetypes (label,mime\_type,file\_extension) values ( 'Unknown' , '\*/\*' , '' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'AutoCAD drawing files' , 'application/acad' , 'dwg' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Andrew data stream' , 'application/andrew-inset' , 'ez' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'ClarisCAD files' , 'application/clariscad' , 'ccad' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Text - Comma separated value', 'text/csv' , 'csv' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'MATRA Prelude drafting' , 'application/drafting' , 'drw' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'DXF (AutoCAD)' , 'application/dxf' , 'dxf' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Filemaker Pro' , 'application/filemaker' , 'fm' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Macromedia Futuresplash' , 'application/futuresplash' , 'spl' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'NCSA HDF data format' , 'application/hdf' , 'hdf' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Image - IGES graphics format' , 'application/iges' , 'iges' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Mac binhex 4.0' , 'application/mac-binhex40' , 'hqx' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Mac Compactpro' , 'application/mac-compactpro' , 'cpt' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Microsoft Word' , 'application/msword' , 'doc' );
insert into mimetypes (label,mime\_type,file\_extension) values ( 'Uninterpreted binary' , 'application/octet-stream' , 'bin' );
-- Open Documents MIME types
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.text', 'odt', 'OpenDocument Text');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.text-template', 'ott', 'OpenDocument Text Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.text-web', 'oth', 'HTML Document Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.text-master', 'odm', 'OpenDocument Master Document');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.graphics', 'odg', 'OpenDocument Drawing');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.graphics-template', 'otg', 'OpenDocument Drawing Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.presentation', 'odp', 'OpenDocument Presentation');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.presentation-template', 'otp', 'OpenDocument Presentation Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.spreadsheet', 'ods', 'OpenDocument Spreadsheet');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.spreadsheet-template', 'ots', 'OpenDocument Spreadsheet Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.chart', 'odc', 'OpenDocument Chart');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.formula', 'odf', 'OpenDocument Formula');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.database', 'odb', 'OpenDocument Database');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.oasis.opendocument.image', 'odi', 'OpenDocument Image');

-- Open XML formats for MS-Office
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.spreadsheetml.sheet', 'xlsx', 'Microsoft Office Excel');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.spreadsheetml-template', 'xltx', 'Microsoft Office Excel Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.presentationml.presentation', 'pptx', 'Microsoft Office PowerPoint Presentation');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.presentationml.slideshow', 'ppsx', 'Microsoft Office PowerPoint Slideshow');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.presentationml-template', 'potx', 'Microsoft Office PowerPoint Template');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.wordprocessingml.document', 'docx', 'Microsoft Office Word');
insert into mimetypes (mime\_type, file\_extension, label) values ('application/vnd.openxmlformats-officedocument.wordprocessingml-template', 'dotx', 'Microsoft Office Word Template'); 
```

第二个架构创建了一个表，然后输入了一些初步信息，注意到在 file 头，我没有检查数据库的创建，所以需要命名 00。

一旦我们成功设计了模式，它就可以用
命令开始完全运行。

```
docker-compose up -d 
```

如果谁还没有安装 docker-compose，请安装它。根据这个链接。

安装坞站复合

使用
命令查看运行是否成功。

```
docker-compose logs -f 
```

如果成功的话，它会有最后一行，大约是这个
230㎡。

```
mariadb\_1 | 2019-03-13 14:40:43 0 [Note] mysqld: ready for connections.
mariadb\_1 | Version: '10.3.13-MariaDB-1:10.3.13+maria~bionic' socket: '/var/run/mysqld/mysqld.sock' port: 3306 mariadb.org binary distribution 
```

就让 Ctrl + C 出来。

#### 测试数据库

让我们测试一下我们创建的数据库是否真的存在，简单的测试，而不需要使用命令(docker-compose 命令时间)进入 container。yml)
t

```
docker-compose exec mariadb mysqlshow -p data 
```

*   docker-compose →命令
*   exec →与 docker 一样 exec 是在容器内命令执行执行执行或执行命令
*   mariadb→docker-compose 中声明的服务名称。yml
*   mysqlshow →是容器内的命令
*   -p →是 mysqlshow 命令的一个选项，是说它必须包含 password (p → password)。
*   数据是我们提供的数据库名。

如果一切正常的话就能得到大致的结果

[t1⾹T2⾹T3⾹显示 T4Ӂ数据库中的表。](https://res.cloudinary.com/practicaldev/image/fetch/s--d7UbrEHd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/552/1%2AQUAUZ9enzdjfGa6E5wTwMw.png)

密码是 123456，正如在 docker-compose 中设置的。是 yml

或者，如果检查了 mimetype 和 test 的结构，它可以如下
命令。

```
docker-compose exec mariadb mysqlshow -p data mimetype 
```

[![](img/7d27e3071f706c5af140ec2c24ea8716.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x8DEuceZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6lpzrFArihiMBIn94R3Ugw.png)T3】

```
docker-compose exec mariadb mysqlshow -p data test 
```

[![](img/3339f11bfdf03673122a7f94a7f1df86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ncck_VDB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArvJD2w7IxmaeT-JhwemGQA.png)

或者，如果要访问 mysql 语句，它可以简单地按照
的方式进行排序。

```
docker-compose exec mariadb mysql -p 
```

[![](img/1869db18317a3bee071cd1cc63166ac4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--INSK9iK8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/713/1%2A_9kGUg2fy8gc9YJ92kg_nA.png)

[![](img/85ef72971fa3bd26b5de70056a8e6062.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5C6LxZL9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/803/1%2Az7ngGjBnHU0qjP6UAm9K4A.png)

<figcaption>MySQL</figcaption>

指令的实现

如果要通过本地主机与其他 app 进行联系，则可以通过
直接连接。

```
localhost:3306 
```

或者，我们将创建更多的服务，比如 phpmyadmin，它可以通过在 docker-compose 中添加来实现。yml 连续时间也可以使用服务名称，例如:

**码头-化合物. yml**

```
version: '2'
services:
**mariadb** :
 image: mariadb:10
 hostname: mariadb
 volumes:
 - ./mariadb:/var/lib/mysql
 - ./schema:/docker-entrypoint-initdb.d
 environment:
 - TZ=Asia/Bangkok
 - MYSQL\_ROOT\_PASSWORD=123456
 - "MYSQL\_ROOT\_HOST=%"

**phpmyadmin** :
 image: phpmyadmin/phpmyadmin:latest
 hostname: phpmyadmin
 ports:
 **- 9999:80**
 environment:
 - PMA\_HOST= **mariadb** 
```

在示例中，我添加了一个新的服务，即 phpmyadmin，注意到我可以完全删除 mariadb 服务端口，因为我们将让服务进行讨论。

直接环境。我从说数据库主机的名称是什么？就这样，它会自动找到的。而我们可以设定什么？可以在这里阅读。

坞站枢纽

一旦我们解决了，就可以订购一个新的 docker-compose
。

```
docker-compose up -d 
```

这一次让我们通过网络浏览器，通过调用

。

```
http://localhost:9999 
```

[![](img/710b3011f7032ffd251ea2d3ddec04c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d2dtI7Vf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoNPm6CW3cNLF3PbZ1BXJoA.png) 

<figcaption>phpmyadmin</figcaption>

把 user root 和 password 123456 放在我们最初设定的位置

[![](img/d923a6d309afb80e92f1796ff48f603b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L8GIfMFA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXYM8Rxsti4yzPmRsbGDdmQ.png) 

<figcaption>【数据库管理】</figcaption>

。

如果真的是新手，也许会有些困惑，慢慢地去追逐。如果你懂了，它可以给工作带来更大的便利。

当我们不再使用时，请使用
命令关闭容器。

```
docker-compose down 
```

如果要删除我们装载的 mysql，请删除 mariadb 目录。

* * *