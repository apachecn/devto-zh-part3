# 用 scala 从头开始创建一个验证 DSL

> 原文：<https://dev.to/namnguyen/create-a-validation-dsl-from-scratch-with-scala-91b>

# 简介

如果我必须选择任何一个主题来谈论函数式编程，我会选择验证。有没有开发人员不需要验证一些输入数据并提供所有相关的错误？

初级开发人员经常会忽略错误处理过程。他们接受正确的输入，测试程序，当他们看到程序运行时非常高兴，然后推向生产。

处理错误的更好方法是捕捉主程序中所有可能的错误。这并不理想，因为如果您有一个包含数百个参数的大型配置文件，那么您必须一个接一个地纠正错误。我们想一次看到所有的错误。让我们探索函数式编程技术如何很好地解决这个问题。

# 简单实现

在函数式编程中，我们必须考虑最能描述我们问题的类型。`Try`或`Either`数据类型不是最佳选择，因为它只能携带一个错误值。所以让我们编写我们的`Validation`数据类型。

```
sealed trait Validated[E, A]
case class Invalid(errors: List[E]) extends Validated[E, A]
case class valid(result: A) extends Validated[E, A] 
```

Enter fullscreen mode Exit fullscreen mode

我们用两个类型参数`E`和`A`定义了数据类型`Validated`。如果它可能是一个携带类型为`E`的错误列表的无效结果，如果它是一个有效结果，它将携带类型为 a 的结果。

等一下；这里可能有问题。我们知道一个`List`可能是`Empty`或`having something`。在这种情况下，这个值存在的可能性:`Invalid(Nil)`而且是非常错误的。一个不能提供任何错误值的无效值究竟是怎么回事？如果没有错误，那么它一定是一个`Valid`值。

让我们引用亚龙·明斯基的话

> 使非法国家没有代表性

我们来做一个叫`NonEmptyList`的永远不能为空的数据类型。

```
case class NonEmptyList[A](first: A, rest: List[A]) 
```

Enter fullscreen mode Exit fullscreen mode

要考虑的另一个改进是，我们必须使数据类型由`E`和`A`协变，以接受`E`和`A`的任何子类型

这是我们的数据类型
的完整定义

```
case class NonEmptyList[+A](first: A, rest: List[A])

sealed trait Validated[+E, +A]
case class Invalid(errors: NonEmptyList[E]) extends Validated[E, A]
case class Valid(result: A) extends Validated[E, A] 
```

Enter fullscreen mode Exit fullscreen mode

让我们举个例子更容易理解。假设我们有一个形式为`Map[String, String]`的输入，我们想验证它并将其转换为如下定义的`Person`类型:

```
case class Person(name: String, age: Int) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要从`Map`中解析两个字段。第一个字段是类型为`String`的`name`。我们必须定义我们的异常数据类型。为了举例，我们只能指定一种错误数据类型。

```
case class ErrorParsing(message: String)

type Validation[A] = Validated[ErrorParsing, A] 
```

Enter fullscreen mode Exit fullscreen mode

让我们编写代码来解析来自`input`
的`fieldName`字符串值

```
def validateString(input: Map[String, String], fieldName: String): Validation[String] = {
    input.get(fieldName) match {
      case Some(v) => Valid(v)
      case None => Invalid(NonEmptyList(ErrorParsing(s"Field $fieldName does not exist"), Nil))
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

另一个函数解析来自`input`
的`Int`值

```
 def validateInt(input: Map[String, String], fieldName: String): Validation[Int] = {
    input.get(fieldName) match {
      case Some(v) => Try(v.toInt) match {
        case Success(int) => Valid(int)
        case Failure(exception) => Invalid(NonEmptyList(ErrorParsing(s"Cannot convert $v to String"), Nil))
      }
      case None => Invalid(NonEmptyList(ErrorParsing(s"field $fieldName does not exist"), Nil))
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个值`Validation[String]`和一个值`Validation[Int]`

```
def validatePerson(input: Map[String, String]): Validation[Person] = {
    val validatedName = validateString(input, "name")
    val validatedAge = validateInt(input, "age")

    (validatedName, validatedAge) match {
      case (Valid(name), Valid(age)) => Valid(Person(name, age))
      case (Invalid(nameError), Valid(_)) => Invalid(nameError)
      case (Valid(_), Invalid(ageError)) => Invalid(ageError)
      case (Invalid(errorName), Invalid(errorAge)) => Invalid(
        NonEmptyList(errorName.first, errorAge.first :: errorName.rest)
      )
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这段代码完成了这项工作。但不知何故，我们可以看到这段代码是不可重用的。如果我们有另一个名为`Position(x: Int, y: Int)`的 case 类，我们必须一次又一次地重写相同的代码。

这导致我们编写一个更通用的函数 validate2，它接受两个不同类型的验证，并为第三个值使用一个构造函数。

```
def validate2[A, B, C](first: Validated[ValidationError, A], second: Validated[ValidationError, B])(f: (A, B) => C): Validated[ValidationError, C] = {
    (first, second) match {
      case (Valid(a), Valid(b)) => Valid(f(a, b))
      case (Invalid(firstError), Valid(_)) => Invalid(firstError)
      case (Valid(_), Invalid(secondError)) => Invalid(secondError)
      case (Invalid(firstError), Invalid(secondError)) => Invalid(
        NonEmptyList(firstError.first, secondError.first :: firstError.rest)
      )
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 猫简介

函数`validate2`与应用函子中定义的函数`product`非常相似。

```
trait Applicative[F[_]] extends Functor[F] {
    def product[A, B](fa: F[A], fb: F[B]): F[(A, B)]
} 
```

Enter fullscreen mode Exit fullscreen mode

这比我们只处理类型`Validated[ValidationError, A]`的代码更通用。这段代码可以处理很多我们已经知道的高级类型(`Option`、`Either`、`Future`、`List`)。使用像`cats`这样定义了所有这些函数的库非常方便。使用`cats`库，我们可以自动拥有`map2`、`map3`甚至`mapN`

看看 cats 中的[验证，看看定义验证的非常方便的语法:](https://typelevel.org/cats/datatypes/validated.html) 

```
def validateForm(username: String, password: String, firstName: String, lastName: String, age: Int): ValidationResult[RegistrationData] = {
    (validateUserName(username),
    validatePassword(password),
    validateFirstName(firstName),
    validateLastName(lastName),
    validateAge(age)).mapN(RegistrationData)
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

在本文中，我介绍了验证的需求和简单实现。我们很快就意识到我们需要一个更强大的抽象来重用代码。这就是像 cats 或 scalaz 这样的泛型库能够大放异彩的原因。