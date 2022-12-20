# Swift For-In 循环

> 原文：<https://dev.to/amarildo/for-in-loops-3non>

在 [Swift 语言](https://developer.apple.com/swift/)中，我们使用`for-in`循环来迭代一个序列。例如，一个序列包含的项目有`Array`元素、`Range`数字或`String`中的`Character`。

让我们快速试验一些代码。你可以像这样重复一个音乐流派`Array`例如:

```
// Iterate genres
let genres = ["Ambient", "Dubstep", "Electronic", "Hip Hop", "Jazz"]
print("Choose a genre:")
for genre in genres {
  print("\(genre)")
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，我们迭代了一个`Array`来打印里面的每一个类型。我们可以更进一步，用一个`Dictionary`代替一个`Array`来做同样的尝试。

现在我们有了文件类型的音乐标题。

```
// Iterate dictionaries
let titleWithAudioTypes = ["Castle in the Hill": "MP3", "Different seas": "WAV", "See you again": "FLAC"]
for (title, audioType) in titleWithAudioTypes {
  print("\(title).\(audioType.lowercased())")
} 
```

Enter fullscreen mode Exit fullscreen mode

上面，`Dictionary`中的每一项都作为**(键，值)**对返回。

现在让我们说，我们要把一个声音重复五次。您可以使用带有数字`Range`的`for-in`循环来实现这一点。

```
// Iterate ranges
let soundTitle = "See you again.mp3"
let soundRepeats = 5
for time in 1...soundRepeats {
  print("Repeating \(soundTitle): \(time) time(s)")
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键是关闭的`Range`操作符`(...)`。大多数时候，当你不需要循环中的常量时，例如在`for time in (...)`中的时间，你可以用下划线`_`代替变量名来忽略这些值。`time`它的隐含性通过包含在循环声明中来声明。

你可以在[苹果 Swift Book](https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html) 上阅读更多关于 Swift 中`for-in` loops 的内容。