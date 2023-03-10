# 我的 Laravel 设置备忘单

> 原文：<https://dev.to/gabiglazberg/my-laravel-setup-cheatsheets-1ega>

1.  路由器。
2.  控制器。
3.  资源。
4.  管理 CMS。

在本文中，我将向您展示一些小代码，在我开始编码之前，我用它们来扩展我的 Laravel 应用程序。我的大部分 Laravel 项目的结构都是一样的，网站，api 和管理系统(内容管理系统)。

### 路由器。

*   网络。
*   api。
*   管理员。

转到 RouteServiceProvider 并添加这个名称空间。

```
 protected $namespace = 'App\Http\Controllers';
    protected $apiNamespace = 'App\Http\Controllers\Api';
    protected $webNamespace = 'App\Http\Controllers\Web';
    protected $adminNamespace = 'App\Http\Controllers\Admin'; 
```

现在将它添加到地图函数中。

```
 $this->mapApiRoutes();

        $this->mapWebRoutes();

        $this->mapAdminRoutes(); 
```

mapWebRoutes 函数将如下所示。

```
 protected function mapWebRoutes()
    {
        Route::middleware('web')
            ->as('web.')
            ->namespace($this->webNamespace)
            ->group(base_path('routes/web.php'));
    } 
```

mapApiRoutes 和 mapAdminRoutes 函数看起来与 mapWebRoutes 函数相似，只是添加了->prefix(' ')方法。不要忘记将必要的文件添加到 routes 目录中。

### 控制器

文件结构。

```
|-- app
    |-- Http
        |-- Controllers
            |-- Admin
                |-- BaseController.php
            |-- Api
                |-- BaseController.php
            |-- Web
                |-- BaseController.php
            |-- Controller.php 
```

Api/BaseController.php.

```
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class BaseController extends Controller
{
    /**
     * @OA\Info(title="Project Name Api", version="1.0.0")
     */
    protected $request;

    public function __construct(Request $request)
    {
        $this->request = $request;
    }

    public function successResponse($data, int $status = 200)
    {
        return response()->json(
            [
                'data'    => $data
            ], $status
        );
    }

    public function errorResponse($message = 'server error', int $status = 400)
    {
        return response()->json(
            [
                'data'    => $message
            ], $status);
    }
} 
```

我的每个 Api 控制器都将扩展 Api\BaseController。

### 资源

文件结构。

```
|-- resources
    |-- assets
        |-- admin
        |-- web
    |-- lang
        |-- en
        |-- he
    |-- views
        |-- admin
        |-- web 
```

从现在开始，我将把重点放在管理系统上，但是也可以把一些概念放到网上。

### 管理员

当我构建管理系统时，我关注的是:

*   管理插件。市场上有很多，大约 10 美元左右。
*   网络包。
*   rtl 有限公司。
*   观点。
*   侧栏。
*   toastr。
*   所有的基础:迁移、模型、认证、安全、中间件等等。(网上有许多关于它们的有用信息)。

```
|-- resources
    |-- assets
        |-- admin
            |-- images
            |-- js
            |-- sass
                |-- components
                |-- vendor
                    |-- bi-app
                |-- app.scss
                |-- main-ltr.scss
                |-- main-rtl.scss
            |-- someAdminSkinThatYouNeedToBuy
            |-- webpack.mix.js
            |-- package.json
        |-- web
    |-- lang
    |-- views
        |-- admin
            |-- auth
            |-- layouts
            |-- pages
            |-- partials
        |-- web 
```

resources/assets/admin/web pack . mix . js .

```
var mix = require('laravel-mix');

var adminPublicPath = '../../../public/assets/admin/';

mix
    .sass('sass/main-ltr.scss', adminPublicPath + 'css/app.css')
    .sass('sass/main-rtl.scss', adminPublicPath + 'css/app-rtl.css')

    .combine([
    'someAdminSkinThatYouBought/global/plugins/bootstrap/css/bootstrap-rtl.css',
    'someAdminSkinThatYouBought/global/plugins/font-awesome/css/font-awesome.css',
    'someAdminSkinThatYouBought/global/plugins/simple-line-icons/simple-line-icons.css',
    'someAdminSkinThatYouBought/global/plugins/bootstrap-switch/css/bootstrap-switch.css',
    ... 
```

文件的其余部分用于捆绑插件、js、图片、css 等。大部分是 bootstrap 和 jquery 插件。

