# 为什么您应该在项目中使用存储库模式？以及如何做到这一点，简单的方法…

> 原文：<https://dev.to/mihaidev/why-should-you-use-the-repository-pattern-in-your-project-and-how-to-do-it-the-easy-way-12ij>

你最不需要担心的是另一种模式…

为什么我们不能用简单明了的方式写代码呢？

嗯……如果你问我，模式在软件开发中有非常重要的作用。

## 设计模式 VS 简单代码

没有简单明了的代码这种东西。

即使你不知道任何模式，你仍然在每次写代码时使用一个。这叫做“意大利面条代码模式”😊

是的，在半夜长时间编码之后，这听起来可能很美味，但是相信我，这是开发人员最糟糕的噩梦。

模式是你最好的朋友，因为它们帮助你以一种清晰易读、灵活扩展和易于推理的方式组织你的代码。

模式可以让你的生活变得更加容易，并且可以提高你将来向项目添加更多特性的速度。

1.  那么基本存储库模式应该做什么呢？

2.  它看起来怎么样？

3.  他的主要好处是什么？

基本存储库模式在模型(领域逻辑)和持久层(数据库)之间引入了一个抽象层。

它帮助您将模型从持久层中分离出来，因此在将来，您可以轻松地更改数据库表，而不会影响应用程序的逻辑。

你甚至可以改变你的整个数据库实现，你的模型不应该关心这个。

谈论一些忘恩负义的领域模型，对吗？😊

## 我们来看一些代码，这样可以更好的掌握概念

为此我将使用 Typescript，主要是因为它为 javascript 带来了强类型和接口。还因为我每天都在工作中使用它。😊

如果你不太了解 Typescript，我建议你先看看这个:Typescript 是有超能力的 Javascript

现在，让我们回顾一下，沿着记忆的轨迹…

我过去是这样将模型持久化到数据库中的:

```
import { User } from './../models'

let user = new User('Bob', 'Smith', 29, 'front end developer')

user.persiste() 
```

在用户模型中:

```
import { myslqConnection } from './mysql-connection'
export default class User
{
   private _firstName : string
   private _lastName : string
   private _age : number
   private _job : string
   constructor(
       firstName : string, 
       lastName : string, 
       age : number, 
       job : string
   ){
       this._firstName = firstName
       this._lastName  = lastName
       this._age       = age
       this._job       = job
   }
   persist()
   {
      // Somehow you need to pass in the configs for connecting to the database
      return myslqConnection.query(`
              INSERT INTO users 
              (first_name, last_name, age, job) 
              VALUES (?)`, [
          this.firstName, 
          this.lastName, 
          this.age, 
          this.job ])
    }
} 
```

这看起来不对劲。

以下是这绝对是一场灾难的一些原因:

1.  我将业务逻辑中的模型与持久层混合在一起。Obs:用户模型不应该知道它是如何在数据库中持久化的，因为它不关心这个。这种忘恩负义的用户模型，他们什么都不在乎…😊

2.  我在实际模型中实现了到数据库的连接，如果您想要更改凭证，这是一件坏事。

这个实现不好还有很多其他原因，但我不会用细节来烦你…

如果您想了解我是如何用存储库模式解决所有这些问题的，那么可以查看 Medium 上的完整文章:

[https://medium . com/@ mihaiblebea/why-should-you-use-the-repository-pattern-in-you-project-and-how-to-do-it-the-easy-way-2076 e 0889 e2b](https://medium.com/@mihaiblebea/why-should-you-use-the-repository-pattern-in-your-project-and-how-to-do-it-the-easy-way-2076e0889e2b)