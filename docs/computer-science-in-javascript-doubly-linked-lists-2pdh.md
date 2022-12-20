# JavaScript 中的计算机科学:双向链表

> 原文：<https://dev.to/nzakas/computer-science-in-javascript-doubly-linked-lists-2pdh>

在我的[上一篇文章](https://humanwhocodes.com/blog/2019/01/computer-science-in-javascript-linked-list/)中，我讨论了用 JavaScript 创建一个单向链表(如果你还没有读过那篇文章，我建议你现在就去读)。单个链表由节点组成，每个节点都有一个指向列表中下一个节点的指针。单链表通常需要遍历整个链表进行操作，因此性能通常很差。提高链表性能的一种方法是在每个节点上添加第二个指针，指向链表中的前一个节点。一个链表，它的节点既指向前一个节点又指向下一个节点，称为*双向链表*。

## 双向链表的设计

与单向链表类似，双向链表由一系列节点组成。每个节点都包含一些数据以及指向列表中下一个节点的指针和指向上一个节点的指针。下面是一个简单的 JavaScript 表示:

```
class DoublyLinkedListNode {
    constructor(data) {
        this.data = data;
        this.next = null;
        this.previous = null;
    }
} 
```

在`DoublyLinkedListNode`类中，`data`属性包含链表项应该存储的值，`next`属性是指向列表中下一个项的指针，`previous`属性是指向列表上一个项的指针。`next`和`previous`指针都以`null`开始，因为在类被实例化时，下一个和上一个节点都是未知的。然后，您可以使用`DoublyLinkedListNode`类创建一个双向链表，如下所示:

```
// create the first node
const head = new DoublyLinkedListNode(12);

// add a second node
const secondNode = new DoublyLinkedListNode(99);
head.next = secondNode;
secondNode.previous = head;

// add a third node
const thirdNode = new DoublyLinkedListNode(37);
secondNode.next = thirdNode;
thirdNode.previous = secondNode;

const tail = thirdNode; 
```

和单向链表一样，双向链表中的第一个节点叫做头。通过使用每个节点上的`next`和`previous`指针来分配第二个和第三个节点。下图显示了生成的数据结构。

[![Doubly linked list diagram by Lasindi - Own work, Public Domain](img/59202f5b45930adc8030c4715f70b3c6.png)](https://commons.wikimedia.org/w/index.php?curid=2245165)

通过跟随每个节点上的`next`指针，可以像遍历单向链表一样遍历双向链表，例如:

```
let current = head;

while (current !== null) {
    console.log(current.data);
    current = current.next;
} 
```

双向链表通常也跟踪链表中的最后一个节点，称为*尾*。列表的尾部有助于跟踪新节点的插入，以及从列表的后面到前面进行搜索。为此，您从尾部开始，沿着`previous`链接，直到不再有节点。下面的代码反向打印出双向链接中的每个值:

```
let current = tail;

while (current !== null) {
    console.log(current.data);
    current = current.previous;
} 
```

这种在双向链表中来回遍历的能力通过允许双向搜索而提供了优于单向链表的优势。

## [T2`DoublyLinkedList`阶级](#the-raw-doublylinkedlist-endraw-class)

与单向链表一样，操作双向链表中节点的操作最好封装在一个类中。这里有一个简单的例子:

```
const head = Symbol("head");
const tail = Symbol("tail");

class DoublyLinkedList {
    constructor() {
        this[head] = null;
        this[tail] = null;
    }
} 
```

`DoublyLinkedList`类表示一个双向链表，将包含与它所包含的数据交互的方法。有两个符号属性，`head`和`tail`，分别用于跟踪列表中的第一个和最后一个节点。和单链表一样，`head`和`tail`不能从类外部访问。

### 向列表中添加新数据

向双向链表添加条目与向单向链表添加条目非常相似。在这两种数据结构中，您必须首先找到列表中的最后一个节点，然后在它后面添加一个新节点。在单向链表中，你必须遍历整个链表来找到最后一个节点，而在双向链表中，最后一个节点是使用`this[tail]`属性来跟踪的。下面是`DoublyLinkedList`类的`add()`方法:

```
class DoublyLinkedList {

    constructor() {
        this[head] = null;
        this[tail] = null;
    }

    add(data) {

        // create the new node and place the data in it
        const newNode = new DoublyLinkedListNode(data);

        // special case: no nodes in the list yet
        if (this[head] === null) {
            this[head] = newNode;
        } else {

            // link the current tail and new tail
            this[tail].next = newNode;
            newNode.previous = this[tail];
        }

        // reassign the tail to be the new node
        this[tail] = newNode;
    }

} 
```

双向链表的`add()`方法接受一个参数，即要插入到列表中的数据。如果列表是空的(`this[head]`和`this[tail]`都是`null`，那么新节点被分配给`this[head]`。如果列表不为空，则在当前`this[tail]`节点之后添加一个新节点。最后一步是将`this[tail]`设置为`newNode`，因为在空列表和非空列表中，新节点总是最后一个节点。

注意，在空列表的情况下，`this[head]`和`this[tail]`被设置为同一个节点。这是因为单节点列表中的单个节点既是列表中的第一个也是最后一个节点。保持对列表尾部的正确跟踪是很重要的，这样在必要的时候可以反向遍历列表。

这个`add()`方法的复杂度是 O(1)。对于空列表和非空列表，该操作不需要任何遍历，因此比只跟踪列表头的单链表的`add()`要简单得多。

### 从列表中检索数据

双向链表的`get()`方法与单向链表的`get()`方法完全相同。在这两种情况下，您必须从`this[head]`开始遍历列表，并跟踪已经看到了多少个节点，以确定何时到达正确的节点:

```
class DoublyLinkedList {

    // other methods hidden for clarity

    get(index) {

        // ensure `index` is a positive value
        if (index > -1) {

            // the pointer to use for traversal
            let current = this[head];

            // used to keep track of where in the list you are
            let i = 0;

            // traverse the list until you reach either the end or the index
            while ((current !== null) && (i < index)) {
                current = current.next;
                i++;          
            }

            // return the data if `current` isn't null
            return current !== null ? current.data : undefined;
        } else {
            return undefined;
        }
    }

} 
```

从单链表帖子中重申一下，`get()`方法的复杂度从移除第一个节点(不需要遍历)时的 O(1)到移除最后一个节点(需要遍历整个列表)时的 O(n)不等。

### 从双向链表中删除数据

从双向链表中删除数据的算法本质上与单向链表相同:首先遍历数据结构，找到给定位置的节点(与`get()`相同的算法)，然后将其从链表中删除。与单链表中使用的算法的唯一显著区别是:

1.  不需要一个`previous`变量来跟踪循环中的一个节点，因为前一个节点通过`current.previous`总是可用的。
2.  您需要观察列表中最后一个节点的变化，以确保`this[tail]`保持正确。

否则，`remove()`方法看起来与单链表非常相似:

```
class DoublyLinkedList {

    // other methods hidden for clarity

    remove(index) {

        // special cases: no nodes in the list or `index` is negative
        if ((this[head] === null) || (index < 0)) {
            throw new RangeError(`Index ${index} does not exist in the list.`);
        }

        // special case: removing the first node
        if (index === 0) {

            // store the data from the current head
            const data = this[head].data;

            // just replace the head with the next node in the list
            this[head] = this[head].next;

            // special case: there was only one node, so also reset `this[tail]`
            if (this[head] === null) {
                this[tail] = null;
            } else {
                this[head].previous = null;
            }

            // return the data at the previous head of the list
            return data;
        }

        // pointer use to traverse the list
        let current = this[head];

        // used to track how deep into the list you are
        let i = 0;

        // same loop as in `get()`
        while ((current !== null) && (i < index)) {

            // traverse to the next node
            current = current.next;

            // increment the count
            i++;
        }

        // if node was found, remove it
        if (current !== null) {

            // skip over the node to remove
            current.previous.next = current.next;

            // special case: this is the last node so reset `this[tail]`.
            if (this[tail] === current) {
                this[tail] = current.previous;
            } else {
                current.next.previous = current.previous;
            }

            // return the value that was just removed from the list
            return current.data;
        }

        // if node wasn't found, throw an error
        throw new RangeError(`Index ${index} does not exist in the list.`);
    }

} 
```

当`index`为`0`时，意味着第一个节点被删除，`this[head]`被设置为`this[head].next`，与单链表相同。当您需要更新其他指针时，差异就出现在这一点之后。如果列表中只有一个节点，那么您需要将`this[tail]`设置为`null`来有效地删除这一个节点；如果有多个节点，您需要将`this[head].previous`设置为`null`。记住，新的 head 先前是列表中的第二个节点，因此它的`previous`链接指向刚刚被删除的节点。

在循环之后，你需要确保被移除节点之前的节点的`next`指针和被移除节点之后的节点的`previous`指针。当然，如果要删除的节点是最后一个节点，那么您需要更新`this[tail]`指针。

### 创建反向迭代器

您可以在 JavaScript 中使用与单链表相同的`values()`和`Symbol.iterator`方法来创建一个双向链表。然而，在双向链表中，您有机会创建一个反向迭代器，它从尾部开始产生数据，并向头部移动。下面是一个`reverse()`生成器方法的样子:

```
class DoublyLinkedList {

    // other methods hidden for clarity

    *reverse(){

        // start by looking at the tail
        let current = this[tail];

        // follow the previous links to the head
        while (current !== null) {
            yield current.data;
            current = current.previous;
        }
    }
} 
```

`reverse()`生成器方法遵循与单链表中的`values()`生成器方法相同的算法，除了`current`从等于`this[tail]`开始，并遵循`current.previous`直到不再有节点。创建一个反向迭代器有助于发现实现中的错误，也有助于避免仅仅为了以不同的顺序访问数据而重新安排节点。

### 其他方法

大多数其他不涉及添加或删除节点的方法都遵循与单链表相同的算法。

### 使用类

完成后，您可以像这样使用链表实现:

```
const list = new DoublyLinkedList();
list.add("red");
list.add("orange");
list.add("yellow");

// get the second item in the list
console.log(list.get(1));       // "orange"

// print out all items in reverse
for (const color of list.reverse()) {
    console.log(color);
}

// remove the second item in the list 
console.log(list.remove(1));    // "orange"

// get the new first item in the list
console.log(list.get(1));       // "yellow"

// convert to an array
const array1 = [...list.values()];
const array2 = [...list];
const array3 = [...list.reverse()]; 
```

完整的源代码可以在 GitHub 上我的 JavaScript 项目的[计算机科学中获得。](https://github.com/humanwhocodes/computer-science-in-javascript)

## 结论

双向链表类似于单向链表，因为每个节点都有一个指向列表中下一个节点的`next`指针。每个节点还有一个指向列表中前一个节点的`previous`指针，允许您在列表中轻松地前后移动。双向链表通常跟踪列表中的第一个和最后一个节点，这使得向列表中添加节点的操作为 O(1)而不是单向链表中的 O(n)。

然而，其他双向链表操作的复杂性和单向链表是一样的，因为你总是要遍历链表的大部分。因此，与内置的 JavaScript `Array`类相比，双向链表在存储一组不相关的数据方面并没有提供任何真正的优势(尽管相关的数据，比如浏览器中的兄弟 DOM 节点，在某种类型的链表中表示可能是有用的)。

*本文原载于[人类编码博客](https://humanwhocodes.com)2019 年[2 月 5 日](https://humanwhocodes.com/blog/2019/02/computer-science-in-javascript-doubly-linked-lists/)。*