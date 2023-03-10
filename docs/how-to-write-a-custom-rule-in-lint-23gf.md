# 如何用 Lint 编写自定义规则

> 原文：<https://dev.to/dbottillo/how-to-write-a-custom-rule-in-lint-23gf>

在我在 Deliveroo 的团队中，我们最近决定开始使用 Google Truth 而不是 Hamcrest 进行测试断言，但是考虑到代码库中单元测试的数量，将所有测试迁移到 Truth 几乎是不可能的。相反，我们决定将两个库都放在项目中，使用 Truth 进行新的测试，并在接触现有库时进行转换。此外，我们还希望在发出拉取请求时得到有关使用 Hamcrest 的通知，以避免审查者必须手动检查。

因此，我们决定编写一个自定义 lint 规则，以便检测 Hamcrest 导入并将其作为警告报告，因为我们使用了 Danger([https://github.com/danger/danger](https://github.com/danger/danger))它还会报告 pull 请求，这正是我们想要的。

为了编写一个定制的 lint 规则，我们需要创建一个包含该规则的模块，并将其导入到您希望实施该规则的每个模块中。

让我们从创建 lint 模块开始:在 Android Studio 中创建一个名为`lint-rules`的新文件夹，并在其中添加一个`build.gradle`文件:

```
file: lint-rules/build.gradle
apply plugin: 'java-library'
apply plugin: "kotlin"

dependencies {
    compileOnly "com.android.tools.lint:lint-api:26.3.1"
    compileOnly "org.jetbrains.kotlin:kotlin-stdlib-jdk8:1.3.21"
    testImplementation "com.android.tools.lint:lint:26.3.1"
    testImplementation "com.android.tools.lint:lint-tests:26.3.1"
    testImplementation "com.google.truth:truth:0.42"
} 
```

这里没有什么奇怪的，这个模块是一个 Kotlin 库，我们已经定义了 lint、Kotlin 和 Google Truth 的依赖关系(稍后会详细介绍)。我们还需要将模块添加到`settings.gradle`中，以便它可以用于项目的其余部分:

```
file: settings.gradle
include ':app'
include 'lint-rules' 
```

现在我们已经定义了模块，我们可以向它添加代码:)首先我们需要创建一个`IssueRegistry`类，这是 lint 如何让您将您自己的问题检查注入到命令:

```
file: lint-rules/src/main/java/IssueRegistry.kt
class IssueRegistry : com.android.tools.lint.client.api.IssueRegistry() {
    override val issues: List<Issue>
        get() = listOf()

    override val api: Int = com.android.tools.lint.detector.api.CURRENT_API
} 
```

一个`IssueRegistry`需要公开两件事:要检查的问题列表和当前 api 值，lint 文档建议只返回当前值，这样我们就可以专注于问题列表。

Lint 中的`Issue`是什么？看待这个问题的最佳方式是查看关于`Issue`类的 create 方法文档:

```
/**
 * Creates a new issue. The description strings can use some simple markup;
 * see the [TextFormat.RAW] documentation
 * for details.
 *
 * @param id the fixed id of the issue
 * @param briefDescription short summary (typically 5-6 words or less), typically
 * describing the **problem** rather than the **fix**
 * (e.g. "Missing minSdkVersion")
 * @param explanation a full explanation of the issue, with suggestions for
 * how to fix it
 * @param category the associated category, if any
 * @param priority the priority, a number from 1 to 10 with 10 being most
 * important/severe
 * @param severity the default severity of the issue
 * @param implementation the default implementation for this issue
 * @return a new [Issue]
 */ 
```

这很简单！所以我们需要一个 id，一些描述和解释的文本，一个类别，一个优先级，一个严重性和实际的实现。然后让我们创建一个新的`IssueHamcrestImport` :

```
val IssueHamcrestImport = Issue.create("HamcrestImport",
        "Hamcrest is deprecated",
        "Use Google Truth instead",
        CORRECTNESS,
        5,
        Severity.WARNING,
        TODO()
) 
```

这就是我们的问题！所以现在我们可以返回一个包含该问题的列表，但是我们仍然需要用实际的实现来填充`TODO()`部分。
但至少注册表现在完成了:

```
file: lint-rules/src/main/java/IssueRegistry.kt
class IssueRegistry : com.android.tools.lint.client.api.IssueRegistry() {
    override val issues: List<Issue>
        get() = listOf(IssueHamcrestImport)

    override val api: Int = com.android.tools.lint.detector.api.CURRENT_API
}

val IssueHamcrestImport = Issue.create("HamcrestImport",
        "Hamcrest is deprecated",
        "Use Google Truth instead",
        CORRECTNESS,
        5,
        Severity.WARNING,
        TODO()
) 
```

`Issue.create`(或`TODO()`)的最后一个参数是检测器 api 的一个实现，所以让我们创建一个新的检测器:

```
file: lint-rules/src/main/java/HamcrestNamingPatternDetector.kt
class HamcrestNamingPatternDetector : Detector(), Detector.UastScanner {

} 
```

但是到底什么是`Detector`？还是那句话，我们来看看文档:

```
/**
 * A detector is able to find a particular problem (or a set of related problems).
 * Each problem type is uniquely identified as an [Issue].
 *
 * Detectors will be called in a predefined order:
 *
 *  1\.  Manifest file
 *  2\.  Resource files, in alphabetical order by resource type
 *      (therefore, "layout" is checked before "values", "values-de" is checked before
 *      "values-en" but after "values", and so on.
 *  3\.  Java sources
 *  4\.  Java classes
 *  5\.  Gradle files
 *  6\.  Generic files
 *  7\.  Proguard files
 *  8\.  Property files
 */ 
```

有趣的是，`Detector`是一个可以在这些文件/源文件中找到问题的对象。对于我们当前的用例，我们对 sources 类的`imports`感兴趣，但幸运的是我们不必手动解析所有文件，lint 已经为我们做了！我们只需要指定我们感兴趣的:

```
override fun getApplicableUastTypes() = listOf(UImportStatement::class.java) 
```

好的，等一下，什么是`UAST`？让我们再看一下文档:

```
* UAST is short for "Universal AST" and is an abstract syntax tree library
* which abstracts away details about Java versus Kotlin versus other similar languages
* and lets the client of the library access the AST in a unified way. 
```

哇，太神奇了！这意味着我们不必自己解析文件，我们可以使用已经定义好的`UElement` :

```
public interface UImportStatement : org.jetbrains.uast.UResolvable, org.jetbrains.uast.UElement { 
```

那确实提供了使用已经全部进口的东西。精彩！
好了，现在我们需要定义实际的检测器实现:

```
class HamcrestInvalidImportHandler(private val context: JavaContext) : UElementHandler() {
    override fun visitImportStatement(node: UImportStatement) {
        node.importReference?.let { importReference ->
            if (importReference.asSourceString().contains("org.hamcrest.")) {
                context.report(IssueHamcrestImport, node, context.getLocation(importReference), "Forbidden import")
            }
        }
    }
} 
```

所以 Lint 可以为我们提供一个`UElementHandler`，让你访问不同的东西，比如`Class`、`Enum`、`Field`等等..但是这里我们只对进口感兴趣，所以我们可以忽略

```
override fun visitImportStatement(node: UImportStatement) 
```

然后检查`importReference`看看它包含什么，如果它包含`org.hamcrest.`，那么我们可以使用我们之前创建的`IssueHamcrestImport`来报告`JavaContext`对象中的问题。
探测器的完整实现是:

```
class HamcrestNamingPatternDetector : Detector, Detector.UastScanner(){
    override fun getApplicableUastTypes() = listOf(UImportStatement::class.java)

    override fun createUastHandler(context: JavaContext) = HamcrestInvalidImportHandler(context)
}

class HamcrestInvalidImportHandler(private val context: JavaContext) : UElementHandler() {
    override fun visitImportStatement(node: UImportStatement) {
        node.importReference?.let { importReference ->
            if (importReference.asSourceString().contains("org.hamcrest.")) {
                context.report(IssueHamcrestImport, node, context.getLocation(importReference), "Forbidden import")
            }
        }
    }
} 
```

现在回到我们的`Issue`定义，我们可以用实际的探测器
代替`TODO()`

```
val IssueHamcrestImport = Issue.create("HamcrestImport",
        "Hamcrest is deprecated",
        "Use Google Truth instead",
        CORRECTNESS,
        5,
        Severity.WARNING,
        Implementation(HamcrestNamingPatternDetector::class.java,
                EnumSet.of(Scope.JAVA_FILE, Scope.TEST_SOURCES))
) 
```

注意这里我们可以使用一个枚举来定义作用域，这样你就可以定义你想要作用于哪种类型的文件(`JAVA_FILE`也包含 Kotlin 文件)。

缺少的一件事是向 lint 订阅我们的自定义注册表:

```
end of file: lint-rules/build.gradle
jar {
    manifest {
        attributes('Lint-Registry-v2': 'IssueRegistry')
    }
} 
```

最后，我们可以将定制的 lint 检查模块添加到我们想要强制检查的模块:

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    ...all other dependencies...
    lintChecks project(":lint-rules")
} 
```

如果您在一个包含使用 Hamcrest 的测试的项目上运行`./gradlew lint`，现在您应该看到类似这样的内容:
[![Lint output](img/b39da2328f08d52e479bd83d93a6a744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yfg8tLYw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ms3czmsfbikxqrb64qwa.png)

还有一件事，我在开头提到了 lint 规则模块的真值依赖性，因为我们也可以为我们的注册表和检测器编写测试:

```
class IssueRegistryTest {

