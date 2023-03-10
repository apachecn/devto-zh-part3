# Windows 上带有 AWS 联邦的 Git 和 SourceTree 设置

> 原文：<https://dev.to/katiekodes/git-and-sourcetree-setup-with-aws-federation-on-windows-1ek5>

您的 Amazon Web Services 帐户有“单点登录”功能，但需要设置您的计算机来与他们的 CodeCommit git 存储库主机对话吗？如果你的电脑上安装了 Windows、Python 和 Git，这里告诉你如何在不弄乱你连接的任何其他普通的旧用户名和密码库的情况下做到这一点！

这些我都没想出来——我只是把它转录成“给假人”
*(也就是我的电脑被取代后的未来的自己)*。

## 总结

下面是我们将要告诉 Git 如何与 Amazon 对话的要点:

1.  我们将设置一种简单的方法，每小时向`c:\users\MY_USERNAME\.aws\credentials`中放入一次新内容*(也就是说，我们将使运行一个 300 行的 Python 脚本变得容易，这个脚本专门为我们提供一个漂亮的小“用户名”&“密码”提示)*
2.  我们将告诉 Git 安装程序，AWS CloudCommit 上托管的任何存储库都应该使用 AWS CLI 来方便登录，而不是使用用户名和密码，而是继续使用用户名和密码进行其他操作
3.  我们将把“AWS CLI”安装到我们的计算机上，并告诉 Git 在执行命令“`aws`”时如何找到它

### 等级:中级+级

这里并没有涵盖所有内容。我假设您有独立完成以下工作的必要经验，并且您拥有计算机的管理员权限，可以方便地安装软件:

1.  舒适地使用命令行
2.  设置 Git / SourceTree，并在硬盘上的存储库和另一台机器上的普通“远程”存储库之间建立工作关系*(当连接到“远程”时，只需按照 Git 或 SourceTree 的提示输入用户名和密码即可)*
3.  编辑您的 Windows“路径”环境变量
4.  在你的电脑上安装 Python，并演示你的电脑能够执行`print('Hello World')` *(即使只是通过像 Spyder 这样的 IDE)*
5.  在您安装/配置为能够运行`pip` *(例如“Anaconda Prompt”)*的任何命令行环境中，使用`pip`命令安装您尚未安装的 Python 模块，作为 Python 安装的一部分

* * *

