# 在 PHPUnit 中组织(大型)数据提供者

> 原文：<https://dev.to/erikbooij/keeping-large-data-providers-organized-in-phpunit-983>

当使用数据提供者进行 PHPUnit 测试时，很容易忘乎所以，添加参数有细微差别的不同测试用例。如果你需要那么多测试用例，让我们不要讨论你的测试对象是否做了太多的事情，让我们关注如何保持这些测试用例的可读性、可理解性和可管理性。

假设您正在为下面这段代码编写一个测试，它根据会话的当前状态在一个表示模型类上设置了三个标志(应该显示一个主要动作，应该显示一个登录按钮，应该显示一个升级按钮):

```
/**
 * @param ServerRequestInterface $request
 * @param PresentationModel      $presentationModel
 *
 * @return PresentationModel
 */
public function present(ServerRequestInterface $request, PresentationModel $presentationModel): PresentationModel
{
    // Set defaults
    $showLogin = true;
    $showUpgrade = false;
    $showPrimaryButton = true;

    $session = $this->sessionProvider->getSession();
    // Get users loggedIn status and default to false, in case the session is fresh
    $isLoggedIn = (bool)$session->get('loggedIn', false);

    if ($isLoggedIn) {
        $showLogin = false;
        $showUpgrade = true;
    }

    // Check if the user is logged in *and* a pro user
    if ($isLoggedIn && $session->get('subscriptionLevel', Subscription::LEVEL_BASIC) === Subscription::LEVEL_PRO) {
        $showUpgrade = false;
        $showPrimaryButton = false;
    }

    return $presentationModel->withVariables([
        'show_login' => $showLogin,
        'show_upgrade' => $showUpgrade,
        'show_primary_button' => $showLogin || $showPrimaryButton,
    ]);
} 
```

现在我们将编写三个测试用例:

*   这是第一次访问该网站，会话仍然是空的
*   这是一个登录状态设置为`false`的访问，并且在会话中没有设置`subscriptionLevel`
*   这是一次登录状态设置为`true`且`subscriptionLevel`状态为 PRO 的访问。

我们将首先编写数据提供者:

```
/**
 * @return array[]
 */
public function sessionDataProvider(): array
{
    return [
        [
            [],
            true,
            false,
            true,
        ],
        [
            ['loggedIn' => false],
            true,
            false,
            true,
        ],
        [
            ['loggedIn' => true, 'subscriptionLevel' => Subscription::LEVEL_PRO],
            false,
            false,
            false,
        ],
    ];
} 
```

你愿意猜猜这些不可描述的布尔值是什么意思吗？你可能会弄明白，但这肯定会花费不必要的时间。我们把它描述得更清楚些怎么样？请记住，PHPUnit 将简单地按照定义的顺序获取数据提供者返回的数组的**值**，它不太关心**数组键**。

```
/**
 * @return array[]
 */
public function sessionDataProvider(): array
{
    return [
        [
            'session' => [],
            'showLogin' => true,
            'showUpgrade' => false,
            'showPrimaryButton' => true,
        ],
        [
            'session' => ['loggedIn' => false],
            'showLogin' => true,
            'showUpgrade' => false,
            'showPrimaryButton' => true,
        ],
        [
            'session' => ['loggedIn' => true, 'subscriptionLevel' => Subscription::LEVEL_PRO],
            'showLogin' => false,
            'showUpgrade' => false,
            'showPrimaryButton' => false,
        ],
    ];
} 
```

