# 角度指令和 AfterViewInit

> 原文：<https://dev.to/prestonjlamb/angular-directives-and-afterviewinit-k5c>

我最近需要为我的 Angular 应用程序创建一个属性指令。我希望能够为 typeahead 结果列表设计结果样式。重点是突出显示结果中与搜索词匹配的部分。例如，如果搜索词是“测试”，结果可能是这样的:

这是一个**测试**

我编写了所有的实用函数来匹配文本，在需要的地方添加了`strong`标签，我还编写了单元测试。但是当我实际执行该指令时，没有显示结果的文本；我刚得到一个空字符串。我不确定这是怎么回事，但是经过一点努力，我终于弄明白了。我将列出我所经历的，希望它能帮助其他人。

> 角度指令有三种风格:属性指令、结构指令和组件(是带有模板的指令)。属性指令改变 HTML 元素、另一个指令或组件的外观。结构化指令通过添加和删除 DOM 元素来改变 DOM 布局。最后，组件具有属性指令和潜在结构指令的所有功能，而且还包含一个模板。

因为我已经为指令和实用函数编写了单元测试，所以我知道指令正在工作。我能够测试匹配的术语在通过效用函数后被包装在`strong`标签中，并且在测试的`debugElement`中用这些`strong`标签输出。那么为什么价值没有显示出来呢？

当我尝试我的指令的多个实现时，我注意到任何时候一个变量被插值，结果的文本都是一个空字符串；但是当它用在只有 HTML 的元素上时，它就工作了。下面是一些例子，说明它什么时候有效，什么时候无效:

```
<!-- Didn't work -->
<p typeaheadResult>{{ myVariable }}</p>
<!-- Did work -->
<p typeaheadResult>My Result</p> 
```

Enter fullscreen mode Exit fullscreen mode

显然这和插值有关。为了测试我的理论，我在指令中设置了一个超时，在运行匹配函数之前等待 3 秒钟。当我这样做时，属性开始工作。所以，我发现属性代码是在插值完成之前运行的。因此，最后一步是让属性代码只在元素中有文本时运行，并在插入的文本准备好时启动函数。

属性通过使用`ngOnChanges`生命周期方法工作。这是修复我的插值错误之前的函数的样子:

```
ngOnChanges(changes: SimpleChanges) {
    if (changes.matchTerm && this.highlightMatches) {
        this.markStringMatches(this._element);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当`matchTerm`改变时，调用`markStringMatches`函数。但是因为插值没有准备好，所以在元素中用空字符串调用它。因此插值被替换为空字符串。所以我把那个函数改成了下面的:

```
ngOnChanges(changes: SimpleChanges) {
    if (changes.matchTerm && this.highlightMatches && this._element.nativeElement.textContent) {
        this.markStringMatches(this._element);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个微小的变化，但是我确保等待运行`markStringMatches`函数，直到元素中包含文本。此外，在插值花费了很长时间的情况下(由于这样或那样的原因)，我添加了另一个生命周期方法:`AfterViewInit`。该功能的内容与`ngOnChanges`功能的内容相同。这样，一旦插值发生(视图准备好了)，就会运行`markStringMatches`函数，指令就会生效。

## 结论

有时我们的一些角度代码需要视图准备好才能正常工作，但是会在视图准备好之前运行。通过使用所提供的生命周期钩子，我们可以让我们的角度代码在运行之前一直等到视图准备好，这样我们就可以得到想要的结果。

希望这已经帮助了你，也为你的问题提供了答案！