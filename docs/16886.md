# 2019 年使用 Groovy 的 Goovy Java 10 大理由

> 原文：<https://dev.to/jcoelho/10-reasons-to-use-groovy-in-2019-431f>

## 为什么是 Groovy？

我加入公司的 **DevOps 团队**已经有一年了，我们使用的主要工具之一是[詹金斯](https://jenkins.io/)，它和 **Groovy** 相处得很好。我主要使用 Groovy 来编排管道和自动化一些无聊的任务。

Groovy 是一种强大的 Java 平台语言，它可以与任何 Java 程序顺利集成。它也是一种很棒的脚本语言，语法强大且易于学习。

> [Groovy]旨在提高开发人员的生产力，这得益于简洁、**熟悉且易于学习的
> 语法**。它可以与任何 Java 程序顺利集成，并立即为您的应用程序提供
> 强大的功能，包括脚本功能、**特定领域语言**创作、
> 运行时和编译时**元编程**和**功能性**编程。

下面是我在过去一年中学到的让我爱上 Groovy 的 10 个特性:

### 1。简单

来自 Java 后端开发团队，学习 Groovy 对我来说轻而易举。它建立在 Java 标准库之上，提供了额外的特性。它们中的大多数使得编程更加简单。

#### 1.1 申报清单/地图

Groovy 是一种可选类型的语言，你可以使用`def`关键字来声明变量。例如，声明列表或映射非常简单:

```
def myList = []
def myMap = [:] 
```

Enter fullscreen mode Exit fullscreen mode

#### 1.2 遍历列表/映射

使用**闭包** :
对它们进行迭代是非常容易和易读的

```
myList.each {element -> 
    println element
}

myMap.each {entry -> 
    println "User: $entry.user | Email: $entry.email"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 2。字符串插值

> [字符串]插值是在对字符串求值时用字符串中的值替换占位符的行为。

在 Groovy 中，占位符用`${}`包围，或者用`$`作为带点表达式的前缀。

在前面的代码片段中，我们可以看到一个字符串插值的例子。不过这里还有一个:

```
try{
    throw new Exception()
}catch(Exception e){
    println "Error during operation. Cause: ${e}"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3。铸造

Groovy 使用`as`操作符使造型变得非常容易和易读。要使用这个操作数，被造型的类必须实现`asType()`方法。这已经发生在标准类中，比如列表、枚举器等。

例如:

```
enum Employee {
    MIKE,
    HANNA
}

String name = "JOHN"

try{
    name as Employee
}catch(Exception e){
    println "Could not find employee ${name}. Cause: ${e}"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。Json 到类

我经常使用带有 Json 响应的 Web 服务，所以不可避免地必须将响应映射到 Groovy 类。这是 Groovy 自带的，非常简单，只需通过类构造函数传递一个 Json。

```
String response = '{name:"John", position: "Developer", age: 32}'

// Json response to map
def employeeMap = new JsonSlurper().parseText(response)
// Consider an Employee class with the attributes name, position and age
Employee employee = new Employee(employeeMap) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。我们刚刚从 Json 字符串构建了一个 employee 对象。

反过来也一样简单:

```
def json = JsonOutput.toJson(employee)
// json == '{name:"John", position: "Developer", age: 32}' 
```

Enter fullscreen mode Exit fullscreen mode

### 5。断言

Groovy 和 Java 有相同的`assert`语句，但是功能更强大——因此它被命名为*权力断言*。

不同之处在于断言解析为`false`时的输出。比如:

```
def contacts = ['John', 'Anna']

assert contacts.isEmpty()

//Output:
//ERROR org.codehaus.groovy.runtime.powerassert.PowerAssetionError:
//assert contacts.isEmpty()
//       |        |
//       |        false
//       [John, Anna] 
```

Enter fullscreen mode Exit fullscreen mode

这使您能够非常容易地理解是什么导致了断言失败。

### 6。定义变量

Groovy 是可选的类型，这意味着你可以用它的类型定义一个变量或者简单地使用关键字`def`。这同样适用于声明列表或映射，它们的类型是可选的。比如:

```
String name
int age
def address
List friends = ['John', 'Anna']
Map family = ['Mother':'Mary', 'Father':'Joseph']

def getFamilyMember("Mother"){ ... } 
```

Enter fullscreen mode Exit fullscreen mode

对于懂 Javascript 的人来说，这类似于关键字`var`。

这给了你难以置信的灵活性，但是使用时要小心。这可能会让您的团队或使用您代码的其他人更难阅读它，更难理解什么是预期的输入或输出。

### 7。散列法

如果您曾经使用过 Java，您可能知道散列一个字符串有多冗长——除非您使用的是第三方库。

Groovy 2.5 给`String`类带来了一些有用的方法。计算哈希值就像在字符串上调用方法一样简单。Groovy 让它变得简单:

```
def password = "thisIsMyPassword"

def md5 = password.md5()
def sha256 = password.sha256()
//For other algorithms use digest() method
def sha1 = password.digest('SHA-1')
... 
```

Enter fullscreen mode Exit fullscreen mode

### 8。经营者

Groovy 支持其他语言中最常见的操作符。但是这还不够，Groovy 还提供了一些更有趣的操作符。以下是一些例子:

#### 猫王符

这是三元运算符的一个较短版本。这非常有用，例如，当条件可能评估为 null 时。

```
// Instead of this
def user = person.name ? person.name : 'Guest'
// Use this
def user = person.name ?: 'Guest' 
```

Enter fullscreen mode Exit fullscreen mode

#### 安全导航操作员

另一个可以用来检查变量是否为空的操作符是安全导航操作符。

```
def user = person?.name
// user == null 
```

Enter fullscreen mode Exit fullscreen mode

当你想避免`NullPointerException` s 时使用这个操作符。如果你访问的对象为空，这个操作符将返回一个`null`而不是抛出一个`NullPointerException`。

#### 传播算子

spread 操作符(`.*`)用于对集合中的所有项目执行操作，而不是像我们之前看到的那样使用循环或闭包。比如:

```
def numbers = [1,2,3,4,5,6]
*numbers // == [1,2,3,4,5,6]

def people = [
    new Person(name: 'John', age: '25'),
    new Person(name: 'Anna', age: '21')
]

def friends = people*.name // friends = ['John', 'Anna'] 
```

Enter fullscreen mode Exit fullscreen mode

### 9。特征

> 特征是语言的结构性构造，它允许:
> 
> *   行为构成
>     
>     
> *   接口的运行时实现
>     
>     
> *   行为覆盖
>     
>     
> *   与静态类型检查/编译的兼容性

我喜欢把特征想象成接口，在那里你可以实际实现方法。当你有一个非常复杂和结构化的应用程序，并且你想保持整洁的时候，这是非常有用的。

这绝对是我在早期 Java 中错过的东西。

这里有一个例子:

```
trait Sociable {
    void greet() { println "Hello!" }
}

class Person implements Sociable {}

Person p = new Person()
p.greet() // Hello! 
```

Enter fullscreen mode Exit fullscreen mode

### 10。正则表达式

Groovy 原生支持正则表达式，并且非常简单。它有 3 个正则表达式运算符:

*   `~`这是**模式操作符**，它是创建`java.util.regex.Pattern`实例的简单方法:

```
def pattern = ~"^abc{2}\d"
// pattern instanceof Pattern == true 
```

Enter fullscreen mode Exit fullscreen mode

*   `=~`这是**查找操作符**，它将在字符串中查找模式并返回一个`Matcher`:

```
def pattern = ~"abc{2}\d"
def found = "abcc5" =~ pattern
// found instanceof Matcher == true 
```

Enter fullscreen mode Exit fullscreen mode

*   最后是**匹配操作符** `==~`，如果字符串是正则表达式的严格匹配，则返回`true`:

```
def found = "abcc5" ==~ pattern
// found == true 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

如果您已经用 Java 或其他 OOP 语言编程多年，那么 Groovy 就像一股新鲜空气。

它让事情变得更加容易和简单，也不那么罗嗦。加上脚本和特定领域语言功能等额外特性，将 Groovy 推向了一个新的高度，并赋予了它旧语言所没有的全新外观。

我希望你会对这篇文章感兴趣，如果你有 Groovy 的经验以及如何使用它，请在下面的评论中告诉我。:)