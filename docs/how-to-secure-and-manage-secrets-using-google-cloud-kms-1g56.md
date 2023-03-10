# 如何使用谷歌云 KMS 保护和管理机密

> 原文：<https://dev.to/rameshl/how-to-secure-and-manage-secrets-using-google-cloud-kms-1g56>

让我们直接开始吧。我们都知道在我们的代码中存储应用程序的秘密不是一个好主意。那我们为什么还把它存在那里呢？我们举个例子。

我们可以将这些秘密存储在一个文件中，并将其添加到 gitignore 中，这样它就不会被添加到版本控制中。但是有几个障碍:

我们如何管理这些秘密？
删除本地副本会发生什么？
我们如何与其他开发者分享？
我们如何在变更期间管理这些秘密的版本，以及谁变更了什么的审计日志？
好多问题！所以我们最终将它存储在代码中，因为处理起来太复杂了。

对于大型应用程序或需要更高安全级别的应用程序，我们可以使用生产级秘密管理服务，如 [Hashicorp Vault](https://www.vaultproject.io/) 。

在本文中，我们将研究一种处理秘密的体面方法，同时仍然实现更好的安全性。我们将使用谷歌 KMS + Git + IAM +自动化来实现这一点。

[阅读更多](https://medium.freecodecamp.org/securing-managing-secrets-using-google-cloud-kms-3fe08c69f499)