# fake _ rest _ server–PHP 中的 REST 服务器

> 原文：<https://dev.to/jalbam/fakerestserver--rest-server-in-php-19la>

" [fake_rest_server](https://github.com/jalbam/fake_rest_server) "是一个用 PHP 制作的简单易配置的 rest 服务器。

我这样做只是为了快速创建不同的 REST 服务器用于测试目的。

可以在 GitHub 上找到:[https://github.com/jalbam/fake_rest_server](https://github.com/jalbam/fake_rest_server)

## 配置服务器

要添加新的路径(路线)和方法，开发人员只需要创建一个新的文件夹结构(文件夹和子文件夹，如果需要的话)来表示路线，并在每个名为 *[method_desired]的方法的一个文件中。php* (比如 put.php*)。*

要开始开发 REST 服务器，您只需要下载 **[src/](https://github.com/jalbam/fake_rest_server/blob/master/src)** 文件夹中的文件。

你可能想看一下**[src/_ code/functions . PHP](https://github.com/jalbam/fake_rest_server/blob/master/src/_code/functions.php)**文件(引擎会自动包含它)，因为它提供了一些基本但有用的函数。在这里，您还可以添加新功能或修改现有功能。

如果要添加数据，可以使用**[src/_ code/data/data . PHP](https://github.com/jalbam/fake_rest_server/blob/master/src/_code/data/data.php)**文件(由引擎自动包含)。

如果要添加配置数据，可以使用**[src/_ code/config . PHP](https://github.com/jalbam/fake_rest_server/blob/master/src/_code/config.php)**文件(由引擎自动包含)。

由于引擎使用 _REST_SERVER 常量定义了**，因此您可以使用以下开头的行来保护任何文件:** 

```
<?php if (!defined("USING_REST_SERVER") || !USING_REST_SERVER) { return; } ?> 
```

注意:上面的这个授权码也在 **AUTHORIZATION_CODE** 常量中定义(在**[src/_ CODE/config . PHP](https://github.com/jalbam/fake_rest_server/blob/master/src/_code/config.php)**文件中)。

### 举例:

1)在根文件夹中(放置 **[src/_code/](https://github.com/jalbam/fake_rest_server/blob/master/src/_code/)** 文件夹和 **[src/index.php](https://github.com/jalbam/fake_rest_server/blob/master/src/index.php)** 文件的地方)，创建一个名为 **myRESTService/** 的文件夹。

2)在我们刚刚创建的 **myRESTService/** 文件夹中，创建另一个名为 **user/** 的文件夹，并在其中创建两个文件:【index.php】和**get.php**。

3)在**myRESTService/user/index . PHP**文件中，放置以下代码:

```
<?php
    //Users info (this could be in the "src/_code/config.php" file, but it is just an example):
    $usersData = Array
    (
        //User IDs:
        "1" =>
            Array
            (
                "name" => "John Doe",
                "favouriteFood" => "meat"
            ),
        "2" =>
            Array
            (
                "name" => "Joan Alba Maldonado",
                "favouriteFood" => "pizza"
            )
    );

    //Gets the data needed which has been sent through the REST client:
    $userId = getVariable("id"); //"getVariable" and other functions available in the "src/_code/functions.php" file. 
```

4)在**myRESTService/user/get . PHP**文件中放入以下代码:

```
<?php
    if ($userId === "") { echo "No id sent!"; }
    else if (array_key_exists($userId, $usersData))
    {
        echo $usersData[$userId]["name"] . " likes eating " . $usersData[$userId]["favouriteFood"];
    }
    else { echo "User cannot be found! (id=" . $userId . ")"; } 
```

5)这样，我们将使用 **myRESTService/user/** 路由配置 REST 服务器，接受带有 **id** 参数的 **GET** 方法。这个例子可以在 **[example_easy/](https://github.com/jalbam/fake_rest_server/blob/master/example_easy)** 文件夹中找到。

## 测试服务器

如果您没有 REST 客户端，可以在任何 web 浏览器上测试服务器，方法是将 **debug=1** 参数添加到 URL，并将**方法**参数添加到所需的方法(如果方法是 **GET** 则不需要)，例如:*[http://localhost/fake \ _ REST \ _ server/src/index . PHP/route \ _ 1/subroute？方法=post &调试=1 &用户名= Joan](http://localhost/fake%5C_rest%5C_server/src/index.php/route%5C_1/subroute?method=post&debug=1&username=Joan)T9】*

按照上面的示例，您可以使用 web 浏览器访问以下链接:

*[http://localhost/route \ _ to \ _ the \ _ REST \ _ server/index . PHP/myRESTService/user/？method = get&debug = 1&id = 1](http://localhost/route%5C_to%5C_the%5C_REST%5C_server/index.php/myRESTService/user/?method=get&debug=1&id=1)T3】(应该显示“无名氏喜欢吃肉”)*

*[http://localhost/route \ _ to \ _ the \ _ REST \ _ server/index . PHP/myRESTService/user/？method = get&debug = 1&id = 2](http://localhost/route%5C_to%5C_the%5C_REST%5C_server/index.php/myRESTService/user/?method=get&debug=1&id=2)T3】(应该显示“琼·阿尔巴·马尔多纳多喜欢吃披萨”)*

*[http://localhost/route \ _ to \ _ the \ _ REST \ _ server/index . PHP/myRESTService/user/？method = get&debug = 1&id = 3](http://localhost/route%5C_to%5C_the%5C_REST%5C_server/index.php/myRESTService/user/?method=get&debug=1&id=3)T3】(应该显示“找不到用户！(id=3)")*

*[http://localhost/route \ _ to \ _ the \ _ REST \ _ server/index . PHP/myRESTService/user/？method = get&debug = 1](http://localhost/route%5C_to%5C_the%5C_REST%5C_server/index.php/myRESTService/user/?method=get&debug=1)T3】(应该显示“没有 id 发送！”)*

注意，路径末尾的“ */* ”字符是可选的。

## 最终评论

使用 PHP 语言进行扩展是非常容易的。该项目已经包括一些路线，方法，功能和用户帐户等数据的例子。作为示例(在 **[example/](https://github.com/jalbam/fake_rest_server/blob/master/example)** 和 **[example_easy/](https://github.com/jalbam/fake_rest_server/blob/master/example_easy)** 文件夹中)但是它们可以被删除。

唯一真正需要的代码位于 **[src/](https://github.com/jalbam/fake_rest_server/blob/master/src/)** 文件夹中。在其中， **[route_1/](https://github.com/jalbam/fake_rest_server/blob/master/src/route_1)** 文件夹及其所有内容也可以被删除，因为这只是一个例子。

## 执照

这个项目可以用于任何非商业目的，但始终保持作者的名字和版权条款的自由复制，分发和修改。除此之外，这个项目你想怎么用就怎么用，千万不要卖！