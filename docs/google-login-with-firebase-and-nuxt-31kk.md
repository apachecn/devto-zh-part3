# 用 Firebase 和 Nuxt 登录 Google

> 原文：<https://dev.to/thomas_ph35/google-login-with-firebase-and-nuxt-31kk>

想在几分钟内给你的应用添加一个“用谷歌登录”按钮吗？下面是我是如何做到的！

# 环境

*   节点@11.0.0
*   Vue @3.0.4
*   Npm @6.9.0
*   Nuxt @2.6.3
*   Vuetify @1.3.3

## 文档

nuxt:[https://nuxtjs.org/](https://nuxtjs.org/)T2[https://firebase.google.com/docs/](https://firebase.google.com/docs/)

# Firebase 安装

要将 firebase 安装到您的 Nuxt 项目中，只需运行命令

```
npm install firebase --save 
```

并在您的`pulgins`目录中创建一个类似于`firebase.js`的文件。

转到 Firebase 控制台，创建一个新项目。注册您的项目后，您将能够找到您的 firebase 配置对象。

这里怎么找:[http://support.google.com/firebase/answer/7015592](http://support.google.com/firebase/answer/7015592)T3【我们的 app 类型是`web app`)

现在你有了你的 firebase 配置对象，让我们把它注册到你的 nuxt 插件中！

```
// /plugins/firebase.js
import firebase from 'firebase'

let firebaseConfig = {
  apiKey: "api-key",
  authDomain: "project-id.firebaseapp.com",
  databaseURL: "https://project-id.firebaseio.com",
  projectId: "project-id",
  storageBucket: "project-id.appspot.com",
  messagingSenderId: "sender-id",
  appID: "app-id",
}

let app = null
if (!firebase.apps.length) {
  app = firebase.initializeApp(config)
}

export const db = app.database() 
```

不要忘记将`'~/plugins/firebase.js'`添加到`plugins`部分的`nuxt.config.js`中。

你就可以出发了！

> 这时你仍然需要进入页面。

# 用弹出框登录

在这个例子中，我将使用 firebase 提供的`signInWithPopup()`方法

## 按钮

```
<v-btn outline fab @click="googleSignIn" color="#4285F4"><v-icon>fab fa-google</v-icon></v-btn> 
```

## 法

```
 googleSignIn () {
        this.provider = new firebase.auth.GoogleAuthProvider()
        firebase.auth().signInWithPopup(this.provider).then(result => {
          // store the user ore wathever
          this.$router.push('/home')
        }).catch(e => {
          this.$snotify.error(e.message)
          console.log(e)
        })
      } 
```

> 用户信息存储在`result.user`中。由你来存储主题在你想去的地方！

## 干得好！👋

就是这样！如果你的 firebase 应用程序注册正确，你应该可以获取你的谷歌账户信息！

让我知道你是否有困难，如果我犯了任何错误，我还在学习！