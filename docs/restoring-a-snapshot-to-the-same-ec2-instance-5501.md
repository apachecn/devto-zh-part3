# 将快照恢复到同一个 EC2 实例

> 原文：<https://dev.to/jbesw/restoring-a-snapshot-to-the-same-ec2-instance-5501>

EC2 中的快照很容易通过 Lifecycle Manager 实现自动化，但是当需要恢复快照时，该如何做呢？

最好的方法通常是创建一个映像，并从这个映像启动一个新的 EC2 实例。这符合将实例视为可处置的、可替换的资产的思维模式，并且过程很简单。

但是，如果您只想访问快照以将几个文件恢复到原始实例，这里有一种替代方法，在许多情况下可能更容易。

## 步骤 1:从快照创建卷。

请记住，快照本身不能装载或附加到任何东西上——它就像一个冻结的、不可变的数据副本。因此，您必须首先基于快照创建一个卷:

*   在 EC2 控制台的快照菜单中，右键单击所需的快照，然后单击“创建卷”。
*   确保卷的大小至少是快照的大小。
*   至关重要的是，确保所选的可用性区域与您打算将它装载到的 EC2 实例的 AZ 相匹配。

## 步骤 2:将卷附加到 EC2 实例。

从快照创建卷会将数据从快照复制到卷。现在您有了一个可用的卷，您需要将它附加到 EC2 实例:

*   在 EC2 控制台的“卷”菜单中，右键单击卷，然后单击“附加卷”。
*   选择实例并单击“附加”。
*   回到实例菜单，如果您选择实例，您将需要一个名为“/dev/sdf”的新块设备。

## 第三步:挂载卷。

如果您通过 ssh 进入 EC2 实例并键入`lsblk`，您将看到两个设备连接到该实例。通常，xvda 是根设备，xvdf 是新卷。您还会看到该设备被分区，通常命名为 xvdf1。

这里有几个很重要的问题。如果您尝试一个典型的 mount 命令(`sudo mount /dev/xvdf1 /mnt/`)，您会看到一个错误，声称文件系统类型不正确。

如果您运行`sudo lsblk --output NAME,TYPE,SIZE,FSTYPE`，您会看到文件系统是 xfs，但是如果您随后尝试挂载指定的文件系统类型(`sudo mount /dev/xvdf1 /mnt/ -t xfs`，您会得到相同的错误。发生了什么事？

查看错误日志可以提供更多的信息——运行`dmsg | tail`,您会看到命令失败，因为“文件系统有重复的 UUID”。这很有意义，因为我们要将快照恢复到创建它的原始实例。

要解决这个问题，只需在原始命令中添加-o nouid 标志:

`sudo mount -o nouuid /dev/xvdf1 /mnt/`

现在，您会发现新卷安装在/mnt 目录中，您可以从快照中访问所有文件。

关于这一过程的完整演示，请参见[我的 YouTube 视频](https://www.youtube.com/watch?v=85djWS1rDyE)。

## 快照与卷

我听到了很多关于这两者区别的问题，所以想总结一些问答:

每个卷都有快照吗？
不，你必须选择制作快照。

如果我删除一个卷，它的快照会怎么样？
无-不会删除快照。

如果我删除快照，其卷会发生什么变化？
无-卷不会被删除。

每个快照都只有一个关联卷吗？
快照知道它是从哪个卷创建的，但是您可以稍后删除卷而不删除快照。您也可以稍后从同一个快照创建多个卷。

数据是在卷中还是在卷的快照中？
两者皆有。将快照视为不可变的，因此如果您想要使用快照，您必须创建一个副本作为卷。

AWS 对快照和/或卷收费吗？
两者皆有。

有 AWS 问题吗？在 Twitter 上问我 [@jbesw](https://twitter.com/jbesw) 。谢谢！