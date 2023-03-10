# 设计 Laravel REST API:最佳实践

> 原文：<https://dev.to/mshossain110/designing-laravel-rest-api-best-practices-3ilg>

在现代应用程序中，API 是应用程序的主要特征之一。它不仅用于创建移动应用程序、桌面应用程序，对于自托管的 web 应用程序也很重要。

现在前端和后端应用开发使用 Vue 或者 React 前端框架有很大的变化。所有新的应用程序都要单页应用程序。这就是为什么在后端实现 API 是主要的特性。

基本上，API 是一个以特殊格式返回数据的接口，任何类型的应用程序，无论是 Android 应用程序还是 web 应用程序，都可以理解。

当开发一个 API 时，你需要考虑一些遵循不同开发者的最佳实践。我对 web 教程进行了研究，并整理出一些我在 Laravel 应用程序中遵循的最佳实践。

### **开发 Laravel rest API 的最佳实践**

#### 编码标准

它不仅与设计 rest API 相关，而且与所有类型的应用程序相关。每当我开始构建某个应用程序时，我就开始阅读该应用程序的编码标准，即使我以前也阅读过。它迫使我遵循编码标准。其中一些与在 Laravel 中设计 rest API 有关。

#### 使用 routes/api.php 文件进行 api 路由

Laravel be default 有一个独立的`routes/api.php`文件，它不同于通常的`routes/web.php`文件。我认为我们必须在这个文件中存储我们的 API 路由。它有一个板载应用中间件(可以在`app/Http/Kernel.php`的$middlewareGroups 变量中看到，在`api`下)和一个前缀`/api`，所以所有定义的路由对`/api`都是可用的。

#### 带 api 前缀的路线名称

我喜欢做的是为整个 API 设置一个组 route，这样我就可以用`api`通过它们的名字来访问路由。前缀。

```
Route::get('/users', "API\V1\UserController@index")->name("users"); 
```

此路线的 URL 可以使用`route('users')`获得，但它可能与`web.php`冲突。

```
Route::group(['as' => 'api.'], function () {
  Route::get('/users', 'API\V1\UserController@index)->name('users');
}); 
```

这样，您将在`route('api.users')`拥有它。

如果您使用路由命名，如果您编写测试，如果您计划更改 URL 位置并保留路由名称，您不需要到处替换 URL。

#### 用复数描述资源

当你写资源路径时，你应该写复数。

```
Route::resource('/users', 'API\V1\UserController’)->name('users');
// mysite.com/api/v1/customers 
```

#### API 版本控制

记住 API 将被其他程序使用。我们偶尔会更新服务器上的代码，但这会破坏客户端的应用程序。这就是 API 版本控制派上用场的时候了。如果你的 API 不能公开访问，你可以保持默认。考虑到版本控制，我们上面的 URL 如下所示。

```
mysite.com/api/v1/customers 
```

#### 使用 Passport 代替 JWT 进行 API 认证

我个人喜欢认证用的护照。它是可靠的，并且支持 Laravel 开发人员。

#### 使用变压器

我总是使用转换器以一种格式获取数据。虽然 Laravel 有自己的变形金刚，但我个人很喜欢`league/fractal`

#### 响应代码和错误处理

HTTP 状态代码将简化给 API 用户的反馈。此外，您必须给出一个带有响应的服务器消息。为了处理错误，转换和保存相似的数据集，我编写了一个负责处理错误的控制器。

#### 限制在给定时间段内来自同一个 IP 地址的请求数量

如果它暴露在公众面前，那么它就是垃圾邮件、机器人和滥用的目标。您可以将每秒发出的请求限制为 5 个。任何超过这一点的都可能是自动化程序滥用 API 的迹象。

