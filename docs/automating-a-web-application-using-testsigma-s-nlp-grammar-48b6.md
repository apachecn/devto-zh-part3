# 使用 Testsigma 的 NLP 语法自动化 Web 应用程序

> 原文：<https://dev.to/lvnya_c/automating-a-web-application-using-testsigma-s-nlp-grammar-48b6>

本文解释了如何使用基于 NLP 的测试自动化工具 **[Testsigma](https://testsigma.com)** 来自动化一个示例 web 应用程序，如 [OrangeHRM](https://opensource-demo.orangehrmlive.com/) (一个人力资源和休假管理系统)。

[![](img/f1b5a734bf352a767d819d6f27dd1ec9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lQ2C9I3U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/EUrKZf2.png)

我们将执行以下操作:

*   导航到 OrangeHRM 网站
*   在用户名字段中输入用户名(Admin)
*   在密码字段中输入密码(admin123)
*   点击登录

When you [create a trial account](https://testsigma.com/signup) with Testsigma, you will have a default Project named, **Testsigma** with a few samples. You may create a new Test Case in the existing Project or create a new Project and add test cases to this.

现在点击 Add Test Steps 开始为我们需要执行的 4 个步骤中的每一个编写测试步骤。

## 步骤 1:导航到应用程序

第一步很容易。只需键入/复制并粘贴您希望自动化的应用程序的 URL 到 Go To 字段，然后点击 Start！

[![](img/d0e2661731c3bc98245452a534076471.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x5JEKDq3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Enter_URL_Testsigma.png) 

第一个测试步骤会自动添加。

[![](img/d0720abac3a8ab5bd0503fbe14f04bf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VjR2Tm67--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Add_Next_Test_Step_Testsigma.png)

## 第 2 步:输入用户名

现在，为了测试这个应用程序的登录场景。我们将从输入用户名开始。

开始输入，“回车”就会出现一个语法建议列表。
从列表中选择 NLP 语法，*“在 ui 标识符字段中输入测试数据”*，因为这与我们需要执行的内容密切相关。

[![](img/ac861544d65f2aa63271b70ca07ba775.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8xJOWoAV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Enter_Action_Testsigma.png)

我们在语法中看到两个关键字；一个**测试数据**和一个 **ui 标识符**。“测试数据”是我们提供的输入，如您的姓名或本例中的用户名等。

“ui 标识符”是我们用来“标识”网站或网页上任何位置的元素(用户名字段)的路径，以便在网页上对该元素执行任何操作。

现在，您只需要用值“*管理员*”替换**测试数据**部分。此外，用一些有意义的变量替换 **ui 标识符**部分，比如“ *UsernameLogin* ”。我们将把用户名字段的唯一 ui 标识符/路径分配给这个变量。

[![](img/55d678dcf8ad3ef0489b89b2331712c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zpKPRKug--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Edit_ui_identifier_Testsigma.png)

您也可以跳过这一部分，继续添加测试步骤，稍后编辑 ui 标识符值。如果您正在 sprint 中执行[测试自动化，这将特别有用。](https://dzone.com/articles/in-sprint-test-automation-at-agile-and-dev-ops-spe)

让我们马上为 ui 标识符赋值。如果您熟悉创建 XPath，您可以手动[创建和存储 ui 标识符值，或者您可以使用 Testsigma 的 Chrome 扩展，它将自动为您识别和存储用户名字段的确切路径。以下是你如何使用](https://youtu.be/zVCCduIjOXs) [Chrome 扩展](https://youtu.be/Q2QOmclAx2A)来实现这一点。

[![](img/401f43530bec4f161893d0e08db168c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ec4ZlHcG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Ui_identifier_values_Testsigma.png)

现在，单击“创建并继续”继续添加后续测试步骤。

## 第三步:输入密码

现在，在密码字段中输入密码。使用与用户名相同的 NLP 语法，“在 ui 标识符字段中输入测试数据”。
用 *admin123* 替换测试数据，用某个变量替换 ui 标识符，比如， *PasswordforLogin* 。即“在密码登录字段中输入 admin123”。

像以前一样，您可以使用 Chrome 扩展来获取 ui 标识符“PasswordforLogin”的 ui 标识符值，或者您可以尝试通过检查 Password field 元素来手动输入 ui 标识符值。

点击创建并继续。

## 步骤 4:执行点击操作

现在，让我们看看如何执行点击操作来执行登录。在这种情况下，不需要输入测试数据值，对吗？它只是点击登录按钮。
所以，NLP 语法只是“*点击 ui 标识符*”。将 ui 标识符替换为一个名称，比如“Login ”,并像我们之前所做的那样分配它的 ui 标识符值。

点击创建。

## 第五步:验证一些东西

这里有一个额外的步骤！让我们也验证一些文本是否显示在页面中。
点击添加下一步。你只需要使用语法，“*验证当前页面 URL 是测试数据*”。你也可以尝试其他的 NLP 语法建议。用你的期望值代替测试数据。

[![](img/878873990fbba6a6bacac05d76e9c06a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h9XlBlKb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Test_Steps_Testsigma.png)

要知道使用什么语法，只需参考工具左窗格的 NLP 语法链接或参考帮助部分(a **？**测试步骤右侧的符号)获取与要执行的操作(如输入或点击)相关的示例。

## 执行测试步骤

现在，让我们运行这 5 个测试步骤。只需点击测试步骤页面右上角的 **Run** 来快速执行这个测试用例，然后点击 Create and Run。

测试运行后，检查运行结果。你会看到详细的测试日志，截图和视频的执行细节。

[![](img/b31913663720b6d26b2fe9d9822cf197.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FhgTx42T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://assets.testsigma.com/external/product/11-2019/Detailed_Run_Results_Testsigma.png)

如果你想更详细地了解这一点，用 **[Testsigma](https://testsigma.com)** 创建一个[免费试用](https://testsigma.com/signup)账户 30 天，看看它是否对你有用。