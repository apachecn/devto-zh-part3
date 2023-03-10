# Node.js 的 MySQL 示例

> 原文：<https://dev.to/evert/mysql-examples-for-nodejs-ce1>

如果您正在将 Node.js 服务与 MySQL 集成，您可能希望执行查询。

我见过相当多丑陋的代码来做这件事，经常使用回调。我想在这里分享一些我自己的模式。也许这是对别人有用的阅读。

我通常不使用 ORM。如果你已经对 [sequalize](https://www.npmjs.com/package/sequelize) 感到满意，这篇文章可能不适合你。

## 先决条件

MySQL 最流行的 NPM 包是 [mysql](https://www.npmjs.com/package/mysql) ，但是我用 [mysql2](https://www.npmjs.com/package/mysql2) 包代替。

`mysql2`包也很受欢迎，但有几个优点。我关心的优势是对承诺的支持。这个包的作者实际上已经与`mysql`包的作者合作，以减少双重努力，并与`mysql`兼容，所以对许多人来说，这是一个顺便的替代。

## 创建池

```
const mysql = require('mysql2/promise');
const pool = mysql.createPool({
  host: 'localhost',
  user: 'root',
  database: 'test',
  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0
}); 
```

Enter fullscreen mode Exit fullscreen mode

一个池管理多个连接。我为每个 node.js 服务器创建了一个池。

注意:如果您从`mysql`迁移到`mysql2`包，您可能不希望需要`mysql2/promise`。相反，您可以要求`mysql2`获得完全的向后兼容性，并使用`promise()`函数来访问 API 的承诺版本。

注 2: `mysql2`默认使用`utf8mb4`字符集。如果你不知道这意味着什么，相信这是正确的选择。如果你确实知道这是什么，那就为有人最终选择了一个合理的违约而高兴吧。

## 执行一个`SELECT`查询

一般来说，我不需要事务来执行`SELECT`查询，因此我可以简单地请求池来执行查询。

```
async function getBlogPost(id) {

  const result = await pool.query('SELECT * from posts WHERE id = ?', [id]); 
  if (!result[0].length < 1) {
    throw new Error('Post with this id was not found');
  }
  return result[0][0];

} 
```

Enter fullscreen mode Exit fullscreen mode

`result[0][0]`看起来有点怪异，这是因为查询的结果返回了一个包含 2 项的 tuple(数组)。第一项包含查询结果，第二项包含元数据。这个微妙的决定是我对图书馆的头号抱怨，因为它使许多东西比它们需要的稍微难看。

所以如果我们只想要结果的第一条记录，这就需要使用`result[0][0]`。

每当我写一个应该返回 1 项的函数时，我要么返回一项，要么抛出一个错误。我不回`undefined`或者`null`。

返回多条记录的`SELECT`查询更优雅:

```
async function getAllBlogPost() {

  const result = await pool.query('SELECT * from posts'); 
  return result[0];

} 
```

Enter fullscreen mode Exit fullscreen mode

每当我写一个返回 0 或更多项的函数时，这个函数总是返回一个包含 0 或更多项的数组。如果集合是空的，我返回一个空数组。

注意:有时空集合和不存在的集合是有区别的。如果存在这种区别，我会在后一种情况下抛出一个错误。

## 执行一个`INSERT`查询

一般在做`INSERT`查询的时候，大多数人会使用下面的语法:

```
INSERT INTO posts (title, body) VALUES (?, ?) 
```

Enter fullscreen mode Exit fullscreen mode

MySQL 还有第二个不太流行的插入系统，看起来更像一个`UPDATE`语句:

```
INSERT INTO posts SET title = ?, body = ? 
```

Enter fullscreen mode Exit fullscreen mode

我使用的是第二种语法。一个很大的优点是“值”接近字段的名称。如果你曾经不得不数问号，你知道为什么这很好。

简单地说，您可以如下执行这个查询:

```
async function insertPost(title, body) {

  await pool.query(
    'INSERT INTO posts SET title = ?, body = ?',
    [ title, body ]
  );

} 
```

Enter fullscreen mode Exit fullscreen mode

但是有一种更好的方法来做到这一点:

```
async function insertPost(title, body) {

  await pool.query(
    'INSERT INTO posts SET ?',
    { title, body } 
  );

} 
```

Enter fullscreen mode Exit fullscreen mode

如果您习惯于 MySQL 预准备语句，您可能会奇怪为什么会这样？

原因是当你传递对象或数组给占位符`?`时，它会得到特殊处理。

具体来说，如果你像这样传递一个数组:

```
['foo', 'bar'] 
```

Enter fullscreen mode Exit fullscreen mode

它扩展到

```
'foo', 'bar' 
```

Enter fullscreen mode Exit fullscreen mode

还有这样的物体:

```
{ foo: 'bar', gaz: 'zim' } 
```

Enter fullscreen mode Exit fullscreen mode

展开到:

```
`foo` = 'bar', `gaz` = 'zim` 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个很好的例子:

```
async function getByCategoryIds(ids) {

  // Pretends ids is [1, 2]
  const result = await pool.query(
    'SELECT * from posts WHERE category_id IN (?)',
    [ids],
  ); 
  return result[0];

} 
```

Enter fullscreen mode Exit fullscreen mode

这确实有效。如果你非常熟悉 MySQL 的工作方式，你可能会在这一点上尖叫:“这没有使用真正的准备好的语句”，你可能是对的。

## 准备好报表

默认情况下，`mysql`和`mysql2`包都模拟客户端的预准备语句。很多人认为这是一种非常糟糕的安全做法。

我不同意，可能会在另一篇文章中讨论这个问题。但是问问大多数安全专家，他们会告诉你这很糟糕。

要做一个真正的预准备报表，你必须使用`execute()`函数:

```
async function insertPost(title, body) {

  await pool.execute(
    'INSERT INTO posts SET title = ?, body = ?',
    [ title, body ]
  );

} 
```

Enter fullscreen mode Exit fullscreen mode

这使用了 *real* MySQL 预准备语句，但不幸的是不允许使用数组和对象。

## 在单个连接上运行多个查询

每次您调用`.query()`或`.execute()`时，您可能会从连接池中获得一个新的连接。如果您执行多个查询，有时使用同一个连接是个好主意。

一个原因可能是您有多个复制的 MySQL 服务器，并且您希望确保您在同一台机器上以相同的状态运行查询。

为此，您需要从池中获取一个连接，并在完成后释放它。

对我来说，理想的模式是这样的:

```
async function batchingThings() {

  const connection = await pool.getConnection();
  try {

    await connection.query('...');
    await connection.query('...');

  } finally {

    connection.release();

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，如果您在一个连接上运行多个查询，它们必须被序列化。它们不能平行。

## 交易

如果您运行多个更改数据库状态的查询，让它们作为一个组成功或失败通常是一个非常好的主意。

交易就是为此而使用的。在事务开始后，可以回滚自事务开始以来的每个查询。

我使用的模式与上一个相似，但是稍微复杂一点:

```
async function batchingThings() {

  const connection = await pool.getConnection();
  await connection.beginTransaction();

  try {

    await connection.query('...');
    await connection.query('...');

    await connection.commit();

  } catch (err) {

    await connection.rollback();
    // Throw the error again so others can catch it. 
    throw err;

  } finally {

    connection.release();

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我的事务代码变得太复杂，我需要把它分成多个函数，我就把`connection`对象作为参数传递。

## 打印一切

我希望我的例子是可访问的，但是我所有的代码都是用 Typescript 写的。每一个 MySQL 表行都有自己的类型，我尽可能地严格。

这真是个好主意。如果有兴趣，我可以用 typescript 和 mysql2 的例子写另一篇文章。