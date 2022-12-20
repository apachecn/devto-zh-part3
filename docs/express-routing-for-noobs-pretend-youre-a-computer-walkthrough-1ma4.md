# Noobs 快速路由演练:假设你是一台计算机

> 原文：<https://dev.to/bananabrann/express-routing-for-noobs-pretend-youre-a-computer-walkthrough-1ma4>

## **内容**

1.  介绍的
2.  MVC 是什么？
3.  当你访问一个网页时会发生什么
4.  成为计算机:路线图、路线
5.  成为计算机:路线图，控制器示例
6.  结论

#### 本博客包含:

*   Express 中路由的可视化
*   MVC 的基础、基本回顾
*   不包含型号或控制器的说明

* * *

## **入门**

所以我在学习如何编码，我抓住把手——明白了吗？——为了亲爱的生活。有这么多的“事情”，有这么多的“东西”一路上，我想知道文件夹里所有小文件的用途的爱好被吹到了窗外，被冲到了排水沟里，掉进了海里，只为被鲨鱼吃掉。那条鲨鱼目前是快递，节点，车把，不知何故发射它在月球上希望赶上互联网。

池塘里最大最凶猛的鲨鱼是快车。尤其是通过路由在文件之间导航。我开始征服对它的内在理解，对我有帮助的最好方法是假装我是一台计算机。哔哔！

当你阅读它的时候，这是非常多余的，最好在 10 秒钟内亲自解释，但这是理解 Express 中路线如何工作的非常有用的思维方式。

* * *

## **什么是 MVC？**

MVC 代表模型、视图、控制器。这是在项目中组织文件的一种方式。我们根据文件的用途将它们分开。

*   模型，是什么东西。
*   看，什么东西看起来像。
*   控制器，这东西是干什么的。

没有要安装的程序，没有统一的名字，什么都没有。这只是我们为自己的个人禅命名和放置文件的一种方式。

* * *

## **当你访问一个网站时会发生什么？**

当你打开浏览器并输入一个网站时，你的浏览器会提交一个 GET 请求。这是四种基本的数据请求类型之一。有了这个 GET 请求，网站的服务器就“监听”你去了哪里。因为您决定通过 MVC 来组织您的文件，所以我们的路由器现在负责将您的文件链接在一起，以便向客户端提供正确的响应！

* * *

## **成为电脑:路线图、路线**

帮助我修补所有(大部分)Express 的最重要的一件事是，考虑我是否是服务器端的一台计算机。这是有趣的部分！

为了便于参考，在这个计算机角色扮演的例子中，我们的相关文件结构将是这样的:

```
.
├── controllers
│   ├── application.js
│   ├── birds.js
│   ├── index.js
│   └── lizards.js
├── index.js
├── models
│   ├── Bird.js
│   ├── Lizard.js
├── routes
│   ├── birds.js
│   ├── index.js
│   └── lizards.js
└── views
    ├── birdsflying.hbs
    └── lizardscrawling.hbs 
```

Enter fullscreen mode Exit fullscreen mode

你的文件结构的名字，文件夹，惯例，可能都是不同的。这不是一个标准的模式，你的路线可以连接任何东西！

* * *

### 我们的旅程从 index.js 开始

作为一个高度先进的技术奇迹，你需要在准备工作之前被告知一些事情。我是谁，我是什么？

为了做到这一点，我们的 index.js 和主项目文件夹回答了大部分问题。

请记住，我们是服务者！

