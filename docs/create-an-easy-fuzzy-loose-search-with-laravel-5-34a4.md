# 使用 Laravel 5 创建一个简单的模糊/松散搜索

> 原文：<https://dev.to/anwar_nairi/create-an-easy-fuzzy-loose-search-with-laravel-5-34a4>

# 我所说的“模糊”或“松散”搜索是什么？

这是一个很棒的特性，可以让您检索到与您的请求条件部分匹配的结果。

# 1。选择你的冠军

[![Shazam announce who he really is](img/34a903d4842dc88994b113b07cc2790b.png)](https://i.giphy.com/media/1YfqfpyCZBXGTcQFK0/giphy.gif)

首先，我们需要一个您想要执行搜索的模型。

对我来说，这将是我的[Schema.org](https://schema.org/docs/full.html)T2 类型。它们是表示概念的语义对象，比如:鞋店、产品、组织，...

# 2。创建 api 路由

使用`search`方法添加新路线:

```
// routes/api.php

use Illuminate\Http\Request;

Route::get('type/search/{search?}', 'TypeController@search'); 
```

Enter fullscreen mode Exit fullscreen mode

该路线将能够匹配如下路线:

*   `http://localhost:8000/api/type/search/acco`
*   `http://localhost:8000/api/type/search/accomodation`
*   `http://localhost:8000/api/type/search/store`
*   `http://localhost:8000/api/type/search/`
*   ...

`{search?}`中的问号表示该参数不是必需的。因此，您可能会遇到一个有效的 API 调用，如:

*   `http://localhost:8000/api/type/search`

# 3。创建一个控制器(如果您还没有)

如果您还没有`TypeController`，运行这个命令来启动并运行:

```
php artisan make:controller TypeController 
```

Enter fullscreen mode Exit fullscreen mode

您将在`app/Http/Controllers/TypeController.php`找到您的控制器

# 4。在控制器上添加逻辑

```
// app/Http/Controllers/TypeController.php

namespace App\Http\Controllers;

use App\Type;
use Illuminate\Http\Request;;

class TypeController extends Controller {
  public function search(Request $request, $search = "") {
    if ($request->wantsJson()) {
      return response()->json(Type::search($search));
    } else {
      abort(403);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们只是说:

如果请求是使用 AJAX 发起的，则返回静态方法`Type::search()`的结果。

否则，一定是用户在浏览器上手动浏览路线`http://localhost:8000/api/type/search`，所以显示 403 页面。

# 5。搜索逻辑

[![I am something of a scientist myself meme](img/66ef7ef5300148a05d26e46fee697600.png)](https://i.giphy.com/media/Zy7s96dP38MlQe3OjG/giphy.gif)

在您的`Type`型号上写下搜索方法:

```
// app/Type.php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Type extends Model {
  protected $table = "type";
  protected $primaryKey = "id";

  public static function search(string $search) {
    $columns = ["id", "name", "partOf", "comment"];

    // If the search is empty, return everything
    if (empty(trim($search))) {
      return static::select($columns)->get();
    }
    // If the search contains something, we perform the fuzzy search 
    else {
      $fuzzySearch = implode("%", str_split($search)); // e.g. test -> t%e%s%t
      $fuzzySearch = "%$fuzzySearch%"; // test -> %t%e%s%t%s%

      return static::select($columns)->where("name", "like", $fuzzySearch)->get();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这里的技巧是使用`LIKE` SQL 操作符来做这件事。

# 6。利润$$$

我正在使用[失眠客户端应用](https://insomnia.rest/)来检查我的模糊搜索是否有效。

[![Fuzzy type search showcase using Insomnia client app](img/3d04840c1b92b13e75884cd89b25108a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ayLmmwtx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5l4i2oaipewhgekai49r.png)

不要忘记在您的请求中添加标题`Accept: application/json`。

# 黑掉它

一个改进是允许在搜索中使用空格，这样:

*   `http://localhost:8000/api/type/search/acc+o`
*   `http://localhost:8000/api/type/search/shoe s tore`

仍然会返回结果。

[![Is this still possible meme](img/500b99ef81af3ac5551a77ec1128045a.png)](https://i.giphy.com/media/ZsXGPHnHmQYCY/giphy.gif)

只需在您的类型模型`search`方法中更改这一行:

```
// app/Type.php

// $fuzzySearch = implode("%", str_split($search));
$fuzzySearch = implode("%", str_split(str_replace(" ", "", $search))); 
```

Enter fullscreen mode Exit fullscreen mode

你可以做的另一件事是使用`->orWhere("column2", "like", $fuzzySearch)`向搜索中添加几列。你可以在专用的 [Laravel 文档](https://laravel.com/docs/5.8/queries#where-clauses)上找到`orWhere`。

就性能而言，如果您正在处理大量数据，这将不符合您的需求。这就是像 Algolia 搜索这样的服务存在的原因。

如果您没有所需的预算，您仍然可以使用`MATCH` SQL 操作符(关键字名称可能因您选择的数据库引擎而异)。我发现了一篇非常干净的文章，解释了如何使用 Laravel 为那些正在寻找这种模糊搜索的免费更快替代方案的人设置这种搜索方式[这里](https://arianacosta.com/php/laravel/tutorial-full-text-search-laravel-5/)，如果你不担心数据库模式上的一些额外变化。

# 结论

我认为这个技巧可以让你为你的用户提供一些很棒的搜索体验，给他们带来更多的搜索结果，只需要很少的努力。

我也很好奇，看看我亲爱的 Laravelers 是否有其他东西可以给他们的用户带来模糊搜索。请在评论中告诉我。

再见了，快乐编码:)

[![Cat on a truck with a hat](img/ae9fa4896886b6ee91eb5a98cb11745e.png)](https://i.giphy.com/media/FJje57DTS5KrC/giphy.gif)