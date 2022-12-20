# 你说孤立网络

> 原文：<https://dev.to/ferricoxide/isolated-network-you-say-2ckh>

在过去的十五年里，我的绝大多数客户都非常注重安全。其他公司因数据泄露而出现在新闻中的频率——要么是由于黑客，要么只是没有充分保护 S3 水桶——使他们中的许多人在迁移到云时变得极其谨慎。

我的一个顾客特别谨慎。因此，他们向云的迁移包括大量使用非常封闭的，在某些情况下是隔离的[VPC](https://aws.amazon.com/vpc/)。这使得实施工作既具有挑战性又令人沮丧。

最近，我不得不在一个封闭的 VPC 内实现自托管 GitLab 解决方案。而且，当我说“锁定 VPC”时，我的意思是甚至标准的 AWS 服务端点已经(有效地)被定制的、严格控制的端点所取代。很有趣。

当我部署一个新的 GitLab 实例时，我注意到它的备份作业正在失败。是的，我已经通过`gitlab.rb`文件的`gitlab_rails['backup_upload_connection']`配置块完成了我认为足够的配置。我甚至钻研文档来寻找指定必要的定制端点所必需的 juju。虽然我最终错误地找到了 fog(git lab 用来与基于云的存储选项交互的 Ruby 模块)的文档，但我最终发现必需的设置在备份和恢复文档的[数字海洋部分](https://docs.gitlab.com/ee/raketasks/backup_restore.html#digital-ocean-spaces)(简单地说，它需要为“`endpoint`”参数设置一个适当的值)。

然而，事实证明这还不够。当我查看 git 的错误日志时，我发现它从 Excon Ruby 模块获得了 SSL 错误。是的，VPC 中的所有东西都使用来自私人证书颁发机构(CA)的证书，但是我已经将根 CA 安装到了操作系统的信任链中。所有操作系统级别的工具都可以使用来自私有 CA 的证书。所有 AWS CLIs 和 SDK 都很好(因为我包含了逻辑以确保它们都指向操作系统信任存储)——做`aws s3 ls`(等等。)如预期的那样工作。所以，我又挖了一些。找到了关于 [SSL](https://docs.gitlab.com/omnibus/settings/ssl.html) 的深入配置指南和[开头的注释，详细介绍了 GitLab 和 SSL 如何工作](https://docs.gitlab.com/omnibus/settings/ssl.html#details-on-how-gitlab-and-ssl-work)部分:

> GitLab-Omnibus 包括自己的 OpenSSL 库，并链接所有编译好的程序(如 Ruby、PostgreSQL 等。)对抗本库。该库被编译以在`/opt/gitlab/embedded/ssl/certs`中寻找证书。

这告诉我，我走在正确的道路上。事实上，往下看一页，我发现:

> 注意，OpenSSL 库支持`SSL_CERT_FILE`和`SSL_CERT_DIR`环境变量的定义。前者定义了要加载的默认证书包，而后者定义了在其中搜索更多证书的目录。如果您已经将证书添加到 trusted-certs 目录中，则不需要这些变量。然而，如果由于某种原因你需要设置它们，它们可以被定义为环境变量。

所以，我加了一句:

> ```
> gitlab\_rails['env'] = {
>     "SSL\_CERT\_FILE" => "/etc/pki/tls/certs/ca-bundle.crt"
> } 
> ```

到我的`gitlab.rb`并做了一个快速的`gitlab-ctl reconfigure`使新的设置在运行的服务中激活。后来，我的 GitLab 备份到 S3 的工作没有进一步的问题。

注意事项:

*   我们目前使用 GitLab 的综合安装。改变源代码构建的安装的方法会有所不同。参见 GitLab 文档。
*   `SSL_CERT_FILE`参数的上述路径适用于 RedHat/CentOS 7。如果使用不同的发行版，请查阅您的发行版手册以找到合适的位置。