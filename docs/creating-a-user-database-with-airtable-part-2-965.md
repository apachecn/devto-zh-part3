# 使用 Airtable 创建用户数据库第 2 部分

> 原文：<https://dev.to/cjwd/creating-a-user-database-with-airtable-part-2-965>

在第 1 部分中，我们学习了如何使用散列密码将用户添加到 Airtable 数据库中。在第 2 部分中，我们将学习如何用会话持久化用户数据。

## 只是想要代码？

在 [Github](https://github.com/cjwd/user-database-airtable-tut) 上获得完整的工作演示。如果您想继续，下载 starter files 文件夹，并将其重命名为您想要的名称，然后运行 yarn 来安装依赖项。别忘了去看看《T2》第一部。

## 配置会话

我们将使用由 express 团队开发的 [express-session](https://github.com/expressjs/session) 中间件来添加简单的 Express 会话。

```
yarn add express-session 
```

然后我们需要 app.js 文件中的包，并配置 express 来使用中间件。要了解有关使用 express-session 的更多信息，请阅读 Github repo 上的[文档](https://github.com/expressjs/session)。

```
// app.js
const session = require("express-session");

// session config
app.use(
  session({
    secret: "arandomstring",
    resave: false,
    saveUninitialized: true
  })
); 
```

## 保护一条路线

现在，我们已经包含并配置了快速会话，让我们保护我们的用户配置文件路由，以便注销的用户无法访问该页面。然后，我们将在用户登录时创建一个会话，将他们的用户详细信息存储在会话变量中。

```
// index.js
router.get("/profile", userController.isLoggedIn, appController.getProfile); 
```

现在让我们创建 isLoggedIn 函数。

```
// userController.js
exports.isLoggedIn = (req, res, next) => {
  if (req.session && req.session.authenticated) {
    next();
    return;
  }

  res.redirect("/login");
}; 
```

该函数检查会话是否存在。如果用户成功登录，将会有一个会话。如果没有会话，用户将被重定向到登录页面。

## 创建会话

让我们编辑 userController.js 中的 authenticate 函数来创建会话。

```
// userController.js
exports.authenticate = (req, res, next) => {
  const { username, password } = req.body;
  const options = {
    filterByFormula: `OR(email = '${username}', username = '${username}')`
  };

  data
    .getAirtableRecords(table, options)
    .then(users => {
      users.forEach(function(user) {
        bcrypt.compare(password, user.get("password"), function(err, response) {
          if (response) {
            // Passwords match, response = true
            req.session.authenticated = user.fields;
            res.redirect("/profile");
          } else {
            // Passwords don't match
            console.log(err);
          }
        });
      });
    })
    .catch(err => {
      console.log(Error(err));
    });
}; 
```

现在，当用户名和密码匹配时(bcrypt 回调函数中的*),我们创建一个传递用户数据的会话，并将用户重定向到他们的个人资料页面。这将再次调用 isLoggedIn 函数，该函数现在通过了 if 条件，成功地将用户登录并重定向到他们的个人资料页面。让我们更新应用控制器中的 getProfile 函数，以使用会话中保存的用户数据。* 

```
// appController.js
exports.getProfile = (req, res) => {
  res.render("profile", {
    title: "Your Profile",
    user: req.session.authenticated
  });
}; 
```

在本系列的第三部分也是最后一部分，我们将创建一个密码重置工作流。敬请关注。