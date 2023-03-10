# Java 中的 OOP 概念

> 原文：<https://dev.to/codegym_cc/oop-concepts-in-java-44c9>

Java 最强的一点是面向对象编程。这就是这种语言如此流行的原因，它非常适合任何规模的项目。

[![OOP concepts in Java](img/43fd4dd5165bf7e93266bd4a9c5ba0fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xuZ3NRmT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codegym.cc/api/1.0/reimg/10000017/e5867c90-24ee-4094-a9d6-e7c982300b79%3Fsize%3D0)

什么是面向对象编程？这不是魔术，但如果你真的进入其中，它看起来就像魔术一样。OOP 是关于如何构建你的软件。一个概念，或者更确切地说是一组概念，允许您在 Java 对象之间构建一些特定的交互和关系，以便有效地开发和使用软件。

经典版本包括 3 + 1 个主要的 OOPs 概念。先说经典。

*物体*

Java 中的对象和现实世界中的对象一样有两个特征，状态和行为。

例如，对象人类具有状态(姓名、性别、睡眠……)和行为(学习 Java、走路、说话...).任何 Java 对象都将其状态存储在字段中，并通过方法公开其行为

## 封装

数据封装是对外部世界隐藏内部数据，并且只能通过公开的方法访问它。那是什么意思？什么数据，瞒着谁？隐藏意味着限制对类的数据成员(字段)的直接访问。

### Java 的工作原理:

1.  字段被设置为私有
2.  类的每个字段都有两个特殊的方法:一个 getter 和一个 setter。Getter 方法返回该字段。Setter 方法允许您以非直接但合法的方式更改字段的值。

**封装的例子，Java 代码:**

```
public class Student {
private int age;
private String name;

public int getAge() {
return age;
}

public void setAge(int age) {
this.age = age;
}

public String getName() {
return name;
}
public void setName(String name) {
this.name = name;
}
}

public class Test{
public static void main(String[] args) {
Student firstStudent = new Student();
firstStudent.setName("John");
//now you can’t do this:  firstStudent.name = “John”, the name field is private!
}
} 
```

### 你为什么要这么做？

主要原因是简化您的代码更改。想象一下，你有一个曲棍球学校的申请，有一个类`HockeyStudent`有两个字段:学生入学时的姓名和年龄。大概是这样:

```
public class HockeyStudent {
public String name;
public  int ageOfEnrollment;
} 
```

Public `ageOfEnrollment`，没有 getters 或 setter……这个类被许多其他类使用，在一个人说 int age 不够用之前一切都很好。小组中的一些曲棍球运动员几乎比其他人大一岁，所以根据他们出生的月份将他们分成两组会更方便。所以新字段`ageOfEnrollment`应该是数组 int[][]第一个数字代表整年，第二个数字代表月份。现在你应该重构所有使用类`Student`的代码！然而，如果你的`ageOfEnrollment`是私有的，并且有 getters 和 setters，那么一切都变得容易了。如果设置学生年龄的要求改变了，只需更新 setter 方法`setAgeOfEnrollment()`中的逻辑，您的类就可以继续使用`Student`而不会有问题！

这个例子有些做作，但是我希望它解释了为什么使用封装是一个好主意。

## 继承

这个道理即使不实践也比较好理解。不重复自己(干)可能是继承观念的座右铭。

继承允许创建继承父类的字段和方法的子类，而无需重新定义它们。当然，您可以在子类中覆盖父类的字段和方法，但这不是必需的。你也可以在子类中添加新的状态和行为。父类有时被称为超类或基类，子类被称为子类。Java `extends`关键字用来实现代码中的继承原则。

**在 Java 中如何工作:**

1.  创建父类。
2.  使用`extends`关键字创建一个子类。
3.  在子类的构造函数中使用`super(parentField1, parentField2,...)`方法继承父类的字段。

*构造函数是初始化新创建对象的特殊方法。构造函数与其类名同名。有两种类型的构造函数:默认(无参数构造函数)和参数化构造函数。类必须至少有一个构造函数(默认情况下总是这样)，并且可以有很多构造函数。*

每次你创建一个新的对象，你就调用它的构造函数。上例中的线是

```
Student firstStudent = new Student(); 
```

*你调用* `Student` *类的默认构造函数* `Student()` *使用* `new`

**一些规则:**

1.  一个类只能有一个父类。
2.  一个父类可能有许多子类。
3.  子类可以有自己的子类。

**示例继承，Java 代码**

让我们创建一个`Phone`类。

```
public class Phone {
    int price;
    double weight;

//constructor
public Phone(int price, double weight) {
        this.price = price;
        this.weight = weight;
    }

    void orderPhone(){
        System.out.println("ordering phone...");
    }
} 
```

手机有不同的类型，所以让我们创建两个子类，一个用于 Android 手机，另一个用于 iPhones，并添加一些它们的父类没有的字段和方法。它们的父类拥有的字段，让我们用`super()`调用构造函数。

**Java 继承示例**

```
public class Android extends Phone {

//some new fields
String androidVersion;
int screenSize;

    String secretDeviceCode;

//constructor
    public Android(int price, double weight, String androidVersion, int screenSize, String secretDeviceCode) {
        super (price, weight); //Android inherits Phone’s fields

        //this - reference to the current class object
        //super - reference to the parent class object

        this.androidVersion = androidVersion;
        this.screenSize = screenSize;
        this.secretDeviceCode = secretDeviceCode;
    }

    //new method specific for Android only, but not for Phone class
    void installNewAndroidVersion() {
        System.out.println("installNewAndroidVersion invoked...");

    }

}

public class IPhone extends Phone {
    int price;
    double weight;

    boolean fingerPrint;

    public IPhone(int price, double weight, boolean fingerPrint) {
        super (price, weight, countryProduced);
        System.out.println("IPhone constructor was invoked...");
        this.fingerPrint = fingerPrint;
    }

    void deleteIPhoneFromDb() {
        System.out.println("deleteIPhoneFromDb invoked...");
    }

@Override //This is about polymorphism, see below
void orderPhone(){
        System.out.println("ordering my new iPhone and deleting the old one...");
    }
} 
```

所以，再说一次:在 Java 中，继承允许你用继承父类的字段和方法的子类来扩展一个类。这是实现代码重用的一个很好的方法。

## 多态性

多态是一个对象呈现不同形式或者以不同方式行动的能力。通常，如果使用父类引用来引用子类对象，Java 中就会出现多态性。

### 在 Java 中是什么意思，是如何工作的:

Java 中的多态性是什么？一般来说，这意味着您可以将同一个方法名用于不同的目的。Java 中有两种类型的多态性:方法覆盖(动态多态性)和方法重载(静态多态性)。

**方法覆盖**

您可以在子类中覆盖父类的方法，迫使它以不同的方式工作。让我们用 play()方法创建一个父类音乐家。

**Java 多态性示例**

```
public class Musician {
    String name;
    int age;

    //default constructor
    public Musician() {
    }

    //parameterized constructor
    public Musician(String name, int age) {
        this.name = name;
        this.age = age;
    }

    void play() {
        System.out.println("I am playing my instrument...");
    }
} 
```

音乐家使用不同的乐器。让我们创建两个子类`Pianist`和`Violinist`。由于多态性，他们都将使用自己版本的`play()`方法。对于覆盖，你可以使用`@Override`符号，但这不是必须的。

```
public class Pianist extends Musician {

    String favoritePianoType;

    public Pianist(String name, int age, String favoritePianoType) {
        super(name, age);
        this.favoritePianoType = favoritePianoType;
    }

    @Override
void play(){
        System.out.println("I am playing piano...");
    }
} 
```

小提琴演奏者可以是独奏者，也可以是乐队成员。让我们在重写我们的`play()`方法时考虑一下它。

```
public class Violinist extends Musician {
    boolean isSoloist;

public Violinist(String name, int age, boolean isSoloist) {
            super(name, age);
            this.isSoloist = isSoloist;
        }

    @Override
void play(){
if (isSoloist)
        System.out.println("I am playing violin solo...");
else
System.out.println("I am playing violin in Orchestra...");

    }
} 
```

让我们创建一个`Demo`类，其中我们创建三个对象，每个类一个，并检查结果。

```
public class Demo {
    public static void main(String[] args) {
  Musician musician = new Musician();
        Violinist violinist = new Violinist("John", 32,true);
  Pianist pianist = new Pianist("Glen", 30, "Wooden");

        System.out.println("Musician said:");
        musician.play();
        System.out.println("Violinist said:");
        violinist.play();
  System.out.println("Pianist said:");
  pianist.play();
    }
} 
```

**这就是我们在这里得到的:**

***音乐家说:
我正在演奏我的乐器...
小提琴手说:
我在演奏小提琴独奏……
钢琴家说:
我在演奏钢琴...***

每个小提琴手和钢琴家都是音乐家，但不是相反。这意味着你可以使用音乐家的演奏方法，如果你不需要创造一个新的。或者您可以使用`super`关键字从子方法调用父方法。让我们用钢琴师的代码来做:

```
public class Pianist extends Musician {

    String favoritePianoType;

    @Override
    void play(){
        super.play();
        System.out.println("I am playing piano...");
    }
} 
```

现在让我们调用我们的演示类。下面是结果:

***音乐家说:
我正在演奏我的乐器...小提琴手说:
我正在演奏小提琴独奏...钢琴师说:我正在演奏我的乐器...我在弹钢琴...*T9】**

**方法重载**

方法重载意味着在同一个类中使用不同的同名方法。它们应该在数量、顺序或参数类型上有所不同。据说，钢琴家会弹古典钢琴和电钢琴。要演奏最后一首曲子，这位音乐家需要电。让我们创建两个不同的`play()`方法。第一个没有参数，用于木制乐器，第二个带有电检查器，用于电钢琴。

```
public class Pianist extends Musician {

    String name;
    int age;
    String favoritePianoType;

    @Override
    void play(){
        super.play();
        System.out.println("I am playing piano...");
    }
    void play(boolean isElectricity){
        if (isElectricity) {
            System.out.println("Electricity on");
            System.out.println("I am playing piano...");
        }
        else System.out.println("I can't play this without electricity");
    }
} 
```

顺便说一下，你可以这样使用第二个`play(boolean)`方法中的第一个`play()`方法:

```
void play(boolean isElectricity){
        if (isElectricity) {
            System.out.println("Electricity on");
            play();
        }
        else System.out.println("I can't play this without electricity");
    } 
```

让我们在我们的`Demo`类中添加一些行来检查我们的重载:

```
public class Demo {
    public static void main(String[] args) {

        Musician musician = new Musician();
        Violinist violinist = new Violinist("John", 23,true);
        Pianist pianist = new Pianist("Glen", 30, "Wooden");

        System.out.println("Musician said:");
        musician.play();
        System.out.println("Violinist said:");
        violinist.play();
        System.out.println("Pianist said:");
        pianist.play();
        System.out.println("Now pianist tries electronic Piano:");
        pianist.play(true);
        System.out.println("now electricity's gone and electronic Piano player said:");
        pianist.play(false);
    }
} 
```

结果如下:

***音乐家说:
我正在演奏我的乐器...小提琴手说:
我正在演奏小提琴独奏...钢琴师说:我正在演奏我的乐器...我在弹钢琴...
现在钢琴家尝试电子琴:
通电
我正在弹奏我的乐器...我在弹钢琴...
现在没电了，电子琴手说:
没电我弹不了这个***

Java 根据它的参数和对象的类型知道应该使用什么方法。这就是多态性。

## 抽象

当我们描述一个物体时，我们试图建立它的模型。例如，我们用不同的汽车编写一个赛车视频游戏 **MyRacer** 。玩家可以选择其中之一，然后更新或购买更新的。那么…什么是汽车？汽车是相当复杂的东西，但是如果我们试图创建一个街机赛车游戏(不是模拟器)，我们不应该描述它包含的所有成千上万的齿轮。

我们需要它的型号，最大速度，机动性，价格，颜色…也许这就够了。这是这个游戏的汽车模型。

后来，在 MyRacer 2 中，我们决定添加影响路面弹性的轮胎。这里的模型是不同的，因为我们需要更多的细节。

让我们将数据抽象定义为仅识别对象的重要(或必需)特征并忽略不相关细节的过程。

有不同层次的抽象。例如，如果你是一辆公共汽车的乘客，你应该知道你的公共汽车看起来怎么样，它去哪里，但是你不应该知道如何驾驶它。如果你是一个总线驱动者，你不需要知道如何创建一个新的总线，你应该知道如何驾驶它。但是如果你是一个总线构造者，你应该进入较低的抽象层次…关于总线架构的细节对你来说非常重要。希望你明白我的意思。

### Java 的工作原理:

让我们在 Java 或者说在 OOP 中从最低层(最具体的)到最高层(更抽象的)建立四个抽象层。

1.  较低层次的抽象是一个特定的**对象**。它是一个实体，具有一个类的特定实例所固有的一系列特征。它有特定的字段值

2.  创建对象的模板是一个**类**。它是对具有相似属性和内部结构的一组对象的描述。

3.  **抽象类**是一组类的特征的抽象描述(充当其他类继承的模板)。它具有高度的抽象性，因此，不可能直接从抽象类中创建对象，只能通过从子类中创建对象。抽象类可以包括具有实现的方法，但不是必须的。

4.  **接口**是一个 Java 编程语言构造，其中只能描述抽象公共方法和静态属性常量(final static)。也就是说，和在抽象类的基础上一样，在接口的基础上生成对象是不可能的。

顺便说一下，在 Java 8 或更高版本中，你不仅可以在接口中使用抽象方法和常量，还可以使用默认方法和静态方法。

在 Java 接口中定义了一个行为，抽象类创建了层次结构。一个接口可以实现不同的类。

**Java 接口示例**

```
interface Human {
    public void struggle();
    public void protect();
}

interface Vulcanian {
int earSharpnessAngle;
    public void emotionOff(boolean isOn);
    public void telepathy();
} 
```

你可以实现多个接口

```
Class Spock implements Human, Vulcanian {
public void struggle() {
System.out.println (“I am struggling...”);
}
    public void protect() {
System.out.println(“You are under my protection!”);
}
public void emotionOff(boolean isOn){
If (isOn) {
System.out.println(“I am turning off my emotions”);
isOn= !isOn;
}
}
    public void telepathy() {
System.out.println(“Connecting to your brain... ”);
}

} 
```

对于初学者来说，这就是 Java 中主要的面向对象编程概念。除了 OOP 的 4 大原则，Java 里还有关联、聚合、组合。你可以称它们为“额外的 OOP 原则”,它们值得拥有自己的文章。

之前发表在 [CodeGym 博客](https://codegym.cc/groups/posts/76-oop-concepts-in-java)上。