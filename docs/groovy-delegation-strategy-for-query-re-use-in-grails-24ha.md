# Grails 中查询重用的 Groovy 委托策略

> 原文：<https://dev.to/handy/groovy-delegation-strategy-for-query-re-use-in-grails-24ha>

## 问题

我们的许多 web 应用程序*视图*/页面，以及其他功能，都由来自数据库表的条目列表组成。例如，您可以进入*/客户/列表*，获取您的客户列表和数量。对于像我们这样的 MVC 应用程序来说，这是相当常见的。

我们发现，当从数据库中获取这些查询时，我们最终会重复许多代码，因为我们的许多域/表都是由相同的字段过滤的——条目的当前状态(启用或禁用)、属于某个公司、用户权限允许他/她查看等等。

所以我们最终会得到类似这样的东西来获取那些*列表*视图:
的信息

```
 class Customer {
        Company company
        boolean enabled
    }      

    def customerList(def params) {
        Customer.createCriteria().list {
            eq 'enabled', params.boolean('enabled')
            eq 'company',  params.company
            maxResults params.int('max', 10)
        }
    }

    def customerCount(def params){
        Customer.createCriteria().get {
            eq 'enabled', params.boolean('enabled')
            eq 'company', params.company
            projections {
                countDistinct 'id'
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
 class User {
        Company company
        boolean enabled
    }

    def userList(def params) {
        User.createCriteria().list {
            eq 'enabled', params.boolean('enabled')
            eq 'company', params.company
            maxResults params.int('max', 10)
        }
    }

    def userCount(def params){
        User.createCriteria().get {
            eq 'enabled', params.boolean('enabled')
            eq 'company', params.company
            projections {
                countDistinct 'id'
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

【list 和 count 方法的主体几乎是相同的，唯一的区别是查询所指向的表或域。

因此，我们正在寻找一种方法来重构这些代码，以避免重复我们自己。

## 解

我们将使用一个 **Groovy 闭包委托**来避免重复这些类型的查询。更具体地说，我们将使用称为闭包委托策略的东西来实现它。我们开始吧！

## 背景理论

### Groovy 闭包

闭包本身应该有一篇专门的博客文章，但是我发现 Groovy 文档非常清晰和中肯。如果您以前没有使用过闭包，或者您正在寻找更深入的理解，[请看看文档](http://groovy-lang.org/closures.html#_delegation_strategy)。

现在我将只简要地解释一些闭包概念。

一个 Groovy 闭包看起来像这样:

```
 def myClosure = { "This is a closure" } 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样称呼它:

```
 assert myClosure() == "This is a closure" 
```

Enter fullscreen mode Exit fullscreen mode

Groovy 闭包定义了三个主要组件:

*   **this:** 定义闭包的*类*
*   **所有者:**定义闭包的*封闭对象*，可以是一个类或另一个闭包。与**这个**的区别:它将返回**直接**封闭对象。如果闭包定义在内部类中，它将返回那个类，而 **this** 将返回顶级类。
*   **delegate:** 是一个第三方对象，每当消息的接收者未被定义时，方法调用或属性就被解析

如果没有相关的经验，很难完全理解这些概念，所以我在这里提供了一个 Groovy 片段，您可以在其中随意移动以完全掌握闭包组件。

