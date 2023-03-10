# 用于 JS 开发人员的 Kotlin(第 2 部分)

> 原文：<https://dev.to/cassiozen/kotlin-for-js-devs-part-2-fam>

在[之前的帖子](https://dev.to/cassiozen/kotlin-for-js-devs-part-1-5bld)中，我简要介绍了 Kotlin 的语法，总是与 JavaScript 片段相对照。但是，Kotlin 在一些地方有独特的方法，与 JS(以及其他许多语言)不可同日而语。最突出的例子是 Kotlin 的可空性方法——它普遍存在于所有 Kotlin 代码中，所以如果你想学习 Kotlin，你必须了解它，但在 JavaScript 中确实没有直接的相似之处。另一个例子是异步编程，它类似于 JavaScript Promises 和 Async/Await，但是有更多的选项和对执行模型和线程的控制。

# 为空性

Javascript 有两个“非值”——未定义的和 null。语言使用前者来通知缺少值，开发人员可以使用后者来显式地将某些内容设置为不存在的值——但通常情况下，它们带来的问题比解决的问题更多。你处理过多少次“未捕获的 TypeError:无法读取 undefined 的属性 x”或者“TypeError: 'null '不是对象”？

空引用的整个概念被它的发明者东尼·霍尔认为是十亿美元的错误。他还说“编程语言应该对他们的用户负责”——许多现代编程语言都提供了零安全机制，但 Kotlin 的方法因其务实、简单和优雅而值得称道。

Kotlin 支持空性作为其类型系统的一部分——这意味着您可以声明一个变量是否可以包含空值。我们来看一个例子:

```
var greeting: String = "Hello, World"
greeting = null // Compilation Error 
```

默认情况下，Kotlin 假设 greeting 不能为 null:
要允许 null 值，您必须通过在变量的类型声明中附加一个问号来将变量声明为可为 null:

```
var nullableGreeting: String? = "Hello, World"
nullableGreeting = null // Works 
```

通过在类型系统中支持可空性，编译器跟踪变量，如果没有处理空值，就拒绝编译。

例如，下面的方法 access 可以工作，因为 Kotlin 知道变量 greeting 不能为空:

```
val len = greeting.length 
```

但是同样的方法调用不能用于 nullableGreeting 变量-

```
val len = nullableGreeting.length // Compilation Error 
```

如果您处理了空值的可能性，Kotlin 编译器将愉快地接受您的代码而不出错:

```
val len = if (nullableGreeting != null) {
    nullableGreeting.length
} else {
    0
} 
```

## 安全呼叫操作员

当然，空值比较有点过于冗长。Kotlin 提供了一个安全的调用操作符，`?.`,它在一个表达式中结合了空检查和方法调用。

我们来看一个例子:

```
val a = "Kotlin"
val b: String? = null
println(a?.length) // 6
println(b?.length) // null 
```

这很好，但这还不是全部。您可以像这样链接多个安全调用:

```
val currentCity: String? = user?.address?.city 
```

如果链中的任何属性为 null，则链返回 null。

## 猫王符

[![Elvis Operator](img/4672898feb7a76342b6af21389816177.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jqr-T4Zk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rhrojdjwwdawocka13gh.png)

如果你想在某个变量为空时提供一个默认值，你可以使用 Elvis 运算符`?:`

```
val name = nullableUserName ?: "Guest" 
```

您可以使用 Elvis 运算符左侧的安全调用运算符(或任何其他表达式):

```
val name = nullableUser?.name ?: "Guest" 
```

* * *

# 异步编程

异步或非阻塞编程是新的现实:无论我们是创建服务器端、桌面还是移动应用程序，重要的是我们提供的体验不仅从用户的角度来看是流畅的，而且在需要时是可扩展的。

JavaScript 是一种单线程语言，它使用事件循环来支持异步行为:当从远程服务器获取数据、设置计时器或任何其他异步操作时，您提供一个回调，事件循环将处理任务并在完成时发出通知。该语言的新版本在此基础上提供了抽象(promises 和 async/await ),允许开发人员编写看起来像同步代码的异步代码。

Kotlin 是一种多线程语言——开发人员可以产生多个线程并真正并发地执行代码，该语言还具有内置的协同例程机制，支持延迟(类似于承诺)和异步/等待模式:

#### JavaScript

```
async function getStatus() {
    const currentUserPromise = someApi.fetchUser();
    const currentCompanyPromise = someApi.fetchCompany();
    return await Promise.all([currentUserPromise, currentCompanyPromise]);
} 
```

#### [釜底抽薪](#kotlin)

```
suspend fun getStatus(): List<String> {
    val currentUserDeferred = someApi.fetchUser()
    val currentCompanyDeferred = someApi.fetchCompany()
    return listOf(currentUserDeferred.await(), currentCompanyDeferred.await())
} 
```

公平地说，这是 Kotlin 的协同例程和 deferrend 用法的一个非常小的例子——该语言(及其标准库)提供了除 suspend、async 和 await 之外的许多其他选项，使开发人员能够对异步执行进行细粒度的控制(包括取消、线程池和许多其他在 JavaScript 中没有对等物的事情)。同样，这里的目的只是让你对这门语言有一个大致的、鸟瞰式的了解。意识到协程和延迟的存在，并在全面熟悉这门语言之后进行技术探索，这是一件好事。

* * *

# 何去何从？

这些文章的目的是对 Kotlin 做一个非常简短的介绍，只是让你对这种语言有一个大概的了解，并给你一个好的开始。当然，这里有很多遗漏的信息，但是这应该足够让您开始使用这种语言了。

在[科特林游乐场](https://play.kotlinlang.org)不安装任何东西也可以到处玩。如果你想挑战，试试[科特林考恩](https://play.kotlinlang.org/koans/overview)。