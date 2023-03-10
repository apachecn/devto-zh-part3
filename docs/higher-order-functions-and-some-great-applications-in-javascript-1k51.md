# javascript 中的高阶函数和一些伟大的应用

> 原文：<https://dev.to/duranenmanuel/higher-order-functions-and-some-great-applications-in-javascript-1k51>

*最初发表于[EnmaScript.com](https://enmascript.com/articles/2019/02/27/higher-order-functions-and-some-great-applications-in-javascript)T3】*

## 什么是高阶函数？

高阶函数(HOF) 是一个非常强大的概念，简而言之，我们可以说 HOF 是一个将另一个函数作为参数和/或返回一个函数的函数，因此基于此，我们可以说它们是高阶函数，因为它们*以某种方式*充当其他函数的“父”或“包装器”。

如果你是一个使用过函数式编程的开发人员，你可能已经知道我在说什么，但是，请继续阅读！

## 举例

假设我们有这样的需求:

*实现一个从给定的起点计数到 100 的函数，如果给定点是奇数，该函数将以 5 为间隔计数，如果相反该点是偶数，则以 10 为间隔计数。请注意，有时用户会要求在提供起点后立即触发计数器，但情况并不总是如此*，用户可能会提供起点，然后要求在流程中的稍后点触发计数器(不是立即触发)。_

因此，不使用高阶函数的第一个实现可能如下所示:

```
const counterToOneHundred = startingPoint => {
    const isOdd = startingPoint % 2;
    const interval = isOdd ? 5 : 10;

    for (let i = startingPoint; i < 100; i += interval) {
        console.log(`${i} of 100`);
    }
}; 
```

太好了，我们拿到了...对吗？让我们看看我们的清单:

1.  [x]接收一个起点
2.  [x]如果起点是奇数，它以 5 为间隔计数
3.  [x]如果起点是一个偶数，它以 10 为间隔计数
4.  [x]它能够在提供起点后立即执行计数器
5.  [ ]它能够在流程的稍后点执行计数器

啊！我们缺少一个需求，我们几乎得到了它，让我们试着检查列表中的最后一个元素:

```
const startingPoint = 5; // starting point being any number

const counterToOneHundred = () => {
    const isOdd = startingPoint % 2;
    const interval = isOdd ? 5 : 10;

    for (let i = startingPoint; i < 100; i += interval) {
        console.log(`${i} of 100`);
    }
}; 
```

现在，因为我们将`startingPoint`从函数范围中取出，我们能够独立于变量定义执行计数器，这意味着，我们可以检查最后一个元素:

1.  [x]它能够在流程的稍后点执行计数器

**Woohoo！**还不错，对吧？但是等等，我们忽略了一些东西:

1.  为了能够定义`startingPoint`并独立执行计数器*，我们在计数器的实现之外公开了一个变量。*
**   当我们执行函数时，我们正在计算时间间隔，但是进行这个计算所需的值`startingPoint`在很久以前就已经存在了，这意味着我们可以提前计算这个值，以避免在函数中一次做所有的事情。我们可以通过将变量`isOdd`和`interval`的定义移出函数来实现这一点，但是如果我们这样做，我们会在函数之外暴露更多的变量。*   暴露变量增加了我们的应用程序发生突变的风险，因此也增加了不一致性。*

 *好吧，这可不好...

我知道这现在看起来像一个悲伤的故事...但是，它。是。不是。

(史诗英雄入口)。

### 高阶函数来救援

更少的单词，更多的代码:

```
const counterToOneHundred = startingPoint => {
    const isOdd = startingPoint % 2;
    const interval = isOdd ? 5 : 10;

    return () => {
        for (let i = startingPoint; i < 100; i += interval) {
            console.log(`${i} of 100`);
        }
    };
}; 
```

**轰！**就这样，祝你愉快...开个玩笑，现在让我们看看新的清单，然后解释一些重要的要点:

**超级动力清单:**

1.  【x】收到一个起点:**是。**(作为参数传递)。
2.  [x]如果起点是奇数，它以 5 为间隔计数:**是**。
3.  [x]如果起点是偶数，它以 10 为间隔计数:**是。**
4.  [x]它能够在提供起点后立即执行计数器
5.  [x]它能够在流程的稍后点执行计数器
6.  [x]它将变量封装起来，与外部范围隔离开来。
7.  [x]在需要时为`interval`进行计算。

### 第四点。“它能够在提供起点后立即执行计数器”

**是的。**当我们像`counterToOneHundred(1)()`一样执行函数时，我们在第一次函数调用中定义变量并返回内部匿名函数定义，然后在第二次调用中执行内部函数。

### 点 5，“能够在流程中的后一点执行计数器”和点 7。"需要时计算时间间隔"

是的。我们可以保存第一次函数调用的返回，然后在需要的时候调用内部函数:

下面的代码将匿名子函数的定义保存在一个变量中，并进行`interval`计算。

```
const counter = counterToOneHundred(1); 
```

然后我们在以后需要的时候执行计数器

```
counter(); 
```

**精彩！**

### 第 6 点，“它将变量封装起来，与外部作用域隔离”

因为所有变量都在函数范围内，所以**肯定**。

因此，通过使用 HOF，我们能够

*   封装我们的数据。
*   增加我们实施的灵活性。
*   优化流程的代码和执行顺序。

不算太寒酸吧？

### 更现实的例子

现在，计数器已经足够了，让我们用 h of 来举一个更好的例子，一个更现实的例子，*想象我们需要创建三个社交分享按钮来在 twitter、facebook 或 Linkedin 上发布我们当前的页面，这些按钮会在点击它们时打开一个弹出窗口，这取决于所点击的网络。*

这种实现可能类似于:

```
const share = () => {
    /* We setup the data required here to be able to save it in advance */
    const pageUrl = 'https://enmascript.com';
    const pageTitle = 'A place to share about web development and science';
    const networks = {
        twitter: `https://twitter.com/share?url=${pageUrl}&text=${pageTitle}`,
        facebook: `https://www.facebook.com/sharer/sharer.php?u=${pageUrl}`,
        linkedIn: `https://www.linkedin.com/shareArticle?mini=true&url=${pageUrl}`
    };

    /**
     * We receive the network type and return a function
     * with the event which is binded to the click.
     */
    return network => event => {
        event.preventDefault();

        /* if the network is not valid return */
        if (!(network in networks)) {
            return false;
        }

        /* open the popup with the selected network */
        const networkWindow = window.open(
            networks[network],
            'network-popup',
            'height=350,width=600'
        );

        /* Apply the focus to the popup window after opening it */
        if (networkWindow.focus) {
            networkWindow.focus();
        }
    };
}; 
```

这可能的用法(假设在 *React* 上)如下所示:

```
/* We setup the data once */
const shareOn = share();