这样，这些值代表什么就一目了然了。六个月后，当您回到这个测试时，您不必首先找到测试实现来找到[`true`、`false`、`true`的含义。

尽管如此，仍有改进的余地。尽管很清楚这些变量的含义，但我们测试的是什么还不清楚。我们可以做得更好，其中一个方法是为数据集提供一个数组键:

```
/**
 * @return array[]
 */
public function sessionDataProvider(): array
{
    return [
        'fresh-session' => [
            'session' => [],
            'showLogin' => true,
            'showUpgrade' => false,
            'showPrimaryButton' => true,
        ],
        'not-logged-in-subscription-unknown' => [
            'session' => ['loggedIn' => false],
            'showLogin' => true,
            'showUpgrade' => false,
            'showPrimaryButton' => true,
        ],
        'logged-in-pro-user' => [
            'session' => ['loggedIn' => true, 'subscriptionLevel' => Subscription::LEVEL_PRO],
            'showLogin' => false,
            'showUpgrade' => false,
            'showPrimaryButton' => false,
        ],
    ];
} 
```

这同样有助于提高数据提供者的可读性。你永远都不需要思考“为什么我/某人要添加这个测试用例，它到底在测试什么？”。

数据提供者现在可读性很好，让我们快速实现测试本身:

```
/**
 * @dataProvider sessionDataProvider
 *
 * @param array  $sessionData
 * @param bool   $showLogin
 * @param bool   $showUpgrade
 * @param bool   $showPrimaryButton
 *
 * @return void
 */
public function testHeaderShouldBeShownWithCorrectButtonAction(
    array $sessionData,
    bool $showLogin,
    bool $showUpgrade,
    bool $showPrimaryButton
): void {
    $this->sessionProvider->getSession()->willReturn(Session::fromData($sessionData));

    $presentationModel = $this->presenter->present($this->request, $this->presentationModel);

    $this->assertEquals($showLogin, $presentationModel->getVariable('show_login'));
    $this->assertEquals($showUpgrade, $presentationModel->getVariable('show_upgrade'));
    $this->assertEquals($showPrimaryButton, $presentationModel->getVariable('show_primary_button'));
} 
```

非常简单，您会看到数据集的名称现在也出现在测试输出中，立即指出哪个测试用例失败了:

```
There were 3 failures:

1) TestClass::testHeaderShouldBeShownWithCorrectButtonAction with data set "fresh-session" (array(), true, false, true)
Failed asserting that false matches expected true.

/path/to/TestClass.php:88

2) TestClass::testHeaderShouldBeShownWithCorrectButtonAction with data set "not-logged-in-subscription-unknown" (array(false), true, false, true)
Failed asserting that false matches expected true.

/path/to/TestClass.php:88

3) TestClass::testHeaderShouldBeShownWithCorrectButtonAction with data set "logged-in-pro-user" (array(true, 'pro'), false, false, true)
Failed asserting that false matches expected true.

/path/to/TestClass.php:88 
```

我们现在已经非常接近了，但是我们可以更上一层楼，因为`Failed asserting that false matches expected true`在调试中仍然不尽如人意，我们有多个断言可以期待`true`，但是现在得到了`false`。让我们再次改进我们的设置:不使用测试用例的描述作为数据提供者中的数组键，而是让它成为返回数据集的一部分，因为幸运的是 PHPUnit 允许向断言传递定制的错误消息。

```
/**
 * @return array[]
 */
public function sessionDataProvider(): array
{
    return [
        [
            'testCase' => 'Fresh Session',
            'session' => [],
            'showLogin' => true,
            'showUpgrade' => false,
            'showPrimaryButton' => true,
        ],
        [
            'testCase' => 'Not Logged In & Subscription Unknown',
            'session' => ['loggedIn' => false],
            'showLogin' => true,
            'showUpgrade' => false,
            'showPrimaryButton' => true,
        ],
        [
            'testCase' => 'Logged In & PRO subscription',
            'session' => ['loggedIn' => true, 'subscriptionLevel' => Subscription::LEVEL_PRO],
            'showLogin' => false,
            'showUpgrade' => false,
            'showPrimaryButton' => false,
        ],
    ];
} 
```

```
/**
 * @dataProvider sessionDataProvider
 *
 * @param string $testCase
 * @param array  $sessionData
 * @param bool   $showLogin
 * @param bool   $showUpgrade
 * @param bool   $showPrimaryButton
 *
 * @return void
 */
public function testHeaderShouldBeShownWithCorrectButtonAction(
    string $testCase,
    array $sessionData,
    bool $showLogin,
    bool $showUpgrade,
    bool $showPrimaryButton
): void {
    $this->sessionProvider->getSession()->willReturn(Session::fromData($sessionData));

    $presentationModel = $this->presenter->present($this->request, $this->presentationModel);

    $this->assertEquals(
        $showLogin,
        $presentationModel->getVariable('show_login'),
        "`show_login` set incorrectly for test case \"{$testCase}\""
    );
    $this->assertEquals(
        $showUpgrade,
        $presentationModel->getVariable('show_upgrade'),
        "`show_upgrade` set incorrectly for test case \"{$testCase}\""
    );
    $this->assertEquals(
        $showPrimaryButton,
        $presentationModel->getVariable('show_primary_button'), 
        "`show_primary_button` set incorrectly for test case \"{$testCase}\""
    );
} 
```

如果现在测试失败，这将是输出:

```
There were 3 failures:

1) TestClass::testHeaderShouldBeShownWithCorrectButtonAction with data set #0 ('Empty Session', array(), true, false, true)
`show_login` set incorrectly for test case "Empty Session"
Failed asserting that false matches expected true.

/path/to/TestClass.php:88

2) TestClass::testHeaderShouldBeShownWithCorrectButtonAction with data set #1 ('Not Logged In', array(false), true, false, true)
`show_login` set incorrectly for test case "Not Logged In"
Failed asserting that false matches expected true.

/path/to/TestClass.php:88

3) TestClass::testHeaderShouldBeShownWithCorrectButtonAction with data set #2 ('Not Logged In & Basic Subscription', array(false, 'basic'), true, false, true)
`show_login` set incorrectly for test case "Not Logged In & Basic Subscription"
Failed asserting that false matches expected true.

/path/to/TestClass.php:88 
```

乍看之下，这一点非常清楚:

*   哪个测试失败了
*   那个测试的哪个测试用例失败了
*   在那个特定场景中，哪个断言失败了

# 结论

描述你的测试用例以及测试参数，以便在将来的某个时候仍然能够维护/读取/调试你的测试。当你的测试/断言失败时，也要确保通过提供描述性的消息来帮助你未来的自己。到时候你会感谢过去的自己。