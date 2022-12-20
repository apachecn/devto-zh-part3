# 为 NodeJS REST APIs 自动生成锅炉板代码

> 原文：<https://dev.to/mayankshah1607/generating-boiler-plate-code-for-nodejs-rest-apis-fpb>

我成为 NodeJS 开发人员已经快一年了。我主要用 mongoose 和 express 开发 REST APIs。最初当我开始开发时，我的代码并不被认为是“干净的”。后来，我了解了 MVC 的概念，以及我的项目必须如何相应地构建。我主要把我的代码组织成 3 个文件夹——模型、控制器和路线。“模型”将由带有已定义模式的 mongoose 模型组成。控制器提供了这些“模型”和来自客户端或“视图”的传入请求之间的主要功能。我发现自己写了很多模板代码，以确保我的应用程序遵循这样的架构。

这是我开发一个个人使用的 npm 模块——“节点锅炉”(node-boiler)，后来我把它做了开源(你可以在这里找到)。它非常容易使用。您只需要在名为“boil.yml”的文件中设置您的项目配置。将该文件保存在项目的根目录下，并运行命令“nodeboil”。您的整个项目的所有目录和文件与锅炉板代码将被生成！:)

有很多项目模板可以用作模板。但是，“节点锅炉”提供了很大的灵活性，确保您的项目是按照您的需求配置的。你可以选择你想要的，以及你想要的方式。此外，它是一个 CLI，这意味着，您甚至不必在代码库中包含来自该模块的任何代码。

让我们试一试。

我必须首先全局安装这个库:

```
$ npm i -g node-boiler 
```

一旦完成，我需要创建一个名为`boil.yml`的文件，并将其保存在项目的根目录下。`boil.yml`是不言自明的。想想这个:

```
models:
  - 'users'
  - 'admins'
  - 'players'

controllers:
  authController:
    - 'login'
    - 'signUp'

  playerController:
    - 'pass'
    - 'shoot'

views:
  - 'home'
  - 'profile' 

routes:
  admin-routes:
    post:
      - '/delete'
      - '/another-route'
    get:
      - '/get-here'
      - '/lol'
  player-routes:
    get:
      - '/shoot'
      - '/kick' 
```

它以最明显的方式定义了你的项目结构。

完成后，运行以下命令:

```
$ nodeboil 
```

您可能会看到以下输出:

```
Generated file users.js
Generated file admins.js
Generated file players.js
Generated file authController.js
Generated file playerController.js
Generated file home.html
Generated file profile.html
Generated file admin-routes.js
Generated file player-routes.js
*******GENERATED ALL FILES******* 
```

您的项目目录现在将看起来像

```
 --node_modules
   |--your modules
 --models
   |--users.js
   |--admins.js
   |--players.js
 --views
   |--home.html
   |--profile.html
 --controllers
   |--authController.js
   |--playerController.js
 --routes
   |--admin-routes.js
   |--player-routes.js
 --boil.yml
 --package.json
 -- <entry file>.js 
```

这些生成的文件带有您可以编辑的基本锅炉板代码。

*   authController 示例(在/controllers 下)

```
module.exports = {
 login: function(){},// Add function logic here
 signUp: function(){},// Add function logic here
} 
```

*   admin-routes.js 的示例(在/routes 下)

```
const router = require('express').Router;

router.post('/delete', (req, res) => {}); // Add your route logic here
router.post('/another-route', (req, res) => {}); // Add your route logic here
router.get('/get-here', (req, res) => {}); // Add your route logic here
router.get('/lol', (req, res) => {}); // Add your route logic here

module.exports = router; 
```

*   users.js 示例(模型)(在/models 下)

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const usersSchema = new Schema({}); //Write your schema here

const users = mongoose.model('users', usersSchema); 

module.exports = users; 
```

*   home.html 示例(下/视图)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    home
</head>
<body>

</body>
</html> 
```

点击[这里](https://github.com/mayankshah1607/node-boiler)在 GitHub 上查看。如果你觉得它有用，请给它一颗星。:)