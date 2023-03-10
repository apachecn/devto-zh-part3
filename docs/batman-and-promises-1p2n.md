# 蝙蝠侠和承诺

> 原文：<https://dev.to/jckuhl/batman-and-promises-1p2n>

# 阿尔弗雷德

想象你是某个企业的首席执行官。我猜是韦恩企业。你在蝙蝠洞里四处游荡，试图找出小丑下一步要做什么，但是你，韦恩先生，渴望喝点咖啡因。所以你去叫阿尔弗雷德，告诉他，“给我拿杯咖啡。”

所以阿尔弗雷德去给你买了杯咖啡。当他出来走动时，你仍然可以自由地继续擦亮蝙蝠战车，在蝙蝠电脑上搜索小丑的信息，修补你的蝙蝠盔甲上的洞和裂缝。

过了一会儿，阿尔弗雷德回来了。

承诺是阿尔弗雷德。承诺是 JavaScript 执行的一项任务，它保证在某个时候会返回某个值。它可能不是你想要的值，但它会是某种值。承诺是异步的。传递给 promise 的操作被放在事件循环上，而不是函数堆栈上，这允许代码中的其他函数在您等待 Promise 时继续执行。

这是一个基本的承诺:

```
new Promise((resolve, reject) => {
    doTheThing((error, value)=> {
        if(error) {
            reject(new Error('something broke'));
        } else {
            resolve(value);
        }
    });
}); 
```

我们有一些函数，`doTheThing()`得到一些值。它是什么并不重要。可能是 API 调用什么的。无论哪种方式，基本原理都是这样的。如果得到想要的值，就把它传递给`resolve()`函数。如果你没有得到你想要的值，你调用`reject()`并传递给它一个错误或者其他你想要的值，如果承诺返回一个错误。

传递给 resolve 的任何对象，然后被传递给任何`then()`调用的回调函数。`then()`在`resolve()`之后立即被解雇，一旦你的承诺兑现，它将执行你希望发生的行动。`catch()`与`reject()`合作。`catch()`也接受回调，传递给`reject()`的值随后被传递给`catch()`使用的回调。如果您有多个承诺和多个`then()`方法，那么`catch()`是所有抛出错误的一站式商店；你只需要一个`catch()`。

所以用阿尔弗雷德的例子来改写一下:

```
const coffee = new Promise((resolve, reject) => {
    getCoffee((error, coffee)=> {
        if(error) {
            reject(new Error('no coffee!');
        } else {
            resolve(coffee);
        }
    });
});
coffee.then((coffee) => drink(coffee))
    .catch((error) => console.error(error.msg)); 
```

阿尔弗雷德回来说他有你最喜欢的咖啡！你的承诺实现了，你可以喝咖啡了。阿尔弗雷德回来告诉你，他没有得到咖啡，因为小丑炸毁了所有的咖啡店。你的承诺被拒绝了。你生气了，穿上衣服，开着蝙蝠战车去处理这件事。。。错误。

# 三明治

处理完小丑，你就饿了。你想要三明治！但不是一般的三明治。蝙蝠侠毕竟品味高雅。所以你告诉阿尔弗雷德，你想从专业奶酪店买奶酪，从你最喜欢的熟食店买肉，从你最喜欢的面包师那里买新鲜面包。

阿尔弗雷德现在必须同时处理三个承诺。

这就是`Promise.all()`的用武之地。`Promise.all()`处理一个承诺数组，直到*数组中的所有*承诺都被解析后才会被解析。如果他们中的任何一个被拒绝，他们就*全部*被拒绝。

```
const sammich = Promise.all([getBread, getCheese, getMeat]);
sammich
    .then((nom) => eat(nom))
    .catch((error) => console.error(error.msg)); 
```

当阿尔弗雷德回来时，你要么把三明治的三个部分都吃了，要么就没有三明治了。

但是，当有这么多美味的哥谭市三明治店可供选择时，为什么每次只买一种配料呢？你决定你想要一个三明治，但是你不能在三个不同的商店之间做出选择，所以你会吃从你那里退回的第一个三明治。你让阿尔弗雷德、罗宾和戈登分别出去给你买三明治。

这是`Promise.race()`。`Promise.race()`接受一个承诺数组，返回第一个要解决的承诺，跳过所有被拒绝的承诺。只有所有人都被拒绝，`Promise.race()`才会被拒绝。

```
const sammich = Promise.race([sandwich1, sandwich2, sandwich3]);
sammich
    .then((nom) => eat(nom))
    .catch((error) => console.error(error.msg)); 
```

罗宾先回来了，所以你吃了他的三明治。

# 异步/等待

但是事情是这样的，使用`.then`和`.catch`会变得笨拙。我们能绕过这个吗？毕竟，你是*蝙蝠侠*和你的蝙蝠洞需要一点清理。

Async/Await 提供了一些清理。它允许您的代码中的流控制感觉更自然。它的工作方式很简单，但只对 Promise 对象有效。如果您将一个函数标记为`async`，并在该函数内标记一行`await`，则该函数的执行将在该行停止，同时等待该承诺被解决或拒绝。同时，所有其他功能将继续执行。异步函数的执行被抛出到事件循环中，并且只有在返回值和函数堆栈被清除时才会执行。

当阿尔弗雷德、戈登和罗宾去给你们拿三明治的时候，你们可以在蝙蝠洞里随意摆弄，做你们想做的任何事情，只要你们想做的事情不需要那些三明治。

`await`正是它听起来的样子，执行*必须*或`await`对这个承诺的决议或拒绝。任何拒绝都会被“尝试/捕捉”的`catch`模块拾取。

```
async function waitForCoffee() {
    let coffee;
    try {
        coffee = await getCoffee();
        coffee.drink();
    } catch(error) {
        console.error(error.msg);
    } finally {
        thank('Alfred').forServices();
    }
} 
```

> 在这个例子中，我们假设`getCoffee()`返回一个承诺。

在这里，async/await 提供了一个更加自顶向下的控制流。使用 async/await 比使用`.then()/.catch()`没有其他好处，它不会更快，也不一定更有性能。只是更容易阅读和管理。

请注意，async/await 只对承诺有效。如果你尝试在类似地理定位 API 的东西上使用它，它不会工作，除非你先把那些功能包装在承诺中。

简而言之，这就是承诺。