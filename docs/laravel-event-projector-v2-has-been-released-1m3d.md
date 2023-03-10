# ★ Laravel 事件投影仪 v2 已经发布

> 原文：<https://dev.to/freekmurze/laravel-event-projector-v2-has-been-released-1m3d>

[Laravel event projector](https://docs.spatie.be/laravel-event-projector/v2/introduction) 是一个软件包，旨在成为 Laravel 中事件采购的入口点。它可以帮助您设置聚集，投影仪和反应器。今天早些时候我们的团队[发布了 v2](https://github.com/spatie/laravel-event-projector/releases/tag/2.0.0) 。该版本简化了 v1 的投影仪和反应器，并增加了对聚合的支持。

在这篇博文中，我想解释传统构建的应用程序的一个潜在问题。之后，我们将看看什么是投影和聚合。最后，我们将通过一个例子来说明如何创建一个聚合的 laravel-event-projector。

我们走吧！

## 你是视觉学习者吗？

这里有一个视频，展示了什么是活动采购，以及如何使用 [Laravel 活动投影仪](https://docs.spatie.be/laravel-event-projector/v2/introduction)。不喜欢视频，那就继续看这篇博文的下一节。

[https://www.youtube.com/embed/9tbxl_I1EGE](https://www.youtube.com/embed/9tbxl_I1EGE)

## 传统应用

在传统的应用程序中，您可能会使用数据库来保存应用程序的状态。每当你想更新一个状态时，你只需简单地覆盖旧的值。那个旧值已经不可用了。您的应用程序只保存当前状态。

您可能认为您的备份中仍然有旧的状态。但是他们不算。您的应用程序可能无法也不应该对这些备份中的数据做出决策。

首先，我们把值 X. [![](img/23512cb95266e6f2a94eec3ac6c5df51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nnqM5tmC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/db-01.svg)

接下来，我们用 y 覆盖 X。X 不能再被访问。[![](img/26b5d04ffa7a29067a79b757bdbdc22f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xmqEm2jY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/db-02.svg)

这是一个使用传统架构的演示应用。在 [`AccountsController`](https://github.com/spatie/larabank-traditional/blob/6ceb08f4700a9be72f0ebfe49b997d5871d64c6b/app/Http/Controllers/AccountsController.php) 中，我们只需[创建新账户](https://github.com/spatie/larabank-traditional/blob/6ceb08f4700a9be72f0ebfe49b997d5871d64c6b/app/Http/Controllers/AccountsController.php#L19-L27)和[更新余额](https://github.com/spatie/larabank-traditional/blob/6ceb08f4700a9be72f0ebfe49b997d5871d64c6b/app/Http/Controllers/AccountsController.php#L19-L27)。

```
// in the AccountsController

public function store(Request $request)
{
  Account::create([
    'name' => $request->name,
    'user_id' => Auth::user()->id,
  ]);

  return back();
} 
```

我们用一个雄辩的模型来更新数据库。每当我们改变账户的余额，旧的值就丢失了。

## 用投影仪来变换事件

我们可以不直接更新数据库中的值，而是将我们想要进行的每个更改都作为事件写入数据库。

让我们将第一个事件写入数据库。

[![](img/f6e2ef8d800099f21964d5bb04b469bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jZTycnVw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/transform-01.svg)

当新事件进来时，我们也将它们写入事件表。

[![](img/b761a7665a46263831f27b4ca1f81fb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YjWi4O-w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/transform-02.svg)

所有的事件都被传递给一个我们称之为投影仪的类。投影仪将事件转换成便于在我们的应用程序中使用的格式。回到我们的例子，events 表保存单个事务的信息，如`MoneyAdded`和`MoneySubtracted`。一个投影仪可以基于这些交易构建一个`Accounts`表。

[![](img/86b945769559d40b9e499ca96e503551.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mDaUw4H8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/transform-03.svg)

假设您已经存储了一些事件，并且您的第一台投影仪正在创建那个`Accounts`表。银行目录现在想知道哪些账户上执行了最多的交易。没问题，我们可以创建另一个投影仪，读取所有以前的事件，并扮演`MoneyAdded`和`MoneySubtracted`来进行投影。

[![](img/3b384909ea24cf3504a01b94fe3f82ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pF3C3dfx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/transform-04.svg)

我们的 laravel-event-projector 包可以帮助您将本地 laravel 事件存储在一个`stored_events`表中，并创建转换这些事件的投影仪。

这是我们的示例应用程序[用投影仪重建拉班克](https://github.com/spatie/larabank-event-projector)。在[`AccountsController`](https://github.com/spatie/larabank-event-projector/blob/d02fd1de7f31f4b915c05df79d9ba61440f9e6b5/app/Http/Controllers/AccountsController.php#L20-L36)中，我们不再直接修改数据库。相反，控制器将调用方法，而不是在[它们的 turn fire off 事件](https://github.com/spatie/larabank-event-projector/blob/master/app/Account.php#L15-L42)。

```
public static function createWithAttributes(array $attributes): Account
{
    /*
     * Let's generate a uuid.
     */
    $attributes['uuid'] = (string) Uuid::uuid4();

    /*
     * The account will be created inside this event using the generated uuid.
     */
    event(new AccountCreated($attributes));

    /*
     * The uuid will be used the retrieve the created account.
     */
    return static::uuid($attributes['uuid']);
}

public function addMoney(int $amount)
{
    event(new MoneyAdded($this->uuid, $amount));
}

public function subtractMoney(int $amount)
{
    event(new MoneySubtracted($this->uuid, $amount));
} 
```

我们的包将监听这些事件(实现空的`ShouldBeStored`接口)并将它们存储在`stored_events`表中。这些事件也将传递给[所有注册的放映机](https://github.com/spatie/larabank-event-projector/blob/d02fd1de7f31f4b915c05df79d9ba61440f9e6b5/config/event-projector.php#L14)。在 [`AccountsProjector`](https://github.com/spatie/larabank-event-projector/blob/d02fd1de7f31f4b915c05df79d9ba61440f9e6b5/app/Projectors/AccountsProjector.php) 中，将使用[监听](https://github.com/spatie/larabank-event-projector/blob/d02fd1de7f31f4b915c05df79d9ba61440f9e6b5/app/Projectors/AccountsProjector.php#L17-L22)的几个事件来构建`Accounts`表。

```
final class AccountsProjector implements Projector
{
    use ProjectsEvents;

    protected $handlesEvents = [
        AccountCreated::class => 'onAccountCreated',
        MoneyAdded::class => 'onMoneyAdded',
        MoneySubtracted::class => 'onMoneySubtracted',
        AccountDeleted::class => 'onAccountDeleted',
    ];

    public function onAccountCreated(AccountCreated $event)
    {
        Account::create($event->accountAttributes);
    }

    public function onMoneyAdded(MoneyAdded $event)
    {
        $account = Account::uuid($event->accountUuid);

        $account->balance += $event->amount;

        if ($account->balance >= 0) {
            $this->broke_mail_sent = false;
        }

        $account->save();
    }

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $account = Account::uuid($event->accountUuid);

        $account->balance -= $event->amount;

        $account->save();
    }

    public function onAccountDeleted(AccountDeleted $event)
    {
        $account = Account::uuid($event->accountUuid);

        $account->delete();
    }
} 
```

投影仪是一个接收事件并将其转换为另一种格式的类。我并不是说你应该这样做，但是理论上你可以为你的应用程序中的每个视图创建一个投影仪。在我们的 Larabank 应用程序中，我们使用`MoneyAdded`和`MoneySubtracted`事件，并使用[投影仪](https://github.com/spatie/larabank-event-projector/blob/f6f2251e06f2b3e127a0df3bf2e7dec497769d78/app/Projectors/TransactionCountProjector.php)将它们转换为`transaction_counts`表，该表保存每个账户上的交易量。

```
final class TransactionCountProjector implements QueuedProjector
{
    use ProjectsEvents;

    protected $handlesEvents = [
        MoneyAdded::class => 'onMoneyAdded',
        MoneySubtracted::class => 'onMoneySubtracted',
    ];

    public function onMoneyAdded(MoneyAdded $event)
    {
        $account = Account::uuid($event->accountUuid);

        TransactionCount::record($account);
    }

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $account = Account::uuid($event->accountUuid);

        TransactionCount::record($account);
    }
} 
```

我们可以直接使用这些事务计数来驱动视图。

```
class TransactionsController extends Controller
{
    public function index()
    {
        $transactionCounts = TransactionCount::orderByDesc('count')->get();

        return view('transactions.index', compact('transactionCounts'));
    }
} 
```

在传统应用程序中，您可能需要编写一个昂贵的查询来对每个帐户的交易进行计数和分组。使用投影仪，您只需将事件转换为一种易于观看的格式。

如果你想了解更多关于投影仪以及如何使用它们，请前往文档中的[和`using-projectors`部分。](https://docs.spatie.be/laravel-event-projector/v2/using-projectors/writing-your-first-projector)

## 利用过去的总量来做决定

现在你知道什么是投影了，让我们再进一步看看聚合。在前面的例子中，每当我们想要触发一个事件时，我们就这样做。当使用聚合时，我们的主代码不再触发事件。相反，聚合将完成这一任务。聚合是一个帮助您根据过去发生的事件做出决策的类。

在触发一个事件之前，聚合将首先检查它是否被允许记录该特定事件。再次使用我们的 Larabank 示例，假设您必须实现一个帐户的余额不允许低于-5000 美元的规则。当试图从一个特定的帐户中扣除金额时，聚合将首先遍历该帐户以前的所有事件，并计算当前余额。如果余额减去我们减去的金额不小于-5000 美元，它将记录那个`MoneySubtracted`事件。之后，`MoneySubtracted`事件将被传递给所有投影仪和反应堆。

让我们一步一步来。

**第一步**:我们的应用程序想要减去 1000 美元。我们创建一个新的聚合根实例，并将所有事件提供给它。在此过程中还没有要检索的事件。聚合将得出结论，允许减去 1000 美元，并将记录该`Subtract`事件。这个记录只是在内存中，还没有任何东西被写入数据库。

[![](img/9ab96a58f85c09f392c9f0f32544f7ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gNWxu1_i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/aggregate-01.svg)

**步骤 2** :我们要持久化聚合。当持久化一个聚合时，该聚合拥有的所有新记录的事件都将被写入数据库。此外，如果您设置了投影仪，它们也将接收最新持续的事件。

[![](img/77cc0d08dfc5f42683ab74ee35f9bb1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cRg23diG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/aggregate-02.svg)

让我们达到账户限额，现在试着减去 4800 美元。首先，聚合将从所有以前的事件中重新构建。因为它可以计算内存中的当前余额(当然是 1000 美元)。总根可以得出结论，如果我们减去 4800 美元，我们将超过我们的限制-5000 美元。所以它不会记录那个事件。相反，我们可以记录达到帐户限额事实。

[![](img/00c209f4a9c1e88f55aa8b12051faf7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EtvumX3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/aggregate-03.svg)

**步骤 4** :聚合被持久化，账户限额命中事件被写入数据库。

[![](img/120435827e8f5d3e0e7c1caaa9dcc947.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rJpB5P4D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/aggregate-04.svg)

所以现在我们已经保护我们的帐户不低于 5000 美元。让我们更进一步，当我们的客户连续三次达到账户限额时，向他或她发送一封贷款建议邮件。使用一个集合这很容易！

第五步:让我们再减去一大笔钱，达到 5000 美元的账户限额。我们第二次达到了账户限额。

[![](img/39a8a8de4071b05dcffede32461084ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMW3j5a---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/aggregate-05.svg)

**第六步**:这次变得有趣了。我们将尝试减去钱，并将第三次达到我们的极限。我们的集合从所有事件中重组。这些事件被一个接一个地提供给聚合。内存中的聚合保存一个计数器，该计数器显示它接收到的限制命中事件的数量。那个柜台现在在 2 楼。因为我们减去的金额将超过帐户限额，所以合计将不会记录减去事件，而是记录新的限额命中事件。它会将极限命中计数器从 2 更新为 3。因为计数器现在是 3 点。它还可以记录一个新事件，称为贷款建议。存储聚合时，新事件将被持久存储在数据库中。所有的投影仪和反应堆都会被这些事件调用。`LoanProposalReactor`听到`LoanProposed`事件并发送邮件。

[![](img/b2885bf89c97cd6181a7a45781c2b1cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rXzbofSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/event-projector-v2/aggregate-06.svg)

以上所有这些都需要你去思考。为了帮助您更好地理解这一点，这是我们的 Larabank 应用程序，但这次使用聚合构建了[。在控制器中，您可以看到我们没有触发事件，但是我们使用了聚合](https://github.com/spatie/larabank-event-projector-aggregates)的[。在聚合内部，我们将](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Http/Controllers/AccountsController.php#L21-L52)[记录事件](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Domain/Account/AccountAggregateRoot.php#L46)，一旦我们[持久化](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Http/Controllers/AccountsController.php#L40)聚合，这些事件将被写入数据库。

每当我们检索一个集合时，所有先前存储的事件将被一个接一个地提供给它的`apply*`方法。我们可以使用[那些应用方法](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Domain/Account/AccountAggregateRoot.php#L77-L82)来重新计算像余额这样的东西，或者账户限额被达到的次数[作为实例变量](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Domain/Account/AccountAggregateRoot.php#L28-L35)。当我们想尝试减去金钱时，我们可以使用那些实例变量来决定我们是要记录`MoneySubtracted`事件还是记录其他事件。

总之，总量用于根据过去的事件做出决策。

## 我们一起打造一个集合体吧！

当我第一次学习聚合时，我花了一段时间来理解这个概念。为了帮助您理解使用聚合的事件建模，让我们一起实现 Larabank 示例应用程序的一小部分[。](https://github.com/spatie/larabank-event-projector-aggregates)

我们将添加方法来记录[`AccountCreated`](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Domain/Account/ShouldBeStoreds/AccountCreated.php)[`MoneyAdded`](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Domain/Account/ShouldBeStoreds/MoneyAdded.php)和 [`MoneySubtracted`](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Domain/Account/ShouldBeStoreds/MoneySubtracted.php) 事件。

### 创建一个聚合

使用我们的包，创建聚合根的最简单方法是使用`make:aggregate`命令:

```
php artisan make:aggregate AccountAggregate 
```

这将创建一个这样的类:

```
namespace App\Aggregates;

use Spatie\EventProjector\AggregateRoot;

final class AccountAggregate extends AggregateRoot
{
} 
```

### 记录事件

记住，聚合只是一个普通的 PHP 类。您可以在聚合中添加您需要的任何方法或变量。

首先，让我们添加一个`createAccount`方法到我们的集合中，它将记录`AccountCreated`事件。

```
namespace App\Aggregates;

use Spatie\EventProjector\AggregateRoot;

final class AccountAggregate extends AggregateRoot
{
    public function createAccount(string $name, string $userId)
    {
        $this->recordThat(new AccountCreated($name, $userId));
    }

    public function addMoney(int $amount)
    {
        $this->recordThat(new MoneyAdded($amount));
    }

    public function subtractAmount(int $amount)
    {
        $this->recordThat(new MoneySubtracted($amount));
    }
} 
```

`recordThat`函数不会将事件保存到数据库中。它只是将它们保存在内存中。当聚合本身被持久化时，事件将被写入数据库。

有两件事需要注意。首先，方法名是用现在时写的，而不是过去时。我们正在尝试做一些事情，对于我们应用程序的其余部分来说，直到实际的`AccountCreated`被保存，这些事情才发生。这只会在`AccountAggregate`被持久化时发生。

第二件要注意的事情是，方法和事件都不包含 uuid。聚合本身知道要使用的 uuid，因为它被传递给了 retrieve 方法(`AccountAggregate::retrieve($uuid)`，我们稍后会讲到)。当持久化 aggregateroot 时，它将保存记录的事件和 uuid。

有了这些，你就可以使用集合了。

```
AccountAggregate::retrieve($uuid)
    ->createAccount('my account', auth()->user()->id)
    ->persist();

AccountAggregate::retrieve($uuid)
    ->addMoney(123)
    ->persist();

AccountAggregate::retrieve($uuid)
    ->subtractMoney(456)
    ->persist(); 
```

当持久化聚合时，聚合根内所有新记录的事件将被保存到数据库中。新记录的事件也将传递给所有监听它们的投射器和反应堆。

在我们的演示应用程序中，我们在`AccountsController` 中检索并持久化聚合[。该软件包不知道应该在哪里与聚合进行交互。想做什么就做什么。](https://github.com/spatie/larabank-event-projector-aggregates/blob/c9f2ff240f4634ee2e241e3087ff60587a176ae0/app/Http/Controllers/AccountsController.php)

### 实施我们的第一条业务规则

现在让我们执行一个账户不能低于 5000 美元的规则。需要记住的是:当检索一个集合时，给定 uuid 的所有事件都将被检索，并被传递给集合上名为`apply<className>`的方法。

因此，为了让我们的聚合接收所有过去的`MoneyAdded`和`MoneySubtracted`事件，我们需要向我们的聚合添加`applyMoneySubtracted`和`applyMoneySubtracted`方法。因为这些事件都被提供给聚合的同一个实例，所以我们可以简单地添加一个实例变量来保存计算出的余额。

```
// in our aggregate 

private $balance = 0;

//...

public function applyMoneyAdded(MoneyAdded $event)
{
    $this->balance += $event->amount;
}

public function applyMoneySubtracted(MoneySubtracted $event)
{
    $this->balance -= $event->amount;
} 
```

现在我们在内存中有了账户的余额，我们可以给`subtractAmount`添加一个简单的检查来防止事件被记录。

```
public function subtractAmount(int $amount)
{
    if (! $this->hasSufficientFundsToSubtractAmount($amount) {
        throw CouldNotSubtractMoney::notEnoughFunds($amount);
    }

    $this->recordThat(new MoneySubtracted($amount));
}

private function hasSufficientFundsToSubtractAmount(int $amount): bool
{
    return $this->balance - $amount >= $this->accountLimit;
} 
```

### 实施另一个业务规则

我们可以更进一步。您还可以记录达到帐户限制的事件。

```
public function subtractAmount(int $amount)
{
    if (! $this->hasSufficientFundsToSubtractAmount($amount) {
        $this->recordThat(new AccountLimitHit($amount));

        // persist this so the recorded event gets persisted
        $this->persist();

        throw CouldNotSubtractMoney::notEnoughFunds($amount);
    }

    $this->recordThat(new MoneySubtracted($amount));
} 
```

现在让我们添加一个新的业务规则。每当有人达到限额三次，贷款建议应该发送。我们可以这样实现。

```
private $accountLimitHitCount = 0;

// we need to add this method to count the amount of this the limit was hit
public function applyAccountLimitHit()
{
    $this->accountLimitHitCount++;
}

public function subtractAmount(int $amount)
{
    if (! $this->hasSufficientFundsToSubtractAmount($amount) {
        $this->recordThat(new AccountLimitHit($amount));

        if ($this->accountLimitHitCount === 3) {
            $this->recordThat(new LoanProposed());
        }

        // persist the aggregate so the recorded events gets persisted
        $this->persist();

        throw CouldNotSubtractMoney::notEnoughFunds($amount);
    }

    $this->recordThat(new MoneySubtracted($amount));
} 
```

当达到三次限制时，我们记录另一个事件`LoanProposed`。我们可以设置一个反应器来监听该事件并发送实际的邮件。

如果您想摆弄这个例子，可以克隆带有聚合的[Lara bank](https://github.com/spatie/larabank-event-projector-aggregates)例子。

## 关闭思绪

恭喜你。这篇文章快结束了，现在你应该对什么是事件源以及如何使用聚合来建模一个流程有了基本的了解。Laravel 事件投影仪可以帮助您设置聚集，投影仪和反应器。要了解关于这个包的更多信息，请查看广泛的文档。

当使用事件源时，写是昂贵的。你的应用程序需要比平常多做一点工作来写一些东西。它不会简单地更新一条记录。使用聚合时，事件将在内存中重放，需要写入事件，需要调用投影仪。但是好处还是蛮大的。读取很容易，因为您可以将数据转换成易于使用的东西。聚合使基于过去的决策变得容易。

在以下情况下，活动采购可能是您项目的一个好选择:

*   你的应用需要根据过去做出决策
*   你的应用程序有审计需求:你的应用程序处于某种状态的原因和状态本身一样重要
*   您预见到将来会有报告需求，但是您还不知道需要为这些报告收集哪些数据

查看这些资源，了解有关活动采购的更多信息:

*   Kickstarter 的 Philippe Creux 的博客文章。有趣的事实:阅读这篇博客启动了我们的软件包的工作。
*   Michiel Rook 的演讲:处理事件源应用的变化
*   Barry O Sullivan 的博客活动采购:它是什么，为什么很棒

Laravel event projector 可能是在 Laravel 应用程序中开始活动采购的最简单方法。但是有一些用 PHP 编写的替代方法:

*   EventSauce 是一个实用的、框架不可知的事件源包，由 FlySystem 创建者 [Frank De Jonge](https://github.com/frankdejonge) 构建。
*   [EventSauce Laravel bindings](https://github.com/spatie/laravel-eventsauce)是由[我们的团队](https://spatie.be/about-us)开发的一个包，它使得在 Laravel 应用中使用 event sauce 变得很容易。
*   Prooph 是一个功能丰富的软件包套件。

以下是一些关于我们如何实现 laravel-event-projector 中的一些功能的链接:

*   [实现事件源:改善开发者体验](https://freek.dev/1329-implementing-event-sourcing-improving-the-developer-experience)
*   [实施事件采购:聚集](https://freek.dev/1326-implementing-event-sourcing-aggregates)

最后，请务必查看我的团队之前发布的所有开源包。我相信那里会有对你下一个项目有用的东西。

非常感谢我的同事威廉姆在这篇博文中的插图。