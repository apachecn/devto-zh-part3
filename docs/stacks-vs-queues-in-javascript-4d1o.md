# JavaScript 中的堆栈与队列

> 原文：<https://dev.to/emmabostian/stacks-vs-queues-in-javascript-4d1o>

[![comparision](img/588da6c9f57bb3b0cd0092bdbac33c17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BgQwtlaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AxSRTv4g2tofWQktkUwoRog.png)

队列和堆栈是技术访谈中常用的两种数据结构。由于它们在结构上非常相似，所以很难区分。所以今天我们将用 JavaScript 构建一个堆栈和一个队列。

# 书库

栈是遵循“后进先出”或“LIFO”范式的数据结构。我们可以把它们想象成一堆书。为了检索书库中的第三本书，我们必须先取下第五本书，然后取下第四本书，直到检索到第三本书。

JavaScript 没有提供原生的栈数据结构，所以我们必须用一个数组和一个闭包或者一个类来构建自己的栈数据结构。

[![Stack](img/3921cc3d8dd7e1b53ef77298f280aa28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nsKF_YXk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AO91_4SoZ3V2uouYqeEYQlQ.png)

[![Stack](img/c43cb0815613f80323bd89c9074450df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ra8LRLUO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AgoQO0s2M21d-A24jHJrdiA.png)

### 好处

堆栈允许不断地添加和删除项目。这是因为我们不需要移动条目来添加和删除它们。

### 约束

不幸的是，栈不像数组那样提供对栈中第 n 项的固定时间访问。这意味着检索一个项目可能需要 O(n)的时间，其中 n 是堆栈中元素的数量。

### 方法

堆栈利用以下方法:

*   `pop()`:从堆栈中移除最上面的项目
*   `push(item)`:将一个项目添加到堆栈的顶部
*   `peek()`:返回堆栈顶部的项目
*   `isEmpty()`:如果堆栈为空，返回真

### 让我们建造

让我们做一个书架，里面放一摞我们最喜欢的小说。栈的伟大之处在于 push 和 pop 方法与我们将使用的相应数组方法同名。

**构造器**

我们将定义一个类 BookStack，并给它一个具有一个属性的构造函数方法:

*   `this.stack = [];`

```
constructor() {
  this.stack = [];
} 
```

**获取**

我将添加一个 getter 来返回堆栈的长度。我们将在其他方法中使用它。

```
get length() {
  return this.stack.length;
} 
```

**按下**

我们希望将该项添加到数组的末尾，所以我们可以使用`array.push()`方法。`array.push()`方法返回新的长度数组。

```
push(item) {
  return this.stack.push(item);
} 
```

**砰然一声**

我们想删除数组中的最后一项，所以我们可以使用`array.pop()`方法。`array.pop()`方法返回添加的条目，如果数组现在为空，则返回未定义的条目。

```
pop() {
  return this.stack.pop();
} 
```

**偷看**

我们想要返回，或者偷看，堆栈中的最后一项。因此我们只需要访问最后一个索引的值。

```
peek() {
  return this.stack[this.length - 1];
} 
```

**isEmpty**

如果堆栈中没有项目，我们希望返回 true。因此，如果长度为零，则返回 true。

```
isEmpty() {
  return this.length === 0;
} 
```

### 把所有的东西放在一起

我们最终的 BookStack 代码如下所示:

```
class BookStack {
  constructor() {
    this.stack = [];
  }

  push(item) {
    return this.stack.push(item);
  }

  pop() {
    return this.stack.pop();
  }

  peek() {
    return this.stack[this.length - 1];
  }

  get length() {
    return this.stack.length;
  }

  isEmpty() {
    return this.length === 0;
  }
} 
```

你也可以用闭包来创建它。

```
function BookStack() {
  const stack = [];

  return {
    push(item) {
    return stack.push(item);
    },

    pop() {
        return stack.pop();
    },

    peek() {
        return stack[this.length - 1];
    },

    get length() {
    return stack.length;
    },

    isEmpty() {
    return this.length === 0;
    }
  }
} 
```

让我们用一些书籍数据来测试一下。

```
let myBookStack = new BookStack();
myBookStack.push('Oathbringer');
myBookStack.push('The Stand');
console.log(myBookStack.length); // 2
console.log(myBookStack.peek()); // The Stand
myBookStack.pop();
console.log(myBookStack.length); // 1
console.log(myBookStack.peek()); // Oathbringer
console.log(myBookStack.isEmpty()); // false
myBookStack.pop();
console.log(myBookStack.isEmpty()); // true 
```

你可以在这里查看密码本。

# 队列

队列在结构和方法上类似于堆栈，但是范式不同。队列使用“先进先出”或“先进先出”方法。这可以被认为是一个等待购买电影票的人的队列。

排队时间最长的人比刚加入的人先得到服务。

[![queue](img/7f671cabc1232038b142bcc817c10488.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_pp5Lukw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AA143SzcQuOhlZixXbFBpoA.png)

### 用例

队列与链表非常相似，通常用于广度优先搜索或实现缓存时。

### 约束

添加和删除节点时，队列更难更新。

### 方法

队列利用以下方法:

*   `enqueue(item)`:从队列中移除最上面的项目
*   `dequeue()`:将一个项目添加到队列的顶部
*   `peek()`:返回队列顶部的项目
*   `isEmpty()`:如果队列为空，则返回 true

### 让我们建造

对于这个例子，我们将使用 JavaScript 类。如果你想看到函数闭包的运行，请参考栈部分。

**构造器**

我们将定义一个类 MovieQueue，并给它一个具有一个属性的构造函数方法:

*   `this.queue = [];`

```
constructor() {
  this.queue = [];
} 
```

**获取**

我将添加一个 getter 来返回队列的长度。我们将在其他方法中使用它。

```
get length() {
  return this.queue.length;
} 
```

**入队**

我们希望将一个项目添加到数组的第一个索引中(队列的后面)。所以我们用`array.unshift()`的方法。

```
enqueue(item) {
  return queue.unshift(item);
} 
```

**出列**

我们希望删除队列中的第一项，或者数组中的最后一项。我们可以简单地使用`array.pop()`方法来做到这一点。

```
dequeue() {
  return queue.pop();
} 
```

**偷看**

我们想看看队列中的第一项是什么。记住这是数组中的最后一项。我们将使用`queue[this.length — 1]`来获取这个值。

```
peek() {
  return queue[this.length - 1];
} 
```

**isEmpty**

如果队列为空，我们希望返回 true。我们可以使用长度方法来获取这些信息。

```
isEmpty() {
  return this.length === 0;
} 
```

### 把所有的东西放在一起

我们最终的 MovieQueue 代码如下:

```
class MovieQueue {
  constructor() {
    this.queue = [];
  }

  enqueue(item) {
    return this.queue.unshift(item);
  }

  dequeue() {
    return this.queue.pop();
  }

  peek() {
    return this.queue[this.length - 1];
  }

  get length() {
    return this.queue.length;
  }

  isEmpty() {
    return this.queue.length === 0;
  }
} 
```

让我们用一些名字来测试一下。

```
const myMovieQueue = new MovieQueue();
myMovieQueue.enqueue('Sandra');
myMovieQueue.enqueue('Rob');
myMovieQueue.enqueue('Lisa');
myMovieQueue.enqueue('Kai');
console.log(myMovieQueue.length); // 4
console.log(myMovieQueue.peek()); // Sandra
myMovieQueue.dequeue();
myMovieQueue.dequeue();
console.log(myMovieQueue.peek()); // Lisa 
```

你可以在这里查看密码本。

* * *

我希望这篇教程能让你更好地理解队列和栈的区别！