/* We validate each network and open the popup on click */
<div onClick={shareOn('twitter')}><Twitter /></div> <div onClick={shareOn('facebook')}><Facebook /></div> <div onClick={shareOn('linkedIn')}><LinkedIn /></div> 
```

很酷，对吧？在这个实现中，我们还使用了一个叫做*curry*的概念，但这是一个我更愿意在另一篇文章中讨论的话题。

## 用高阶函数实现的强大功能。

有许多高阶函数的应用，下面是用这种方法实现的一些功能。

### 错误捕捉器

允许您通过传递函数定义来轻松捕捉 javascript 错误，它会自动尝试执行它，如果失败，则会发送一个回退消息，您可以用任何想要的替换回退操作。

**实施**

```
function errorCatcher(cb) {
    try {
        cb();
    } catch (error) {
        console.log('Ups, Looks like something went wrong!');
    }
} 
```

**用途**

```
function sayHi() {
    const person = { name: 'Daniel' };

    console.log(`Hi, ${person.name}  ${person.career.name}`);
}

errorCatcher(sayHi); 
```

### 节流器

控制函数`throttledFn`的执行，使其以`delayTime`的间隔执行，这对于避免执行顺序执行次数增加的事件(滚动事件、调整事件)特别有用。

**实施**

```
function throttle(throttledFn, delayTime) {
    let lastCallTime = 0;

    return (...args) => {
        const currentCallTime = new Date().getTime();

        if (currentCallTime - lastCallTime < delayTime) return;

        lastCallTime = currentCallTime;
        throttledFn(...args);
    };
} 
```

**用途**

```
function logger() {
    console.log(`I'm executed every 200ms when actively scrolling`);
}

window.addEventListener('scroll', throttle(logger, 200)); 
```

### 对一个函数的简单性能检查

检查函数执行所需的时间。

**实施**

```
function performance(fn) {
    console.time('fn');
    fn();
    console.timeEnd('fn');
} 
```

**用途**

```
function loop() {
    for (i = 0; i < 1000; i++) {
        console.log('executing loop to 1000');
    }
}

performance(loop); 
```

正如你所见，高阶函数非常有用，它们被广泛使用，你可能一直在使用它们而没有注意到，当使用*装饰模式*时，它们被应用于面向对象编程，它们也被用于像`express`和`redux`这样的库中。

我希望这篇文章对你有帮助，如果你有，请分享给你的朋友，你也可以在 [Twitter](https://twitter.com/duranenmanuel) 上关注我，下次见。*