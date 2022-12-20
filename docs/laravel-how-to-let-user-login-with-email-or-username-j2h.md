# Laravel:如何让用户用电子邮件或用户名登录

> 原文：<https://dev.to/pramanadiputra/laravel-how-to-let-user-login-with-email-or-username-j2h>

默认情况下，Laravel 只允许用户使用他们的电子邮件地址登录。现在，如果你想让你的用户用电子邮件甚至用户名登录呢..？

为此，您只需更改 Laravel 默认认证码中的一些代码。

我们开始吧。我假设您已经设置了您的 Laravel 项目并迁移了 Laravel 默认 auth。如果你没有写命令`php artisan make:auth`和`php artisan migrate`。

上面的命令使 Laravel 为您提供基本和默认的身份验证，所以你不必进行登录，注册，忘记密码和重置密码手动。

### 循序渐进

打开`loginController.php`
默认情况下，你只会看到你的登录控制器
里面的这些代码

```
<?php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\AuthenticatesUsers;

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

Enter fullscreen mode Exit fullscreen mode

这个控制器只有属性和一个构造函数，那么它是如何处理登录函数的呢？这就是`OOP`发挥作用的地方。

注意这里有`use AuthenticatesUsers;`语句，这是 Laravel 构建默认登录功能的地方。所以现在，我们来检查一下`AuthenticateUsers`类。

```
<?php

namespace Illuminate\Foundation\Auth;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Validation\ValidationException;

trait AuthenticatesUsers
{
    use RedirectsUsers, ThrottlesLogins;

    /**
     * Show the application's login form.
     *
     * @return \Illuminate\Http\Response
     */
    public function showLoginForm()
    {
        return view('auth.login');
    }

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
        if (method_exists($this, 'hasTooManyLoginAttempts') &&
            $this->hasTooManyLoginAttempts($request)) {
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

    /**
     * Validate the user login request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    protected function validateLogin(Request $request)
    {
        $request->validate([
            $this->username() => 'required|string',
            'password' => 'required|string',
        ]);
    }

    /**
     * Attempt to log the user into the application.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return bool
     */
    protected function attemptLogin(Request $request)
    {
        return $this->guard()->attempt(
            $this->credentials($request), $request->filled('remember')
        );
    }

    /**
     * Get the needed authorization credentials from the request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    protected function credentials(Request $request)
    {
        return $request->only($this->username(), 'password');
    }

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

    /**
     * The user has been authenticated.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  mixed  $user
     * @return mixed
     */
    protected function authenticated(Request $request, $user)
    {
        //
    }

    /**
     * Get the failed login response instance.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Symfony\Component\HttpFoundation\Response
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    protected function sendFailedLoginResponse(Request $request)
    {
        throw ValidationException::withMessages([
            $this->username() => [trans('auth.failed')],
        ]);
    }

    /**
     * Get the login username to be used by the controller.
     *
     * @return string
     */
    public function username()
    {
        return 'email';
    }

    /**
     * Log the user out of the application.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function logout(Request $request)
    {
        $this->guard()->logout();

        $request->session()->invalidate();

        return $this->loggedOut($request) ?: redirect('/');
    }

    /**
     * The user has logged out of the application.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return mixed
     */
    protected function loggedOut(Request $request)
    {
        //
    }

    /**
     * Get the guard to be used during authentication.
     *
     * @return \Illuminate\Contracts\Auth\StatefulGuard
     */
    protected function guard()
    {
        return Auth::guard();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你是一个初学者，刚刚接触这个世界，当你看到这些代码行时，你可能会感到不知所措。但是不要。现在我们已经看了一下处理登录功能的类，现在你的问题可能是“我们如何定制它来满足我们的需求？”

你不知道！请记住,“AuthenticateUsers”类的位置在`Vendor`目录下的`Illuminate\Foundation\Auth\AuthenticatesUsers`处。不要编辑或更改本课程中的任何内容，否则你的作曲者会不高兴的！。

Laravel 是一个`Object-Oriented`框架。所以我们应用了覆盖的 OOP 方法。

我们希望我们的应用程序允许用户使用电子邮件或用户名登录，所以让我们看看哪个方法在`AuthenticateUsers`中处理这一部分。你可能已经注意到这个方法`username()`描述了它的作用。

```
/**
* Get the login username to be used by the controller.
*
* @return string
*/
public function username()
{
    return 'email';
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的注释告诉我们，它返回`email`作为控制器使用的登录用户名。现在，这是我们必须覆盖的方法。

将那个`username()`方法复制并粘贴到您的`loginController.php`中。现在，我们需要一种返回`email`或`username`的方法，而不是返回`email`。

现在是我们必须检查我们的登录表单的部分。在我们的默认登录表单中，查找默认的电子邮件输入，并将名称从`name="email"`更改为`name="identity"`。稍后，它将`identity`作为 HTTP 请求发送给我们的 Laravel。

再次回到`username()`方法，我们需要实现一种方法来检查 HTTP 请求`email`是否以电子邮件地址或用户名的形式出现。我们可以通过检查发送的数据中是否有“@”来做到这一点。幸运的是，PHP 已经建立了一个函数来做这件事。如果有一个“@”，那么 HTTP 请求`email`确实是一个电子邮件地址。否则，它就是一个用户名。

```
/**
* Get the login username to be used by the controller.
*
* @return string
*/
public function username()
{
     $login = request()->input('identity');

     $field = filter_var($login, FILTER_VALIDATE_EMAIL) ? 'email' : 'username';
     request()->merge([$field => $login]);

     return $field;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码所做的是通过使用`FILTER_VALIDATE_EMAIL`来过滤 HTTP 请求，如果有@或者它是一封电子邮件。如果是电子邮件，那么返回`email`，否则，返回`username`。

做完这些之后，我们只剩下一件要做的事情了，那就是覆盖验证请求。在您的登录控制器类中，添加以下代码:

```
/**
     * Validate the user login request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    protected function validateLogin(Request $request)
    {
        $messages = [
            'identity.required' => 'Email or username cannot be empty',
            'email.exists' => 'Email or username already registered',
            'username.exists' => 'Username is already registered',
            'password.required' => 'Password cannot be empty',
        ];

        $request->validate([
            'identity' => 'required|string',
            'password' => 'required|string',
            'email' => 'string|exists:users',
            'username' => 'string|exists:users',
        ], $messages);
    } 
```

Enter fullscreen mode Exit fullscreen mode

默认的验证只检查电子邮件，现在我们已经把它改为同时检查电子邮件和用户名。

现在，您有了一个可行的解决方案，可以让您的用户使用电子邮件地址或用户名登录。

如果您有任何问题，请告诉我:)

*本帖更新于 2020 年 5 月 5 日梅。修正了一些语法错误，为将来的读者提供更好的阅读体验。*