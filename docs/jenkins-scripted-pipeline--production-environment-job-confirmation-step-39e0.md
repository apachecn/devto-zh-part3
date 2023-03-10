# Jenkins:脚本化管道-生产环境作业确认步骤

> 原文：<https://dev.to/setevoy/jenkins-scripted-pipeline--production-environment-job-confirmation-step-39e0>

[![](img/b0159ef6ab54f04d9c511029efbc58a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZNr-ArAg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2016/01/Jenkins.sh-600x600-e1453134979914.png) 我们有一些作业必须通过[脚本化管道](https://jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline)在开发和生产环境中执行。

在这样的工作中，有在基础设施上执行云信息升级的任务或更新服务器配置的可翻译剧本。

为了避免生产作业的意外执行，我希望在该作业启动并在环境中进行任何更改之前，有一些确认步骤。

让我们用 [`pipeline-input-step`](https://jenkins.io/doc/pipeline/steps/pipeline-input-step/) 和它的类`BooleanParameterDefinition`。

增加一个`verify()`功能:

```
def verify() {
    stage('Verify') {
        def userInput = input(
            id: 'userInput', message: 'This is PRODUCTION!', parameters: [
            [$class: 'BooleanParameterDefinition', defaultValue: false, description: '', name: 'Please confirm you sure to proceed']
        ])

        if(!userInput) {
            error "Build wasn't confirmed"
        }
    }
} 
```

在此处的`if(!userInput)`条件下，将检查`userInput`值是否为*真*或*假*，默认情况下，此处将设置*假*。

现在添加这个函数执行:

```
node {

    switch("${env.LIMIT}") {
        case "backend-production":
            verify()
            echo "Run job"
            break;
        default:
            echo "Dev deploy"
            break;
    }   
} 
```

这里我们检查参数值`LIMIT`，它可以包含一个*后端生产*、*后端开发、*或*后端测试*。

如果它的值等于*后端生产*，那么`verify()`将首先执行，只有在它之后才执行其他构建/部署步骤。

如果有其他的，那么只启动任务。

让我们检查一下——开始一项工作:

[![](img/621ca3c7687cc31d03606bd5563fa9ee.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_182505.png)

出现用户输入请求:

[![](img/865d195bee8446749c176eabccd341ac.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_184514.png)

设置标记，按*继续*:

[![](img/a60cb71b8bf4336dc8de14254719654a.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_184539.png)

并开始构建:

[![](img/73a4367ea639a1f064116bbcc6d7a1a1.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_184554.png)

现在让我们重新运行一次，但此时让我们“忘记”设置一个标记，只需按*继续*:

[![](img/46ef8eae3070e70bacb949659e19db83.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_184651.png)

[![](img/1f2ec194330c0f1b1cec657618d1e22f.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_184705.png)

这里应用了`if(!userInput)`，结果是 [`error`](https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#error-error-signal) 。

最后一次检查——使用*中止*:

[![](img/c49c29fdc9f06e2137b9ad96f74fb501.png "Jenkins: Scripted Pipeline - подтверждение выполнения для Production окружения")](https://rtfm.co.ua/wp-content/uploads/2018/08/Screenshot_20180815_184754.png)

完成了。

### 类似的帖子

*   <small>2017 年 02 月 08 日</small> [詹金斯:沙盒。RejectedAccessException:脚本不允许使用](https://rtfm.co.ua/jenkins-sandbox-rejectedaccessexception-scripts-not-permitted-to-use/) <small>(0)</small>
*   <small>09/22/2017</small>[Jenkins:Pipeline，Groovy，ansi bleиVM provisioning](https://rtfm.co.ua/jenkins-pipeline-groovy-ansible-i-vm-provisioning/)<small>(0)</small>
*   T002/09/2017 t1t2a zure:将附加驱动器连接到 VM 并迁移 Jenkins t3t
*   <small>08/23/2018</small> [詹金斯:包装器脚本似乎没有触及日志文件](https://rtfm.co.ua/jenkins-wrapper-script-does-not-seem-to-be-touching-the-log-file/) <small>(0)</small>