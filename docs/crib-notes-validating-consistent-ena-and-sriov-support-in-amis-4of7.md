# Crib-Notes:验证非盟驻苏特派团对 ENA 和 SRIOV 的一致支持

> 原文：<https://dev.to/ferricoxide/crib-notes-validating-consistent-ena-and-sriov-support-in-amis-4of7>

在我参与的一份合同中，我们负责生产用于整个企业的 ami。在这一点上，这个过程是高度自动化的。基本上，我们使用一个管道，利用一些 CI 工具、打包器和一套 BASH 脚本来完成所有繁重的工作，不仅生成 AMI，还生成像 AMI 配置和包清单这样的工件。

当我们第一次采用 Packer 时，它在注册 AMIs 的方式上有一些限制(或者，也许，我们只是在第一次选择 Packer 时没有找到额外的标志——谁知道呢，在这一点上它已经消失在时间的迷雾中了)。如果您希望最终的 AMI 包含 ENA 和/或 SRIOV 支持(我们就是这样做的)，那么您的上游 AMI 也需要包含这些支持。这需要创建我们自己的“引导”ami，因为你不能指望这些功能被嵌入其中，甚至不能在上游供应商(在我们的情况下，Red Hat 和 CentOS)的 ami 中嵌入。

无论如何，因为整个过程已经从发明自动化的人转向基本上照看自动化任务的人，运行工具的人不一定对自动化正在做的每件事都有牢固的掌握。此外，负责照看自动化的人在不同的运行之间也是不同的。虽然自动化应该确保这无关紧要，但有时多疑是有好处的。因此，缓解这种疑虑的一个快速方法是从 AWS CLI 运行快速报告。下面的片段提供了一个充分的“五万英尺”一致性检查:

```
aws ec2 describe-images --owner <AWS_ACCOUNT_ID> --query \
      'Images[].[ImageId,Name,EnaSupport,SriovNetSupport]' \
      --filters 'Name=name,Values=<SEARCH_STRING_PATTERN>' \
      --out text | \
   aws 'BEGIN {
         printf("%-18s%-60s%-14s-10s\n","AMI ID","AMI Name","ENA Support","SRIOV Support")
      } {
         printf("%-18s%-60s%-14s-10s\n",$1,$2,$3,$4)
      }' 
```

*   有很多组织和个人发布 ami。因此，我们使用- owner 标志只搜索我们已经发布的 ami。
*   我们生产了几个不同的阿米族。因此，我们使用- filter 语句只显示我们感兴趣的 ami 的子集。
*   我真的只关心被报告的 ami 的四个属性:ImageId、Name、EnaSupport 和 SriovNetSupport。因此，使用 JMSE 查询语句来抑制除了我感兴趣的输出之外的所有输出。
*   因为我希望输出很漂亮，所以我使用了复合 awk 语句来创建格式化的头，并将相同的格式应用于 AWS CLI 的输出(只使用了 printf 例程的许多功能中的一点点)。

这将产生类似于以下内容的输出:

```
 AMI ID                AMI Name                                       ENA Support  SRIOV Support
   ami-187af850f113c24e1 spel-minimal-centos-7-hvm-2019.03.1.x86_64-gp2 True         simple
   ami-91b38c446d188643e spel-minimal-centos-7-hvm-2019.02.1.x86_64-gp2 True         simple
   ami-22867cf08bb264ac4 spel-minimal-centos-7-hvm-2019.01.1.x86_64-gp2 True         simple
   [...elided...]
   ami-71c3822ed119c3401 spel-minimal-centos-7-hvm-2018.03.1.x86_64-gp2 None         simple
   [...elided...]
   ami-8057c2bf443dc01f5 spel-minimal-centos-7-hvm-2016.06.1.x86_64-gp2 None         None 
```

正如你所看到的，并不是所有以上的非裔美国人在外表上都是一样的。虽然这个*可能*表示一个过程或人员问题，但我的输出实际上显示的是我们的 ami 中的演变。最初，我们没有做任何事情来支持斯里奥夫或 ENA。然后，我们添加了 SRIOV 支持(因为我们的 AMI 用户最终提出了要求)。最后，我们添加了 ENA 支持(主要是为了我们可以使用第五代 EC2 实例类型的全部范围和功能)。

无论如何，运行如上所述的报告，我们可以识别是否存在意外的差异，如果出现不合标准的 AMI，我们可以提醒我们的 AMI 用户“如果您需要 ENA 和/或 SRIOV，请不要使用”。