# Laravel Passport，创建带身份验证的 REST API

> 原文：<https://dev.to/abdoumjr/laravel-passport-create-rest-api-with-authentication-5enk>

应用程序接口(API)是允许一个软件应用程序与另一个软件应用程序进行对话的代码片段，提供了一种通用语言。无论是允许最终用户跨多个应用程序的无缝体验，还是允许数据从一个应用程序传递到另一个应用程序，API 在过去几年都发生了革命性的变化。

如果你是一个初学者，你正在学习并弄清楚如何制作 api 并保护它们，那么你来对地方了，在这篇文章中我将向你展示如何设置 API 认证。

什么是护照？Laravel Passport 是一个完整的 OAuth2 服务器实现，它旨在使基于 Laravel 的 web 应用程序通过 API 应用认证变得更加容易。

我们开始吧

设置 laravel 并安装 composer 后，请遵循以下步骤:

1-通过 Composer 软件包管理器安装 Passport:

作曲家需要护照

passport 包将注册它自己的数据库迁移。

2-迁移 passport 表:

php 工匠迁移

3-安装 passport:

php artisan passport:安装

此命令将创建生成安全访问令牌所需的加密密钥。

4-配置 passport:

将 Laravel\Passport\HasApiTokens 特征添加到您的 App\Usermodel 中。

→ /project/app/User.php

[https://thepracticaldev.s3.amazonaws.com/i/ovyjolcls53zenwm2m7m.png](https://thepracticaldev.s3.amazonaws.com/i/ovyjolcls53zenwm2m7m.png)

在 AuthServiceProvider 的 boot 方法中调用 Passport::routes 方法

→/project/app/Providers/authserviceprovider . PHP

[https://thepractical dev . S3 . Amazon AWS . com/I/3 yqsq 0 ut 0 lm 6 ik 36t 124 . png](https://thepracticaldev.s3.amazonaws.com/i/3yqsq0ut0lm6ik36t124.png)

将 api 身份验证防护的驱动程序选项设置为 passport

→ /project/config/auth.php

[https://thepractical dev . S3 . Amazon AWS . com/I/xjecpwgz 8 x 14 BZ 9ms 7 ox . png](https://thepracticaldev.s3.amazonaws.com/i/xjecpwgz8x14bz9ms7ox.png)

5-创建路线

→/project/routes/api.php

使用照明\ Http \请求；
/*
|————————
|
| API 路线
| — — — — — — —】您可以在这里注册 API 路线进行申请。这些
|路由由组内的 RouteServiceProvider 加载，该组
|被分配到“api”中间件组。享受构建您的 API 吧！
|
*/
Route::post(' log in '，' auth controller @ log in ')；
Route::post('register '，' auth controller @ register ')；
Route::中间件(' auth:api')- > get('/user)，function(Request $ Request){
return $ Request->user()；
})；
6-创建控制器

PHP artisan make:controller AuthController
然后将下面的代码复制并粘贴到您的 auth controller 中:

命名空间 App \ Http \ Controllers
使用照明\ Http \请求；
使用 App \ Http \ Controllers \ Controller；
使用 App \用户；
使用照明\支撑\立面\验证；
使用验证器；
类 AuthController 扩展控制器
{
/**

*   login api *
*   @ return \ Illuminate \ Http \ Response*/public function log in(){ if(Auth::attempt([' email ' =>request(' email ')，' password ' =>request(' password ')){ $ user = Auth::user()；$ success[' token ']= $ user->create token(' myApp ')->access token；return response()->JSON([' success ' =>$ success)，200)；} else { return response()->JSON([' error ' =>' unauthorized ']，401)；} } /* *
*   注册 api *
*   @ return \ Illuminate \ Http \ Response */public function register(Request $ Request){ $ Validator = Validator::make($ Request-> all()，email ' = > ' required | email '，' password' => 'required '，' confirm _ password ' = > ' required | same:password '，])；if($ validator-> fails()){ return response()-> JSON([' error ' = > $ validator-> errors()]，401)；} $ input = $ request-> all()；$ input[' password ']= bcrypt($ input[' password '])；$ User = User::create($ input)；$ success[' token ']= $ user-> create token(' myApp ')-> access token；$ success[' name ']= $ user-> name；return response()-> JSON([' success ' = > $ success)，200)；} }

在您的应用程序可以颁发个人访问令牌之前，您需要创建一个个人访问客户端:

您需要创建个人访问令牌

php 工匠护照:客户-个人

最后，让我们试试我们的注册和登录功能:

php 工匠服务

对我来说，我使用基于 http 的 API 的失眠症来发送 HTTP 请求。

[https://thepractical dev . S3 . Amazon AWS . com/I/69j 0 jgndf 38s 5 jkfktc 5 . png](https://thepracticaldev.s3.amazonaws.com/i/69j0jgndf38s5jkfktc5.png)

通过发送一个包含所有所需数据的注册请求，我们可以看到来自 api 的成功响应，通过一个特殊的令牌，我们可以使用这个令牌与 api 进行通信。

现在，如果我们断开连接或令牌过期，我们可以再次登录并获取令牌，抛出登录 api:

[https://thepractical dev . S3 . Amazon AWS . com/I/qibwbe 4h 3s 3 xcopsqwdt . png](https://thepracticaldev.s3.amazonaws.com/i/qibwbe4h3s3xcopsqwdt.png)