# 使用 Airtable 第 1 部分创建用户数据库

> 原文：<https://dev.to/cjwd/creating-a-user-database-with-airtable-part-1-44ij>

我以前写过一篇关于如何通过唯一字段值如电子邮件或用户名从 Airtable 获取记录的文章。我将使用一个实际的例子——用户数据库——来详细说明这一点。如果您正在使用 Airtable 作为后端构建应用程序，这可能会派上用场。让我们看一个我创建的示例用户数据库。

[示例用户数据库](https://airtable.com/shrlcd86mTnOvSC1m)

## 只是想要代码？

在 [Github](https://github.com/cjwd/user-database-airtable-tut) 上获得完整的工作演示。如果您想继续，下载 starter files 文件夹，并将其重命名为您想要的名称，然后运行 yarn 来安装依赖项。

起始文件已经有了应用程序的基本框架，比如登录和注册页面，以及显示这些页面的路径。本文将着重于构建用于创建用户和处理登录的用户控制器。

## 创建用户

当用户在注册页面上提交他们的信息时，它将向/user/add 路由发送一个 post 请求。这已在注册表的 action 属性中指定。让我们首先在 index.js 文件中为这个 post 请求创建一个路由。

```
// index.js
router.post("/user/add", userController.addUser); 
```

当用户向该路由发送请求时，它将调用 userController.js 中的 addUser 函数。

```
// userController.js

exports.addUser = (req, res, next) => {
  const { fullname, email, username } = req.body;

  table.create(
    {
      email,
      username,
      display_name: fullname
    },
    function(err, record) {
      if (err) {
        console.error(err);
        return;
      }
      req.body.id = record.getId();
      // store password
    }
  );
}; 
```

我们使用 Airtable 的 create 方法来创建记录。注意，我没有包括密码字段，因为在将密码保存到数据库之前，我们需要一个额外的步骤来散列密码。我们稍后会谈到这一点。

## 为电子邮件和用户名字段添加约束

我们在添加用户时遇到了一个小问题。现在，我们可以使用数据库中已经存在的电子邮件地址和用户名添加另一个用户。Airtable 目前没有为字段设置约束的功能。幸运的是，我们可以通过 API 从代码中实现。

为此，我们将创建一个助手函数，它将根据用户是否存在返回 true 或 false。

```
// userController.js

const findUser = async (email, username) => {
  let recordExists = false;
  const options = {
    filterByFormula: `OR(email = '${email}', username = '${username}')`
  };

  const users = await data.getAirtableRecords(table, options);

  users.filter(user => {
    if (user.get("email") === email || user.get("username") === username) {
      return (recordExists = true);
    }
    return (recordExists = false);
  });

  return recordExists;
}; 
```

然后我们需要从 addUser 函数中调用这个函数，只有当它返回 true 时我们才创建用户，否则我们用一条消息显示登录页面。addUser 函数现在变成了。

```
// userController.js

exports.addUser = async (req, res, next) => {
  const { fullname, email, username } = req.body;

  const userExists = await findUser(email, username);

  if (userExists) {
    res.render("login", {
      message: "Username or Email already exists!"
    });
    return;
  }

  table.create(
    {
      email,
      username,
      display_name: fullname
    },
    function(err, record) {
      if (err) {
        console.error(err);
        return;
      }
      req.body.id = record.getId();
      next();
    }
  );
}; 
```

## 存储用户的密码

我们正在成功创建用户记录，但我们不会存储用户的密码。我们可以存储输入的纯文本密码，但显然这样不好。我将使用 bcrypt 包散列用户的纯文本密码，并将散列后的密码存储在 Airtable base 中。

首先，我们需要安装 bcrypt npm 包，并在 userController.js 文件中需要它。如果您正在使用 starter 文件，这已经为您完成了。

然后，我们创建一个函数来创建散列密码，并将其存储在新创建的用户记录中。由于用户记录已经创建，我们需要*更新*用户记录以添加密码。为此，我们将使用 Airtable 的更新方法。

```
// userController.js

exports.storePassword = (req, res) => {
  const { password, id } = req.body;

  bcrypt.hash(password, 10, function(err, hash) {
    if (err) {
      console.error(err);
      return;
    }

    table.update(
      id,
      {
        password: hash
      },
      function(err) {
        if (err) {
          console.error(err);
          return;
        }
        res.render("login", {
          message: "Your account has been created!"
        });
      }
    );
  });
}; 
```

然后，我们需要修改 addUser 函数，以便在创建记录后立即调用该函数，这样我们就可以访问用户的电子邮件和密码。这样做将修改路由以在 addUser 之后调用 storePassword 函数，并在 addUser 函数中创建记录时调用 next()来调用我们的路由链中的下一个函数 storePassword 函数。

```
// index.js
router.post("/user/add", userController.addUser, userController.storePassword); 
```

```
// userController.js
exports.addUser = (req, res, next) => {
  const { fullname, email, username } = req.body;

  const userExists = await findUser(email, username);

  if (userExists) {
    res.render("login", {
      message: "Username or Email already exists!"
    });
    return;
  }

  table.create(
    {
      email,
      username,
      display_name: fullname
    },
    function(err, record) {
      if (err) {
        console.error(err);
        return;
      }
      req.body.id = record.getId();
      // The user has been successfully create, let's encrypt and store their password
      next();
    }
  );
}; 
```

## 登录用户

现在让我们创建用户登录的流程。登录表单向该路由/用户/授权发送一个 post 请求。

```
// index.js
router.post("/user/auth", userController.authenticate); 
```

我们将创建一个名为 authenticate 的功能，通过电子邮件或用户名找到用户，并比较密码来决定是否让用户登录。

```
// userController.js
exports.authenticate = (req, res) => {
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
            res.render("profile", {
              user: user.fields
            });
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

这就完成了第 1 部分。你可以在 [Github](https://github.com/cjwd/user-database-airtable-tut) 上获得完整的工作代码。
在第 2 部分中，我们将实现一个简单的会话，在用户登录时保存用户数据。请关注第 2 部分👀。