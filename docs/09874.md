# 在浏览器中存储数据

> 原文：<https://dev.to/ujjwal_kr_gupta/storing-data-in-browser-3ie9>

随着网络技术的进步——现在我们可以制作一个网络应用程序，它将像本地应用程序一样工作。我们可以存储数据，离线工作，发送推送通知等。

在这篇文章中，我将只谈论存储数据。

在浏览器中存储数据有多种方式-

*   本地存储
*   会话存储
*   WebSql
*   索引 b

### 本地存储

localStorage 提供了一种以键和值的形式保存数据的方法。键和值应该是严格的字符串。

例如-

```
//save data - key : 'hello', value : 'world'
        localStorage.setItem('hello','world');

//get data - you can get data by using key.

        localStorage.getItem('hello') 
```

Enter fullscreen mode Exit fullscreen mode

有关本地存储的更多信息，请访问此链接-[https://developer . Mozilla . org/en-US/docs/Web/API/Window/local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)

### [会话存储](#sessionstorage)

sessionStorage 类似于 localStorage，但是存储在 localStorage 中的数据没有过期时间，但是只要浏览器会话结束，sessionStorage 中的数据存储就会过期。

选项卡关闭时，浏览器会话结束。

例如-

```
//save data - key : 'hello', value : 'world'
        sessionStorage .setItem('hello','world');

//get data - you can get data by using key.

        sessionStorage .getItem('hello') 
```

Enter fullscreen mode Exit fullscreen mode

有关本地存储的更多信息，请访问此链接-[https://developer . Mozilla . org/en-US/docs/Web/API/Window/session storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)

### WebSql

WebSql 提供了一种以数据库形式存储数据的方法，这就是关系数据库管理系统。所以你可以写 sql 查询来做任何操作。

听起来很棒，对吧？

尽管 chrome 只支持 WebSql，但由于存在许多问题，WebSql 已被弃用。

问题是-

1.  WebSql 是同步的，这意味着在查询时，它可能会冻结用户界面，直到结果被评估。
2.  它只支持基本数据类型，而用户希望存储 blob、二进制类型等。
3.  WebSql 的大小是有限的，并且非常小，这取决于不同的浏览器。
4.  当数据量很大时，查询会花费更多时间。
5.  WebSql 不是基于事务的。

WebSql 的主要目的是提供一种根据数据库存储数据的方法。它能做这项工作，但由于这些问题，它没有达到标准。

由于它已被否决，我不提供任何例子。我也不会建议使用它。

### 索引编制

IndexedDB 是 WebSql 的下一个版本，它解决了 WebSql 中的所有问题。

让我们看看 indexedDB 的特性

1.  它是异步的，这意味着它不会冻结用户界面——不管运行多大的查询。
2.  它是 NoSql 数据库，支持先进的数据类型，如数组，blob 等。
3.  它允许你索引数据，因此速度非常快。
4.  它是基于交易的。

所以使用 indexed db——你可以以对象的形式存储大量数据(因为它不是 NoSql)。但是随着更多的特性而来的是复杂性:IndexedDB 也是如此。

IndexedDB 为各种需求提供了许多 API。它提供了基于事件的 api，这使得在存储方面很难学习和思考。

关于如何使用 indexedd 的更多信息，请阅读本文-[https://developer . Mozilla . org/en-US/docs/Web/API/IndexedDB _ API/Using _ IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB)

最后，你会发现这个综合体。但是不要担心，有一些库可以使这变得容易。

有些是-

*   https://github.com/ujjwalguptaofficial/JsStore
*   德歇-[https://github.com/dfahlander/Dexie.js/](https://github.com/dfahlander/Dexie.js/)

# 参考文献

*   [https://developer . Mozilla . org/en-US/docs/Web/API/indexed db _ API/Using _ indexed db](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB)
*   [http://jsstore.net/](http://jsstore.net/)
*   [https://developer . Mozilla . org/en-US/docs/Learn/JavaScript/客户端 _ web _ APIs 客户端 _ 存储](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage)