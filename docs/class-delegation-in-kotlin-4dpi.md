# 科特林的班级代表团

> 原文：<https://dev.to/lankydandev/class-delegation-in-kotlin-4dpi>

> 在软件工程中，委托模式是一种面向对象的设计模式，它允许对象组合实现与继承相同的代码重用。

是的，这是从维基百科复制粘贴的教科书😬。我引用这句话作为开场白，因为它是对[委托模式](https://en.wikipedia.org/wiki/Delegation_pattern)的简明描述。喜欢组合胜过继承是几乎每个使用面向对象语言(如 Java 和 Kotlin)的开发人员都会提到的事情。使用这种模式可以让你在秘密编写实现接口的代码的同时，与讨厌继承的开发人员保持良好的关系。

好吧，我可以看到你抽搐(不是真的🙄).你想知道科特林在哪里发挥作用🤔。

Kotlin 通过提供关键字`by`使遵循委托模式变得更加容易。在你的代码中使用这个关键字允许你实现(**而不是**一个类)一个接口，同时将它的所有功能委托给`by`指定的*委托*。所有的接口函数都将得到满足，而不需要您自己在新类中提供一个实现。一种思考方式是，*委托*基本上变成了一个伪抽象类。

现在，您可以挑选想要提供自己的实现的接口函数。任何你没有提供的都被委托给*代表*。这就是力量的来源。

我想是时候给你举个例子了。下面是一些在 [Corda](https://github.com/corda/corda) 中使用的代码(完全用 Kotlin 编写，实际上在 [Kotlin 主页](https://kotlinlang.org/)上有提及)👏👏).这也是我第一次看到`by`被使用:

```
class RestrictedEntityManager(private val delegate: EntityManager) : EntityManager by delegate {

  override fun close() {
    throw UnsupportedOperationException("This method cannot be called via ServiceHub.withEntityManager.")
  }

  override fun clear() {
    throw UnsupportedOperationException("This method cannot be called via ServiceHub.withEntityManager.")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的类做了我刚才谈到的所有事情。所以让我们仔细看看。`RestrictedEntityManager`想实现`EntityManager`。为此，它使用了由`delegate`属性提供的实现。

注意，`by`关键字在这里的用法。对我来说，这表示，`EntityManager`是实现了`by`的`delegate`属性。

顾名思义，`RestrictedEntityManager`这个类想要限制`EntityManager`的一些功能。提供并使用`close`和`clear`的覆盖，而不是`delegate`提供的版本。

这给你留下了一个新的类，`RestrictedEntityManager`，它实现了`EntityManager`接口中的每一个函数(如果我没数错的话，有 51 个函数😳😵)而自己只写 2。这不仅允许你更懒惰，它实际上更清晰，并且只依赖于接口而不是具体的类。

在这个问题上，来自科特林医生的一条信息相当重要。

> 但是，请注意，以这种方式重写的成员不会从委托对象的成员中调用，委托对象只能访问其自己的接口成员实现

我前面说过*委托*就像一个抽象类。上面的陈述稍微否定了这一点，因为你提供的实现根本不会被*委托*使用。但是，我仍然认为这种比较是一种很好的思考方式，你只需要记住这个事实，以防止将来出现错误。

在我结束这篇文章之前，我想比较一下上面的代码和 Java 版本。这将让你看到 Kotlin 如何为你提供工具来改进你的代码:

```
public class RestrictedEntityManager implements EntityManager {

  private EntityManager delegate;

  public RestrictedEntityManager(EntityManager delegate) {
    this.delegate = delegate;
  }

  @Override
  public void close() {
    throw new UnsupportedOperationException("This method cannot be called via ServiceHub.withEntityManager.");
  }

  @Override
  public void clear() {
    throw new UnsupportedOperationException("This method cannot be called via ServiceHub.withEntityManager.");
  }

  @Override
  public void persist(Object entity) {
    delegate.persist(entity);
  }

  @Override
  public <T> T merge(T entity) {
    return delegate.merge(entity);
  }

  // and another 47 functions/methods 😰😰😱
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一行代码注释足以让您相信 Kotlin 解决方案的优雅。为了清楚起见，所有缺少的方法只是委托给了`delegate`属性。如果您确实对一些函数进行了更改，并且没有很好地排序，您可能会发现很难注意到您做了什么。另一方面，Kotlin 版本只包含被修改的函数，默认情况下委托其他函数。

最后，这篇短文介绍了 Kotlin 提供的关键字`by`,以及它如何帮助您利用委托模式。帮助您在成为超级开发人员的道路上更进一步。放下那个爪哇☕🤮你紧紧抓住的东西，用双手抓住科特林👌👏👏。

只是为了保护我自己。我不反对 Java。请不要来杀我！😨😨

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 来了解我的新文章。