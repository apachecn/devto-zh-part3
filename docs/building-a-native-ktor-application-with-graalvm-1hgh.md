# 用 GraalVM 构建原生 ktor 应用程序

> 原文：<https://dev.to/viniciusccarvalho/building-a-native-ktor-application-with-graalvm-1hgh>

这篇文章的源代码可以在这个 [github repo](https://github.com/viniciusccarvalho/ktor-native) 上找到

# 简介

在我的上一篇文章:[用 kotlin 和 graalvm 构建原生 cli](https://dev.to/viniciusccarvalho/building-a-native-cli-with-kotlin-and-graalvm-55ee)中，我演示了如何使用 graalvm 构建原生 CLI 应用程序。在这篇文章中，我们将使用 [ktor.io](https://ktor.io) 和 [GraalVM](https://graalvm.org) 构建一个 web 应用程序。

# 要求

*   安装了 GraalVM ,并且$GRAALVM_BIN 指向您安装的二进制文件夹

# 建造它

Ktor 支持几种服务器选项(Netty、Jetty、CIO)。Netty 是我最喜欢的 java 应用程序选项，但是试图让 netty 与 GraalVM 一起工作是相当痛苦的，正如这里的[所解释的](https://medium.com/graalvm/instant-netty-startup-using-graalvm-native-image-generation-ed6f14ff7692)

所以我尝试了 CIO 服务器(仅利用 kotlin 代码和协程)。然后我发现 GraalVM 不支持由协程生成的字节码。

幸运的是，经过一些谷歌搜索后，我遇到了似乎可以解决问题的 kraal。

该示例将使用 Gradle Kotlin DSL，只需在 intellij 上创建一个新的 Gradle 项目，并标记“使用 Kotlin DSL”

从那里修改你的 build.gradle.kts，看起来像这样:

```
import org.gradle.jvm.tasks.Jar
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    kotlin("jvm") version "1.3.21"
    id("com.hpe.kraal") version "0.0.15" // kraal version - for makeRelease.sh
}

group = "io.igx.kotlin"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral()
    jcenter()
}

dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation("org.slf4j:slf4j-simple:1.7.26")
    implementation("io.ktor:ktor-server-cio:1.1.3")
    implementation("io.ktor:ktor-gson:1.1.3")
}

tasks.withType<KotlinCompile>().configureEach {
    kotlinOptions {
        jvmTarget = "1.8"
        // need use-experimental for Ktor CIO
        freeCompilerArgs += listOf("-Xuse-experimental=kotlin.Experimental", "-progressive")
        // disable -Werror with: ./gradlew -PwarningsAsErrors=false
        allWarningsAsErrors = project.findProperty("warningsAsErrors") != "false"
    }
}

val fatjar by tasks.creating(Jar::class) {

    from(kraal.outputZipTrees) {
        exclude("META-INF/*.SF")
        exclude("META-INF/*.DSA")
        exclude("META-INF/*.RSA")
    }

    manifest {
        attributes("Main-Class" to "io.igx.kotlin.ApplicationKt")
    }

    destinationDirectory.set(project.buildDir.resolve("fatjar"))
    archiveFileName.set("ktor-native.jar")
}

tasks.named("assemble").configure {
    dependsOn(fatjar)
} 
```

像往常一样，只需编辑 group/Main-Class 以匹配您自己的打包结构。

让我们确保我们的应用程序输出一些基于域类的 JSON:

```
data class Driver(val id: Int, val firstName: String, val lastName: String, val nationality: String) 
```

你的`Application`应该是这样的:

```
fun main(args: Array<String>) {
    val server = embeddedServer(CIO, 8080, module = Application::module)
    server.start(wait = true)
}

fun Application.module() {
    install(CallLogging)
    install(ContentNegotiation) {
        gson {
            setPrettyPrinting()
        }
    }
    routing {
       get("/drivers"){
           call.respond(Driver(102, "Ayrton", "Senna", "Brazilian"))
       }
    }
} 
```

# 测试

运行`./gradlew clean build`，然后`java -jar build/fatjar/ktor-native.jar`，你的应用程序应该已经运行:

```
[DefaultDispatcher-worker-1] INFO ktor.application - No ktor.deployment.watch patterns specified, automatic reload is not active
[DefaultDispatcher-worker-1] INFO ktor.application - Responding at http://0.0.0.0:8080 
```

点击`http://0.0.0.0:8080/drivers`

```
{  "id":  102,  "firstName":  "Ayrton",  "lastName":  "Senna",  "nationality":  "Brazilian"  } 
```

# 原生形象

和我们之前的帖子一样，运行`native-image`命令:

```
$GRAALVM_BIN/native-image  --report-unsupported-elements-at-runtime --jar build/fatjar/ktor-native.jar ktor-native --enable-url-protocols=http --no-server 
```

运行`./ktor-native`，点击`http://0.0.0.0:8080/drivers`，你将得到:

```
{} 
```

[![Say Whaaat?](img/10a7442f82646626910d03dcee98b5a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wv6uV3Nz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://api.ning.com/files/iHTeMy8PBD0esxtcJ8dNEjiNZqKEUP-vf9pg5%2AvyP1JWZ-RmSMcNDTkQ7-rHCvbYbM8AGTY1-iQAarBw3mSrddaandhna1-x/saywhatanchorman.jpg)

# 反思问题

因此，GraalVM 需要一些帮助来处理反射，如这里的[所解释的](https://github.com/oracle/graal/blob/master/substratevm/REFLECTION.md)。

将以下文件添加到项目的根目录(reflection.json)

```
 [  {  "name"  :  "io.igx.kotlin.model.Driver",  "allDeclaredConstructors"  :  true,  "allPublicConstructors"  :  true,  "allDeclaredMethods"  :  true,  "allPublicMethods"  :  true,  "fields"  :  [  {  "name"  :  "id"  },  {  "name"  :  "firstName"  },  {  "name"  :  "lastName"  },  {  "name"  :  "nationality"  }  ]  },  {  "name"  :  "java.lang.Integer",  "methods"  :  [{  "name"  :  "parseInt",  "parameterTypes"  :  ["java.lang.String"]}]  },  {  "name"  :  "java.lang.Long",  "methods"  :  [{  "name"  :  "parseLong",  "parameterTypes"  :  ["java.lang.String"]}]  },  {  "name"  :  "java.lang.Boolean",  "methods"  :  [{  "name"  :  "parseBoolean",  "parameterTypes"  :  ["java.lang.String"]}]  },  {  "name"  :  "java.lang.Byte",  "methods"  :  [{  "name"  :  "parseByte",  "parameterTypes"  :  ["java.lang.String"]}]  },  {  "name"  :  "java.lang.Short",  "methods"  :  [{  "name"  :  "parseShort",  "parameterTypes"  :  ["java.lang.String"]}]  },  {  "name"  :  "java.lang.Float",  "methods"  :  [{  "name"  :  "parseFloat",  "parameterTypes"  :  ["java.lang.String"]}]  },  {  "name"  :  "java.lang.Double",  "methods"  :  [{  "name"  :  "parseDouble",  "parameterTypes"  :  ["java.lang.String"]}]  }  ] 
```

再次运行`native-image`命令，但是这次添加了**-H:ReflectionConfigurationFilesflag**T3】

```
~/java/graalvm-ce-1.0.0-rc13/Contents/Home/bin/native-image  --report-unsupported-elements-at-runtime -H:ReflectionConfigurationFiles=reflection.json  -jar build/fatjar/example.jar ktor-native --enable-url-protocols=http --no-server 
```

再试一次，你会看到你的驱动 JSON 有效载荷的原始结果。

# 最后的想法

在 GraalVM 上处理反射可能有点挑战性，幸运的是，业内的一些人，比如 RedHat 的人，正试图提出像 [quarkus.io](https://quarkus.io) 这样的框架来克服这种限制。

我毫不怀疑，ide 将很快赶上来，能够检查代码并为我们生成那些反射文件。

同时，享受你的高速 ktor 服务器在你的服务器上作为本地镜像运行。

编码快乐！