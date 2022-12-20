# Web 组件，用于 VueJS 太多的时候

> 原文：<https://dev.to/neutrino2211/web-components-for-when-vuejs-is-too-much-8em>

如今，当你想创建一个网站时，你就无法避免“VueJS”或“ReactJS”这两个词，而且有很好的理由，这些库使开发网站变得更加容易，这要归功于它们基于组件的架构，以及它们如何处理数据/属性并相应地更新网站的相关部分*这就像魔术一样！！* ✨.

但是当我需要一个简单的组件或者我想要的元素没有动态数据时，我会问自己“我真的需要 React/Vue 吗？🤔”，这就是 web 组件的用武之地。

Web 组件是帮助你做很多事情的特性(不是元素本身)，其中之一是创建一个可以像`input`、`div`等一样使用的定制元素。

开始吧！。

### 第一步:定义我们的组件

一种方法是创建一个实现`HTMLElement`接口的类，并使用`customElements.define`函数给它一个标记名。

根据 [MDN。](https://developer.mozilla.org/)

> HTMLElement 接口表示任何 HTML 元素。一些元素直接实现这个接口，而另一些元素通过继承它的接口实现它。

```
//component.js

class MyComponent extends HTMLElement {
    constructor(){
        super();
        console.log("My component works!");
    }
}

customElements.define("my-component", MyComponent); //Register the new element 
```

Enter fullscreen mode Exit fullscreen mode

*注意，组件名是用连字符连接的，这是因为我们不允许将组件命名为类似于`coolcomponent`的东西，组件名需要类似于`x-cool-component`或`cool-component`*

现在，当我们在 HTML 文件中包含`component.js`时，我们可以使用刚刚创建的组件。

```
<!-- index.html -->

<body>
    <h1>Hello world!</h1>
    <my-component></my-component>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们检查控制台，我们会看到消息`"My component works!"`，这意味着我们的组件工作正常。

### 第二步:元素生命周期

就像在 Vue 中一样，也有生命周期回调

*   这个函数在我们的元素被渲染后被调用。

*   当我们的元素将要被移除时，这个函数被调用。

```
//component.js

class MyComponent extends HTMLElement {
    constructor(){
        super();
        console.log("My component works!");
    }

    connectedCallback(){
        console.log("Mounted!")
    }

    disconnectedCallback(){
        console.log("Unmounted!")
    }
}

customElements.define("my-component", MyComponent); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在向 index.html 添加一个按钮，删除我们的元素，这样我们就可以测试所有的生命周期回调。

```
<!-- index.html -->

<body>
    <h1>Hello world!</h1>
    <my-component id="mycomponent"></my-component>
    <button onclick="document.getElementById('mycomponent').remove()">Remove Component</button>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们按下按钮时，我们的组件被移除，我们在控制台中看到消息`"Unmounted!"`。

### 第三步:做点东西

现在我们已经有了如何制作自定义元素的基本知识，让我们来使用它吧！。时钟元素就是一个很好的例子。

*警告！！！！，**代码炸弹来袭！！！！！**T3】💣💣💣* 

```
//component.js

class ClockElement extends HTMLElement {
    constructor(){
        super();
        // Time update interval id
        this.intervalID = 0;
    }

    pad(str){
        if(str.length == 1){
            str = "0"+str
        }
        return str;
    }

    //Check if hour is pm or am
    pmOrAm(hour){
        return Number(hour) < 12 ? "am" : "pm";
    }

    getTimeString(){
        const date = new Date();
        const seconds = date.getSeconds().toString()
        const hours = date.getHours().toString()
        const minutes = date.getMinutes().toString()

        var hoursNumber = Number(hours)
        var regularHOurs = hoursNumber-12<=0? hoursNumber : hoursNumber-12;
        return this.pad(regularHOurs.toString())+":"+this.pad(minutes)+":"+this.pad(seconds)+"  "+this.pmOrAm(hours)
    }

    disconnectedCallback(){
        //Clear the timer interval
        clearInterval(this.intervalID);
        console.log("Unmounted")
    }

    connectedCallback(){
        //Start the timer
        this.intervalID = setInterval(()=>{
            this.innerHTML = this.getTimeString()
        },1000);
        console.log("Mounted")
    }
}

customElements.define("x-clock",ClockElement) 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下这是怎么回事，

*   我们已经将元素重命名为`ClockElement`，并将其注册为`x-clock`

*   现在有一个区间 id 用于识别并最终清除`connectedCallback`中声明的区间

*   `pad`方法用于给一位数的数字加一个 0，这使得时间看起来像`00:09:16`,而它看起来像`0:9:16`

*   `pmOrAm`方法根据小时返回适当的时间后缀

*   `getTimeString`方法看起来很复杂，但实际上并不复杂，我们只是获取当前的小时、分钟和秒，并将其转换成一个漂亮的字符串，以 12 小时格式显示时间

*   在`connectedCallback`中，我们启动了一个定时器，每隔 1000 毫秒(1 秒)将元素的 innerHTML 设置为当前时间

*   在`disconnectedCallback`中，我们清除计时器。

现在我们已经理解了代码，让我们将元素添加到我们的网站中。

```
<!-- index.html -->

<body>
    <h1>Hello world!</h1>
    <x-clock></x-clock>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步:属性

到目前为止，我们的时钟看起来不错，但它还可以更好，我们现在将根据我们选择的属性使它显示 24 小时或 12 小时格式。我个人喜欢这个语法:

> `<x-clock military></x-clock>`

所以我们的目标是将属性的存在作为一个布尔量。

```
 getTimeString(military){
        const date = new Date();
        const seconds = date.getSeconds().toString()
        const hours = date.getHours().toString()
        const minutes = date.getMinutes().toString()

        if(typeof military == "string"){
            return this.pad(hours)+":"+this.pad(minutes)+":"+this.pad(seconds)
        } else {
            var hoursNumber = Number(hours)
            var regularHOurs = hoursNumber-12<=0? hoursNumber : hoursNumber-12;
            return this.pad(regularHOurs.toString())+":"+this.pad(minutes)+":"+this.pad(seconds)+"  "+this.pmOrAm(hours)
        }
    }

    disconnectedCallback(){
        //Clear the timer interval
        clearInterval(this.intervalID);
        console.log("Unmounted")
    }

    connectedCallback(){
        const military = this.getAttribute("military")
        this.innerHTML = this.getTimeString(military)
        this.intervalID = setInterval(()=>{
            this.innerHTML = this.getTimeString(military)
        },1000);
        console.log("Mounted")
    } 
```

Enter fullscreen mode Exit fullscreen mode

*如果你注意在`getTimeString`中添加的新代码，你会看到一个非常奇怪的语句`typeof military == "string"`，这是因为当我们像这样设置属性* :

```
<x-clock military></x-clock> 
```

Enter fullscreen mode Exit fullscreen mode

*我们得到属性的值为`""`，在 javascript 中它相当于 false，所以即使属性存在*，`if(military)`也将返回 false

现在，我们可以通过添加属性来选择以 12 小时制或 24 小时制显示！！

```
<!-- index.html -->

<body>
    <h1>Hello world!</h1>
    <x-clock></x-clock>
    <x-clock military></x-clock>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步:反应状态

我们的元素目前在运行时不改变状态，即使我们的属性改变了，这看起来可以改进。因此，我们现在将使元素对属性变化作出反应。

为此，我们使用了一个`MutationObserver`，这有助于我们观察元素的任何变化。

放置它的一个好地方是在元素构造函数中。`MutationObserver`构造函数返回一个 MutationObserver，每当我们的元素发生变化时，它就调用一个指定的回调函数。

```
 constructor(){
        super();
        // Time update interval id
        this.intervalID = 0;
        this.observer = new MutationObserver((mutations)=>{
            for(var mutation of mutations){
                if(mutation.type == "attribute"){
                    // React to changes
                }
            }
        });

        this.observer.observe(this,{
            attributes: true // Only listen for attribute changes
        });
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们将观察者分配给`this.observer`而不是`const observer`，因为我们需要清理`disconnectedCallback`中的监听器。

```
 disconnectedCallback(){
        //Disconnect observer
        this.observer.disconnect();

        //Clear the timer interval
        clearInterval(this.intervalID);

        console.log("Unmounted")
    } 
```

Enter fullscreen mode Exit fullscreen mode

当属性改变时，我们需要显示准确的时间格式，为此，我们还需要将`const military`改为`this.military`，这样我们就可以从 MutationObserver 访问变量。

```
 constructor(){
        super();
        // Time update interval id
        this.intervalID = 0;
        this.observer = new MutationObserver((mutations)=>{
            for(var mutation of mutations){
                if(mutation.type == "attribute"){
                    // React to changes
                    this.military = this.getAttribute("military");
                }
            }
        });

        this.observer.observe(this,{
            attributes: true // Only listen for attribute changes
        });
    }

    //Other code

    connectedCallback(){
        this.military = this.getAttribute("military")
        this.innerHTML = this.getTimeString(this.military);
        this.intervalID = setInterval(()=>{
            this.innerHTML = this.getTimeString(this.military);
        },1000);
        console.log("Mounted");
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 我们完成了🎉🎉🎉🎉🎉🎉🎉🎉

我们不仅制作了自定义元素，还让它对变化做出动态反应。这仅仅触及了 web 组件所能做的表面，我迫不及待地想看到你们将使用它做的伟大的事情。

再次声明，这并不是 VueJS(或其同类产品)的替代品，它只是在 **Vue 过度使用**T3】时的一种替代方案

## 感谢阅读！！