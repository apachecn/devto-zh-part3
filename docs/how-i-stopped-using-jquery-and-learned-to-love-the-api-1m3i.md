# 我是如何停止使用 jQuery 并学会爱上这个 API 的

> 原文：<https://dev.to/funkybob/how-i-stopped-using-jquery-and-learned-to-love-the-api-1m3i>

我最近在一篇文章中读到，大约 75%的网站仍然在使用 jQuery。我不知道这个统计数据有多准确，但我仍然很惊讶有人会认为它有那么高。

现在，我不会开始咆哮每个人应该如何使用 React，Vue，$ THIS _ MONTHS _ HOT _ FRAMEWORK——我更经常是一个普通的 JavaScript 人——但我认为认识到为什么创建 jQuery 以及为什么它不再那么相关是很重要的。

jQuery 开始于过去那段疯狂的日子，那时标准很松散，竞争很有问题，兼容性也很低。此外，DOM 和其他浏览器 API 的开发程度要低得多，更不用说 JavaScript 本身了。

它为我们带来了简单的$()接口，可以轻松地选择和处理元素集合。它抽象了跨浏览器兼容性问题。它封装了常见的模式，让我们免去了重复代码的繁琐。

# 香草

所以接下来是一些常见的 jQuery 片段和模式，以及如何用普通的 JS 实现它们。如果您想继续使用 jQuery，那么这取决于您——它仍然有价值，遗留项目应该具有一致性，并且我不是来为您决定工具的。

## 选择

显然，最常见的行为是选择元素。

```
 var buttons = $("button.myclass") 
```

在 DOM 中，我们现在有了[查询选择器](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/querySelector)和[查询选择器 All](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/querySelectorAll) 。第一个用于选择第一个匹配，后者用于所有匹配。这可以在文档或任何元素上调用。

```
 let buttons = document.querySelectorAll('button.myclass') 
```

这将返回一个[节点列表](https://developer.mozilla.org/en-US/docs/Web/API/NodeList)，它是*，类似于*一个数组，但不完全是。重要的是，你可以像数组一样给它加下标，它有 [forEach](https://developer.mozilla.org/en-US/docs/Web/API/NodeList/forEach) 。

## 事件监听器

要将一个事件监听器附加到一组元素上，在 jQuery 中可以:

```
 buttons.on('click', function (ev) { alert('You clicked it!'); }) 
```

在 vanilla JS 中，使用 [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) :
会更详细一些

```
 buttons.forEach(
      el => el.addEventListener('click', ev => alert('You clicked it!'))
    ) 
```

此外，jQuery 允许您添加“委托”处理程序，这些处理程序绑定到一个元素，并从其匹配模式的后代中捕获事件。

```
 $('div.toolbar').on('click', '.hide', function (ev) { doHideToolbar(ev.target); }); 
```

对于普通的 JS 来说，这又需要多做一点工作，但是远远少于以前的需要:

```
 document.querySelectorAll('div.toolbar').forEach(el => {
      el.addEventListener('click', ev => {
        if (!ev.target.matches('.hide')) return;
        doHideToolbar(ev.target);
      })
    }) 
```

所以这里我们给每个`dev.toolbar`元素添加一个事件监听器来捕捉“点击”事件，然后使用[匹配](https://developer.mozilla.org/en-US/docs/Web/API/Element/matches)方法基于委托选择器进行过滤。

## 爬树

通常情况下，您会希望沿着 DOM 回溯到特定的节点。

```
 var form = $(ev.target).closest('form'); 
```

碰巧的是，这个*更容易*在原生 JS:

```
 let form = ev.target.closest('form'); 
```

## 操纵类

另一个常见的动作是处理元素上的类；添加或删除、切换或测试。

```
 var button = $('button[type=submit]').first();

    button.addClass('active');
    button.removeClass('active');
    button.toggleClass('active');

    if button.hasClass('active') { 
```

在现代 DOM 中，元素有一个 [classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList) 属性，这给了我们更多的能力:

```
 let button = document.querySelector('button[type=submit]');

    button.classList.add('active');
    button.classList.remove('active');
    button.classList.toggle('active');

    if button.classList.contains('active') { 
```

还有一个奖励:

```
 bonus.classList.replace('old', 'new'); 
```

作为一个 [DOMTokenList](https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList) 的`classList`，也支持许多类似数组的接口，比如`forEach()`和`length`。

## 属性和数据

在黑暗的过去，操纵 DOM 元素的属性是不兼容方法和拜占庭限制的雷区。所以，当然，jQuery 抽象了它。

然而，值得注意的是，jQuery 中的“属性”和“属性”之间有明显的区别——也就是说，DOM 元素的属性和 HTML 文档的属性之间有明显的区别。

```
 var inp = $('input[type=checkbox]');

    inp.prop('value')
    inp.prop('value', 'six'); 
```

同样，现代的 DOM 提供了[属性](https://developer.mozilla.org/en-US/docs/Web/API/Element/attributes)，作为一个[命名节点映射](https://developer.mozilla.org/en-US/docs/Web/API/NamedNodeMap):一个无序的、名字到[属性](https://developer.mozilla.org/en-US/docs/Web/API/Attr)实例的动态映射。

```
 let inp = document.querySelector('input[type=checkbox]')

    inp.attributes.value;
    // alternatively
    inp.attributes['value'];

    inp.attributes.value = 'six'; 
```

此外，jQuery 提供了用于操作元素上的`data-*`属性的`data()`方法。现代 DOM 又一次带给我们[元素.数据集](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset)，这是一个 [DOMStringMap](https://developer.mozilla.org/en-US/docs/Web/API/DOMStringMap) 。

```
 $('[data-thing]').data('thing')  // yields the value of data-thing
    $('[data-thing]').data('thing', 'wibble')  // sets the value 
```

还有香草的:

```
 let el = document.querySelector('[data-thing]')

    el.dataset.thing
    el.dataset['thing']

    el.dataset.thing = 'wibble';
    el.dataset['thing'] = 'wibble'; 
```

# 终于

我请某人来评论这篇文章，他向我展示了一个很好的资源是[http://youmightnotneedjquery.com/](http://youmightnotneedjquery.com/)，其中包括 jQuery 的例子以及如何用 IE8+/9+/10+来替代它。

## 更？

我打算一头扎进美元。ajax 和 family，但是我觉得这篇文章已经够长了，也一样长，所以我会把它留到另一篇文章中。

如果你有更多想看的，请评论，我会尽我所能帮助你。