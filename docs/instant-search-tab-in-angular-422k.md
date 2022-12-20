# 角度的即时搜索选项卡

> 原文：<https://dev.to/nishugoel/instant-search-tab-in-angular-422k>

在这篇博文中，我们将学习使用 Cloud Firestore 的数据在 Angular 中进行即时搜索。因此，让我们首先假设我们实际上希望在浏览器上得到什么样的结果。

我们希望在浏览器上创建一个搜索选项卡，它有一个占位符，上面写着“按名称搜索”。当我们输入一个字符时，我们希望应用程序根据存储在 Firestore 上的数据给出建议。它应该是这样的:

[https://thepractical dev . S3 . Amazon AWS . com/I/o 685 nd 7 twhmhbwneeph 7。PNG](https://thepracticaldev.s3.amazonaws.com/i/o685nd7twhmhbwneeph7.PNG)

为此，让我们假设我们的 Firestore 数据库中有一些数据。要了解如何在 Firestore 数据库中添加数据，请参考我的文章 Firestore with Angular。我们在 Firestore 中的数据如下所示:

[https://thepractical dev . S3 . Amazon AWS . com/I/djqgru 65 trypdvd 7 f 65 u . png](https://thepracticaldev.s3.amazonaws.com/i/djqgru65trypdvd7f65u.png)

我们已经创建了四个包含不同人物详细信息的文档，每个文档都包含该人物的姓名和职业。

我们现在的任务是在浏览器上创建一个搜索选项卡。让我们这样做吧。

[https://gist . github . com/NishuGoel/0 f1 a3 f81d 01 F3 c 65 a50 d 382 cc9 ee 1654](https://gist.github.com/NishuGoel/0f1a3f81d01f3c65a50d382cc9ee1654)

在这段代码中，我们对组件中定义为字符串的属性 searchterm 进行了双向数据绑定。此外，为了记录某处最近键入的值，我们在组件内部取一个变量。

[https://gist . github . com/NishuGoel/103 b 0381 ea 736 d6e 54299 c 93925 a5 e8e](https://gist.github.com/NishuGoel/103b0381ea736d6e54299c93925a5e8e)

我们的下一步是准备搜索框，根据我们输入的字符给出建议。为此，让我们添加一个显示这些建议的列表。现在，这个过程最重要的部分是从 RxJS 库中添加两个主题，即 startAt 和 endAt。

startAt = new Subject()；
endAt = new Subject()；
同样，我们将取两个可观测量来使用被试作为可观测量。

startObs = this . startat . asobservable()；
endObs = this . endat . asobservable()；
现在，由于这个功能调用方法 search()，所以在它内部，我们提到，

search($ event){
this . startat . next(q)；
this . endat . next(q+" \ uf8ff ")；
}
我知道你可能想知道这个长相奇怪的角色是从哪里来的！我也有同样的反应。

[https://thepractical dev . S3 . Amazon AWS . com/I/ntbk 388 a 6 na 9 SZ 6 fhs 03 . JPEG](https://thepracticaldev.s3.amazonaws.com/i/ntbk388a6na9sz6fhs03.jpeg)

在这里了解更多:模式匹配

现在，在生命周期钩子 ngOnInit()中订阅 observables 的值。

ngOnInit(){
observable . combinelatest(this . startobs，this.endObs)。subscribe((值)= > {
this.firequery(值[0]，值[1])。subscribe((names)=>{ this . names = names；
})
})
}
到目前为止，我们的组件看起来是这样的:

[https://gist . github . com/NishuGoel/48 A0 ca 4 E3 a 098 cc 63 AFA 2e 334 B3 c 34 AE](https://gist.github.com/NishuGoel/48a0ca4e3a098cc63afa2e334b3c34ae)

现在让我们更新模板，将结果显示在浏览器上。

[https://gist . github . com/NishuGoel/e6d 30527 ff 240 e2ea 03 ff 547 da 0 b7e 62](https://gist.github.com/NishuGoel/e6d30527ff240e2ea03ff547da0b7e62)

现在使用这个列表，我们使用内插法显示数据，并放置一个结构指令*ngIf，如果字符长度小于 1，它将显示给定的语句。

让我们最后看看我们现在是否得到了想要的结果。

跑发球和发球。

[https://thepractical dev . S3 . Amazon AWS . com/I/vftfzce 9 bk 5 bohl 2 sal 8 . png](https://thepracticaldev.s3.amazonaws.com/i/vftfzce9bk5bohl2sal8.png)

[https://thepractical dev . S3 . Amazon AWS . com/I/qh CGI 9m 8 sb 38 dwiwzjle . png](https://thepracticaldev.s3.amazonaws.com/i/qhcgi9m8sb38dwiwzjle.png)

这是输出，这绝对是我们想要的改进版本。厉害！所以这是关于通过从 Firestore 检索数据在 Angular 中创建一个即时搜索标签。