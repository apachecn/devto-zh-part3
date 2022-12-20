# 用于 Windows AzureAD 和共享驱动器的 Docker

> 原文：<https://dev.to/azure/docker-for-windows-azuread-and-shared-drives-3da2>

现在我[为微软](https://dev.to/aaronpowell/starting-2019-with-a-new-job-2cgm-temp-slug-6541646)工作，我有了一个使用 Azure Active Directory (AzureAD 或 OrgID)登录的设备，而不是我以前使用的微软帐户(MSA)。

当设置一个新设备时，我把我的磁盘共享给 Docker for Windows，这样我就可以安装卷了。不幸的是，Docker for Windows 中有一个使用 AzureAD 进行身份验证的[错误。](https://github.com/docker/for-win/issues/132)

每个人的解决方案似乎是创建一个本地管理员帐户，我觉得这没有吸引力。本地帐户意味着它不会在任何地方同步，并且当我格式化设备时，我有丢失数据的风险。

我的解决方案？将我的 MSA 添加到设备，并将其设置为管理员级别的帐户。使用该帐户快速登录，然后直接退出日志(反正我不打算使用该帐户)并返回到我的 AzureAD 登录。现在，我可以共享卷，输入我的 MSA 凭据，我们就完成了！

所以下次我设置设备使用 AzureAD 登录时，我还会以管理员身份添加我的 MSA，这样我就可以共享一个卷到 Docker。似乎有点过头了，但是🤷‍♂，你得做你该做的。