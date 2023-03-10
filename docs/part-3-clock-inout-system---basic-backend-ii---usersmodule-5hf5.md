# 第三部分。上下班打卡系统-基本后端(II) -用户模块

> 原文：<https://dev.to/carlillo/part-3-clock-inout-system---basic-backend-ii---usersmodule-5hf5>

这篇文章是我正在描述的一个上下班打卡系统的系列文章的一部分
如果你想了解更多，你可以阅读下面的文章:

*   第一部分。上下班打卡系统:图表。
*   第二部分。上下班打卡系统:基本后端— AuthModule。
*   第三部分。上下班打卡系统:基本后端—用户模块。
*   第四部分。上下班打卡系统:基本后端应用模块。
*   第五部分。打卡系统:种子数据库和迁移数据
*   第六部分。上下班打卡系统:基本前端。
*   第七部分。打卡/下班系统:使用 docker/docker-compose 部署后端(nestJS)。
*   第八部分。上下班打卡系统:使用环境部署前端(角度 2+)。
*   第九部分。测试:后端测试—单元测试—服务
*   第十部分。测试:后端测试—单元测试—控制器
*   第十一部分。测试:后端测试— E2E 测试
*   第十二部分。测试:前端测试—单元测试
*   第十三部分。测试:前端测试—集成测试

在之前的帖子中，我介绍了基本的后端结构和第一个
模块(AuthModule)。所以，我建议你在这个
之前看完那个帖子，因为你可以了解整个系统。

* * *