[![Screenshot of a web browser's URL bar](img/97bf6df1365232d2c3190f7be0eb4a00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZG2M64LC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/urlbar.png)

## 获取存储库的 URL

1.  只有“n . Virginia”(“East 1”)是所有 CodeCommit 现在居住的地方。所以查看你的知识库的网址是:[https://console.aws.amazon.com/codecommit/home?地区=美国东部-1](https://console.aws.amazon.com/codecommit/home?region=us-east-1)
2.  虽然首先，要登录，你可能必须去:[https://SOME-SORT-OF-WEB-SITE-YOUR-COMPANY-GIVES-YOU.com](https://SOME-SORT-OF-WEB-SITE-YOUR-COMPANY-GIVES-YOU.com)
3.  在左侧导航中，展开“Source”并点击“Repositories”[https://console . AWS . Amazon . com/code suite/code commit/Repositories？地区=美国东部-1](https://console.aws.amazon.com/codesuite/codecommit/repositories?region=us-east-1)
4.  找到您有权访问的存储库，然后单击它。[https://console . AWS . Amazon . com/code suite/code commit/repositories/OUR _ AWESOME _ REPOSITORY/browse？地区=美国东部-1](https://console.aws.amazon.com/codesuite/codecommit/repositories/OUR_AWESOME_REPOSITORY/browse?region=us-east-1)
5.  最右边，靠近页面顶部，但不在顶部导航中，有一个按钮和一个下拉框，上面写着“克隆网址”下拉并选择“连接步骤”[https://console . AWS . Amazon . com/code suite/code commit/repositories/OUR _ AWESOME _ REPOSITORY/connect？地区=美国东部-1](https://console.aws.amazon.com/codesuite/codecommit/repositories/OUR_AWESOME_REPOSITORY/connect?region=us-east-1)
6.  如果你在说明的顶部看到一条警告，并有以下文字，那么这篇博文就是为你写的！ >您使用联合访问或临时凭证登录。这些登录类型唯一支持的连接方法是使用 AWS CLI 附带的凭据管理器，如下所述。要通过 HTTPS 使用 SSH 或 Git 凭据配置连接，请以 IAM 用户身份登录。
7.  仍然应该有一个“克隆 URL”下拉列表，它位于页面上与上一页相同的区域。放下它，选择“克隆 HTTPS”
8.  一个绿色的警告栏应该出现在页面的顶部，在顶部导航的下面说: >复制> >[https://git-code commit . us-east-1 . amazonaws . com/v1/repos/OUR _ AWESOME _ REPOSITORY](https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY)
9.  将存储库 URL 放在某个地方...你以后会需要它的。

* * *

[![Git logo](img/bbd5800f4955b4a22dccd917fe203452.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OjZAcybE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-scm.cimg/logos/downloads/Git-Logo-2Color.png)

## 在你的电脑上安装 Git & SourceTree，如果你还没有的话

测试您的 Git 设置是否能与您过去已经使用过的“普通”远程存储库一起工作。

*   在 Git Bash 命令行界面中使用“`git fetch --all`”，确保提示您输入密码，然后保存
*   编辑自述文件或博客中的帖子，将更改推送到 SourceTree，并确保它们生效

如果这些都不管用，就把它们都解决掉。

* * *

[![Screenshot of a gitconfig file icon](img/2a59edf6cabb1550e8d65630286f4ff0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gYUqHePz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/gitconfig-fileicon.png)

## 编辑 Git 安装的“全局”GitConfig 文件

您将需要编辑您的“全局”GitConfig 文件来包含这些行:

```
[credential]
    helper = 

[credential "https://github.com"]
    helper = 

[credential "https://bitbucket.org"]
    helper = 

[credential "https://git-codecommit.us-east-1.amazonaws.com"]
    helper = "!aws codecommit credential-helper "
    UseHttpPath = true 
```

我的储存在 **`C:\users\MY_USERNAME\`** 文件夹中，是一个名为 **`.gitconfig`** 的文件

**再次用“普通”遥控器测试你的 git 设置**以确保你没有破坏任何东西。

*(关闭&保存 GitConfig 内容后，测试前重新打开 SourceTree。)*

如果你不想让 Git Bash 在你每次做`fetch`、`push`、`pull`、`clone`等动作时提示你输入密码，你可以对 GitHub 和 BitBucket 说`helper = wincred`而不是`helper =`。

如果你曾经意识到你后悔在 Windows 凭证管理器中存储给定帐户的密码，只需键入 Windows 密钥，开始搜索"**管理 windo...**，【点击控制面板 app】**管理 Windows 凭证**您可以浏览列表，并根据给定的存储库删除您的凭据。

* * *

[![Screenshot of an Admit One ticket](img/9a14f30543232c1638b39a377b63f9df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--48-e8SrY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/admitone.png)

## 从 AWS 获取每小时令牌

[蒂姆·沙利文创建了一个方便的 Python 脚本](https://github.com/tjsullivan1/aws_saml_auth)，它可以让你从 AWS 获取一个新的凭证，并将其转储到硬盘上 AWS 的“命令行界面”工具寻找这些东西的部分，称为“ **`aws_saml_auth.py`** ”

*(您需要编辑第 34 行`idpentryurl`的值，因为 GitHub 上的脚本是为他写脚本时的雇主量身定制的。)*

我没有将“Python”添加到我的路径中，所以现在从命令行运行给定的 Python 脚本看起来有点荒谬:

```
c:\users\MY_USERNAME\AppData\Local\Continuum\anaconda3\python.exe c:\users\MY_USERNAME\Desktop\auth_saml\aws_saml_auth.py 
```

如果我将 Python 放入我的 Windows“PATH”环境变量中，并在我放入“`aws_saml_auth.py`”的同一个文件夹中执行该命令，我可以简单地执行以下命令:

```
python aws_saml_auth.py 
```

假设我安装的 Python 已经安装了“`aws_saml_auth.py`”所指的所有模块*(查找`pip install` )* ，在命令行界面上运行这个脚本应该要求我输入用户名&密码*(这是我检查工作电子邮件的单点登录凭证...用户名后面带“@ company name . com”)*。

如果我输入了正确的值，我会看到下面的输出，告诉我在我的硬盘上的 **`C:\Users\MY_USERNAME\`** 下有一个名为 **`.aws`** 的新文件夹，其中有一个名为 **`credentials`** *(没有文件扩展名)*的文件，该文件的内容将有助于在下一个小时*登录 Amazon(之后，我必须再次运行这个 Python 脚本来为“`credentials`”*:
生成新内容)

```
----------------------------------------------------------------
Your new access key pair has been stored in the AWS configuration file C:\Users\MY_USERNAME\.aws\credentials under the default profile.
Note that it will expire at 2019-02-20 19:35:01+00:00.
After this time, you may safely rerun this script to refresh your access key pair.
If you specified a credential other than default, to use this credential, call the AWS CLI with the --profile option (e.g. aws --profile default ec2 describe-instances).
---------------------------------------------------------------- 
```

请注意，第 1 行和第 4 行中显示了单词“`default`”，因为第 87 行中的“`aws_saml_auth.py`”就是这么说的:🤷‍♀️

```
profile: ("The name of the profile we want to put it the credential file", 'option', 'p')='default', 
```

您需要大约每小时运行一次这个 Python 脚本。

因此，您可能会发现向您的命令行环境中添加一个名为“`awslogin`”的命令非常方便，只要您注意到“`git fetch`”等，就可以快捷地弹出用户名&密码提示。不支持使用“联合”登录的 AWS 托管的存储库。

我的同事将其设置为一个名为“`login`”的命令，该命令只能在“Git Bash”命令行界面中工作，方法是转到`C:\Program Files\Git\usr\bin\`并在其中创建一个名为“`login`”*(无文件扩展名)*的新文件，其内容为:

```
python /usr/bin/aws_saml_auth.py 
```

*(在他的例子中，他将`aws_saml_auth.py`保存到了`C:\Program Files\Git\usr\bin\`，`python`是他的一部分。)*

* * *

[![CodeCommit](img/c8bfe893e63cabca92a04d742a4f328f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2O6EG_M8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/awscodecommitlogo.png)

## 安装 AWS CLI，这样 Git 执行命令“`aws codecommit credential-helper`”的尝试就能正常工作

现在我们需要安装 AWS CLI，这样 Git 执行命令“`aws codecommit credential-helper`”的尝试就可以正常工作了。

否则，Git Bash / SourceTree 将忠实地按照我们的指示“对 AWS CloudCommit remotes 使用命令'`aws`'而不是用户名和密码”，然后“退回到”提示输入用户名&密码*(这不起作用，因为亚马逊不允许“联合”登录)*。

让我们[下载 AWS CLI 安装程序](https://docs.aws.amazon.com/cli/latest/userguide/install-windows.html#install-msi-on-windows)，安装它，[将`aws`添加到我们的 PATH 环境变量](https://docs.aws.amazon.com/cli/latest/userguide/install-windows.html#awscli-install-windows-path)中。

在我的例子中，AWS CLI 安装程序为我处理了 PATH 环境变量:它将`C:\Program Files\Amazon\AWSCLI\bin\`添加到系统范围的路径中。

如果我打开一个标准的 Windows 命令提示符 *(Windows 键- >键入“`cmd`”并按回车键)*，如果我键入“`aws --version`”，我会得到一个关于我的计算机上安装的 AWS CLI 版本的答案。

然而，饭桶 ***还是*** 不太明白*应该是什么意思。*

 *我可以看到我有这个问题，因为如果我打开“Git Bash”CLI 并键入“`aws --version`”，我会得到“aws: command not found”的抱怨。

为了解决这个问题，我添加了一个名为“**`aws`**”*(没有文件扩展名)*到`C:\Program Files\Git\bin`的文件，其内容为:

```
C:\Program Files\Amazon\AWSCLI\bin\aws.cmd 
```

*( [谢了，栈溢出](https://stackoverflow.com/questions/53015630/bash-aws-command-not-found-on-windows-7-in-git-bash)。)*

我想这就是它突然开始工作的原因。

我有点紧张是不是这样，因为现在我已经让它工作了，如果我删除这个文件的内容，`aws --version`仍然可以工作。

无论如何，只要"`aws --version`"从 Git Bash CLI 开始工作，我就可以开始了！

1.  我有办法每小时给`c:\users\MY_USERNAME\.aws\credentials`放一次新内容
2.  我已经告诉 Git，AWS CloudCommit 上托管的任何存储库都使用 AWS CLI 来方便登录，而不是用户名和密码，而是继续使用用户名和密码进行其他任何操作
3.  我已经在我的电脑上安装了 AWS CLI，并告诉 Git 如何找到它

* * *

[![Photo of a clock](img/ae3418d6cde41b77b9b575fc9c4df67b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IB_nO2bi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/goldclock.jpg)

## 检测并修复过期凭证

不幸的是，设置好这一切并写博客需要花费大量的故障排除时间，我在 Amazon 的 1 小时凭证过期了，所以我可能需要再次从命令行运行“`aws_saml_auth.py`”，并用一个新的令牌更新`c:\users\MY_USERNAME\.aws\credentials`的内容。

当在 Git Bash CLI 中对我之前为之努力的 repo 执行`git fetch --all`时，过期的凭证看起来是这样的:

```
MY_USERNAME@MY_COMPUTER_ID MINGW64 ~/Desktop/aws_test3/test_repo_3 (master)
$ git fetch --all
Fetching origin
fatal: unable to access 'https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY/': The requested URL returned error: 403
error: Could not fetch origin 
```

在 SourceTree 中，右键单击左侧导航中“Remotes”下的“origin ”,然后单击“Fetch from origin ”,出现以下错误:

```
git -c diff.mnemonicprefix=false -c core.quotepath=false fetch origin
fatal: unable to access 'https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY/': The requested URL returned error: 403
Completed with errors, see above. 
```

Git Bash 中一次成功的获取看起来像这样:

```
MY_USERNAME@MY_COMPUTER_ID MINGW64 ~/Desktop/aws_test3/test_repo_3 (master)
$ git fetch --all
Fetching origin

MY_USERNAME@MY_COMPUTER_ID MINGW64 ~/Desktop/aws_test3/test_repo_3 (master) 
```

在 SourceTree 中，进度条消失了，除非你选择了“显示全部输出”，在这种情况下，输出看起来像这样:

```
git -c diff.mnemonicprefix=false -c core.quotepath=false fetch origin
Completed successfully. 
```

* * *

[![Git logo](img/bbd5800f4955b4a22dccd917fe203452.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OjZAcybE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-scm.cimg/logos/downloads/Git-Logo-2Color.png)

## 针对 AWS 运行您的第一个工作`git`命令

当然，我认为你会做一个“`git fetch`”有点冒昧——这意味着你一开始就有一个远程-本地配对。:-)

还记得我们一开始从亚马逊得到的那个网址吗？

```
https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY 
```

让我们在我们的计算机上设置两个本地存储库，每个都将其作为“远程”使用——一个使用“g it Bash”CLI*(或者使用我们的 Windows CLI，如果我们在安装 Git 时设置它能够执行“Git”命令)*，另一个使用 SourceTree。

### 用 Git Bash 克隆

1.  在硬盘上创建一个新文件夹，`c:\example\`
2.  在 Git Bash 命令提示符下执行`cd c:\example`
3.  做`git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY aws_test_1`

输出应该是这样的，并且您的计算机上应该有一个新的`c:\example\aws_test_1\`文件夹。里面应该有文件，如果遥控器里有文件的话。

```
MY_USERNAME@MY_COMPUTER_ID MINGW64 /c/example
$ git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY aws_test_1
Cloning into 'aws_test_1'...
remote: Counting objects: 3, done.
Unpacking objects: 100% (3/3), done. 
```

### 用 SourceTree 克隆

1.  单击顶部附近打开的存储库选项卡旁边的“+”，在“本地存储库”屏幕顶部附近的大按钮中，单击带有图标的“克隆”按钮，该图标看起来像指向打开的框的箭头。
2.  在“源路径/ URL”中，键入:`https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY`
3.  在“目标路径”中，键入:`C:\example\aws_test_2`
4.  在“名称”中，键入:`Best AWS Test 2`
5.  单击“克隆”按钮

如果你没有选中进度条的“显示全部输出”复选框，它可能会在完成后消失。但是如果你这样做了，它的日志看起来会像这样:

```
git -c filter.lfs.smudge= -c filter.lfs.required=false -c diff.mnemonicprefix=false -c core.quotepath=false clone --branch master https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OUR_AWESOME_REPOSITORY C:\example\aws_test_2
Cloning into 'C:\example\aws_test_2'...
warning: templates not found in C:\Program Files\Git\share\git-core\templates

remote: 
remote: Counting objects: 0
remote: Counting objects: 3
remote: Counting objects: 3
remote: Counting objects: 3, done.        
Completed successfully. 
```

在左侧导航中，如果您单击“Branches”并单击“master”*(或者您的分支被称为什么)*，您应该能够从存储库的远程副本*(即 AWS)* 中看到完整的提交日志历史，假设有任何历史。

你的电脑上应该有一个新的`c:\example\aws_test_2\`文件夹。里面应该有文件，如果遥控器里有文件的话。

恭喜，你被录取了！

快乐去吧！

* * *

[![Stack of books](img/b4dd9474a544156607411eaad6a8c524.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SA6zo9hj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/1/1c/Book_sale_loot_%25284552277923%2529.jpg/800px-Book_sale_loot_%25284552277923%2529.jpg)

## 进一步资源

*   使用 CodeCommit 和 GitHub 凭证助手*