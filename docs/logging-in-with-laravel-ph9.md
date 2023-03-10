# 使用 Laravel 登录

> 原文：<https://dev.to/mariowhowrites/logging-in-with-laravel-ph9>

欢迎来到我的 Laravel 身份验证系列的迟来的第 2 部分！如前所述，在这一章中，我们将深入探讨认证过程的具体细节。我们还将看看登录过程，试图更好地理解 Laravel 应用程序中的认证过程是如何工作的。

如果您还没有阅读 Laravel 认证系列的第 1 部分，您可以在介质[这里](https://medium.com/@mariowhowrites/laravel-authentication-under-the-hood-78064b5b89e6)找到它。

我将从开发人员使用`make:auth`命令后添加到 Laravel 应用程序中的代码开始。从这里开始，我们将自上而下地工作，在检查使这样的登录工作流成为可能的代码之前，查看典型登录请求背后的代码。

## 脚手架验证

首先，让我们看看驱动`make:auth`命令的代码:

```
// Illuminate\Auth\Console\AuthMakeCommand

/**
* Execute the console command.
*
* @return void
*/
public function handle()
{
    $this->createDirectories();

    $this->exportViews();

    if (! $this->option('views')) {
        file_put_contents(
            app_path('Http/Controllers/HomeController.php'),
            $this->compileControllerStub()
        );

        file_put_contents(
            base_path('routes/web.php'),
            file_get_contents(__DIR__.'/stubs/make/routes.stub'),
            FILE_APPEND
        );
    }

    $this->info('Authentication scaffolding generated successfully.');
} 
```

对于我们的目的来说，重要的部分是通过`file_put_contents`附加到我们的`web.php`文件中的路线。这就是 Laravel 添加路由来支持默认身份验证体验的地方。

事实证明，附加的存根只有两行长:

```
Auth::routes();

Route::get('/home', 'HomeController@index')->name('home'); 
```

更方便的是，`routes`方法是唯一直接在 Auth facade 上定义的方法。检查该方法表明，我们的大部分身份验证逻辑包含在 Router 类中:

```
public static function routes(array $options = [])
{
    static::$app->make('router')->auth($options);
} 
```

相应的路由器功能接受一系列选项，指定是否应该包括注册、重置和验证路由。使用这些选项，路由器向我们的应用程序添加了许多路由:

```
// Illuminate\Routing\Router

/**
    * Register the typical authentication routes for an application.
    *
    * @param  array  $options
    * @return void
    */
public function auth(array $options = [])
{
    // Authentication Routes...
    $this->get('login', 'Auth\LoginController@showLoginForm')->name('login');
    $this->post('login', 'Auth\LoginController@login');
    $this->post('logout', 'Auth\LoginController@logout')->name('logout');

    // Registration Routes...
    if ($options['register'] ?? true) {
        $this->get('register', 'Auth\RegisterController@showRegistrationForm')->name('register');
        $this->post('register', 'Auth\RegisterController@register');
    }

    // Password Reset Routes...
    if ($options['reset'] ?? true) {
        $this->resetPassword();
    }

    // Email Verification Routes...
    if ($options['verify'] ?? false) {
        $this->emailVerification();
    }
} 
```

因为我们试图遵循一个典型的登录请求的过程，所以一旦登录请求被发送，我们将只关注与 Laravel 接口的路由。特别是，我们关心的是`POST login`路由，它重定向到`LoginController@login`方法。

查看登录控制器，我们没有看到太多:

```
class LoginController extends Controller
{
    /*
    |--------------------------------------------------------------------------
    | Login Controller
    |--------------------------------------------------------------------------
    |
    | This controller handles authenticating users for the application and
    | redirecting them to your home screen. The controller uses a trait
    | to conveniently provide its functionality to your applications.
    |
    */

    use AuthenticatesUsers;

    /**
    * Where to redirect users after login.
    *
    * @var string
    */
    protected $redirectTo = '/home';

    /**
    * Create a new controller instance.
    *
    * @return void
    */
    public function __construct()
    {
        $this->middleware('guest')->except('logout');
    }
} 
```

但是，请注意这里有一个`AuthenticatesUsers`特征，这意味着该特征中可用的任何方法也可以在这个控制器中访问。让我们看看是否能找到一个`login`方法:

```
// Illuminate\Foundation\Auth\AuthenticatesUsers

/**
    * Handle a login request to the application.
    *
    * @param  \Illuminate\Http\Request  $request
    * @return \Illuminate\Http\RedirectResponse|\Illuminate\Http\Response|\Illuminate\Http\JsonResponse
    *
    * @throws \Illuminate\Validation\ValidationException
    */
public function login(Request $request)
{
    $this->validateLogin($request);

    // If the class is using the ThrottlesLogins trait, we can automatically throttle
    // the login attempts for this application. We'll key this by the username and
    // the IP address of the client making these requests into this application.
    if ($this->hasTooManyLoginAttempts($request)) {
        $this->fireLockoutEvent($request);

        return $this->sendLockoutResponse($request);
    }

    if ($this->attemptLogin($request)) {
        return $this->sendLoginResponse($request);
    }

    // If the login attempt was unsuccessful we will increment the number of attempts
    // to login and redirect the user back to the login form. Of course, when this
    // user surpasses their maximum number of attempts they will get locked out.
    $this->incrementLoginAttempts($request);

    return $this->sendFailedLoginResponse($request);
} 
```

幸运的是，与许多 Laravel 源代码一样，顶级 API 利用描述性方法名称来尽可能地类似于工作流的简单语言描述。

首先，验证登录请求。我们需要确保包含用户名和密码，并且两者都是字符串:

```
protected function validateLogin(Request $request)
{
    $request->validate([
        $this->username() => 'required|string',
        'password' => 'required|string',
    ]);
} 
```

从那里，我们转移到登录限制。如[认证文档](https://laravel.com/docs/5.8/authentication#login-throttling)中所述，如果用户多次登录失败，登录限制可以防止用户向您的服务器发送垃圾登录请求。

下面是`hasTooManyLoginAttempts`方法:

```
protected function hasTooManyLoginAttempts(Request $request)
{
    return $this->limiter()->tooManyAttempts(
        $this->throttleKey($request), $this->maxAttempts()
    );
} 
```

我们看到节流逻辑的主要部分被委托给由`limiter()`返回的类，它是 Laravel 的`Cache/RateLimiter`的一个实例。在看这个之前，让我们先来考察一下在`ThrottlesLogins` : `throttleKey`和`maxAttempts`中可以找到的两种方法。

```
protected function throttleKey(Request $request)
{
    return Str::lower($request->input($this->username())).'|'.$request->ip();
} 
```

我们看到，通过将登录请求中提供的用户名(默认情况下，这是用户的电子邮件)与用户登录设备的 IP 地址相结合，创建了一个限制密钥。

接下来，`maxAttempts()`:

```
public function maxAttempts()
{
    return property_exists($this, 'maxAttempts') ? $this->maxAttempts : 5;
} 
```

这一点很容易理解:除非开发人员定义了自定义的`maxAttempts`属性，否则将使用默认值 5。请记住，您应该在应用程序中的`LoginController`上定义这个属性，而不是在`ThrottlesLogins`特征本身上定义。由于`LoginController`同时使用了`ThrottlesLogins`和`AuthenticatesUsers`特征，控制器上可用的任何属性也可用于其特征。

考虑到这一点，我们来看看`Cache/RateLimiter`中的`tooManyAttempts`方法:

```
public function tooManyAttempts($key, $maxAttempts)
{
    if ($this->attempts($key) >= $maxAttempts) {
        if ($this->cache->has($key.':timer')) {
            return true;
        }

        $this->resetAttempts($key);
    }

    return false;
} 
```

我们看到`RateLimiter`类大量使用了 Laravel 的本地缓存功能。它使用上面定义的`throttleKey`来检查缓存，并查看用户是否登录了太多次。如果有，并且那个键上还有计时器，`tooManyAttempts`返回 true，用户被拒绝进入。否则，如果用户的节流键上没有计时器，这意味着他们的节流期已经到期。在这种情况下，`RateLimiter`重置用户的登录尝试，给他们一个干净的石板，避免基于陈旧尝试的限制。

当然，如果从`tooManyAttempts`方法返回`true`，则不允许用户登录，Laravel 相应地做出响应。首先，使用 Laravel 的事件系统触发一个新的`Lockout`事件。之后，用户会收到一个锁定响应，指定允许再次尝试之前的秒数:

```
protected function sendLockoutResponse(Request $request)
{
    $seconds = $this->limiter()->availableIn(
        $this->throttleKey($request)
    );

    throw ValidationException::withMessages([
        $this->username() => [Lang::get('auth.throttle', ['seconds' => $seconds])],
    ])->status(429);
} 
```

### 防御性编程

至此，已经确认用户已经提供了登录信息并且当前没有被锁定，我们在第一章中学习的认证过程开始发挥作用。此时，该逻辑被封装在`LoginController@login`方法中:

```
if ($this->attemptLogin($request)) {
    return $this->sendLoginResponse($request);
} 
```

请注意“快乐之路”是如何封装在自己的条件逻辑中的:这是防御性编程的一个很好的例子。

说白了，就是上面的意思。任何一段代码的“快乐之路”都是当一切按计划进行时发生的事情。这是金砖之路，神话般的目的地，作为开发人员，我们意识到没有什么地方比得上良好的用户体验。

在我们的例子中,“快乐之路”是一次成功的登录:用户提供正确的凭证并被允许访问我们的应用程序。`sendLoginResponse`方法包含幸福之路出现的必要动作。因此，重要的是此方法只能在条件中找到。当且仅当,`attemptLogin()`的返回值是真的，那么我们的用户就可以走这条快乐的路了。

让我们用更多的上下文来看看这段代码:

```
if ($this->attemptLogin($request)) {
    return $this->sendLoginResponse($request);
}

$this->incrementLoginAttempts($request);

return $this->sendFailedLoginResponse($request); 
```

如果用户遵循快乐的路径，那么`incrementLoginAttempts`和`sendFailedLoginResponse`方法永远不会被调用，因为执行会在`sendLoginResponse`方法上停止。

因此，如果用户已经到达调用`incrementLoginAttempts`的点，那么我们知道登录尝试没有成功。

这种策略的关键，以及一般的防御性编程的关键，是对失败的假设。快乐之路由一个条件来保护，防御性编程假设这个条件返回 falsy。这种方法的主要好处是，意外拒绝成功登录比意外允许欺诈性登录要好。

意外拒绝有效登录没有什么坏处:您的用户总是可以再试一次。相比之下，允许非法访问，因为你的程序假设幸福的道路，可以导致您的网站的帐户受到损害。

在这种情况下，防御性编程很像防御性驾驶:假设最坏的情况并据此进行规划是你成功的最佳机会，不管结果如何。

### 尝试登录

抛开这些不谈，让我们来看看实际的登录尝试代码:

```
protected function attemptLogin(Request $request)
{
    return $this->guard()->attempt(
        $this->credentials($request),
        $request->filled('remember')
    );
} 
```

为了执行我们的身份验证检查，我们使用由请求和`credentials()`方法提供的凭证:

```
protected function credentials(Request $request)
{
    return $request->only($this->username(), 'password');
} 
```

默认情况下，`username()`返回`email`，这意味着用户的电子邮件和密码被用于身份验证检查。

这是登录验证过程和我们在第一章中检查的代码之间的第一个主要区别:当我们之前检查`TokenGuard`时，登录尝试的默认防护是`SessionGuard`。让我们来看看`SessionGuard@attempt`的方法:

```
public function attempt(array $credentials = [], $remember = false)
{
    $this->fireAttemptEvent($credentials, $remember);

    $this->lastAttempted = $user = $this->provider->retrieveByCredentials($credentials);

    if ($this->hasValidCredentials($user, $credentials)) {
        $this->login($user, $remember);

        return true;
    }

    $this->fireFailedEvent($user, $credentials);

    return false;
} 
```

注意工作中的防御性编程:只有当用户拥有有效凭证时，他们才能登录。否则，将触发失败登录事件，并拒绝用户访问。和以前一样，通过假设失败为默认，这种方法减少了误报的可能性，从而保护了用户数据。

除此之外，这里还有三个主要的函数需要考虑:`retrieveByCredentials`、`hasValidCredentials`和`login`。

`retrieveByCredentials`附属于护卫队的提供者。正如我们在上一章中了解到的，提供者负责定义您的应用程序如何在身份验证的上下文中定义“用户”。Laravel 提供了两个现成的提供者。两者都试图从数据库中获取用户，但它们在如何从数据库行创建用户上有所不同:

1.  驱动程序将你的用户塑造成一个雄辩的模型。
2.  `database`驱动程序将您的用户转换成一个`GenericUser`模型，它是一个简单的类包装器，包裹着一个数据库行，只包含必要的认证方法。

`eloquent`驱动程序是默认的，所以我们先来看看:

```
// within Illuminate\Auth\EloquentUserProvider...

public function retrieveByCredentials(array $credentials)
{
    if (empty($credentials) ||
        (count($credentials) === 1 &&
        array_key_exists('password', $credentials))) {
        return;
    }

    // First we will add each credential element to the query as a where clause.
    // Then we can execute the query and, if we found a user, return it in a
    // Eloquent User "model" that will be utilized by the Guard instances.
    $query = $this->newModelQuery();

    foreach ($credentials as $key => $value) {
        if (Str::contains($key, 'password')) {
            continue;
        }

        if (is_array($value) || $value instanceof Arrayable) {
            $query->whereIn($key, $value);
        } else {
            $query->where($key, $value);
        }
    }

    return $query->first();
} 
```

首先，我们确保有足够的信息来获取用户。由于不能仅通过未散列的密码来获取用户，因此如果只提供了密码，该方法将返回 null。

接下来，使用您在`eloquent`驱动程序旁边定义的任何模型创建一个新的雄辩查询。在绝大多数情况下，这将是所有 Laravel 应用程序的默认`User`模型。

从那里，对于在`credentials`数组中提供的每个值，一个新的`WHERE`子句被添加到查询中。匹配结果查询的第一个数据库条目作为用户模型返回。

让我们将它与`DatabaseUserProvider`中的`retrieveByCredentials`方法进行比较:

```
// In Illuminate\Auth\DatabaseUserProvider

public function retrieveByCredentials(array $credentials)
{
    if (empty($credentials) ||
        (count($credentials) === 1 &&
        array_key_exists('password', $credentials))) {
        return;
    }

    // First we will add each credential element to the query as a where clause.
    // Then we can execute the query and, if we found a user, return it in a
    // generic "user" object that will be utilized by the Guard instances.
    $query = $this->conn->table($this->table);

    foreach ($credentials as $key => $value) {
        if (Str::contains($key, 'password')) {
            continue;
        }

        if (is_array($value) || $value instanceof Arrayable) {
            $query->whereIn($key, $value);
        } else {
            $query->where($key, $value);
        }
    }

    // Now we are ready to execute the query to see if we have an user matching
    // the given credentials. If not, we will just return nulls and indicate
    // that there are no matching users for these given credential arrays.
    $user = $query->first();

    return $this->getGenericUser($user);
} 
```

和以前一样，如果没有足够的凭证来成功获取用户，我们将中止。与`EloquentUserProvider`不同，我们建立了与数据库的直接连接，而不是依赖于雄辩来管理我们的连接。

一旦建立了连接，查询本身就以类似于`EloquentUserProvider`的方式建立起来。主要区别是查询的`first`方法的返回值。我们得到的不是用户模型，而是从数据库返回的原始值。然而，使用原始数组可能会很麻烦并且容易出错，所以查询的数组在返回到 SessionGuard 之前被转换成一个`GenericUser`模型。

幸运的话，上面的解释演示了不同的提供者如何使用不同的数据源和方法来实现相同的结果。虽然两个提供者的`retrieveByCredentials`方法最终都返回一个用户对象，但这两个类在选择查询数据库和构造用户对象的方式上有所不同。

### 验证凭证

有了我们的用户，是时候检查他们的密码了。这由 SessionGuard 的`hasValidCredentials`方法处理:

```
protected function hasValidCredentials($user, $credentials)
{
    return ! is_null($user) && $this->provider->validateCredentials($user, $credentials);
} 
```

如果`retrieveByCredentials`没有返回用户，我们可以安全地假设给定的凭证是无效的，我们返回 false。否则，确定如何验证凭证的责任就被传递给了提供者。在这种情况下，两个提供者以完全相同的方式实现方法:

```
public function validateCredentials(UserContract $user, array $credentials)
{
    $plain = $credentials['password'];

    return $this->hasher->check($plain, $user->getAuthPassword());
} 
```

现在，密码学是一门有趣而有用的学科，但是如果我们要在这个专栏中介绍密码学，阅读时间将会超过一个小时。现在可以说的是，`hasher@check`方法根据所提供的密码是否与记录的密码相匹配而返回‘true’或‘false’。

## 任务成功:登陆

此时，如果用户通过了 hasher 检查并到达了`login`方法，那么登录尝试就成功了。从这里开始，SessionGuard 开始执行让用户登录到您的应用程序所需的工作。

```
/**
* Log a user into the application.
*
* @param  \Illuminate\Contracts\Auth\Authenticatable  $user
* @param  bool  $remember
* @return void
*/
public function login(AuthenticatableContract $user, $remember = false)
{
    $this->updateSession($user->getAuthIdentifier());

    // If the user should be permanently "remembered" by the application we will
    // queue a permanent cookie that contains the encrypted copy of the user
    // identifier. We will then decrypt this later to retrieve the users.
    if ($remember) {
        $this->ensureRememberTokenIsSet($user);

        $this->queueRecallerCookie($user);
    }

    // If we have an event dispatcher instance set we will fire an event so that
    // any listeners will hook into the authentication events and run actions
    // based on the login and logout events fired from the guard instances.
    $this->fireLoginEvent($user, $remember);

    $this->setUser($user);
} 
```

首先，因为这是 SessionGuard，我们的应用程序更新用户的会话以反映他们的成功登录。

```
/**
* Update the session with the given ID.
*
* @param  string  $id
* @return void
*/
protected function updateSession($id)
{
    $this->session->put($this->getName(), $id);

    $this->session->migrate(true);
} 
```

一个新的会话值与用户 ID 一起添加，让我们的应用程序知道这个用户已经登录。从那里，使用`migrate`来更新会话，以强制我们的应用程序刷新并确认我们的更改。

我将跳过记住令牌过程背后的逻辑，因为它相当复杂，需要一些关于 cookies 如何工作的知识，并且最终与用户是否真的是他们所声称的那个人没有直接关系。

除了不直接影响登录过程的事件方法之外，`login`方法中唯一剩下的函数是`setUser`。该方法的唯一职责是在内存中保存一些关于用户和登录尝试的信息，这样用户就不必在应用程序生命周期的稍后调用该类时再次被加载。

这样，`login`函数停止执行，有效地结束了登录过程。从守卫的`attempt`方法中返回`true`值，然后导致我们的 LoginController 中的`attemptLogin`方法也返回 true。此时，唯一要做的事情就是向用户发送一个成功的登录响应:

```
// Illuminate\Foundation\Auth\AuthenticatesUsers

/**
* Send the response after the user was authenticated.
*
* @param  \Illuminate\Http\Request  $request
* @return \Illuminate\Http\Response
*/
protected function sendLoginResponse(Request $request)
{
    $request->session()->regenerate();

    $this->clearLoginAttempts($request);

    return $this->authenticated($request, $this->guard()->user())
            ?: redirect()->intended($this->redirectPath());
} 
```

请记住，我们的应用程序的会话是在我们存储了用户的 ID 之后重置的。通过刷新请求会话，我们保证请求会话与应用程序会话完全同步。这意味着在我们的用户登录后发生的对`$request->session()`的调用将正确地声明用户已经过身份验证。

最后一步取决于您的应用。如果您已经在 LoginController 中定义了一个`authenticated`方法，该方法将从这里接管。如果您想实现一些比简单重定向更高级的定制逻辑，这很好。

否则，使用`RedirectsUsers`特征重定向您的用户:

```
// Illuminate\Foundation\Auth\RedirectsUsers

/**
* Get the post register / login redirect path.
*
* @return string
*/
public function redirectPath()
{
    if (method_exists($this, 'redirectTo')) {
        return $this->redirectTo();
    }

    return property_exists($this, 'redirectTo') ? $this->redirectTo : '/home';
} 
```

如果您的控制器上有一个`redirectTo`方法，这个特征将调用它并将用户重定向到任何返回值。如果你的应用程序有不同层次的用户，应该被定向到不同的地方，这可能是有帮助的——例如，`/subscribers/`的订户和`/dashboard/`的普通用户。

否则，如果存在一个`redirectTo`属性，它将是重定向目标。如果方法和属性都不存在，默认的重定向路径是到`/home`。就我个人而言，我从来没有在自己的应用程序中使用过`/home`路径，所以我建议您定义自己的定制`redirectTo`属性来适应您的应用程序。

## 包装完毕

希望这个 Laravel 登录生命周期的旅程是有说明性的。我们不仅借此机会从较高的层面研究了身份验证过程是如何工作的，还研究了 Laravel 中不同的驱动程序和保护程序如何在创建相同最终结果的同时定制过程的各个步骤。

如果您对类似的演练有任何更多的要求，请不要犹豫，通过 Twitter、Medium、Practical Dev 或我自己的[网站](https://mariowhowrites.com)联系我 [@mariowhowrites](https://dev.to/mariowhowrites) 。