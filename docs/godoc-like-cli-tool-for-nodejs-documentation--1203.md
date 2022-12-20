# 用于 node.js 文档的 godoc like cli 工具

> 原文：<https://dev.to/anikhasibul/godoc-like-cli-tool-for-nodejs-documentation--1203>

你好，
这是一个用于 nodejs 文档的工具。[https://github.com/AnikHasibul/jsdoc](https://github.com/AnikHasibul/jsdoc)

[![Image from https://www.conetix.com.au/blog/a-closer-look-at-go-and-nodejs](img/d7b63b2078174b55ace332c893a57003.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0iI_CxDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/izqmebdkue5xftpgoevr.jpg)

我们都知道，找出一个 nodejs 模块的方法和功能有多难。
我们也知道在 [Golang](https://golang.org) 有多容易。就这么简单`go doc fmt`

但是和其他开发者一样，我总是试图转储(`console.log`)一个对象来了解这个对象。而 [jsdoc](https://github.com/AnikHasibul/jsdoc) 也是这么做的！；)

这里有几个 jsdoc 的例子:

从`console`模块中列出对象。

```
$ jsdoc console
Console {
  log: [Function: bound consoleCall],
  debug: [Function: bound consoleCall],
  info: [Function: bound consoleCall],
  dirxml: [Function: bound consoleCall],
  warn: [Function: bound consoleCall],
  error: [Function: bound consoleCall],
  dir: [Function: bound consoleCall],
  time: [Function: bound consoleCall],
  timeEnd: [Function: bound consoleCall],
  timeLog: [Function: bound timeLog],
  trace: [Function: bound consoleCall],
  assert: [Function: bound consoleCall],
  clear: [Function: bound consoleCall],
  count: [Function: bound consoleCall],
  countReset: [Function: bound consoleCall],
  group: [Function: bound consoleCall],
  groupCollapsed: [Function: bound consoleCall],
  groupEnd: [Function: bound consoleCall],
  table: [Function: bound consoleCall],
  Console: [Function: Console],
  profile: [Function: profile],
  profileEnd: [Function: profileEnd],
  timeStamp: [Function: timeStamp],
  context: [Function: context],
  [Symbol(kBindStreamsEager)]: [Function: bound ],
  [Symbol(kBindStreamsLazy)]: [Function: bound ],
  [Symbol(kBindProperties)]: [Function: bound ],
  [Symbol(kWriteToConsole)]: [Function: bound ],
  [Symbol(kGetInspectOptions)]: [Function: bound ],
  [Symbol(kFormatForStdout)]: [Function: bound ],
  [Symbol(kFormatForStderr)]: [Function: bound ],
  [Symbol(counts)]: Map {},

  [Symbol(kGroupIndent)]: '' } 
```

从`http`包的`METHODS`数组中列出项目:

```
$ jsdoc http.METHODS
[ 'ACL',
  'BIND',
  'CHECKOUT',
  'CONNECT',
  'COPY',
  'DELETE',
  'GET',
  'HEAD',
  'LINK',
  'LOCK',
  'M-SEARCH',
  'MERGE',
  'MKACTIVITY',
  'MKCALENDAR',
  'MKCOL',
  'MOVE',
  'NOTIFY',
  'OPTIONS',
  'PATCH',
  'POST',
  'PROPFIND',
  'PROPPATCH',
  'PURGE',
  'PUT',
  'REBIND',
  'REPORT',
  'SEARCH',
  'SOURCE',
  'SUBSCRIBE',
  'TRACE',
  'UNBIND',
  'UNLINK',
  'UNLOCK',
  'UNSUBSCRIBE' ] 
```

我希望你对`jsdoc`工具有所了解。

你可以自己下载这个工具，在 https://github.com/AnikHasibul/jsdoc 的回购协议上画一颗星

谢谢大家！