run()方法包括一些注释来阐明发生了什么。你可以[复制粘贴到这里试试](https://groovy-playground.appspot.com/)！

```
 class OuterClass {

        String name = 'outer class'

        static void main(String[] args) {}

        class InnerClass {

            String name = 'inner class'

            def ownerClosure = { owner }
            def thisClosure = { this }
            def delegateClosure = { delegate }

            def shoutMyName = {
                name.toUpperCase()
            }
        }

        def nestedThisClosure = {
            def closure = { this }
            closure()
        }

        def nestedOwnerClosure = {
            def closure = { owner }
            closure()
        }

        void run() {
            def inner = new InnerClass()

            // Here, **this** and **owner** are equal, that is, the enclosing class (InnerClass)
            assert inner.thisClosure() == inner
            assert inner.ownerClosure() == inner

            // Here, **this** is the enclosing class (OuterClass) and **owner** is the enclosing closure (nestedOwnerClosure)
            assert nestedThisClosure() == this
            assert nestedOwnerClosure() == nestedOwnerClosure

            // The **delegate**, by default, is set to the **owner**
            assert inner.delegateClosure() == inner.ownerClosure()
            assert inner.shoutMyName() == 'INNER CLASS'

            // And even though we can change the **delegate**
            inner.shoutMyName.delegate = this

            // We are still getting our InnerClass name, why?
            assert inner.shoutMyName() == 'INNER CLASS'

            // The default **resolveStrategy** for closures is **OWNER_FIRST**,
            // that means that the name field of the **owner** is resolved first.
            // Luckily, we can also change the **resolveStrategy** of the closure
            inner.shoutMyName.resolveStrategy = Closure.DELEGATE_FIRST
            assert inner.shoutMyName() == 'OUTER CLASS'

        }
    }

    new OuterClass().run() 
```

Enter fullscreen mode Exit fullscreen mode

既然我们对闭包的功能有了更多的了解，我们可以实现我们的委托策略来重用我们的查询。

## 实现

正如在开始时提到的，我们有两个非常相似的域，具有相似的字段，我们需要在某个时候获得这些域的列表和计数。

为了解决这个问题，我们将**创建一个闭包**来获取列表和另一个计数闭包。然后，**我们根据我们正在查询的域来改变闭包的委托**。

假设我们有一个 ListService，我们将在其中管理所有这些逻辑。

以下是闭包:

```
 class ListService {

        private def getList = { def params ->
            createCriteria().list {
                eq 'company', params.company
                if(!params.boolean('showDisabled', false)) {
                    eq 'enabled', true
                }
                maxResults params.int('max', 10)
            }
        }

        private def getCount = { def params ->
            createCriteria().get {
                eq 'company', params.company
                if(!params.boolean('showDisabled', false)) {
                    eq 'enabled', true
                }
                projections {
                    countDistinct 'id'
                }
            }
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要动态地改变闭包的委托，这取决于要查询的域:

```
 class ListService {

        [...]

        def getDomainList(def params, def domain) {
            getList.delegate = domain
            getList(params)
        }

        def getDomainCount(def params, def domain) {
            getCount.delegate = domain
            getCount(params)
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将使用来自控制器的服务将值返回到我们的列表视图:

```
 class CustomerController {

        def listService

        def list() {
            [
                list: listService.getDomainList(params, Customer),
                count: listService.getDomainCount(params, Customer)
            ]
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

```
 class UserController {

        def listService

        def list() {
            [
                list: listService.getDomainList(params, User),
                count: listService.getDomainCount(params, User)
            ]
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

列表视图现在拥有了呈现每个结果并对其分页所需的一切！

## 总结

最后的流程是这样的:

1.  一个请求传到控制器，例如 UserController，以获取用户列表
2.  控制器**使用 ListService** 来查询数据库。它转发来自请求的参数，并且**指定要查询的域**
3.  ListService 使用给定的域来**改变闭包的委托**并继续执行闭包
4.  Closure 看到 createCriteria()方法调用并寻找实现它的对象。由于我们没有改变闭包的解析策略，**它将首先查看所有者**。所有者，我们的 ListService，没有实现 createCriteria()方法，所以闭包**现在查看委托**。**委托是我们的域**，它有一个 createCriteria 方法，所以它使用它并将我们的结果返回给我们的控制器。

现在我们有两个闭包，被多个域用来从数据库中获取相似的数据。

* * *

这只是 Groovy 中闭包代理的众多强大用例之一。你以前用过这个功能吗？如果有，请在评论中告诉我！

**感谢你为我朗读❤️**