# 什么是头盔保险库？

> 原文：<https://dev.to/just_insane/what-is-helm-vault-55bg>

Helm-Vault 是一个新的应用程序，旨在保护 Helm Chart 的 values.yaml 文件中包含的秘密。

#### 问题:

将 Helm 与 Kubernetes 一起使用的问题是，没有好的方法来保护存储在 YAML 配置文件中的私有配置项。

您可能出于多种原因想要这样做，例如审核谁在访问哪些机密，或者防止对环境进行未经授权的修改。另一个原因可能是您希望公开可用的文档，而不必担心清理文件。

#### 当前解决方案:

当前可用的解决方案要求您大幅修改舵图，或者使用 GPG 或托管 KMS 解决方案加密整个文档。

这可能是一种痛苦的管理，谁愿意一直使用 GPG 来处理文件呢？如果钥匙丢了会怎么样？如果互联网中断，您无法访问您的 KMS 提供商，会发生什么情况？

#### 头盔与众不同之处:

使用 Helm-Vault，您可以完全访问 YAML 文档的结构和内容，即使它们处于“加密”状态，这为您提供了更多的灵活性。在发布图表时，您可以无缝地提供加密的 YAML 文件，并通过去名器提供需要更改信息的明显位置。

如果你在内部运行 Hashicorp [Vault](https://www.vaultproject.io/) ，你将可以随时访问你的秘密数据，并且不必担心使用 GPG 密钥，这使得你的开发者的生活更加容易。

#### 入门:

只要您拥有 Python 3.7、一个有效的 Hashicorp Vault 环境和一个 Vault 令牌，您就可以通过两个简单的步骤开始使用 Helm-Vault:

1.  `pip3 install git+https://github.com/Just-Insane/helm-vault`
2.  `helm plugin install https://github.com/Just-Insane/helm-vault`

你可以在[自述文件](https://github.com/Just-Insane/helm-vault/blob/master/README.md#usage-and-examples)中找到关于使用头盔保险库的信息。

如果您有任何问题或顾虑，请随时在该项目的 [GitHub 问题跟踪者](https://github.com/Just-Insane/helm-vault/issues)上提出问题。