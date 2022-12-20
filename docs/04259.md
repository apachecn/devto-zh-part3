# Javascript 中的定时器和调度器

> 原文：<https://dev.to/ajahso4/timers-and-schedulers-in-javascript-2o7a>

在构建最终将被集成到我们的软件中的程序时，有时，我们需要相对于时间推迟程序的某些部分的执行。此外，我们可能希望根据给定的基准，以特定的时间间隔执行一些例行任务。为此，Javascript 开发人员大多利用 setTimeout 或 setInterval 全局函数的规范来实现他们的目标。在本文中，我们将对这些定时器函数的使用进行深入研究和实验。

首先，我们将从 setTimeout 函数开始。它用于调度一个任务，该任务在事件周期经过指定的延迟后仅运行一次。setTimeout 函数接受一个函数或字符串作为其第一个参数，一个以毫秒为单位的延迟时间作为其第二个参数，以及一个可选的第三个或更多的参数，这些参数应该作为函数的参数。然而，当 setTimeout 函数的第一个参数是一个字符串时，它在内部将其包装到一个 eval 函数中，并执行**(这将在 NodeJS 运行时引发一个 type error)**，当没有提供 delay 参数时，它假定延迟为 0ms。setTimeout 函数的返回值是一个引用，可用于在其执行之前或之后清除超时，以便将其从事件循环中移除。

setTimeout 函数的典型用法

```
let timeoutId = setTimeout(function(){
console.log(`Isn't Javascript a cute language?`)
}, 2000)

let timeoutId = setTimeout(`console.log('How are you')`)

const greeter = (name, time_of_day) => {
console.log(`Good ${time_of_day}, ${name}`)
}

// then we call the setTimeout function with the greeter function as an argument
let timeoutId = setTimeout(greeter,2000,'Emeka','afternoon') 
```

setTimeout 函数有一个反物质函数，负责从事件循环中取消和清除超时函数。这个函数利用 setTimeout 函数的返回参考值来知道要取消哪个超时函数。其用法如下:

```
let timeoutId = setTimeout(function(name){
console.log(`How are you today ${name}?`)
}, 2000, 'Emeka')

clearTimeout(timeoutId) 
```

既然我们已经详尽地讨论了 setTimeout 函数的使用，现在我们可以讨论 setInterval 函数了。其用法与 setTimeout 函数相同。然而，它的妙处在于，它会在函数调用中的每个指定延迟时间之后执行一个操作。更像是在指定的延迟时间(以毫秒为单位)后重复执行一个动作。该功能适用于有时间限制的重复性任务。

setInterval 函数的典型用法

```
let intervalId = setInterval(function(){
console.log(`Isn't Javascript a cute language?`)
}, 2000)

let intervalId = setInterval(`console.log('How are you')`)

const greeter = (name, time_of_day) => {
console.log(`Good ${time_of_day}, ${name}`)
}
// then we call the setInterval function with the greeter function as an argument
let intervalId = setInterval(greeter,2000,'Emeka','afternoon') 
```

对 setInterval 的第一次调用每 2 秒执行一次分配给它的函数，而第二次调用像循环一样执行分配给它的函数，没有任何延迟。

然而，由于有一个 clearTimeout 函数用于取消 setTimeout 函数并将它们从事件循环中移除，因此也有一个 clearInterval 函数用于移除 setInterval 函数。它利用 setInterval 调用的返回值作为它的引用参数。其用法如下:

```
let intervalId = setInterval(function(name){
console.log(`How are you today ${name}?`)
}, 2000, 'Emeka')

clearInterval(intervalId) 
```

除此之外，在 NodeJS 中还有另一个名为 setImmediate 的调度函数，它在 NodeJS 事件循环结束时执行。它的工作原理类似于 setTimeout 函数，延迟值为 0ms。它也有取消它和垃圾收集的 clearImmediate 反物质。它是这样使用的:

```
let immediate = setImmediate(function(name){
console.log(`How are you today ${name}?`)
}, 'Emeka')

clearImmediate(immediate) 
```

在 NodeJS 中，上述计时器函数确实有额外的方法，这些方法只能在 NodeJS 运行时内访问。这些是 ref、hasRef、refresh(仅用于 setTimeout)和 unRef 方法，可用于函数的返回值。

要阅读更多关于 Javascript 中定时器和调度器的内容，请点击下面的链接。他们帮了我很多。谢谢你的时间。

[使用 setTimeout&setInterval](https://alligator.io/js/settimeout-setinterval/)
[在 JavaScript 中调度任务:setTimeout 和 setInterval](https://javascript.info/settimeout-setinterval)
[Timers | NodeJS](https://nodejs.org/api/timers.html#timers_class_immediate)