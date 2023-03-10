# 用 Kotlin 和 GraalVM 构建本机 CLI

> 原文：<https://dev.to/viniciusccarvalho/building-a-native-cli-with-kotlin-and-graalvm-55ee>

# 使用 java 构建原生代码

[GraalVM](https://www.graalvm.org/) 是 Oracle 发布的一款激动人心的新 VM，支持多语言应用的创建。也许最令人兴奋的新特性之一是`native-image`工具，它将您的字节码编译成无需 JVM 就可以在目标运行时上执行的单个二进制文件。

但是因为它现在被打包成一个单一的二进制文件，这意味着它也不需要首先创建一个 JVM，并且与 JVM 进程相关的内存使用和`warmup`周期早已过去。

为了给你一个我们正在讨论的概念，我们今天将要构建的应用程序有如下结果(java 与本地版本)

```
Java: 0.97s user 0.10s system 131% cpu 0.808 total

Binary: 0.01s user 0.01s system 8% cpu 0.232 total 
```

Enter fullscreen mode Exit fullscreen mode

# 命令行应用程序 java 的缺失用例

现在，有几个框架，像 quar kus T1 和 T2 micro naut T3，提供了一个简单的方法将服务器端应用移植到本地应用。

这些都很好，但是 IMHO 服务器端从来都不是 java 的问题，一旦 JVM 开始服务成千上万的请求，就很难用 node/go 版本来击败它。它很快，很稳定，而且一旦用户达到一个给定的阈值，内存占用就不会那么糟糕。

另一方面，CLI 应用程序一直是 java 的弱点。没有人想下载一个 jar 文件，安装(正确版本的)java，只是为了执行几个命令。

我们的团队经常需要编写一个 CLI 来集成一些东西，而 java/kotlin 从来不是我们的选择。这是围棋成为王者的地方，希望现在有了 GraalVM，事情可以有所改变。

# 那么我们今天在建造什么呢？

我想构建一个 CLI 来包装 REST 服务。现在很多 CLI 都是以这种方式工作的:kubectl、gcloud、azure...所以这似乎是一个好的开始。

我们的 CLI 应用程序，我们称之为`f1`，将是 [Ergast 赛车 API](https://ergast.com/mrd/) 的包装器。

# 要求，限制，哪里疼？

根据您使用的 JVM 的特性，将 java 应用程序转换成本地应用程序可能是一个痛苦的经历。特别是反射，如果你想了解更多，请阅读关于反射的章节。

在写这篇文章时，我尽力避免使用基于反射的框架，当你不得不处理 JSON 和 kotlin 数据类时，这变得特别痛苦。

依赖关系:

*   Clikt -基于 Kotlin 的 CLI 解析器和框架。没有注释，没有反射，在构建您的原生应用程序时更不用担心
*   [Fuel](https://fuel.gitbook.io/documentation/) - Kotlin http 客户端库，没有反射(像 retrofit2)等。
*   [Gson] - Google JSON 库，我选择编写特定的 SerDes 类，这并不坏，如果你走反射路线，你可能需要将类追加到`reflect-config.json`

# 让我们建造它吧

所有的代码都可以在这个 [github repo](https://github.com/viniciusccarvalho/f1-kotlin) 上找到。

开始一个新的格雷尔·科特林项目。我通常使用 IntelliJ IDEA 的新项目向导。

我宁愿创建一个胖罐子，所以确保你的`build.gradle`看起来像这样:

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath 'com.github.jengelman.gradle.plugins:shadow:4.0.3'
    }
}
apply plugin: 'java'
apply plugin: 'kotlin'
apply plugin: 'application'
apply plugin: "com.github.johnrengelman.shadow"

group 'io.igx.kotlin'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

mainClassName = "io.igx.kotlin.ApplicationKt"

repositories {
    mavenCentral()
    jcenter()
}

dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk8"
    implementation 'com.github.ajalt:clikt:1.7.0'
    implementation 'com.github.kittinunf.fuel:fuel-gson:2.0.1'
    implementation 'de.vandermeer:asciitable:0.3.2'
    testCompile group: 'junit', name: 'junit', version: '4.12'
}

compileKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
    kotlinOptions.jvmTarget = "1.8"
} 
```

Enter fullscreen mode Exit fullscreen mode

修改您的`Application.kt`文件，在上面打印一个简单的内容:

```
fun main(args: Array<String>) {
    println("Hello Native")
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**重要提示**:确保您已经安装了 GraalVM，并且$GRAALVM_BIN 是您的安装二进制文件所在的文件夹。

* * *

构建您的应用程序`./gradlew build`，现在通过调用下面的命令
生成本机映像

```
$GRAALVM_BIN/native-image --report-unsupported-elements-at-runtime  -jar build/libs/f1-1.0-SNAPSHOT-all.jar f1 --no-server 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里查看每个标志的含义

在本机映像工具完成之后，您应该有一个`f1`二进制文件。只需调用它:`./f1`就可以得到一个漂亮的`Hello Native`输出。

## 创建命令

我们现在需要的是一些我们想翻译成 REST 调用的命令。Clikt 我们将创建一个主命令`F1`并嵌套两个子命令:`Drivers`和`Circuits`。

创建一个`Commands.kt`文件，并添加以下代码:

```
 class F1 : CliktCommand() {
    override fun run() = Unit
}

class DriverCommands : CliktCommand(name = "drivers") {

    val season: Int by option(help = "Season year").int().default(LocalDate.now().year)

    override fun run() {
       println("Fetching drivers of $season season")
    }

}

class CircuitCommands : CliktCommand(name="circuits") {
    val season: Int by option(help = "Season year").int().default(LocalDate.now().year)

    override fun run() {
       println("Fetching circuits of $season season")
    }

}

Change your `Application.kt` main method to: 
```

Enter fullscreen mode Exit fullscreen mode

科特林
fun main(args: Array) {
F1()。子命令(DriverCommands()、CircuitCommands())。main(args)
}

```
 Build the whole thing with `gradle` and run the `native-image` command again. 
```

Enter fullscreen mode Exit fullscreen mode

现在试着运行`./f1`，你将会从 clikt:
得到一些不错的默认帮助消息

```
Usage: f1 [OPTIONS] COMMAND [ARGS]...

Options:
  -h, --help  Show this message and exit

Commands:
  drivers
  circuits 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们有两个注册的命令，并运行这些命令的帮助:

```
./f1 drivers --help
Usage: f1 drivers [OPTIONS]

Options:
  --season INT  Season year
  -h, --help    Show this message and exit 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了 CLI 应用程序的框架，让我们给它添加一些内容。

## 处理 JSON

正如我之前所说，GraalVM 的反射有点痛苦。所以我去寻找一种为远程 API 的每个域对象编写自己的`JsonDeserializer`的方法。

ergast API 还有另一个问题，它们表示`wrapped` json 的方式对 java 解析并不友好(它们非常类似于它们的 XML 表示)，所以无论如何都需要定制来处理这些包装器。

我已经创建了一个`Domain.kt`文件，将所有的序列化逻辑:

```
data class Response(val series: String, val limit: Int, val offset:Int, val total:Int)
data class Driver(val driverId: String, val code: String?, val url: String? = "", val givenName: String, val familyName: String, val dateOfBirth: String?, val nationality: String?)
data class DriverResponse(val response: Response, val season: Int, val drivers: List<Driver>)

data class Location(val lat: Float?, val lon: Float?, val locality: String?, val country: String?)
data class Circuit(val circuitId: String, val url: String?, val name: String, val location: Location)
data class CircuitResponse(val response: Response, val season: Int, val circuits: List<Circuit>)

class CircuitDeserializer : JsonDeserializer<Circuit>{
    override fun deserialize(element: JsonElement?, typeOfT: Type?, context: JsonDeserializationContext?): Circuit {
        val json = element as JsonObject
        val jsonLocation = json.getAsJsonObject("Location")
        val location = Location(jsonLocation.get("lat")?.asFloat, jsonLocation.get("long")?.asFloat, jsonLocation.get("locality")?.asString, jsonLocation.get("country")?.asString)
        return Circuit(json.get("circuitId").asString, json.get("url")?.asString, json.get("circuitName").asString, location)
    }
}

class DriverDeserializer : JsonDeserializer<Driver> {
    override fun deserialize(element: JsonElement?, typeOfT: Type?, context: JsonDeserializationContext?): Driver {
        val json = element as JsonObject
        return Driver(json.get("driverId").asString, json.get("code")?.asString, json.get("url")?.asString, json.get("givenName").asString, json.get("familyName").asString, json.get("dateOfBirth")?.asString, json.get("nationality")?.asString)
    }

}

class CircuitResponseDeserializer : JsonDeserializer<CircuitResponse> {
    override fun deserialize(
        element: JsonElement?,
        typeOfT: Type?,
        context: JsonDeserializationContext?
    ): CircuitResponse {
        val wrapper = element as JsonObject
        val json = wrapper.getAsJsonObject("MRData")
        val response = json.toResponse()
        val table = json.get("CircuitTable").asJsonObject
        val season = table.get("season").asInt
        val circuitType = object : TypeToken<List<Circuit>>() {}.type
        return CircuitResponse(response, season, context!!.deserialize(table.get("Circuits").asJsonArray, circuitType))

    }

}

class DriverResponseDeserializer : JsonDeserializer<DriverResponse> {

    override fun deserialize(element:  JsonElement?, typeOfT: Type?, context: JsonDeserializationContext?): DriverResponse {

        val wrapper = element as JsonObject
        val json = wrapper.getAsJsonObject("MRData")
        val response = json.toResponse()

        val table = json.get("DriverTable").asJsonObject
        val season = table.get("season").asInt
        val driversType = object : TypeToken<List<Driver>>() {}.type

        return DriverResponse(response, season, context!!.deserialize(table.get("Drivers").asJsonArray, driversType))
    }

}

fun JsonObject.toResponse() : Response {
    return Response(this.get("series").asString, this.get("limit").asInt, this.get("offset").asInt, this.get("total").asInt)
} 
```

Enter fullscreen mode Exit fullscreen mode

你的`Commands.kt`的最终版本应该是这样的:

```
 class DriverCommands : CliktCommand(name = "drivers") {

    val season: Int by option(help = "Season year").int().default(LocalDate.now().year)

    override fun run() {
        val gson = GsonBuilder()
            .registerTypeAdapter(DriverResponse::class.java, DriverResponseDeserializer())
            .registerTypeAdapter(Driver::class.java, DriverDeserializer())
            .create()
        val responseString = Fuel.get("http://ergast.com/api/f1/$season/drivers.json").responseString().third.get()
        val driverResponse = gson.fromJson<DriverResponse>(responseString, DriverResponse::class.java)
        val table = AsciiTable()
        table.addRule()
        table.addRow(null, null, "$season season drivers").setTextAlignment(TextAlignment.CENTER)
        table.addRule()
        table.addRow("Driver name", "Date birth", "Nationality").setTextAlignment(TextAlignment.CENTER)
        table.addRule()
        driverResponse.drivers.forEach {driver ->
            table.addRow("${driver.givenName} ${driver.familyName}", driver.dateOfBirth, driver.nationality).setTextAlignment(TextAlignment.CENTER)

            table.addRule()
        }
        println(table.render())
    }

}

class CircuitCommands : CliktCommand(name="circuits") {
    val season: Int by option(help = "Season year").int().default(LocalDate.now().year)

    override fun run() {
        val gson = GsonBuilder()
            .registerTypeAdapter(CircuitResponse::class.java, CircuitResponseDeserializer())
            .registerTypeAdapter(Circuit::class.java, CircuitDeserializer())
            .create()
        val table = AsciiTable()
        table.addRule()
        table.addRow(null, null, "$season season circuits").setTextAlignment(TextAlignment.CENTER)
        table.addRule()
        table.addRow("Circuit", "Location", "Country").setTextAlignment(TextAlignment.CENTER)
        table.addRule()
        val responseString = Fuel.get("http://ergast.com/api/f1/$season/circuits.json").responseString().third.get()
        val circuitResponse = gson.fromJson<CircuitResponse>(responseString, CircuitResponse::class.java)
        circuitResponse.circuits.forEach {circuit ->
            table.addRow(circuit.name, circuit.location.locality, circuit.location.country).setTextAlignment(TextAlignment.CENTER)
            table.addRule()
        }
        println(table.render())
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

1.  首先，我们通过注册适当的适配器来准备我们的`Gson`实例
2.  `AsciiTable`只是一种展示结果的好方法
3.  我们使用`Fuel`获得字符串形式的响应，然后将其映射到我们的`Response`类型
4.  打印表格。

现在使用`gradle`再次构建项目，但是这次您需要修改`native-image`命令来启用`http`协议(默认情况下禁用):

```
$GRAALVM_BIN/native-image  --report-unsupported-elements-at-runtime  -jar build/libs/f1-1.0-SNAPSHOT-all.jar f1 --enable-url-protocols=http --no-server 
```

Enter fullscreen mode Exit fullscreen mode

构建完成后，尝试运行`drivers`命令:

```
 f1 ./f1 drivers
┌──────────────────────────────────────────────────────────────────────────────┐
│                             2019 season drivers                              │
├──────────────────────────┬─────────────────────────┬─────────────────────────┤
│       Driver name        │       Date birth        │       Nationality       │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Alexander Albon      │       1996-03-23        │          Thai           │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Valtteri Bottas      │       1989-08-28        │         Finnish         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│       Pierre Gasly       │       1996-02-07        │         French          │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│    Antonio Giovinazzi    │       1993-12-14        │         Italian         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Romain Grosjean      │       1986-04-17        │         French          │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│      Lewis Hamilton      │       1985-01-07        │         British         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Nico Hülkenberg      │       1987-08-19        │         German          │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│      Robert Kubica       │       1984-12-07        │         Polish          │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│       Daniil Kvyat       │       1994-04-26        │         Russian         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Charles Leclerc      │       1997-10-16        │       Monegasque        │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Kevin Magnussen      │       1992-10-05        │         Danish          │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│       Lando Norris       │       1999-11-13        │         British         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│       Sergio Pérez       │       1990-01-26        │         Mexican         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│      Kimi Räikkönen      │       1979-10-17        │         Finnish         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Daniel Ricciardo     │       1989-07-01        │       Australian        │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│      George Russell      │       1998-02-15        │         British         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│       Carlos Sainz       │       1994-09-01        │         Spanish         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│       Lance Stroll       │       1998-10-29        │        Canadian         │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│      Max Verstappen      │       1997-09-30        │          Dutch          │
├──────────────────────────┼─────────────────────────┼─────────────────────────┤
│     Sebastian Vettel     │       1987-07-03        │         German          │
└──────────────────────────┴─────────────────────────┴─────────────────────────┘ 
```

Enter fullscreen mode Exit fullscreen mode

# 关闭思绪

看到我们现在可以用 Java 构建令人惊叹的 CLI 工具，我真的很兴奋。我希望这篇文章也让你感到兴奋。不需要分裂你的团队，或者要求他们学习一种新的语言来为你的内部 API 编写一些很酷的工具。

编码快乐！