原载于[www . carloscaballero . io](https://carloscaballero.io/part-3-clock-in-out-system-basic-backend-ii-usersmodule/)2018 年 12 月 3 日。

* * *

这个帖子将被赋予**用户模块**，用于管理用户
的信息。服务**用户服务**提供了两个重要的方法:

1.  **getUsersWithoutKey**
2.  **addUser**

这些方法用于知道用户没有有效的 id 卡，并在系统中添加一个
用户。

因此，第一步是显示图 1 中所示的
的**用户模块**的文件结构。

[![](img/73c7b968d394238c46452e582b790d29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qcdc4qDJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2A6mEVlUiORhxVl1JX.png)

在这个模块的开发中，我们使用了与 AuthModule 中的
相同的目录结构，但是我们有了一个名为**控制器**的新目录，该目录
用于使用 **APIRestful** 与外部通信这个模块。
此外，您可以看到两个实体，因为在这个模块中我们需要两个表
( **User** 和 **UserSchedule** )。

因此，模块的文件如下面的代码所示:

[![](img/a63f883483df6064973ce5c2bc6b63d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--69Y4II9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AzCFemzoBahUCEL_BGCLtzA.png)

这个模块只导入 **DatabaseModule** 来使用
类型的表单连接我们的 Postgres，并导出 **AppController** 中使用的 **UserService** 。在
中，该模块被定义为控制器**用户控制器**，其将用于
将该模块与外部通信。

### 实体

在这个模块中，我们需要使用两个实体:

*   **用户**:该实体定义用户信息。
*   **调度器**:该实体定义用户的调度器(弱实体)。

因此，定义实体的第一步是定义允许
通过注入在我们的服务中使用**用户存储库**的提供者。

[![](img/8467446fb2152c116811a14aa42408fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rd9MGG8l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2All4enyWIZTnq8wBrZyr-_g.png)

因此，第一个实体是由以下字段定义的用户:

*   **uid** :用户的 uid，在本例中是由用户的“姓，名”组成的字符串。
*   **姓名**:用户的姓名，该字段用于在屏幕上显示姓名。
*   **auth** :表 **Auth** 和 **Users** 的关系。此字段是用户的身份验证列表。
*   **键**:分配给任何用户的键。
*   时间表:这是最重要的字段之一，因为它是用户和他们的时间表之间的关系。用户模块的第二个实体是。

[![](img/0f1e31ef7cc8daaed5e0abf1bcdae7fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B0sbsdDJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Ark22TDjJwedZP9nS3yo1zA.png)

**UserSchedule** 实体用于反映在
的每个会话中哪个用户必须在大楼中。存储在该表
中的字段如下:

*   **UID** :用户日程的 UID。该字段由数据库自动生成。
*   **日**:一周中用户必须在楼内的那一天(*从 0 到 6* 相当于*从周日到周六*)。
*   **小时**:一天中用户必须在建筑物中的时间(从 0 到 11 的*相当于从 8:15 到 22.10 的*，但是这种关系不是线性的，但是有一个函数完成这个任务)。
*   **房间**:用户在那一小时必须待的空间。
*   **用户**:表格**用户日程**和**用户**的关系。许多**用户计划**都与一个**用户**相关。

[![](img/1469c3c63d247cbfc4d79cbadd04cc26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--219rD1jW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AqB4EJTV0QBcFWozhX6VYkw.png)

最后，系统由 3 个表组成:

*   **用户**:系统中用户及其密钥的信息。
*   **用户日程**:用户必须在的日程和房间信息。
*   **Auth** :上下班打卡信息(包括时间戳)

### 常量和 dto

下一部分非常简单，就像上一篇文章一样。在本节中，我们
定义常数和 dto 以获得更好的代码。常量用于清理字符串或数字的代码，而 d to 用于从客户端验证用户
。

在文件 **user.constants.ts** 中你可以看到几个数组:

*   **SCHEDULE_EXCLUDE** :将被排除在列表之外的调度程序列表(用户必须在大楼内)。
*   **SCHEDULE_HOURS** :用户会话开始和结束的不同时间
*   几个常数导出格式的时刻或第一个和最后一个小时在不同的工作班次。

[![](img/c73f48e7f73d201eebba80eedd7f046b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U3EuD79Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AVJY_oPdR-LgMjfkxknf8pA.png)

**user.dto** 文件也非常简单。在这个文件中，你可以看到一个类的
定义，其中定义了两个字段(UID 和 name)。

[![](img/d636d10eaa99fbf51884d5635b8aec16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gj-YPJF3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A7f85UM9L_47H_jXTmbvKnQ.png)

### 控制器

现在是介绍用户控制器的时候了。在这个文件中，您可以看到
控制器被称为**用户**，并且使用了两个动词:

*   **GET /user:** 该方法从服务中调用 getUsersWithoutKey 方法，获取系统中所有非 Key 的用户(用于填充客户端的信息)。
*   **POST /user:** 这个方法从服务中调用 addUser 方法来为用户添加密钥。事实上，文章的主体应该是一个 uid 和 key。

[![](img/49d188ee181c1771207a06583d1a5baf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2v4DiIoi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Auwf2GksEsd3WE-BAl_2ziQ.png)

### 服务

最后，这个模块中最重要的是服务，因为模块
的逻辑在这个文件中。所以， **UserService** 有三个重要的方法:

*   **getUsersWithoutKey** :该方法返回值是来自*类型表单*的 UserEntity[] 的*承诺。因此，该方法的目标是使用关键字值为空的所有用户中的 *ORM* 调用正确的 *SELECT* 语句。*
*   **addUser** :在这个方法中返回值是一个*承诺*，它是从 TypeORM 的方法 save 返回的。因此， **addUser** 是 *TypeORM* 的包装器，而后者是 *INSERT/UPDATE* 语句的包装器。
*   **getusersworkingow**:该方法不在 **UsersModule** 内部使用，而是在 **AppController** 中使用。这个方法返回一个用户身份[]的*承诺，它是由当前必须在建筑物中的所有用户组成的。这个方法使用了库 *MomentJS* 。这个方法可以在有很多代码味道的*坏代码*中完成，但是我更喜欢使用几个变量来清理代码。此外，我使用了一个私有函数 **isMorning** ，它允许我知道系统是否在上午或下午。这是因为有几个用户必须在早上几个小时和下午几个小时呆在大楼里。返回的**用户**包含当天的认证列表(使用下限和上限)。*

[![](img/e55437a0d9d34ef133279dd9cc7f719d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9mn6MXNr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Acy0JZUobEkgpYkTomevACA.png)

### 简历

‌In 在这篇文章中，我解释了我的*用户模块*，它非常简单，因为我在编码中使用了
干净的代码。该模块用于保存
用户和打卡信息。方法 *getUsersMustBeWorkingNow* 是系统的主要方法
，因为该方法使用几个约束返回必须在
建筑物中的用户列表。这种约束很容易定制。

在本系列的后续文章中，我将解释 *AppModule* ，它
在客户端和服务器端之间进行通信，以及它们之间的
服务器端的模块。

GitHub 项目是
[https://github.com/Caballerog/clock-in-out](https://github.com/Caballerog/clock-in-out)。

这个帖子的 GitHub 分支是
[https://GitHub . com/Caballerog/clock-in-out/tree/part 3-basic-back end-users](https://github.com/Caballerog/clock-in-out/tree/part3-basic-backend-users)。

* * *

原载于[www . carloscaballero . io](https://carloscaballero.io/part-3-clock-in-out-system-basic-backend-ii-usersmodule/)2018 年 12 月 3 日。