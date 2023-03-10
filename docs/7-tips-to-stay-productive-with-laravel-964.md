# 使用 Laravel 保持高效工作的 7 个技巧

> 原文：<https://dev.to/anwar_nairi/7-tips-to-stay-productive-with-laravel-964>

Laravel 内在的力量在于细节。让我给你这 7 个技巧来保持你的代码可维护性。

*   [1。控制器应该...嗯，控制](#1-controllers-should-well-control)
*   [2。使用申请表验证清洁剂](#2-cleaner-validation-with-form-requests)
*   [3。在您的控制器中键入提示您的型号](#3-type-hint-your-models-in-your-controllers)
*   [4。集中式错误处理](#4-centralized-error-handling)
*   [5。收集...无处不在的收藏](#5-collections-collections-everywhere)
*   [6。用资源分解您的路线](#6-factorize-your-routes-with-resources)
*   7 .[。使用变异函数的精益代码](#7-lean-code-with-mutators)

# 1。控制器应该...嗯，控制

见过/写过这样的大男孩吗？

```
use Validator;
use Kreait\Firebase\Factory;
use Kreait\Firebase\ServiceAccount;

class AuthenticationController extends Controller {
  public function index(Request $request) {
    if ($request->wantsJson()) {
      $validator = Validator::validate($request->all(), [
        'token' => 'required|filled|string'
      ]);

      if ($validator->fails()) {
        return response()->json('wrong token', 400);
      }

      $serviceAccount = ServiceAccount::fromArray(config('firebase'));
      $firebase = (new Factory)->withServiceAccount($serviceAccount)
        ->create()
        ->getAuth();

      try {
        $firebase->verifyIdToken($token);

        return response()->json('ok');
      } catch(Exception $exception) {
        app('sentry')->captureException($exception);

        return response()->json('unauthorized', 403);
      }
    } else {
      abort(403);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个呢:

```
use App\Firebase;
use App\Http\Requests\AuthenticationRequest;

class AuthenticationController extends Controller {
  public function index(AuthenticationRequest $request) {
    if ($request->wantsJson()) {
      if (Firebase::tokenValid($request->input('token'))) {
        return response()->json('ok');
      } else {
        return response()->json('unauthorized', 403);
      }
    } else {
      abort(403);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用几样东西来帮助我们将业务逻辑从其余部分中解放出来。我将在这篇文章中介绍[请求表单](https://laravel.com/docs/5.8/validation#creating-form-requests)，它帮助我从控制器中移除了验证步骤，以及[错误处理](https://laravel.com/docs/master/errors)。

但是重要的是，**不要把业务逻辑放在你的控制器中**。[控制器](https://laravel.com/docs/master/controllers)应该保持轻量级，只使用它需要的工具来决定如何重新路由请求。最终，它只是一个请求控制器，仅此而已。任何商业决策都应该基于独立的[车型](https://laravel.com/docs/master/eloquent)。

是的，您可以创建不一定与表相关的类。在我的例子中，我**使用一个类对**的一个“问题”进行了建模:Firebase 认证。

# 2。表单请求的更清晰验证

我喜欢被拉弗尔认可。我喜欢它们干净而富有表现力的使用方式。典型的验证用户购物车的例子。

```
use App\Product;
use App\Order;
use App\Session;

class CartController extends Controller {
  public function store(Request $request) {
    $request->validate([
      'id' => 'required|exists:product,id'
    ]);

    // Request passed, time to save the data

    $productId = $request->input('id');
    $product = Product::find($productId);

    Order::insert([
      'productId' => $product->id
    ]);

    Session::flash('success', 'Order completed!');

    return response()->view('product');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但我们可以简化更多。首先，您需要创建一个表单请求。

```
php artisan make:request CartRequest 
```

Enter fullscreen mode Exit fullscreen mode

这将在`app/Http/Requests/CartRequest.php`上创建一个文件。打开一看，是这样的:

```
use Illuminate\Foundation\Http\FormRequest;

class CartRequest extends FormRequest {
  public function authorize() {
    return true; // Unless you have Guards, set it to true.
  }

  public function rules() {
    return [
      'id' => 'required|exists:product,id'
    ];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以在我们的控制器中使用它:

```
use App\Product;
use App\Order;
use App\Session;
use App\Http\Requests\CartRequest;

class CartController extends Controller {
  public function store(CartRequest $request) {
    // The request is validated using our CartRequest

    $productId = $request->input('id');
    $product = Product::find($productId);

    Order::insert([
      'productId' => $product->id
    ]);

    Session::flash('success', 'Order completed!');

    return response()->view('product');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个去除模糊并专注于业务算法的好方法。最好的是，您可以对共享相似验证的模型重用表单请求。

```
use App\Http\Requests\ProductRequest;

class Shoe extends Controller {
  public function index(ProductRequest $request) {
    // ...
  }
}

class Clothing extends Controller {
  public function index(ProductRequest $request) {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![HAPPY ROBERT REDFORD GIF](img/d9ba691d0abfce612473bcf2fbf49e8b.png)](https://i.giphy.com/media/xSM46ernAUN3y/giphy.gif)

# 3。在您的控制器中键入提示您的模型

有时我写了 3 公里长的路线，我的控制者跟着走...

```
Route::get('/customer/{customerId}/contract/{contractId}/invoice/{invoiceId}', 'CustomerContractInvoiceController@show'); 
```

Enter fullscreen mode Exit fullscreen mode

```
use App\Customer;
use App\Contract;
use App\Invoice;

class CustomerContractInvoiceController extends Controller {
  public function show(Request $request, int $customerId, int $contractId, int $invoiceId) {
    $customer = Customer::findOrFail($customerId);
    $contractId = Contract::findOrFail($contractId);
    $invoice = Invoice::findOrFail($invoiceId);

    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Laravel 可以使用[路由模型绑定](https://laravel.com/docs/5.8/routing#route-model-binding)为您处理模型获取。反抗是徒劳的...

```
use App\Customer;
use App\Contract;
use App\Invoice;

class CustomerContractInvoiceController extends Controller {
  public function show(Request $request, Customer $customer, Contract $contract, Invoice $invoice) {
    $customerName = $customer->name;

    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样你可以直接使用雄辩的模型。为此，Laravel 需要您在模型上正确指定`$primaryKey`属性，以防这不是`id` :

```
use Illuminate\Database\Eloquent\Model;

class Customer extends Model {
  protected $primaryKey = 'customerId';
} 
```

Enter fullscreen mode Exit fullscreen mode

# 4。集中式错误处理

我厌倦了不得不检查我的控制器中的错误。

```
use Validator;
use Session;

class NewsletterController extends Controller {
  public function store(Request $request) {
    $validator = new Validator($request->all(), [
      'email' => 'required|email'
    ]);

    if ($validator->fails) {
      return redirect()->back()->withInput()->withErrors($validator);
    }

    Session::flash('success', 'We publish each week so stay tuned!');

    return redirect()->route('newsletter.success');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我配置了我的全局[错误处理](https://laravel.com/docs/master/errors)来利用表单请求抛出的异常。为此，请转至`app/Exceptions/Handler.php`。

```
class Handler extends ExceptionHandler {
  public function report(Exception $exception) {
    // This is where you log to your error dashboard like Sentry, ...

    parent::report($exception);
  }

  public function render($request, Exception $exception) {
    // Here we manage errors globally
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们要定制的方法是`render`。向像 [Sentry](https://sentry.io) 这样的错误管理应用推送通知是很棒的。

让我们全局处理验证错误。

```
use Illuminate\Validation\ValidationException;

class Handler extends ExceptionHandler {
  public function report(Exception $exception) {
    // This is where you log to your error dashboard like Sentry, ...

    parent::report($exception);
  }

  public function render($request, Exception $exception) {
    if ($exception instanceof ValidationException) {
      return redirect()->back()->withInput()->withErrors($exception->errors());
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以让它一次性管理导航和 AJAX 错误。

```
use Illuminate\Validation\ValidationException;

class Handler extends ExceptionHandler {
  public function report(Exception $exception) {
    // This is where you log to your error dashboard like Sentry, ...

    parent::report($exception);
  }

  public function render($request, Exception $exception) {
    // The request have a "Accept: application/json"
    // This is an AJAX request
    if ($request->wantsJson()) {
      if ($exception instanceof ValidationException) {
        return response()->json($exception->errors(), 400);
      }
    }
    // This is a normal form validation 
    else {
      if ($exception instanceof ValidationException) {
        return redirect()->back()->withInput()->withErrors($exception->errors());
      }      
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![BEST GIF THUMBS UP GIF](img/cbb3c1dd31e45e122f7c7464d6c9a9bd.png)](https://i.giphy.com/media/111ebonMs90YLu/giphy.gif)

# 5。收集...到处都是收藏

我对 PHP 感到遗憾的是缺乏 OOP。Javascript 的流行是如此的酷。

```
const popularPosts = posts.filter(post => post.views > 5000)->map(post => post.title); 
```

Enter fullscreen mode Exit fullscreen mode

在 Laravel，最好的功能之一是收藏。雄辩的模型变得如此方便，我们可以流利地表演许多事情。

```
use App\Post;

$popularPosts = Post::all()->filter(function($post) { 
  return $post->views > 5000;
})->map(function($post) {
  return $post->title;
}); 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以在雄辩的用法之外使用它！

```
$menus = [
  [ 'placement' => 'left', 'text' => 'Home' ],
  [ 'placement' => 'left', 'text' => 'About us' ],
  [ 'placement' => 'right', 'text' => 'Contact' ]
];

$rightMenus = collect($menus)->filter(function($menu) {
  return $menu['placement'] === 'right';
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 6。用资源分解您的路线

我不知道这是否发生在你身上，但有一次我在工作中开发了一个 web 应用程序(仍在生产中)，我的`routes/web.php`变成了这样:

```
// routes/web.php

Route::get('/customer', 'CustomerController@index');
Route::get('/customer/create', 'CustomerController@create');
Route::get('/customer/{id}', 'CustomerController@show');
Route::get('/customer/{id}/edit', 'CustomerController@edit');
Route::get('/customer/{id}/delete', 'CustomerController@delete');
Route::get('/customer/{id}/phone', 'CustomerPhoneController@index');
Route::get('/customer/{id}/phone/create', 'CustomerPhoneController@create');
Route::get('/customer/{customerId}/phone/{phoneId}', 'CustomerPhoneController@show');
Route::get('/customer/{customerId}/phone/{phoneId}/edit', 'CustomerPhoneController@edit');

Route::post('/customer', 'CustomerController@store');
Route::post('/customer/{id}/phone', 'CustomerPhoneController@store');

Route::put('/customer/{id}', 'CustomerController@update');
Route::put('/customer/{customerId}/phone/{phoneId}', 'CustomerPhoneController@update');

Route::delete('/customer/{id}', 'CustomerController@destroy');
Route::delete('/customer/{customerId}/phone/{phoneId}', 'CustomerPhoneController@destroy'); 
```

Enter fullscreen mode Exit fullscreen mode

...这是摘录。我还管理客户地址、客户电子邮件、客户公司、客户合同、客户合同发票，...

[![OH NO OOPS GIF BY HALLMARK CHANNEL<br>
](img/7dbe60fafd1a42fc1fb7f45aa8059cb1.png)](https://i.giphy.com/media/628gjKuwSNGkX9inxX/giphy.gif)

幸运的是，Laravel 支持我们的[资源控制器](https://laravel.com/docs/5.8/controllers#resource-controllers)。

```
// routes/web.php

Route::resource('customer', 'CustomerController');
Route::resource('customer.phone', 'CustomerPhoneController'); 
```

Enter fullscreen mode Exit fullscreen mode

终于有新鲜空气了...

参考资料自动将第一个参数绑定到 4 个常见的 HTTP 方法。事实上如果你运行

```
php artisan route:list 
```

Enter fullscreen mode Exit fullscreen mode

您将得到以下输出。

| 领域 | 方法 | 上呼吸道感染 | 名字 | 行动 | 中间件 |
| --- | --- | --- | --- | --- | --- |
|  | 得到，头 | /客户 | 客户索引 | app \ Http \ controller \ customer controller @ index | 网 |
|  | 得到，头 | /客户/{客户} | 客户.展示 | app \ Http \ Controllers \ customer controller @ show | 网 |
|  | 得到，头 | /customer/{ customer]/编辑 | 客户.编辑 | app \ Http \ controller \ customer controller @ edit | 网 |
|  | 得到，头 | /客户/{客户}/电话 | 客户.电话.索引 | app \ Http \ Controllers \ CustomerPhoneController @ index | 网 |
|  | 得到，头 | /客户/{客户/电话/{电话} | 客户.电话.展示 | app \ Http \ Controllers \ CustomerPhoneController @ show | 网 |
|  | 得到，头 | /客户/{客户}/电话/{电话}/编辑 | 客户.电话.编辑 | app \ Http \ Controllers \ CustomerPhoneController @ edit | 网 |
|  | 邮政 | /客户 | customer.store | app \ Http \ controller \ customer controller @ store | 网 |
|  | 邮政 | /客户/{客户}/电话 | customer.phone.store | app \ Http \ Controllers \ CustomerPhoneController @ store | 网 |
|  | 放，补丁 | /客户/{客户} | 客户.更新 | app \ Http \ controller \ customer controller @ update | 网 |
|  | 放，补丁 | /客户/{客户}/电话/{电话} | 客户.电话.更新 | app \ Http \ Controllers \ CustomerPhoneController @ update | 网 |
|  | 删除 | /客户/{客户} | 客户.销毁 | app \ Http \ Controllers \ customer controller @ destroy | 网 |
|  | 删除 | /客户/{客户}/电话/{电话} | 客户.电话.销毁 | app \ Http \ Controllers \ CustomerPhoneController @ destroy | 网 |

同样，Laravel 为您提供了保护，所以您可以使用这个命令行来创建您的资源控制器:

```
php artisan make:controller CustomerController --resource 
```

Enter fullscreen mode Exit fullscreen mode

这将自动为您创建上述方法！

```
// app/Http/Controllers/CustomerController.php

use Illuminate\Http\Request;

class CustomerController extends Controller
{
  public function index() {}

  public function create() {}

  public function store(Request $request) {}

  public function show($id) {}

  public function edit($id) {}

  public function update(Request $request, $id) {}

  public function destroy($id) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

和...如果你创建了一个模型，你也可以在资源模式下创建它的关联控制器，这样 Laravel 就会为你输入提示你的控制器。

```
php artisan make:model Customer --resource --controller 
```

Enter fullscreen mode Exit fullscreen mode

```
// app/Http/Controllers/CustomerController.php

use App\Customer;
use Illuminate\Http\Request;

class CustomerController extends Controller
{
  public function index() {}

  public function create() {}

  public function store(Request $request) {}

  public function show(Customer $customer) {}

  public function edit(Customer $customer) {}

  public function update(Request $request, Customer $customer) {}

  public function destroy(Customer $customer) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Productivity level: over 9000 meme](img/6ba169f5d5c437212bcca92ad444bb69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k-8ax-yl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2w5ttm.jpg)

# 7。带有变异函数的精益代码

有时，混合记录的多个字段有助于创建有意义的内容。例如，在客户视图中:

```
<!-- resources/views/customer/show.blade.php -->

@extends('layout/logged')
@section('content')
  <h1>viewing customer {{ $customer->firstName }} {{ $customer->lastName }}</h1>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

Mutators 将帮助您抽象您的业务逻辑。

```
// app/Customer.php

use Illuminate\Database\Eloquent\Model;

class Customer extends Model {
  protected $table = 'customer';

  public function getNameAttribute() {
    return "{$this->firstName}  {$this->lastName}";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要访问一个`$customer->name`属性，您需要创建一个类似上面的函数，形式为`public function get<yourattribute>Attribute() {}`。当你收集到一个雄辩的模型时，Laravel 会给你提供这个属性。

```
<!-- resources/views/customer/show.blade.php -->

@extends('layout/logged')
@section('content')
  <h1>{{ $customer->name }}</h1>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

更好的是，你还可以从你的数据库中提取数据。

想象一下，在客户表中有一个名为`wantsNewsletter`的布尔字段。如您所知，MySQL 中不存在布尔类型，所以它用这个列来模拟它:

```
wantsNewsletter TINYINT(1) NOT NULL DEFAULT 0 
```

Enter fullscreen mode Exit fullscreen mode

0 代表`false`，1 代表`true`。在您的雄辩模型中，您可以要求 Laravel 将其转换为布尔:

```
// app/Customer.php

use Illuminate\Database\Eloquent\Model;

class Customer extends Model {
  protected $table = 'customer';
  protected $casts = [
    'wantsNewsletter' => 'boolean'
  ];

  public function getNameAttribute() {
    return "{$this->firstName}  {$this->lastName}";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用三重等于运算符来执行验证。

```
// app/Console/Commands/SendNewsletter.php

use Illuminate\Console\Command;
use App\Mail\NewsletterMail;
use App\Customer;

class SendNewsletter extends Command {
  protected $signature = 'newsletter:send';

  public function handle() {
    $customers = Customer::all();

    $customers = $customers->filter(function($customer) {
      $customer->wantsNewseletter === true;
    });

    foreach($customers as $customer) {
      Mail::to($customer->email)->send(new NewsletterMail($customer));
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

所有这一切都让人不知所措。不要连续应用这些。试着在你觉得正确的事情和你能做什么来改进你的代码库之间找到一个平衡点。

改编从 [Vuex 网站](https://vuex.vuejs.org/)上找到的一段话:

> 这些建议就像眼镜:你需要的时候就会知道。

我希望当我学会这些技术时，你会和我一样惊讶。如果你学到了新的东西，如果你有我没有列出的其他建议，或者如果我可以用这些建议做得更好，请在评论中告诉我。

快乐的优化！

照片由来自 [Pexels](https://www.pexels.com) 的[尿布](https://www.pexels.com/@nappy)拍摄