# 用 Laravel 和 Pusher 记录活动

> 原文：<https://dev.to/yordadev/logging-activity-with-laravel-and-pusher-1l52>

[![LaravelEliteSquad](img/daf54c92707a78584251248a45cd8f3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iE6O-3wN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/65tizs3gdh7w5tdi1tid.png)

日志记录活动很重要，原因有很多，我不太依赖包，每个人和他们的兄弟都有一个包，用于在不需要的时候记录活动。

我假设你已经安装了 laravel 并且已经开始写了。如果没有，或者你不确定 Laravel 是什么，去 www.laravel.com 看一看。

在我们开始之前，我们需要要求并安装 Pushers PHP SDK([https://pusher.com](https://pusher.com))，可以在[https://github.com/pusher/pusher-http-laravel](https://github.com/pusher/pusher-http-laravel)找到

## 安装

需要这个包，用作曲([https://getcomposer.org/](https://getcomposer.org/))，在你的项目根目录下。

```
$ composer require pusher/pusher-http-laravel 
```

将服务提供商添加到`providers`数组中的`config/app.php`。如果您使用的是 Laravel 5.5 或更高版本，则没有必要这样做。

```
Pusher\Laravel\PusherServiceProvider::class 
```

如果你愿意，你可以使用[门面](http://laravel.com/docs/facades)。将`config/app.php`中的引用添加到别名数组中。

```
'Pusher' => Pusher\Laravel\Facades\Pusher::class 
```

## 配置

Laravel 推进器需要连接配置。首先，您需要发布所有供应商资产:

```
$ php artisan vendor:publish --provider="Pusher\Laravel\PusherServiceProvider" 
```

这将在您的应用程序中创建一个`config/pusher.php`文件，您可以修改它来设置您的配置。此外，请确保在两次发布之间检查对这个包中的原始配置文件的更改。

最后，为了完成推手部分，请访问他们的网站，获取您的密钥，并将其放入 Laravel 的`.env`文件中。

首先，我们需要一个事件和模型。因此，让我们将下面的命令写入我们的控制台。

```
php artisan make:event "LogActivity" 
```

```
php artisan make:model "UserActivity" --migration 
```

现在，让我们设置迁移和模型

打开`App\UserActivity.php`，放入以下内容。

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class UserActivity extends Model
{
    protected $table = 'user_activities';

    protected $fillable = [
      'user_id',
      'type',
      'description',
      'archived'
    ];
} 
```

打开`App\database\migrations\TimeStamp_create_user_activities.php`，放入以下内容。

```
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUserActivitiesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
     public function up()
    {
        Schema::create('user_activities', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('user_id');
            $table->string('type');
            $table->longText('description');
            $table->boolean('archived')->default(false);
            $table->timestamps();
        });
    }
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('user_activities');
    }
} 
```

很好，现在我们有了模型和迁移设置，在开始记录活动之前，还有最后一件事要做。

打开`App\Events\LogActivity.php`，放入以下内容。

```
<?php

namespace App\Events;

use App\User;
use App\UserActivity;
use Pusher\Laravel\Facades\Pusher;
use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;

class LogActivity
{
    use Dispatchable, SerializesModels;
    public $user;
    public $type;
    public $description;
    public function __construct($user, $type, $description)
    {
        $this->data = [
            'user'        => $user, 
            'type'        => $type,
            'description' => $description,
            'now'         => \Carbon\Carbon::now()->toDateTimeString()
        ];
        $this->storeActivity();
        $this->broadcast();
    }
    private function storeActivity(){
        return UserActivity::create([
            'user_id'     => $this->data['user']->id,
            'type'        => $this->data['type'],
            'description' => $this->data['description']
        ]);
    }
    public function broadcast()
    {
        $this->data['user'] = $this->data['user']->name;
        return Pusher::trigger('staff-dashboard', 'logActivity', ['data' =>   $this->data]);
    }
} 
```

简单来说，`storeActivity()`执行数据库操作，`broadcast()`通过 Pusher 将活动实时转发到我的管理仪表板，在我的网站上创建活动的实时反馈。

那它，你准备好开始在行动中使用它。每当您想要记录活动时，您需要通过在控制器的顶部放置以下内容来确保包含该类。

```
use App\Event\LogActivity; 
```

现在要使用它，只需放下下面的:

```
event(new LogActivity(Obj $user, String $type, String $description)); 
```

下面是一些用例的例子:

```
event(new LogActivity($user, 'Log', $user->name . ' logged in.'));

event(new LogActivity($user, 'Alert', $user->name . ' has sent you a message.'));

event(new LogActivity($user, 'Security', $user->name . ' is probing route user.support.')); 
```

我希望你喜欢我在 [https://dev.to](https://dev.to) 上的第一篇帖子，让我知道你的想法或你使用的东西，如果它类似于此。