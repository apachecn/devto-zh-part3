# 零值终于击中了我

> 原文：<https://dev.to/mjb2kmn/go-zero-values-finally-hit-me-58ec>

我决定通过打开文档和编辑器来学习围棋(不是我的第一语言)。经过两个星期和几百行代码，我有了一个实用的工具，并且非常喜欢使用 Go。

我曾在各处看到过“零值”的提法，但没有太注意。我的变量定义、赋值、流控制等等都如预期的那样工作，所以我不觉得我错过了什么。
然后，突然，布尔变量的*零值*是`false`的想法穿透了我故意的无知，我重新检查了我的代码。我发现了很多类似的模式:

```
type Test struct {
    Pass    bool
}

if len(test.Passes) > 0 && len(test.Fails) == 0 {
    test.Pass = true
} else {
    test.Pass = false
} 
```

但是如果“零值”意味着简单地声明一个变量名和类型就能保证它用一个已知值初始化，那么`test.Pass`一存在就已经是`false`了。这意味着所有这些`else`分支都是多余的。

```
type Test struct {
    Pass    bool
}

if len(test.Passes) > 0 && len(test.Fails) == 0 {
    test.Pass = true
} 
```

仅仅通过了解和使用所有基本类型的零值，我就能够删除很大一部分行并简化逻辑。

在这之后，我决定不仅仅通过语言规范来学习，并买了一本书。这本 Go 编程语言的书在大约前十几页中涵盖了“零值”。(`palm.Smack(&forehead)`)

这里的教训不仅仅是 Go 的零值使 Go 代码更加简洁、安全和可读，而且...我很确定这里还有另一个教训。也许有人能发现它并给我解释一下？；)