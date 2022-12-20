# Terraform 0.12 通用版

> 原文：<https://dev.to/david_j_eddy/terraform-0-12-general-release-52k0>

早在去年 7 月，Hashicorp 宣布了 Terraform 0.12 的预览版。截至昨天(5 月 22 日)，0.12 现已正式上市。我期待的一些亮点包括(但不限于):

*   **不兼容性和注释**
    *   `-var`和`-var-file`对应于声明的变量，如果不对应，则产生错误或警告。
*   **新功能**
    *   一级表达式:ami ="${var.foo}" -> ami = var.ami
    *   `[for](https://www.terraform.io/docs/configuration/expressions.html#for-expressions)`快车
    *   广义“splat”算子
    *   可为空的参数值
    *   修改了错误消息
    *   广义类型系统
    *   平面图的结构图
*   **其他改进**
    *   [格式化日期](https://www.terraform.io/docs/configuration/functions/formatdate.html)函数(...仍在等待 Unix 时间戳支持。#哈希公司...)
    *   一个新的`pg`状态存储后端允许在 PostgreSQL 数据库中存储状态。
*   **Bug 修复**
    *   ...很多修正。

<figure>[![the cloud](img/f37ba4211c8a8720706dd63b52be342d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gst4TLNt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2019/05/TheCloud_Box.jpg) 

<figcaption>你是这样电脑的吗？</figcaption>

</figure>

总而言之，0.12 看起来是一个不错的释放点。我期待着看到这些改进将如何帮助我弯曲“云”我的意志

当我探究从 0.11.x 到 0.12.0 的迁移对于当前的几个项目来说有多困难时，请继续关注。

## 附加阅读

*   [https://blog . davidjeddy . com/2018/07/11/terra form-0-12-preview-from-hashi corp](https://blog.davidjeddy.com/2018/07/11/terraform-0-12-preview-from-hashicorp)
*   [https://www.hashicorp.com/blog/announcing-terraform-0-12](https://www.hashicorp.com/blog/announcing-terraform-0-12)
*   [https://github . com/hashi corp/terraform/blob/v 0 . 12 . 0/changelog . MD](https://github.com/hashicorp/terraform/blob/v0.12.0/CHANGELOG.md)