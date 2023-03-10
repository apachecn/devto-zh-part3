# 如何用 Java 生成大量随机的 CSV 文件

> 原文：<https://dev.to/aussieguy/how-to-generate-tons-of-random-csv-files-in-java-56e4>

*这篇文章最初发表在[我的博客](http://anthonybruno.dev/2017/12/28/generating-csvs-with-SDGen-and-Faker.html)T3 上*

CSV 格式是一种简单但常用的数据交换格式。许多应用程序支持以 CSV 文件的形式导入和导出信息。由于这种格式的流行，开发人员需要生成大量的 CSV 文件来进行测试。这就是我最新的开源项目 [SDGen](https://github.com/AussieGuy0/SDgen) 发挥作用的地方。

SDGen 是一个 Java 库，帮助开发人员生成用于测试目的的随机数据文件。它支持 CSV 和固定宽度格式，将来还会支持更多格式，比如 JSON。

本指南将向您展示如何使用 [SDGen](https://github.com/AussieGuy0/SDgen) 和 [Faker](https://github.com/DiUS/java-faker) 生成一个简单的 CSV 文件。Faker 将用于帮助创建随机值。

## [胃](#maven)

对于 Maven 项目，我们可以通过将以下 xml 插入到项目的 pom.xml 文件中来添加所需的库。

```
<dependencies>
    <dependency>
        <groupId>au.com.anthonybruno</groupId>
        <artifactId>SdGen</artifactId>
        <version>0.3.0</version>
    </dependency>
    <dependency>
        <groupId>com.github.javafaker</groupId>
        <artifactId>javafaker</artifactId>
        <version>0.14</version>
    </dependency>
</dependencies> 
```

## 指令

首先，我们需要通过编写:
来获得 Faker 实例

```
 Faker faker = Faker.instance(); 
```

然后，我们可以使用 faker 实例来生成 URL`faker.internet().url()`和星球名称`faker.space().planet()`等值。

接下来，我们将使用`SDGen`的 fluent builder 为我们想要创建的 CSV 文件创建模式。首先，我们写:

```
 Gen.start() 
```

然后我们可以使用`addField`方法添加字段(即列)。`addField`有两个参数:一个是`String`名称，用于标识生成的文件中的字段，另一个是`Generator`。一个`Generator`是一个具有单一方法`generate`的简单接口。这是创建随机值并将其添加到字段中的方式。

我们将制作一个简单的 CSV 文件的人。为此，我们将添加一个“名字”和一个“姓氏”列，使用相应的`Faker`方法为这些列生成值:

```
 Gen.start()
        .addField("First Name", () -> faker.name().firstName())
        .addField("Last Name", () -> faker.name().lastName()) 
```

*注:使用 lambdas(如`() -> faker.name().firstName()`相当于写作:*

```
new Generator() {
    @Override
    public Object generate() {
        return faker.name().firstName();
    }
} 
```

我们还想添加一个“年龄”字段。为此，我们可以使用`SDGen`的内置`IntGenerator`。我们可以给它一个合理的最小值和最大值来限制它将生成的数字的范围。`SDGen`为所有原始类型提供生成器。

```
Gen.start()
    .addField("First Name", () -> faker.name().firstName())
    .addField("Last Name", () -> faker.name().lastName())
    .addField("Age", new IntGenerator(18, 80)) 
```

接下来，我们使用`generate`方法指定要生成多少行。我们还想选择生成数据的格式。我们将使用`asCsv`来生成 CSV 格式的数据。`SDGen`还支持定宽格式，将来还会支持 JSON 等其他数据格式。

```
Gen.start()
    .addField("First Name", () -> faker.name().firstName())
    .addField("Last Name", () -> faker.name().lastName())
    .addField("Age", new IntGenerator(18, 80))
    .generate(1000) //1000 rows will be generated
    .asCsv() 
```

最后，我们指定数据将如何输出。我们将使用`toFile`方法将信息放入文件中。

```
Gen.start()
    .addField("First Name", () -> faker.name().firstName())
    .addField("Last Name", () -> faker.name().lastName())
    .addField("Age", new IntGenerator(18, 80))
    .generate(1000)
    .asCsv()
    .toFile("people.csv"); 
```

就是这样！运行代码将在项目的工作目录中生成一个 CSV 文件。下面是我运行它时产生的一些数据:

```
First Name,Last Name,Age
Corrine,Berge,78
Gerald,Carter,63
Enid,Padberg,66
Eleanora,Murray,79
Coy,Okuneva,76
Jovan,Reynolds,77
Lane,Haag,48 
```

更多关于 SDGen 的信息，请访问 [Github](https://github.com/AussieGuy0/SDgen) 上的。

## 详细信息

*   Faker: [Github](https://github.com/DiUS/java-faker)
*   SDGen: [Github](https://github.com/AussieGuy0/SDgen)