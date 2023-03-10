# 构建您的第一个 Laravel 助手

> 原文：<https://dev.to/__mfarag/build-your-first-laravel-helpers-519b>

### 什么是帮手？

*助手是*你今天要创造的所有小而神奇的方法。这是一种提高代码效率和清理的更高级的方法。
*用 WebDev 的方式声明东西*:是一组定制的方法而不是类。

* * *

### 我们来设置一下。

[1]
用任何 IDE - *我用[升华](https://www.sublimetext.com)自己*。请新建一个(*)。php* 文件&命名为(*helpers.php*)，然后保存到你的 app 目录。

*你这边(dir)会像*

> `app/helpers.php`

*注*

> *   We didn't use the (**. blade.php** ) layer for this file.

* * *

[2]
let's assume you wanna inject your **layout.blade.php** template file with [FontAwesome](https://fontawesome.com/icons?d=gallery&m=free) Icons-Api. So the usual way is to just add a **link tag** referencing your Api like the following:

*代码*

> `<head>
> <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.3/css/all.css"crossorigin="anonymous">
> </head>`

*注*

> *   I have deleted some links, but you can have a new full api link [here](https://fontawesome.com/start)

【2-2】
*旁题* /请和我一起思考，如果你想使用更多的 API 呢？
*回答* / **aooch** 一个*头部标签*填了几块不干净的线。

[![the Beautiful Laravel](img/afc59b5f36d908a84d4e3689f70e0b6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K75AwUm---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/564x/c9/84/b9/c984b9ab6affc907e8771ba76a728402.jpg)

* * *

[3]
give yourself **[5-minutes]** : *eyes-looking-to-the-ceiling*. Work your mind to create a feature that can clean up our code? *you can use a paper and a pencil*.

[![hmmm](img/0da5581710dcd3dd27e586361a7db3b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x3fBBzq2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.pinimg.com/originals/51/a3/37/51a337efac7c505665820aee3bfddd8e.gif)

[3-2]
现在我们来谈谈**助手特性或功能** &是的，这肯定是使用 **Laravel 定制助手**的一个微小目的。因此，让我们回到我们的空文件( *app/helpers.php* )并开始向其中添加一些代码，如下所示:

*代码*

> `<?php
> function fontawesomeloader(){
> echo '<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.3/css/all.css"crossorigin="anonymous">';
> }`

*注*

> *   I prefer to use the naming style of **Snake Case** to name Assistant Laravel.

[3-3]
现在让我们回到我们的**layout.blade.php**请**删除**之前的**链接标签**是在*【Point:2】*中添加的，那么请用我们在*【Point:3-2】***font awesome loader()**中添加的 helpers 方法名替换它。现在刷新你的页面。还有 **Ohhh 什么都不管用**怎么了？！！

> *   Laravel will throw an exception saying *it can't find any function named* **fontawesomeloader ()** . Let's solve this problem.
> *   Remember to declare the helpers function name `{!! fontawesomeloader() !!}` like this.

[3-4]
请转到您的主项目目录中一个名为( *composer.json* )的文件，您会在这样的目录中找到它(*LaravelProjectName/composer . JSON*)。

**在**文件中请搜索“**自动加载**”:{

[3-5]
现在编辑*“自动加载”*对象内的代码，如下所示

*代码*

> `"autoload": {
> "psr-4": {
> "App\\": "app/"
> },
> "classmap": [
> "database/seeds",
> "database/factories"
> ],
> "files":[
> "app/helpers.php"
> ]
> }`

*注*

> *   We added our helper.php file location in **files-array** , which is called _ _ register _ _ your assistant.

**现在你已经注册了**你的 helpers.php 文件，并在你的项目目录中的任意文件中打开它进行随机**调用/访问**。

**然后嗷嗷嗷，什么都不管用了**。

[3-6]
*最后一步*永远开机。请前往您的**航站楼**。和 **cd** 到你的**项目目录**然后执行下面的**命令**

*代码*

> *composer 转储-自动加载*

* * *

[4]
**Ohhhh** 终于开始工作了。
**好消息**，您的*助手*已经启动并运行&您可以*轻松*添加任意多的方法。

**你做得很好**学习并设置了你的第一个 Laravel 助手。
我为你感到高兴和骄傲。

[4-1]

### Laravel 不是很好看吗？

【5】

### 感谢您的时间和耐心。

我希望我对你有用。请试用一下，修改一下我写的代码。开发你自己的助手，如果你愿意，可以在这里发表评论。