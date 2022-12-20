# 科特林具体化泛型:解释

> 原文：<https://dev.to/cjbrooks12/kotlin-reified-generics-explained-3mie>

Kotlin 是一种奇妙的语言，充满了许多小东西和惊喜，使日常开发变得更加愉快。它的大部分好东西实际上只是一个非常智能的编译器，为你做了很多你本来会做的事情，具体化泛型就是一个很好的例子。

如果你坐在那里想“怎么回事🙊是具体化的泛型，我应该在什么时候使用它们？”，那么这个帖子就是给你的。以下是我在 Kotlin Slack 上对这个问题的回答，我想它也能帮助这里的人们。

在普通的 Java 泛型中，泛型参数只有在编译时才是已知的，并在运行时被删除。Kotlin 的具体化泛型允许你假装泛型在运行时仍然存在，当实际使用时，用已知类型有效地替换占位符`<T>`。

例如:

```
class JavaClass {
    <T> List<T> getList() { ... }
}
// somewhere else
List<String> list = new JavaClass().getList<String>(); 
```

Enter fullscreen mode Exit fullscreen mode

有效地用 Java 编译成

```
class JavaClass {
    List getList() { ... }
}
// somewhere else
List list = new JavaClass().getList(); 
```

Enter fullscreen mode Exit fullscreen mode

这里的假设是，如果类型检查器能够在编译时验证 T 在任何地方都是安全的，那么在运行时对该方法的所有调用都是安全的，并且不再真正需要`T`。但是有时候在运行时知道 T 参数会很好，这是具体化泛型所允许的。

在运行时，您可能希望使用那个`T`的一个例子是从一个 map 中获取一个值，其中 map 可以保存任何类型，并且您的类需要检查 map 中对象的类型。

```
class JavaClass {
    <T> T getItemFromMap(String key) { 
        Object item = _map.get(key); // this is OK
        if(item instanceof T) { // compilation error, T is not known here at runtime
            return (T) item
        }
        else {
            return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

解决方案是传递一个与 T 参数匹配的类对象，这个类对象有一些方法可以帮你检查:

```
class JavaClass {
    <T> T getItemFromMap(Class<T> itemClass, String key) { 
        Object item = _map.get(key); // this is OK
        if(itemClass.isAssignableFrom(item.getClass())) { // this works
            return (T) item
        }
        else {
            return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在我们的方法很难看，我们必须向它传递两个参数才能让它工作，而在编译时所有的信息都是现成的。具体化的泛型使得绕过这个限制成为可能。JVM 根本没有能力在运行时做到这一点，所以具体化泛型在编译时使用了一个技巧来使它看起来像这样:它只是内联函数调用，并用内联代码中的实际类替换泛型参数。

回到 map 的例子，我们可以使用如下的具体化泛型来重写它:

```
inline fun <reified T> getItemFromMap(String key) { 
    val item = _map.get(key)
    if(T::class.java.isAssignableFrom(item.getClass())) {
        return (T) item
    }
    else {
        return null;
    }
}
// somewhere else
val list: String = getItemFromMap("key") 
```

Enter fullscreen mode Exit fullscreen mode

有效编译到

```
val item = _map.get(key)
val list: String = if(String::class.java.isAssignableFrom(item.getClass())) {
    (String) item
}
else {
    null;
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这有所帮助！