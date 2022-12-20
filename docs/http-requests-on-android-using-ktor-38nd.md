# Android 上使用 Ktor 的 HTTP 请求

> 原文：<https://dev.to/amrfarid140/http-requests-on-android-using-ktor-38nd>

**这篇博客最初发表在我的网站上:[https://amryousef.me/android-ktor](https://amryousef.me/android-ktor)T3】**

[![Ktor logo](img/63de6ffca80ecbc6d3bec88132924b6b.png "Ktor Logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--QtvahgAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amryousef.me/media/screen-shot-2019-05-02-at-17.08.32.png) 
Ktor 是一个构建异步服务器和客户端的新框架。它是 100% Kotlin，运行在协程上。它支持多平台项目，这意味着你可以将它用于任何针对 Android、iOS 或 Javascript 的项目。在这篇博客中，我们将探索如何使用 Ktor 客户端在 Android 应用程序中发出 HTTP 请求。

## 构建客户端

Ktor 客户端为您提供了一个进行 HTTP 请求的接口，但是，它依赖于一个引擎来执行它的职责。对于 Android，您可以使用以下引擎之一:

*   CIO(基于协程的 I/O ),它没有额外的依赖性，使用协程的能力异步执行请求。它也是唯一与 Ktor WebSockets 兼容的引擎。**注意:不支持 HTTP/2。**

```
// in build.gradle add
implementation "io.ktor:ktor-client-cio:$ktor_version"
// in your code, create the client
HttpClient(CIO) 
```

*   Android 使用通常的 HttpURLConnection 和 ThreadPool 来执行请求。

```
// in build.gradle add
implementation "io.ktor:ktor-client-android:$ktor_version"
// in your code, create the client
HttpClient(Android) 
```

*   依赖于 Square 的 OkHttp 客户端的 OkHttp。

```
// in build.gradle add
implementation "io.ktor:ktor-client-okhttp:$ktor_version"
// in your code, create the client
HttpClient(Okhttp) 
```

## 配置客户端

配置 Ktor 客户端非常简单。它依靠的是所谓的`Features`。你所需要的就是安装你想要的`Feature`。
例如，使用 Gson 添加 json 序列化/反序列化功能。它变得像
一样简单

```
// You can replace CIO with your engine of choice
HttpClient(CIO) {    
    install(JsonFeature) {        
        serializer = GsonSerializer()    
    }
} 
```

**注意:**你将需要包括以下依赖

```
implementation "io.ktor:ktor-client-gson:$ktor_version" 
```

## 配置所有出站请求

Ktor HTTP client 允许您为所有出站请求设置一些默认配置。例如，给每个请求添加一个标题就像
一样简单

```
// You can replace CIO with your engine of choice
HttpClient(CIO) {    
    install(DefaultRequest) {
        headers.append("Accept","application/xml;q=0.9")
        headers.append("Authorization","Bearer abc123")    
    }
} 
```

## 发出请求

最后，要提出请求，你可以做

```
val client = HttpClient(CIO)
client.get<String>("https://eample.com") 
```

这是一个完整的例子，它调用 Github API 来获取公共库名称列表

```
data class Repo(
@SerializedName("full_name") val name: String
)

CoroutineScope(Dispatchers.IO).launch {    
    val client = HttpClient(CIO) {
    install(DefaultRequest) {   
        headers.append("Accept", "application/vnd.github.v3+json")
    }
    install(JsonFeature) {
        serializer = GsonSerializer()
    }
}    
client.get<List<Repo>>("https://api.github.com/repositories").forEach { 
repo -> Log.v("Ktor-Test", repo.name)   
}
client.close()
} 
```