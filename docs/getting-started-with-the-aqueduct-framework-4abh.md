# Aqueduct 框架入门

> 原文：<https://dev.to/khophi/getting-started-with-the-aqueduct-framework-4abh>

你没时间了。我也没时间。让我们尽快解决这个问题。在本指南中，您将理解 Aqueduct 框架的一些方面。

*   开始一项输水管道工程
*   处理迁移并为迁移提供种子数据或装置
*   路由，即处理参数和查询参数
*   发出 CRUD 请求，由控制器处理
*   使用 PostgreSQL 作为后端
*   渡槽中的基本分页
*   编写基本测试。

> 这篇文章(以及我的许多其他文章)背后的原因是我在完成最基本的事情时遇到的挫折，以及其他教程/文章经历的不必要的冗长过程，漫长而浪费时间。完整的咆哮，见最后。
> 
> <cite>TL;DR</cite>

事不宜迟，让我们开始摇滚吧

## 设置项目

我不会纵容你的。以下是如何设置一个渡槽项目。你没有时间了，所以直接吃肉吧

*   [安装镖](https://webdev.dartlang.org/tools/sdk#install)
*   [安装 Postgres 数据库](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04)
*   [安装导水管](https://aqueduct.io/docs/getting_started/)
*   克隆[渡槽-CRUD](https://github.com/seanmavley/aqueduct-crud) 仓库

### 设置数据库

创建一个 postgreSQL 数据库，你想叫它什么都行。

```
 $ sudo -u postgres psql
  > CREATE USER projectuser WITH PASSWORD 'password';
  > CREATE DATABASE mydatabase;
  > GRANT ALL PRIVILEGES ON DATABASE mydatabase TO projectuser;
  > \q 
```

然后在根项目中创建一个`database.yaml`文件(与`pubspec.yaml`在同一个位置)，并放入以下内容:

```
username: 'projectuser'
password: 'password'
host: 'localhost'
port: 5432
databaseName: 'mydatabase'
```

上面的`database.yaml`用于`aqueduct db`命令连接到数据库。然而，为了让我们的应用程序与数据库对话，我们需要用我们的数据库信息更新`config.yaml`。

```
database:
  host: 'localhost'
  port: 5432
  username: 'projectuser'
  password: 'password'
  databaseName: 'mydatabase'
```

### 移民

> 如果你来自 Django 或 Laravel 这样的地方，移民对你来说可能是小菜一碟。
> 
> 否则，请将迁移视为一种将模式更改传达给数据库的方式。如果你没有得到，请谷歌更多关于迁移的细节
> 
> <cite>TL;DR</cite>

最后但同样重要的是，运行迁移，向数据库传达我们拥有和想要的数据库方案。您可以检查`migrations/00000001_initial.migration.dart`文件，看看会发生什么。

此外，种子/夹具数据是免费添加的。咄！

运行`aqueduct db upgrade`将迁移提交到数据库。

### 运行应用程序

在项目根文件夹中，运行`aqueduct serve`

因为这个项目只是 API 端点，所以您必须使用 REST 客户端，比如 [Postman](https://www.getpostman.com/) 。

如果使用 Postman，[查看这个 API 请求集合](https://www.getpostman.com/collections/b5c30941ce917a3a2ca9)来帮助您开始使用您的端点。你可能想看看如何使用邮差的收藏。

我们已经花了两秒钟了。时间太长了。让我们真正进入*真正的交易*

## 应用程序通道、入口点和路由

在`lib/channel.dart`中，我们或多或少会发现应用程序的自举，以及一些我们希望完成的操作，例如，通过加载配置文件与数据库保持联系，以及创建一个或两个路由。

没什么特别的事情发生。就像你从 Ruby、 [DJango](https://blog.khophi.co/category/django) 或 Laravel 那里知道的一样。见鬼，连[都棱角分明！](https://blog.khophi.co/category/javascript)

用这些内容更新您的`lib/channel.dart`文件

```
import 'controller/icd_controller.dart';
import 'dart:io';
import 'mpharma.dart';

class MpharmaChannel extends ApplicationChannel {
  ManagedContext context;

  @override
  Future prepare() async {
    logger.onRecord.listen(
        (rec) => print("$rec ${rec.error ?? ""} ${rec.stackTrace ?? ""}"));

                   // this Class is at the bottom
    final config = DatabaseConfig(options.configurationFilePath);

    final dataModel = ManagedDataModel.fromCurrentMirrorSystem();

    final persistentStore = PostgreSQLPersistentStore.fromConnectionInfo(
      config.database.username,
      config.database.password,
      config.database.host,
      config.database.port,
      config.database.databaseName,
    );

    context = ManagedContext(dataModel, persistentStore);
  }

  @override
  Controller get entryPoint {
    final router = Router();

    router.route("/example").linkFunction((request) async {
      return Response.ok({"key": "value"});
    });

    router.route('/icd/[:id]').link(() => ICDController(context));

    return router;
  }
}

class DatabaseConfig extends Configuration {
  DatabaseConfig(String path) : super.fromFile(File(path));

  DatabaseConfiguration database;

  @optionalConfiguration
  int identifier;
}
```

在英语中:

*   我们正在进口一些。`dart:io`允许我们读取文件(我们正在读取`config.yaml`文件。就当是魔法吧！
*   我们还加载了一个控制器文件。如果来自 Angular，就把它当成 Component.ts 文件
*   然后我们创建一些路线。这一部分，让我解释一下

`router.route('/icd/[:id]').link(() => ICDController(context));`

上面的单行代码包含了很多内容。这么多，光这一行我就能写一本书了。但是好吧...

上面的路线匹配类似这样的任何东西

```
example.com/icd GET
example.com/icd POST
example.com/icd/1 GET
example.com/icd/1 PUT
example.com/icd/1 DELETE

example.com/icd?limit=x&offset=x
```

`[:id]`是参数。它周围的`[ ]`使得这个参数是可选的。因此，在没有 ID 参数的情况下，它会做得很好。因此，如果`example.com/icd`进来，应用程序会做得很好。

当我们接下来切换到控制器时，您将看到这些 HTTP 动词是如何在控制器中处理的。但是，要知道我们使用了尽可能多的 HTTP 动词，至少是为了区分请求

`ICDController()`是从控制器导入的，控制器处理通过这个端点的路由。

要传递查询参数，只需在 URL 中传递，就像在其他框架中一样。如果在控制器中，您只需要处理查询参数。

所以你看，只有一行，所有的重拳？兴奋了吗？如果没有，控制器可能会磨你！

## 控制器先生

我们的~~组件~~控制器是我们处理逻辑的地方，并通过 Aqueduct 附带的 ORM 与数据库进行通信。上面，我们设置使用 PostgreSQL，这取决于你问谁，它是世界上最好的关系数据库平台。

阿布蒂，让我们结束这场战争吧！

像其他平台一样，拥有一个数据库模型意味着我们可以或多或少地控制哪些数据结构流经我们的应用程序并进入数据库。

在 Aqueduct 中，我们通过创建一个`model/icd_model.dart`来做到这一点

```
import 'package:mpharma/mpharma.dart';

class ICD extends ManagedObject<_ICD> implements _ICD {

  @override
  void willInsert() {
    createdAt = DateTime.now().toUtc();
  }
}

class _ICD {
  @primaryKey
  int id;

  @Column()
  String categoryCode;

  @Column()
  String diagnosisCode;

  @Column()
  String fullCode;

  @Column()
  String abbrDesc;

  @Column()
  String fullDesc;

  @Column()
  String categoryTitle;

  @Column()
  DateTime createdAt;

}
```

我唯一要评论的部分是

```
 @override
  void willInsert() {
    createdAt = DateTime.now().toUtc();
  }
```

在 mongose(express js，NodeJS，MongoDB)中，有一个`{ timestamp: true }`选项，当指定该选项时，任何通过模式进入数据库的数据都会获得一个`createdAt`和`updatedAt`时间戳。这是免费的，无缝的

在渡槽中，通过模型信号可以得到类似的结果。在上面的例子中，每当有一个插入将要发生时- `willInsert()`，创建一个`createdAt`字段，以 UTC 格式插入当前日期时间，然后作为一个字段添加到将要保存的数据结构中。

剩下的代码就是，嗯，代码！

我一直在你面前晃着控制器，却不给你看代码？我们开始吧

```
import 'package:aqueduct/aqueduct.dart';
import 'package:mpharma/mpharma.dart';
import 'package:mpharma/model/icd_model.dart';

class ICDController extends ResourceController {
  ICDController(this.context);

  final ManagedContext context;

  @Operation.get()
  Future<Response> getAll({@Bind.query('limit') int limit, @Bind.query('offset') int pageby}) async {

    limit ??= 100;
    pageby ??= 0;

    if (pageby != null) { pageby = pageby * 10; }

    final icdQuery = Query<ICD>(context);

    icdQuery
      ..pageBy((p) => p.createdAt, QuerySortOrder.descending)
      ..fetchLimit = limit
      ..offset = pageby;

    final icds = await icdQuery.fetch();

    return Response.ok(icds);
  }

  @Operation.get('id')
  Future<Response> getById(@Bind.path('id') int id) async {
    final query = Query<ICD>(context)..where((icd) => icd.id).equalTo(id);

    final icd = await query.fetchOne();

    if (icd == null) {
      return Response.notFound(body: 'Not found');
    }

    return Response.ok(icd);
  }

  @Operation.post()
  Future<Response> createICD(@Bind.body() ICD body) async {
    final query = Query<ICD>(context)..values = body;
    final insertICD = await query.insert();

    return Response.ok(insertICD);
  }

  @Operation.put('id')
  Future<Response> updateById(
      @Bind.path('id') int id, @Bind.body() ICD body) async {
    final query = Query<ICD>(context)
      ..where((icd) => icd.id).equalTo(id)
      ..values = body;

    final icd = await query.updateOne();

    if (icd == null) {
      return Response.notFound(body: 'Not found');
    }

    return Response.ok(icd);
  }

  @Operation.delete('id')
  Future<Response> deleteById(@Bind.path('id') int id) async {
    final query = Query<ICD>(context)..where((icd) => icd.id).equalTo(id);

    final icd = await query.delete();
    return Response.ok({'state': true, 'msg': 'Delete successfull'});
  }
} 
```

有很多东西要打包吗？没有。就几个

```
 @Operation.get()
  Future<Response> getAll({@Bind.query('limit') int limit, @Bind.query('offset') int pageby}) async {
    print(limit);
    limit ??= 100;
    pageby ??= 0;
```

`@Operation.get()`、`@Operation.post()`等，更像是在表达`router.get('/url', req, res)`

```
Future<Response> getAll({@Bind.query('limit') int limit, @Bind.query('offset') int pageby}) async { .. }
```

注意这个部分**第一个**，
`getAll({@Bind.query('limit') int limit, @Bind.query('offset') int pageby})`

做`getAll(@Bind.query...)`的时候和`getAll({@Bind.query ...})`的意思完全不一样

你注意到不同了吗？第二个在花括号里。第一个不是。

在花括号内意味着它是 **[可选参数](http://aqueduct.io/docs/http/resource_controller/#optional-bindings)** ，因此，在上面的花括号内，如果 URL 不提供任何查询参数，我们会做得很好。

```
 limit ??= 100;
    pageby ??= 0;

    // should this next line even be there? I wrote it
    // Can't tell for sure if it should be there. ¯\_(ツ)_/¯
    if (pageby != null) { pageby = pageby * 10; }

    final icdQuery = Query<ICD>(context);

    icdQuery
      ..pageBy((p) => p.createdAt, QuerySortOrder.descending)
      ..fetchLimit = limit
      ..offset = pageby;
```

`limit ??= 100;`基本上是说，如果`limit`查询参数为空，只需将 100 赋给该变量。`pageby`号也一样

其余的，只是用英语写的代码。如果控制器代码的任何部分不清楚，请留下评论。否则我会认为你们所有的读者(更像是我的学生)对我说“是的，先生，我们理解”。

## 结论

所以我们结束了。上述项目的源代码在此处:

[渡槽积垢 API](https://github.com/seanmavley/aqueduct-crud)

请在下面的评论区留下您的任何评论。

如果您对测试感兴趣，请查看我为这个项目编写的示例测试。