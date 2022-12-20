# JavaScript 中的链表

> 原文：<https://dev.to/hardy613/a-linked-list-in-javascript--50g>

# 简介

你好，戴夫·托！

在 dev.to 上有很多关于链表的好文章，这篇文章将讲述用 JavaScript 实现一个链表的基本特性:`length`、`unshift`、`shift`、`remove`按值、`find`也按值、`get`按索引。然后我们再添加几个其他的特性:`map`、`reduce`和`filter`。

本帖将使用 [ECMAScript6](https://dev.to/hardy613/notes-on-ecmascript-6-es6-267l) 。

# 什么是链表

链表是数据的集合，其中每个节点指向下一个节点，而不是使用它们在内存中的位置。链表最基本的形式是一个`Singly Linked List`，其中节点只包含一个`value`和`next`属性。我们将实现一个`singly linked list`，然而其他[类型的链表确实存在](https://en.wikipedia.org/wiki/Linked_list#Basic_concepts_and_nomenclature)。

# 入门

我们可以从一个类和我们的主方法`unshift`开始。

```
class LinkedList {
    constructor(value) {
        this.head = null
        this.length = 0
        return this.unshift(value)
    }

    unshift(value) {
        const node = { value }
        node.next = this.head
        this.head = node
        this.length += 1
    }
}

const list = new LinkedList('test')
console.log(list) // LinkedList {head: "test", length: 1, next: null} 
```

Enter fullscreen mode Exit fullscreen mode

我们的构造函数初始化两个属性`head`和`length`。

*   `head`指向列表中的第一个节点
*   `length`将记录添加了多少物品
*   `unshift`创建一个新节点
    *   将`next`设置为前一个头节点
    *   将标题设置为新节点
    *   增加了`length`

现在我们可以初始化一个空列表和一个只有一个值的列表。让我们做一个小小的改变，允许`constructor`和`unshift`有多个值。为了有效地做到这一点，我们可以创建一个添加一个值的方法，并在我们的`unshift`方法中使用，该方法将接受多个值。

```
class LinkedList {
    constructor(...values) {
        this.head = null
        this.length = 0
        return this.unshift(...values)
    }

    _unshiftOneValue(value) {
        const node = { value }
        node.next = this.head
        this.head = node
        this.length += 1
    }

    unshift(...values) {
        values.forEach(value => this._unshiftOneValue(value))
        return this
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`constructor`和`unshift`接受带有[其余参数](https://github.com/hardy613/es6-notes/blob/master/rest-parameters.md)的多个值。

*   `constructor`将把这些值传递给`unshift`
*   `unshift`用`Array.forEach`对值进行迭代——用我们的新方法`_unshiftOneValue`将它们相加
*   `_unshiftOneValue`增加一个值，并增加`length`

返回`this`也允许我们链接方法。

添加一个`shift`从确保我们有一个项目要移除开始，否则添加`null`。

```
class LinkedList {

    // ...

    shift() {
        if (this.length === 0) {
            return null
        }
        const { value } = this.head
        this.head = this.head.next
        this.length -= 1
        return value
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   返回没有`length`的`null`
*   如果我们有`head`
    *   抓取`head`的值
    *   将当前机头设置为先前的`head`秒`next`
    *   减小`length`
*   将拆下的`value`返回。

# `find`按值

到目前为止，我们已经返回了一个值或 null，让我们保持这种设计模式。我们的`find`将:

*   接受一个`value`。
*   返回`null`或找到的`value`

```
class LinkedList {

    // ...

    find(value) {
        let node = this.head
        while (node) {
            if (node.value === value) {
                return node
            }
            node = node.next
        }
        return node
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将 node 设置为在构造函数中设置为`null`的`head`。

*   当我们有一个节点时
*   如果`node.value`严格匹配`value`参数
    *   返回节点
*   将`node.next`设置为下一个节点
*   返回节点

`node.next`不在的话就是`null`。如果我们有节点并且没有找到`value`参数，我们仍然返回`null`。

# `remove`按值

链表就像一条链，要移除一个`value`，我们需要前一个节点和当前节点`next`。如果我们发现节点是`head`，那么我们可以重用我们的`shift`方法。我们不需要返回移除的值，因为它是从作者与我们的列表的集成中得知的。让我们返回新的列表(如果没有删除任何内容，则返回同一个列表)。

```
class LinkedList {

    // ...

    remove(value) {
        if (this.length === 0) {
            return this
        }

        if (this.head.value === value) {
            this.shift()
            return this
        }
        let prevNode = this.head
        let node = prevNode.next
        while (node) {
            if (node.value === value) {
                break
            }
            prevNode = node
            node = node.next
        }

        if (node === null) {
            return this
        }
        prevNode.next = node.next
        this.length -= 1
        return this
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   如果我们没有列表，返回`this`。
*   如果`value`是`head`
    *   使用`shift`
    *   返回`this`
*   前一个节点成为`head`
*   要比较的节点被设置为`head` s `next`
*   当我们有一个节点时
    *   如果节点`value`严格匹配`value`
        *   `break`退出循环
    *   将上一个节点设置为节点
    *   将节点设置为`node.next`
*   如果我们的节点是`null`，那么返回`this`
*   将先前的节点`next`设置为找到的节点`next` -移除找到的节点
*   减小`length`
*   返回`this`

# `get`被索引

我们有足够的关于链表的信息，不需要给每个节点添加一个`index`属性。一个`Singly linked list`总是在`head` ( `index` 0)开始搜索，然后继续移动到`next`节点。单个参数是必需的，并且必须是等于或大于`0`但小于我们的`length`属性的`Number`。

```
class LinkedList {

    // ...
    get(index = 0) {
        if (this.length === 0 || Number.isNaN(index)
            || index < 0 || this.length <= index) {
            return null
        }

        if (index === 0) {
            return this.head
        }
        let node = this.head.next
        let i = 1
        while (node) {
            if (i === index) {
                return node
            }
            node = node.next
            i += 1
        }
        return null
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   返回`null`如果
    *   我们没有`length`
    *   `index`不是数字
    *   `index`小于 0(越界)
    *   `index`大于或等于我们的`length`(出界)
*   如果索引为 0，则返回`head`
*   将节点设置为`head` s next
*   将`i`设置为 1(我们的节点位置)
*   当我们有一个节点时
    *   如果`i`严格等于`index`则返回节点
    *   将我们的下一个节点设置为`node.next`
    *   将`i`增加 1
*   返回`null`

# `reduce`

我们将在数组中遵循相同的实现。对列表中的每个值执行 reducer 函数，得到一个输出值。减速器功能有四个参数:

*   累加器-累加回调的返回值
*   当前值-正在处理的值
*   当前索引-从 0 开始，带有一个`initialValue`，否则为 1。
*   Source -被缩减的列表

减速器功能也将接受一个起始`initialValue`作为第二个参数。

```
class LinkedList {

    // ...
    reduce(func = () => {}, initialValue) {
        if (this.length === 0 || typeof func !== 'function') {
            return typeof initialValue !== 'undefined' ? initialValue : null
        }
        let node = this.head
        let acc = initialValue
        let i = 0
        while (node) {
            if (typeof acc === 'undefined') {
                acc = node.value
                node = node.next
                i += 1
            }
            acc = func(acc, node.value, i, this.head)
            node = node.next
            i += 1
        }
        return acc
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

*   返回`initialValue`(如果已定义)或`null`
    *   如果`length`为 0
    *   如果缩减器函数不是函数
*   将节点设置为`head`
*   将累加器设置为`initialValue`
*   将`i`设置为 0
*   当我们有一个节点时
    *   如果累加器为`undefined`
        *   将累加器设置为值
        *   将当前节点设置为`node.next`
        *   将`i`增加 1
    *   将蓄能器设置为减速器的结果
    *   将节点设置为`node.next`
    *   将`i`增加 1
*   返回蓄能器

# `map`

有两种方法，一种是递归的，一种是命令式的。我们将双管齐下。
正如我们对`reduce`所做的一样，让我们也遵循阵列实施。`map`将创建一个新的列表，包含调用列表中每个元素的函数的结果。提供的函数有三个参数

*   CurrentValue -数组中正在处理的当前元素
*   Index -数组中正在处理的当前元素的索引
*   数组-数组映射被调用

```
class LinkedList {

    // ...
    mapRecursive(func = () => {}) {
        if (this.length === 0 || typeof func !== 'function') {
            return new LinkedList()
        }
        let i = -1
        const _map = (node, list) => {
            if (node.next) {
                _map(node.next, list)
            }
            i += 1
            return list.unshift(func(node.value, i, this.head))
        }
        return _map(this.head, new LinkedList())
    }

    map(func = () => {}) {
        if (this.length === 0 || typeof func !== 'function') {
            return new LinkedList()
        }
        const list = new LinkedList()
        let node = this.head
        let i = 0
        while (node) {
            list.unshift(func(node.value, i, this.head))
            i += 1
            node = node.next
        }
        return list
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

# `filter`

`filter`将和`map`一样，我们将在遵循`filter`的数组实现的同时进行递归和命令。`filter`将创建一个新的列表，其中包含通过由所提供的函数实现的测试的所有元素。提供的函数有三个参数:

*   元素-数组中正在处理的当前元素
*   Index -数组中正在处理的当前元素的索引
*   Array -调用了数组筛选器。

```
class LinkedList {

    // ...
    filterRecursive(func = () => {}) {
        if (this.length === 0 || typeof func !== 'function') {
            return new LinkedList()
        }
        let i = -1
        const _filter = (node, list) => {
            if (node.next) {
                _filter(node.next, list)
            }
            i += 1
            if (func(node.value, i, this.head)) {
                return list.unshift(node.value)
            }
            return list
        }
        return _filter(this.head, new LinkedList())
    }

    filter(func = () => {}) {
        if (this.length === 0 || typeof func !== 'function') {
            return new LinkedList()
        }
        const list = new LinkedList()
        let node = this.head
        let i = 0
        while (node) {
            if (func(node.value, i, this.head)) {
                list.unshift(node.value)
            }
            i += 1
            node = node.next
        }
        return list
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   返回新列表
    *   如果没有长度
    *   如果参数不是函数
*   创建新列表
*   将节点设置为`head`
*   将`i`设置为 0
*   当我们有一个节点时
    *   如果节点`value`通过测试
        *   将节点添加到新列表中
    *   增量`i`
    *   将节点设置为`node.next`
*   归还名单

# 结论

我们现在有了一个链表，并增加了许多功能！

如果你愿意，你也可以[为列表](https://github.com/hardy613/js-data-structures/blob/master/test/test.linked-list.js)编写测试。

一如既往，感谢阅读。