# bug detective——当返回 JSON 出错时

> 原文：<https://dev.to/codingsam/bug-detective-when-returning-json-goes-wrong-283g>

作为一名开发人员，我花了很多时间来修复 bug。如果你是一个软件开发人员，你可以涉及到这一点，对不对？我喜欢创造新事物，开发新功能。但是，有很多天，我的大部分时间都花在了调试上。有些日子真的很难，你必须像一个侦探一样去修复一个 bug。那是你当侦探的日子。我觉得大部分开发商都时不时有这样的日子。

[![The code detective](img/ba682758893b36aaebb45f6256093060.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WzQrb9Na--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0raphcvm8o6zuwfrm0ra.png)

在这篇文章中，我将介绍一个 bug 案例，并解释修复它的过程。这个案例是基于一个真实的问题，过去我和一个同事在开发 PHP 代码库时不得不调试这个问题。我更改了一些变量和函数名，以避免共享私有代码。这种编程语言有很多坏名声，因为很多人用它写了非常糟糕的代码。但是在任何编程语言中都可以这样做，对吗？这个帖子的重点不在 PHP。即使你以前从未用那种编程语言编写过代码，你也可以继续阅读。主要关注点在案件本身，以及寻找原因的过程中。最后，我会给你一些建议来帮助你度过那些侦探的日子。

我们就从案子开始吧！

## 案情

你使用一个 web 应用程序，其中一个页面有一个表格，这对于你的用户使用你的应用程序非常重要。有一天，您收到许多关于这个表根本没有显示任何数据的错误报告。那里没有一排。就像任何阻碍用户的错误一样，你必须马上修复它。

你要做的第一件事就是重现用户描述的行为。你只要这样做，当然，他们是对的。根本没有数据。您检查了浏览器的开发工具，没有一个错误被抛出。您检查了服务器的日志，那里也没有错误。

你开始调查…

## 调查

我们来问第一个问题… *表格是如何工作的，它的数据来自哪里？*

一个 HTTP 请求由 Javascript 中的一些代码触发，发送到用 PHP 编写的端点，它应该返回一个 JSON 数组。该数组包含几个对象，用于在前端“输入”表格的内容。

很简单，对吧？

*端点返回是什么？*我们来检查一下响应，可能是返回一个空数组……但不是。*的回应完全是空洞的*。不是空数组。响应主体中没有任何内容，但其状态代码是一个 *200 OK* 。现在开始变得很奇怪。没有一个错误，也不是一个空数组。怎么可能没有抛出错误，却得到一个空的响应体呢？服务器上发生了非常奇怪的事情。

服务器端点的代码看起来像什么？

```
<?php

$data = getDataFromDatabase();

echo json_encode($data);

?> 
```

对于那些从未见过 PHP 代码的人来说，初始化变量的方式是这样的:

```
$variableName = value; 
```

让我们来分解一下:

*   *getDataFromDatabase* 是项目代码库中的一个函数，它向数据库发送查询，并返回关联数组的[数组](https://www.w3schools.com/php/php_arrays.asp)(键-值结构，类似于 PHP 中的 Javascript 对象)。

*   *[json_encode](https://www.php.net/manual/en/function.json-encode.php)* 是一个内置函数，由编程语言提供，将一个关联数组或一组关联数组转换成 json。

*   *[echo](https://www.php.net/manual/en/function.echo.php)* 是一个众所周知的 PHP 构造，它只输出一个或多个字符串。在这种情况下，它以 JSON 格式输出 *$data* 数组，因为有 *json_encode* 调用。如果一切顺利，您将从 *$data* 变量中获得一个 JSON 数组。

在这一点上，我要求你稍微休息一下，想想是什么导致这个端点不返回任何东西。

你想过吗？

你需要问自己 getDataFromDatabase 函数有什么问题吗？是否有任何连接到数据库的错误被捕获而没有被抛出给调用者？

让我们试试自上而下的方法。这意味着，让我们从顶层开始，在这种情况下是端点的代码，如果这里有问题，您需要查看一下 *getDataFromDatabase* 函数的内部。因为这是你唯一可以看的函数。 *json_encode* 是 PHP 内置函数。它应该工作正常。让我们来看看 *$data* 变量的内容。为此，您只需调用 *[var_dump](https://www.php.net/manual/en/function.var-dump.php)* ，这是一个转储给定变量内容的函数。

```
<?php

$data = getDataFromDatabase();

// Let's output the $data variable's content
var_dump($data);

echo json_encode($data);

?> 
```

更改之后，您的响应看起来会很奇怪，因为您正在转储变量的内容，这是意料之中的。当 *var_dump* 执行时，你可以看到一个实际的数组，里面有很多关联数组。这个 *getDataFromDatabase* 函数似乎工作得很好，对吗？

现在，唯一的解释是，调用 *json_encode* 函数有问题。但它是一个内置函数。它应该像预期的那样运行。

在这一点上，你认为我撒谎了，这一切都是假的，我真的明白。但是，正如我在这篇文章的开头告诉你的，这个案例是基于一个我必须调试的真实问题。类似的事情确实发生在我身上。我最后问自己…

*“有没有可能是 json_encode 这样的内置函数工作不正常？”*

还有… *WTF* ？

这是一个你花了很多时间试图解决的问题，你越深入了解它，你就越说" *WTF* ？"。你开始变得非常绝望，开始在谷歌上搜索你的问题。在此之前，我们先来看看 [PHP 网站](PHP%20website)，搜索“ *[json_encode](https://www.php.net/manual/en/function.json-encode.php)* ”。“返回值”部分有一些真正有趣的东西:

*"成功时返回 JSON 编码的字符串，失败时返回`FALSE`。"*

因为端点没有返回任何东西，所以 *json_encode* 函数一定是失败了，并且返回了`false`。让我们修改代码来调用 *var_dump* ，看看 *json_encode* 返回的是什么:

```
<?php

$data = getDataFromDatabase();

// Let's output the $data variable's content
var_dump($data);

// Let's output the result of calling json_encode
$encoded = json_encode($data);

var_dump($encoded);

echo $encoded;

?> 
```

您将在端点的响应中看到转储 *$data* 变量和另一行`FALSE`的输出。没错，您的 HTTP 端点代码没有返回任何内容，因为 *json_encode* 函数失败，当您将`FALSE`传递给 echo 时，它没有输出任何内容。有很多原因可以解释这种失败的原因。当我遇到这种情况时，我的一些关联数组有一些`NaN`(不是数字)值。看起来它不能被编码成 JSON。

当 *json_encode* 函数失败时我们如何得到错误？

*   将`JSON_THROW_ON_ERROR`选项传递给函数

*   调用 *[json_last_error](https://www.php.net/manual/en/function.json-last-error.php)* 函数，从最后一次 json_encode 调用中获取实际错误

*   调用*[JSON _ last _ error _ msg](https://www.php.net/manual/en/function.json-last-error-msg.php)*函数，从上次 json_encode 调用中出现的错误中获取错误消息

这个错误的原因是什么？您将不得不查看实际创建您从 *getDataFromDatabase* 函数获得的数据的代码。如果抛出一个错误，就可以避免花费这些时间去试图理解为什么端点的响应是空的。我认为大多数时候，开发人员不会对错误想太多，或者他们试图编写无声失败的代码。这是一个很好的例子，说明为什么在大多数情况下，最好的解决方案是抛出一个错误。你的用户会得到它，但你可以更快地修复它，并开始寻找正确的地方。

## 这其中有哪些可借鉴之处？

有时候，我花很长时间调试。我想大多数开发人员都会有同感。我在这篇文章中告诉你的故事是基于一个我实际上必须解决的问题，那就是阻止用户。你可以在大部分侦探生涯中获得一些有用的信息:

*   *使用自上而下的方法*。这意味着，您开始查看似乎是问题所在的最高级代码。在这种情况下，您可以避免查看 *getDataFromDatabase* 函数内部，因为您可以开始查看端点代码本身。如果你先看看函数的内部，你会在整个调查中花费更多的时间

*   *看文档*。如果你使用一些内置函数，你不能假设它们总是以正确的方式工作，永远不会有错误。试着理解它们是如何工作的，以及如何进行正确的错误处理

*   *避免编写无声失败的代码*。如果有些事情不应该失败，不要认为它永远不会发生，你不需要做任何事情。编写得到错误的代码，如果你不知道如何处理它，就抛出它。如果出现问题，您的用户会受到影响，但是您可以更快地解决问题

*   *大多数时候，bug 不在你认为的地方*。这与前面的项目符号一致。如果你抛出错误，而不是认为它们永远不会发生，你可能会在调查开始时就开始寻找正确的地方

*   休息一下，喝点水或吃点东西。你将需要额外的能量。当你的用户和你的老板因为某些东西不工作而对你大喊大叫的时候，这可能会很难。但是真的，试着去做。否则你的大脑将会“爆炸”,你将会花费更多的时间来解决问题。

我希望你喜欢这个故事，并从中获得一些对你作为软件开发人员的生活有帮助的东西。特别是在那些你必须修复非常奇怪的错误的日子里。

编码快乐！:)

让我知道你的想法，并关注我以获得更多关于开发人员的精彩内容:)

[codingsam @ Twitter](https://twitter.com/codingsam01)