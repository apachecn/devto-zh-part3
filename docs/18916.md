# 在 HTMLElement 原型中重新实现 jQuery 方法

> 原文：<https://dev.to/jochemstoel/re-implementing-jquery-methods-in-the-htmlelement-prototype-15b9>

现在已经是 2019 年了，人们普遍认为 jQuery 已经过时了。我个人对此有非常不同的看法，但那是以后的事了。那么为什么还有人在用呢？旧习惯？方便吗？事实证明，大多数使用 jQuery 的人只将它用于少数几种方法。

我认为对于 n00b 程序员来说，在 HTMLElement 原型上重新实现这些 jQuery 方法会很有趣，也很有教育意义。

## 查询选择器

让我们首先为文档查询选择器定义一个快捷方式。 *$* 用于单个元素， *$$* 用于所有匹配。我们希望能够提供第二个*上下文*参数，像 jQuery 一样默认为整个文档。我假设 ES6+支持函数声明中的默认值。

```
/**
 * $ for document.querySelector
 * $$ for document.querySelectorall
 * with optional context just like jQuery (defaults to document)
 */
window.$ = (query, ctx = document) => ctx.querySelector(query)
window.$$ = (query, ctx = document) => ctx.querySelectorAll(query) 
```

```
$('h2') // will return single _&lt;h2&gt;_ element
$$('h2') // will return array with all _&lt;h2&gt;_ elements 
```

使用上下文参数，我们可以选择另一个元素(

<article></article>

)
中的所有

元素

```
$$('p', $('article')) 
```

## NodeList 迭代

你不得不承认， *jQuery.prototype.each* 也非常简洁。将属性 *each* 添加到节点列表原型，并将值设置为一个函数，该函数将*节点列表*转换为*数组*，然后使用提供的回调函数对其进行迭代。

```
/**
 * This allows you to "forEach" a NodeList returned by querySelectorAll or $$
 * similar to jQuery.prototype.each
 * use: $$('li').each(callback)
 */
Object.defineProperty(NodeList.prototype, 'each', {
    value: function (fn) {
        return Array.from(this).forEach((node, index) => fn(node, index))
    }
}) 
```

## 属性

另一个常见的 jQuery 方法是 *attr* 。我们可以使用 *attr* 通过一个方法读写 DOM 中元素的属性。我们将在我们的方法中添加一个微小的特性。当没有提供参数时，它返回所有属性。
带*属性*还带 *removeAttr* (移除属性)*带*来确定一个参数是否存在。

```
/** 
 * single method to get/set/list attributes of HTMLElement. 
 * get argument id:     $('div').attr('id')
 * set argument id:     $('div').attr('id', 'post123')
 * list all arguments:  $('div').attr()  // Fuck yeah
 */
HTMLElement.prototype.attr = function (key, value) {
    if (!value) {
        if (!key) {
            return this.attributes
        }
        return this.getAttribute(key)
    }
    this.setAttribute(key, value)
    return this
}

/**
 * remove attribute from HTMLElement by key
 */
HTMLElement.prototype.removeAttr = function (key) {
    this.removeAttribute(key)
    return this
}

/**
 * check whether a DOM node has a certain attribute.
 */
HTMLElement.prototype.has = function(attribute) {
    return this.hasAttribute(attribute)
} 
```

## innerText 和 innerHTML

```
/** 
 * single function to get and set innerHTML
 * get:  $('body').html()
 * set:  $('body').html('<h1>hi!</h1>')
 */
HTMLElement.prototype.html = function (string) {
    if (!string)
        return this.innerHTML
    this.innerHTML = string
    return this
}

/** 
 * single function to get and set innerText
 * get:  $('body').text()
 * set:  $('body').text('hi!')
 */
HTMLElement.prototype.text = function (string) {
    if (!string)
        return this.textContent
    this.innerText = string
    return this
} 
```

## 追加和前置

下面的 *append* 方法允许您在指定的目标元素的末尾插入一个 HTML 元素。 *prepend* 方法将在之前插入它。

```
/**
 * append HTMLElement to another HTMLElement
 * like jQuery append()
 */
HTMLElement.prototype.append = function (child) {
    if (child instanceof HTMLElement) {
        this.appendChild(child)
        return this
    }
    this.append(child)
    return this
}

/**
 * prepend HTMLElement to another HTMLElement
 * like jQuery prepend()
 */
HTMLElement.prototype.prepend = function (sibling) {
    if (sibling instanceof HTMLElement) {
        this.parentNode.insertBefore(sibling, this)
        return this
    }
    this.parentNode.insertBefore(sibling, this)
    return this
} 
```

## 删除元素

在 JavaScript 中移除元素是通过访问其父节点来调用 removeChild()实现的。我知道这很奇怪。

```
HTMLElement.prototype.remove = function() {
    this.parentNode.removeChild(this)
} 
```

您可能知道，您不能在 jQuery 中使用箭头函数。然而

```
$('#foo').remove()
// or
$$('div').each(element => element.remove()) 
```

## 父级

获取节点的父节点。

```
/** 
 * get a HTMLElement's parent node
 * use: $('h1').parent()
 */
HTMLElement.prototype.parent = function () {
    return this.parentNode
} 
```

## 事件

现代 JavaScript 库实现了 *on* 、 *off* 和 *emit* 来获取、设置和调度事件。

```
/**
 * add event listener to HTMLElement
 * $(document).on('click', event => ...)
 */
HTMLElement.prototype.on = function (event, callback, options) {
    this.addEventListener(event, callback, options)
    return this
}

/**
 * remove event listener from HTMLElement
 * $(document).off('click', callback)
 */
HTMLElement.prototype.off = function (event, callback, options) {
    this.removeEventListener(event, callback, options)
    return this
}

/**
 * dispatch an event on HTMLElement without needing to instanciate an Event object.
 * $(document).emit('change', { foo: 'bar' })
 */
HTMLElement.prototype.emit = function (event, args = null) {
    this.dispatchEvent(event, new CustomEvent(event, {detail: args}))
    return this
} 
```

## 数据集

最后但同样重要的是，这是一种访问数据属性的好方法。

```
/**
 * single method to get/set/list HTMLElement dataset values
 * get:  $('div').data('color')     assuming <div data-color="..."></div>
 * set:  $('div').data('color', '#0099ff')
 */
HTMLElement.prototype.data = function (key, value) {
    if (!value) {
        if (!key) {
            return this.dataset
        }
        return this.dataset[key]
    }
    this.dataset[key] = value
    return this
} 
```

## 定义

这与 jQuery 无关，但仍然是一个不错的快捷方式。

```
/**
 * Convenient shortcut 
 * use:   define('property', { ...descriptor })
 */
Object.defineProperty(window, 'define', {
    value: (property, ...meta) => meta.length == 2 ? Object.defineProperty(meta[0], property, meta[1]) : Object.defineProperty(window, property, meta[0]),
    writable: false,
    enumerable: true
}) 
```

现在我们可以这样做:

```
/** 
 * now | single statement accessor that returns current time
 * @returns {number} 
 */
define('now', {
    get: Date.now
}) 
```

标识符 *now* 将返回当前时间。你不必把它作为一个函数来调用，简单地访问它就可以了。

```
setInterval(() => console.log(now), 10)
/*
1543930325785
1543930325795
1543930325805
1543930325815
1543930325825
1543930325835
*/ 
```

## 要旨

为了您的方便，以上是所有的要点。
[https://gist . github . com/jochemstoel/856 D5 b 2735 c 53559372 EB 7 b 32 c 44 e 9 a 6](https://gist.github.com/jochemstoel/856d5b2735c53559372eb7b32c44e9a6)