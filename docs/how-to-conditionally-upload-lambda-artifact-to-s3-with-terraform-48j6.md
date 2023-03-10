# 如何用 Terraform 有条件的上传 Lambda 神器到 s3？

> 原文：<https://dev.to/piczmar_0/how-to-conditionally-upload-lambda-artifact-to-s3-with-terraform-48j6>

我在 Gitlab 上有 2 个项目。一个使用 terraform 提供基础设施，另一个使用 Lambda 代码。Lambda 配置为从 S3 存储桶部署。

在 terraform 中，我有一个虚拟的 zip 文件上传到 s3。我必须给出任何 ZIP 否则 Terraform 在申请期间抱怨。

我使用一个单独的项目来保存 Lambda 代码，它有单独的构建管道并部署到同一个 s3 存储桶。它应该在部署时覆盖虚拟 ZIP。

问题是每当 Terraform 执行时，真实的 ZIP 文件又会被虚拟文件覆盖，所以我需要再次从另一个项目部署真实的 ZIP 文件。

我不想把功能代码和 Terraform 项目放在一起。
我也不希望我的 Terraform 构建总是触发另一个项目构建(包含函数源代码)，因为最常见的是 Lambda 不变但其他资源在 Terraform 配置中被修改。

我考虑过在 ZIP 上传到 S3 时触发另一个 Lambda 来验证它是真的还是假的。在虚拟项目的情况下，它将使用 Gitlab API 触发另一个项目部署。

我的问题有什么简单的解决方法吗？