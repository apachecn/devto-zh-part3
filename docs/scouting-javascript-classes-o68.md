# 搜索 Javascript 类

> 原文：<https://dev.to/kudacoder/scouting-javascript-classes-o68>

[![Scouting Javascript Classes](img/0a2aa8e7b0e6366bae19622dc0568914.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dqbO_zmc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/4f12rsK/Scouting-Class.png)

受到昨天听到的一段对话的启发，我想给面向对象的 JavaScript 和 ***类*** 一个清晰的解释。理解类的第一个“时空”关键是理解 ***对象字面量*** 。你可能知道代码可以用来描述抽象和现实世界的对象。对象文字是一种通过 ***属性*** 和 ***方法*** 的键值对将数据封装在一个整洁的包中的方式。由于增强的对象文字在 ES2015 中发挥了作用，因此创建它们变得更加容易。

### 对象文字

[![Luna and Artemis](img/8dc2b0721ffd31b3a7f6efa81024ed62.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G169vexo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/VSBbrGz/lunaandartemis.jpg%23feature)

所以，这听起来很疯狂，但无意中听到的对话是两只猫在谈论一个神奇的女英雄。让我们看看我是否能把她描述成一个物体文字。

```
const sailorMoon = {
  humanName: 'Usagi',
  nickName: 'Meatball Head',
  birthday: 'June 30',
  age: 14,
  favoriteAnimal: 'Rabbit',
  favoriteColor: 'White',
  favoriteFood: 'Ice Cream',
  strength: 'Loyalty',
  weakness: 'Thunder',
  phrase: function() {
    console.log('Moon Prism Power Makeup!')
  }
} 
```

请注意，我们的女主角的对象文字被安排在花括号内，键值对用逗号分隔。所有的键值对都是 *const sailorMoon* 的 ***属性*** 。最后一种配对叫做 ***方法*** 。
我们可以使用*点符号*来访问美少女战士的不同属性或方法，比如:***object name . property key***。

```
console.log(sailorMoon.nickName);
    'Meatball Head' 
```

[![Sailor Scouts](img/c00dbdc11e623767d452236e5ea8a535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lxPnLh7V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/rGYJDQ0/Senshi-Sailor-Moon.jpg%23feature)

### 类构造函数

我们的美少女战士目标字面上是很好的，但是想象一下当我得知还有另外四个神奇的水手童子军时我有多惊讶！一定有一种更快的方法来建立我们的水手 Senshi 团队，而不是每次都写出相同的对象文字键，一个新的就神奇地出现了！感谢 ES2015，是的，有！JavaScript 中的 ***类*** 是可爱的语法糖，在对象创建和继承问题上挥舞着新月形魔杖。

有了 ***类*** ，我们可以制作一个水兵侦察兵蓝图，用几行代码粗制滥造出四个、五个，甚至九个侦察兵。**月球宇宙梦行动！**

[![Outer Sailor Scouts](img/2fa8ec2de9e31b80cae61f6847d16e49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gZ30Msuy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/NpWmxV3/Outer-Senshi.jpg%23feature)

让我们从声明我们的 ***类*** 开始。注意 ***类的名称*** 是大写的。这是惯例。在我们的 ***类*** 里面，我们将添加一个 ***构造函数*** 方法。*构造函数*就像一个工厂，它将生成我们请求的所有属性值。我们通过将这些*参数*放置在 *()* 中来请求它们。那么，你认为一个水手童子军需要什么？

```
class SailorScout {
  constructor(scoutName, humanName, birthday, element, favoriteColor, strength, weakness) {
  }
} 
```

所以我们有了我们的*构造函数*和请求列表(*参数*)。让我们在*构造函数*中放置一些对象*属性*，并将它们链接到我们的*参数*。

我们需要用点符号和关键字 ***这个*** 。在这种情况下 ***这个*** 指的是被创建的对象。它使用点符号链接到一个*属性*，并设置为等于一个*参数*。

```
class SailorScout {
  constructor(scoutName, humanName, birthday, element, favoriteColor, strength, weakness) {
    this.scoutName = scoutName;
    this.humanName = humanName;
    this.birthday =  birthday;
    this.element = element;
    this.favoriteColor = favoriteColor;
    this.strength = strength;
    this.weakness = weakness;
  }
} 
```

现在我们准备将 ***实例化*** 我们的水兵侦察兵！*实例化*意为创造。首先，我们将声明一个变量来保存对象。我们将变量设置为等于关键字 *new* 和 ***class*** name，然后我们将水手侦察器的值作为*参数*传入。

```
const sailorMercury = new SailorScout('Sailor Mercury', 'Ami', 'September 10', 'Water', 'Blue', 'Intelligence', 
'Shyness') 
```

现在如果我们***console . log(Sailor Mercury)***我们应该看到水手水星的所有属性和值！

```
SailorScout {scoutName: "Sailor Mercury", humanName: "Ami", birthday: "September 10", element: "Water", favoriteColor: "Blue", strength: "Intelligence", weakness: "shyness"} 
```

### 给我们的类添加方法

水兵侦察兵需要做一些神奇的事情，所以我们需要给我们的水兵侦察兵*增加一些*方法*。这是一个类似于向我们的美少女战士对象文本添加一个*方法*的过程。首先，我们要给我们的水手童子军增加一个*属性*:***magic phase***。然后我们将在一个*方法*中使用那个*属性*，我们将把 ***命名为*** 。*

```
class SailorScout {
  constructor(scoutName, humanName, birthday, element, favoriteColor, strength, weakness, magicalPhrase) {
    this.scoutName = scoutName;
    this.humanName = humanName;
    this.birthday =  birthday;
    this.element = element;
    this.favoriteColor = favoriteColor;
    this.strength = strength;
    this.weakness = weakness;
    this.magicalPhrase = magicalPhrase;
  }

  cast() {
    console.log(this.magicalPhrase);
  }

} 
```

让我们来看看实际情况:

```
 const sailorMars = new SailorScout( "Sailor Mars", "Rei",  "April 17th", "Fire", "Red", "Psychic Abilities","Stubborness", "Mars Fire Ignite!")

  const sailorJupiter = new SailorScout("Sailor Jupiter", "Makoto", "December 5", "Thunder", "Green", "Physical Strength", "Impulsivity","Jupiter Thunder Crash!")

  sailorMars.cast();
  sailorJupiter.cast();

  Mars Fire Ignite!
  Jupiter Thunder Crash! 
```

[![Mars Fire Ignite!](img/b1dff6d9dbddd0920b9a9d9a2d622598.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xN91QVSQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/9HFcdvH/sailor-mars-fire.jpg%23feature)

**祝贺你！您现在可以快速创建自己的水手军团了！！**
下次我们将升级我们的水兵侦察兵为‘getters’和‘setters’！*