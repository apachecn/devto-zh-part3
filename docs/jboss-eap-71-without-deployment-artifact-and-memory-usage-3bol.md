# 没有部署工件和内存使用的 jboss eap 7.1

> 原文：<https://dev.to/peysal/jboss-eap-71-without-deployment-artifact-and-memory-usage-3bol>

# 大问题

你真的知道默认情况下 eap-7.1 会使用多少内存吗？所以在你开始部署你的应用之前，你最好做一些基准测试。

## 我们将使用的工具

*   jboss-eap-7.1(咄)
*   visualvm-1.4.2

## 我们是如何做到的

1.  运行您的 eap
2.  启动您的虚拟虚拟机
3.  将您的 visualvm 与正确的 jvm 进程连接起来

## 输出

[![heap without any deployment artifact](img/b9ae16c0b2e174fe87a7875d1e28720e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkIPz-OE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6vmx4tr1y0q4jovfmv99.png)

[![metaspace without any deployment artifact](img/d20fec0865d100029ff988193f2fe87b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U24fJPvY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/psbyi8w51d4ws53tvw75.png)

[![number of classes loaded by eap without deployment artifact](img/85bcd0713d5bcf738c5bb8792c0fcc1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k_zYJGaf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0kk1ohui081mkyq5vtm.png)

## 事情要深思

1.  部署简单的 spring MVC REST hello world 会怎么样？
2.  有人需要知道所有这些 JVM 的内存管理吗？