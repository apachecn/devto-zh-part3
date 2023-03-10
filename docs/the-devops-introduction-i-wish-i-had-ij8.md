# 我希望我的 Devops 介绍

> 原文：<https://dev.to/perigk/the-devops-introduction-i-wish-i-had-ij8>

spacelift 的人写了一篇类似性质的文章。随意探索[这个](https://spacelift.io/blog/how-to-become-devops-engineer)，获得更多价值。

流行语无处不在。你可能已经注意到我不是他们的忠实粉丝。它们在任何地方都被使用(有时是以错误的方式),正因为如此，大多数人害怕问 101 问题，因为他们不想听起来无知。

几年前，我当时的经理问我是否想做一些 devops 工作，作为我软件工程师工作的一部分。当然，我当时很年轻，也害怕问这个问题，所以我只是说“好吧，让我们试一试。最坏的情况是我会死，而你会放一天假去悲伤。

感谢上帝，我还活着，用我的作品折磨人。现在我想给你一个很好的全面的概述，我在最初的步骤中遇到的术语，我希望我当时就知道，再加上一些不是发明出来的，但现在知道它们是很好的。

我们开始吧

<figure>[![](img/32462d1a48de5a6b71fa97dd95da90a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yLx04WCJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2ActJGr1h7Nw8kZ2Ij) 

<figcaption>(鸣谢威廉·史迪特，unsplash.com)</figcaption>

</figure>

### Devops

开发运维规程的创建是为了在开发人员和运营人员(如 IT、系统工程师、数据库管理员等)之间架起一座桥梁。主要的愿景是(大部分)使从开发环境到测试/生产环境的过渡完美、快速、自动化并且完全透明。

在此之前，部署导致两个“部落”之间持续的枕头大战和 nerf-shooting 战争(有时甚至是键盘投掷),部署缓慢、痛苦，需要大量的工作时间。如果你知道有一家软件公司不采用这种做法，你可以很容易地证实这一点。

这场运动受到了敏捷方法越来越受欢迎的影响，正如你所想象的，快速部署受到高度鼓励，并且是严肃软件公司的标准。

### IaaS

基础设施即服务。您可以使用自己选择的操作系统启动虚拟机(嗯，云提供商必须支持它，**，但大多数已知的 Windows 和 Linux 版本都有**)，以及自己选择的规格(RAM、持久存储、CPU 数量等)。

与所有精简的虚拟机一样，您必须自己设置一切，权衡的结果是**更能控制**。

最著名的例子是 AWS 的 EC2，Google Cloud 的 Google compute engine。

### PaaS

平台即服务。你可以把它看作是 IaaS 的抽象。

除了您的业务逻辑和数据库部分之外的一切对您来说都是一个黑盒。你可能知道基础设施的细节，但是你对它几乎没有控制权。

例如，您可能能够更改您使用的软件版本，但这种情况会发生:

*   从供应商的用户界面
*   版本修改将发生在供应商支持的范围内。如果供应商不支持 python 3.7，您就不能拥有它，如果您尝试手动更新，这很可能是痛苦的，并且很有可能您会搞砸一切。至少，这是我的经历。

最著名的例子是 AWS 的 ElasticBeanStalk，Google Cloud 的 Google AppEngine。

**注意**:在实际案例中，存在*aaS 组件的组合。PaaS 和 IaaS 协同工作尤其常见。这被称为*混合云*。

### Saas

软件即服务。在这种情况下，你想访问一个软件(基于云)，你根据你的使用付费(如果没有免费计划)。

除了 UI 和潜在的 API 所允许的，你对软件没有控制权。最著名的例子是网飞、Dropbox 和大多数谷歌服务。

在这篇[文章](https://www.cloudexpectations.com/managed-services/managed-service-provider-responsibility-iaas-paas-saas/attachment/demarcation-of-managed-services-provider-responsibility/)中，您可以看到 IaaS、Saas 和 PaaS 的直观对比。

### 调配

在 devops 上下文中，配置意味着(自动)引导基础架构。

因此，如果有人想要调配测试环境，那么他可能必须设置几个虚拟机(不是随机的虚拟机、特定类型的虚拟机或用作模板的某个映像)，然后设置所需的软件、设置任何 web 服务器等。听起来很无聊，是吧？

确实是这样，这就是为什么人们使用自动完成工作的供应工具(这样他们就可以在等待的时候毫无愧疚地浏览 Reddit 和 9gag)。

两个最著名的供应工具是 [Ansible](https://www.ansible.com/use-cases/provisioning) 和 [Terraform](https://www.terraform.io/) 。我现在不会深入细节，因为这将需要一篇 5 倍长的文章，但我将在 Terraform 中展示一个示例，向您展示它是多么简单。

```
resource “aws\_instance” “example” {
ami = “ami-b374d5a5”
instance\_type = “t2.micro”
provisioner “local-exec” {
command = “echo ${aws\_instance.example.public\_ip} > ip\_address.txt”
}
} 
```

Enter fullscreen mode Exit fullscreen mode

([从此处复制](https://learn.hashicorp.com/terraform/getting-started/provision))

上面的例子可能需要一个简短的解释；ami 来自亚马逊映像，是一个虚拟机模板。

t2.micro 指的是机器的计算特性(CPU、RAM 等)。如果你之前没看过这个，很好奇，可以看看这个[页面](https://www.ec2instances.info/)。

它用预定义的 ami 设置了一个 t2.micro 实例，并在启动时在 local-exec 部分运行 echo 命令。

Ansible 在一个较低的层次上发挥作用，在这个层次上，您定义了一套命令，称为剧本，具有更详细的命令。这里有很多很棒的文章，比如这篇文章，我建议你读一读。

### 码头化

Dockerazition 意味着您需要执行一些特定的步骤来使您的应用程序与 docker 一起工作。

简单地说，Docker 是一种轻量级虚拟化的手段(稍微简化一点，你可以在这里阅读更多的)。

您可以通过使用所谓的 dockerFile 来定义 Docker 映像，Docker file 实际上是一组逐步构建 Docker 映像的命令。

这里的是你能找到的最简单的 docker 文件之一，并附有解释。更多的图片可以在[码头中心](https://hub.docker.com/search?q=&type=image)找到。

### 为什么对 docker 如此大惊小怪？docker 提供什么？

使用 docker 有很多好处，但对我来说，最重要的用例是:

*   便携式环境。我可以使用完全相同的环境来进行开发、生产、试运行环境、重现生产问题、向客户进行演示……这样的例子不胜枚举。不再有“在我的机器上工作”的借口。
*   用几个命令设置环境。正如本文中的[所示，这非常符合资源调配。](https://www.vagrantup.com/docs/provisioning/docker.html)

### 无服务器

与名称所暗示的相反，我们仍然有运行后端逻辑的服务器。实际情况是:

当您想要执行某段代码(称为函数，因此这种计算模型有时也称为函数即服务— FaaS)时，云提供商会分配必要的资源(以动态方式)来执行它。

在执行结束后，它释放那些资源。

这些函数在各种偶然的触发器中运行，或者以类似 cron 的方式定期运行。

理想情况下，函数应该是纯的，这意味着它们只依赖于它们的参数来计算结果，而不依赖于任何外部状态(如数据库)，尽管使用外部数据库服务器或其他没有问题。

主要的优点是您只关心您的业务逻辑，并且您可以在一瞥之间扩展您的功能。

如果你想在这方面有更多的见解，可能是针对 AWS 的，但是这篇文章非常值得一读。

Traversy Media 的这个视频是一个很好的快速介绍视频。

### CI/CD

持续集成和持续交付。故事是这样的:不同的开发人员反对一个即将到来的版本或一组并行进行中的特性。当提交代码时(通常提交到主分支，尽管这是可配置的)，一组操作自动运行，目标是让所有操作成功。

此类操作将确保代码编译成功，成功运行单元测试，确保测试覆盖率高于可接受的阈值，产品的 docker 映像生成无误，映像成功部署到云环境等。

我想你可以理解持续的反馈和执行所有必要的步骤是多么重要，而不必去想它们，更糟糕的是，必须依靠你的意志力去做。你知道很多喜欢测试的开发者吗？我不:D

### 结论

感谢您阅读这篇文章。如果你现在进入 devops，那是一个迷人的领域。我希望这篇文章能让这些超基础的术语更加清晰。一如既往，请随意评论您的任何反馈。

*原载于 https://perigk.github.io/**。*

* * *