# 继承之上的组合

> 原文：<https://dev.to/thisismahmoud/composition-over-inheritance-4fn9>

# 成分超过继承

当谈到构建软件时，面向对象编程(OOP) 是最流行的范例之一，然而，随着我对 JavaScript 的了解越来越多，我开始接触*函数式编程(FP)* ，这是一种完全不同的思维方式。在这篇文章中，我将与你分享我所学到的东西，并用我自己的例子向你解释。

例如:你想构建一个游戏，其中的主角是霍格沃茨的一名学生，他正在向他的老师学习巫术。
所以你创建了一个*学生*类，其中你的学生对象将能够参加考试，你还有另一个名为*教师*的类，其中教师对象将能够给考试评分。

```
Student
  .takeTest()

Teacher
  .gradeTest() 
```

假设你想增加一些功能，比如吃饭、睡觉和走路，你最终会得到类似这样的东西

```
Student
  .takeTest()
  .sleep()
  .eat()
  .walk()

Teacher
  .gradeTest()
  .sleep()
  .eat()
  .walk() 
```

问题是上面的代码没有遵循 DRY 原则，DRY 原则代表(不要重复自己)，所以解决方案是创建一个*父*类，我们在其中放置睡眠、进食和行走功能。所以我们最终得到了这样的结果:

```
Human
  .sleep()
  .eat()
  .walk()

    Student
     .takeTest()

    Teacher
     .takeTest() 
```

Yaaay 问题解决了！每个人都很开心。然而，几个月后，为了让你的游戏更加刺激，你决定给你的游戏添加另一个角色，比如说你想添加一条龙。这是你最后得到的:
所以你最后得到的是这样的:

```
EvilDragon
 .breathFire()
 .fly()
Human
  .sleep()
  .eat()
  .walk()

    Student
     .takeTest()

    Teacher
     .takeTest() 
```

虽然这很棒，但你并不满意，所以你决定让游戏更真实，像人类一样赋予你的龙能力，你希望你的龙能够睡觉和吃饭，所以你最终得到了这样的东西:

```
GameObject
  .sleep()
  .eat()

    EvilDragon
     .breathFire()
     .fly()

    Human
     .walk()

        Student
        .takeTest()

        Teacher
        .takeTest() 
```

现在，这是完全正常的，但假设在你的游戏中，老师在仔细研究了巫术后，能够创造一个能让他飞的斗篷。现在问题来了:*你如何组织你的项目？你不能在人类职业中加入飞行能力，因为不是所有的人类都能飞，你也不能让老师继承恶龙职业，因为老师不能喷火。
有一个解决方案是添加教师对象永远不会使用的功能，使他能够飞行，就像喷火一样。然而，这就像要一根香蕉，收到一只拿着香蕉的大猩猩。
所以现在，你碰壁了，你不能把你的游戏想法变成现实。当然，你可以重复你自己，但是因为你是一个优秀的开发人员，关心他写的每一行代码，所以你不会这样做。不要担心，有一个解决方案:**构图***

 *有了继承，你可以围绕它们是什么来构造你的类。使用函数式编程，你可以根据**构造你的类，它们做什么**。

通过更喜欢组合而不是继承，并且考虑什么事情*做什么事情*而不是什么事情*是什么事情*，你将自己从脆弱和紧密耦合的继承结构中解放出来。

当我们从对象的角度考虑时，下面是我们的代码在游戏中的一个片段:

```
class Teacher{
    constructor(name){
        this.name = name;
    }
    sleep(){
        setTimeout(console.log('Damn, that was a great nap'),8000);
    }
}

const teacher = new Teacher(Dumbledore);
teacher.sleep();
//outputs 'Damn, that was a great nap' after 8 seconds 
```

当我们使用函数式方法编写项目时，我们最终会得到一个独立函数的集合，每个函数都服务于一个单一的目的，这有助于维护，并使调试更容易，因为我们可以预测函数的结果。当创建一个对象时，我们简单地导入我们需要的所有函数。

```
//same code rewritten using factory functions
//factory functions are functions that create objects
//we will use
const sleep = () => {
     return setTimeout(console.log('Damn, that was a great nap'),8000);
}
Object.assign(

) 
```

哪一个赢了？通常情况下，当对象之间有关系时，你会使用继承，比如我，马哈茂德，我是一个人，所以我会继承一个人拥有的所有东西。另一方面，当一个物体*具有*某种特征时，你可以使用复合，比如一辆汽车将有一个引擎组件。

然而，这并不完全正确，因为，例如，汽车是一种交通工具，而我，马哈茂德，我有手臂，我可以举起东西。所以，在现实中，你可以遵循这些模式中的任何一种。总之，组合更容易调试、维护，所有的东西都被封装在一个项目中，你可以很容易地添加特性。*