# 破解 PouchDB 以在 React Native 上使用它

> 原文：<https://dev.to/craftzdog/hacking-pouchdb-to-use-on-react-native-1gjh>

[![cover](img/9ab3816c36df192d9a73d9bd05be2a49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eoN_aQlR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q2639n467n60nstp1i60.jpg)

## 更新(2021/02/12)

[![craftzdog image](img/44ad99b67bb5a99eb22364374cf20505.png)](/craftzdog) [## 为 React Native 创建了 PouchDB@7

### 松山拓也 2 月 12 日 4 分钟阅读

#pouchdb #reactnative](/craftzdog/created-pouchdb-7-for-react-native-23g6)

* * *

嗨，我是这里的拓也。

我正在开发一个需要与 CouchDB 同步数据的 React 本机应用程序。
[PouchDB](https://pouchdb.com/) ，一个设计良好的数据库，可以和浏览器用 JavaScript 写的 CouchDB 同步，也可以用在 React Native 上。
我已经构建了 [react-native-sqlite-2](https://github.com/craftzdog/react-native-sqlite-2) 和[pouchdb-adapter-react-native-SQLite](https://github.com/craftzdog/pouchdb-adapter-react-native-sqlite)让它在 RN 上工作。

但是仍然有一些问题需要解决。
例如，[存储附件](https://github.com/stockulus/pouchdb-react-native/issues/68)有个大问题，因为 RN 还不支持`FileReader.readAsArrayBuffer`(不过我希望他们将来会支持)。

似乎这些天来, [pouchdb-react-native](https://github.com/stockulus/pouchdb-react-native) 项目是不活跃的。
所以我一直致力于让 PouchDB 在 RN 上完美运行，并成功做到了这一点。以下是我所做的。

## 如何在 RN 上运行 PouchDB

首先，我将向您展示如何在 RN 上使用 PouchDB。我做了一些基于官方 PouchDB 核心模块的包。
这里是[的一个工作演示应用](https://github.com/craftzdog/pouchdb-react-native-demo)。
我打算在我的制作应用程序 [Inkdrop](https://inkdrop.app/) 中使用它。

### 安装 deps

安装 PouchDB 核心包:

```
npm i pouchdb-adapter-http pouchdb-mapreduce 
```

Enter fullscreen mode Exit fullscreen mode

并为 React Native 安装被黑的包:

```
npm i @craftzdog/pouchdb-core-react-native @craftzdog/pouchdb-replication-react-native 
```

Enter fullscreen mode Exit fullscreen mode

接下来，安装 SQLite3 引擎模块:

```
npm i pouchdb-adapter-react-native-sqlite react-native-sqlite-2
react-native link react-native-sqlite-2 
```

Enter fullscreen mode Exit fullscreen mode

然后，安装一些包到 PouchDB 需要的 polyfill 函数:

```
npm i base-64 events 
```

Enter fullscreen mode Exit fullscreen mode

### 创建聚合填充

制作一个 js 文件，聚合填充 PouchDB 需要的一些函数:

```
import {decode, encode} from 'base-64'

if (!global.btoa) {
    global.btoa = encode;
}

if (!global.atob) {
    global.atob = decode;
}

// Avoid using node dependent modules
process.browser = true 
```

Enter fullscreen mode Exit fullscreen mode

在您的`index.js`的第一行导入它。

### 加载 PouchDB

使`pouchdb.js`像这样:

```
import PouchDB from '@craftzdog/pouchdb-core-react-native'
import HttpPouch from 'pouchdb-adapter-http'
import replication from '@craftzdog/pouchdb-replication-react-native'
import mapreduce from 'pouchdb-mapreduce'

import SQLite from 'react-native-sqlite-2'
import SQLiteAdapterFactory from 'pouchdb-adapter-react-native-sqlite'

const SQLiteAdapter = SQLiteAdapterFactory(SQLite)

export default PouchDB
  .plugin(HttpPouch)
  .plugin(replication)
  .plugin(mapreduce)
  .plugin(SQLiteAdapter) 
```

Enter fullscreen mode Exit fullscreen mode

如果需要其他类似`pouchdb-find`的插件，添加进去就好了。

### 使用 PouchDB

然后，照常使用:

```
import PouchDB from './pouchdb'

function loadDB () {
  return new PouchDB('mydb.db', { adapter: 'react-native-sqlite' })
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我是如何黑掉 PouchDB 的

为了让它在 React Native 上工作，我们需要避免从 PouchDB 核心模块调用`FileReader.readAsArrayBuffer`。
这意味着我们总是用 Base64 处理附件，而不是用`Blob`。
这可以通过[几行代码](https://github.com/craftzdog/pouchdb-react-native/commit/78de4baad85c30d8dc41e0609cff894d44cc6d33)来完成。

### 那里的`readAsArrayBuffer`叫什么

PouchDB 试图为每个文档计算 MD5 摘要，这需要调用`readAsArrayBuffer`。

在`pouchdb-binary-utils/lib/index-browser.js` :

```
72 function readAsBinaryString(blob, callback) {
73   if (typeof FileReader === 'undefined') {
74     // fix for Firefox in a web worker
75     // https://bugzilla.mozilla.org/show_bug.cgi?id=901097
76     return callback(arrayBufferToBinaryString(
77       new FileReaderSync().readAsArrayBuffer(blob)));
78   }
79
80   var reader = new FileReader();
81   var hasBinaryString = typeof reader.readAsBinaryString === 'function';
82   reader.onloadend = function (e) {
83     var result = e.target.result || '';
84     if (hasBinaryString) {
85       return callback(result);
86     }
87     callback(arrayBufferToBinaryString(result));
88   };
89   if (hasBinaryString) {
90     reader.readAsBinaryString(blob);
91   } else {
92     reader.readAsArrayBuffer(blob);
93   }
94 } 
```

Enter fullscreen mode Exit fullscreen mode

从`pouchdb-md5/lib/index-browser.js` :
调用该函数

```
24 function appendBlob(buffer, blob, start, end, callback) {
25   if (start > 0 || end < blob.size) {
26     // only slice blob if we really need to
27     blob = sliceBlob(blob, start, end);
28   }
29   pouchdbBinaryUtils.readAsArrayBuffer(blob, function (arrayBuffer) {
30     buffer.append(arrayBuffer);
31     callback();
32   });
33 } 
```

Enter fullscreen mode Exit fullscreen mode

那么，如何避免呢？

### 存储附件

禁用`pouchdb-core/src/adapter.js`中`getAttachment`方法的`binary`选项，如下:

```
714     if (res.doc._attachments && res.doc._attachments[attachmentId]
715       opts.ctx = res.ctx;
716       // force it to read attachments in base64
717       opts.binary = false;
718       self._getAttachment(docId, attachmentId,
719                           res.doc._attachments[attachmentId], opts, callback);
720     } else { 
```

Enter fullscreen mode Exit fullscreen mode

通过这一更改，您将始终获得以 base64 编码的附件。

### 拉复制

当在`pouchdb-replication/lib/index.js`中从远程数据库获取附件时，我们必须将 blob 转换为 base64，例如:

```
function getDocAttachmentsFromTargetOrSource(target, src, doc) {
  var doCheckForLocalAttachments = pouchdbUtils.isRemote(src) && !pouchdbUtils.isRemote(target);
  var filenames = Object.keys(doc._attachments);

  function convertBlobToBase64(attachments) {
    return Promise.all(attachments.map(function (blob) {
      if (typeof blob === 'string') {
        return blob
      } else {
        return new Promise(function (resolve, reject) {
          var reader = new FileReader();
          reader.readAsDataURL(blob);
          reader.onloadend = function() {
            const uri = reader.result;
            const pos = uri.indexOf(',')
            const base64 = uri.substr(pos + 1)
            resolve(base64)
          }
        });
      }
    }));
  }

  if (!doCheckForLocalAttachments) {
    return getDocAttachments(src, doc)
      .then(convertBlobToBase64);
  }

  return target.get(doc._id).then(function (localDoc) {
    return Promise.all(filenames.map(function (filename) {
      if (fileHasChanged(localDoc, doc, filename)) {
        return src.getAttachment(doc._id, filename);
      }

      return target.getAttachment(localDoc._id, filename);
    }))
      .then(convertBlobToBase64);
  }).catch(function (error) {
    /* istanbul ignore if */
    if (error.status !== 404) {
      throw error;
    }

    return getDocAttachments(src, doc)
      .then(convertBlobToBase64);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

成功了！
这也是我同时制作`@craftzdog/pouchdb-core-react-native`和`@craftzdog/pouchdb-replication-react-native`的原因。
如果你发现他们有任何问题，欢迎提出请求[这里](https://github.com/craftzdog/pouchdb-react-native)。