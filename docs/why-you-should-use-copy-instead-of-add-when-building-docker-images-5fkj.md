# 为什么在构建 Docker 映像时应该使用复制而不是添加

> 原文：<https://dev.to/lirantal/why-you-should-use-copy-instead-of-add-when-building-docker-images-5fkj>

Docker 提供了两个命令，用于在构建 Docker 映像时将文件从主机复制到 Docker 映像:`COPY`和`ADD`。这些指令性质相似，但功能不同:

*   复制—在给定显式源文件和目标文件或目录的情况下，递归复制本地文件。使用 COPY，您必须声明位置。
*   添加—递归复制本地文件，当目标目录不存在时隐式创建目标目录，并接受归档文件作为本地或远程 URL 作为其来源，然后将其分别展开或下载到目标目录中。

添加和复制之间的区别虽然微妙，但却很重要。请注意这些差异，以避免潜在的安全问题:

*   当使用远程 URL 将数据直接下载到源位置时，它们可能会导致中间人攻击，从而修改正在下载的文件的内容。此外，远程 URL 的来源和真实性需要进一步验证。当使用 COPY 时，要从远程 URL 下载的文件的来源应该通过安全的 TLS 连接声明，并且它们的来源也需要验证。
*   空间和图像层考虑事项:使用 COPY 可以将添加的归档文件从远程位置分离出来，并将其解包为不同的层，从而优化图像缓存。如果需要远程文件，将所有这些文件合并到一个 RUN 命令中，然后下载、提取和清理文件，可以优化使用 ADD 时需要的多层操作。
*   使用本地归档文件时，ADD 会自动将它们提取到目标目录中。虽然这可能是可以接受的，但它增加了 zip 炸弹和 [Zip Slip 漏洞](https://snyk.io/research/zip-slip-vulnerability)的风险，这些漏洞可能会自动触发。

* * *

这个技巧是你应该采用的完整的 [10 个 Docker 图像安全最佳实践](https://snyk.io/blog/10-docker-image-security-best-practices/)的一部分。感谢你的阅读，也感谢和我一起工作的[奥马尔·列维·赫弗罗尼](https://twitter.com/omerlh)。

最初的博客帖子包括一个高分辨率的可打印 PDF，就像你在下面看到的片段。[看看吧](https://snyk.io/blog/10-docker-image-security-best-practices/)