# 一个灵活的电子邮件节点认证模板。

> 原文：<https://dev.to/perkinsjr/a-flexible-node-authentication-boilerplate-with-email-504i>

我最近完成了一个节点认证的样板文件。我厌倦了为我的大多数节点项目创建新的身份验证，并决定社区可以使用轻量级(最少数量的节点模块)的样板文件，并准备用于几乎任何场景。

[![How?](img/46f739859b53d98710bdc59c5825f94e.png)](https://i.giphy.com/media/l4FGnHKwXZpdYu208/giphy.gif)

首先，目前有两种选择:纯节点实现和容器版本。
查看我的 [GitHub](https://github.com/perkinsjr/authentication_boilerplate/) 想要 docker 就选 docker 分支，想要纯节点就选 master。

请注意，存储数据需要 MongoDB。我们还需要用于密码重置的 sendgrid API

签出后，请确保在。包封/包围（动词 envelop 的简写）

```
APP_SECRET=somekey
SEND_GRID_EMAIL= your@email.com
SEND_GRID_USER=[sendgriduser]
SEND_GRID_PASS=[sendgridpass] 
```

节点实现

1.  npm 安装
2.  npm 运行开发

Docker 实现

1.  码头工人建造
2.  docker 运行图像

API 路线

所有路线都通过下面的
/api/auth/

要运行 register，您需要呼叫 post:

```
/api/auth/register 

 {
  "fullName":"aname",
  "email":"email@email.com",
  "companyName": "name",
  "password": "password"
 } 
```

要运行登录，您需要调用 post:

```
/api/auth/login 

{
  "email":"email@email.com",
  "password": "password"
 } 
```

要运行忘记密码，您需要致电 post:

```
/api/auth/forgot-password 

{
  "email":"email@email.com",
 } 
```

要运行重置密码，您需要呼叫 post:

```
/api/auth/reset-password

{
  "password":"newpassword",
  "confirmPassword":"newpassword",
  reset_password_token: "reset_token_from_email"
 } 
```

这是最基本的，我试图让它尽可能的轻便，希望你能在你的下一个应用中找到它的用处，加速你的开发。也可以随意分叉，让它成为自己的。如果你有一个问题提交和问题，我会尽快解决