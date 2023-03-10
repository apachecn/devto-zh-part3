# Java ☕️中的类、继承、包和模块化

> 原文：<https://dev.to/martinsonuoha/classes-inheritance-packages-modularization-in-java-4llg>

[![Header Image](img/4a348e289a1cc487b445289e55f272c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VF5UgkP5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqSkHZmZh8kewtJkgTCTsFg.png)

> 模块化编程是将计算机程序细分成独立子程序的过程。模块是一个独立的软件组件。它通常可以与系统的其他组件一起用于各种应用和功能。—www.techopedia.com

虽然这篇文章强调的是模块化，但是我们将会对类和继承做一个简短的介绍，这是 Java 实施的面向对象方法的一部分，目的是将这些内容放在一起。

## 跟班☕️

如果我们还不知道，Java 中的一切都以这样或那样的方式与一个类相关联，为什么呢？因为 Java 是一种面向对象的编程语言，就像所有面向对象的语言一样，一切都是对象。对于具有“分组”或“蓝图”的对象，它们需要属于一个类或被分类在一个类中。

这里有一个例子。

[![classical_illustration](img/f64b19eaaeaf9dd40b59438b604f8a70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gt2-5cNq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AD_LtVhlWlYed00f3.jpg)

> **交通工具*包括货车、自行车、机动车(摩托车、汽车、卡车、公共汽车)、轨道交通工具(火车、有轨电车)、水上交通工具(轮船、小船)、两栖交通工具(螺旋推进交通工具、气垫船)、飞机(飞机、直升机)和宇宙飞船。—维基百科*

根据维基百科对交通工具的定义，虽然我们可能很容易将蓝色汽车和蓝色自行车归类为两种不同的物体，但我们同样可以说它们都是“交通工具”，因此属于“交通工具”的类别。我们也看到类是如何作为其他几种对象的蓝图的(包括上面)。

虽然这些对象属于同一个类，但它们不一定执行相同的“功能/动作”，在这种情况下，我们将这些功能/动作称为“方法”。

## 方法🏃🏼‍♀️💨

我们可以说方法是对象可以执行的任何动作。行动包括:飞行，驾驶，骑行，倒车。然而，同一类的对象(比如我们例子中的汽车和自行车)可能没有所有相同的方法，就像汽车不能被踩踏，自行车不能被驱动一样。把方法想象成动词。

[![methods_illustration](img/83318da118e1dd49981c6f173b02d2c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gAkmsC-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2APZgv3L76WhQo5ntxj9nXaA.png)T3】

```
public class Car {
  public void drive() {

  }
  public void reverse() {

  }
  public void park() {

  }
} 
```

## 属性🎨

属性是对象的特性，它们是定义对象或区分对象的东西。“一辆蓝色的车”——单词“蓝色”告诉你更多关于这辆车的信息。把属性想象成形容词。

你也可以说类属性是类中的变量。

[![attributes_illustration](img/16a35b88cb879840bd76d6af5bbc152e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QwkWoT_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Auv3db6aXYdI4AmoK8Y8oPQ.png)T3】

```
public class MyClass {
  int x = 5;
  int y = 6;
} 
```

[![spooky](img/8855ea1aa69462a504de3d5fc0e95209.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--htEbhBje--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AL2NJkAOvvHj3b7Hs.png)

## 继承👩‍👩‍👧‍👧

拥有一堆对象以及它们的集合方法和属性是很好的，但是更好的是让我们在其他相关对象之间共享这些方法和属性，我们为什么要这样做呢？嗯，主要是为了可重用性和保持 DRY(不要重复自己)原则。这就是继承派上用场的地方，继承允许一个类`( subclass/child )`“继承”另一个类`( super-class/parent )`的属性和方法。

我们不会太深入地讨论继承的细节，我们只看一个 Java 中的实际例子。但是对于 notes，我们有许多类型的继承:

*   单身(我们会碰这个)
*   多重
*   多层次的
*   等级体系的
*   混合物

> 要在 Java 中声明父类的子类，可以使用关键字*扩展*。

## 模块化🍰

模块化包括将程序分解成组件或子程序的过程。

[.]😚'+∞+'😉'] =😘

希望上面的表情符号是一个足够好的说明。模块化程序的主要优点是它帮助我们更快更有效地检测和调试问题。

### 套餐

Java 中的包用于对相关的类进行分组。包基本上是相关类的文件夹。当构建更易维护的代码时，包就派上了用场，包主要有两种类型:

*   内置包(来自 Java API 的包)
*   用户定义的包(您创建的包)

我们只看第二个。

## 把所有的东西放在一起👩‍👩‍👦‍👦

让我们看一个非常基本的例子:我们将用类方法和属性构建一个 Calculator 类(父类),然后从它扩展一个 MyCalculator 子类(子类),它将继承它的方法和属性。默认情况下，我们将使用模块化，因为我们将分离每个类文件，并将它们放在一个文件夹(包)中。

> 注意:每个包文件应该只有一个类

首先，我们的包文件夹结构如下:

[![](img/2264c1bc83b498c5899bdd6d365e93d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sl5mHf13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ArIcOjSGxEHZv-Amoxl3ymA.png)

> 注意:本课我将使用 Visual Studio 代码，“为什么？?"尽管拥有一个像 Netbean 或 IntelliJ 这样的 IDE 来抽象所有的编译细节是非常好的事情，但一个问题是，当需要时，您可能无法完全理解这个过程并充分地调试问题。

## Calculator.java👨🏽‍💼👩🏼‍💼

[![](img/a9b7e57a49af0166154f4f9daa184e73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bCvugNTi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ACu0iZK2EotJkTe-_93lu3A.png)

我们已经声明了一个父类 Calculator，它有一个类属性 z 和两个类方法 add()和 subtraction()。加法方法接受两个参数并打印出它们的和，而减法方法打印出它们的差。

## MyCalculator.java👨‍👧

[![](img/609d2501427a353a1ff9da44e6dc62de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aVtRB3bG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Astoxqys_Hjpd1yAAuUmi-w.png)

这里我们声明了一个继承自父类 Calculator 的子类 MyCalculator。注意我们使用了一个未声明的变量 z，我们不必再次声明 z 变量，因为子类(MyCalculator)继承了父类(Calculator)的所有属性和方法。因为我们已经在父类中声明了 z，所以我们不需要在子类中重复声明。

我们还创建了一个新的乘法方法，它也接受两个整数变量并返回它们的乘积。

> 注意:在子类中创建的新方法不能从父类中访问。继承不起作用，反之亦然。
> 
> 注意(再次):要从父类继承，使用“extends”关键字

## Main.java ☕️

这个类没有什么特别的，它基本上是我们拥有 main 方法的地方，如果你还不知道的话，main 方法是我们程序初始化的标志，Java 寻找这个家伙。

[![](img/eaf73302c1a9f970b56c403b4834354b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Te3eFTW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aah0nBMtik_GlowPbpXFOIw.png)

*   第 3 行:我们声明了两个整型变量 a 和 b，这将是我们传递给方法的参数。
*   第 4 行:为了访问 MyCalculator 类中的方法，我们需要创建该类的一个实例。所以我们创建了一个名为 demo 的 MyCalculator 实例。
*   我们称之为加法、减法和乘法，将变量 a 和 b 作为参数传递。

## 运行🚀

好了，这是我们运行程序的部分。因为我们没有使用 Netbean 或 IntelliJ，所以我们将手动编译和运行我们的程序。

成功运行 Java 程序有两个主要步骤:

*   编译(javac)
*   执行(java)

> 对于编译，我们使用 javac 命令
> 
> 为了执行，我们使用 java 命令

*注意*:通常，当处理一个单独的 Java 文件/类时，我们会为那个类运行两个命令。但是在这种情况下，我们有多个相关的类，如果能一次性编译所有的程序就好了。谢天谢地，我们可以。

根据您使用的操作系统，打开您的命令行或终端。

对于 windows:按 Windows 键+ R，然后键入“cmd”(不带引号)，并按 enter 键。您应该会看到命令行弹出。

[![](img/9affb299795ad82b9e0b60b963c823da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ew0vGaEs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A2xhcsA2TD9zhbTGdR2ZSVw.png)

现在我们需要进入保存程序的目录。我假设它保存在桌面上，文件夹名为“calculator”。要进入您的程序目录，请键入:

`cd Desktop/calculator`

[![](img/a5915f8909c3b23fe7234f4bd8875311.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sNRPmj5N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AKSxSD_X4tWPLGQOotVFgEw.png)

现在，您应该位于项目目录中。

### 编译

要一次性编译目录中的所有类，请键入:

`javac ./*.java`

> 注意:这个命令告诉 java 编译器在当前文件夹中查找，找到任何带有“java”扩展名的文件(在这个例子中，是 Calculator.java、MyCalculator.java 和 Main.java)并编译它们。

另一种编译类的方法是一次编译一个:如果您更愿意使用这种方法，您需要按照以下顺序进行编译:

*   编译父类(javac Calculator.java)
*   编译子类(贾瓦茨 MyCalculator.java)
*   最后，编译主类(javac Main.java)

这将为每个类(MyCalculator.class、Calculator.class、Main.class)创建一个. class 文件。

### 执行

要运行您的程序，您只需要运行包含 main 方法的类(在我们的例子中是 Main.class)

`java Main`

您应该得到以下输出:

[![](img/6213385468b05fe53661969175961125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--42E9TkZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AgS52AyF1G-Z7GDYNrb7UrQ.png)

🎉🥂我们已经结束了这个短暂的😅教程。希望这能帮助到一些人。