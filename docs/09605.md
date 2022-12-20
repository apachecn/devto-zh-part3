# 异步队列处理期间验证的用户

> [https://dev . to/lito/users-authentic ation-during-El-processed-异步队列-99n](https://dev.to/lito/usuarios-autenticados-durante-el-procesado-asncrono-de-colas-99n)

Laravel(以及任何其他框架)允许我们使用队列来运行后台进程，这些进程可能会在某个时间阻止页面加载。

典型的队列流程是发送电子邮件。

发送电子邮件的具体问题是，一旦排队，您就无法控制执行情况，就像您在使用“`Jobs`”时一样。

如果出于某种原因需要对模板使用某些经过身份验证的用户数据，则必须在队列本身运行时进行身份验证。

例如，一个案例是使用名为`money`的`helper`方法，该方法获取经过认证的用户的货币，以便在前端显示带有适当符号的价格。这方面的问题是，不能用于例如发送订单正确反映货币的邮件:

```
<td>{{ money($item->price) }}</td> 
```

```
/**
 * @param mixed $value
 * @param int $decimals = 2
 *
 * @return string
 */
function money($value, int $decimals = 2): string
{
    return number($value, $decimals).app('currency')->symbol;
} 
```

```
<?php declare(strict_types=1);

namespace App\Providers;

use Illuminate\Support\Facades\Auth;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * @return void
     */
    public function register()
    {
        $this->singletons();
    }

    /**
     * @return void
     */
    protected function singletons()
    {
        $this->app->singleton('user', function () {
            return Auth::user();
        });

        $this->app->singleton('currency', function () {
            return app('user')->currency;
        });
    }
} 
```

就像你可以跟随`money`使用单曲`currency`，该单曲又通过`user`访问经过认证的用户，以获得会话引用。

这是因为在运行队列时没有会话，因此无法获取引用用户。

为了解决这一问题，我们可以应用一个简单的解决方案，如在运行每个队列之前对参考用户进行身份验证。

```
<?php declare(strict_types=1);

namespace App\Providers;

use Illuminate\Queue\Events\JobProcessing;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Queue;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * @return void
     */
    public function boot()
    {
        $this->queues();
    }

    /**
     * @return void
     */
    public function register()
    {
        $this->singletons();
    }

    /**
     * @return void
     */
    protected function singletons()
    {
        $this->app->singleton('user', function () {
            return Auth::user();
        });

        $this->app->singleton('currency', function () {
            return app('user')->currency;
        });
    }

    /**
     * @return void
     */
    protected function queues()
    {
        Queue::before(function (JobProcessing $event) {
            $command = $event->job->payload()['data']['command'];

            if (preg_match('/"App\\\Models\\\User";s:2:"id";i:([0-9]+)/', $command, $matches)) {
                Auth::loginUsingId($matches[1]);
            } else {
                Auth::logout();
            }
        });
    }
} 
```

使用此简单的修补程序，我们可以在开始执行每个事件之前对每个事件中引用的用户进行身份验证。

当然，这种解决办法可以根据每项任务的需要而进一步完善，例如，只适用于某些特定任务。