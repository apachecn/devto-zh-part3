# JS 中的创造模式

> 原文：<https://dev.to/facundogflores/creational-patterns-in-js-1ce1>

## 构造器模式

它与其他语言有点不同，因为我们不会为此使用类(尽管 ES6 中提供了类)。所以这里的主要目标是创建一个具有自己的对象范围的新对象。我们将开始看看`new`操作员在为我们做什么

1.  如果你把这个操作符放在一个对象前面(记住函数是 javascript 中的对象),它会给我们一个新的对象。
2.  将创建的对象链接到一个对象原型(它不是继承)
3.  将`this`关键字绑定到新对象的范围
4.  返回上一个`this`

(欲了解更多信息，请访问[新操作员](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new))

一个例子

```
function Todo(title, isDone) {
    this.title = title;
    this.isDone = isDone;
    // here should be placed this, but new is doing this for us
} 
```

所以这个模式允许我们传递一些参数给一个函数(这是我们对象的名字)，因为我们要使用`new`，`this`在我们的作用域中是可用的。

让我们来看看如何使用`constructor`

```
// wrong way
a = Todo('title', false) // a will be undefined
console.log(a.title) // because a is undefined you can't access to a

// right way
a = new Todo('title', false) // a will be the object with this in context
console.log(a.title) // will print title 
```

## 工厂模式

这是允许它为您创建对象的核心模式之一。这种方法的主要目的是隐藏一些客户端的对象创建逻辑。

所以，假设现在我们有个人和工作`Todo`个，区别是`personal`个完成或没有完成，而`work`个可以衡量进度。

```
function PersonalTodo(config) {
    this.title = config.title;
    this.isDone = config.isDone;
    this.complete = function () {
        this.isDone = true;
    }
    this.reset = function () {
        this.isDone = false;
    }
} 
```

```
function WorkTodo(config) {
    this.title = config.title;
    this.progress = 0;
    this.addProgress = function() {
        if(this.progress > 90) return;
        this.progress += 10;
    }
    this.substractProgress = function() {
        if(this.progress < 10) return;
        this.progress -= 10;
    }
} 
```

现在我们可以在`TodoFactory`上创建我们的

```
function TodoFactory() {
    this.createTodo = function(config) {
        if(config.type === 'personal') {
            this.todoType = PersonalTodo;
        }
        if(config.type === 'work') {
            this.todoType = WorkTodo;
        }
        return new this.todoType(config)
    }
} 
```

让我们试一试:

```
var myFactory = new TodoFactory();
var personal = myFactory.createTodo({type: 'personal', title: 'foo', isDone: false})

console.log(personal instanceof PersonalTodo); // true
console.log(personal) // personal todo with title: "foo" isDone: false

var work = myFactory.createTodo({type: 'work', title: 'foo'})

console.log(work instanceof WorkTodo); // true
console.log(work) // work todo with title: "foo" progress: 0
work.addProgress()
console.log(work) // work todo with title: "foo" progress: 10 
```

我们在这里看到的是一种隐藏对象创建的方法(显式地使用`new`)来赋予新对象生命。

## 单一模式

主要目标是在我们的应用程序中只有一个对象实例。从这个模式中，你通常会在你的对象中有一个名为`getInstance`的方法来获取单例实例，在这里你将检查该实例是否存在，如果不存在，你需要创建一个。

```
var TodoSingleton = (function() {
    var instance;
    function init() {
        var instance = new WorkTodo({title: 'workTodo'});
        return instance;
    }
    return {
        getInstance: function () {
            if(!instance) {
                instance = init();
            }
            return instance;
        }
    }
})(); 
```

现在，让我们试一试:

```
work1 = TodoSingleton.getInstance() // here we create a new WorkTodo
work2 = TodoSingleton.getInstance() // here we use the previous instance

console.log(work1.progress) // 0
console.log(work2.progress) // 0

work1.addProgress()

console.log(work1.progress) // 10
console.log(work2.progress) // 10 
```

当您必须在应用程序中使用服务时，这种模式非常有用。特别是，你不希望在你的应用程序中运行其中一个的不同实例。此外，有时你想在你的应用程序中加载第三方库，你想避免每次你想使用它时加载它。

## 额外

```
<button id="status"> Get Status </button> 
```

```
function PersonalTodo(config) {
    this.title = config.title;
    this.isDone = config.isDone;
    this.complete = function () {
        this.isDone = true;
    }
    this.reset = function () {
        this.isDone = false;
    }
    this.getStatus = function() {
        return this.isDone;
    }
}

var personal = new PersonalTodo({title: 'foo', isDone: false})
personal.getStatus() // false

var element = document.getElementById("status");
element.onclick = personal.getStatus; // does not work this is in context of dom
element.onclick = personal.getStatus.bind(personal); // now you bind this to the object 
```

怎样才能避免使用`bind`？

```
// Solution 1
function PersonalTodo(config) {
    this.title = config.title;
    this.isDone = config.isDone;
    this.complete = function () {
        this.isDone = true;
    }
    this.reset = function () {
        this.isDone = false;
    }
    return {
      getStatus: function() {
          return alert(this.isDone);
      }
    }
}

var element = document.getElementById("status");
element.onclick = personal.getStatus; // not getStatus will be in the context of the object 
```

注意:可以使用 ES6 中的箭头功能替换之前的样本。

## 结论:

设计模式在开发时非常有用。拥有识别它们的能力可以加速你的编码和解决常见问题。我看到并欣赏的另一个优势是，你可以很容易地理解新的代码库，并有可能更快地学习新技术，因为所有的软件工程师都将他们的程序建立在这些基本而强大的东西之上。