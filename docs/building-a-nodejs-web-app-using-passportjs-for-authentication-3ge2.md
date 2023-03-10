# 使用 passports 进行身份验证构建 nodejs web 应用程序

> 原文：<https://dev.to/gm456742/building-a-nodejs-web-app-using-passportjs-for-authentication-3ge2>

更新了！！！
本教程使用 **PassportJS** 向 MySQL 数据库管理软件(DBMS)认证 NodeJS 应用。这篇文章的原因很简单。在我学习 web 开发的时候，当我在我的第二个项目中将 PassportJS 身份验证集成到我的应用程序中时，我面临着一个挑战。那时，我使用 **MySQL** 进行数据库管理， **SequelizeJS** 用于基于 SQL 的数据库的对象关系映射器(ORM ),在这里是 MySQL， **ExpressJS** 中间件， **Body Parser** 和 **Express Session** 用于服务器和会话管理。困难在于我只能找到使用*手柄*作为 ORM 和 *MongoDB* 作为 DBMS 的教程，那时我还不太熟悉，所以如果你发现自己处在这个十字路口，这是给你的。我不会浪费你的时间，而是马上投入进去。我会尽可能的不夸张，这样即使是对 web 开发了解最少的人也能理解。我们需要为这个设置做一些准备。我用的是 Windows Pc，所以如果我说的任何东西不适合你的操作系统，特别是我的建议，请找一个变通办法，但我相信过程是一样的。

首先，你需要一台装有你最喜欢的文本编辑器(我用的是 VS Code)、网络浏览器(我推荐 Google Chrome)、你最喜欢的终端(推荐 Git Bash)和你选择的 SQL DBMS 的电脑。我用的是 MySQL Workbench 6.3 CE。我将列出所有的步骤，这样很容易理解。当你具备以上所有条件并正确设置后，请按照以下说明进行操作。我假设你已经有你的数据库创建，如果没有，我会带你通过。

1.  在您的计算机上的任意位置创建一个文件夹。我更喜欢使用我的终端导航到我喜欢的位置，然后输入`mkdir nameOfProject`。对于本教程，我将在我的桌面上创建文件夹 *learningPassportJS* 。下一步是键入 cd nameOfProject 导航到项目文件夹。