```
<?php

namespace App\Http\Controllers\Api\V1;

use League\Fractal\Manager;
use League\Fractal\Resource\Item;
use App\Http\Controllers\Controller;
use League\Fractal\Resource\Collection;
use League\Fractal\Pagination\IlluminatePaginatorAdapter;

class ApiController extends Controller
{
    /**
     * @var int $statusCode
     */
    protected $statusCode = 200;

    const CODE_WRONG_ARGS = 'GEN-FUBARGS';
    const CODE_NOT_FOUND = 'GEN-LIKETHEWIND';
    const CODE_INTERNAL_ERROR = 'GEN-AAAGGH';
    const CODE_UNAUTHORIZED = 'GEN-MAYBGTFO';
    const CODE_FORBIDDEN = 'GEN-GTFO';
    const CODE_INVALID_MIME_TYPE = 'GEN-UMWUT';

    /**
     * @var Manager $fractal
     */
    protected $fractal;

    public function __construct()
    {
        $this->fractal = new Manager;

        if (isset($_GET['include'])) {
            $this->fractal->parseIncludes($_GET['include']);
        }
    }

    /**
     * Get the status code.
     *
     * @return int $statusCode
     */
    public function getStatusCode()
    {
        return $this->statusCode;
    }

    /**
     * Set the status code.
     *
     * @param $statusCode
     * @return $this
     */
    public function setStatusCode($statusCode)
    {
        $this->statusCode = $statusCode;

        return $this;
    }

    /**
     * Repond a no content response.
     * 
     * @return response
     */
    public function noContent()
    {
        return response()->json(null, 204);
    }

    /**
     * Respond the item data.
     *
     * @param $item
     * @param $callback
     * @return mixed
     */
    public function respondWithItem($item, $callback, $message = 'Successfully')
    {
        $resource = new Item($item, $callback);

        $data = $this->fractal->createData($resource)->toArray();

        $data['message'] = $message;

        return $this->respondWithArray($data);
    }

    /**
     * Respond the collection data.
     *
     * @param $collection
     * @param $callback
     * @return mixed
     */
    public function respondWithCollection($collection, $callback, $message = 'Successfully')
    {
        $resource = new Collection($collection, $callback);

        $data = $this->fractal->createData($resource)->toArray();
        $data['message'] = $message;

        return $this->respondWithArray($data);
    }

    /**
     * Respond the collection data with pagination.
     *
     * @param $paginator
     * @param $callback
     * @return mixed
     */
    public function respondWithPaginator($paginator, $callback, $message = 'Successfully')
    {
        $resource = new Collection($paginator->getCollection(), $callback);

        $resource->setPaginator(new IlluminatePaginatorAdapter($paginator));

        $data = $this->fractal->createData($resource)->toArray();
        $data['message'] = $message;

        return $this->respondWithArray($data);
    }

    /**
     * Respond the data.
     *
     * @param array $array
     * @param array $headers
     * @return mixed
     */
    public function respondWithArray(array $array, array $headers = [])
    {
        return response()->json($array, $this->statusCode, $headers);
    }

    /**
     * Respond the message.
     * 
     * @param string $message
     * @return json
     */

    public function respondWithMessage ($message) {
        return $this->setStatusCode(200)
            ->respondWithArray([
                    'message' => $message,
                ]);
    }

    /**
     * Respond the error message.
     * 
     * @param string $message
     * @param string $errorCode
     * @return json
     */
    protected function respondWithError($message, $errorCode, $errors = [])
    {
        if ($this->statusCode === 200) {
            trigger_error(
                "You better have a really good reason for erroring on a 200...",
                E_USER_WARNING
            );
        }

        return $this->respondWithArray([
            'errors' => $errors,
            'code' => $errorCode,
            'message' => $message,
        ]);
    }

    /**
     * Respond the error of 'Forbidden'
     * 
     * @param string $message
     * @return json
     */
    public function errorForbidden($message = 'Forbidden', $errors = [])
    {
        return $this->setStatusCode(500)
                    ->respondWithError($message, self::CODE_FORBIDDEN, $errors);
    }

    /**
     * Respond the error of 'Internal Error'.
     * 
     * @param string $message
     * @return json
     */
    public function errorInternalError($message = 'Internal Error', $errors = [])
    {
        return $this->setStatusCode(500)
                    ->respondWithError($message, self::CODE_INTERNAL_ERROR, $errors);
    }

    /**
     * Respond the error of 'Resource Not Found'
     * 
     * @param string $message
     * @return json
     */
    public function errorNotFound($message = 'Resource Not Found', $errors = [])
    {
        return $this->setStatusCode(404)
                    ->respondWithError($message, self::CODE_NOT_FOUND, $errors);
    }

    /**
     * Respond the error of 'Unauthorized'.
     * 
     * @param string $message
     * @return json
     */
    public function errorUnauthorized($message = 'Unauthorized', $errors = [])
    {
        return $this->setStatusCode(401)
                    ->respondWithError($message, self::CODE_UNAUTHORIZED, $errors);
    }

    /**
     * Respond the error of 'Wrong Arguments'.
     * 
     * @param string $message
     * @return json
     */
    public function errorWrongArgs($message = 'Wrong Arguments', $errors = [])
    {
        return $this->setStatusCode(400)
                    ->respondWithError($message, self::CODE_WRONG_ARGS, $errors);
    }
} 
```

#### 安全

你应该注意的最大问题是安全。Laravel 应用程序很容易保护它，但如果做得不好，你可能会被黑客攻击。在 Laravel 中，您可能需要使用 Laravel Passport——它属于 Laravel 生态系统，支持通过该应用 ID——应用机密之类的东西进行身份验证，以便获得访问令牌，无论是模拟某人还是服务器，无论是后端还是前端。

#### 太难理解了？联系我！

如果您有更多关于 Laravel 的问题，如果您需要任何与 Laravel 相关的信息，您可以联系我们。

#### 说谢谢。

如果有任何错误，请在评论中指出。如果你喜欢，你可以鼓励我说谢谢。

帖子[设计 Laravel REST API:最佳实践](https://mshossain.me/blog/designing-laravel-rest-api-best-practices/)最先出现在[的 Shahadat Hossain](https://mshossain.me) 上。