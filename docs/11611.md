# Docker & RunC 中发现的漏洞

> 原文：<https://dev.to/leppercameron/vulnerability-identified-in-docker--runc-2oc9>

# Docker 漏洞报告

周六，RedHat 在 **[常见漏洞和暴露数据库](https://access.redhat.com/security/security-updates/#/cve)** 中发布了一个新条目，影响级别**重要**。该漏洞可让恶意容器得以执行，最终导致对主机文件系统的轻松访问。

它将弱点定义为:

运行容器时，在 runc 处理系统文件描述符的方式中发现了一个缺陷。恶意容器可以利用该缺陷覆盖 runc 二进制文件的内容，从而在容器主机系统上运行任意命令。

完整的细节可以在 **[这里找到](https://access.redhat.com/security/cve/cve-2019-5736#field_cve_details_text)** 。

建议尽快修补 RunC，如果你还没有的话。

这是今天早上分享给我的，有点意外。这对你或你的组织有影响吗？

编辑: *有人告诉我，这本 CVE 的初版是在二月！为此感谢 [@ohffs](https://dev.to/ohffs) 。希望这个博客仍然是一个有用的资源，并提醒保持补丁更新的重要性！*