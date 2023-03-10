# JavaScript 中的计算机科学:循环双向链表

> 原文：<https://dev.to/nzakas/computer-science-in-javascript-circular-doubly-linked-lists-4n75>

在我的[上一篇文章](https://humanwhocodes.com/blog/2019/02/computer-science-in-javascript-doubly-linked-lists/)中，我讨论了将单向链表转变为双向链表需要做哪些改变。我建议在这篇文章之前先看看那篇文章(如果你还没看过的话)。这篇文章是关于修改一个双向链表(也叫做*线性双向链表*)的，通过这种方式，列表中的最后一个节点指向列表中的第一个节点，有效地使列表循环。*循环双向链表*很有趣，因为它允许你连续地遍历列表项，而不需要检查列表的结尾。在创建播放列表或将流量循环分配给服务器时，您可能会遇到这种情况。

注意:也可以创建一个循环的单链表。我不会在这个博客系列中讨论循环单链表，但是，你可以在我的 GitHub repo 中找到循环单链表的源代码，JavaScript 中的[计算机科学](https://github.com/humanwhocodes/computer-science-in-javascript)。

## 循环双向链表的设计

循环双向链表中的节点与线性双向链表中的节点没有什么不同。每个节点都包含指向列表中下一个和上一个项目的数据和指针。这在 JavaScript 中是这样的:

```
class CircularDoublyLinkedListNode {
    constructor(data) {
        this.data = data;
        this.next = null;
        this.previous = null;
    }
} 
```

然后，您可以使用`CircularDoublyLinkedListNode`类创建一个循环双向链表，如下所示:

```
// create the first node
const head = new CircularDoublyLinkedListNode(12);

// add a second node
const secondNode = new CircularDoublyLinkedListNode(99);
head.next = secondNode;
secondNode.previous = head;

// add a third node
const thirdNode = new CircularDoublyLinkedListNode(37);
secondNode.next = thirdNode;
thirdNode.previous = secondNode;

// point the last node to the first
thirdNode.next = head;
head.previous = thirdNode; 
```

列表头和列表中的后续节点的创建方式与线性双向链表相同。唯一的区别是最后一步，最后一个节点的`next`指针被设置为`head`，而`head`节点的`previous`指针被设置为最后一个节点。下图显示了生成的数据结构。

[![Circular doubly linked list diagram](img/91ad89d406fc8f1d863ebbe23f71297f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HiBGrpCe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://humanwhocodes.cimg/posts/2019/circular-doubly-linked-list.png)

遍历循环双向链表和线性双向链表有点不同，因为单独跟随`next`指针会导致无限循环。例如，这是一个无限循环:

```
let current = head;

// infinite loop: `current` is never `null`
while (current !== null) {
    console.log(current.data);
    current = current.next;
} 
```

在某些情况下，您可能希望永远在循环中迭代，但这通常不会发生在像这段代码这样的循环上下文中。在其他情况下，您可能希望迭代循环中的每个节点，直到找到最后一个节点。要做到这一点，你需要检查一下`current`何时是`head`，这意味着你又回到了循环的起点。然而，简单地将前面例子中的`null`替换为`head`会导致循环根本不执行:

```
let current = head;

// loop is skipped: `current` is already `head`
while (current !== head) {
    console.log(current.data);
    current = current.next;
} 
```

这里的问题是`current`开始时等于`head`，只有当`current`不等于`head`时，循环才继续。解决方案是使用测试后循环而不是测试前循环，在 JavaScript 中，这意味着使用一个`do-while`循环:

```
let current = head;

if (current !== null) {

    do {
        console.log(current.data);
        current = current.next;
    } while (current !== head);

} 
```

在这段代码中，检查`current`是否等于`head`出现在循环的末尾，而不是开始。为了确保循环不会开始，除非`current`不是`null`，一个`if`语句通常必须在`do-while`循环之前(你不再需要`while`循环的预测试来覆盖这种情况)。循环将继续进行，直到`current`再次成为`head`，这意味着整个列表已经被遍历。

同样类似于线性双向链表，您可以从最后一个节点开始以相反的顺序遍历节点。循环双向链表不会单独跟踪列表的尾部，因为你总是可以通过`head.previous`来访问尾部，例如:

```
let current = head.previous;

if (current !== null) {

    do {
        console.log(current.data);
        current = current.previous;
    } while (current !== head.previous);

} 
```

## [T2`CircularDoublyLinkedList`阶级](#the-raw-circulardoublylinkedlist-endraw-class)

除了没有跟踪列表中最后一个节点的`tail`属性之外，`CircularDoublyLinkedList`类看起来与上一篇文章中的`DoublyLinkedList`类非常相似:

```
const head = Symbol("head");

class CircularDoublyLinkedList {
    constructor() {
        this[head] = null;
    }
} 
```

线性和循环双向链表的主要区别在于添加、删除和遍历节点的方法。

### 向列表中添加新数据

用于添加数据的相同的基本算法被用于线性和循环双向链表，不同之处在于指针必须被更新以完成该过程。下面是`CircularDoublyLinkedList`类的`add()`方法:

```
class CircularDoublyLinkedList {

    constructor() {
        this[head] = null;
    }

    add(data) {

        const newNode = new CircularDoublyLinkedListNode(data);

        // special case: no items in the list yet
        if (this[head] === null) {
            this[head] = newNode;
            newNode.next = newNode;
            newNode.previous = newNode;
        } else {

            const tail = this[head].previous;

            tail.next = newNode;
            newNode.previous = tail;
            newNode.next = this[head];
            this[head].previous = newNode;
        }
    }

} 
```

循环双向链表的`add()`方法接受一个参数，即要插入到链表中的数据。如果列表是空的(`this[head]`是`null`，那么新节点被分配给`this[head]`。使列表循环的额外步骤是确保`newNode.next`和`newNode.previous`都指向`newNode`。

如果列表不为空，则在当前尾部之后添加一个新节点，使用`this[head].previous`检索该节点。然后可以将新节点添加到`tail.next`中。请记住，您实际上是在列表的尾部和头部之间插入了一个新的节点，所以这个操作看起来更像是插入而不是追加。一旦完成，`newNode`就是列表的尾部，因此`newNode.next`必须指向`this[head]`，而`this[head].previous`必须指向`newNode`。

与线性双向链表一样，这个`add()`方法的复杂度是 O(1 ),因为不需要遍历。

### 从列表中检索数据

循环双向链表的`get()`方法遵循本文开头的基本算法。您必须遍历列表，同时跟踪您已经进入列表的深度，并确保您不会返回到列表的前面。下面是如何实现`get()`方法的。

```
class CircularDoublyLinkedList {

    // other methods hidden for clarity

    get(index) {

        // ensure `index` is a positive value and the list isn't empty
        if ((index > -1) && (this[head] !== null)) {

            let current = this[head];
            let i = 0;

            do {

                if (i === index) {
                    return current.data;
                }

                current = current.next;
                i++;

            } while ((current !== this[head]) && (i <= index));

        }

        return undefined;
    }

} 
```

`get()`方法首先检查以确保`index`是一个正值，并且列表不为空。如果任一情况为真，则该方法返回`undefined`。请记住，由于使用了后测试而不是前测试循环，在开始遍历之前，您必须始终使用`if`语句来检查循环双向链表是否为空。

使用与前面讨论的相同的遍历算法，`get()`方法使用`i`变量来跟踪它已经遍历了多深的列表。当`i`等于`index`时，返回该节点的数据(提前退出循环)。如果循环退出，或者因为它再次到达列表的头部，或者在列表中没有找到`index`，则返回`undefined`。

和线性双向链表一样，`get()`方法的复杂度从 O(1)到 O(n)；

### 从列表中删除数据

从循环双向链表中移除数据基本上与线性双向链表相同。不同之处在于:

1.  使用测试后循环代替测试前循环进行遍历(与`get()`相同)
2.  确保移除任何一个节点时，循环链接仍保留在头节点和尾节点上

下面是一个`remove()`方法的实现:

```
class CircularDoublyLinkedList {

    // other methods hidden for clarity

    remove(index) {

        // special cases: no nodes in the list or `index` is an invalid value
        if ((this[head] === null) || (index < 0)) {
            throw new RangeError(`Index ${index} does not exist in the list.`);
        }

        // save the current head for easier access
        let current = this[head];

        // special case: removing the first node
        if (index === 0) {

            // if there's only one node, null out `this[head]`
            if (current.next === this[head]) {
                this[head] = null;
            } else {

                // get the last item in the list
                const tail = this[head].previous;

                /*
                 * Set the tail to point to the second item in the list.
                 * Then make sure that item also points back to the tail.
                 */
                tail.next = current.next;
                current.next.previous = tail;

                // now it's safe to update the head
                this[head] = tail.next;
            }

            // return the data at the previous head of the list
            return current.data;
        }

        let i = 0;

        do {

            // traverse to the next node
            current = current.next;

            // increment the count
            i++;

        } while ((current !== this[head]) && (i < index));

        // the node to remove has been found
        if (current !== this[head]) {

            // skip over the node to remove
            current.previous.next = current.next;
            current.next.previous = current.previous;

            // return the value that was just removed from the list
            return current.data;
        }

        // `index` doesn't exist in the list so throw an error
        throw new RangeError(`Index ${index} does not exist in the list.`);

    }

} 
```

虽然在这个`remove()`方法中有特殊的情况，但是由于列表的循环性质，几乎每种情况都需要调整两个节点上的指针。唯一不需要这样做的情况是删除列表中唯一的节点。

删除列表中的第一个节点(`index`是`0`)被视为一种特殊情况，因为不需要遍历，而且必须给`this[head]`赋一个新值。列表中的第二个节点成为头节点，必须相应地调整它的`previous`指针。

该方法的其余部分遵循与线性双向链表相同的算法。因为我们不需要担心特殊的`this[head]`指针，所以在`index`节点的搜索和移除可以像列表是线性的一样进行。

<aside class="tip">You can further simply removal of nodes if you don't mind losing track of the original head of the list. The implementation of `CircularDoublyLinkedList` in this post assumes you want the original head of the list to remain as such unless it is removed. However, because the list is circular, it really doesn't matter what nodes is considered the head because you can always get to every other node as long as you reference to one node. You can arbitrarily reset `this[head]` to any node you want an all of the functionality will continue to work.</aside>

### 创建迭代器

循环链表中迭代器有两种不同的使用情况:

1.  用于 JavaScript 的内置迭代功能(如`for-of`循环)
2.  对于特定的应用程序(如播放列表)，以循环方式遍历列表中的值

为了解决第一种情况，在类上创建一个`values()`生成器方法和一个`Symbol.iterator`方法是有意义的，因为这些方法是 JavaScript 集合所期望的。这些方法类似于双向链表中的方法，除了循环必须翻转，并且你需要检查你是否到达了链表头来退出循环。这两种方法如下所示:

```
class CircularLinkedList {

    // other methods hidden for clarity

    values() {

        // special case: list is empty
        if (this[head] !== null) {

            // special case: only one node
            if (this[head].next === this[head]) {
                yield this[head].data;
            } else {

                let current = this[head];

                do {
                    yield current.data;
                    current = current.next;
                } while (current !== this[head]);
            }

        }
    }

    [Symbol.iterator]() {
        return this.values();
    }
} 
```

`values()`生成器方法有两种特殊情况:当列表为空时，这种情况下它不产生任何东西；当只有一个节点时，这种情况下不需要遍历，存储在头部的数据被产生。除此之外，`do-while`循环和你在这篇文章中看到的是一样的。

创建一个循环的迭代器只需要修改这个算法，这样循环就永远不会退出。看起来是这样的:

```
class CircularDoublyLinkedList {

    // other methods hidden for clarity

    *circularValues() {

        // special case: list is empty
        if (this[head] !== null) {

            let current = this[head];

            // infinite loop
            do {
                yield current.data;
                current = current.next;
            } while (true);
        }

    }

} 
```

在 JavaScript 将耗尽迭代器的任何情况下(如在`for-of`循环中),您都不希望使用`circularValues()`生成器方法，因为这将导致无限循环和崩溃。相反，每当需要另一个值时，手动调用迭代器的`next()`方法。

<aside class="tip">For this method, it really doesn't matter if you use a `do-while` loop or a `while` loop. I used `do-while` to keep it consistent with the rest of this post, but you can use any flavor of infinite loop that you want.</aside>

### 使用类

完成后，您可以像这样使用循环双向链表实现:

```
const list = new CircularDoublyLinkedList();
list.add("red");
list.add("orange");
list.add("yellow");

// get the second item in the list
console.log(list.get(1));       // "orange"

// print out all items
for (const color of list.values()) {
    console.log(color);
}

// remove the second item in the list 
console.log(list.remove(1));    // "orange"

// get the new first item in the list
console.log(list.get(1));       // "yellow"

// convert to an array
const array1 = [...list.values()];
const array2 = [...list];

// manually cycle through each item in a circular manner
const iterator = list.circularValues();

let { value } = iterator.next();
doSomething(value);    

({ value } = iterator.next());
doSomething(value); 
```

完整的源代码可以在 GitHub 上我的 JavaScript 项目的[计算机科学中获得。](https://github.com/humanwhocodes/computer-science-in-javascript)

## 结论

循环双向链表的建立方式类似于线性双向链表，因为每个 ndoe 都有一个指针指向链表中的下一个和前一个节点。不同之处在于列表尾总是指向列表头，所以你可以跟随`next`指针而不会收到`null`。这种功能可以用于诸如播放列表或数据处理的循环分发之类的应用。

双向链表操作的实现不同于线性双向链表，因为你必须使用一个后测试循环(`do-while`)来检查你是否回到了链表的开头。对于大多数操作，当再次到达列表头时停止是很重要的。唯一的例外是创建一个手动调用的迭代器，并且您希望它永远不会用尽要返回的项。

循环双向链表操作的复杂性与线性双向链表操作相同。与本系列博文中讨论的其他数据结构不同，循环双向链表在需要重复循环相同数据的 JavaScript 应用程序中非常有用。这是 JavaScript 内置集合类型没有很好覆盖的一个用例。

*本文原载于[人类编码博客](https://humanwhocodes.com)2019 年[3 月 5 日](https://humanwhocodes.com/blog/2019/03/computer-science-in-javascript-circular-doubly-linked-lists/)。*