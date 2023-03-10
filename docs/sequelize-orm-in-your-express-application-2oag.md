# 如何在你的 Express 应用程序中使用 Sequelize ORM

> 原文：<https://dev.to/teodeleanu/sequelize-orm-in-your-express-application-2oag>

添加和 ORM 或选择一个绝对不是一件容易的事情。但是它给你带来的编码速度是至关重要的。
我已经在这里快速检查了 JS [可用的一个 ORM。对于那些喜欢基于 SQL 的关系数据库的人来说，你可以使用 Sequelize。
这是我们选择它作为这个例子的主要原因。](https://www.slant.co/topics/11235/~javascript-orms)

# 利弊

Sequelize——在撰写本文时每周下载量为 483，000 次——似乎是最稳定的，不同之处在于没有使用 mongo(这是最受欢迎的 MVP 数据库或项目)。
Sequelize 是一个基于 promise 的 Node.js ORM，用于 Postgres、MySQL、MariaDB、SQLite 和 Microsoft SQL Server。它具有可靠的事务支持、关系、急切加载和延迟加载、读取复制等特性。
优点:

*   Sequelize 抽象出了各种 SQL 实现之间的细微差别。
*   Sequelize 实现了一个中间件，允许为每个字段中的失败定制错误消息。
*   Sequelize 提供了一个基于承诺的库，这有助于更好地管理异常和意外结果。
*   能够“同步”已更改型号的数据库。所以它会改变或自动为您创建表格。
*   能够为您查询所有复杂的连接。你只需要学会如何将这些物体联系起来。

缺点:

*   Sequelize 有时会生成复杂的查询。在模型定义中，当关系为“有一个”时，有时不能正确理解关系
*   Sequalize 缺乏 NoSQL 支持:想使用 Mongo？得换了。
*   有一些奇怪的异常，并且您想要创建更多到数据库的连接。为此，您可以使用连接池。

ORM -对象关系模型最显著的优点是可以在任何 SQL 数据库之间快速切换。因此，如果您开始使用 SQLite 作为本地数据库，并希望下周切换到 MySQL 或 Postgre，您只需更改连接器。

# 选择 SQL 数据库

*   **[SQlite](https://www.sqlite.org/index.html)**——易于使用本地数据库，无需运行服务器。它将只使用一个本地文件，所以它适合于非并发访问和单服务器应用程序。何时使用 SQLite:嵌入式应用程序、磁盘访问替换、测试何时不使用 SQLite:处理大量数据、高写入量、需要网络访问。
*   ——这是事实上的 SQL 服务器，支持大多数旧语言。诺基亚，谷歌，脸书，YouTube 都用它。它允许多个用户连接到同一个服务器并拥有不同的数据库。还有 [mysql workbench](https://www.mysql.com/products/workbench/) ，mysql 最常用的 UI 工具之一，以防你不喜欢 MySQL CLI。何时使用 MySQL:普及性和易用性——这样你可以很容易地找到关于它、安全性、速度和复制的资源。网站和 web 应用程序、分布式操作、不使用 MySQL 时的预期未来增长:已知限制-缺乏对完全连接、许可和专有功能的支持-自 2009 年被 Oracle 收购以来，一些功能在开源许可证中不可用，新功能的开发速度减慢。SQL 合规性是必要的，并发性和大数据量。MySQL 被广泛用于那些只需要数据库进行简单数据交易的基于 web 的项目。然而，MySQL 在负载过重或试图完成复杂查询时表现不佳是很常见的。当只要求读取速度时，MySQL 表现良好。MySQL + InnoDB 为不同场景提供了非常好的读写速度。总的来说，MySQL 在高并发场景下表现良好。MySQL 是可靠的，并且可以很好地与商业智能应用程序一起工作，因为商业智能应用程序通常是大量读取的。
*   :PostgreSQL 的 [PGAdmin](https://www.pgadmin.org/) 工具非常棒，在配置和与服务器交互方面相当完整。PostgreSQL 广泛应用于读写速度至关重要且需要验证数据的大型系统中。此外，它还支持各种性能优化，这些优化仅在商业解决方案中可用，如地理空间数据支持、无读锁的并发性。总的来说，PostgreSQL 性能在需要执行复杂查询的系统中得到了最好的利用。当需要读/写速度，并且需要大量数据分析时，PostgreSQL 表现良好。PostgreSQL 也适用于商业智能应用程序，但更适合需要快速读/写速度的数据仓库和数据分析应用程序。使用 Postgres 时:数据完整性必不可少，与其他工具的集成，复杂的操作——查询可以分布在多个 CPU 上。何时不使用 Postgres:速度是必要的——对于快速读取、简单设置、复杂复制。
*   许多大公司、Linux 发行版等等都在使用 MariaDB 。一些使用 MariaDB 的组织包括 Google、Craigslist、Wikipedia、archlinux、RedHat、CentOS 和 Fedora。MariaDB 是 Mysql 的一个分支，所以大部分特性都是兼容的，包括查询和索引。
*   **[繁琐——微软 SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-2016)** 与 MySQL 非常相似，也是它的一个分支。使用 MSSQL，您可以创建一个数据库，而不需要指定不同的引擎。一般来说运行起来很贵，因为你需要服务器运行软件的许可证。如果你使用。NET，你有一些优势，但这不是我们的情况。另一方面，您可以使用直接连接到 MSSQL 的节点来扩展现有功能。

# 配置序列

现在让我们回到我们的有序表格。对于我们的例子，我们将选择最简单的方式: [Sqlite](https://www.npmjs.com/package/sqlite) -每周 20K 下载量-因为服务器端没有配置。
如果您需要更复杂的示例，请在下面的部分进行评论。

让我们开始安装周围的东西:

```
$ npm install --save sequelize # This will install v5

$ npm install --save sqlite3

# Optionally add other db connectors - but you will need only one at a time
# keep your code clean and remove the unused ones from package.json
$ npm install --save pg pg-hstore # [Postgres](https://www.npmjs.com/package/pg) 680K vs [HStore deserializer](https://www.npmjs.com/package/pg-hstore) 80K AMT
$ npm install --save mysql2 # [Mysql](https://www.npmjs.com/package/mysql2) 198K weekly downloads ATM
$ npm install --save mariadb #  [MariaDB connector](https://www.npmjs.com/package/mariadb) 5K weekly downloads ATM
$ npm install --save tedious # [Microsoft SQL Server](https://www.npmjs.com/package/mssql) - 80K downloads ATM 
```

那是容易的部分。我们实际上应该也想使用`sequelize-cli.`

```
$ npm install --save --only=dev sequelize-cli 
```

现在我们要创建默认的配置文件。

```
node_modules/.bin/sequelize init 
```

这将生成一些文件夹 config/config.js、seeders、models/index.js、migrations。

> 与这个主题无关:在 [Appseed PRO](https://appseed.us/fullstack-apps-generator) 版本中，你也可以在 VSCode 中设置一切来轻松检查代码质量。你甚至可以免费获得以上所有代码，自己使用。麻省理工的执照。

index.js 文件获取您将在`models`文件夹下创建的所有模型，并在运行时加载它们。它还调用`sync`方法来确保数据库是最新的。

让我们来看看配置文件:

```
module.exports = {
    development: {
        dialect: 'sqlite',
        storage: './db.development.sqlite'
    },
    test: {
        dialect: 'sqlite',
        storage: ':memory:'
    },
    production: {
        username: process.env.DB_USERNAME,
        password: process.env.DB_PASSWORD,
        database: process.env.DB_NAME,
        host: process.env.DB_HOSTNAME,
        port: process.env.DB_PORT,
        dialect: 'mysql',
        use_env_variable: 'DATABASE_URL'
    },
    production2: {
        use_env_variable:"DB_CONNECTION_STRING",
        dialect: "postgres",
        ssl: true,
        dialectOptions: {
            ssl: true
        }
    }
}; 
```

如您所见，您可以根据不同的环境对数据库使用不同的配置。它必须与。环境文件。您可以使用的选项包括:环境变量、ssl、主机、端口、完整连接字符串、存储、数据库名称、拨号选项、协议(对于 Heroku 为 tcp 或 null)、日志记录(可以设置为 null 或 console.log)、同步:{ force: true }、池(最大值:5、空闲值:30000、获取值:60000)。关于 coonfig 上的序列选项的更多细节可以在[序列文档页面](http://docs.sequelizejs.com/manual/usage.html)上找到。

现在让我们创建一个用户。您可以使用 CLI 或自己编写代码。当添加信息作为主键和其他额外细节时，我喜欢使用这两种方式。

```
node_modules/.bin/sequelize model:create --name User --attributes name:string,surname:string,email:string,password:string 
```

最酷的事情是，这将自动生成完整的模型。如果需要，您可以从迁移中复制 id，并将其添加到`id: DataTypes.INTEGER`

```
'use strict';
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    name: DataTypes.STRING,
    surname: DataTypes.STRING,
    email: DataTypes.STRING,
    password: DataTypes.STRING
  }, {});
  User.associate = function(models) {
    // associations can be defined here
  };
  return User;
}; 
```

另外，它还为这个表模型生成迁移。

```
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Users', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      name: {
        type: Sequelize.STRING
      },
      surname: {
        type: Sequelize.STRING
      },
      email: {
        type: Sequelize.STRING
      },
      password: {
        type: Sequelize.STRING
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Users');
  }
}; 
```

这样你就有了一个完整的例子，你可以自己去做。
如果您已经手动创建了一些模型，并且想要创建一个迁移，您可以通过调用 sequelize:
来完成

```
$ sequelize migration:generate --name [name_of_your_migration] 
```

# 编码到 DB

但是让我们回到生活中最美好的部分:编码！！！在你的方法中，你必须包括模型。我建议将模型和数据库访问从 API 路由处理程序中分离出来。

```
const User        = require('../../models').User;

//get some fake data for example
const faker = require('faker');
const email = faker.internet.email();
const password = faker.internet.password();
const firstName = faker.name.firstName();
const lastName = faker.name.lastName();
const body = {email: email, password: password, name: lastName, surname: firstName};

//create a user
const newUser = await User.create(body);

//find the user
let user = await User.findOne({where: {email}});

//destroy the user with the object, this will call DELETE where id = our_user_id automatically.
await user.destroy(); 
```

现在我们有了一个对象的全部循环。

**奖金 1** :除了一个用户的正常周期，你可能想在一个管理仪表板显示所有用户:

```
 let limit = 50;   // number of records per page
let offset = 0;
try{
    const data = await User.findAndCountAll();
    let page = req.params.page;      // page number
    let pages = Math.ceil(data.count / limit);
    offset = limit * (page - 1);
    const users = User.findAll({
        attributes: ['id', 'name', 'surname', 'email'],
        limit: limit,
        offset: offset,
        $sort: { id: 1 }
    });
    // next return an object that describes
    // the page and offset
    // this ensures you can display pages
    // and give to the ui the option
    // to display this to the user
    return {
        page,
        pages,
        offset,
        users
    };
}catch(err){
    //do something with your status like in the commented example
    //res.status(500).send('Internal Server Error');
} 
}); 
```

**奖励 2** :在将数据添加到模型中之前，您可以使用 [Joi](https://www.npmjs.com/package/joi) 在路线端验证您的数据。

```
const Joi         = require('joi');
const userSchema = Joi.object().keys({
    username: Joi.string().alphanum().min(3).max(30).optional(),
    password: Joi.string().required(),
    email: Joi.string().email({ minDomainAtoms: 2 }).required(),
    name: Joi.string().alphanum().min(2).max(100).optional(),
    surname: Joi.string().alphanum().min(2).max(100).optional()
});
//....
const result = Joi.validate(user, userSchema);
if(result.error){
    return res.status(422).json({
        errors: result.error
    });
} 
```

**加成 3** :
调用代码中的`npm run test`会为你检查一切。用 [BDD 测试](https://blog.appseed.us/automated-tests-bdd-adding-tests-with-lab-in-your-project-with-express/)设置检查文章。
你也可以用 [ESLint](https://blog.appseed.us/automate-error-detection-with-es-lint/) 很容易地设置你的代码质量和错误检测。

请在下面的评论区告诉我你对这篇文章的看法。我很乐意回答你的要求。

PS:本文最早发表于 [appseed.us 博客](https://blog.appseed.us/sequelize-orm-express-application/)。