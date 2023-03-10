# AWS S3 存储桶策略出错

> 原文：<https://dev.to/totalcloudio/aws-s3-bucket-policy-gone-wrong-1akd>

[![AWS S3 Bucket Policy Gone Wrong](img/4656d098ed0039cad14f5648ba87dfd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aqUztrwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7qxa6idakm6h4j5adxr3.png)

### 这个“好奇的开发人员”试图玩弄 AWS S3 水桶政策。

#### 法定警告:好奇心害死人。工作时千万不要尝试这种特技。大到 [美国五角大楼](https://www.awsforbusiness.com/massive-us-pentagon-data-left-exposed-aws-s3/) 这样的组织都因此而面临音乐。

策略代码中的“主体”元素指定允许或拒绝访问驻留在 AWS S3 存储桶中的资源的用户、帐户、服务或其他实体。世界各地已经报道了几起 S3 桶如何导致数据泄露的案例。在大多数情况下，这颗“星星”是罪魁祸首。

有关更多信息，请访问 IAM 用户指南中的 AWS' [负责人](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) 页面。要了解更多关于如何将亚马逊 S3 存储桶的访问权限限制到特定的 IAM 角色，请点击 [此处](https://aws.amazon.com/blogs/security/how-to-restrict-amazon-s3-bucket-access-to-a-specific-iam-role/)。

与我们分享你的经历。

* * *

#### 请务必查看 TotalCloud Inc .的新旗舰功能安全组视图，它将提供实时和 3D 空间中安全漏洞的视觉线索。 [报名试试。](https://totalcloud.io/user-signup.html) 想知道视觉提示如何安全呈现， [阅读此贴](http://blog.totalcloud.io/reinforce-aws-security-posture-multiple-levels-seconds/) 。

看看这个视频，它给出了 ***AWS 安全组视图*** : 的要点

[https://www.youtube.com/embed/jfJlbDXLd5Q](https://www.youtube.com/embed/jfJlbDXLd5Q)

想要一个快速演示吗？[点击这里](http://tc.totalcloud.io/security-group-view-request-a-demo)。

#### 相关阅读:

#### [AWS NaCl(网络访问控制列表)的 5 个不容忽视的最佳实践](http://blog.totalcloud.io/5-not-to-ignore-best-practices-for-aws-nacls-network-access-control-lists/)

#### [AWS 安全团队的 5 个不容忽视的最佳实践](http://blog.totalcloud.io/5-not-ignore-best-practices-aws-security-groups/)

注:最初发布于 2018 年 3 月 20 日 [blog.totalcloud.io](https://blog.totalcloud.io/aws-s3-bucket-policy-gone-wrong/) 。