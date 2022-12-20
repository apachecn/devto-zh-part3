# 什么是对象关系映射？如何开发自己的 JavaScript ORM

> 原文：<https://dev.to/nielsenjared/what-is-object-relational-mapping-how-to-roll-your-own-javascript-orm-4ni3>

[![Object-Relational Mapping](img/1fc0ec4489d4348751afb11001970bd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cvkHzgUC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cyxo5eg87yw5xf41wve7.png)

*本文原载于[jarednielsen.com](http://jarednielsen.com/object-relational-mapping-javascript-orm/)T3】*

当您第一次学习 web 开发时，理解对象关系映射可能是一个挑战。在本教程中，您将使用 JavaScript 构建自己的 ORM，以帮助您更好地理解全功能的对象关系映射(如 Sequelize)是如何工作的。

您将了解到:

*   什么是对象关系映射？
*   如何使用承诺处理异步数据库查询
*   模型以及如何实现它们
*   如何用 JavaScript 实现一个简单的基于承诺的 ORM

## 什么是对象关系映射？

根据[维基百科](https://en.wikipedia.org/wiki/Object-relational_mapping)，对象-关系映射是:

> ...一种使用面向对象编程语言在不兼容类型系统之间转换数据的编程技术。

马丁·福勒在[或](https://martinfowler.com/bliki/OrmHate.html)中给了我们一个更微妙的答案:

> 本质上，您所做的是在两种完全不同的数据表示之间进行同步，一种表示在关系数据库中，另一种表示在内存中。

这对我们 Node.js 开发者来说意味着什么？

我们的第一个问题是 SQL 数据库管理系统只存储标量值。在 JavaScript 中，除了原始数据类型，一切都是对象。与对象不同，标量变量一次只能保存一个值。因此，在使用 SQL 数据库管理系统(DBMS)的情况下，这意味着字符串或整数。幸运的是，有一些 [npm 包](https://www.npmjs.com/search?q=sql)，比如 [mysql](https://www.npmjs.com/package/mysql) 和 [mysql2](https://www.npmjs.com/package/mysql2) ，它们以 JSON 格式返回我们的查询。但这只是成功的一半。

问题的另一半是编写允许我们用本地语言(JavaScript)而不是 SQL DBMS 查询数据库的方法。对象关系映射很有用，因为它允许我们用可重用的数据库查询方法来分离应用程序中的关注点。这种关注点分离的额外的，也许也是最重要的好处是，我们可以很容易地使用其他 SQL 数据库，而不需要重写整个代码库，只需要对 ORM 进行微小的修改。

## 战争永远不是长久之计

在研究这篇文章的时候，我遇到了很多关于 ORM 的参考，称之为“计算机科学的越南”。这个短语可以追溯到 Ted Neward 在一次会议上的发言，他在他的博客上详细描述了这个短语:

> 对象/关系映射[...]代表了一个泥潭，开始很好，变得更复杂久而久之，不久就让用户陷入一个没有明确分界点，没有明确获胜条件，也没有明确退出策略的承诺中。

就像在战场上一样，对象关系映射辩论有两个激烈的、固执己见的方面:支持 ORM 的一方和反对 ORM 的一方。2016 年的一篇文章，[我该不该用 ORM？Mithun Sasidharan 描述了两个阵营之间不同的中间立场:选择最适合您的应用的方法。他列出了做这个决定时要问的几个问题，但我们可以将其浓缩为两个:](https://medium.com/@mithunsasidharan/should-i-or-should-i-not-use-orm-4c3742a639ce)

*   你的数据访问模式会很简单吗？使用 ORM
*   速度是你优先考虑的吗？不要使用 ORM

在最近的一篇文章中，[为什么你应该避免 ORM(在 Node.js 中有例子)](https://blog.logrocket.com/why-you-should-avoid-orms-with-examples-in-node-js-e0baab73fa5)，托马斯·亨特二世提出了一个介于手写数据库驱动程序和第三方 ORM 之间的中间地带:查询构建器，比如 [knex](https://www.npmjs.com/package/knex) 。他概括的理由是:

*   ORM 非常复杂。
*   ORM 效率很低。
*   ORM 不是万能的。

虽然以上对于全功能的 ORM 来说是正确的，但是 Sasidharan 概述了使用 ORM 的几个相对优势:

*   ORMs 促进了模型的实现
*   ORMs 导致更小的代码库
*   ORM 能够加快启动时间

足够的历史和辩论。让我们开始建造吧！

## 许诺，承诺

Sequelize 标榜自己是一个基于承诺的 ORM。为了理解对象关系映射，我们需要对承诺有一个基本的理解。

### 异步

让我们声明一个函数`asynchronousEmulator`，来模拟异步数据库查询。该函数生成一个随机数，并将其传递给`setTimeout`。

```
function asynchronousEmulator(num){
  const rand = Math.floor(Math.random() * 1000)
  setTimeout(function(){
      console.log(num);
  }, rand);
} 
```

接下来，让我们声明第二个函数`ordinalNumbers`，并对 asynchronousEmulator 进行三次调用。

```
function ordinalNumbers(){
  asynchronousEmulator("First");
  asynchronousEmulator("Second");
  asynchronousEmulator("Third");
}

ordinalNumbers(); 
```

我们期望`ordinalNumbers`在写入时记录每个数字，但是当我们从命令行运行时，序号被无序记录。(除非是按顺序！🤯)

### 回调

我们可以通过向我们的`asynchronousEmulator`传递一个函数`cb`以及一个字符串来解决这个问题。当`setTimout`执行完毕后，它将调用我们的函数`cb()`。我们重构了`ordinalNumbers`,这样每次调用`asynchronousEmulator`都需要第二个参数，一个匿名函数。那是我们的复试！每个匿名函数(除了最后一个)调用`asynchronousEmulator`。

```
function asynchronousEmulator(num, cb){
  const rand = Math.floor(Math.random() * 1000)
  setTimeout(function(){
      console.log(num);
      cb();
  }, rand);
}

function ordinalNumbers(){
  asynchronousEmulator("First", function(){
    asynchronousEmulator("Second", function(){
      asynchronousEmulator("Third", function(){
        return;
      });
    });
  });
}

ordinalNumbers(); 
```

但我们现在正处于回调地狱的边缘！

### 承诺

使用 Promise 对象，我们可以省略回调，而是重构`asynchronousEmulator`来返回一个新的 Promise 对象:

```
function asynchronousEmulator(num){
  return new Promise(function(resolve, reject){
    const rand = Math.floor(Math.random() * 1000);
    setTimeout(function(){
       console.log(num);
       resolve();
       reject();
     }, rand);
  });
} 
```

承诺是一个对象(这就是我们使用`new`关键字的原因),它管理异步任务的最终解决(或失败)。承诺只会承诺结束。

我们将我们的承诺传递给一个带有两个参数的函数(执行者)，`resolve`和`reject`。如果承诺兑现(即执行成功)，将调用`resolve`。如果承诺失败(即有错误)，将调用`reject`函数。

然后我们需要重构序数以使用`.then()`和`.catch()`而不是回调:

```
function ordinalNumbers(){
  asynchronousEmulator("First")
  .then(function(){
    return asynchronousEmulator("Second")
  })
  .then(function(){
    return asynchronousEmulator("Third")
  })
  .catch(function(){
    return "Something went wrong...";
  });
}

ordinalNumbers(); 
```

我们将`.then()`和`.catch()`函数链接到我们的承诺调用。我们可以链接任意多的对象，有效地向我们的 Promise 对象添加多个回调。`.then()`处理我们承诺的成功决议。`.catch()`处理我们承诺的失败或拒绝。

要深入了解，请阅读我的相关文章[学习 JavaScript 承诺和承诺方法](https://dev.to/javascript-promises/)。

## 设置 MySQL 数据库

在整个教程中，我们将使用 MySQL 和 mysql npm 包来构建一个简单的节点应用程序，供兽医跟踪宠物和主人。我们将使用以下模式和种子:

```
DROP DATABASE IF EXISTS vet_db;
CREATE DATABASE vet_db;
USE vet_db;

CREATE TABLE cats
(
    id INT NOT NULL AUTO_INCREMENT,
    pet_name VARCHAR(30) NOT NULL,
    pet_age INTEGER(2),
    pet_sex VARCHAR(6) NOT NULL,
    desexed BOOLEAN DEFAULT false,
    PRIMARY KEY (id)
);

INSERT INTO cats (pet_name, pet_age, pet_sex, desexed)
VALUES  ('Tommy', 12, "Male", false),
        ('Whiskers', 2, "Female", true),
        ('Felix', 1, "Male", true)
; 
```

## 所有的猫都是未定义的

让我们构建一个简单的查询数据库的节点应用程序。创建一个新目录并安装我们唯一的依赖项:

```
npm install mysql 
```

然后创建一个`server.js`文件并建立到我们数据库的连接:

```
const mysql = require("mysql");

const connection = mysql.createConnection({
  host: "localhost",
  port: 3306,
  user: "root",
  password: "",
  database: "vet_db"
});

connection.connect(function(err) {
  if (err) {
    console.error('error connecting: ' + err.stack);
    return;
  }

  console.log('connected as id ' + connection.threadId);
}); 
```

让我们编写第一个数据库查询，从我们的`cats`表中选择所有的猫:

```
function all(){
  connection.query("SELECT * FROM cats;", function(err, data) {
    if (err) throw err;
    return data;
  });
}

let allTheCats = all();

console.log(`All the cats are ${allTheCats}!`); 
```

所有的猫都没有定义！为什么？我们试图在连接到数据库之前查询它。我们可以用承诺来解决(双关语)这种情况。

```
function all(){
  const sql = "SELECT * FROM cats";

  return new Promise(function(resolve, reject){
    connection.query(sql, function(err, data) {
      if (err) reject(err);
      resolve(data);
    });
  })
} 
```

现在我们正在使用一个承诺，我们将`.then()`和`.catch()`链接到我们对`all()`的调用。

```
all()
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

结果应该类似于:

```
Connected as id 6
[ RowDataPacket {
    id: 1,
    pet_name: 'Tommy',
    pet_age: 12,
    pet_sex: 'Male',
    desexed: 0 },
  RowDataPacket {
    id: 2,
    pet_name: 'Whiskers',
    pet_age: 2,
    pet_sex: 'Female',
    desexed: 1 },
  RowDataPacket {
    id: 3,
    pet_name: 'Felix',
    pet_age: 1,
    pet_sex: 'Male',
    desexed: 1 } ] 
```

## CRUD: Cat 读取更新删除

实现我们剩余的 CRUD 方法很简单:

```
// create new cat with name and sleepy values
function create(name, age, sex, fixed){
  const sql = `INSERT INTO cats (pet_name, pet_age, pet_sex, desexed) VALUES (?, ?, ?, ?)`;

  return new Promise(function(resolve, reject){
    connection.query(sql, [name, age, sex, fixed], function(err, data) {
      if (err) reject(err);
      resolve(data);
    });
  })
} 
```

我们称之为`create` :

```
create("Keyboard", 7, "female", false)
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

结果将类似于:

```
Connected as id 7
OkPacket {
  fieldCount: 0,
  affectedRows: 1,
  insertId: 4,
  serverStatus: 2,
  warningCount: 0,
  message: '',
  protocol41: true,
  changedRows: 0 }
[ RowDataPacket {
    id: 1,
    pet_name: 'Tommy',
    pet_age: 12,
    pet_sex: 'Male',
    desexed: 0 },
  RowDataPacket {
    id: 2,
    pet_name: 'Whiskers',
    pet_age: 2,
    pet_sex: 'Female',
    desexed: 1 },
  RowDataPacket {
    id: 3,
    pet_name: 'Felix',
    pet_age: 1,
    pet_sex: 'Male',
    desexed: 1 },
  RowDataPacket {
    id: 4,
    pet_name: 'Keyboard',
    pet_age: 7,
    pet_sex: 'female',
    desexed: 0 } ] 
```

我们的`update`方法:

```
// update cat sleepy value by id
function update(desexed, id){
  const sql = `UPDATE cats SET desexed = ? WHERE id = ?`;

  return new Promise(function(resolve, reject){
    connection.query(sql, [desexed, id], function(err, data) {
      if (err) reject(err);
      resolve(data);
    });
  })
} 
```

我们这样称呼`update`:

```
update(true, 1)
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

我们的查询结果将类似于:

```
Connected as id 8
OkPacket {
  fieldCount: 0,
  affectedRows: 1,
  insertId: 5,
  serverStatus: 2,
  warningCount: 0,
  message: '',
  protocol41: true,
  changedRows: 0 }
OkPacket {
  fieldCount: 0,
  affectedRows: 1,
  insertId: 0,
  serverStatus: 2,
  warningCount: 0,
  message: '(Rows matched: 1  Changed: 1  Warnings: 0',
  protocol41: true,
  changedRows: 1 }
[ RowDataPacket {
    id: 1,
    pet_name: 'Tommy',
    pet_age: 12,
    pet_sex: 'Male',
    desexed: 1 },
  RowDataPacket {
    id: 2,
    pet_name: 'Whiskers',
    pet_age: 2,
    pet_sex: 'Female',
    desexed: 1 },
  RowDataPacket {
    id: 3,
    pet_name: 'Felix',
    pet_age: 1,
    pet_sex: 'Male',
    desexed: 1 },
  RowDataPacket {
    id: 4,
    pet_name: 'Keyboard',
    pet_age: 7,
    pet_sex: 'female',
    desexed: 0 },
  RowDataPacket {
    id: 5,
    pet_name: 'Keyboard',
    pet_age: 7,
    pet_sex: 'female',
    desexed: 0 } ] 
```

请注意，汤米现在是固定的。不再是种牛了！

还要注意，我们有两个键盘猫，因为我们再次运行了`create`方法。让我们用一个`destroy`方法来解决这个问题。(“Destroy”听起来有些极端，但是`delete`是 JavaScript 中的保留关键字。)

```
function destroy(id){
  const sql = `DELETE FROM cats WHERE id = ?`;

  return new Promise(function(resolve, reject){
    connection.query(sql, [id], function(err, data) {
      if (err) reject(err);
      resolve(data);
    });
  })
} 
```

我们这样调用`destroy`方法:

```
destroy(5)
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

## ORM +架构模式

在 web 应用程序中实现[架构模式](https://en.wikipedia.org/wiki/Architectural_pattern)时，有许多约定。[例如 MVC，或者模型视图控制器](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)，是(was？)在 web 开发者中非常受欢迎。无论你选择哪种模式，它都很可能实现一个*模型*层。

为什么叫模型？该模型如此命名是因为它定义了数据库的形状。你可以说它“模拟”了数据。模型还负责管理应用程序中的数据结构。

如上所述，使用架构模式的主要原因之一是[关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns)。在`server.js`中，我们将通过将数据库查询与控制器逻辑分离来实现和模块化模型。

在目录的根级别，我们创建一个文件夹`db`并将两个`.sql`文件移动到其中。这是保持应用程序有组织的常见做法，因为它向其他开发人员发出信号，表明该文件夹中有与数据库相关的文件。

同样，在目录的根级别，我们创建一个文件夹`config`，在`config`中我们创建一个文件`connection.js`。这也是保持应用程序有序的常见做法，也是存储其他配置文件的位置。

从`server.js`开始，我们剪切样板 MySQL 配置，包括 NPM 导入，并将其粘贴到`connection.js`。我们用`module.exports`出口`connection`。

```
const mysql = require("mysql");

const connection = mysql.createConnection({
  host: "localhost",
  port: 3306,
  user: "root",
  password: "",
  database: "petshop_db"
});

connection.connect(function(err) {
  if (err) {
    console.error('error connecting: ' + err.stack);
    return;
  }

  console.log('connected as id ' + connection.threadId);
});

module.exports = connection; 
```

同样，在目录的根级别，我们创建一个文件夹`models`，在`models`中我们创建一个文件`cat.js`。这是我们的第一个模型。

在`cat.js`的顶部，我们要求`connection.js`导入我们的数据库连接。

在`cat.js`中，我们创建了一个对象`cat`。

从`server.js`中，剪切`all`、`create`、`update`和`destroy`函数，粘贴到`cat.js`中的`cat`对象内。**将对这些函数的调用留在`server.js`中。**

现在将`cat`对象中的函数转换成对象方法并导出`cat`。**不要忘记逗号！**

```
const connection = require('../config/connection.js');

const cat = {
  all: function(){
    const sql = "SELECT * FROM cats";

    return new Promise(function(resolve, reject){
      connection.query(sql, function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  },

  create: function(name, age, sex, fixed){
    const sql = `INSERT INTO cats (pet_name, pet_age, pet_sex, desexed) VALUES (?, ?, ?, ?)`;

    return new Promise(function(resolve, reject){
      connection.query(sql, [name, age, sex, fixed], function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  },

  update: function(desexed, id){
    const sql = `UPDATE cats SET desexed = ? WHERE id = ?`;

    return new Promise(function(resolve, reject){
      connection.query(sql, [desexed, id], function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  },

  destroy: function(id){
    const sql = `DELETE FROM cats WHERE id = ?`;

    return new Promise(function(resolve, reject){
      connection.query(sql, [id], function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  }
}

module.exports = cat; 
```

在`server.js`中，要求`./models/cat.js`并修改对`all`、`create`和`update`的调用，以调用导入的对象方法。

```
const cat = require('./models/cat.js');

cat.all()  
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  });

cat.create("Keyboard", 7, "female", false)
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  });

cat.update(true, 1)
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  });

cat.destroy(5)
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

您的目录结构现在应该是这样的:

```
/config
  |_ connection.js
.gitignore
/models
  |_ cat.js
/db
  |_ schema.sql
  |_ seeds.sql
server.js 
```

运行`server.js`以验证一切正常。

除非我们的兽医专门研究猫，否则我们会想要其他物种的模型。我们*可以*复制`cat.js`文件，重新命名，以及所有适当的引用`dog`、*但*，那就不会干了。有什么解决办法？你猜对了。对象关系映射。

## 什么是 ORM？

如果我们阅读 Sequelize 源代码，我们会发现它是基于类的。JavaScript 中的类是构造函数的语法糖，所以，暂时我们将使用构造函数，然后再将我们的 ORM 翻译成类。

构造函数是做什么的？

构造函数创建一个对象。

**什么是 MySQL？**

关系数据库。

**那么什么是对象关系映射呢？**

将数据库功能和结构映射到对象。

让我们开始吧。

在`config`文件夹中新建一个文件`orm.js`，并要求`connection.js`。

接下来，在`orm.js`中创建一个带有参数`table`的构造函数`ORM`，并用`this.table = table`初始化它。

从`cat.js`中剪切方法并粘贴到`ORM`构造函数中。

使用`this`将对象方法重构为构造函数方法，并用`this.table`替换所有对`cats`表的引用。**别忘了把`:`改成`=`！**

然后导出 ORM: `module.exports = ORM;`

完成的`orm.js`将看起来像:

```
const connection = require('./connection.js');

const ORM = function(table){
  this.table = table;

  this.all = function(){
    const sql = `SELECT * FROM ??`;

    return new Promise(function(resolve, reject){
      connection.query(sql, table, function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  },

  this.create = function(name, age, sex, fixed){
    const sql = `INSERT INTO ?? (pet_name, pet_age, pet_sex, desexed) VALUES (?, ?, ?, ?)`;

    return new Promise(function(resolve, reject){
      connection.query(sql, [table, name, age, sex, fixed], function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  },

  this.update = function(desexed, id){
    const sql = `UPDATE ?? SET desexed = ? WHERE id = ?`;

    return new Promise(function(resolve, reject){
      connection.query(sql, [table, desexed, id], function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  },

  this.destroy = function(id){
    const sql = `DELETE FROM ?? WHERE id = ?`;

    return new Promise(function(resolve, reject){
      connection.query(sql, [table, id], function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  }
}

module.exports = ORM; 
```

最后，重构`cat.js`模型，分别反映下面的代码:

```
const ORM = require('../config/orm.js');

const cat = new ORM("cats");

module.exports = cat; 
```

请注意，我们在`server.js`中没有改变任何东西。现在运行它来验证一切正常。

## 模型宠物

我们现在有几个扩展 ORM 的选项。如果我们想保持原样，我们可以将`cat.js`重命名为`index.js`并创建一个新的 ORM，`dog`，然后将它们导出到一个对象中。看起来应该是这样的:

```
const ORM = require('../config/orm.js');

const Cat = new ORM("cats");
const Dog = new ORM("dogs");

module.exports = { Cat, Dog }; 
```

现在我们可以更改我们的`server.js`中的导入来导入`models`目录(节点将找到`index.js`)并更新方法实例以使用嵌套的模型对象:

```
const db = require('./models');

db.Cat.all()
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  });

db.Dog.all()
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

在运行我们的服务器之前，我们需要创建一个`dogs`表:

```
CREATE TABLE dogs
(
    id INT NOT NULL AUTO_INCREMENT,
    pet_name VARCHAR(30) NOT NULL,
    pet_age INTEGER(2),
    pet_sex VARCHAR(6) NOT NULL,
    desexed BOOLEAN DEFAULT false,
    PRIMARY KEY (id)
); 
```

并用狗播种:

```
INSERT INTO dogs (pet_name, pet_age, pet_sex, desexed)
VALUES  ('Rex', 12, "Male", false),
        ('Fido', 2, "Female", true),
        ('Spot', 1, "Male", true)
; 
```

结果应该是所有的猫狗，像这样:

```
connected as id 16
[ RowDataPacket {
    id: 1,
    pet_name: 'Tommy',
    pet_age: 12,
    pet_sex: 'Male',
    desexed: 0 },
  RowDataPacket {
    id: 2,
    pet_name: 'Whiskers',
    pet_age: 2,
    pet_sex: 'Female',
    desexed: 1 },
  RowDataPacket {
    id: 3,
    pet_name: 'Felix',
    pet_age: 1,
    pet_sex: 'Male',
    desexed: 1 } ]
[ RowDataPacket {
    id: 1,
    pet_name: 'Rex',
    pet_age: 12,
    pet_sex: 'Male',
    desexed: 0 },
  RowDataPacket {
    id: 2,
    pet_name: 'Fido',
    pet_age: 2,
    pet_sex: 'Female',
    desexed: 1 },
  RowDataPacket {
    id: 3,
    pet_name: 'Spot',
    pet_age: 1,
    pet_sex: 'Male',
    desexed: 1 } ] 
```

## 重构变量表结构的自定义对象关系映射

如果我们的表有相似的结构，这种方法是很好的。但是如果我们的表不同呢？让我们创建一个所有者表。

```
CREATE TABLE owners
(
    id INT NOT NULL AUTO_INCREMENT,
    first_name VARCHAR(30) NOT NULL,
    last_name VARCHAR(30) NOT NULL,
    PRIMARY KEY (id)
); 
```

我们需要重构我们的 ORM 来使用准备好的语句和`format`方法:

```
this.create = function(cols, vals){
    let sql = `INSERT INTO ?? (??) VALUES (?)`;
    let inserts = [this.table, cols, vals];
    sql = connection.format(sql, inserts);

    return new Promise(function(resolve, reject){
      connection.query(sql, function(err, data) {
        if (err) reject(err);
        resolve(data);
      });
    })
  }, 
```

然后我们需要修改`models/index.js`来创建和导出`Owner` :

```
const ORM = require('../config/orm.js');

const Cat = new ORM("cats");
const Dog = new ORM("dogs");
const Owner = new ORM("owners");

module.exports = { Cat, Dog, Owner }; 
```

最后，在`server.js`中，我们需要创建一个所有者，并从数据库中选择它:

```
db.Owner.create(
  ["first_name", "last_name"],
  ["Jared", "Nielsen"]
  )
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  });

db.Owner.all()
  .then(function(data){
    console.log(data);
  })
  .catch(function(err){
    console.log(err);
  }); 
```

## 重构对象关系映射，通过模型实例化创建表格

如果我们希望 ORM 具有类似于 Sequelize 和其他第三方对象关系映射库的功能，我们需要能够通过模型实例化来创建我们的表。不过，这需要一些重构。我们开始吧！

让我们从重构`orm.js` :
开始

```
const connection = require('../config/connection.js');

function ORM(table, cols){
  this.table = table;
  this.cols = cols;
}

ORM.prototype.sync = function(){
  const columns = (cols) => Object.keys(cols).map(key => `${key}  ${cols[key]}`);

  const createTableSQL =`
    CREATE TABLE ${this.table} (
      id INT AUTO_INCREMENT NOT NULL, ${columns(this.cols).join(',')},
      PRIMARY KEY (id)
    )`;

  //@TODO: convert to Promises
  return new Promise(function(resolve, reject) {
      connection.query(createTableSQL, function(err, data){
        if (err) reject(err);
        resolve(data);
      });
  });
};

module.exports = ORM; 
```

然后我们可以重构`cat.js`中的`Cat`调用(双关语),以获取一个额外的参数，一个对象，其中属性是我们的列名，值是 SQL 数据类型:

```
const ORM = require('../config/orm.js');

const Cat = new ORM("cats", {
  pet_name: "VARCHAR(30) NOT NULL",
  pet_age: "INTEGER(2)",
  pet_sex: "VARCHAR(6)",
  desexed: "BOOLEAN DEFAULT false"
});

module.exports = Cat; 
```

但是当我们运行`server.js`时，我们得到一个错误:

```
TypeError: Cat.all is not a function 
```

为什么？

## 猫未定义

哇！你还和我在一起吗？这篇教程有将近 1000 行，所以我将在这里停下来，在随后的文章中继续。敬请期待扣人心弦的结论。我保证会很好。

* * *

我每周写一份时事通讯，分享关于编程、解决问题和终身学习的文章。[立即加入](http://eepurl.com/cP8CMn)