[![](img/88c63cc031afdf8ab0b4edf3d455ee40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dcr8CU50--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57241/my8dGvx5QZm1UbjmfRDq_projectTerminal.PNG)

1.  在终端中，我们必须初始化文件夹来处理所有的 NodeJS 框架。您可以稍后再决定这样做，但是如果您是这个过程的新手，我建议您先这样做。通过键入`npm init`并按回车键来完成此操作。这将使用一个 *package.json* 文件建立您的项目。该文件将包含所有预期的依赖项和许可证以及您的姓名等信息。出于我们的目的，我将一直按键盘上的 enter 键来加载默认值，但我会将入口点设置为 ***server.js*** 。随意换成自己喜欢的。请确保您的文本中有小写字母，否则您将不得不自己键入它们。

[![](img/d4dbbb53691be1b4b70e6580797d64ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SI5wNlwS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57241/0SiEsACSSa5teEke1eyb_projectTerminalNpmInit.PNG)

1.  初始化项目后，我们将在终端上用`touch server.js`创建 server.js 文件。

2.  现在让我们安装我们需要的所有依赖项。稍后我会解释为什么我们需要每一个，但我喜欢安装所有的东西来摆脱它。你可以在以后安装它们，但你需要它们来成功运行应用程序。将它们作为`npm i --save sequelize passport passport-local mysql2 mysql express express-session body-parser bcrypt-nodejs`安装在一行中，或者您可以选择将它们单独安装为
    `npm i --save sequelize`
    `npm i --save passport`
    `npm i --save passport-local`
    `npm i --save mysql2`
    `npm i --save mysql`
    `npm i --save express`
    `npm i --save express-session``npm i --save body-parser`
    `npm i --save bcryptjs`

添加`--save`确保您的依赖项被添加并保存到 package.json 文件中。如果您想要部署此应用程序，这一点很重要。你将实现一个名为 *node_modules* 的新文件夹。不要碰这个。这是 node 用来在您的计算机上本地运行应用程序的。如果您的项目使用 Git，不要忘记将`node_modules`添加到项目根文件夹中的`.gitignore`文件中。

感谢[乔丹·怀特](https://github.com/jordanrw)的贡献，我想值得一提的是，你必须先安装 Sequelize CLI，然后才能使用 Sequelize。通过从您的首选终端运行`npm install -g sequelize-cli`来进行全局安装，或者您可以删除`-g`来进行本地安装。

1.  打开在您喜欢的终端中创建的 server.js 文件，并在我们的 server.js 文件中输入几行代码。别担心，我会对所有代码进行大量的注释，所以很容易理解我为什么要写每一行。您可以将下面的代码复制到服务器文件中。

```
// Requiring necessary npm middleware packages 
var express = require("express");
var bodyParser = require("body-parser");
var session = require("express-session");
// Setting up port
var PORT = process.env.PORT || 8080;
// Creating express app and configuring middleware 
//needed to read through our public folder
var app = express();
app.use(bodyParser.urlencoded({ extended: false })); //For body parser
app.use(bodyParser.json());
app.use(express.static("public"));
//
//we are doing a GET to test if our server is working fine
app.get('/', function(req, res) {    
       res.send('Welcome to Passport with Sequelize and without HandleBars');
});
//
//this will listen to and show all activities on our terminal to 
//let us know what is happening in our app
app.listen(PORT, function() {
    console.log("App listening on PORT " + PORT);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

保存服务器文件。让我们运行服务器以确保它正常工作。通过在终端中键入`npm start`或`node server.js`来完成。你还记得我们跑`npm init`时的*入口点*吗？这就是你运行`npm start`时的叫法。

如果您一直按照说明进行操作，您应该会看到以下内容

[![](img/d68d7cbf9d1da4afd526d7aa7d635b85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Sl3q9PT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57241/efP7HKyVTYGJAzXZkI9S_projectTerminalServerTest.PNG)

打开浏览器，输入位置 [localhost:8080](http://localhost:8080) 。这将显示*欢迎使用带序列和不带把手的护照*。干得好！！走到这一步。您正在创建自己的应用程序。如果您没有看到该页面，请从头开始查找步骤。您可以结束服务器并回到您的代码。

1.  我从一开始就假设您可能已经创建了数据库。如果你没有或者不知道如何去做，不要担心。只需打开您选择的 MySQL 程序，在查询 shell 中输入`CREATE DATABASE passport_demo;`并运行它。你应该有一个名为 *passport_demo* 的数据库。

2.  现在我们的服务器和数据库已经工作了，是时候添加其他部分了。我们将配置和初始化我们的序列模块。通过在终端上键入`sequelize init:models & sequelize init:config`并按回车键来完成。
    这段代码运行后，您应该会看到两个文件夹*模型*和*配置*。
    打开 config 文件夹，你应该会看到一个 *config.json* 文件。打开它并编辑开发对象的设置以匹配您的设置。如果您的数据库有密码，请在这里用引号将其输入。示例如下

```
{  "development":  {  "username":  "root",  "password":  "yourpassword",  "database":  "passport_demo",  "host":  "127.0.0.1",  "dialect":  "mysql"  },  "test":  {  "username":  "root",  "password":  null,  "database":  "database_test",  "host":  "127.0.0.1",  "dialect":  "mysql"  },  "production":  {  "username":  "root",  "password":  null,  "database":  "database_production",  "host":  "127.0.0.1",  "dialect":  "mysql"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

向后导航并打开模型文件夹。您应该会看到一个 *index.js* 文件。这在我们的教程中应该是不变的，但是如果你的 config 文件夹在不同的位置，你可以打开它，从 Col 37 编辑第 8 行到你的位置，因为它需要 *config.json* 文件才能工作。一些 Windows 电脑还会抛出找不到配置模块的错误。将反斜杠改为正斜杠来修复这个错误。

1.  在 *models* 文件夹中创建一个名为 *user.js* 的新文件。这将使用 sequelize 将我们的用户信息插入数据库。根据您的需要，您可以拥有多个模型文件。models 文件夹应该包含您在数据库中插入的各种表格。在本教程中，我们需要一个用户模型。我们将要求 *bcryptjs* 包对用户创建或登录的密码进行加密和解密。您的 *user.js* 文件应该如下所示

```
// Requiring bcrypt for password hashing. Using the bcryptjs version as 
//the regular bcrypt module sometimes causes errors on Windows machines
var bcrypt = require("bcryptjs");
//
// Creating our User model
//Set it as export because we will need it required on the server
module.exports = function(sequelize, DataTypes) {
  var User = sequelize.define("User", {
    // The email cannot be null, and must be a proper email before creation
    email: {
      type: DataTypes.STRING,
      allowNull: false,
      unique: true,
      validate: {
        isEmail: true
      }
    },
    // The password cannot be null
    password: {
      type: DataTypes.STRING,
      allowNull: false
    }
  });
  // Creating a custom method for our User model. 
  //This will check if an unhashed password entered by the 
  //user can be compared to the hashed password stored in our database
  User.prototype.validPassword = function(password) {
    return bcrypt.compareSync(password, this.password);
  };
  // Hooks are automatic methods that run during various phases of the User Model lifecycle
  // In this case, before a User is created, we will automatically hash their password

  User.hook("beforeCreate", function(user) {
    user.password = bcrypt.hashSync(user.password, bcrypt.genSaltSync(10), null);
  });
  return User;
};

//This is a fix by Samaila Philemon Bala in case you want to use ES6
//and the above is not working

//User.beforeCreate(user => {
  //  user.password = bcrypt.hashSync(
    //  user.password,
      //bcrypt.genSaltSync(10),
      //null
    //);
  //}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  让我们回到我们的 *server.js* 文件，添加几行代码。我们将需要要求服务器读取模型文件夹，我们还需要它来同步我们的插入和读取到数据库。您的 server.js 应该如下所示

```
// Requiring necessary npm middleware packages 
var express = require("express");
var bodyParser = require("body-parser");
var session = require("express-session");
// Setting up port
var PORT = process.env.PORT || 8080;
//Import the models folder
var db = require("./models");
//
// Creating express app and configuring middleware 
//needed to read through our public folder
var app = express();
app.use(bodyParser.urlencoded({ extended: false })); //For body parser
app.use(bodyParser.json());
app.use(express.static("public"));
//
//we are doing a GET to test if our server is working fine
app.get('/', function(req, res) {    
       res.send('Welcome to Passport with Sequelize and without HandleBars');
});
//
//this will listen to and show all activities on our terminal to 
//let us know what is happening in our app
// Syncing our database and logging a message to the user upon success
db.sequelize.sync().then(function() {
  app.listen(PORT, function() {
    console.log("==> 🌎  Listening on port %s. Visit http://localhost:%s/ in your browser.", PORT, PORT);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在让我们导航到 config 文件夹，创建另一个名为*中间件*的文件夹，并在该文件夹中创建一个名为 *isAuthenticated.js* 的文件。你应该有`/config/middleware/isAuthenticated.js`。打开并编辑 *isAuthenticated.js* 文件以匹配这个

```
// This is middleware for restricting routes a user is not allowed to visit if not logged in
module.exports = function(req, res, next) {
  // If the user is logged in, continue with the request to the restricted route
  if (req.user) {
    return next();
  }
  // If the user isn't' logged in, redirect them to the login page
  return res.redirect("/");
}; 
```

Enter fullscreen mode Exit fullscreen mode

这也将被导出，我们将需要这一点来限制对登录用户只意味着页面的访问。

1.  是时候办护照了。在 *config* 文件夹中创建一个名为 passport.js 的文件。打开该文件并在文件中输入以下内容。评论解释了一切。

```
//we import passport packages required for authentication
var passport = require("passport");
var LocalStrategy = require("passport-local").Strategy;
//
//We will need the models folder to check passport agains
var db = require("../models");
//
// Telling passport we want to use a Local Strategy. In other words,
//we want login with a username/email and password
passport.use(new LocalStrategy(
  // Our user will sign in using an email, rather than a "username"
  {
    usernameField: "email"
  },
  function(email, password, done) {
    // When a user tries to sign in this code runs
    db.User.findOne({
      where: {
        email: email
      }
    }).then(function(dbUser) {
      // If there's no user with the given email
      if (!dbUser) {
        return done(null, false, {
          message: "Incorrect email."
        });
      }
      // If there is a user with the given email, but the password the user gives us is incorrect
      else if (!dbUser.validPassword(password)) {
        return done(null, false, {
          message: "Incorrect password."
        });
      }
      // If none of the above, return the user
      return done(null, dbUser);
    });
  }
));
//
// In order to help keep authentication state across HTTP requests,
// Sequelize needs to serialize and deserialize the user
// Just consider this part boilerplate needed to make it all work
passport.serializeUser(function(user, cb) {
  cb(null, user);
});
//
passport.deserializeUser(function(obj, cb) {
  cb(null, obj);
});
//
// Exporting our configured passport
module.exports = passport; 
```

Enter fullscreen mode Exit fullscreen mode

1.  为了让我们的应用程序像预期的那样工作，我们需要能够获得并发布到我们的数据库。例如，我们放在 *server.js* 文件中的`app.get`代码块。让我们写一个干净的代码。在根文件夹中创建一个名为 *routes* 的文件夹，并创建两个名为 *api-routes.js* 和 *html-routes.js* 的文件。 *api-routes.js* 将用于路由`GET`和`POST`进出数据库。打开 *api-routes.js* 并粘贴以下内容。评论解释了一切。

```
// Requiring our models and passport as we've configured it
var db = require("../models");
var passport = require("../config/passport");
//
module.exports = function(app) {
  // Using the passport.authenticate middleware with our local strategy.
  // If the user has valid login credentials, send them to the members page.
  // Otherwise the user will be sent an error
  app.post("/api/login", passport.authenticate("local"), function(req, res) {
    // Since we're doing a POST with javascript, we can't actually redirect that post into a GET request
    // So we're sending the user back the route to the members page because the redirect will happen on the front end
    // They won't get this or even be able to access this page if they aren't authed
    res.json("/members");
  });
//
  // Route for signing up a user. The user's password is automatically hashed and stored securely thanks to
  // how we configured our Sequelize User Model. If the user is created successfully, proceed to log the user in,
  // otherwise send back an error
  app.post("/api/signup", function(req, res) {
    console.log(req.body);
    db.User.create({
      email: req.body.email,
      password: req.body.password
    }).then(function() {
      res.redirect(307, "/api/login");
    }).catch(function(err) {
      console.log(err);
      res.json(err);
      // res.status(422).json(err.errors[0].message);
    });
  });
//
  // Route for logging user out
  app.get("/logout", function(req, res) {
    req.logout();
    res.redirect("/");
  });
//
  // Route for getting some data about our user to be used client side
  app.get("/api/user_data", function(req, res) {
    if (!req.user) {
      // The user is not logged in, send back an empty object
      res.json({});
    }
    else {
      // Otherwise send back the user's email and id
      // Sending back a password, even a hashed password, isn't a good idea
      res.json({
        email: req.user.email,
        id: req.user.id
      });
    }
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们暂时离开 html-routes.js 。我们会回来的。我们将需要它来处理登录和服务我们的页面。

1.  在 *server.js* 文件中，我们需要导入并初始化 passport。请确保在 passport 之前初始化您的 express，因为 passport 需要 express。服务器中的标记非常重要。我还将删除`app.get`代码块，因为我们不需要它。您的服务器文件应该如下所示

```
// Requiring necessary npm packages
var express = require("express");
var bodyParser = require("body-parser");
var session = require("express-session");
// Requiring passport as we've configured it
var passport = require("./config/passport");
//
// Setting up port and requiring models for syncing
var PORT = process.env.PORT || 8080;
var db = require("./models");
//
// Creating express app and configuring middleware needed for authentication
var app = express();
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());
app.use(express.static("public"));
// We need to use sessions to keep track of our user's login status
app.use(session({ secret: "keyboard cat", resave: true, saveUninitialized: true }));
app.use(passport.initialize());
app.use(passport.session());
//
// Requiring our routes
require("./routes/html-routes.js")(app);
require("./routes/api-routes.js")(app);
//
// Syncing our database and logging a message to the user upon success
db.sequelize.sync().then(function() {
  app.listen(PORT, function() {
    console.log("==> 🌎  Listening on port %s. Visit http://localhost:%s/ in your browser.", PORT, PORT);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们还需要 html-routes。下一步是创建我们的用户界面(UI ),以便能够捕获用户的登录信息。这将是我们通常用 css 和 js 文件创建的常规 html 文件，但这次它将被放在一个公共文件夹中。这是 express 将使用和解析的文件夹。如果您熟悉 [POSTMAN](https://www.getpostman.com/) ，那么您可以在此时使用它来测试服务器。

1.  我创建了一个包含文件的示例公共文件夹。这就是我将在本教程中使用的。从 [Mediafire](http://www.mediafire.com/file/39hjqqp8t2ddyuy/learnPassportPublic.zip) 下载并解压到根文件夹。

2.  看一下公共文件夹中的 html 文件。您将看到我使用`API` s 捕获了注册、登录和成员页面`GET`,这样我们可以轻松地将其传递给服务器。

3.  现在打开 *html-routes.js* 并粘贴下面的代码

```
// Requiring path to so we can use relative routes to our HTML files
var path = require("path");
//
// Requiring our custom middleware for checking if a user is logged in
var isAuthenticated = require("../config/middleware/isAuthenticated");
//
module.exports = function(app) {
//
  app.get("/", function(req, res) {
    // If the user already has an account send them to the members page
    if (req.user) {
      res.redirect("/members");
    }
    res.sendFile(path.join(__dirname, "../public/signup.html"));
  });
//
  app.get("/login", function(req, res) {
    // If the user already has an account send them to the members page
    if (req.user) {
      res.redirect("/members");
    }
    res.sendFile(path.join(__dirname, "../public/login.html"));
  });
//
  // Here we've add our isAuthenticated middleware to this route.
  // If a user who is not logged in tries to access this route they will be 
  //redirected to the signup page
  app.get("/members", isAuthenticated, function(req, res) {
    res.sendFile(path.join(__dirname, "../public/members.html"));
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

保存所有文件，然后用`npm start`或`node server.js`运行服务器。如果你的服务器失败了，在你的终端上检查错误，并检查本教程中的所有内容。看一看`*/public/js/members.js*`，你就能明白你需要什么来获取用户信息。Sequelize 自动为用户创建一个`id`，这样您就可以用它来处理数据库中其他表上的关联。
例如，如果您希望在一个分类网站上进行身份验证，您只需确保有一个模型，如插入数据库的发布、执行发布的 api 路由、在发布到数据库之前和之后获取页面的 html 路由。总是确保你通过任何你不希望用户在没有登录的情况下访问的 html 路径上的`isAuthenticated`。`isAuthenticated`如果使用，将始终检查访问。

我希望我的字面解释能够帮助您在不使用手柄或 MongoDB 的情况下解决身份验证问题。

理查德·德布拉