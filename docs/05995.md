# 拉勒维尔的门和政策简介

> 原文：<https://dev.to/devmelas/an-introduction-to-laravel-s-gates-and-policies-3jck>

鉴于`Authentication`允许我们验证访问资源的实体的身份，`Authorization`允许我们确定该实体是否对所述资源具有某些特权。

授权是指定经过身份验证的实体是否有权访问服务器上的特定资源的过程。由此得出的一个结论是，在实施授权之前，我们需要了解实体(身份验证)。

Laravel 提供了两种实现授权的机制——门和策略。

### 我们要建造什么

出于本教程的目的，我们将致力于一个简单的论坛-只有线程🤓。我们将有两种类型的用户——版主和用户。版主可以创建和更新主题，用户不能。只有线程创建者可以更新线程。

### 先决条件

要跟随本教程，您应该对 Laravel 及其认证系统有一个基本的了解。我们将尝试采用一个 TDD(测试驱动开发)系统来允许我们非常快速地测试我们的代码。你可以在这里了解更多关于 TDD [的信息。](https://laracasts.com/lessons/tdd-by-example)

为了获得启动代码，克隆这个库并签出到启动分支。运行`composer install`来获取依赖项。您会注意到用户和线程模型的迁移已经设置好了，并且必要的关系已经包含在模型中。您还会注意到，用户和线程模型的工厂已经建立，phpunit.xml 文件已经更新为使用 SQLite 并将数据存储在内存中。

最后，打开。env 文件，填写你的数据库参数并运行`php artisan migrate`，你就应该设置好了。

所以让我们开始吧。

## 城门

门基本上是闭包(闭包只是匿名函数)，它决定用户是否可以对资源执行给定的操作。

### 定义闸门

我们使用大门外观来定义大门。

```
 use Gate;

// ...

Gate::define("action", function($user) {
    // Authorization logic
}); 
```

Gate 定义闭包将总是接收当前通过身份验证的用户作为第一个参数。第二个参数是我们的闭包，在这里我们为指定的动作编写授权逻辑。这应该可以解析为一个布尔值。

我们还可以使用`class@method`样式定义登机口——与我们定义路线的方式相同，比如:

```
Gate::define('action', 'ClassName@methodName'); 
```

但是，稍后会详细介绍。

好的。让我们写一些代码。

将以下内容添加到 app/Providers 文件夹中 AuthServiceProvider.php 类的引导方法中。

```
 use Gate;

// ...

public function boot()
{
    $this->registerPolicies();

    Gate::define('create-thread', function($user) {
       return $user->role === 'moderator';
    });
} 
```

我们基本上是说，只有当用户的角色属性值为“版主”时，才应该允许用户创建线程(操作)。

### 利用闸门

好的。我们已经定义了我们的星门。我们如何利用它们来授权行动？

在此之前，我们需要做一些设置。

首先，我们需要创建我们的 ThreadsController。

```
 php artisan make:controller ThreadsController 
```

像这样给它添加一个存储方法:

```
public function store(Request $request) 
{

} 
```

接下来，在 web.php 文件中为这个动作设置一个路径，如下:

```
 Route::post('/threads', 'ThreadsController@store')->name('threads.store'); 
```

最后，让我们为这个特性写一个测试。

像这样创建一个测试:

```
 php artisan make:test CreatesThreadTest 
```

这将在`tests/Feature`文件夹中创建一个`CreatesThreadTest.php`文件。添加如下所示的两个测试:

```
 <?php

namespace Tests\Feature;

use App\Models\Thread;
use App\Models\User;
use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;

class CreatesThreadTest extends TestCase
{
    use RefreshDatabase;

    / @test */
    public function a_moderator_can_create_a_thread()
    {
        $moderatorUser = factory(User::class)->create(['role' => 'moderator']);

        $thread = factory(Thread::class)->make(['user_id' => null])->toArray();

        $this->actingAs($moderatorUser);

        $this->post('/threads', $thread)
            ->assertStatus(200);

        $this->assertDatabaseHas('threads', array_merge($thread, ['user_id' => $moderatorUser->id]));
    }

    / @test */
    public function a_normal_user_cannot_create_a_thread()
    {
        $normalUser = factory(User::class)->create(['role' => 'user']);

        $thread = factory(Thread::class)->make(['user_id' => null])->toArray();

        $this->actingAs($normalUser);

        $this->post('/threads', $thread)
            ->assertStatus(403);

        $this->assertDatabaseMissing('threads', array_merge($thread, ['user_id' => $normalUser->id]));
    }
} 
```

对于第一个测试，`a_moderator_can_create_a_thread`，我们创建了一个具有版主角色的用户，我们生成了一些线程数据，然后将这些数据发送到我们的 post `/threads`路由。我们期待一个 200 的成功状态响应，也期待数据库将有我们刚刚生成的创建数据。

对于第二个测试，`a_normal_user_cannot_create_a_thread`我们使用一个普通用户，我们预计线程创建会失败，并显示一个 403 未授权状态代码。我们还确保数据库不包含生成的线程数据。

在终端中键入以下命令来运行该测试:

```
vendor/bin/phpunit --filter CreatesThreadTest 
```

当然，我们预计我们的测试会失败。所以让我们修复这个🧩.

像这样更新 ThreadsController 的存储方法:

```
 public function store(Request $request)
{
    $thread = auth()->user()
                    ->threads()
                    ->create($request->all());

    return response()->json(['thread' => $thread], 200);
} 
```

这里，我们使用用户的线程关系来创建一个使用请求有效负载的新线程。

**注意**理想情况下，你应该为你的请求添加一些验证，但是因为本教程不是关于验证的，所以我跳过了这一点。

再次运行您的测试。

嘣！一个通过了。但是有一个失败了。这是因为，在这一点上，我们所有的用户都被允许创建一个线程。这就是星门授权的用武之地。现在更新你的存储方法，看起来像下面的代码片段。不要忘记`use Gate;`。

```
 public function store(Request $request)
{

    if (Gate::allows('create-thread')) {
        $thread = auth()->user()
                        ->threads()
                        ->create($request->all());

        return response()->json(['thread' => $thread], 200);
    }

    return response()->json(['message' => 'Unauthorized Action'], 403);
} 
```

再做一次测试。现在，应该都过去了。

我们使用`Gate::allows()`方法添加了一个条件。该方法接收动作的名称，并使用我们为其定义的授权逻辑来确定当前通过身份验证的用户是否有权继续执行该动作。

还有一个`Gate::denies()`方法，它确定用户是否**不**被允许执行第一个参数中指定的动作。此外，如果我们想要对不同于当前认证用户的用户执行授权检查，我们有`Gate::forUser($user)->allows()`和
`Gate::forUser($user)->denies()`。

## 政策

策略是围绕特定模型或资源组织授权逻辑的类。这意味着通过策略，我们试图将所有控制对一个模型执行动作的能力的逻辑分组到一个类中。policy 类包含为不同操作定义授权逻辑的方法。

### 生成策略

我们可以像这样使用 artisan 创建策略:

```
php artisan make:policy PolicyName 
```

命名约定是将单词 Policy 附加到相应的模型名称上。因此，我们的线程模型的策略将被称为 ThreadPolicy。上面的命令将在我们项目的`app/Policies`文件夹中创建一个`PolicyName.php`文件。

就像服务提供商一样，策略也需要注册。我们在`AuthServiceProvider`(再次这样做😌).我们使用`$policies`受保护的类变量，这是一个关联数组，将我们的模型映射到它们对应的策略。

```
 protected $policies = [
    ModelName::class => ModelPolicyName::class
]; 
```

**注意【Laravel 也支持策略的自动发现，只要遵循模型和策略的命名约定，并且模型和策略位于正确的目录中。**

现在，我们了解了基本情况，让我们编写自己的策略。

#### 第一步:我们创建它

```
php artisan make:policy ThreadPolicy 
```

#### 第二步:我们注册一下。

在`AuthServiceProvider.php`文件中，将`$policies`变量更新为

```
protected $policies = [
    App\Models\Thread::class => App\Policies\ThreadPolicy::class
]; 
```

#### 第三步:给策略添加授权逻辑的方法。

是时候写一些授权逻辑了。从我们的软件规格😇，我们提到只有创建了一个主题的版主才能更新它。我们还可以添加第二个子句，这个用户**必须**是版主。这意味着如果版主不再是版主，他们就不能再更新他们的帖子了。我们将在 ThreadPolicy 类的 update 方法中实现这一点。

```
public function update(User $user, Thread $thread)
{
    return $user->role === "moderator" && $thread->user_id === $user->id;
} 
```

这里我们返回一个布尔值，它决定用户是否既是版主又是线程的创建者。

对于某些数据库，即使 thread user_id 字段被指定为 int，该值也将作为字符串返回，并且三重相等检查将不起作用。我们需要显式地将字段转换为整数，我们在`app/Models/Thread.php`文件中这样做:

```
protected $casts = ['user_id' => 'integer']; 
```

#### 步骤 4:在我们的 ThreadsController 中添加一个更新方法

```
public function update(Request $request, Thread $thread)
{

} 
```

如上所示，我们使用路由模型绑定将一个要更新的线程实例注入到控制器方法中。

#### 第五步:为线程更新添加路由

```
// web.php

Route::post('/threads/{thread}', 'ThreadsController@update')->name('threads.update'); 
```

#### 第六步:为线程更新添加一些测试

正如承诺的那样，我们将走 TDD 路线。运行`php artisan make:test UpdatesThreadTest`并更新创建的测试文件，如下所示:

```
 <?php

namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;
use App\Models\User;
use App\Models\Thread;

class UpdatesThreadTest extends TestCase
{
    use RefreshDatabase;

    / @test */
    public function a_moderator_can_update_his_thread()
    {
        $moderatorUser = factory(User::class)->create(['role' => 'moderator']);

        $thread = factory(Thread::class)->create(['user_id' => $moderatorUser->id]);

        $update = [
            'title' => 'Updated Title'
        ];

        $this->actingAs($moderatorUser);

        $this->post("/threads/{$thread->id}", $update)
            ->assertStatus(200);

        $this->assertDatabaseHas('threads', array_merge($update, [
            'user_id' => $moderatorUser->id,
            'description' => $thread->description
        ]));
    }

    / @test /
    public function a_moderator_cannot_update_another_moderators_thread()
    {
        $moderatorUser = factory(User::class)->create(['role' => 'moderator']);

        $thread = factory(Thread::class)->create();

        $update = [
            'title' => 'Updated Title'
        ];

        $this->actingAs($moderatorUser);

        $this->post("/threads/{$thread->id}", $update)
            ->assertStatus(403);

        $this->assertDatabaseMissing('threads', array_merge($update, [
            'user_id' => $moderatorUser->id,
            'description' => $thread->description
        ]));
    }

    /** @test /
    public function a_former_moderator_cannot_update_his_thread()
    {
        $formerModerator = factory(User::class)->create(['role' => 'user']);

        $thread = factory(Thread::class)->create(['user_id' => $formerModerator->id]);

        $update = [
            'title' => 'Updated Title'
        ];

        $this->actingAs($formerModerator);

        $this->post("/threads/{$thread->id}", $update)
            ->assertStatus(403);

        $this->assertDatabaseMissing('threads', array_merge($update, [
            'user_id' => $formerModerator->id,
            'description' => $thread->description
        ]));
    }
} 
```

我们有三个测试:

1.  确保版主可以更新他们的帖子
2.  确保版主不能更新其他版主的帖子
3.  确保普通用户不能更新线程，即使该线程是他们创建的。

当然，当我们用`vendor/bin/phpunit --filter UpdatesThreadTest`运行这些测试时，三个测试都失败了😡。让我们解决这个问题。

首先，我们需要用更新线程的逻辑来更新我们的控制器更新方法。

```
 // app/Http/Controllers/ThreadsController.php

public function update(Request $request, Thread $thread)
{
     $thread->update($request->all());

     return response()->json(['thread' => $thread->fresh()], 200);
} 
```

再次运行我们的测试，我们只有一个通过。最后两个失败了。这是因为我们还没有使用授权。

经过身份验证的用户的实例具有 can 和 cannot 方法，可用于检查用户是否可以对资源或模型执行某个操作。我们将利用 can 方法，根据策略更新方法中包含的登录来执行授权。

```
 public function update(Request $request, Thread $thread)
{
    if ($request->user()->can('update', $thread)) {

        $thread->update($request->all());

        return response()->json(['thread' => $thread->fresh()], 200);
    }

    return response()->json(['message' => 'Unauthorized Action'], 403);
} 
```

我们添加了一个条件来检查用户是否可以更新线程，现在，我们的测试应该通过了。

`can`方法将动作作为它的第一个参数。第二个参数将是我们正在执行操作的模型的实例。然而，有些动作不需要模型实例，例如 create 动作。那样的话，我们就这样传递模型类的名字:`can('create','App\Models\Thread')`。

**先不说这个:**记得我之前提到过，我们可以使用用于路由的 ClassName@method 样式来定义门，可以这样做:

```
Gate::define('update-post', 'App\Policies\ThreadPolicy@update'); 
```

我还想提一下，我们可以使用`Gate::resource() method`来定义资源门。这将通过指定的查看、创建、更新、删除操作将一个门映射到一个策略类。例如，`update`将被映射到`ResourcePolicyClass@update`。

```
Gate::resource('threads', 'App\Policies\ThreadPolicy'); 
```

### 最后的考虑

我们已经做了很多，但这就像是你可以用门和政策做的所有事情的一小部分。你可以在路由中间件、控制器甚至视图中使用它们。

具体来说，你可以从 Laravel 官方文档[这里](https://laravel.com/docs/5.8/authorization)了解更多关于 Laravel 授权、门和政策的信息。

### 结论

在本教程中，我们学习了什么是授权，以及它与身份验证有何不同。我们还看到了如何使用策略和门在 Laravel 中实现基本授权。我们也看到了一些测试驱动的开发，相信我，我们也学会了如何规范一个小项目...或者不是。😇

去授权吧。`Happy coding`🙂