    @Test
    fun `check explanation for hamcrest import is correct`() {
        val output = IssueRegistry().issues
                .joinToString(separator = "\n") { "- **${it.id}** - ${it.getExplanation(TextFormat.RAW)}" }

        assertThat("""
        - **HamcrestImport** - Use Google Truth instead
        """.trimIndent()).isEqualTo(output)
    }
} 
```

和

```
class HamcrestImportDetectorTest {

    @Test
    fun `should not report imports that are not hamcrest`() {
        TestLintTask.lint()
                .files(TestFiles.java("""
            package foo;
            import foo.R;
            class Example {
            }""").indented())
                .issues(IssueHamcrestImport)
                .run()
                .expectClean()
    }

    @Test
    fun `should warning about hamcrest import`() {
        TestLintTask.lint()
                .files(TestFiles.java("""
          package foo;
          import org.hamcrest.MatcherAssert.assertThat;
          class Example {
          }""").indented())
                .issues(IssueHamcrestImport)
                .run()
                .expect("""
          |src/foo/Example.java:2: Warning: Forbidden import [HamcrestImport]
          |import org.hamcrest.MatcherAssert.assertThat;
          |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          |0 errors, 1 warnings""".trimMargin())
    }
} 
```

`TestLintTask`类让我们测试我们的检测器，创建一个内存文件，这样我们就可以断言检查的输出。

就是这样！你可以在 Github 上找到一个有效的实现:[https://github.com/dbottillo/Blog/tree/hamcrest_lint](https://github.com/dbottillo/Blog/tree/hamcrest_lint)