```
const express = require("express");
// Cool, I'm going to be using Express today. I hope my user didn't forget to npm install express me!

const hbs = require("hbs");
// Looks like I'm also using Handlebars.

const app = express();
// Nice! When I see "app," I'm going to be calling the function named express.

const routes = require("./routes/index.js");
// Uh-huh... I see that when I call the variable "routes," I'm going to be shooting it over to the routes folder!

app.use(express.static("public"));
// Looks like I'll be serving "static" files today, such as CSS and JavaScript.

app.set("view engine", "hbs");
// Oh right, I'm supposed to use hbs when I go to display things.

app.use("/", routes);
// When I get something, I'll call the "routes" variable. Good thing I defined it above!

app.use(require("./routes/index.js"));
// I need the routes/index.js file, OR ELSE I QUIT. That'll show the user!

app.listen(3000, () => {
  console.log("server is running");
});
// Oh cool, I'm listening on port 3000. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道自己是谁了，我们洗耳恭听用户的意见！

* * *

### 地面零点，index.js

我们有个请求！有人访问了 3000 端口，现在我们准备角色！这是一个 GET 请求，所以我们需要做的就是向用户显示一些东西。我们知道这一点，因为我们与生俱来！(我们从 Express 和 JavaScript 内置中知道这些东西)

1.  让我们滚下 index.js 文件。起初，我们只是知道我们是谁。
2.  好吧。用户正在请求名为 pigeon `/birds/pigeon`的页面。哇哦！当我被请求`/`时，我的一个命令被激活！`app.use("/", routes);`
3.  `app.use("/", routes);`所以基于此，我称之为`routes`。我们之前用`const routes = require("./routes/index.js");`定义了它。
4.  不错！这意味着我现在去那个文件，并继续下去！

* * *

### 旅程路线文件夹，routers/index.js

如前所述，`app.use("/", routes);`引用了`const routes = require("./routes/index.js");`，我们探索当我们的用户访问/pigeon 时会发生什么，首先将我们带到 routes 中的 index.js 文件。

routers/index.js 文件主要负责将所有其他路由汇集在一起。

*为什么？面向未来的路由，因此我们可以简单地引用 router/index.js，而不是直接链接路由。

我们，计算机，从上到下阅读。使用`//`，让我们跟随我们的路径，看看我们的文件想要我们去哪里。

```
// Ahh, the routes index.js file! Good times here. Let's take a look... I'm looking to see where I should go for /birds/pigeon.

const express = require('express');
// Looks like I'm still using Express.

const router = express.Router();
// Okay, I'm using Express's Router method for the following. I need this because it's a part of the framework.

router.use('/', require('./application.js'));
// If user requests homepage, shoot them to ./application.js. Remember, . is current directory, but this isn't what we're looking for!

router.use('/lizards', require('./lizards'));
// If user requests /lizards, we will send them to lizards.js. Nope!

router.use('/birds', require('./birds'));
// If user requests /birds, we wil send them to birds.js. This is what we want, let's go! 
```

Enter fullscreen mode Exit fullscreen mode

### 步入航线/birds.js

是啊！我们到了，在 routes/birds.js！好吧，这里面是什么？

```
 const express = require("express");
const router = express.Router();
// Hey, I remember these!

const birdController = require("../controllers/birds");
// Huh. Alright, we have a variable called birdController, and it requires us to go to the controllers/birds.js file. We don't need to mark it with `.js`, because Express framework tells us to. Remember, `..` goes up a directory and we're currently in routes!

router.get('/', birdController.show);
// Hey, this is our path, birds/, but we're looking for /birds/pigeon. We just see / and not /birds because we're in /birds. Think of it as changing directories. So... Nope, not our stop!

router.post('/pigeon', birdController.create);
// This is it! This is /birds/pigeon! But there's one thing... I have a GET request, not a POST request. So this is the correct path, but not the correct action. Let's keep looking...

router.get('/pigeon', birdController.show);
// YES! Here is our GET request for /birds/pigeons. Let's see... It wants us to execute the .show method on birdController. What's birdController? Oh, right, we defined it up above! We need to travel to ../controllers/birds.js

module.exports = router;
// How we travel! Without this, we'd be trapped here. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **成为计算机:路线图，控制器示例**

### 抵达魔都，控制器/birds.js

我们成功了！控制器是行动的地方。认为控制器是普通的 JavaScript。这就是核心所在，普通的 JavaScript 驱动着它。看看模特们现在是如何出场的，这就是一切发生的地方，多亏了路线，动态地引导你！

注意之前 birdController 调用`birdController.show`时的方法，`.show`是这个方法。

```
 const Bird = require('../models/Bird');
const User = require('../models/User');

module.exports = {
    create: (req, res) => {
        console.log("This is what we weren't looking for!");
    },
    show: (req, res) => {
        console.log("This is what we were looking for!");
        // The code here would be executed with birdController.show.
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

思考计算机如何读取代码极大地帮助了我理解为什么代码是这样写的。我真的希望它能帮助你想象和理解它！

觉得有帮助？我有什么错误吗？有问题吗？有反馈？让我知道！

感谢您的阅读，祝您编码愉快！
~bananabrann