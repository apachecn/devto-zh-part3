# 用 Laravel 资源返回状态代码

> 原文：<https://dev.to/jakzaizzat/return-status-code-with-laravel-resource-244k>

Laravel 资源是 Laravel 中最好的特性之一。它使得修改结果的输出变得容易。如果您使用的是 API，您需要有一个状态代码来让前端知道请求的状态。

状态代码示例
202 -已创建
401 -未授权
404 -未找到

您可以在这里找到 HTTP 状态代码列表。

例如，您有一个用户模型，控制器想要返回特定 id 的用户信息。您希望让前端知道您根据状态代码找到了数据。

Api.php

```
Route::get('user/{user}', 'UserController@show'); 
```

UserController.php

```
public function show(User $user) {
    return (new UserResource($user))
        ->response()
        ->setStatusCode(202);
} 
```

## 数据库里没有 ID 怎么办？

可能是这样的。我们应该首先检查 ID 在我们的数据库中是否可用，如果不可用，它应该返回一个状态代码 **not found** 。如果是，我们可以继续使用以前的系统。

UserController.php

```
public function show(User $user) {

    // If user not found
    if(!$user){
    return response(array(
        'message' => 'Not Found',
     ), 410);
    } else {
        // If user is found
        return (new UserResource($user))
            ->response()
            ->setStatusCode(202);
    }
} 
```

在调试过程中，如果 else 块有问题，它将被写入。最佳实践是首先检查否定结果，然后最后返回正确的输出。如果 if else 变得更深，对开发者来说就更糟了。

### 重构

```
public function show(User $user) {

    // If user not found
    if(!$user){
    return response(array(
        'message' => 'Not Found',
     ), 410);
    }

    // If user is found
    return (new UserResource($user))
        ->response()
        ->setStatusCode(202);
} 
```

希望它能帮助你理解如何通过 laravel 资源或不通过它发送状态代码。

原文提交自[我的博客](https://jakzaizzat.com/how-to-return-status-code-with-laravel-resource/)