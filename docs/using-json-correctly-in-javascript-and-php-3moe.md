# 在 JavaScript 和 PHP 中正确使用 JSON

> 原文：<https://dev.to/webdeasy/using-json-correctly-in-javascript-and-php-3moe>

## *[更新版本可用(点击)！](https://webdeasy.de/en/using-json-in-javascript-and-php/)🔥*

JSON 是 web 应用不可或缺的一部分。在这里，您将学习如何在 JavaScript(客户端)和 PHP(服务器端)之间正确地交换和存储 JSON 数据。

## 目录

*   [在 PHP 中读取并保存 JSON](#json-in-php)
    *   [读取 JSON](#read-json)
    *   [保存 JSON](#save-json)
*   [将 JSON 从 PHP 发送到 JavaScript](#send-json)
*   [JavaScript 中的 JSON](#json-in-js)
*   [结论](#conclusion)

JSON 代表 JavaScript Object Notation，是一种非常简单和紧凑的数据格式，用于存储和发送数据。用户(客户机)和服务器之间经常交换数据。在本文中，我们将研究 JSON 的数据传输和存储。

```
{
  "ceos": [
    {
      "id": "1",
      "name": "Steve Jobs"
    },
    {
      "id": "2",
      "name": "Bill Gates"
    },
    {
      "id": "3",
      "name": "Paul Allen"
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 JSON 的一个例子。具有属性 id 和名称的三个条目存储在 ceos 类别中。

您可以使用这个工具来验证和格式化您的 JSON。

因为我想在这里更深入地研究 JavaScript 和 PHP 的使用，如果你感兴趣的话，可以在本文中查看 JSON 的确切结构。

## 在 PHP 中读取并保存 JSON

### 阅读 JSON

原则上，JSON 数据可以非常容易地存储。一个简单的文本文件足以在那里存储数据。当涉及少量数据时，这也是一种常见的好的解决方案。但是，如果您有大量数据，或者是动态添加的数据(如联系人表单)，建议将数据存储在数据库中。

但是这里我们从少量的数据开始，在服务器端我们有一个名为 *storage.json* 的文件，其内容如下(内容与上面的例子相同，只是缩小了):

```
{"heroes":[{"id":"1","name":"Steve Jobs"},{"id":"2","name":"Bill Gates"},{"id":"3","name":"Paul Allen"},{"id":"4","name":"Sundar Pichai"}]} 
```

Enter fullscreen mode Exit fullscreen mode

下面几行可以用来读入和输出文件:

```
<?php 
$file = file_get_contents("storage.json");
print_r($file);
?> 
```

Enter fullscreen mode Exit fullscreen mode

无格式输出为我们提供了文件的简单文本内容:

```
{"heroes":[{"id":"1","name":"Steve Jobs"},{"id":"2","name":"Bill Gates"},{"id":"3","name":"Paul Allen"},{"id":"4","name":"Sundar Pichai"}]} 
```

Enter fullscreen mode Exit fullscreen mode

对于这种形式的数据，我们无能为力。我们可以编写自己的解析器将它转换成一个对象或数组。但这要容易得多。

PHP 为我们提供了函数 [json_decode()](https://www.php.net/manual/de/function.json-decode.php) 来将 json 字符串转换成一个对象。

```
<?php 
$file = file_get_contents("storage.json");
$json_decoded = json_decode($file);
print_r($json_decoded);
?> 
```

Enter fullscreen mode Exit fullscreen mode

JSON 字符串已经被转换成一个对象，我们可以像对待 PHP 中的其他对象一样对待它。这个输出看起来是这样的:

```
stdClass Object
(
    [heroes] => Array
        (
            [0] => stdClass Object
                (
                    [id] => 1
                    [name] => Steve Jobs
                )

            [1] => stdClass Object
                (
                    [id] => 2
                    [name] => Bill Gates
                )

            [2] => stdClass Object
                (
                    [id] => 3
                    [name] => Paul Allen
                )

            [3] => stdClass Object
                (
                    [id] => 4
                    [name] => Sundar Pichai
                )

        )

) 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我们现在可以使用普通的对象操作符来显示或改变对象的值:

```
<?php 
$file = file_get_contents("storage.json");
$json_decoded = json_decode($file);

echo $json_decoded->heroes[0]->name;    // Output: Steve Jobs

$json_decoded->heroes[0]->name = "CEO Steve Jobs";

echo $json_decoded->heroes[0]->name;    // Output: CEO Steve Jobs
?> 
```

Enter fullscreen mode Exit fullscreen mode

### 萨维杰森

一旦我们根据需要调整了数据，我们可以再次保存它。

```
<?php 
$filename = "storage.json";
$file = file_get_contents($filename);
$json_decoded = json_decode($file);

$json_decoded->heroes[0]->name = "CEO Steve Jobs";

$json_encoded = json_encode($json_decoded);
file_put_contents($filename, $json_encoded);
?> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在读取 JSON 字符串时对它进行了解码，所以我们必须在保存它之前再次对它进行编码。这是在 PHP 中通过 [json_encode()](https://www.php.net/manual/de/function.json-encode.php) 完成的。也是这样。很简单，不是吗？

## 从 PHP 发送 JSON 到 JavaScript

为了在客户端请求数据，我喜欢使用 [jQuery](https://jquery.com/) 。下面是一个例子，它向我们的 server.php 发出一个 Ajax 请求，并获得返回的数据。

*index.html*T2】

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    JSON in JavaScript and PHP
</head>

<body>

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script>
        $.getJSON('server.php', {}, function(data) {
            console.log(data);
        });
    </script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

*server.php*T2】

```
<?php
$file = file_get_contents("storage.json");
exit($file);
?> 
```

Enter fullscreen mode Exit fullscreen mode

控制台输出看起来是这样的:
[![JSON Console Log](img/b5ed5f4994b9b7b3d8900466e3106750.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mP6yNySm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webdeasy.de/wp-content/uploads/2019/05/json-console-log.png)

这使我们能够在 JavaScript 中正常访问最初来自我们的 *storage.json* 的数据。

## JavaScript 中的 JSON

如果我们现在在 JavaScript 中有一个 JSON 字符串，我们可以使用 [JSON.parse()](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) 函数将其转换为 JavaScript 对象。看起来是这样的:

```
var json = '{"heroes":[{"id":"1","name":"CEO Steve Jobs"},{"id":"2","name":"Bill Gates"},{"id":"3","name":"Paul Allen"},{"id":"4","name":"Sundar Pichai"}]}';

var obj = JSON.parse(json);
console.log(obj); 
```

Enter fullscreen mode Exit fullscreen mode

输出与我们的 storage.json.
[![JSON Console Log](img/b5ed5f4994b9b7b3d8900466e3106750.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mP6yNySm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webdeasy.de/wp-content/uploads/2019/05/json-console-log.png) 的输出相同

反过来，我们可以用 [JSON.stringify()](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 将一个 JavaScript 对象转换成 JSON。

```
var json = '{"heroes":[{"id":"1","name":"CEO Steve Jobs"},{"id":"2","name":"Bill Gates"},{"id":"3","name":"Paul Allen"},{"id":"4","name":"Sundar Pichai"}]}';

var obj = JSON.parse(json);

var jsonAgain = JSON.stringify(obj);
console.log(jsonAgain); 
```

Enter fullscreen mode Exit fullscreen mode

控制台输出又是我们的 json 字符串，它也在变量 JSON 中。

```
{"heroes":[{"id":"1","name":"CEO Steve Jobs"},{"id":"2","name":"Bill Gates"},{"id":"3","name":"Paul Allen"},{"id":"4","name":"Sundar Pichai"}]} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们已经讨论了 JavaScript 和 PHP 最重要的 JSON 函数。JSON 只是一种非常简单紧凑的数据格式。正如您现在看到的，在 JavaScript 和 PHP 中的使用也非常简单。

如果你喜欢这篇文章，我会很高兴你能在我的[博客](https://webdeasy.de/en/web-development-server-administration-tutorials/)上看到更多文章，并在[推特](https://twitter.com/WebDEasy)上关注我！:)

如果您有任何问题，请随时联系我！:)