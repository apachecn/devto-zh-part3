# 将 Visual Studio 配置为对 Windows 使用不同的 Git 凭据管理器

> 原文：<https://dev.to/xpirit/configure-visual-studio-to-use-a-different-git-credential-manager-for-windows-2mfj>

[![Configure Visual Studio to use a different Git Credential Manager for Windows](img/c97a6b8748af99b6f8da57723d9cbd0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KsMJut8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jessehouwing.net/conteimg/2019/02/9912778713_2df74a1279_k.jpg)

Visual Studio 附带了 Git credential Manager for Windows(GCMW ),作为其团队资源管理器功能的一部分。这个漂亮的小助手允许你使用正常的用户名和密码以及可选的 2FA 在其他 git 提供商中对 Azure Repos 进行身份验证，它将为你处理个人访问令牌+续订。有些情况下，您需要一个特定的(通常是更新的)GCMW 版本。

其中一个当前场景是，当您想要使用 Azure Active Directory 帐户访问 Microsoft 帐户支持的 Azure DevOps 组织时:

[![Configure Visual Studio to use a different Git Credential Manager for Windows](img/79010953abf3dc7ed11254c7dc0fcf37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iB2s7Svy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jessehouwing.net/conteimg/2019/02/image.png) 

<figcaption>您可能会收到“Git 因致命错误而失败。的身份验证失败..."</figcaption>

过去当[试图访问位桶](https://stackoverflow.com/q/51757837/736079)时也发生过类似的问题。在这种情况下，错误略有不同，但根本原因是相同的:

```
Git failed with a fatal error.
HttpRequestException encountered.
cannot spawn /C/Program Files (x86)/Microsoft Visual Studio/2017/Community/Common7/IDE/CommonExtensions/Microsoft/TeamFoundation/Team Explorer/Git/mingw32/libexec/git-core/git-askpass.exe: No such file or directory
could not read Username for 'https://github.com': terminal prompts disabled. 
```

## 解

在所有这些情况下，建议升级用于 Windows 的 Git 凭据管理器。许多关于堆栈溢出的帖子会告诉您用 GCMW 存储库中的最新文件覆盖 Visual Studio 安装中的文件。虽然这通常是可行的(我自己过去也这样做过)，但在将来安装 Visual Studio 更新时，这可能会导致问题。

## 更好的解决方案

最好将 Git 指向特定版本的 Git 凭证管理器 for Windows。首先安装最新版本的 Git 凭证管理器。[您总能在这里找到最新版本的 Git Credential Manager for Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows/releases)。然后更新您的全局 git 配置:

```
c:\>git config --global --edit 
```

找到`[credential]`部分并用以下内容覆盖它(将路径更新到系统上安装`git-credential-manager.exe`的位置:

```
[credential]
    helper = C:\\\\Program\\ Files\\\\Git\\\\mingw64\\\\libexec\\\\git-core\\\\git-credential-manager.exe 
```

这将确保您系统上的所有 Git 安装都将使用 GCMW 的这个特定安装。

请重新启动任何打开的 Visual Studio 实例，并根据需要从 Windows 凭据管理器中清除现有凭据，然后重试:

[![Configure Visual Studio to use a different Git Credential Manager for Windows](img/29bdee8bdbe514500ef0903e7cb17758.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6kAhW2rZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jessehouwing.net/conteimg/2019/02/image-1.png)

当所有其他方法都失败时，您可以完全关闭 Git 凭证管理器，返回到缺省的提示输入密码行为:

```
[credential]
    helper = 
```