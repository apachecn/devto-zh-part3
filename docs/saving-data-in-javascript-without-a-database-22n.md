# 在没有数据库的情况下用 JavaScript 保存数据

> 原文：<https://dev.to/healeycodes/saving-data-in-javascript-without-a-database-22n>

您刚刚写了一段很棒的 JavaScript。但是当正在运行的进程停止，或者用户刷新时，所有这些好的数据**都消失在以太网**中。

这是你吗？

当构建原型，或者在小项目上工作时，管理一些状态是有帮助的，而不需要求助于不是为您正在尝试的创造之痒而设计的数据库解决方案。

我们将探索一些选项，我希望当我开始在网络上修修补补时就知道这些选项。我们将在浏览器中查看 JavaScript，在后端查看 Node.js。我们还将研究一些使用本地文件系统的轻量级数据库。

## Node.js

首先是 JSON 序列化数据并保存到磁盘。如果你以前没有使用过 JSON，MDN 文档有一篇很棒的文章。

```
const fs = require('fs');

const users = {
    'Bob': {
        age: 25,
        language: 'Python'
    },
    'Alice': {
        age: 36,
        language: 'Haskell'
    }
}

fs.writeFile('users.json', JSON.stringify(users), (err) => {  
    // Catch this!
    if (err) throw err;

    console.log('Users saved!');
}); 
```

我们创建了 users 对象，用 [JSON#stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 将它转换成 JSON，并调用 [fs#writeFile](https://nodejs.org/api/fs.html#fs_fs_writefile_file_data_options_callback) 。我们传入了文件名、序列化数据和一个箭头函数，作为写操作完成时执行的回调函数。与此同时，您的程序将继续执行代码。

您还可以使用此方法，通过传入任何可以转换为字符串的内容来编写普通的序列化数据。如果你正在存储文本数据，你可能会发现 [fs#appendFile](https://nodejs.org/api/fs.html#fs_fs_appendfile_path_data_options_callback) 很有用。它使用几乎相同的 API，但是将数据发送到文件的末尾，保留现有的内容。

有一个同步选项， [fs#writeFileSync](https://nodejs.org/api/fs.html#fs_fs_writefilesync_file_data_options) ,但不推荐使用，因为在写入操作完成之前，您的程序将没有响应。在 JavaScript 中，你的目标应该是[永远不要阻塞](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Never_blocking)。

如果您正在处理 CSV 文件，请使用久经沙场的 [node-csv](https://github.com/adaltas/node-csv) 项目。

让我们用 [fs#readFile](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback) 将这些用户加载回我们的程序。

```
fs.readFile('user.json', (err, data) => {
    // Catch this!
    if (err) throw err;

    const loadedUsers = JSON.parse(data);
    console.log(loadedUsers);
}); 
```

### 轻量级数据库

SQLite 使用一个本地文件作为数据库——它是世界上我最喜欢的软件之一。它使我的许多较小的项目只需要很少的维护和很少的部署麻烦就可以存在。

以下是一些关于 SQLite 的事实:

*   与其他代码相比，该项目的测试代码和测试脚本数量是 T1 的 711 倍。
*   开发者承诺至少在 2050 年保持向后兼容。
*   它用在飞机上，用在安卓系统中，你可能在今天写这篇文章的路上以某种方式与它互动过。

说真的，[SQLite 是如何被测试的](https://www.sqlite.org/testing.html)是一次狂野之旅。

在 Node.js 中，我们常用`sqlite3` [npm 包](https://www.npmjs.com/package/sqlite3)。我将使用 Glitch 的`hello-sqlite`模板中的一些代码，你可以[在没有账户的情况下随意摆弄和重新组合](https://glitch.com/edit/#!/remix/hello-sqlite)。

```
// hello-sqlite
var fs = require('fs');
var dbFile = './.data/sqlite.db'; // Our database file
var exists = fs.existsSync(dbFile); // Sync is okay since we're booting up
var sqlite3 = require('sqlite3').verbose(); // For long stack traces
var db = new sqlite3.Database(dbFile); 
```

通过这个`db`对象，我们可以像连接到外部数据库一样与本地数据库进行交互。

我们可以创建表格。

```
db.run('CREATE TABLE Dreams (dream TEXT)'); 
```

插入数据(带错误处理)。

```
db.run('INSERT INTO Dreams (dream) VALUES (?)', ['Well tested code'], function(err) {
  if (err) {
    console.error(err);
  } else {
    console.log('Dream saved!');
    }
}); 
```

将数据选回来。

```
db.all('SELECT * from Dreams', function(err, rows) {
  console.log(JSON.stringify(rows));
}); 
```

你可能想考虑用[序列化你的一些数据库查询](https://stackoverflow.com/a/42946402)。*serialize()函数中的每个命令都保证在下一个命令开始之前执行完。sqlite3 [文档](https://github.com/mapbox/node-sqlite3/wiki)是可扩展的。密切关注 SQLite [数据类型](https://www.sqlite.org/datatype3.html)，因为它们可能与其他数据库略有不同。*

如果 SQLite 看起来对你的项目来说开销太大，考虑一下 [lowdb](https://github.com/typicode/lowdb) (还有[可以在小故障](https://glitch.com/edit/#!/low-db)上重新组合)。lowdb 令人兴奋，因为它是一个由 Lodash 支持的小型本地 JSON 数据库*(支持 Node、Electron 和**浏览器** )* 。它不仅在后端作为 JSON 文件的包装器工作，还提供了一个 API 来包装浏览器中的 localStorage。

从他们的[例子](https://github.com/typicode/lowdb/tree/master/examples#browser) :

```
import low from 'lowdb'
import LocalStorage from 'lowdb/adapters/LocalStorage'

const adapter = new LocalStorage('db')
const db = low(adapter)

db.defaults({ posts: [] })
  .write()

// Data is automatically saved to localStorage
db.get('posts')
  .push({ title: 'lowdb' })
  .write() 
```

## 浏览器

这就把我们带到了前端。 [window#localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 是在 HTTP cookies 中存储数据的现代解决方案——MDN[不再推荐使用](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage#Old_fashioned_cookies)来存储数据。

让我们现在就和他们互动。如果你在桌面上，打开你的开发控制台(Chrome 上的 F12 ),看看开发为你存储了什么:

```
for (const thing in localStorage) {
  console.log(thing, localStorage.getItem(thing))
}

// Example of one thing:
// pusherTransportTLS {"timestamp":1559581571665,"transport":"ws","latency":543} 
```

我们看到了 lowdb 如何与 localStorage 交互，但是对于我们的小项目来说，直接与 API 对话可能更容易。像这样:

```
// As a script, or in console
localStorage.setItem('Author', 'Andrew') // returns undefined
localStorage.getItem('Author') // returns "Andrew"
localStorage.getItem('Unset key') // returns null 
```

更简单的是:你可以把它当作一件物品。虽然，MDN 推荐 API 胜过这个[快捷方式](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API#Basic_concepts)。

```
console.log(localStorage['Author']); // prints "Andrew" 
```

如果你不想永远在用户的电脑上存储数据(可以用`localStorage.clear()` **清除，但不要在开发**上运行这个)，你可能会对[会话存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)感兴趣，它有一个几乎相同的 API，只在用户在页面上时存储数据。

## 结束注释

我在某处读到 SQLite 被用于国际空间站的某些功能，但我还没有找到来源。我的未婚妻想让你知道 SQLite *是一个数据库，这篇文章的标题是不正确的。*

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。