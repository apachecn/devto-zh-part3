# 在 Firebase 中使用 Kotlin 扩展函数和协同程序

> 原文：<https://dev.to/rosariopfernandes/using-kotlin-extension-functions-and-coroutines-with-firebase-j0k>

*葡萄牙语版于[developingwith.firebaseapp.com](https://developingwith.firebaseapp.com/Kotlin-Features-Firebase/)T3】上市*

> "代码越简单，你理解得越快."——
> [保罗·伊诺克](https://medium.com/@pauloenoque2014)

Kotlin 的简单明了是开发人员在工作中采用这种语言的主要原因。
我还必须提醒你，2017 年 5 月，谷歌宣布 Kotlin 成为 Android 应用开发的官方语言。从那时起，Kotlin 开发人员的数量一直在增加。

* * *

在 Kotlin 成为 Android 开发的官方语言一年半之后，Firebase 将这种语言添加到了他们的官方文档中:

> Android 的@Firebase 代码样本开始获得 Java 的 [@kotlin](https://twitter.com/kotlin?ref_src=twsrc%5Etfw) 样本！感谢@daggerdwivedi 和 [@_rpfernandes](https://twitter.com/_rpfernandes?ref_src=twsrc%5Etfw) 在这里的助攻。总的来说，这是一项巨大的努力，能够得到社区的帮助真是太好了。[https://t.co/PMlAViMf9F](https://t.co/PMlAViMf9F)[#安卓开发](https://twitter.com/hashtag/AndroidDev?src=hash&ref_src=twsrc%5Etfw)[pic.twitter.com/W386VlM3HC](https://t.co/W386VlM3HC)
> 
> — Doug Stevenson 🔥 (@CodingDoug) [October 11, 2018](https://twitter.com/CodingDoug/status/1050473809994629120?ref_src=twsrc%5Etfw)

但我必须同意流行的说法“迟到总比不到好”。这一变化为平台带来了许多改进:

*   为了更好地与 Kotlin 互操作，改进了 Firebase Android SDK 上的 Java 代码；
*   创建“扩展功能”是为了让 Android SDK 的使用更加简洁；
*   一些开发人员创建了库来改进 Firebase 与 Kotlin 一起使用的方式，等等。

我决定在 Kotlin 中创建一个库来帮助人们使用 Firebase，它被称为[fire extensions](https://github.com/rosariopfernandes/fireXtensions)(现在已被否决)，它曾经为 Firebase Android SDK 提供一些**扩展功能**。

# Kotlin 扩展函数

简而言之，扩展函数是 Kotlin 的一个特性，它允许你向一个类添加新的方法/函数，即使这个类不是你创建的。不需要实现类，也不需要扩展它。

在将 Kotlin 添加到他们的官方文档中之后，Firebase 团队也在他们的 Android SDK 中添加了一些扩展功能(因此不支持 fireXtensions)。

第一个扩展是库 **Common KTX** 和 **Firestore KTX** 。

## 普通 KTX

`firebase-common-ktx`模块包含用于获取 Firebase 实例的扩展函数。如果你曾经使用过 Firebase(Java 或 Kotlin)，你可能对这个方法/函数`FirebaseApp.getInstance()`很熟悉。如果使用`firebase-common-ktx`模块，调用这个方法会变得更容易:`Firebase.app`。

为了使用这个模块，请确保您的`build.gradle(project)`文件中有 Kotlin 插件 1.3.20 或更高版本:

```
dependencies {
    classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.3.20"
} 
```

然后将下面的依赖项添加到您的`build.gradle(app)` :

```
dependencies {
    // ... Other dependencies ...
    implementation 'com.google.firebase:firebase-common-ktx:16.1.0'
} 
```

参见[可用扩展功能](https://firebaseopensource.com/projects/firebase/firebase-android-sdk/docs/ktx/common.md/)的完整列表。

## Firestore KTX

顾名思义，该模块包含简化 Firestore Android SDK 使用方式的扩展。这个模块可以像我们使用 Common KTX 一样添加到你的项目中，但是使用这个依赖项:

```
dependencies {
    // ... Other dependencies ...
    implementation 'com.google.firebase:firebase-firestore-ktx:18.2.0'
} 
```

参见[可用扩展功能](https://firebaseopensource.com/projects/firebase/firebase-android-sdk/docs/ktx/firestore.md/)的完整列表。

注意，当使用`firebase-firestore-ktx`依赖项时，不再需要使用`com.google.firebase:firebase-firestore`。

虽然这些是目前唯一可用的模块，但我非常肯定会有更多的模块添加到他们的 SDK 中。每次添加新模块时，我都会更新这篇文章。

# 科特林合唱团

您可能已经知道，[Firebase API 是异步的](https://medium.com/google-developers/why-are-firebase-apis-asynchronous-callbacks-promises-tasks-e037a6654a93)，迫使我们在代码中使用监听器从数据库中读取数据。程序员经常试图(错误地)在没有侦听器的情况下读取数据，或者试图在这些侦听器之外使用数据，就像这样:

**例 1:** 读取 Firestore 中存储的所有用户。

```
// What we would like to do
// (PS: This code doesn't work)
val db = FirebaseFirestore.getInstance()
var users = db.collection("users").get()
updateUI(users)

// (This code doesn't work either)
// What some people do (wrongly)
var users: List<User>()? = null
usersRef.get().addOnSuccessListener { querySnapshot ->
    users = querySnapshot.toObjects(User::class.java)
}
if (users == null) {
    displayError()
    // displayError() will always be called because
    // the users list is loaded asynchronously.
    // The if is executed while the list hasn't been loaded yet.
} else {
    updateUI(users)
}

// What they should do:
usersRef.get()
    .addOnSuccessListener { querySnapshot ->
        val users = querySnapshot.toObjects(Userr::class.java)
        updateUI(users)
        // The method is now being called after
        // loading the users list.
    }.addOnFailureListener { e ->
        displayError()
    } 
```

看着上面的代码，您可能会认为侦听器没有任何问题，因为一切都有意义，甚至可以保持代码有组织。但是，如果您需要从不同的集合中读取数据，并在显示在应用程序 UI 上之前合并结果，该怎么办呢？这将迫使我们使用嵌套的监听器，这并不容易阅读，我们可以看到如下:

**示例 2:** 加载 John 的个人资料和他的朋友列表(该列表存储在不同的集合下)。

```
// Allow me to send a shot out to all
// JavaScript Developers who have been to callback hell
usersRef.document("john").get().addOnSuccessListener { querySnapshot ->
            val johnUser = querySnapshot.toObject(User::class.java)

            friendsRef.get().addOnSuccessListener { friendSnapshot ->
                val friends = friendSnapshot.toObjects(Friend::class.java)
                showProfileAndFriends(johnUser, friends)
            }.addOnFailureListener {
                displayError()
            }

        }.addOnFailureListener { e ->
            displayError()
        } 
```

协程程序已经开始改变这种状况。它们允许您编写异步代码，就像它是同步的一样，使它更简洁，更易于阅读。
为了在您的 Android 项目中使用协同程序，请确保您使用的是 1.3.x 或更高版本的 Kotlin 插件，然后将以下依赖项添加到您的 build.gradle(app)文件中:

```
dependencies {
    // ... Other Dependencies ...
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.1.1'
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-play-services:1.1.1'
} 
```

截至本文发布时，1.1.1 是最新版本。你可以查看 [maven 仓库](https://mvnrepository.com/artifact/org.jetbrains.kotlinx/kotlinx-coroutines-play-services)中最新版本的 kot linx-coroutines-play-services。

现在，如果我们在第一个例子中使用协程，它将变成:

```
try {
    val snapshot = usersRef.get().await()
    val users = snapshot.toObjects(User::class.java)
    updateUI(users)
} catch (e: FirebaseFirestoreException) {
    displayError()
} 
```

它类似于示例 1 中的“我们想做什么”代码，不是吗？

现在我们的**第二个例子** :

```
try {
    val querySnapshot = usersRef.document("john").get().await()
    val johnUser = querySnapshot.toObject(User::class.java)

    val friendSnapshot = friendsRef.get().await()
    val friends = friendSnapshot.toObjects(Friend::class.java)
    showProfileAndFriends(johnUser, friends)
} catch (e: FirebaseFirestoreException) {
    displayError()
} 
```

代码现在看起来是同步的，更容易阅读，对吗？

* * *

仅此而已。我希望这些 Kotlin 特性对你有用，我也希望它们能提高你开发 firebase 应用程序的效率。

如果你有任何疑问或建议，请在下面的评论中留下。

如果你在尝试使用 Kotlin 扩展或协程时遇到了问题，你可以把它发布到 [StackOverflow](https://pt.stackoverflow.com/questions/ask?tags=firebase) 上，解释你做了什么以及遇到了什么错误。我相信你会找到帮助的(无论是我还是 Firebase 社区的人)。