#### css ltr rtl

资源/资产/管理/sass/_app.scss.

```
@import "variables";

// Bootstrap
@import './override-bootstrap';
@import 'custom';

//Component
@import 'component/login';
... 
```

资源/资产/管理/sass/main-ltr.scss.

```
@import 'vendors/bi-app/bi-app-ltr';
@import 'app'; 
```

resources/assets/admin/sass/main-RTL . scss:

```
@import 'vendors/bi-app/bi-app-rtl';
@import 'app'; 
```

资源/视图/管理/布局/app.blade.php.

```
 <link href="{{ url((config('app.direction') == 'rtl') ? 'assets/admin/css/app-rtl.css' : 'assets/admin/css/app.css') }}?v={{ env('version', time()) }}" rel="stylesheet" type="text/css" /> 
```

不要忘记在配置/应用程序中添加方向。

#### 侧栏

config/admin-sidebar.php.

```
<?php

return [

    'dashboard' => [
        'route'   => 'admin.dashboard',
        'icon'    => 'fa fa-dashboard',
        'display' => 'Dashboard',
    ],
] 
```

app/Http/Middleware/admin sidebar . PHP .

```
 public function handle($request, Closure $next)
    {
        $adminSideBar = config('admin-side-bar');

        View::share('adminSideBar', $adminSideBar);

        return $next($request);
    } 
```

routes/admin.php

```
Route::group([
    'middleware' => [
        'auth_admin', 'admin_side_bar'
    ],

], function () {
... 
```

最后，侧边栏正在使用中。resources/views/admin/partials/side-bar . blade . PHP .

```
 <ul class="nav nav-list">
            @foreach($adminSideBar as $sideBarItem)
                @if(isset($sideBarItem['items']))
                <li>
                    <a class="dashboard" href="javascript:;">
                        <i class="main-icon {{ array_get($sideBarItem, 'icon') }}"></i>
                        <span>{{ array_get($sideBarItem, 'display') }}</span>
                    </a>

                    <ul>
                        @foreach($sideBarItem['items'] as $adminSideBar => $sideBarItemChild)
                            <li>
                                <a href="{{ route(array_get($sideBarItemChild, 'route')) }}">{{ array_get($sideBarItemChild, 'display') }}</a>
                            </li>
                        @endforeach
                    </ul>
                </li>
                @else
                    <li>
                        <a class="dashboard" href="{{ route(array_get($sideBarItem, 'route')) }}">
                            <i class="main-icon {{ array_get($sideBarItem, 'icon') }}"></i>
                            <span>{{ array_get($sideBarItem, 'display') }}</span>
                        </a>
                    </li>
                @endif
            @endforeach
        </ul> 
```

*   toastr resources/views/admin/layouts/app . blade . PHP

```
 function showToast(message, title, type) {
        if(!type)
            type = 'success';

        if(title)
            toastr[type](message, title);
        else
            toastr[type](message);
    }

    $(document).ready(function() {
        @if(session('toastr'))
            showToast('{{ session('toastr')['message'] }}',
                {!! isset(session('toastr')['title']) ? "'".session('toastr')['title']."'" : 'false' !!},
                {!!isset(session('toastr')['type']) ? "'".session('toastr')['type']."'" : 'false'!!});
        @endif
    }); 
```

app/Http/Support/helper . PHP .

```
if (! function_exists('toastr')) {
    function toastr($data) {
        session()->flash('toastr' ,$data);
    }
} 
```

不要忘记在 composer.json 中包含这个内容

```
"files": [
            "app/Support/helpers.php"
        ] 
```

toastr 函数的一个例子。

```
toastr([
    'type'      => 'success',
    'title'     => 'you did a great job',
    'message'   => 'thank you come again'
]); 
```

### 参考文献

*   时髦的
    *   [https://github.com/zircote/swagger-php](https://github.com/zircote/swagger-php)
    *   [https://github . com/zir cote/swagger-PHP/blob/master/Examples/pet store-3.0/controllers/pet . PHP](https://github.com/zircote/swagger-php/blob/master/Examples/petstore-3.0/controllers/Pet.php)
*   双应用程序:
    *   [https://anasnakawa . github . io/bi-app-sass/](https://anasnakawa.github.io/bi-app-sass/)
*   拉勒维尔-米克斯
    *   [https://www.npmjs.com/package/laravel-mix](https://www.npmjs.com/package/laravel-mix)