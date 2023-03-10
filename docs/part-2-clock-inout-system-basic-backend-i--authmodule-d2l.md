# 第二部分。上班打卡/下班打卡系统:基本后端(I) —身份验证模块

> 原文：<https://dev.to/carlillo/part-2-clock-inout-system-basic-backend-i--authmodule-d2l>

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

在第一篇帖子中( [Part 1。上下班打卡系统:图表](https://carloscaballero.io/part-1-clock-in-out-system-diagram))我们描述了上下班打卡系统的不同组件。所以，如果你不
理解系统的架构，你可以读一下那个帖子，因为我已经
描述了系统的每个组件。

* * *

原载于[www . carloscaballero . io](https://carloscaballero.io/part-2-clock-in-out-system-basic-backend/)2018 年 11 月 25 日。

* * *

在这篇文章中，我将描述使用 NestJS 的基本后端。第一步
是了解目录结构。如果您使用 NestJS
starter 项目来启动一个项目，您可以执行以下步骤:

之后，我们需要在 **src** 目录下创建我们自己的模块结构，如图 1 中的
所示。

[![](img/a0daaaa00dd315c53c10dc92461eb98f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--obz7eKes--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AGqK5UyoKrtcTMre4.png)

此时，服务器正在端口`3000`运行，使用动词`GET`的端点
T1 正在工作。如果您在这个地址的
上导航，您会得到一条如图 2 所示的`Hello World`消息。

[![](img/07da169e2e9a2c9d0d3f2910afb2d249.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P750i8jf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2Ak2aQ_wzJv4RLrcoP.png)

### 后台的模块列表

下一步是定义我们的后端需要哪些模块:

*   **DatabaseModule:** 该模块用于共享不同模块之间的数据库连接。
*   **AuthModule:** 该模块用于当用户打卡下班时在数据库中注册。服务 **AuthService** 将负责在用户入住和退房时保存在数据库中。由于外部原因，此模块不可访问。因此，不存在使用 API 进行通信的控制器。
*   **用户模块:**该模块用于管理用户信息。服务 **UserService** 提供了两个重要的方法:**1。getUsersWithoutKey
*   addUser**。这些方法用于知道用户没有有效 id 卡，并将用户添加到系统中。
*   **AppModule:** 该模块是主模块，将与前端进行通信。在这个模块中，其他模块将被导入，所以这些模块的服务可以在这个模块中使用。该模块中的主控制器具有以下端点:
*   **POST: /in:** 该端点从 **AuthService** 中调用方法 **authIn** 。
*   **POST: /out:** 该端点从 **AuthService** 中调用方法 **authOut** 。
*   **GET: /users:** 这个端点调用来自 **UsersService** 的方法 **getUsersMustBeWorkingNow** 并将信息组合在一起**服务器的时间戳**。

### 数据库模块

模块数据库用于共享数据库连接。我们使用
TypeORM 作为 ORM，它完美地集成在 NestJS 中。所以我们的
database.module 的定义如下:

[![](img/caf8ecba3aff08593890aeb32a6c4bf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E_T1gS_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ANl8aQN58SwaIxHQ3IQJ_-g.png)

在提供者中，我们使用工厂来定义我们的`DbConnectionToken`。在我们的
案例中，我们使用`Postgres`作为数据库。因此，最有趣的部分是在`TypeORM`中自动检测的实体的
定义。虽然
nestj 包含了一个指定的模块来管理`TypeORM`连接，但是我更喜欢
直接使用 typeorm
库中的方法 createConnection 来配置提供者。

此时，我们必须在节点中安装以下库:

`npm i typeorm pg`

[![](img/e52dcd7b05202b46e0fc522530a2b203.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--koPJlJ5m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A-nncmGY2WJ3sPeXaXrjGJw.png)

所以，下一步是在你的机器上安装一个数据库 Postgres，但是我认为使用 Docker 是更好的选择，因为你可以得到一个干净的环境
来开发，它将与你部署时一样。

所以，docker-compose 中你将一个名为
**的容器 PostgreTestClock** 使用端口 5531 如下:

[![](img/68e93fb2986d791f232a23e9d700f29b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---DQH0JK6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AjVFM0iV6dwkcSOcIZX2Ksg.png)

所以，要启动系统的数据库只需运行命令`docker-compose
up`。其他模块将使用此模块连接到数据库。

### 认证模块

我们实现的第二个模块是 AuthModule，该模块具有图 3 所示的
结构。你可以注意到有几个目录
作为常量、d to 和实体，它们被用来独立地组织一个模块
作为服务。这个想法是构建模块即服务，因为任何一天
你都可以构建微服务。然而，在这个项目中，所有模块之间都是
关系，因为这个项目的架构非常复杂。

[![](img/43fbed0ac9c6c203ca8facdad18b42dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AFMdeVBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2Axry_GvhDz7kzZKtg.png)

在 **auth.module** 的定义中可以看到导入了 **DatabaseModule**
和 **UsersModule** 。DatabaseModule 用于连接数据库(如前面的
所示), UsersModule 用于检查
auth module 中的用户信息。此时，模块 UsersModule 仍在
开发中(见后文)。服务 AuthService 被导出，因为
未来的 AppController 将使用该服务。

[![](img/466107f666be6286173caa2106f24c8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WjosGAr7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AqjZy1roqvEdEfhrMZh4r6Q.png)

### 实体

下一步是知道信息保存在数据库中。这是使用文件 user.entity 定义的
。

[![](img/0a87b04e9c5b9a12b26f82c05bbb7e6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6idj0xVv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AAAX9R7SUV8OmNyFATyozRg.png)

类真实性的字段如下:

*   id_key :那是 UID 的表。该字段由 TypeORM 自动生成。
*   阅读器:这是发送用户密钥的设备。在这个项目中有两个阅读器——输入和输出。
*   **用户**:这是 Auth 和 user 的关系。在 TypeORM 中，可以使用 decorator @ManyToOne 来定义每个用户的身份验证中有几个条目。使用用户的字段键建立关系。在用户实体中，需要用反向装饰器(@OneToMany)定义字段键。在下面的帖子中，我们将描述这个模块(目前是评论)。
*   **时间戳**:这是 unix 时间戳中输入或输出完成的时刻。因此，这非常重要，因为我们需要知道用户在系统中打卡/下班的准确时间。

最后，提供者文件导出两个存储库来快速访问
数据库。使用的模式是存储库。因此，您可以在任何
服务或控制器中使用这些提供者的令牌(AUTH_REPOSITORY_TOKEN 和 USER_REPOSITORY_TOKEN)来注入这些提供者
。

[![](img/bf00efd3023d34b6256912838638e1b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_QhgUpB7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Ay1eQp2vAtQXDIqKBYAF6mA.png)

### 常量和 dto

现在，我将展示我的代码中最简单但非常重要的部分。
常量文件和 DTO。当您想要
编写干净的代码(最好不要在代码中使用字符串或数字)
并且 dto 定义客户端和
服务器端之间交换的信息时，常量文件非常有用。‌

auth.constant 文件只有对 Arduino 系统
的 STATUS_CODE_RESPONSE，该系统将 0 理解为 KO，将 2 理解为 OK，并有几种 moment 格式。

[![](img/11c3006bc8bb71fa3a8a97fe59bb3896.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JfS6Mkjn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ACppqShOBJPuZe3FUkykO-w.png)

下面的 Dto 非常简单，因为只显示了一个输入
( **AuthDto** )和一个输出( **AuthResponseDto** )的接口。

[![](img/b81f392be9425b8a0954b75ab1412ff6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XpAglUvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A3GyY0PREofo-ZIvs_8i6lA.png)

### 服务

AuthService 应该有两个重要的方法:

*   **authIn** :当用户**在系统中打卡**时，该方法接收来自 Arduino 的信号并保存信息。服务响应一条欢迎消息。
*   **authOut** :该方法在系统中用户**打卡**时从 Arduino 接收信号并保存信息。服务响应 bye 消息。

这种方法的代码如下:

[![](img/e1548eeeeb97fc64c59f5f005330ff9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g7B3pRqt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AFMRFH5Hm3YcC9Vre6nFgPA.png)

在这个类中，我注入了两个存储库(authRepository 和 userRepository)
,用于与数据库通信。代码是干净的，因为我使用了
简单的异步/等待和私有方法( **saveTicketing、welcomeTeacher** 和
T5】bye teacher)。所以，方法 **authIn** 和 **authOut** 对于将来的
测试来说很简单。两种方法的响应都是一个
**AuthResponseDto** 。

私有方法 **saveTicketing** 也很简单，第一步是获取
与密钥相关联的用户(auth.key 来自 Arduino)。
之后，您可以保存认证信息:

*   来自 auth 的**键**和**阅读器**。
*   **数据库中的用户**(user entity)。
*   **时间戳**，从库时刻开始计算。

[![](img/6480cbf34e493af128a58a3380ee9679.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N98WQXKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A6o3vC5cGHe8sFnJcUvFt8g.png)

最后，向用户显示消息的两种方法(显示在
Arduino 屏幕上)如下:

[![](img/fa0241ab8334d88911c8d52fa827a927.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fSW2NtHZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AgcKTE_XescVuTx0A9c7gJA.png)

最后，如果你想看 AuthService 的完整代码如下:

[![](img/6b52603f18b5f0173b004739d054db05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_EBHmjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A9I7Y-LPm0fMLqBDNPo7kdw.png)

### 简历

‌In 在这篇文章中，我解释了我的 AuthModule，它非常简单，因为我在编码中使用了
干净的代码。该模块用于保存
打卡/下班的信息。

在本系列的后续文章中，我将解释 UsersModule 和
AppModule。

GitHub 项目是
[https://github.com/Caballerog/clock-in-out](https://github.com/Caballerog/clock-in-out)。

这个帖子的 GitHub 分支是
[https://GitHub . com/Caballerog/clock-in-out/tree/part 2-basic-back end-auth](https://github.com/Caballerog/clock-in-out/tree/part2-basic-backend-auth)。

* * *

*原载于[www . carloscaballero . io](https://carloscaballero.io/part-2-clock-in-out-system-basic-backend/)2018 年 11 月 25 日。