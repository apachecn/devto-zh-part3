# 观察到的-简介。

> 原文：<https://dev.to/gugadev/observables---una-introduccin-4k7>

观测值是您可以“订阅”以访问这些值的“lazy”价值观集合。Observable 是一种新的 JavaScript“推送”系统；它通过“推”观测者(消费者)产生多个值。

## 是“推”系统吗？

> 推送系统是生产者决定何时将信息发送给消费者的地方。消费者不知道什么时候会收到信息。

Promise 是 JavaScript 中最常见的推送系统。承诺(制作者)会将已解析的值传送给回呼注册的(用户)，但与函数不同，它会负责判断值何时会「植入」回呼。

可观察就像一个承诺；与一个承诺只能授予一个值的不同之处，而可观察到的则可以授予多个值。

## 不可观察

观测值是使用构造函数或创建运算符创建的；他们订阅了一个观察者，运行以交付`next`、`error`和`complete`给观察者的通知，其执行**可以完成。**

当我们与观察者打交道时，有四个概念:

*   创建观测值
*   订阅可观察
*   观察结果的执行
*   抛弃可观察到的

## 运行

让我们将其中一些概念付诸实践。第一，我们通过方法`create` :

```
const observable = Observable.create((subscriber) => {

}) 
```

Enter fullscreen mode Exit fullscreen mode

回调接收的参数是实现 Observer 接口的订户实例。通过此订户，我们可以将值存储在队列中，也可以决定完成作业:

```
subscriber.next('Un dato')
subscriber.next('Otro dato')
subscriber.next('Último dato')
subscriber.complete()
subscriber.next('Me olvidé de este') // nunca se enviará 
```

Enter fullscreen mode Exit fullscreen mode

调用`complete`方法后，订户将不能发出更多数据。好吧，现在我们有一个队列消息，但是，我们可以访问它们吗？这是订阅店的入口。

## 订阅

为了能够访问您拥有观测台的数据，我们需要*通过*订阅该观测台。Un `Observer`只是一个界面，其定义表明它包含三种方法:

*   `next`:此方法接受一个参数，即**观测台发送的数据**。
*   `error`:这种方法也是一种论证，是错误的。它可以是“`Error`”的子类，也可以是任何其他数据类型。
*   `complete`:当观测者通知已没有其他值可发送时，执行此方法。

让我们用一个例子来看一下行动中的观测者:

```
const observer = {
  next: value => console.log('Valor recibido: ', value),
  error: err => console.error('Error encontrado: ', err),
  complete: _ => console.log('Ya no hay más valores por recibir')
}
observable.subscribe(observer) 
```

Enter fullscreen mode Exit fullscreen mode

> **注:**回调`create`是订户，且观测者有`subscribe`方法并非巧合。事实上，当观测者订阅观测者时，“订户”会立即执行。

如果运行该代码，我们将得到以下输出:

```
// Valor recibido: 'Un dato'
// Valor recibido: 'Otro dato'
// Valor recibido: 'Último dato'
// Ya no hay más valores por recibir 
```

Enter fullscreen mode Exit fullscreen mode

### 中止订阅

一个观测者的处决可以是**无限**。观察员的一个常见做法是在我们不再需要继续观察价值观时中止处决。为此目的是上课`Subscription`。

当我们订阅观测器时，会立即获得一个类“`Subscription`”的实例，该实例在其原型中使用方法“`unsubscribe`”。这样，我们就可以停止处决一名观察员。

```
const subscription = observable.subscribe(observer)
// luego de un rato
subscription.unsubscribe() 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们不再释放观测者使用的资源，优化了我们的应用程序

在接下来的交付中，我们将讨论其他类型的观测者，包括。