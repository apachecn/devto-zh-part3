# 如何设置 Android 项目

> 原文：<https://dev.to/onmyway133/how-to-setup-android-projects-11pn>

这篇文章详细介绍了安装 Java 和 Kotlin 的检查工具，以及混淆和 gradle grgit 脚本。

## checkstyle (Java)

*   [http://checkstyle.sourceforge.net/](http://checkstyle.sourceforge.net/)
*   [https://github . com/checksstyle/checksstyle](https://github.com/checkstyle/checkstyle)

> Checkstyle 是一个开发工具，帮助程序员编写符合编码标准的 Java 代码。它自动化了检查 Java 代码的过程，将人类从这种无聊(但重要)的任务中解放出来。这使得它非常适合于想要实施编码标准的项目。

app/build.gradle

```
apply plugin: 'checkstyle'

task checkstyle(type: Checkstyle) {
    description 'Check code standard'
    group 'verification'

    configFile file('$project.rootDir/tools/checkstyle.xml')
    source 'src'
    include '**/*.kt'
    exclude '**/gen/**'

    classpath = files()
    ignoreFailures = false
} 
```

工具/检查样式

```
<?xml version="1.0"?><!DOCTYPE module PUBLIC
        "-//Puppy Crawl//DTD Check Configuration 1.2//EN"
        "http://www.puppycrawl.com/dtds/configuration_1_2.dtd">
<module name="Checker">
    <module name="FileTabCharacter"/>
    <module name="TreeWalker">

        <!-- Checks for Naming Conventions                            -->
        <!-- See http://checkstyle.sourceforge.net/config_naming.html -->
        <module name="MethodName"/>
        <module name="ConstantName"/>

        <!-- Checks for Imports                                 -->
        <!-- See http://checkstyle.sourceforge.net/config_imports.html-->
        <module name="AvoidStarImport"/>
        <module name="UnusedImports"/>

        <!-- Checks for Size                                          -->
        <!-- See http://checkstyle.sourceforge.net/config_sizes       -->
        <module name="ParameterNumber">
            <property name="max" value="6"/>
        </module>

        <!-- other rules ignored for brevity -->
    </module>
</module> 
```

## findbugs (Java)

*   [http://findbugs.sourceforge.net/](http://findbugs.sourceforge.net/)

> 使用静态分析来查找 Java 代码中的错误的程序

app/build.gradle

```
apply plugin: 'findbugs'

task findbugs(type: FindBugs) {
    ignoreFailures = false
    effort = "max"
    reportLevel = "low"
    classes = files("$project.buildDir/intermediates/javac")

    excludeFilter = file("$rootProject.rootDir/tools/findbugs-exclude.xml")

    source = fileTree('src/main/java/')
    classpath = files()

    reports {
        xml.enabled = false
        html.enabled = true
        html.destination file("$project.buildDir/outputs/findbugs/findbugs-output.html")
    }
} 
```

tools/findbugs-exclude.xml

```
<FindBugsFilter>
    <!-- Do not check auto-generated resources classes -->
    <Match>
        <Class name="~.*R\$.*"/>
    </Match>

    <!-- Do not check auto-generated manifest classes -->
    <Match>
        <Class name="~.*Manifest\$.*"/>
    </Match>

    <!-- Do not check auto-generated classes (Dagger puts $ into class names) -->
    <Match>
        <Class name="~.*Dagger*.*"/>
    </Match>

    <!-- http://findbugs.sourceforge.net/bugDescriptions.html#ST_WRITE_TO_STATIC_FROM_INSTANCE_METHOD-->
     <Match>
        <Bug pattern="ST_WRITE_TO_STATIC_FROM_INSTANCE_METHOD" />
    </Match>
</FindBugsFilter> 
```

## pmd (Java)

*   [https://github.com/pmd/pmd](https://github.com/pmd/pmd)

> PMD 是一个源代码分析器。它发现了常见的编程缺陷，如未使用的变量、空的 catch 块、不必要的对象创建等等

app/build.gradle

```
apply plugin: 'pmd'

task pmd(type: Pmd) {
    ruleSetFiles = files("${project.rootDir}/tools/pmd-rules.xml")
    ignoreFailures = false
    ruleSets = []

    source 'src'
    include '**/*.kt'
    exclude '**/gen/**'

    reports {
        xml.enabled = false
        html.enabled = true
        html.destination = file("$project.buildDir/outputs/pmd/pmd.html")
    }
} 
```

tools/pmd-rules.xml

```
<?xml version="1.0"?>
<ruleset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://pmd.sourceforge.net/ruleset/2.0.0"
         xsi:schemaLocation="http://pmd.sourceforge.net/ruleset/2.0.0 http://pmd.sourceforge.net/ruleset_2_0_0.xsd">
    <exclude-pattern>.*/R.java</exclude-pattern>
    <exclude-pattern>.*/gen/.*</exclude-pattern>

    <rule ref="rulesets/java/basic.xml" />

    <rule ref="rulesets/java/braces.xml" />

    <rule ref="rulesets/java/strings.xml" />

    <rule ref="rulesets/java/design.xml" >
        <exclude name="AvoidDeeplyNestedIfStmts"/>
    </rule>

    <rule ref="rulesets/java/unusedcode.xml" />

</ruleset> 
```

## [功能区](#lint)

app/build.gradle

```
android {
  lintOptions {
        lintConfig file("$project.rootDir/tools/lint-rules.xml")
        htmlOutput file("$project.buildDir/outputs/lint/lint.html")
        warningsAsErrors true
        xmlReport false
        htmlReport true
        abortOnError false
    }
} 
```

tools/lint-rules.xml

```
<?xml version="1.0" encoding="UTF-8"?>

<lint>
    <issue id="GoogleAppIndexWarning" severity="ignore" />

    <issue id="InvalidPackage" severity="error">
        <ignore regexp="okio.*jar" />
        <ignore regexp="retrofit.*jar" />
    </issue>

    <!-- Disable the given check in this project -->
    <issue id="IconMissingDensityFolder" severity="ignore" />

    <!-- Change the severity of hardcoded strings to "error" -->
    <issue id="HardcodedText" severity="error" />
</lint> 
```

## 严格模式

*   [https://developer . Android . com/reference/Android/OS/strict mode](https://developer.android.com/reference/android/os/StrictMode)

```
import android.app.Application
import android.os.StrictMode

class App: Application() {
    override fun onCreate() {
        super.onCreate()

        enableStrictMode()
    }

    private fun enableStrictMode() {
        if (BuildConfig.DEBUG) {
            StrictMode.setThreadPolicy(
                StrictMode.ThreadPolicy.Builder()
                    .detectAll()
                    .penaltyLog()
                    .build()
            )

            StrictMode.setVmPolicy(
                StrictMode.VmPolicy.Builder()
                    .detectAll()
                    .penaltyLog()
                    .build()
            )
        }
    }
} 
```

## 版本代码

tools/grgit.gradle

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'org.ajoberstar:grgit:1.5.0'
    }
}

import org.ajoberstar.grgit.Grgit

ext {
    git = Grgit.open(currentDir: projectDir)
    gitCommitCount = git.log().size()
}

task printVersion() {
    println("Commit count: $gitCommitCount")
} 
```

app/build.gradle

```
android {
    defaultConfig {
        versionCode gitCommitCount
    }
} 
```

## 混淆视听

*   [https://developer.android.com/studio/build/shrink-code](https://developer.android.com/studio/build/shrink-code)

> 为了让你的应用程序尽可能的小，你应该在你的发布版本中启用收缩来移除未使用的代码和资源。启用收缩时，您还可以从模糊处理和优化中受益，模糊处理可以缩短应用程序的类和成员的名称，优化可以应用更激进的策略来进一步减小应用程序的大小
> 
> 当你使用 Android Studio 3.4 或 Android Gradle plugin 3.4.0 和更高版本时，R8 是默认的编译器，可以将你的项目的 Java 字节码转换成运行在 Android 平台上的 DEX 格式

app/build.gradle

```
android {
  buildTypes {
        debug {
            signingConfig signingConfigs.debug
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), '$project.rootDir/tools/proguard-rules-debug.pro'
        }

        release {
            signingConfig signingConfigs.release
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), '$project.rootDir/tools/proguard-rules.pro'
        }
    }
} 
```

tools/proguard-rules.pro

```
-ignorewarnings

# Remove logs
-assumenosideeffects class android.util.Log {
    public static boolean isLoggable(java.lang.String, int);
    public static int v(...);
    public static int i(...);
    public static int w(...);
    public static int d(...);
    public static int e(...);
} 
```

tools/proguard-rules-debug . pro

```
-ignorewarnings
-dontobfuscate
-dontoptimize
-ignorewarnings 
```

更多的进步片段[https://github.com/krschultz/android-proguard-snippets](https://github.com/krschultz/android-proguard-snippets)

## 质量

工具/质量等级

```
task findbugs
task pmd
task checkstyle 
```

app/build.gradle

```
apply from: "$project.rootDir/tools/quality.gradle" 
```

## 文件格式

*   [https://twitter.com/kotlin/status/1067825173196414976?lang=en](https://twitter.com/kotlin/status/1067825173196414976?lang=en)

> 您不需要使用 ktlint 或 detekt 来确保代码的格式一致。只需在检查设置中启用“文件未根据项目设置格式化”。

## ktlint(科特林)

*   [https://github.com/pinterest/ktlint](https://github.com/pinterest/ktlint)

> 内置格式化器的防自行车脱落 Kotlin 棉绒

app/build.gradle

```
apply from: "$project.rootDir/tools/ktlint.gradle" 
```

tools/ktlint.gradle

```
repositories {
    jcenter()
}

configurations {
    ktlint
}

dependencies {
    ktlint "com.pinterest:ktlint:0.32.0"
    // additional 3rd party ruleset(s) can be specified here
    // just add them to the classpath (e.g. ktlint 'groupId:artifactId:version') and
    // ktlint will pick them up
}

task ktlint(type: JavaExec, group: "verification") {
    description = "Check Kotlin code style."
    classpath = configurations.ktlint
    main = "com.pinterest.ktlint.Main"
    args "src/**/*.kt", "--reporter=checkstyle, output=${buildDir}/outputs/ktlint.xml"
}

task ktlintFormat(type: JavaExec, group: "formatting") {
    description = "Fix Kotlin code style deviations."
    classpath = configurations.ktlint
    main = "com.pinterest.ktlint.Main"
    args "-F", "src/**/*.kt"
} 
```

.editorconfig

```
[*.{kt,kts}]
indent_size=4 
```

## [检测(锅炉)](#detekt-kotlin)

*   [https://github.com/arturbosch/detekt](https://github.com/arturbosch/detekt)

> Kotlin 的静态代码分析

build.gradle

```
buildscript {}

plugins {
    id "io.gitlab.arturbosch.detekt" version "1.0.0-RC14"
}

allprojects {} 
```

tools/detekt.gradle

```
detekt {
    toolVersion = "1.0.0-RC14"
    input = files("src/main")
    filters = ".*/resources/.*,.*/build/.*"
    baseline = file("${project.rootDir}/tools/detekt-baseline.xml")
    config = files(file("$project.rootDir/tools/detekt.yml"))
} 
```

tools/detekt.xml

*   [https://github . com/arturbosch/detekt/blob/master/docs/pages/baseline . MD](https://github.com/arturbosch/detekt/blob/master/docs/pages/baseline.md)

> 白名单的目的是在进一步分析时只打印新的代码气味。黑名单可以用来记下误报检测(而不是抑制它们并替换您的代码库)。

```
<SmellBaseline>
    <Blacklist>
        <ID>CatchRuntimeException:Junk.kt$e: RuntimeException</ID>
    </Blacklist>
    <Whitelist>
        <ID>NestedBlockDepth:Indentation.kt$Indentation$override fun procedure(node: ASTNode)</ID>
        <ID>TooManyFunctions:LargeClass.kt$io.gitlab.arturbosch.detekt.rules.complexity.LargeClass.kt</ID>
        <ID>ComplexMethod:DetektExtension.kt$DetektExtension$fun convertToArguments(): MutableList&lt;String&gt;</ID>
    </Whitelist>
</SmellBaseline> 
```

tools/detekt.yml

*   [https://github . com/arturbosch/detekt/blob/master/detekt-CLI/src/main/resources/default-detekt-config . yml](https://github.com/arturbosch/detekt/blob/master/detekt-cli/src/main/resources/default-detekt-config.yml)
*   [https://arturbosch.github.io/detekt/configurations.html](https://arturbosch.github.io/detekt/configurations.html)

> detekt 使用 yaml 风格的配置文件做各种事情:

```
autoCorrect: true

build:
  maxIssues: 10
  weights:
    # complexity: 2
    # LongParameterList: 1
    # style: 1
    # comments: 1 
```

运行

```
./gradlew detekt 
```

## 检查

app/build.gradle

```
check.dependsOn 'checkstyle', 'findbugs', 'pmd', 'lint', 'ktlint', 'detekt' 
```

运行

```
./gradlew check 
```

## 引用

*   [配置 Android 项目——重要的小事](https://proandroiddev.com/configuring-android-project-little-things-that-matter-d6a9d34c1ce0)
*   [利用静态分析工具确保高质量的 Android 代码](https://code.tutsplus.com/tutorials/ensure-high-quality-android-code-with-static-analysis-tools--cms-28787)
*   [科特林静态分析——为什么和如何？](https://proandroiddev.com/kotlin-static-analysis-why-and-how-a12042e34a98)

原帖[https://github.com/onmyway133/blog/issues/257](https://github.com/onmyway133/blog/issues/257)