# ★一个用于发送消息的 Laravel 包

> 原文：<https://dev.to/freekmurze/a-laravel-package-to-flash-messages-41om>

在过去的几年里，我们一直在所有项目中使用 [laracasts/flash](https://github.com/laracasts/flash) 包来显示消息。以防你不知道:快速消息是从一个请求传递给下一个请求的消息。Laracasts 包做得很好。它支持多个快速消息，覆盖消息。它带有开箱即用的引导样式和一些预配置的消息传递级别。

我们已经注意到，在我们的项目中，我们只使用了来自 [laracasts/flash](https://github.com/laracasts/flash) 的一小部分功能。这就是为什么我们推出了自己的轻量级软件包，名为 [spatie/laravel-flash](https://github.com/spatie/laravel-flash) 。在这篇博文中，我想向大家介绍一下。

我们的 flash 包一次只能发送一条 flash 消息。因为它只需要这种能力，我们可以保持我们的 API 非常干净。这里有一个你如何使用它的例子。

```
class MyController
{
    public function store()
    {
        // …

        flash('My message', 'my-class');

        return back();
    }
} 
```

让我们来看看如何显示 flash 消息。我们的套餐中没有风景。你必须在自己的应用程序中创建一个局部视图。这个视图的内容可能是:

```
@if(flash()->message)
    <div class="{{ flash()->class }}">
        {{ flash()->message }}
    </div>
@endif 
```

很简单，对吧？

你可能已经注意到在上面的 php 例子中，我们指定了一个具体的 CSS 类名- `my-class` -作为`flash`函数的第二个参数。在一个真正的应用程序中，你可能只想指定它是一个成功的消息，警告或错误消息，而不是指定具体的类名。这个包使得定义消息级别和将它们与 CSS 类关联起来变得很容易。你可以这样做:

```
// this would probably go in a service provider

\Spatie\Flash\Flash::levels([
    'success' => 'alert-success',
    'warning' => 'alert-warning',
    'error' => 'alert-error',
]); 
```

现在你可以这样做来设置一个 flash 消息:

```
flash()->success('My message');

// alternatively you can do this
flash('My message', 'success'); 
```

在下一个请求中，`flash()->class`将返回`alert-success`。

这就是一揽子计划的全部内容。我们打算保持这个包很薄，因为我们从来不需要更多的功能来发送消息。我希望 [spatie/laravel-flash](https://github.com/spatie/laravel-flash) 也能在你的项目中派上用场。也一定要看看[我们团队之前创造的许多包](https://spatie.be/open-source/packages)。