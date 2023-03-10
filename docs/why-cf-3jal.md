# 为什么我用 Coldfusion 编程

> 原文：<https://dev.to/mikeborn/why-cf-3jal>

我被问了很多次。你为什么要用 Coldfusion 编程？cold fusion[*不是死了*](https://mrphilmunro.wordpress.com/2014/05/05/is-coldfusion-dead/) 吗？不是*闭源*吗？不是*贵*吗？

1.  不，Coldfusion 是[不是](https://blog.reybango.com/2006/10/26/is-coldfusion-really-dead/) [死了](https://wris.com/blog/2018/04/04/coldfusion-is-not-dead)。
2.  不，我更喜欢使用开源的 Coldfusion 引擎。
3.  不，不贵。(见上。)Lucee 免费！(如在啤酒中。)

简而言之，我使用 Coldfusion 是因为它既简单又强大。

## 什么是 Coldfusion？

到底什么是 Coldfusion？Coldfusion 是一种运行在 Java 虚拟机(JVM)上的编程语言，这意味着它可以编译成 Java，并且可以在 Java 可以运行的任何地方运行。(Windows、Mac 和 Linux。)Coldfusion 很酷，因为它内置了许多高级工具，使得完成工作变得*超级*容易。您可以索引和搜索文档或网页，创建电子表格和 pdf，通过 IMAP 或 POP 或 FTP 或 LDAP 连接到服务器——这种语言内置了大量功能，无需安装任何库或扩展。

当大多数人想到 Coldfusion 时，他们会想到他们在 05 年使用的带有`<cfdiv>`和`<cftable>`的版本以及所有这些好东西。严肃点。那种旧垃圾已经被人厌恶很久了。真正的 Coldfusion 是一种类似 JS 的语法，具有直接来自 Java 的完全面向对象的功能——`final`和`static`函数、访问器等——以及函数式编程特性，如线程、异步和锁定。对我来说，这就是 Coldfusion 的真正含义。

## [T1】cold fusion 超级简单](#coldfusion-is-super-easy)

有人能告诉我这是什么吗？

```
var ducks = [{name: "Donald"},{name: "Daffy"}];
var names = [];
for( var duck in ducks ) {
    names.append( duck.name );
} 
```

不，不是 Javascript——那是 Coldfusion 的脚本语法。如果你熟悉 JS，你应该能学会 Coldfusion，没问题。

还有一种完全不同的语法，与 HTML 标记风格非常相似。对于那些知道 HTML 但不知道 JS 的开发人员(很可能是一群越来越少的人)，您至少可以选择开始使用这种标记语法。仅此一点就让新开发人员很容易开始使用 CF——汉密尔顿学院提到了这种新开发人员入职心理学，这显然奏效了——新学院网站[去年获得了威比奖](https://www.rasia.io/blog/case-study-hamilton-college.html)。

## Coldfusion 超级强大

几年前，我用 PHP 和 Bolt CMS 为一个画家建了一个网站。客户的另一个要求是在网站上提供的每张图片中嵌入水印。尽管我可能会尝试，但我无法在客户的最低预算内完成这项工作——PHP 的图像处理功能在没有像 GD 这样的附加扩展的情况下是很少的。

在 CF 中，有一个函数允许这样做:`imagePaste()`。取一个水印图像并粘贴到第二个图像的左上角。将图像写入文件。完成了。

```
var watermark = imageRead( "images/myBrand.jpg" );
var thePhoto = imageRead( "images/upload.jpg" );
imagePaste( thePhoto, watermark, 0, 0);
imageWrite( thePhoto, "images/final/upload.jpg" ); 
```

这没什么大不了的——这只是 Coldfusion 原生支持的简单代码，不需要外部库。

以下是如何在 Coldfusion 中进行 HTTP 调用。

```
cfhttp( url="https://api.chucknorris.io/jokes/random" ) {} 
```

一行代码。Uno。就是这样。JSON 存储在`cfhttp.fileContent`中。不需要额外的库——没有 jQuery，没有 Axios，也没有奇怪的函数名(_cough [`file_get_contents()`](https://stackoverflow.com/questions/959063/how-to-send-a-get-request-from-php_) )。

不服气？看看这个。在 Coldfusion 中，发送电子邮件是一个单一的功能。

```
cfmail( to="me@example.com", from="test@you.com", subject="This is an email" ) {
    writeOutput('Hello from your website!');
} 
```

一行。(加上邮件正文。)需要加个附件？那是两行。

```
cfmail( to="me@example.com", from="test@you.com", subject="Daily Report" ) {
    cfmailparam( file="/assets/generated/report.pdf" );
} 
```

为了比较起见，[下面是用 PHP 发送电子邮件的方法](https://www.tutdepot.com/php-e-mail-attachment-script/)。听着，我不是来抨击 PHP 的。很多。:)我知道，一旦你选择了图书馆，它就能做很多事情。我的观点是 CF 在大多数情况下不需要第三方库。

## 为什么不是 Coldfusion？

Coldfusion 不只是“另一种”编程语言，它是一种简单、超级强大的语言，具有熟悉的语法、出色的 Java 集成和简单明了的配置。为什么是 CF？良好的...为什么不呢？