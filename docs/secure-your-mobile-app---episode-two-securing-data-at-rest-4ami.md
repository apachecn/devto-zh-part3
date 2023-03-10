# 保护您的移动应用—第二集(保护静态数据)

> 原文：<https://dev.to/progress/secure-your-mobile-app---episode-two-securing-data-at-rest-4ami>

无论您是开发传统的本机应用程序，还是使用 Appcelerator 或 Xamarin 等交叉编译的应用程序，使用 Ionic 的混合应用程序，或者使用 NativeScript 或 React Native 的 JavaScript-本机应用程序，贯穿其中的一个共同主题是应用程序安全性。

我认为，我们开发人员比以往任何时候都更加意识到我们面临的无数安全问题。当你用 NativeScript 开发一个跨平台的移动应用时，你就是在开发一个真正的原生应用。但这也意味着同样的安全考虑适用于任何其他原生移动应用程序。

> 观看网络研讨会[保护您的移动应用的最佳实践](https://www.progress.com/campaigns/kinvey/best-practices-for-securing-your-mobile-apps),获取一些关于 NativeScript 安全性的提示和技巧！

在上一篇文章的[中，我们深入探讨了如何通过高级模糊处理来保护我们的源代码，防止代码篡改，缩小我们的安装范围，以及将敏感的业务逻辑迁移到云中。](https://dev.to/progress/secure-your-mobile-app---episode-one-protecting-the-code-58e)

今天，我们的重点是如何在本地存储(和保护)数据。所以让我们开始吧！

*   **第一部分:** [保护你的源代码](https://dev.to/progress/secure-your-mobile-app---episode-one-protecting-the-code-58e)
*   **第二部分:**保护静态数据(就是今天！)
*   **第三部分:** [确保设备和服务器之间的数据完整性](https://dev.to/progress/secure-your-mobile-app---episode-three-securing-data-in-transit-491f)
*   **第四部分:** [企业用户认证和授权](https://dev.to/progress/secure-your-mobile-app---episode-four-secure-user-auth-5ep1)

> 查看来自[NativeScripting.com](https://nativescripting.com/course/securing-nativescript-applications)的关于移动应用安全的新课程，并通过代码:NSSECURE 获得 30%的折扣。

## 加密本地数据

iOS 和 Android 都可以防止一个应用程序存储的数据被系统中的任何其他应用程序访问。然而，众所周知通往地狱的道路是由良好的意愿铺成的，阿米利特？🔥😰

因此，最好对我们保存到设备上的所有数据进行加密。

幸运的是，[native script-secure-storage](http://market.nativescript.org/plugins/nativescript-secure-storage)插件已经存在！

安全存储插件允许我们加密、保存、解密和检索密钥/值对:

```
// require the plugin
import { SecureStorage } from "nativescript-secure-storage";

// instantiate the plugin
let secureStorage = new SecureStorage();

// async
secureStorage.set({
  key: "foo",
  value: "I was set at " + new Date()
}).then(success => console.log("Successfully set a value? " + success));

// sync
const success = secureStorage.setSync({
  key: "foo",
  value: "I was set at " + new Date()
}); 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**在内部，在 iOS 上这个插件通过[的 SAMKeychain 库](https://github.com/soffes/SAMKeychain)使用 KeyChain，通过[的 Hawk 库](https://github.com/orhanobut/hawk)使用 Android(反过来使用[的脸书隐藏](https://github.com/facebook/conceal))。

## 用 SQLite + SQLCipher 加密

你是 [SQLite](https://www.sqlite.org/index.html) 的粉丝吗？你知道有一个支持 SQLite 的[全功能 NativeScript 插件](https://market.nativescript.org/plugins/nativescript-sqlite)吗？现在你知道了！

SQLite 插件的免费版本(如上)提供了您期望从 SQLite 获得的所有功能。然而，有一个[付费选项](https://nativescript.tools/product/10)也包括加密您的静态 SQLite 数据库。通过利用 [SQLCipher](https://www.zetetic.net/sqlcipher/) ，您可以在您的用户设备上对 SQLite 数据库进行透明的 256 位 AES 加密。

[![sqlite and sqlcipher](img/a0b77690bfe9b1cf970d7e94713bf5e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JecKp5pg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/securing-app/2-sqlcipher.png)

## 在线/离线数据同步+加密

我们中的许多人使用移动后端服务(mBaaS ),如 [Firebase](https://market.nativescript.org/plugins/nativescript-plugin-firebase) 或 [Progress Kinvey](https://www.progress.com/kinvey) 用于我们的远程后端。当开发移动应用程序时，我们需要了解在线/离线连接，并在用户在这些状态之间切换时同步数据(以免应用程序在没有网络连接的情况下崩溃！).

开箱即用，Kinvey 自带内置的[在线/离线数据同步](https://devcenter.kinvey.com/nativescript/guides/datastore)，如文档中的大量代码示例所示:

```
// Retrieve an instance
const dataStore = Kinvey.DataStore.collection('books', Kinvey.DataStoreType.Sync) as Kinvey.SyncStore;
// Pull data from the backend and save it locally on the device.
const promise = dataStore.pull()
  .then((entities: Array<{}>) => {
    // ...
  })
  .catch((error: Kinvey.BaseError) => {
    // ...
  });
// Find data locally on the device.
const subscription = dataStore.find()
  .subscribe((data: Array<{}>) => {
    // Called once, with local data
  }, (error: Kinvey.BaseError) => {
    // ...
  }, () => {
    // Called after the local data has been retrieved
  });
// Save an entity locally to the device. This will add the item to the sync table to be pushed to the backend at a later time.
const entity = {};
const promise = dataStore.save(entity)
  .then((entity: {}) => {
    // ...
  })
  .catch((error: Kinvey.BaseError) => {
    // ...
  });
// Syncs this store with the backend. This will first push any pending changes on the device to the backend and then pull data from the backend onto the device.
const promise = dataStore.sync()
  .then((entities: Array<{}>) => {
    // result will contain the results of the push to the backend and a pull from the backend
    // result = {
    //   push: [], // pushed entities
    //   pull: [] // pulled entities
    // };
    //
    // Each item in the array of pushed entities will look like the following
    // { _id: '<entity id before push>', entity: <entity after push> }
    // It could also possibly have an error property if the push failed.
    // { _id: '<entity id before push>', entity: <entity after push>, error: <reason push failed> }
  })
  .catch((error: Kinvey.BaseError) => {
    // ...
  }); 
```

Enter fullscreen mode Exit fullscreen mode

此外， [Kinvey 使用 SQLite 和 SQLCipher 对设备上的静态数据](https://devcenter.kinvey.com/nativescript/guides/encryption)进行加密，这可以在初始化 Kinvey 时自动配置:

```
Kinvey.init({

    appKey: '<appKey>',

    appSecret: '<appSecret>',

    encryptionKey: '<encryptionKey>'

}); 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
后端合规和安全注意事项

[![hipaa compliance and kinvey](img/4173b0e0412ef42b25081c61cf81a870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_DfieI47--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/securing-app/2-hipaa.png)

我们许多在企业中开发应用程序的人都非常清楚合规性和安全法规。在美国，为医疗保健提供商或保险公司开发应用程序的开发者面临的一大挑战是 HIPAA。

> 如果您正在开发一个处理医疗保健行业 PHI(私人健康信息)的应用程序，您绝对需要确保数据存储和传输符合 HIPAA 标准。

Kinvey 通过 SOC2、HIPAA、GDPR、萨班斯-奥克斯利法案和其他合规活动，每年审查、确认和改进安全控制措施。对于关注 FFIEC 或 GLBA 法规的银行业客户、关注 HIPAA 的医疗保健行业客户、在欧盟开展业务并关注 GDPR 的客户，Kinvey 平台提供了全面的端到端安全性，以及支持您的法规遵从性工作所需的功能。

[点击此处阅读更多信息](https://www.progress.com/kinvey/enterprise-security)了解 Kinvey 如何为您的组织提供所需的安全性和合规性保障。

## 接下来是第三集:保护传输中的数据！

今天，我们讨论了如何在我们的应用中安全地存储私有数据元素，甚至研究了一些本地和远程安全数据存储选项。接下来，我们将研究如何在客户端和服务器之间安全地来回传输数据。提示:这不是*SSL 那么简单。🤔*