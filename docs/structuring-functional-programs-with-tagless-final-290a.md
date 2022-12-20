# 用无标签 Final 构造函数式程序

> 原文：<https://dev.to/devkat/structuring-functional-programs-with-tagless-final-290a>

#### 单子是处理函数式程序中各种问题的有价值的工具。在这篇文章中，我们展示了如何利用领域特定语言和无标签最终模式来构建模块化的单子程序。

# 特定领域的语言和解释器

特定领域语言(DSL)是模块化功能程序的一种流行方法。DSL 是解决特定问题的一组功能——这可以是从接口到子系统的任何东西，也可以是像日志记录这样的跨领域问题。DSL 通常是分层的，即高层 DSL(用于表达业务流程)构建在低层 DSL(用于访问数据库或连接到远程 API)之上。在函数式编程中，DSL 通常被称为代数，暗示了这个概念在范畴理论中的起源。

如果您熟悉面向对象编程，您可以将 DSL 视为接口的类比:DSL 定义了软件模块的功能，但没有提供具体的实现。在函数式编程中，DSL 的实现被称为*解释器*。解释器实现 DSL 的每个功能。

# 单子和分离的顾虑

正如博客文章[用单子烹饪](https://www.becompany.ch/en/blog/2016/11/08/coooking-with-monads.html)中所概述的，单子提供了一种构造函数式程序的方式。在函数式编程中，我们经常使用单子来显式地处理我们程序的某些方面(“关注”)，而不必在程序代码本身中表达这个方面。单子允许我们将特定的关注点从业务逻辑中分离出来，这导致了我们程序中更好的关注点分离[。](https://en.wikipedia.org/wiki/Separation_of_concerns)

一些例子:

*   读取器 monad 允许传递所有计算步骤都可以访问的上下文，例如配置。
*   状态单子将状态信息从一个调用传递到下一个调用，而不需要程序代码中的可变数据结构。
*   任务 monad 提供了一种处理并发性、副作用和潜在错误的方法。

这些单子中的每一个都支持我们将业务逻辑从处理各自关注点的责任中解放出来。

# 一元 DSL 和无标签 Final

在 Scala 中，DSL 的单个函数通常有一元返回值，这样做的好处是程序可以被编写成可理解的。无标签 Final 模式提供了一种以通用方式声明 DSL 的方法，而无需指定特定的单子。一个 DSL 可以有多个解释器，每个解释器都可能针对不同的单子。

这种方法有多种好处:

*   当编写一个基于无标签最终 DSL 的程序时，程序的目标 monad 可以在将来改变。这样，像并行计算这样的新特性可以在不修改程序本身的情况下引入。
*   DSL 可以与不同的解释器一起使用。一个典型的用例是为测试目的提供一个替代解释器，使用本地数据存储而不是访问外部系统。
*   通过为同一个目标单子选择解释器，可以将多个 DSL 组合成单个 for-comprehension。

# 我们的例子

在我们的示例代码中，我们将模拟一个名为 authn 的 DSL，它提供注册和认证用户的功能:

```
def register(email: String @@ EmailAddress, password: String):
    Either[RegistrationError, User]

def authn(email: String @@ EmailAddress, password: String):
    Either[AuthnError, User] 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道，String @@ EmailAddress 是一个[标记的类型](http://www.vlachjosef.com/tagged-types-introduction/)，表示 email 是一个字符串，唯一的目的是模拟电子邮件地址。

您可以在 GitHub 上找到示例应用程序的源代码。

包结构如下所示。我们基于[功能设计](https://en.wikipedia.org/wiki/Functional_design)耦合我们的代码，这意味着具有共同功能的代码放在同一个包中。

```
ch.becompany
  authn           Authentication functionality
    domain        Authentication domain code
    Dsl           Authentication DSL
  shapelessext    Extensions to the shapeless library
  shared          Shared code
    domain        Shared domain code
  Main.scala      Our main application 
```

Enter fullscreen mode Exit fullscreen mode

要运行该示例，请在控制台中执行以下命令:

```
sbt run 
```

Enter fullscreen mode Exit fullscreen mode

### 用无标签 Final 建模 DSL

在无标签的最终模式中，DSL 被建模为具有单一类型参数的 trait，它必须是 arity 为 1 的类型构造函数。我们将把这个类型构造函数称为`F[_]`。此时，实际上并不要求 F 是单子。稍后，当实现我们的 DSL 的解释器时，解释器的目标单子将代替 f。

让我们以这种方式建模我们的认证 DSL:

```
package ch.becompany.authn

trait Dsl[F[_]] {

  def register(email: String @@ EmailAddress, password: String):
      F[Either[RegistrationError, User]]

  def authn(email: String @@ EmailAddress, password: String):
      F[Either[AuthnError, User]]

} 
```

Enter fullscreen mode Exit fullscreen mode

我们看到所有 DSL 函数的返回值都包装在容器 F 中，在下面的程序中，编译器从 registerAndLogin 函数的返回类型中推断出类型参数 F[_]。

```
package ch.becompany

object Main extends App {

  def registerAndLogin[F[_] : Monad](implicit authnDsl: AuthnDsl[F]):
      F[Either[AuthnError, User]] = {

    val email = tag[EmailAddress]("john@doe.com")
    val password = "swordfish"

    for {
      _ <- authnDsl.register(email, password)
      authenticated <- authnDsl.authn(email, password)
    } yield authenticated
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

函数签名确保 F 是一个单子(通过要求 Monad[F]类型的隐式值的存在)。因此，我们的 DSL 功能可以用于 for-comprehensions。即使在这个阶段，我们也没有为 F 指定一个具体的类型；registerAndLogin 函数实际上可以是更高级 DSL 的一部分。

除了 Monad 实例之外，该函数还需要一个额外的参数:一个用于认证 DSL 的实例(也称为*解释器*)，类型为通用类型 f。该参数被声明为隐式的，以允许编译器自动解析；当我们谈到口译员时，我们将详细研究这一点。

# 口译员

既然我们已经在各自的 trait 中定义了 DSL 的语法，我们必须实现语义。使用无标签最终技术，这是在一个*解释器*中完成的。对于每个 DSL，可以存在多个解释器；每一种都针对一种特定的类型。解释器通常被建模为[类型类](http://danielwestheide.com/blog/2013/02/06/the-neophytes-guide-to-scala-part-12-type-classes.html)，因此当 DSL 用于相应的目标类型时，它们可以被编译器自动解析。

开始时，我们将选择一个目标类型，它使得在一个简单的、自包含的程序中测试概念变得容易。在真实的场景中，您可能会遵循相同的方法:从为您的 DSL 提供易于使用的解释器开始，这些解释器可以在测试用例中使用。这种方法类似于实现模拟，不同之处在于我们的解释器是 DSL 的全功能实现。

稍后，我们可以继续为更复杂的目标类型实现解释器，包括额外的关注点，如并发性和副作用。

# 用于认证 DSL 的解释器

我们在 Dsl 特征的伴随对象中实现解释器，从而支持编译器的隐式解析机制。

```
package ch.becompany.authn

trait Dsl[F[_]] {
  …
}

object Dsl {

  type UserRepository = List[User]

  type UserRepositoryState[A] = State[UserRepository, A]

  implicit object StateInterpreter extends Dsl[UserRepositoryState] {

     override def register(email: String @@ EmailAddress, password: String):
         UserRepositoryState[Either[RegistrationError, User]] =
       State { users =>
         if (users.exists(_.email === email))
           (users, RegistrationError("User already exists").asLeft)
         else {
           val user = User(email, password)
           (users :+ user, user.asRight)
         }
       }

     override def authn(email: String @@ EmailAddress, password: String):
         UserRepositoryState[Either[AuthnError, User]] =
       State.inspect(_
         .find(user => user.email === email && user.password === password)
         .toRight(AuthnError("Authentication failed")))
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望将注册用户存储在一个列表中，所以我们的用户存储库类型是一个简单的用户列表:

```
type UserRepository = List[User] 
```

Enter fullscreen mode Exit fullscreen mode

我们将利用[状态](https://typelevel.org/cats/datatypes/state.html)单子将用户存储库从一个 DSL 函数调用传递到下一个:

```
type UserRepositoryState[A] = State[UserRepository, A] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们定义 StateInterpreter，它是针对 UserRepositoryState monad 的认证 DSL 的解释器。请注意，该对象是用隐式修饰符声明的，这使得它在请求该目标类型的 DSL 解释器时对编译器可见。

```
implicit object StateInterpreter extends Dsl[UserRepositoryState] {

   override def register(email: String @@ EmailAddress, password: String):
       UserRepositoryState[Either[RegistrationError, User]] =
     State { users =>
       if (users.exists(_.email === email))
         (users, RegistrationError("User already exists").asLeft)
       else {
         val user = User(email, password)
         (users :+ user, user.asRight)
       }
     }

   override def authn(email: String @@ EmailAddress, password: String):
       UserRepositoryState[Either[AuthnError, User]] =
     State.inspect(_
       .find(user => user.email === email && user.password === password)
       .toRight(AuthnError("Authentication failed")))
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 运行程序

现在我们已经为 DSL 提供了一个解释器，我们可以执行 registerAndLogin 程序了，这个程序已经在我们的主应用程序中实现了。

```
package ch.becompany

object Main extends App {

  def registerAndLogin[F[_] : Monad](implicit authnDsl: AuthnDsl[F]):
      F[Either[AuthnError, User]] = {

    val email = tag[EmailAddress]("john@doe.com")
    val password = "swordfish"

    for {
      _ <- authnDsl.register(email, password)
      authenticated <- authnDsl.authn(email, password)
    } yield authenticated
  }

  val userRepositoryState = registerAndLogin[UserRepositoryState]

  val result = userRepositoryState.runEmpty

  val (users, authenticated) = result.value

  println("Authenticated: " + authenticated)
  println("Registered users: " + users)

} 
```

Enter fullscreen mode Exit fullscreen mode

通过用 UserRepositoryState 类型参数值调用 registerAndLogin，我们指示编译器为 UserRepositoryState 目标 monad:
解析解释器(声明为隐式参数 authnDsl)

```
val userRepositoryState = registerAndLogin[UserRepositoryState] 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 runEmpty 方法传递一个空的用户列表作为初始状态:

```
val result = userRepositoryState.runEmpty 
```

Enter fullscreen mode Exit fullscreen mode

runEmpty 方法返回 Eval monad 的一个实例，它的计算产生一个由最终状态(在我们的例子中是包含所有注册用户的用户存储库)和程序返回值(在我们的例子中是认证结果)组成的元组。现在我们终于可以使用 Eval monad 的 value 方法提取这些值，并打印结果:

```
val (users, authenticated) = result.value
  println("Authenticated: " + authenticated)
  println("Registered users: " + users)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们程序的输出如下所示:

```
Authentiated: Right(User(john@doe.com,swordfish))
Registered users: List(User(john@doe.com,swordfish)) 
```

Enter fullscreen mode Exit fullscreen mode

### 下一步:组合多个 DSL

为了支持在一个 for-comprehension 中组合来自不同 DSL 的调用，所有这些函数必须在同一个 monad 中返回它们的值。

在许多情况下，可以选择一个 monad 来解决所有需要关注的问题，通常是副作用和错误处理。例如来自 [ScalaZ](https://github.com/scalaz/scalaz) 库的[任务](http://timperrett.com/2014/07/20/scalaz-task-the-missing-documentation/)类型或者来自 [cats-effect](https://typelevel.org/cats-effect/) 库的 [IO](https://typelevel.org/cats-effect/datatypes/io.html) 类型。

但是找到一种通用的方法来处理这种限制实际上证明是相当具有挑战性的。一个可能的解决方案是使用自由单子，例如 Eff 单子；这种方法将在下一篇文章中介绍。

# 进一步阅读

*   [优化无标签最终版——告别免费](https://typelevel.org/blog/2017/12/27/optimizing-final-tagless.html)来自 Typelevel 博客
*   [从 scalac 团队博客中探索广泛可读的 Scala 代码的无标签最终模式](https://blog.scalac.io/exploring-tagless-final.html)
*   免费和无标签的比较——如何不要过早地选择一个单子
*   [无标签期末考试简介](https://www.beyondthelines.net/programming/introduction-to-tagless-final/)来自 Beyond the Lines 博客

非常感谢您的阅读！请在下面留下您的评论，如果您有进一步的问题，请随时通过[Andreas . hartmann @ becom pany . ch](//mailto:andreas.hartmann@becompany.ch)与我联系。

* * *

封面照片:[格洛斯特大教堂](https://unsplash.com/photos/erRtgkmFvh4)由[纳塔莎·维尔马宁](https://unsplash.com/@thenathasja)在 Unsplash 上拍摄