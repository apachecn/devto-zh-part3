# 在 git 存储库中保持敏感数据的秘密

> 原文：<https://dev.to/apium_hub/keeping-sensitive-data-secret-in-a-git-repository-gg>

这篇文章描述了一个工具(git-crypt)，一组实践和原则(例如，城堡安全方法，也称为深度安全原则)，用于增加对(私有) **git 存储库**内部数据的保护。

## 简介:git 仓库

在更好、更安全的替代方案用尽之后，有时我们希望将秘密数据保存到一个 **git 存储库**中。一种手动的方法是使用 GPG 加密/解密信息(参见[本指南](http://www.ianatkinson.net/computing/gnupg.htm)，但是有一种更好的方法可以做到这一点: *git-crypt* 。

### 什么是 git-crypt？

Git crypt 是 GPG 之上的一个包装器，支持透明加密。它创建了一个 GPG 安全的金库。

从他们的项目现场:

git-crypt 支持 git 存储库中文件的透明加密和解密。

您选择保护的文件在提交时被加密，在签出时被解密。

git-crypt 允许您自由共享包含公共和私有内容的存储库。

git-crypt 可以优雅地降级，因此没有密钥的开发人员仍然可以克隆并提交到一个包含加密文件的存储库中。

这允许您将您的秘密材料(比如密钥或密码)存储在与您的代码相同的存储库中，而不需要您锁定整个存储库。

只有授权用户才能授予其他用户权限。否则，安全性将毫无意义，因为您可以将自己添加到任何保险库中。

## Git-crypt:我如何使用它

我通常进行 git-crypt 作者所说的分离:我的存储库的选定部分是秘密的(因此需要加密)。

我通常为这个文件夹选择一个描述性的名称，更愿意显示“keys”作为我的文件夹名称，而不是通过模糊使用[安全性](https://www.us-cert.gov/bsi/articles/knowledge/principles/never-assuming-that-your-secrets-are-safe)作为文件夹名称“src/test/resources/selenium”(只是一个例子)。

#### Git 储存库设置

设置 git 存储库:

```
cd repo
git-crypt init 
```

指定哪些文件夹/文件需要加密，如 git-filters:

```
$ cat .gitattributes
keys filter=git-crypt diff=git-crypt 
```

添加授权用户(通过他们的 GPG 键识别):

```
git-crypt add-gpg-user USER_ID 
```

#### 使用它

*   克隆回购
*   解锁金库:git-地穴解锁
*   做出更改并提交
*   推
*   这会在您的电脑中打开保险箱，但不会打开遥控器。你可以用 git-crypt 锁关闭它。如果攻击者可以发出解锁命令(没有任何进一步的保护)，让它关闭或打开并没有什么区别

#### 常用工作流程

*   开发人员机器:创建 vault，将自己添加为可信用户。向团队成员请求公钥，将用户密钥添加到保险库中。
*   持续集成(CI)机器:为 jenkins/CI 执行者添加一个公共 GPG 键。在回购中授权。

### 安全

git-crypt 在底层使用 GPG，所以安全性应该是由 GPG 提供的，除了 git-crypt 胶水中可能存在的缺陷。

从项目现场:

git-crypt 比其他透明 git 加密系统更安全。git-crypt 在 CTR 模式下使用 AES-256 加密文件，并使用从文件的 SHA-1 HMAC 导出的合成 IV。这种操作模式在确定性选择明文攻击下提供了语义安全性。这意味着，尽管加密是确定性的(这是 git 区分文件何时改变和未改变所必需的)，但除了两个文件是否相同之外，它不会泄露任何信息。[…]

### 限制&抓住你了

使用公共存储库违反了“深度安全”原则。

在向保险库添加密钥时，您必须小心:您可能会暴露您的密钥电子邮件(例如，[me@mydomain.com](mailto:me@mydomain.com))。这是网络钓鱼攻击的入口点。

(对于未经授权的用户)可以看出，我们正在使用 git-crypt，这是基于。gitattributes 文件:

git-crypt 不加密文件名、提交消息、符号链接目标、gitlinks 或其他元数据。

来自项目现场的其他安全问题:

git-crypt 依赖于 git 过滤器，它在设计时并没有考虑加密。因此，git-crypt 不是加密存储库中大多数或所有文件的最佳工具。git-crypt 真正闪光的地方是你的存储库大部分是公开的，但是你有一些文件(可能是名为*的私钥。密钥或具有 API 凭证的文件)，您需要对其进行加密。对于加密整个存储库，可以考虑使用 git-remote-gcrypt 这样的系统。(注意:不认可 git-remote-gcrypt 的安全性)。

git-crypt 不会隐藏文件是否改变，文件的长度，或者两个文件是相同的。

用 git-crypt 加密的文件是不可压缩的。即使是对加密文件的最小更改也需要 git 存储整个更改后的文件，而不仅仅是一个增量。

尽管 git-crypt 使用阿沙-1 HMAC 保护单个文件的内容，但是 git-crypt 不能被安全地使用，除非整个存储库被保护起来不被篡改(可以改变您的存储库的攻击者可以改变您的存储库)。gitattributes 文件来禁用加密)。如果有必要，使用 git 特性，比如带符号的标签，而不是仅仅依赖 git-crypt 来保证完整性。

当保险库打开或关闭时，差异会发生变化。当它打开时，文件内容是“普通”格式(即解密)。因此，您可以看到不同之处。当保险库关闭时，您看不到有效的差异，因为密码文本发生了变化，但人眼无法分辨其中的内容。

### 加贺

有时候我们需要在应用程序中使用秘密。它可能用于环境配置(例如，数据库凭证、我们依赖的服务)或应用程序配置。

理想情况下，这些信息应该存在于其他地方(与应用程序源代码分开):

*   最小特权原则:管理员可以访问机密，但不能访问代码；而开发人员可以访问代码，但不能访问机密(他们可以访问生产前的机密)
*   这些变化的速度不同，因此遵循 [SOLID](https://apiumhub.com/tech-blog-barcelona/solid-principles/) 中的单一责任原则(SRP)

### 原则

我们遵循“我们赖以生存的安全原则”(Howard02)中的以下原则:

*   使用纵深防御
*   永远不要仅仅依靠默默无闻来获得安全感

关于纵深防御:

深度防御背后的想法是用不同的防御策略来管理风险，这样，如果一层防御被证明是不够的，另一层防御将有望防止全面入侵。–(Howard 02)

关于通过模糊获得安全:

始终假设攻击者知道您所知道的一切—假设攻击者可以访问所有源代码和所有设计。即使这不是真的，攻击者也很容易确定模糊的信息。[……]晦涩是一种有用的辩护，只要它不是你唯一的辩护。换句话说，将晦涩作为整体深度防御策略的一小部分是非常有效的。–(Howard 02)

### 参考文献&链接

*   霍华德，迈克尔&勒布朗，大卫。编写安全代码，第 2 版。华盛顿州雷德蒙市:微软出版社，2002 年。可以在[这里](https://ptgmedia.pearsoncmg.cimg/9780735617223/samplepages/9780735617223.pdf)找到一个样本
*   [与 git-crypt 相关](https://www.agwa.name/git/git-crypt.git/raw/master/README)
*   [默默无闻中的安全:永远不要假设你的秘密是安全的](https://www.us-cert.gov/bsi/articles/knowledge/principles/never-assuming-that-your-secrets-are-safe)
*   [GPG 指南](http://www.ianatkinson.net/computing/gnupg.htm)
*   [纵深防御](https://www.us-cert.gov/bsi/articles/knowledge/principles/defense-in-depth#refs)
*   [信息安全成功的原则](http://www.pearsonitcertification.com/articles/article.aspx?p=2218577&seqNum=8)
*   [git-crypt 项目网站](https://www.agwa.name/projects/git-crypt/)

### 附录

#### “我们赖以生存的安全原则”

这是 Howard 和 LeBlanc 在“编写安全代码”中解释的安全原则的完整列表:

*   从错误中学习
*   最小化你的攻击面
*   使用安全默认值
*   使用纵深防御
*   使用最低特权
*   向后兼容总是会给你带来痛苦
*   假设外部系统不安全
*   失败时的计划
*   无法进入安全模式
*   记住安全特性！=安全功能
*   永远不要仅仅依靠默默无闻来获得安全感
*   不要混淆代码和数据
*   正确修复安全问题

如果你想了解更多关于 git repository 的信息，我强烈推荐你订阅我们的每月简讯[这里](http://eepurl.com/cC96MY)。

帖子[将敏感数据保密在 git 仓库](https://apiumhub.com/tech-blog-barcelona/keeping-sensitive-data-secret-git-repository/)首先出现在 [Apiumhub](https://apiumhub.com) 上。