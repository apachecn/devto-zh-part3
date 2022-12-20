# 制作一个可以在桌面和手机上运行的全文搜索模块(Pt。1)

> 原文：<https://dev.to/craftzdog/making-a-full-text-search-module-that-works-on-both-desktop-and-mobile-pt-1-1n9i>

我是 [Inkdrop](https://inkdrop.app/) 的独立开发者，这是一款面向程序员的减价笔记应用，支持 macOS、windows、linux、android 和 iOS。它是基于桌面平台的 Electron 和移动平台的 React Native 构建的。
正如我在[我们的路线图](https://blog.inkdrop.info/the-roadmap-of-inkdrop-vol-3-a32fc0cc42d)中宣布的那样，我目前正在致力于重建全文搜索功能。
这篇文章是我制作它的工作进度记录。

* * *

为了实现全文搜索功能，我想尝试使用 SQLite3 的 FTS。FTS 是 SQLite3 的全文搜索引擎。有多个版本:FTS3、FTS4 和 FTS5。我要试试 FTS5。

使用 [node-websql](https://github.com/nolanlawson/node-websql) 实现移动端(React Native)和桌面端(electronic)的兼容。
它依赖于 [node-sqlite3](https://github.com/mapbox/node-sqlite3) ，所以它在电子应用程序上运行良好。

首先，我想从 nodejs 中试用这个模块。

## 尝试 FTS5

它就像一个魔咒一样好用！

```
const openDatabase = require('websql')

var db = openDatabase('mydb.db', '1.0', 'description', 1)

db.transaction(txn => {
  txn.executeSql('DROP TABLE IF EXISTS notes_fts')
  txn.executeSql(
    'CREATE VIRTUAL TABLE IF NOT EXISTS notes_fts USING fts5(note_id, body)'
  )
  txn.executeSql(
    'INSERT INTO notes_fts (note_id, body) VALUES (:note_id, :body)',
    [1, 'Hello, world!']
  )
  txn.executeSql(
    'SELECT * FROM `notes_fts` where body MATCH :keyword',
    ['hello OR hoge'],
    function(tx, res) {
      for (let i = 0; i < res.rows.length; ++i) {
        console.log('hit:', res.rows.item(i))
      }
    }
  )

  txn.executeSql(
    'SELECT * FROM `notes_fts` where body MATCH :keyword',
    ['worl*'],
    function(tx, res) {
      for (let i = 0; i < res.rows.length; ++i) {
        console.log('hit2:', res.rows.item(i))
      }
    }
  )
}) 
```

支持正向匹配，`OR`运算符，甚至`NOT`运算符...太棒了。

## 移动平台怎么样？

### 安卓默认不支持 FTS5

似乎它需要安装[requery/SQLite-Android:Android SQLite 支持库](https://github.com/requery/sqlite-android)。
但是根据这个页面: [sqlite - SQLite3 FTS4，MATCH 和 Android - Stack 溢出](https://stackoverflow.com/questions/6339022/sqlite3-fts4-match-and-android)
FTS4 从`API >= 11`开始就可以用了。
确认它在模拟器中工作！

### iOS 默认支持 FTS5

这是个好消息。根据这篇文章:[在 iOS 上利用 SQLite 全文搜索| Inside PSPDFKit](https://pspdfkit.com/blog/2018/leveraging-sqlite-full-text-search-on-ios/)
从 iOS 11 开始支持 FTS5。

### ft S5 和 FTS4 的区别

> 总的来说，FTS5 模块的性能比它的前辈好得多。然而，与 FTS4 相比，它缺少一些功能，因为提取匹配信息的辅助功能并不完整。像 matchinfo()和 offsets()这样的函数是不存在的，snippet()函数也没有 FTS4 中那么有用。

所以，结论是用 FTS4。但如果可能的话，换成 FTS5 会更好。

## 同步索引与 pouchdb

Inkdrop 的数据库是用 PouchDB 实现的。

使用[改变进给](https://pouchdb.com/guides/changes.html)来同步 FTS 的索引。

先尝试`UPDATE`，如果失败，再尝试`INSERT`。

```
txn.executeSql(
  'UPDATE notes_fts set body = :body where note_id = :note_id',
  ['Hello, world!', 1],
  (txn, res) => {
    if (res.rowsAffected === 0) {
      console.log('The row does not exist. Trying inserting.')
      txn.executeSql(
        'INSERT INTO notes_fts (note_id, body) VALUES (:note_id, :body)',
        [1, 'Hello, world!'],
        (txn, res) => console.log('inserted:', res)
      )
    }
  }
) 
```

或者，最好先用`SELECT`检查该行是否存在，因为`SELECT`查询更小。

* * *

未完待续。