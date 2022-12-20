# TypeScript: JavaScript + Types =出色的开发效率

> 原文：<https://dev.to/vintharas/typescript-javascript--types--awesome-developer-productivity-1048>

*本文是来自 [JavaScript-mancy OOP 的一章:掌握在 JavaScript 中召唤对象的神秘艺术](https://www.javascriptmancy.com/#get-the-books)一个惊人的故事，史诗般的比例，尴尬的幽默感，一次拯救世界一个 JavaScript 函数。*

```
JavaScript-mancy is a dangerous art.
Get an incantation slightly wrong,
and anything can happen. 

More than one young apprentice
has found out about this the hard way,
just a split second before 
razing a village to the ground,
letting loose a bloodthirsty beast,
or making something unexpected explode.

That's generally the way it is.

There are records of an ancient order,
an order of disciplined warrior monks,
who were able to tame the wild winds of magic.
But too little of them remains.

Did they exist? 
Or are they just wishful thinking and myth?

        - The Chronicler 
```

> ```
> /* 
> The group walks into the chamber and 
> the door closes itself behind them...
> 
> ...the chamber is poorly lit. A metal brazier of eerie blue 
> flames lies in the middle of the room and bathes a strange 
> obsidian obelisk in a mysterious light. Huge columns 
> surround the obelisk at irregular intervals. 
> Under the dim light it is impossible to ascertain 
> the room proportions...
> */
> 
> mooleen.says('Something about this feels very wrong...');
> red.says("I couldn't agree more");
> 
> randalf.says("Well, it's either destroy that totem and " + 
>   "escape aided by magic or go back and fight The Red Legion" +
>   "with our bare fists");
> rat.says("...and sharp claws");
> 
> mooleen.says("Alright, let's get this over with");
> 
> /* 
>   The group approaches the obelisk under an oppressive
>   silence only broken by the sound of boots scraping 
>   the sand covered ground.
> */
> 
> red.says('Oh shit');
> mooleen.says('Oh shit? I thought you ' +
>   'were beyond that type of language');
> 
> /* 
>   Eerie blue and green lights start flaring around the
>   group inundating the chamber with light and a ghastly 
>   atmosphere. Up and up they go to reveal enormous terraces 
>   filled with dark winged figures. A voice suddenly booms 
>   within the chamber:
> */
> 
> voice.thunders('Let the games begin!');
> voice.thunders("In our next game we'll recreate " + 
>   "The Fall of the Order of The Red Moon " + 
>   "the sacred order of warrior monks" + 
>   "whose fierceness still echoes across the centuries");
> voice.thunders('I give you: The Last Stand!');
> 
> /* 
> A thunderous applause mixed with screeches, screams and 
> shouts of joy and excitement follows the proclamation.
> At the same time 4 humongous iron doors start slowly
> opening and row after row of obscene four-legged reptilian 
> creatures start emerging from them. Their impossibly huge 
> mandibles and terrible wails freeze your blood.
> */
> 
> mooleen.says('Oh shit');
> 
> voice.thunders('The rules of the game:');
> voice.thunders('#1\. Fight or Die...');
> voice.thunders('#2\. You Shall Only Use Types!');
> voice.thunders('#3\. Only One Shall Remain'); 
> ```

## 只能使用类型！

恭喜你坚持到了这本书的结尾！我为你准备了一份特别的礼物作为告别礼物:打字稿！在过去几年中，TypeScript 的发展势头越来越大，在。NET 世界即使有 Angular 和 React 等流行的前端框架。TypeScript 提供了您可以在 web 上找到的最接近 C#的体验。尽情享受吧！

## JavaScript +类型=出色的开发效率

TypeScript 是 JavaScript 的超集，它添加了类型注释，从而在 JavaScript 之上添加了静态类型。

如果你是一个 C#或 Java 开发人员，你会觉得在编写打字稿时如鱼得水。如果您是一名 JavaScript 开发人员或具有动态编程语言背景，您将会遇到一个稍微冗长的 JavaScript 版本，它会带来更安全、更好的开发体验。不管怎样，您会很高兴地知道，到目前为止您所学的关于 JavaScript 的一切也适用于 TypeScript，也就是说，**任何 JavaScript 都是有效的 TypeScript** 。

## 任何 JavaScript 都是有效的 TypeScript

> ### 实验 JavaScriptmancer！！
> 
> 您可以[使用 TypeScript playground](https://bit.ly/javascriptmancy-oop-typescript-basic) 或从 [GitHub](https://github.com/vintharas/javascriptmancy) 下载源代码来试验本节中的示例。

JavaScript 的任何部分都是有效的类型脚本。假设我们有你可以编写的最基本的 JavaScript 代码，一个简单的变量声明，代表你的法力储备:

```
var manaReserves = 10; 
```

现在让我们说，我们想通过喝一种魔法药水来补充你的魔法储备:

```
function rechargeMana(potion){
  return potion.manaModifier * (Math.floor(Math.rand()*10) + 1);
} 
```

所以我们去写下面的:

```
manaReserves += rechargeMana({
  name: 'light potion of mana',
  manaModifier: 1.5 
}); 
```

当我们执行上面这段代码时，它会显示以下错误:

```
// => Uncaught TypeError: Math.rand is not a function 
```

这很有意义，因为 JavaScript 中没有所谓的`Math.rand`函数。它叫做`Math.random`。出于某种原因，我把这个函数和一个 C 函数混合在一起，这个 C 函数有相同的目的，只是名字有点不同，我在学生时代用过。不管怎样，我一次又一次地犯这个错误。

上面的代码是一段非常传统的 JavaScript。但它也是完全有效的 TypeScript，只有一点不同。在 TypeScript 中编写`rechargeMana`会自动导致一个编译器错误，该错误应该是:

```
Property 'rand' does not exist on type 'Math'. 
```

这将立即提醒我(再次)犯了一个错误，并且我将能够在执行程序之前修复它。这是 TypeScript 的优势之一:**更短的反馈循环，可以在编译时而不是运行时检测代码中的错误**。

让我们扩展我们之前的例子，喝另一种药水:

```
rechagreMana({
  name: 'Greater Potion of Mana',
  manaModifier: 2
}) 
```

又来了。一个简单的输入错误，一个在运行时会导致`ReferenceError`的 JavaScript 中的经典错误，会立即被 TypeScript 编译器捕获:

```
Cannot find name 'rechagreMana'. 
```

正如我们到目前为止所看到的，位于您编写的 TypeScript 代码和在浏览器中运行的输出之间的 TypeScript 编译器可以在普通 JavaScript 上为您做很多事情。但是**当你开始添加类型注释**时，也就是说，当你用关于事物类型的附加信息来注释你的 JavaScript 代码时，它才真正闪耀。

例如，让我们用一些类型注释来更新我们原来的`rechargeMana`函数:

```
function rechargeMana(potion: { manaModifier : number }) {
  return potion.manaModifier * (Math.floor(Math.random()*10) + 1);
} 
```

上面的例子包含了参数`{manaModifier : number}`的类型注释。这个注释意味着`potion`参数应该是一个具有类型为`number`的属性`manaModifier`的对象。

类型注释为我们做了几件事:

1.  当作为参数传递给`rechargeMana`的对象没有预期的接口时，它可以帮助编译器发现错误。也就是说，当它缺少函数工作所必需的`manaModifier`属性时。
2.  当您在函数体中使用`potion`对象时，它可以帮助编译器发现拼写错误或类型错误。
3.  当在一个伟大的开发者 experience[^statementcompletion].的`rechargeMana`函数中输入`potion`时，它给了我们语句完成如果你不熟悉语句补全，它包含有用的编辑器内信息，它会弹出来告诉你如何使用一个对象，比如哪些属性是可用的方法，不同的参数需要哪些类型，等等。

[^statementcompletion]:您使用的编辑器应该与 TypeScript 编译器很好地集成，以提供这种类型的服务。许多最常见的 ide 和文本编辑器都支持这一点。

让我们用一个例子来说明 1)。想象一下，除了法力药剂，你还有力量药剂:

```
const potionOfStrength = {
  name: 'Potion of Strength',
  strengthModifier: 3,
  duration: 10
}; 
```

在程序中的某个时刻，我们可能会错误地调用这段代码:

```
rechargeMana(potionOfStrength); 
```

用一个`potionOfStrength`作为参数调用`rechargeMana`函数会导致 JavaScript 运行时错误，或者甚至是一个难以捉摸的错误，因为用`number`乘以`undefined`会导致`NaN`，而不是彻底崩溃。

然而，在 TypeScript 中，上面的例子会导致下面的编译器错误:

```
// [ts] 
// Argument of type '{ name: string; strengthModifier: number; }' 
// is not assignable to parameter 
//   of type '{ manaModifier: number; }'.
// Property 'manaModifier' is missing 
// in type '{ name: string; strengthModifier: number; }'. 
```

这个错误会很快告诉我，力量药剂缺乏使用所需的契约，大量的眼泪和沮丧会被保存在那里。还需要花一点时间来欣赏上面的错误消息的质量和精确度。

所以任何 JavaScript 都是有效的类型脚本。将您的`code.js`文件更改为`code.ts`文件，通过 TypeScript 编译器运行它，TypeScript 将尝试从您的代码中推断出尽可能多的信息，并尽最大努力帮助您。在此基础上添加类型注释，TypeScript 将能够更多地了解您的代码和意图，并为您提供更好的支持。

## 那么，TypeScript 的优缺点是什么？

通过用新特性增强 JavaScript，类型批注和静态类型化 TypeScript 提供了以下优势:

*   **更好的错误检测**。TypeScript 可以对您的代码进行静态分析，并在运行实际代码之前揭示错误。这提供了一个更短的反馈循环，这样当这些错误在编辑器中发生时，您就可以修复它们，而不是在它们进入生产环境之后。
*   **更好的工具和开发效率**。丰富的类型信息可以被编辑器和 ide 用来提供很好的工具来提高开发人员的生产力，比如编辑器中的编译器警告、语句完成、安全重构、内联文档等等... [Visual Studio Code](https://code.visualstudio.com/) 是一个文本编辑器，它具有出色的开箱即用的类型脚本支持。
*   **卓越的 API 可发现性**。使用类型注释提供的语句补全是在编辑器中发现新 API 的一种很好的方式。
*   **多写有心代码**。TypeScript 类型注释和访问级别关键字等附加功能允许您约束您设计的 API 的使用方式。这允许您编写更多有意的代码。
*   **ESnext 特色**。TypeScript 支持许多 ESnext 特性，如类成员、decorators 和`async/await`。
*   **附加打字稿功能**。除了 JavaScript 和 ESnext 特性之外，TypeScript 还有少量 ECMA-262 规范中没有的特性，这些特性为语言增加了很多功能，比如属性访问级别和参数属性。
*   **与第三方库合作**。在应用程序代码中使用类型注释是很棒的，但是在整个应用程序代码中使用和引用的所有第三方库呢？TypeScript 如何与它们交互？特别是，当这些库不是用 TypeScript 编写时会发生什么？在最坏的情况下，TypeScript 将它不知道的对象视为类型`any`，这基本上意味着*“这个对象可以有任何形状，所以就像在 JavaScript 中一样，不要做任何假设”*。更常见的是，第三方库要么附带声明文件，为 TypeScript 提供类型信息，要么您可以通过 [DefinitelyTyped 项目](http://definitelytyped.org/)找到这些声明文件，该项目是 TypeScript 类型定义的存储库。这意味着您将能够享受与您自己的代码相同级别的第三方库的 TypeScript 支持(甚至更好)。
*   **非常适合大规模应用和团队**。TypeScript 擅长用大规模应用程序支持多个团队。类型注释和 TypeScript 编译器在捕捉重大变化、细微错误和新的 API 可发现性方面非常出色。

不利的一面是:

*   **TypeScript 需要一个翻译步骤**。任何浏览器都不支持 TypeScript 代码。为了能够用 TypeScript 编写您的应用程序，您需要设置某种构建管道来将您的 TypeScript 代码转换成可以在浏览器中运行的 JavaScript 版本。幸运的是，开源社区对此有很好的支持，您可以在最流行的框架和构建工具中找到 TypeScript 的很好的集成。
*   **你需要学习类型注释语法和相关的工件**。类型注释、它们的语法和相关的工件，如接口、泛型等...除了编写 JavaScript 应用程序所需的知识之外，还要增加更多的认知负担和额外的复杂性。
*   **是啰嗦的**。添加类型注释会使您的 JavaScript 代码更加冗长(`call(person:Person)`)，这在美学上可能非常令人不快(尤其是在开始的时候)。TypeScript 编译器在推断类型和减少需要编写的类型注释数量方面做得很好，但是为了充分利用 TypeScript，您需要自己添加大量的类型注释。
*   有时它与 ECMAScript 标准不一致。今天为您带来的 ESnext 功能虽然很棒，但也有缺点。在 ESnext 特性正式化之前实现它们可能会导致 TypeScript 违反标准，就像它在模块中发生的那样。幸运的是，TypeScript 作为 JavaScript 超集的核心理念使得 TypeScript 团队实现了对 ES6 模块的支持，并摒弃了非标准版本。这是 TypeScript 忠于 JavaScript 的一个很好的标志，但在采用 ESnext 特性时仍然需要考虑。

## 设置一个简单的 TypeScript 项目

了解完整的 TypeScript 开发体验的最佳方式是从头开始设置一个简单的 TypeScript 项目，并按照本章的剩余部分进行操作。像往常一样，您可以从 [GitHub](https://github.com/vintharas/javascriptmancy-code-samples) 下载这些和所有示例的源代码。

最简单的入门方法是在您的开发计算机上安装 [node 和 npm](https://nodejs.org/en/) 。一旦你完成了，我们将使用 npm:
安装 TypeScript 编译器

```
$ npm install -g typescript 
```

您可以通过运行:
来验证安装是否正常运行

```
$ tsc -v
Version 2.4.2 
```

和访问 TypeScript 编译器帮助:

```
$ tsc -h
Version 2.4.2
Syntax:   tsc [options] [file ...]

Examples: tsc hello.ts
          tsc --outFile file.js file.ts
          tsc @args.txt 
```

在这些例子中，我将使用 [Visual Studio 代码](https://code.visualstudio.com/)，但是欢迎你使用[任何你喜欢的编辑器](http://www.typescriptlang.org/index.html#download-links)。

在下面键入这个命令将创建一个名为`hello-wizard.ts`的新的 TypeScript 文件，并将在 Visual Studio 代码:
上打开它

```
$ code hello-wizard.ts 
```

让我们用 TypeScript 编写带有`sayHello`函数的规范 hello 向导:

```
function sayHello(who: string) : void {
  console.log(`Hello ${who}! I salute you JavaScript-mancer!`);
} 
```

请注意我们是如何将类型注释`string`添加到该函数的`who`参数中的。如果我们试图用一个与预期的`string`类型不匹配的参数调用函数，编译器将在我们的编辑器中警告我们一个编译错误:

```
sayHello(42);
// => [ts] Argument of type '42' is not assignable 
//         to parameter of type 'string'. 
```

让我们通过向你致敬来解决它。更新上面的代码，将您的名字包含在一个字符串中:

```
sayHello('<Your name here>'); 
```

现在，您可以在终端中使用编译器来编译 TypeScript 文件(Visual Studio 附带了一个嵌入式终端，您可以在编辑器中运行它，这非常方便)。类型:

```
$ tsc hello-world.ts 
```

这将告诉 TypeScript 编译器将您的 TypeScript 应用程序转换成可以在浏览器中运行的 JavaScript。这将产生一个普通的 JavaScript 文件`hello-world.js`，其中包含以下代码:

```
function sayHello(who) {
  console.log("Hello " + who + "! I salute you JavaScript-mancer!");
}
sayHello('<Your name here>'); 
```

漂亮的香草 JavaScript，就像你徒手打出来的一样。您可以使用`node`来运行这个文件:

```
$ node hello-world.js
Hello <Your name here>! I salute you JavaScript-mancer! 
```

还有 TaDa！你已经编写、编译并运行了你的第一个打字稿程序！世界，我们来了！

由于每次在`ts`文件中进行更改时运行 TypeScript 编译器可能会有点乏味，所以可以在**监视模式**中设置编译器。这将告诉 TypeScript 编译器监视您的源代码文件，并在检测到更改时传输它们。要在监视模式下设置 TypeScript 编译器，只需键入以下命令:

```
$ tsc -w hello-world.ts
10:55:11 AM - Compilation complete. Watching for file changes. 
```

在接下来的几节中，我们将发现您可以在 TypeScript 中使用的一些重要功能，您需要了解的关于 TypeScript 类型注释的所有内容，以及在现实世界的项目中使用 TypeScript 时需要考虑的事项。

> ### Visual Studio 代码非常适合使用 TypeScript！
> 
> 如果你想了解更多关于如何在 Visual Studio 代码中使用 TypeScript 进行良好设置的信息，我推荐你看一看本指南。

## 超酷的打字稿功能

除了类型注释，TypeScript 还通过 ESnext 特性和它自己的一些特性改进了 JavaScript。

> ### TypeScript 带给你很多 ESnext 的特性
> 
> 我们将在本部分看到的许多功能都是 ESnext 功能，它们是处于不同成熟度级别的提案。你可以在 [TC39 ECMA-262 GitHub 知识库](http://bit.ly/ecma262-gh)中找到更多关于目前正在考虑的所有提案的信息。
> 
> 当使用带有实验标志的巴别塔时，这些特性中的一些也是可用的。事实上，当您在 TypeScript 中使用这些功能时，您在 Microsoft 中有一个维护 TypeScript 的团队，这给了您很大的信心。

### 打字稿类

TypeScript 类附带了几个特性，这些特性提供了比 ES6 类更好的开发人员体验。第一个是**类成员**。

而不是像这样写你的类:

```
// ES6 class
class Gladiator {
  constructor(name, hitPoints){
    this.name = name;
    this.hitPoints = hitPoints;
  }
  toString(){
    return `${this.name} the gladiator`
  }
} 
```

您可以将类成员`name`和`hitPoints`提取到类的主体中，就像在静态类型语言中一样:

```
class Gladiator {
  name: string;
  hitPoints: number;

  constructor(name: string, hitPoints: number){
    this.name = name;
    this.hitPoints = hitPoints;
  }

  toString(){
    return `${this.name} the gladiator`
  }
} 
```

这可能有点冗长，因此 TypeScript 附带了另一个称为**参数属性**的特性，它允许您指定一个类成员并通过构造函数一次性初始化它。

使用*参数属性*的上述版本的等效版本如下:

```
class SleekGladiator {
  constructor(public name: string, 
              public hitPoints: number){}

  toString(){
    return `${this.name} the gladiator`
  }
} 
```

好多了，不是吗？类构造函数中的`public`关键字告诉 TypeScript】和`hitPoints`是可以通过构造函数初始化的类成员。

此外，`public`关键字提示了 TypeScript 给类带来的最后一个改进:**访问修饰符**。TypeScript 附带了四个访问修饰符，它们决定了如何访问类成员:

*   **readonly** :使成员只读。您必须在声明时或在构造函数中初始化它，之后就不能再更改了。
*   **私有**:使成员私有。它只能从类本身内部访问。
*   **protected** :使成员受到保护。它只能从类或派生类型中访问。
*   **公开**:将成员公开。任何人都可以访问它。在 JavaScript ES6 类实现之后，如果没有提供，那么`public`是类成员和方法的默认访问修饰符。

`readonly`修饰符让我们不必像前面几章那样定义一个`@readonly`装饰器。

一个人的名字一旦起了就不能更改，所以让我们把`Gladiator`的名字设为只读:

```
class FixedGladiator {

  constructor(readonly name: string,
              public hitPoints: number){}

  toString(){
    return `${this.name}, the gladiator`
  }

} 
```

现在，当我们创造一个新的角斗士，我们给他或她一个名字，它会被刻在石头上

```
const maximo = new FixedGladiator('Maximo', 5000);

maximo.name = "Aurelia";
// => [ts] Cannot assign to 'name' because it is 
//         a constant or a read-only property. 
```

这里需要注意的重要一点是，这些访问修饰符只适用于 TypeScript 世界。也就是说，当您编写 TypeScript 时，TypeScript 编译器将强制执行它们，但是当您的代码转换为 JavaScript 时，它们将被移除。

上面的`FixedGladiator`的转换版本产生了下面的 JavaScript:

```
var FixedGladiator = (function () {

  function FixedGladiator(name, hitPoints) {
    this.name = name;
    this.hitPoints = hitPoints;
  }

  FixedGladiator.prototype.toString = function () {
    return this.name + ", the gladiator";
  };

  return FixedGladiator;
}()); 
```

从上面的例子可以看出，没有任何机制可以确保`name`属性是只读的。

接下来让我们测试一下`private`访问修饰符。在前几章中，我们讨论了在 JavaScript 中实现隐私的不同方法:闭包和符号。使用 TypeScript，您可以通过使用`private`(和`protected`)访问修饰符来实现数据隐藏。

这是我们在第 6 章使用的例子。白塔召唤增强:ES6 职业的奇迹展示使用闭包隐藏数据:

```
class PrivateBarbarian {

  constructor(name){
    // private members
    let weapons = [];
    // public members
    this.name = name;
    this["character class"] = "barbarian";
    this.hp = 200;

    this.equipsWeapon = function (weapon){ 
      weapon.equipped = true;
      // the equipsWeapon method encloses the weapons variable
      weapons.push(weapon);
      console.log(`${this.name} grabs a ${weapon.name} ` + 
                  `from the cavern floor`);
    };
    this.toString = function(){
      if (weapons.length > 0) {
        return `${this.name} wields a ` + 
               `${weapons.find(w => w.equipped).name}`;
      } else return this.name
    };
  }

  talks(){ 
    console.log("I am " + this.name + " !!!");
  }

  saysHi(){ 
    console.log("Hi! I am " + this.name);
  }
}; 
```

在这个例子中，我们使用闭包来封闭`weapons`变量，这个变量对于所有的效果和目的都是私有的。如你所知，闭包的使用迫使我们将使用变量`weapons`的方法`equipsWeapon`和`toString`从类的主体移到构造函数的主体。

该类在 TypeScript 中的等效形式如下:

```
class PrivateBarbarian {
  // private members
  private weapons = [];

  // public members
  ["character class"] = "barbarian";
  hp = 200;

  constructor(public name: string) {}

  equipsWeapon(weapon) { 
    weapon.equipped = true;
    // the equipsWeapon method encloses the weapons variable
    this.weapons.push(weapon);
    console.log(`${this.name} grabs a ${weapon.name} ` + 
                `from the cavern floor`);
  }

  toString() {
    if (this.weapons.length > 0) {
    return `${this.name} wields a ` + 
            `${this.weapons.find(w => w.equipped).name}`;
    } else return this.name
  };

  talks(){ 
    console.log("I am " + this.name + " !!!");
  }

  saysHi(){ 
    console.log("Hi! I am " + this.name);
  }
}; 
```

如果你现在实例化一个不屈不挠的野蛮人，并试图访问`weapons`属性，你会看到以下错误:

```
const conan = new PrivateBarbarian("shy Conan");
// const privateWeapons = conan.weapons;
// => [ts] Property 'weapons' is private and 
//         only accessible within class 'PrivateBarbarian'. 
```

如果你回顾并比较这两种方法，我想你会同意我的观点，TypeScript 语法比 ES6 语法读起来更好。将所有方法放在类体内比将方法分成两部分更一致，也更容易理解。

另一方面，TypeScript `private`访问修饰符是一个 TypeScript 特性，当代码被转换为 JavaScript 时，它就消失了，也就是说，可以访问输出 JavaScript 的库消费者将能够访问这个类的`weapons`属性。这通常不会是一个问题，因为大多数情况下，您的整个开发团队都将使用 TypeScript，但是在某些情况下，这可能会有问题。例如，对于使用 TypeScript 创建库的库创建者来说，我认为这是一个问题，并使使用普通 JavaScript 的消费者可以访问它。

#### 为什么我写 ES6 类的时候会出现 TypeScript 错误？不是有效的 JavaScript 吗？

问得好！当您在选择的 TypeScript 编辑器中键入带有 ES6 `Barbarian`类的代码示例时，您会惊讶地发现`this.name`、`this.hp`和`this.equipsWeapon`声明会导致 TypeScript 编译器错误。*什么？*我认为 JavaScript 的每一部分都是有效的类型脚本，这是完全有效的 ES6 代码。发生了什么事？我一直生活在谎言中吗？

产生这些错误的原因是 TypeScript 具有不同的正确性级别:

*   在第一级中，TypeScript 编译器在应用类型注释之前检查代码的语法是否正确。如果是，那么它能够执行编译并发出正确的 JavaScript 代码(这是我们刚刚发现的关于 ES6 类的问题的情况)。
*   在第二个级别，TypeScript 编译器检查类型注释。根据 TypeScript 的类型系统，`PrivateBarbarian`没有任何属性`name`(属性是在类的主体中声明的)，因此它显示错误 *[ts]属性“name”在类型“private barbaral”*上不存在。
*   在通过编译器标志`--noImplicitAny`启用的第三级中，TypeScript 编译器将变得非常严格，并且不会假定非注释变量的类型是`any`。也就是说，它将要求所有变量、属性和方法都是类型化的。

因此，在我们的 ES6 示例中，TypeScript 将您的代码理解为有效的 ES6，并且能够将您的代码转换为 JavaScript，但是根据 TypeScript 的类型系统，您应该重构您的类，并将类成员移动到类体内。

### 枚举

TypeScript 的另一个重要特性是枚举。枚举是 C#和 Java 等静态类型语言中的一种常见数据类型，用于以强类型方式表示有限数量的事物。

想象你想要表达元素魔法的所有不同流派:火、水、气、土。当你创造不同的元素法术时，它们将属于这些流派中的一些，与其他流派的法术相比有优势也有劣势。例如，火球术看起来像这样:

```
const fireballSpell = {
  type: 'fire',
  damage: 30,
  cast(target){
    const actualDamage = target.inflictDamage(this.damage, 
                                              this.type);
    console.log(`A huge fireball springs from your ` +  
        `fingers and impacts ${target} (-${actualDamage}hp)`);
  }
}; 
```

`target.inflictDamage`将通过考虑目标对特定元素魔法的抗性或者它是否有保护法术来计算对目标造成的`actualDamage`。

这个例子的问题是，字符串不是非常有意的，也没有提供许多关于可用的元素魔法学校的信息。在上面的例子中，很容易出现拼写错误，把字符串`'fire'`拼错成其他的。

对前面方法的一个改进是使用一个对象来封装所有可用的选项:

```
const schoolsOfElementalMagic = {
  fire: 'fire',
  water: 'water',
  air: 'air',
  earth: 'earth'
}; 
```

现在我们可以重写之前的例子:

```
const fireballSpell = {
  type: schoolsOfElementalMagic.fire,
  damage: 30,
  cast(target){
    const actualDamage = target.inflictDamage(this.damage, 
                                              this.type);
    console.log(`A huge fireball springs from your ` +  
        `fingers and impacts ${target} (-${actualDamage}hp)`);
  }
}; 
```

厉害！这比我们之前的魔法线好多了。但是它仍然容易受到打字错误的影响，而且没有什么能阻止你在你的咒语中写下`type: 'banana'`。

这就是 TypeScript 枚举的用武之地。它们为您提供了一种静态和强类型的方式来表示有限的事物或状态的集合。一个`SchoolsOfMagic`枚举可以是这样的:

```
enum SchoolsOfMagic {
  Fire,
  Water,
  Air,
  Earth
} 
```

这个枚举允许我们指定一个表示`Spell`形状的接口。注意一个有效的`Spell`如何拥有一个`type`属性，其类型是我们刚刚创建的枚举`SchoolsOfMagic`:

```
// now we can define a Spell interface
interface Spell {
  type: SchoolsOfMagic,
  damage: number,
  cast(target: any);
} 
```

> ### 接口？
> 
> 接口是 TypeScript 中的另一个新特性。它们允许您定义任意类型，从而产生更多有意的代码并丰富您的开发人员体验。我们将在本章的后面学习更多关于接口的知识。

当我们现在定义一个新的拼写类型时，Script 将强制为该拼写提供的`type`是类型`SchoolsOfMagic`，不仅如此，当使用诸如 Visual Studio Code 之类的编辑器时，它将通过语句完成为我们提供所有可用的选项(`Fire`、`Water`、`Air`和`Earth`)。

```
const enumifiedFireballSpell: Spell = {
  type: SchoolsOfMagic.Fire,
  damage: 30,
  cast(target){
    const actualDamage = target.inflictDamage(this.damage, 
                                              this.type);
    console.log(`A huge fireball springs from your ` +  
        `fingers and impacts ${target} (-${actualDamage}hp)`);
  }
} 
```

如果我们键入除了`SchoolOfMagic`枚举之外的任何东西(例如，一个字符串), TypeScript 会立即用下面的错误消息警告我们:

```
// providing other than a SchoolsOfMagic enum would result in error:
// [ts] 
//   Type 
//  '{ type: string; damage: number; cast(target: any): void; }' 
//   is not assignable to type 'Spell'.
//   Types of property 'type' are incompatible.
//   Type 'string' is not assignable to type 'SchoolsOfMagic'. 
```

当转换成 JavaScript 时，枚举会产生下面的代码:

```
var SchoolsOfMagic;
(function (SchoolsOfMagic) {
    SchoolsOfMagic[SchoolsOfMagic["Fire"] = 0] = "Fire";
    SchoolsOfMagic[SchoolsOfMagic["Water"] = 1] = "Water";
    SchoolsOfMagic[SchoolsOfMagic["Air"] = 2] = "Air";
    SchoolsOfMagic[SchoolsOfMagic["Earth"] = 3] = "Earth";
})(SchoolsOfMagic || (SchoolsOfMagic = {})); 
```

乍一看，这可能有点令人生畏。但是让我们把它分解成更小的语句:

```
// Set 'Fire' property in SchoolsOfMagic to 0
SchoolsOfMagic["Fire"] = 0;

// it evaluates to 0 so that this:
SchoolsOfMagic[SchoolsOfMagic["Fire"] = 0] = "Fire";
// is equivalent to:
SchoolsOfMagic[0] = "Fire";
// which means set '0' property in SchoolsOfMagic to "Fire" 
```

因此，枚举用枚举名称表示数字和字符串之间的双向映射。就像您可以指定名称一样，您可以在声明枚举时选择数字:

```
// Start in 1 and increase numbers
enum SchoolsOfMagic {
  Fire=1,
  Water,
  Air,
  Earth
}

// Explicitly set all numbers
enum SchoolsOfMagic {
  Fire=2,
  Water=4,
  Air=6,
  Earth=8
}

// Computed enums
enum SchoolsOfMagic {
  Fire=1,
  Water=Fire*2,
  Air=2,
  Earth=Air*2
} 
```

每当我们不希望被编译的 JavaScript 包含枚举的引用时(例如，在一个受约束的环境中，我们希望运送更少的代码)，我们可以使用`const`枚举。以下枚举定义将不会被传输到 JavaScript:

```
const enum SchoolOfMagic {
  Fire,
  Water,
  Air,
  Earth
} 
```

相反，它将被内联，任何对`Fire`、`Water`、`Air`和`Earth`的引用都将被替换为一个数字。在这种情况下分别为 0、1、2、3。

### 还是更喜欢字符串？检查此字符串文字类型

如果您仍然喜欢普通字符串，TypeScript 能够基于一系列特定的有效字符串创建类型。我们魔法学校的对等物可能是这样的:

```
type SchoolsOfMagic = "fire" | "earth" | "air" | "water"; 
```

我们再次根据这种新类型来定义接口:

```
interface Spell {
  type: SchoolsOfMagic,
  damage: number,
  cast(target: any);
} 
```

我们准备好创造咒语了。使用除允许的字符串之外的任何内容都会导致翻译错误:

```
const FireballSpell: Spell = {
  type: "necromancy", 
  damage: 30,
  cast(target){
    const actualDamage = target.inflictDamage(this.damage, this.type);
    console.log(`A huge fireball springs from your ` +  
        `fingers and impacts ${target} (-${actualDamage}hp)`);
  }
}
// => [ts] 
//  Type '{ type: "necromancy"; damage: number; 
//          cast(target: any): void; }' 
//  is not assignable to type 'SpellII'.
//  Types of property 'type' are incompatible.
//  Type '"necromancy"' is not assignable to type 'SchoolsOfMagicII'. 
```

### 物体分散而静止

在**JavaScript-mancy:Getting Started**中我们看到了 ES6 带来的 **rest 参数**和**spread 算子**。

您可能还记得， *rest 参数*改善了开发者使用多参数声明函数的体验【^multiple].我们不再像在 ES6 之前那样使用`arguments`对象:

[^multiple]:喜欢 C#中的`params`。

```
function obliterate(){
  // Unfortunately arguments is not an array :O
  // so we need to convert it ourselves
  var victims = Array.prototype.slice.call(arguments, 
                              /* startFromIndex */ 0);

  victims.forEach(function(victim){
    console.log(victim + " wiped off of the face of the earth");
  });
  console.log('*Everything* has been obliterated, ' + 
              'oh great master of evil and deceit!');
} 
```

我们可以使用 rest 语法将所有传入的参数直接收集到一个数组`victims` :

```
function obliterate(...victims){
  victims.forEach(function(victim){
    console.log(`${victim} wiped out of the face of the earth`);
  });
  console.log('*Everything* has been obliterated, ' + 
              'oh great master of evil and deceit!');
} 
```

另一方面，*扩展操作符*以与*其余参数*相反的方式工作。spread 运算符不是获取可变数量的参数并将其打包到一个数组中，而是获取一个数组并将其扩展为复合项。

遵循这一原则，传播算子有许多用途 cases[^use-cases].比如串联数组:

[^use-cases]:回顾 JavaScript-mancy:更多用例入门！

```
let knownFoesLevel1 = ['rat', 'rabbit']
let newFoes = ['globin', 'ghoul'];
let knownFoesLevel2 = [...knownFoesLevel1, ...newFoes]; 
```

或者克隆他们:

```
let foes = ['globin', 'ghoul'];
let clonedFoes = [...foes]; 
```

**Object Spread and Rest** 为对象带来了数组中可用的相同类型的功能。

**对象传播操作符**的一个很好的用例是混合。在前面的章节中，我们使用了`Object.assign`来混合两个或更多不同物体的属性。例如，在这个`Wizard`工厂函数中，我们混合了向导属性和 mixinss，mixin 封装了一些行为，通过名字来识别一些东西，并施法:

```
function Wizard(element, mana, name, hp){
  let wizard = {element, 
                mana, 
                name, 
                hp};
  Object.assign(wizard, 
               canBeIdentifiedByName,
               canCastSpells);
  return wizard;
} 
```

我们可以使用对象展开重写上面的例子如下:

```
function Wizard(element, mana, name, hp){
  let wizard = {element, 
                mana, 
                name, 
                hp};

  // now we use object spread
  return {...wizard, 
          ...canBeIdentifiedByName,
          ...canCastSpells
         };
} 
```

对象扩展操作符本质上说:*获取`wizard`、`canBeIdentifiedByName`和`canCastSpells`的所有属性，并将它们放在同一个对象*中。如果有任何属性同名，则最后一个属性会胜出并覆盖第一个属性。

与对象扩散相反的是对象静止参数。它们的工作方式类似于 ES6 rest 参数，与 ES6 析构一起使用时特别有用。

如果你还记得，我们使用析构和 rest 参数从数组中提取元素:

```
let [first, second, ...rest] = ['dragon', 'chimera', 'harpy', 'medusa'];
console.log(first); // => dragon
console.log(second); // => chimera
console.log(rest); // => ['harpy', 'medusa'] 
```

使用对象扩展操作符，我们可以按照相同的模式从对象中提取和收集属性:

```
let {name, type, ...stats} = {
  name: 'Hammer of the Morning',
  type: 'two-handed war hammer',
  weight: '40 pounds',
  material: 'nephirium',
  state: 'well kept'
};
console.log(name); // => Hammer of Morning
console.log(type); // => two-handed war hammer
console.log(stats); 
// => {weight: '40 pounds', 
//     material: 'nephirium', 
//     state: 'well kept'} 
```

### 还有更多！

TypeScript 中有更多的特性扩展了 ES6，要么是通过目前处于提议阶段的 ESnext 特性的早期实现(如`async/await`或 decorators ),要么是通过全新的特性，如我们已经看到的与类和枚举相关的特性。

如果你有兴趣学习更多关于 TypeScript 的知识，那么我鼓励你看一看 [TypeScript 手册](http://bit.ly/ts-handbook)和[发行说明](http://bit.ly/ts-whats-new)，它们都提供了关于 TypeScript 为你准备了什么的详细信息。

## 在 TypeScript 中键入注释

类型注释是 TypeScript 的基本功能，并在 JavaScript 中提供了一个新层次的元编程:类型元编程。通过缩短反馈循环、编译时错误和 API 可发现性，类型注释使您能够为您和您的团队创建更好的开发人员体验。

TypeScript 中的类型注释不会停留在简单的基本类型上，如`string`或`number`。您可以指定数组的类型:

```
// An array of strings
let saddleBag: string[] = [];
saddleBag.push('20 silvers');
saddleBag.push('pair of socks');

saddleBag.push(666);
// => [ts] Argument of type '666' is not assignable 
//         to parameter of type 'string'. 
```

还有元组:

```
// A tuple of numbers
let position : [number, number];
position = [1, 1];
position = [2, 2];

// position = ['orange', 'delight'];
// => [ts] Type '[string, string]' is not 
//    assignable to type '[number, number]'.
//    Type 'string' is not assignable to type 'number'. 
```

功能:

```
// a predicate function that takes numbers and returns a boolean
let predicate: (...args: number[]) => boolean;
predicate = (a, b) => a > b
console.log(`1 greated than 2? ${predicate(1, 2)}`);
// => 1 greated than 2? false

predicate = (text:string) => text.toUpperCase();
// => [ts] Type '(text: string) => string' is not assignable 
//         to type '(...args: number[]) => boolean'.
//     Types of parameters 'text' and 'args' are incompatible.
//     Type 'number' is not assignable to type 'string'. 
```

甚至还有物体:

```
function frost(minion: {hitPoints: number}) {
  const damage = 10;
  console.log(`${minion} is covered in frozy icicles (- ${damage} hp)`);
  minion.hitPoints -= damage;
} 
```

`{hitPoints: number}`代表一个具有`number`类型的`hitPoints`属性的对象。我们可以对一个危险的敌人施放冰霜法术，这个敌人必须遵守规定的契约——拥有`hitPoints`属性:

```
const duck = {
  toString(){ return 'a duck';}, 
  hitPoints: 100
};

frost(duck);
// => a duck is covered in frozy icicles (-10hp) 
```

如果被冻结的对象不满足要求，TypeScript 会立即提醒我们:

```
const theAir = {
    toString(){ return 'air';}
};
frost(theAir);
// => [ts] Argument of type '{ toString(): string; }' 
//    is not assignable to parameter 
//      of type '{ hitPoints: number; }'.
// Property 'hitPoints' is missing in type '{ toString(): string; }'. 
```

注释对象的一个更好的方法是通过**接口**。

### 键入脚本接口

接口是可重用的，比直接的对象类型注释更简洁。`Minion`界面可以描述如下:

```
interface Minion {
    hitPoints: number;
} 
```

我们可以使用这个新接口来更新我们的`frost`函数:

```
function frost(minion: Minion){
  const damage = 10;
  console.log(`${minion} is covered in frozy icicles (-${damage} hp)`);
  minion.hitPoints -= damage;
} 
```

看起来更好，不是吗？关于**接口**的一个有趣的事实是，它们完全是一个类型脚本工件，其唯一的应用是在类型注释和类型脚本编译器的领域内。因此，**接口**没有被转换成 JavaScript。如果您转存上面的代码，您会惊讶地发现生成的 JavaScript 没有提到`Minion` :

```
function frost(minion) {
    var damage = 10;
    console.log(minion + " is covered in frozy icicles (-" + damage + " hp)");
    minion.hitPoints -= damage;
} 
```

这表明了一个事实，即接口是一种向代码库添加类型注释的轻量级方法，在开发过程中获得好处，而不会对浏览器上运行的代码产生任何负面影响。

让我们用不同类型的参数来测试我们新的`frost`函数和`Minion`接口，看看它们的表现如何。带上我们之前例子中的`duck`！

```
// const duck = {
//  toString(){ return 'duck';}, 
//  hitPoints: 100
//  };
frosty(duck);
// => duck is covered in frozy icicles (-10hp) 
```

这似乎非常有效。如果我们尝试使用一个代表`Tower`并有`hitPoints`和`defense`属性的类，它似乎也能工作:

```
class Tower {
    constructor(public hitPoints=500, public defense=100){}
    toString(){ return 'a mighty tower';}
}
const tower = new Tower();

frosty(tower);
// => a mighty tower is covered in frozy icicles (-10hp) 
```

具有`hitPoints`属性的简单对象文字也是如此:

```
frosty({hitPoints: 100});
// => [object Object] is covered in frozy icicles (-10hp) 
```

然而，如果我们使用一个除了`hitPoints`之外还有其他属性的对象文字，编译器会抛出一个错误:

```
frosty({hitPoints: 120, toString(){ return 'a bat';}})
// => doesn't compile
// => Argument of type '{ hitPoints: number; toString(): string; }' 
//    is not assignable to parameter of type 'Minion'.
//  Object literal may only specify known properties, 
//  and 'toString' does not exist in type 'Minion'. 
```

错误消息似乎很有帮助。它说对于对象文字，我可以只指定已知的属性，并且`toString`不存在于`Minion`中。那么，如果我将对象文字存储在变量`aBat`中会发生什么呢？

```
let aBat = {
    hitPoints: 120, 
    toString(){ return 'a bat';}
};
frosty(aBat);
// => a bat is covered in frozy icicles (-10hp) 
```

有用！有趣的是，从这些实验来看，似乎 TypeScript 会将`Minion`视为满足接口指定的契约的任何对象，也就是说，具有类型为`number`的`hitPoints`属性。

然而，看起来当你使用一个 object literal TypeScript 时，它有一组更严格的规则，并且它期望一个与`Minion`接口完全匹配的参数。那么`Minion`到底是什么呢？当 TypeScript 遇到任意对象时，如何确定它是否是一个`Minion`？

**它遵循结构类型化的规则**。

### 结构类型化

**结构类型化是一种类型系统，其中类型兼容性和等价性是由被比较的类型的结构决定的，也就是它们的属性**。

例如，在结构类型化之后，以下所有类型都是等价的，因为它们具有相同的结构(相同的属性):

```
// an interface
interface Wizard {
  hitPoints: number;
  toString(): string;
  castSpell(spell:any, targets: any[]);
}

// an object literal
const bard = {
  hitPoints: 120,
  toString() { return 'a bard';},
  castSpell(spell: any, ...targets: any[]){
    console.log(`${this} cast ${spell} on ${targets}`);
    spell.cast(targets);
  }
}

// a class
class MagicCreature {
  constructor(public hitPoints: number){}
  toString(){ return "magic creature";}
  castSpell(spell: any, ...targets: any[]){
    console.log(`${this} cast ${spell} on ${targets}`);
    spell.cast(targets);
  }
} 
```

您可以使用下面的代码片段来验证这一点:

```
let wizard: Wizard = bard;
let anotherWizard: Wizard = new MagicCreature(120); 
```

相比之下，像 C#或 Java 这样的语言有我们所谓的**名义类型系统**。在名义类型系统中，类型等价基于类型的名称和显式声明，其中一个`MagicCreature`是一个`Wizard`，当且仅当该类显式实现了接口。

对于 JavaScript 开发人员来说，结构化类型非常棒，因为它的行为非常像鸭子类型，而鸭子类型是 JavaScript 面向对象编程模型的核心特性。对于 C#/Java 开发人员来说，它仍然很棒，因为他们可以享受 C#/Java 的特性，如接口、类和编译时反馈，但具有更高的自由度和灵活性。

仍然有一个用例不符合我们刚刚描述的结构化类型规则。如果您还记得上一节中的例子，对象文字似乎是结构化类型规则的一个例外:

```
frosty({hitPoints: 120, toString(){ return 'a bat';}})
// => doesn't compile
// => Argument of type '{ hitPoints: number; toString(): string; }' 
//    is not assignable to parameter of type 'Minion'.
//  Object literal may only specify known properties, 
//  and 'toString' does not exist in type 'Minion'. 
```

为什么会这样？这是为了防止开发人员的错误。

TypeScript 编译器的设计者认为像这样使用对象文字很容易出错(比如打字错误，想象一下写`hitPoitns`而不是`hitPoints`)。这就是为什么当以这种方式使用对象文字时，TypeScript 编译器会格外小心，并执行**超额属性检查**。在这种特殊模式下，TypeScript 会格外小心，会标记出函数`frosty`不期望的任何附加属性。希望能帮助你避免不必要的错误。

如果您确信您的代码是正确的，您可以通过显式地将对象文字转换为所需的类型或者将它存储在一个变量中来快速告诉 TypeScript 编译器没有问题，正如我们前面看到的:

```
frosty({hitPoints: 120, toString(){ return 'a bat';}} as Minion);
// => a bat is covered in frozy icicles (-10hp) 
```

注意到`as Minion`了吗？这是我们可以告诉 TypeScript 对象文字是类型`Minion`的一种方式。这是另一种方式:

```
frosty((<Minion>{hitPoints: 120, toString(){ return 'a bat';}}));
// => a bat is covered in frozy icicles (-10hp) 
```

### TypeScript 帮助你进行类型标注

TypeScript 的另一个有趣的方面是它的**类型推断**功能。编写类型注释不仅会导致更冗长的代码，而且还需要做额外的工作。为了最大限度地减少注释代码所需的工作量，TypeScript 将尽最大努力从代码本身推断出所使用的类型。例如:

```
const aNumber = 1;
const anotherNumber = 2 * aNumber;

// aNumber: number
// anotherNumber:number 
```

在这个代码示例中，我们没有指定任何类型。无论如何，TypeScript 毫无疑问地知道`aNumber`变量属于`number`类型，并且通过评估`anotherNumber`它知道它也属于`number`类型。同样，我们可以这样写:

```
const double = (n: number) => 2*n;
// double: (n:number) => number 
```

并且 TypeScript 会知道函数`double`返回一个数字。

### 从接口到类

到目前为止，我们已经看到了如何以基本类型、数组、对象文字和接口的形式使用类型注释。所有这些都是特定于类型脚本的 artifacs，当您将类型脚本代码转换为 JavaScript 时，它们就会消失。我们还看到了 TypeScript 如何试图从代码中推断类型，这样您就不需要花费不必要的时间来注释代码。

然后我们上课。类是一个 ES6/TypeScript 特性，我们可以用它来描述一个域模型实体的结构和行为，它包含一个特定的实现，也可以作为一个类型注释。

在前面的章节中，我们定义了一个接口`Minion`，它代表了一个具有`hitPoints`属性的事物。我们可以对一个类做同样的事情:

```
class ClassyMinion {
  constructor(public hitPoints: number) {}
} 
```

并创建一个新的`classyFrost`函数来使用这个类作为参数类型:

```
function classyFrost(minion: ClassyMinion){
  const damage = 10;
  console.log(`${minion} is covered in frozy icicles (-${damage} hp)`)
  minion.hitPoints -= damage;
} 
```

我们可以在新的`ClassyMinion`类中使用这个函数，甚至可以在之前的`aBat`和`bard`变量中使用这个函数，因为按照结构类型化的规则，所有这些类型都是等价的:

```
classyFrosty(new ClassyMinion());
// => a classy minion is covered in frozy icicles (-10hp)
classyFrosty(aBat);
// => a bat is covered in frozy icicles (-10hp)
classyFrosty(bard);
// => a bard is covered in frozy icicles (-10hp) 
```

通常我们会让`class`实现期望的`interface`。例如:

```
class ClassyMinion implements Minion {
  constructor(public hitPoints: number) {}
} 
```

从结构类型的角度来看，这不会改变这个类，但它确实改善了我们的开发者体验。添加`implements Minion`有助于 TypeScript 告诉我们是否正确地实现了接口，或者是否缺少任何属性或方法。在一个只有一个属性的类中，这听起来可能没什么，但是随着我们的类变得越来越丰富，这变得越来越有用。

一般来说，使用`class`和使用`interface`的区别在于，当转换成 JavaScript 时，这个类将产生一个真正的 JavaScript 类(尽管它可能是一个构造函数/原型对，这取决于你所针对的 JavaScript 版本)。

例如，在我们当前的设置中，上面的类将产生下面的 JavaScript:

```
var ClassyMinion = (function () {
    function ClassyMinion(hitPoints) {
        if (hitPoints === void 0) { hitPoints = 100; }
        this.hitPoints = hitPoints;
    }
    ClassyMinion.prototype.toString = function () {
        return 'a classy minion';
    };
    return ClassyMinion;
}()); 
```

这是有意义的，因为不像`interface`那样，它是一个只在类型注释世界中使用的虚构工件，一个`class`是运行你的程序所必需的。

你什么时候使用接口，什么时候使用类？让我们回顾一下这两种结构的作用和行为方式:

*   **界面**:描述形状和行为。在传输过程中被移除了。
*   **类**:描述形状和行为。提供了一个特定的实现。它被转换成了 JavaScript

所以接口和类都描述了类型的形状和行为。另外，类提供了一个具体的实现。

在 C#或 Java 的世界里，遵循**依赖倒置**原则，我们建议在描述类型时优先使用接口而不是类。这将为我们的程序提供很大的灵活性和可扩展性，因为我们将实现一个松散耦合的系统，其中具体的类型彼此不知道。然后，我们就可以注入不同的具体类型来完成接口定义的契约。这在像 C#或 Java 这样的静态类型语言中是必须的，因为它们使用一个名义类型系统。但是 TypeScript 呢？

正如我们前面提到的，TypeScript 使用结构化类型系统，当类型具有相同的结构(即相同的成员)时，它们是等效的。有鉴于此，你可以说，我们是否使用接口或类来表示类型并不重要。如果接口、类或对象文字共享相同的结构，它们将被同等对待，那么为什么我们需要在 TypeScript 中使用接口呢？当您考虑使用接口还是类时，可以遵循以下一些准则:

1.  单一责任是降低程序复杂性的重要经验法则。将单一责任应用于接口与类的两难境地，我们可以将接口用于类型，将类用于实现。接口提供了一种非常简洁的方式来表示类型的形状，而类混合了形状和实现，这使得很难通过查看类来确定类型的形状。
2.  给你比上课更大的灵活性。因为一个类包含一个特定的实现，就其本质而言，它比接口更严格。使用接口，我们可以捕获细粒度的细节或类之间常见的行为。
3.  是一种轻量级的方式，为应用程序可能不熟悉的数据提供类型信息，比如来自 web 服务的数据
4.  对于没有附加行为的类型，即仅仅是数据的类型，您可以直接使用类。在这种情况下使用接口通常是多余的，也是不必要的。使用类将通过构造函数简化对象创建。

因此，一般来说，我们遵循的关于静态类型语言(如 C#和 Java)中的接口的指导原则也适用于 TypeScript。更喜欢用接口来描述类型，用类来实现特定的实现。如果类型只是没有行为的数据，你可以考虑单独使用一个类。

### 高级类型注释

除了我们到目前为止所看到的，TypeScript 提供了更多的机制来在你的程序中表达更复杂的类型。其思想是，无论您使用哪种 JavaScript 结构或模式，您都应该能够通过类型注释来表达其类型，并为您和团队中的其他开发人员提供有用的类型信息。

这些高级类型批注的一些示例如下:

*   无商标消费品
*   交集和并集类型
*   防护类型
*   可空类型
*   键入别名
*   字符串文字类型

让我们来看看它们中的每一个，为什么需要它们，以及如何使用它们。

#### 仿制药

泛型是 C#和 Java 等静态类型编程语言中使用的一种常见技术，用于将数据结构或算法的应用推广到多种类型。

例如，不同类型的`NumberArray`、`StringArray`、`ObjectArray`等不使用单独的`Array`实现:

```
interface NumberArray {
  push(n: number);
  pop(): number;

  // etc
}

interface StringArray {
  push(s: string);
  pop(): string;

  // etc
}

// etc... 
```

我们使用泛型来描述任意类型的`Array``T`:

```
// note that `Array<T>` is already a built-in type in TypeScript
interface Array<T>{
  push(s: T);
  pop(): T;

  // etc
} 
```

我们现在可以通过为`T` :
选择一个类型来重用这个单一的类型定义

```
let numbers: Array<number>;
let characters: Array<string>;
// and so on... 
```

就像我们在接口中使用泛型一样，我们也可以在类中使用它们:

```
class Cell<T> {
  private prisoner: T;

  inprison(prisoner: T) { 
    this.prisoner = item; 
  }

  free(): T { 
    const prisoner = this.prisoner; 
    this.prisoner = undefined;
    return prisoner;
  }
} 
```

最后，您可以将类型`T`限制为类型的一个子集。例如，假设一个特定的函数只在`Minion`的上下文中有意义。你可以写:

```
interface ConstrainedCell<T extends Minion>{
  inprison(prisoner: T);
  free(): T;
} 
```

现在这将是一个完全可用的盒子:

```
let box: ConstrainedCell<MagicCreature>; 
```

但这不会因为类型`T`和`Minion`接口不匹配:

```
let box: ConstrainedCell<{name: string}>;
// => [ts] Type '{ name: string; }' 
//    does not satisfy the constraint 'Minion'.
//    Property 'hitPoints' is missing in type '{ name: string; }'. 
```

#### 交集和并集类型

我们已经看到了基本类型、接口、类、泛型，以及许多提供类型信息的不同方式，尽管这些方式可能很灵活，但仍然有一个用例是他们很难涵盖的: **Mixins** 。

当使用混合时，产生的对象是其他不同对象的混合。该结果对象的类型本身不是已知类型，而是现有类型的组合。

例如，让我们回到之前的向导示例:

```
function Wizard(element, mana, name, hp){
  let wizard = {element, 
                mana, 
                name, 
                hp};

  // now we use object spread
  return {...wizard, 
          ...canBeIdentifiedByName,
          ...canCastSpells
         };
} 
```

我们可以将其分解成单独的元素:

```
interface WizardProps{
  element: string;
  mana: number;
  name: string;
  hp: number;
}

interface NameMixin {
  toString(): string;
}

interface SpellMixin {
  castsSpell(spell:Spell, target: Minion);
} 
```

我们如何定义由`WizardProps`、`NameMixin`和`SpellMixin`组合而成的`Wizard`类型？我们使用**交集类型**。交集类型允许我们定义其他类型组合的类型。例如，我们可以使用下面的类型注释来表示我们的`Wizard`:

```
WizardProps & NameMixin & SpellMixin 
```

我们可以把它作为工厂函数的返回类型:

```
let canBeIdentifiedByName: NameMixin = {
  toString(){ return this.name; }
};

let canCastSpells: SpellMixin = {
  castsSpell(spell:Spell, target:Minion){
    // cast spell
  }
}

function WizardIntersection(element: string, mana: number, 
                            name : string, hp: number): 
         WizardProps & NameMixin & SpellMixin {
  let wizard: WizardProps = {element, 
                mana, 
                name, 
                hp};

  // now we use object spread
  return {...wizard, 
          ...canBeIdentifiedByNameMixin,
          ...canCastSpellsMixin
         };
}

const merlin = WizardIntersection('spirit', 200, 'Merlin', 200);
// merlin.steal(conan);
// => [ts] Property 'steal' does not exist 
//    on type 'WizardProps & NameMixin & SpellMixin'. 
```

以同样的方式，我们有一个交集类型，导致一个类型是其他类型的组合，我们也有能力使一个类型可以是一系列类型中的任何一个，也就是说，要么是`string`或`number`或其他类型。我们称这些类型为**联合类型**。当您有可能接受不同类型参数的重载函数或方法时，通常会使用它们。

看看下面这个养出一支骷髅军的函数:

```
function raiseSkeleton(numberOrCreature){
  if (typeof numberOrCreature === "number"){
    raiseSkeletonsInNumber(numberOrCreature);
  } else if (typeof numberOrCreature === "string") {
    raiseSkeletonCreature(numberOrCreature);
  } else {
    console.log('raise a skeleton');
  }

  function raiseSkeletonsInNumber(n){
    console.log('raise ' + n + ' skeletons');
  }
  function raiseSkeletonCreature(creature){
    console.log('raise a skeleton ' + creature);
  };
} 
```

根据`numberOrCreature`的类型，上面的功能可以召唤骨骼或骨骼生物:

```
raiseSkeleton(22);
// => raise 22 skeletons

raiseSkeleton('dragon');
// => raise a skeleton dragon 
```

我们可以使用联合类型
给`raiseSkeletonTS`函数添加一些类型脚本的优点

```
function raiseSkeletonTS(numberOrCreature: number | string){
  if (typeof numberOrCreature === "number"){
    raiseSkeletonsInNumber(numberOrCreature);
  } else if (typeof numberOrCreature === "string") {
    raiseSkeletonCreature(numberOrCreature);
  } else {
    console.log('raise a skeleton');
  }

  function raiseSkeletonsInNumber(n: number){
    console.log('raise ' + n + ' skeletons');
  }
  function raiseSkeletonCreature(creature: string){
    console.log('raise a skeleton ' + creature);
  };
} 
```

`number | string`是一个联合类型，允许`numberOrCreature`成为`number`或`string`类型。如果我们错误地使用了其他东西，TypeScript 会支持我们:

```
raiseSkeletonTS(['kowabunga'])
// => [ts] Argument of type 'string[]' is not assignable 
//         to parameter of type 'string | number'.
// Type 'string[]' is not assignable to type 'number'. 
```

#### 型卫士

联合类型在函数体内引发了一种特殊情况。如果`numberOrCreature`可以是数字，也可以是字符串，TypeScript 怎么知道支持哪些方法？数字方法和字符串方法差别很大，那么什么是允许的呢？

当 TypeScript 遇到上述函数中的联合类型时，默认情况下，只允许您使用所有包含的类型中都可用的方法和属性。只有当您进行显式转换或包含类型保护时，TypeScript 才能确定所使用的类型并为您提供帮助。幸运的是，TypeScript 将识别常见 JavaScript 模式的类型保护，就像我们在前面的例子中使用的`typeof`。在执行类型保护`if (typeof numberOrCreature === "number")`之后，TypeScript 将确定无论你在 if 块中执行哪段代码，`numberOrCreature`都将是类型`number`。

#### 键入别名

另一个非常适合交集和并集类型的有用机制是类型别名。类型别名允许您提供任意名称(别名)来引用其他类型。厌倦了写这种交集类型？

```
WizardProps & NameMixin & SpellMixin 
```

您可以创建一个别名`Wizard`并使用它来代替:

```
type Wizard = WizardProps & NameMixin & SpellMixin; 
```

这个别名将允许你从先前的例子
中改进*向导*工厂

```
function WizardAlias(element: string, mana: number, 
                name : string, hp: number): Wizard {
  let wizard: WizardProps = {element, 
                mana, 
                name, 
                hp};

  // now we use object spread
  return {...wizard, 
          ...canBeIdentifiedByNameMixin,
          ...canCastSpellsMixin
         };
} 
```

#### 更多类型注释！

尽管我试图在本书的最后一章中全面介绍 TypeScript，但是除非我写了一本完整的关于 TypeScript 的书，否则我还无法介绍更多的特性和有趣的东西。

如果你有兴趣了解更多关于打字稿类型注释的酷东西，那么让我在[打字稿手册](http://bit.ly/ts-handbook)和[发行说明](http://bit.ly/ts-whats-new)中再次强调。

## 在现实世界应用中使用 TypeScript

因此，TypeScript 很棒，它在 ES6 的基础上为您提供了许多很棒的新特性，并通过类型注释提供了令人惊叹的开发人员体验，但是您如何开始在现实世界的应用程序中使用它呢？

好消息是，您很少需要从头开始创建 TypeScript 设置。最流行的前端框架都内置了对 TypeScript 的支持。例如，TypeScript 是 Angular 选择的主要语言，使用 Angular 和 TypeScript 启动一个新项目就像使用 Angular cli 并键入:
一样简单

```
$ ng new my-new-app 
```

同样，使用 React 和 *Create React App* 工具(也称为 CRA)用 TypeScript 启动 React 项目只需要 typing[^react-starter]:

```
$ create-react-app my-new-app --scripts-version=react-scripts-ts 
```

【http://bit.ly/ts-react-starter】这个命令使用了 TypeScript React 在后台启动

如果你使用以上任何一个选项，你就可以开始了。无论是哪种情况，都会为你启动一个新的应用程序，你就可以开始用 TypeScript 构建你的 Angular 或 React 应用程序了。

另一方面，如果出于某种原因，你需要从头开始，你会发现有一些[类型脚本插件](http://bit.ly/ts-plugins)，用于最常见的任务管理器或模块捆绑器，如 grunt、gulp 或 webpack。在将 TypeScript 集成到您的工具链中时，为了配置 TypeScript 编译器，您可能需要采取一个额外的步骤:设置您的`tsconfig`文件。

### `tsconfig.json`文件

`tsconfig.json`文件包含项目的 TypeScript 配置。它告诉 TypeScript 编译器编译项目所需的所有细节，例如:

*   要传输哪些文件
*   要忽略哪些文件
*   使用哪个版本的 JavaScript 作为翻译的目标
*   在输出 JavaScript 中使用哪个模块系统
*   编译器应该有多严格。它应该允许隐式 any 吗？它应该执行严格的空值检查吗？
*   要加载哪些第三方库类型

如果不指定部分信息，TypeScript 编译器会尽力而为。例如，不指定任何要传输的文件将提示 TypeScript 编译器传输项目文件夹中的所有 TypeScript 文件(`*.ts`)。不指定任何第三方类型将导致 TypeScript 编译器在您的项目中查找类型定义文件(例如在`./node_modules/@types`中)。

这是 TypeScript 文档中的一个例子`tsconfig.json`，可以给你一个思路:

```
{  "compilerOptions":  {  "module":  "system",  "noImplicitAny":  true,  "removeComments":  true,  "preserveConstEnums":  true,  "outFile":  "../../built/local/tsc.js",  "sourceMap":  true  },  "include":  [  "src/**/*"  ],  "exclude":  [  "node_modules",  "**/*.spec.ts"  ]  } 
```

关于所有可用选项的完整参考，请看一下 [TypeScript 文档](http://bit.ly/tsc-options)。

> ### 在本章的例子中，我们没有使用 tsconfig。怎么会这样
> 
> TypeScript 编译器`tsc`有两种不同的操作模式:有或没有输入文件。当您在执行`tsc`时没有指定输入文件时，TypeScript 编译器将试图找到一个可用的`tsconfig.json`文件及其配置。
> 当您指定输入文件时，TypeScript 编译器将忽略`tsconfig.json`。这就是为什么在前面的章节中，我们在运行`tsc hello-wizard.ts`时不需要定义一个`tsconfig.json`文件。

### TypeScript 和第三方库

从 TypeScript 2.0 开始，为第三方库安装类型声明就像通过`npm`安装任何其他库一样简单。

假设您想要利用 [ramda.js](http://ramdajs.com/) 这是一个包含有用的实用函数的库，具有很强的函数式编程味道，我们将在 JavaScript-mancy 的函数式编程书籍中深入了解这一点。

您可以使用 npm:
将该库添加到您的 TypeScript 项目中

```
# create package.json
$ npm init 

# install ramda and save dependency
$ npm install --save ramda 
```

您可以使用`@types/<name-of-library-in-npm>` :
来安装该库的类型声明

```
$ npm install --save-dev @types/ramda 
```

现在，当您开始在 Visual Studio 代码或您选择的编辑器中处理您的项目时，您应该在使用 ramda.js 时获得完整的类型支持。尝试编写下面的代码片段，并验证 TypeScript 如何帮助您:

```
import { add } from 'ramda';

const add5 = add(5);

console.log(`5 + 5: ${add5(5)}`);
console.log(`5 + 10: ${add5(1)}`); 
```

所有这些类型定义都来自于[明确类型化的](http://definitelytyped.org/)项目，并按照惯例定期推送到 npm 的`@types/`前缀下。如果你找不到某个特定库的类型声明，使用[类型搜索](http://bit.ly/ts-search) web 应用程序来找到它(例如，你可以尝试 stamps 章节的`stampit`)。

## 总结性的

那是打字稿！这是书中最长的一章，但我希望它足够有趣，能让你一直读到最后。让我们快速回顾一下，这样你会得到一个快速提醒，帮助你记住你刚刚学到的所有打字稿的精彩之处。

TypeScript 是 JavaScript 的超集，包括许多 ESnext 特性和类型注释。到目前为止，TypeScript 的定义特性是它对类型的使用。类型批注允许您提供有关代码的附加元数据，TypeScript 编译器可以使用这些元数据，以代码冗长为代价为您和您的团队提供更好的开发人员体验。

TypeScript 是 ES6 的超集，并通过大量的 ESnext 改进和 TypeScript 特定功能扩展了它的功能。我们看到了几个 ESnext 特性，比如类成员和新的对象 spread 和 rest 操作符。我们还发现了 TypeScript 如何用参数属性和属性访问器增强类，并带来了一种新的枚举类型，允许您编写更多有意的代码。

类型注释是 TypeScript 的基本功能。TypeScript 用新的语法和语义扩展了 JavaScript，允许您提供关于应用程序类型的丰富信息。除了能够表达基本类型之外，TypeScript 还引入了接口、泛型、交集和联合类型、别名、类型保护等...所有这些机制都允许您进行一种新型的元编程，通过类型注释来改善您的开发体验。添加类型注释仍然有点令人生畏，而且工作量很大，为了尽量减少这种情况，TypeScript 试图从代码中推断尽可能多的类型。

本着 JavaScript 和 duck-typing 的精神，TypeScript 有一个结构化的类型系统。这意味着，如果类型共享相同的结构，也就是说，如果它们具有相同的属性，它们将是等效的。这与 C#或 Java 中使用的名义类型系统相反，在名义类型系统中，类型等价是通过显式实现类型来确定的。结构类型非常好，因为它给了你很大的灵活性，同时也提供了很好的编译时错误检测和改进的工具。

在前端开发领域，我们看到越来越多的人采用 TypeScript，特别是，它已经成为 Angular 开发的核心语言。而且在大部分常见的前端框架、ide、tex-editor、前端构建工具中也有。通过类型定义和 DefinitelyTyped 项目，它在第三方库中也得到很好的支持，为库安装类型定义就像做一个`npm install`一样简单。

从个人角度来看，我最喜欢来自 C#世界的 JavaScript 的一点是它的简洁，没有仪式和不必要的工件。突然间，我不再需要写`PurchaseOrder purchaseOrder`或`Employee employee`了，员工就是`employee`、*时期*。我不需要编写看似无限量的样板代码来使我的应用程序灵活和可扩展，也不需要与语言斗争来使它屈从于我的意愿，事情就是这样。当我看到 TypeScript 的发布时，我担心 JavaScript 会失去它的灵魂，变成一种像 C#或 Java 一样死板的语言。在体验了 TypeScript 开发 Angular 应用程序、它的可选类型、出色的开发人员体验以及最重要的是它具有结构化类型这一事实之后，我充满希望。在接下来的几个月和几年里关注它的发展会很有趣。很可能我们所有人最终都会以写打字稿为生。

> ```
> mooleen.says('You shall only use types!?...');
> 
> bandalf.says("I've got my magic back... " + 
>   "but for some reason it won't... work");
> 
> mooleen.says("I, too, can feel the bond with the " +
>   "currents of magic again");
> 
> randalf.says("The Order of the Red Moon...");
> 
> red.says("There are our weapons! Under the obelisk!");
> 
> /*
> The group makes a precarious circle beside the obelisk as
> the hordes of lizard-like beast surround them.
> */
> 
> randalf.says("types... Yes! " + 
>   "Now I remember, The Last Stand and the Sacred Order. " +
>   "Their story lies between history and legend. " +
>   "It is said that they cultivated an obscure " +
>   "flavor of JavaScriptmancy. The legends say that " +
>   "they expanded it and enriched it with types...");
> 
> bandalf.says("Excellent. And what does that mean?");
> 
> rat.says("It means we're dead");
> red.says("A glorious death!");
> 
> randalf.says("Well they were a very guarded Order " +
>   "and they were exterminated to the last woman " + 
>   "in The Last Stand or so the story says..." + 
>   "In the deep jungles of Azons.");
> 
> mooleen.whispers("Azons...");
> 
> /* 
>   The sisters surround her on the battlements,
>   all wearing the black of the order in full armor.
>   The fort has an excellent view of the thick,
>   beautiful jungle below and of the unending hosts
>   of lizardmen surrounding them.
>   The Grand Commander shouts: 'To Arms sisters!'
>   'For one last time!'
> */
> 
> mooleen.says("Types... Types... Types!");
> mooleen.says("I remember..."); 
> ```

## 习题

> ### 实验 JavaScriptmancer！
> 
> 您可以从 [GitHub](https://github.com/vintharas/javascriptmancy-code-samples) 下载源代码，尝试这些练习和一些可能的解决方案。
> 
> ### 赚点时间！一堵冰墙！
> 
> 野兽们正在快速靠近，通过在它们和群体之间竖起一道冰墙来获得一些喘息的机会。这堵墙至少要有`100`英尺高、`7`英尺深、`700`英尺长，才能围住这群人。
> 
> 该墙应满足以下代码片段:
> 
> ```
> const iceWall = new Wall(MagicElement.Ice, {
>                           height: 100,
>                           depth: 7, 
>                           length: 700});
> 
> console.log(iceWall.toString());
> // => A wall of frozen ice. It appears to be about 100 feet high
> //    and extends for what looks like 700 feet.
> 
> iceWall.element = MagicElement.Fire;
> // => [ts] Cannot assign to 'element' because it is 
> //         a constant or a read-only property.
> iceWall.wallOptions.height = 100;
> // => [ts] Cannot assign to 'height' because it is 
> //         a constant or a read-only property. 
> ```
> 
> 提示:您可以使用一个 enum 来表示`MagicElement`，使用一个接口来表示`WallSpecifications`，使用一个类来表示`Wall`本身。记得加类型标注！
> 
> ### 解
> 
> ```
>  enum MagicElement {
>   Fire = "fire",
>   Water = "water",
>   Earth = "earth",
>   Air = "windy air",
>   Stone = "hard stone",
>   Ice = "frozen ice"
> }
> 
> interface WallSpecs{
>   readonly height: number,
>   readonly depth: number,
>   readonly length: number
> }
> 
> class Wall {
>   constructor(readonly element: MagicElement, 
>               readonly specs: WallSpecs){ }
> 
>   toString(){
>     return `A wall of ${this.element}. It appears to be about ` +
>       `${this.specs.height} feet high and extends for what ` +
>       `looks like ${this.specs.length} feet.`;
>   }
> }
> 
> const iceWall = new Wall(MagicElement.Ice, {
>                           height: 100,
>                           depth: 7, 
>                           length: 700});
> 
> console.log(iceWall.toString());
> // => A wall of frozen ice. It appears to be about 100 feet high
> //    and extends for what looks like 700 feet long.
> 
> // iceWall.element = MagicElement.Fire;
> // => [ts] Cannot assign to 'element' because it is 
> //         a constant or a read-only property.
> // iceWall.wallOptions.height = 100;
> // => [ts] Cannot assign to 'height' because it is 
> //         a constant or a read-only property.
> 
> world.randalf.gapes()
> // => Randalf gapes
> 
> world.randalf.says('How?');
> world.mooleen.says('I just remembered...');
> 
> world.randalf.says('Remember?');
> world.randalf.says("You look very young for being millennia old");
> 
> world.mooleen.shrugs();
> // => Moleen shrugs
> world.mooleen.says("Brace yourselves... they're coming " + 
>   "beware if they open their jaws and seem to catch breath " +
>   "they breathe fire"); 
> ```
> 
> ### 冻结蜥蜴！
> 
> 你赢得了一些时间。现在你可以利用这个喘息的机会来观察蜥蜴，适当地模拟它们，并制作一个将它们送到蜥蜴冰冻地狱的`frost`咒语。
> 
> 这是你可以观察到的:
> 
> ```
> giantLizard.jumps();
> // => The giant lizard gathers strength in its 
> //    4 limbs and takes a leap through the air
> giantLizard.attacks(red);
> // => The giant lizard attacks Red with great fury
> giantLizard.breathesFire(red);
> // => The giant lizard opens his jaws unnaturally wide
> //    takes a breath and breathes a torrent of flames
> //    towards Red
> giantLizard.takeDamage(Damage.Physical, 20);
> // => The giant lizard has extremely hard scales
> //    that protect it from physical attacks (Damage 50%)
> //    You damage the giant lizard (-10hp)
> giantLizard.takeDamage(Damage.Cold, 20);
> // => The giant lizard is very sensitive to cold.
> //    It wails and screams. (Damage 200%)
> //    You damage the giant lizard (-40hp) 
> ```
> 
> 创建一个符合以下代码片段的`frost`咒语:
> 
> ```
> frost(giantLizard, /* mana */ 10);
> // => The air surrounding the target starts quickly forming a
> //    frozen halo as the water particles start congealing.
> //    All of the sudden it explodes into freezing ice crystals
> //    around the giant lizard.
> //    The giant lizard is very sensitive to cold.
> //    It wails and screams. (Damage 200%)
> //    You damage the giant lizard (-2000hp) 
> ```
> 
> 提示:使用上面的观察创建一个接口，并在您的`frost`函数中使用这个新类型。思考对敌人造成伤害所需的契约。
> 
> ### 解
> 
> ```
>  enum DamageType {
>   Physical,
>   Ice,
>   Fire,
>   Poison
> }
> 
> // We only need an interface that
> // describes something that can be damaged
> interface Damageable{
>   takeDamage(damageType: DamageType, damage: number);
> }
> 
> function frost(target: Damageable, mana: number){
>   // from the example looks like damage 
>   // can be calculated based on mana
>   const damage = mana * 100;
>   console.log(
>     `The air surrounding the target starts quickly forming a ` + 
>     `frozen halo as the water particles start congealing. ` +
>     `All of the sudden it explodes into freezing ice crystals ` +
>     `around the ${target.toString()}.`);
>   target.takeDamage(DamageType.Ice, damage);
> }
> 
> console.log('A giant lizard leaps inside the wall!');
> // this method returns a Lizard object (see samples)
> const giantLizard = world.getLizard();
> 
> world.mooleen.says('And that is as far as you go');
> 
> frost(giantLizard, /* mana */ 2);
> // => The air surrounding the target starts quickly forming a 
> //    frozen halo as the water particles start congealing. 
> //    All of the sudden it explodes into freezing ice crystals 
> //    around the giant lizard.
> //    The giant lizard is very sensitive to cold.
> //    It wails and screams. (Damage 200%)
> //    You damage the giant lizard (-400hp)
> //    The giant lizard dies.
> 
> world.mooleen.laughsWithGlee();
> // => Mooleen laughs with Glee
> 
> /* 
> More and more lizards make it into the fortified area.
> Mooleen, Red, randalf and bandalf form a semicircle against
> the obsidian obelisk and fight fiercely for every inch.
> When the lizards are about to overwhelm the group a huge furry
> figure flashes in front of them charging through the lizard 
> front line and causing enough damage to let the company regroup.
> */
> 
> world.mooleen.says('What?');
> world.rat.says('Happy to serve!');
> 
> world.mooleen.says('You can do that?!');
> world.rat.says('Err... we familiars are very flexible creatures');
> 
> world.mooleen.says("Why didn't you say it before?");
> world.rat.says("Oh... the transformation is incredibly painful");
> world.rat.says("And I bet you'd want to ride on my back." + 
>     "I'm not putting up with that"); 
> ```
> 
> ### 批发破坏！
> 
> 一个一个地杀死野兽是不够的。我们需要一个更强大的法术来集体消灭他们。设计一个可以同时影响几个目标的`iceCone`法术。
> 
> 它应该实现以下代码片段:
> 
> ```
> iceCone(lizard, smallerLizard, greaterLizard);
> // => Cold ice crystals explode from the palm of your hand
> //    and impact the lizard, smallerLizard, greaterLizard.
> //    The lizard is very sensitive to cold.
> //    It wails and screams. (Damage 200%)
> //    You damage the giant lizard (-500hp)
> //    The smaller lizard is very sensitive to cold.
> //    It wails and screams. (Damage 200%)
> //    You damage the giant lizard (-500hp)
> //    etc... 
> ```
> 
> 提示:您可以使用 rest 参数和数组类型注释！
> 
> ### 解
> 
> ```
> function iceCone(...targets: Damageable[]){
>   const damage = 500;
>   console.log(`
> Cold ice crystals explode from the palm of your hand
> and impact the ${targets.join(', ')}.`);
>   for(let target of targets) {
>     target.takeDamage(DamageType.Ice, damage);
>   }
> }
> 
> iceCone(getLizard(), getLizard(), getLizard());
> // => Cold ice crystals explode from the palm of your hand
> // and impact the giant lizard, giant lizard, giant lizard.
> // The giant lizard is very sensitive to cold.
> // It wails and screams. (Damage 200%)
> // You damage the giant lizard (-1000hp)
> // The giant lizard dies.
> // etc...
> 
> world.mooleen.says('Yes!');
> 
> /* 
> Mooleen looks around. She's fending off the lizards fine but
> her companions are having some problems. 
> 
> Red is deadly with the lance and shield but his lance, 
> in spite of of his massive strength, hardly penetrates
> the lizards' thick skin. 
> 
> Bandalf is slowly catching up and crafting ice spells 
> and Randalf, though, a master with the quarterstaff can
> barely fend off the attacks from a extremely huge lizard.
> 
> Things start to look grimmer and grimmer as more lizards jump over
> the wall around the obelisk.
> */
> 
> world.mooleen.says('I need to do something quick'); 
> ```
> 
> ### 用附魔来给同伴赋能！
> 
> 形势看起来很严峻。你唯一的机会是授权给你的同伴，这样你就可以提供一个强大的联合阵线来对抗不断增长的敌人。制造一个可以让武器和防具获得元素属性的法术。
> 
> `enchant`咒语应该满足下面的代码片段:
> 
> ```
> quarterstaff.stats();
> // => Name: Crimson Quarterstaff
> // => Damage Type: Physical
> // => Damage: d20
> // => Bonus: +20
> // => Description: A quarterstaff of pure red
> 
> enchant(quarterstaff, MagicElement.Ice);
> // => You enchant the Crimson Quarterstaff with a frozen 
> //    ice incantation
> //    The weapon gains Ice damage and +20 bonus damage
> 
> quarterstaff.stats();
> // => Name: Crimson Quarterstaff
> // => Damage Type: Ice
> // => Damage: d20
> // => Bonus: +40
> 
> cloak.stats();
> // => Name: Crimson Cloak
> // => Type: cloak
> // => Protection: 20
> // => ElementalProtection: none
> // => Description: A cloak of pure red
> 
> enchant(cloak, MagicElement.Fire);
> // => You enchant the Crimson Cloak with a fire incantation 
> //    The Crimson Cloak gains +20 fire protection
> 
> cloak.stats();
> // => Name: Crimson Cloak
> // => Type: cloak
> // => Protection: 20
> // => ElementalProtection: Fire (+20)
> // => Description: A cloak of pure red 
> ```
> 
> 提示:在`enchant`法术中使用联合类型和类型守卫，让它同时附魔`Weapon`和`Armor`
> 
> ### 解
> 
> ```
>  class Weapon {
>   constructor(public name: string,
>               public damageType: DamageType,
>               public damage: number,
>               public bonusDamage: number,
>               public description: string){}
>   stats(){
>     return `
> Name: ${this.name} Damage Type: ${this.damageType} Damage: d${this.damage} Bonus: +${this.bonusDamage} Description: ${this.description} `;
>   }
> 
>   toString() { return this.name; }
> }
> 
> enum ArmorType {
>   Cloak = 'cloak',
>   Platemail = 'plate mail'
> }
> 
> interface ElementalProtection {
>   damageType: DamageType;
>   protection: number;
> }
> 
> class Armor {
>   elementalProtection: ElementalProtection[] = [];
>   constructor(public name: string,
>               public type: ArmorType,
>               public protection: number,
>               public description: string){}
>   stats(){
>     return `
> Name: ${this.name} Type: ${this.type} Protection: ${this.protection} ElementalProtection: ${this.elementalProtection.join(', ') || 'none'} Description: ${this.description} `;
>   }
>   toString() { return this.name; }
> }
> 
> function enchant(item: Weapon | Armor, element: MagicElement){
>   console.log(`You enchant the ${item} with a ${element} incantation`);
>   if (item instanceof Weapon){
>     enchantWeapon(item, element);
>   } else{
>     enchantArmor(item, element);
>   }
> 
>   function enchantWeapon(weapon: Weapon, element: MagicElement){
>     const bonusDamage = 20;
>     weapon.damageType = mapMagicElementToDamage(element);
>     weapon.bonusDamage += bonusDamage;
>     console.log(`The ${item} gains ${bonusDamage} ` + 
>                 `${weapon.damageType} damage`);
>   }
>   function enchantArmor(armor: Armor, element: MagicElement){
>     const elementalProtection = {
>       damageType: mapMagicElementToDamage(element),
>       protection: 20,
>       toString(){ return `${this.damageType} (+${this.protection})`}
>     };
>     armor.elementalProtection.push(elementalProtection);
>     console.log(`the ${item} gains ${elementalProtection.protection}` + 
>                 ` ${elementalProtection.damageType} incantation`);
>   }
> }
> 
> function mapMagicElementToDamage(element: MagicElement){
>   switch(element){
>     case MagicElement.Ice: return DamageType.Ice;
>     case MagicElement.Fire: return DamageType.Fire;
>     default: return DamageType.Physical;
>   }
> }
> 
> let quarterstaff = getQuarterstaff();
> console.log(quarterstaff.stats());
> // => Name: Crimson Quarterstaff
> //    Damage Type: Physical
> //    Damage: d20
> //    Bonus: +20
> //    Description: A quarterstaff of pure red
> 
> enchant(quarterstaff, MagicElement.Ice);
> // => You enchant the Crimson Quarterstaff with a frozen ice incantation
> //    The Crimson Quarterstaff gains 20 Ice damage
> 
> console.log(quarterstaff.stats());
> // Name: Crimson Quarterstaff
> // Damage Type: Ice
> // Damage: d20
> // Bonus: +40
> // Description: A quarterstaff of pure red
> 
> let cloak = getCloak();
> console.log(cloak.stats());
> // Name: Crimson Cloak
> // Type: cloak
> // Protection: 20
> // ElementalProtection: none
> // Description: A cloak of pure red
> 
> enchant(cloak, MagicElement.Fire);
> // You enchant the Crimson Cloak with a fire incantation
> // the Crimson Cloak gains 20 Fire incantation
> 
> console.log(cloak.stats());
> // Name: Crimson Cloak
> // Type: cloak
> // Protection: 20
> // ElementalProtection: Fire (+20)
> // Description: A cloak of pure red
> 
> world.mooleen.says('Awesome! This will do!');
> 
> /*
> 
> As soon as Mooleen enchants the group's weapons and
> armor the battle takes a different turn. Where previously
> a lizard would've remained impassible after receiving a wound
> now there's wails and shouts of beast pain surrounding 
> the group...
> 
> */
> 
> world.mooleen.says('haha! To Arms Sisters!');
> world.red.says('What?'); 
> ```