# 用 Jenkins 管道语法从 CucumberJs 中提取测试结果

> 原文：<https://dev.to/rgeraldporter/extracting-test-results-from-cucumberjs-in-jenkins-pipeline-syntax-49h>

Jenkins 可以是一个强大的持续集成(CI)服务系统，允许复杂任务的自动化，例如在 Dockerized 环境中运行端到端(E2E)测试套件。

然而，你不会自动从詹金斯乔布斯那里获得有用的数据。也许您想从 E2E 运行中获取结果，并将其输入 Slack，当有测试失败时，通知 Datadog 等服务上的监视器。您可能还想使用结果来通知 Jenkins 作业本身的成功/失败报告。

## 假设

本指南假设您正在 Jenkins 管道作业中使用`cucumber-js`(撰写本文时为 v5.1.0)和`cucumber-html-reporter` (v5.0.0)。如果您使用的是另一个 reporter，只要您有一个包含要提取的数据的结果文件，您就可以使用本指南进行一些调整。

这是围绕一个完全在 Docker 中运行测试的 E2E 解决方案设计的，并通过使用`docker cp`将生成的 HTML 报告复制到 Jenkins 工作空间来结束。

我们的`Jenkinsfile`将使用由 Groovy 构建的 Jenkins [脚本化管道语法](https://jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline)。你不需要了解太多关于 Groovy 的知识，因为对于我们正在做的事情来说，它与 JS 足够相似。Groovy 是 Java 的一个([类似于](https://stackoverflow.com/questions/4272068/is-groovy-syntax-an-exact-superset-of-java-syntax/4274542))超集，我们将在这个方法中利用它。

## 法

作为我们的提取方法，我们将解析产生的 HTML 文件，找到包含通过和失败结果的两行，然后使用一些简单的正则表达式匹配来确定那些精确的整数。

这可能看起来有点尴尬，但是解析原始的`cucumber-report.json`文件将会是非常非常多的工作，因为我们必须重新计算结果。

## 实现

在我们的`Jenkinsfile`的顶部，让我们声明一些我们将在这个脚本中使用的变量。

```
#!groovy

int e2ePassed = 0
int e2eFailed = 0
int e2eTotal
int e2ePercent

pipeline {
( ... ) 
```

当我们运行测试的主要任务完成后，我们将从 Cucumber JS HTML 报告中生成一个工件。

我用下面的注释记录了每一行的职责。

```
steps {

    /*
     run test, copy the report file out of docker or where ever it is...
    */

    archiveArtifacts artifacts: "cucumber_report.html", fingerprint: true

    // our Groovy script will be contained in this scope
    script {
        println ('Extracting passed & failed values')

        // get the present working directory (PWD)
        String PWD = pwd()

        // based on this, construct the path to the workspace
        String workspacePath = PWD.substring(PWD.lastIndexOf('/') + 1, PWD.length())

        // read our HTML report into a String
        String report = readFile ("${env.PWD}/workspace/${workspacePath}/cucumber_report.html")

        // split each line break into its own String
        String[] lines = report.split("\n")

        // use regex to find the "passed" line and "failed" line
        def foundPassedLine = lines.find{ line-> line =~ /\<span class="label label-success" title=scenarios\>/ }
        def foundFailedLine = lines.find{ line-> line =~ /\<span class="label label-danger" title=scenarios\>/ }

        // match for the numeric values
        def passedMatch = (foundPassedLine =~ /[0-9]+/)
        def failedMatch = (foundFailedLine =~ /[0-9]+/)

        // cast to Integer so we can work with the number values
        e2ePassed = passedMatch[0] as Integer
        e2eFailed = failedMatch[0] as Integer
        e2eTotal = e2eFailed + e2ePassed
        e2ePercent = e2ePassed / e2eTotal * 100

        if (e2eFailed > 0) {
            // do something extra if there are failures
        }

        // print out our results to the console
        println ("Passed: ${e2ePassed}")
        println ("Failed: ${e2eFailed}")
        println ("Percent passes: ${e2ePercent}%")
    }

    // we can also access these values in an `sh` declaration (or other functions) outside of `script` now
    sh "echo \"Passed: ${e2ePassed}\""
    sh "echo \"Failed: ${e2eFailed}\""
    sh "echo \"Percent passes: ${e2ePercent}%\""

    // you may opt to use the vars in plugins like `slackSend`, etc
} 
```

**注意**:最新 Jenkins 设置的安全设置在第一次使用`readFile`时会触发错误。发生这种情况时，转到`Manage Jenkins`和`In-process Script Approval`。您将看到一个提示，允许 readFile 在所有未来的作业运行中用于特定的 Jenkinsfile。您必须是管理员才能批准此操作。

一旦您从报告器中提取了这些数据，您就可以使用它在出现故障时通知外部系统，或者跟踪测试通过/失败的度量。

*最初发布于[https://robporter.ca/jenkins-cucumber/](https://robporter.ca/jenkins-cucumber/)T3】*