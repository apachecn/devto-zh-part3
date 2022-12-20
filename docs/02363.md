# 如果您想要(分配)在我的 JS 对象中:有条件地向 JS 对象添加属性

> 原文：<https://dev.to/silvestricodes/if-you-wannabe-assigned-in-my-javascript-object-haa>

如果您使用过 JavaScript 中的对象，那么您可能会遇到这样的情况:您需要有选择地向现有对象添加值。为了说明我的例子，我想欢迎辣妹来到互联网的这个角落。

传统上，你可以这样赋值:

```
 function relationship(name, youGotWithMyFriends) {
    var interestedParty = {
      name: name
    }

    if(youGotWithMyFriends) {
      interestedParty.lover = true
    }

    return interestedParty
  }

  // Later in our code...
  var person = relationship('person', false)
  console.log(person) // { name: 'person'}
  var nextPerson = relationship('match', true)
  console.log(nextPerson) // { name: 'match', lover: true } 
```

正如辣妹组合在《想成为》中为我们清晰勾勒的那样，你需要和她们的朋友相处才能成为一个爱人。

现在，有人可能会说，您可以将`lover`属性添加到所有对象中，并让它默认为 false。完全有效，但是为了这个例子的目的，假设我们不想要一个`lover`属性，除非它为真，并且继续沿着这条路走下去。

如果我们不得不添加更多的属性和额外的保护子句来防止给我们的对象赋值 falsey 值呢？不得不一遍又一遍地写陈述可能会有点无趣。

## 借助 ES6 实现方法现代化

有了 ES6，我们可以使用 spread 语法。对于从一个集合中获取值并将它们传播到另一个集合中很有用，但是我们上面的例子也可以利用这个语法！

让我们和辣妹们再聊一聊，看看她们对我们有趣的新 ES6 功能有什么想法...

```
 function relationship(name, youGotWithMyFriends) {
    return {
      name,
      ...(youGotWithMyFriends && { lover: true }) // !!!
    }
  }

  // Later in our code...
  const person = relationship('person', false)
  console.log(person) // { name: 'person'}
  const nextPerson = relationship('match', true)
  console.log(nextPerson) // { name: 'match', lover: true } 
```

我们得到同样的结果！但是，嗯...英国辣妹组合...

## 到底怎么回事？

让我们回顾一下利用 spread 操作符将该属性添加到返回对象`...(youGotWithMyFriends && { lover: true })`的代码行，并讨论一下发生了什么。

在`youGotWithMyFriends`为`true`的情况下，您最终继续您的代码并点击`{ lover: true }`片段，并且因为使用了扩展语法，它将把该对象的内容扩展到它所嵌套的对象，给您第二个`console.log`值！

但是如果那个布尔值是`false`会发生什么呢？你能把 false“传播”到一个对象上而不抛出错误吗？

spread 语法可以被认为是一种简化的、更直接的`Object.assign`函数调用。当您使用它时，就像调用如下函数:

```
Object.assign(targetObject, thingsToAddToTarget) 
```

当我们使用`Object.assign`时，我们可以看到我们的第一个例子是如何工作的，因为`thingsToAddToTarget`将只是我们的新对象。但是我们来谈谈作为第二个论点的 false 被通过的情况:

```
const targetObject = {}
Object.assign(targetObject, false) // returns {} 
```

在`Object.assign`的实现周期中的某个时候，团队决定如果第二个参数不是索引值，它将忽略传入的内容并返回原始对象，不做修改！尝试在您的浏览器控制台中传递一个数字、一个空值甚至未定义的值作为第二个参数，您自己会看到:)。

我希望你喜欢这个利用一个很酷的 ES6 特性的小例子，并且你能在今天的代码中找到用例。