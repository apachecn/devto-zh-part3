# Kotlin 类实现函数

> 原文：<https://dev.to/grahamcox82/kotlin-classes-implementing-functions-18e8>

我今天发现的一个小技巧。

您可能知道，在 Kotlin 中——就像在 Java 8 中一样——一个函数能够实现一个方法接口，如下:

```
 Assertions.assertAll(
                Executable { Assertions.assertEquals(clientId, token.client) },
                Executable { Assertions.assertEquals(userId, token.user) },
                Executable { Assertions.assertEquals(NOW, token.issued) },
                Executable { Assertions.assertEquals(NOW.plus(duration), token.expires) },
                Executable { Assertions.assertEquals(setOf(GlobalScopes.ALL), token.scopes) }
        ) 
```

这些`Executable`条目中的每一个都定义了一个与`Executable`接口匹配的函数。

然而，反之亦然。您可以编写一个可以在任何需要特定函数类型的地方使用的类。例如:

```
class UUIDNonceGenerator : () -> String {
    /**
     * Generate the Nonce
     * @return the nonce
     */
    override operator fun invoke() = UUID.randomUUID().toString()
} 
```

这个类的一个实例可以在任何接受 0 个参数并返回一个字符串的函数的地方使用。

这样做的理由实际上很合理。该类实际上实现了`kotlin.jvm.functions.Function0<java.lang.String>`，这正是`() -> String`的含义。所以它实际上只是一个实现接口的类。碰巧这是双向的，所以任何接受了`() -> String`的东西实际上都接受了`kotlin.jvm.functions.Function0<java.lang.String>`，因此类型匹配。瞧。

不知道这有多有用，但如果你需要，它就在那里。