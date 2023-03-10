# 初级 Kotlin 运行时检查教程:要求，检查，断言

> 原文：<https://dev.to/trevornemanic/beginner-kotlin-runtime-check-tutorial-require-check-assert-387l>

当我们的函数被调用时，我们经常假设程序的状态是可接受的。有时我们假设状态或输入可能是无效的，所以我们做了如下的事情:

```
fun doThing(foo: Bar?) {
    if (foo == null) {
        return
    }
    .... // what you meant to do
} 
```

这两种假设都可能导致难以发现的错误和不可读的代码。幸运的是，Kotlin 标准库提供了三个基本功能来确保一切正常:

1.  Require:我的函数需要参数以这种方式有效。为假时抛出一个`IllegalArgumentException`。
2.  检查:我的函数的依赖关系处于正确的调用状态。为假时抛出一个`IllegalStateException`。
3.  断言:我的函数的结果是有效的。为假时抛出一个`AssertionError`。此外，运行时断言必须在 JVM 上用`-ea`启用，或者用 Native 启用。

除了条件验证之外，这些函数中的每一个都有一个可选的惰性参数，用于在计算结果为 false 时显示错误消息。所有三个运行时检查 API 都是相同的。这里有一个来自`require`的例子。

```
fun require(value: Boolean)
inline fun require(value: Boolean, lazyMessage: () -> Any) 
```

让我们深入了解一下每个运行时检查，然后编写代码来发射激光炮

# 要求

`Require`用于参数验证。它确保其参数处于有效状态。

```
fun fireLaser(cannonNumber: Int) {
    require(cannonNumber >= 0) { "Must pass a valid cannon number: $cannonNumber < 0" }
    .... // fire laser beams!!!
} 
```

# 检查

确保我们函数的依赖项可以被安全地调用。它用于验证一切准备就绪。

```
fun fireLaser() {
    // setup above
    check(laserCannonManager.isReadyToFire) { "You must arm the lasers before firing!" }
    .... // fire laser beams!!!
} 
```

# 断言

在函数完成或返回之前进行最后一点验证。在把控制权交给程序的另一部分之前，这是你最后的机会。

```
fun fireLaser() {
    // setup above
    val fired = laserCannon.fire()
    assert(fired == true) { "cannon did not fire successfully" }
} 
```

# 为什么运行时检查？

除了单元测试之外，它们还提供了运行时保证。这些检查迫使程序快速失败，并给开发人员比来自远处失败组件的堆栈跟踪更多的信息。

显然，这些检查不应该无处不在，应该明智地添加。存在于复杂空间中的关键功能将受益最大。看看下面所有三个运行时检查如何确保我们的核心激光炮功能的正确使用。

```
fun fireLaser(cannonNumber: Int) {

    // first, validate parameters
    require(cannonNumber >= 0) { "Must pass a valid cannon number"}

    // then, verify working dependencies
    check(laserCannonManager.isReadyToFire) { "You must arm the lasers before firing!" }
    check(guidanceSystem.canLockOn()) { "Cannot lock on target" }

    // call laser code safely
    laserCannonManager.charge(cannonNumber)
    guidanceSystem.lockOn()
    val laserCannon = laserCannonManager.cannons[cannonNumber]
    val fired = laserCannon.fire() // fire laser beams!!!

    // lastly, make sure our cannon fired flawlessly
    assert(fired == true) { "cannon did not fire successfully" }

} 
```

希望您对这些运行时条件有一个舒适的理解。欢迎在评论中发表你的任何问题！你喜欢你读到的东西吗？你也可以在 Twitter 上关注我的 Kotlin 和 Android 内容。