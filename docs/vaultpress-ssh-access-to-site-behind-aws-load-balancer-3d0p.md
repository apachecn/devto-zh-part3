# vault 按 SSH 访问 AWS 负载平衡器后面的站点

> 原文：<https://dev.to/abdallah/vaultpress-ssh-access-to-site-behind-aws-load-balancer-3d0p>

我最近在按照 https://github.com/aws-samples/aws-refarch-wordpress 中概述的参考架构将一个大型 WordPress 网站从专用服务器迁移到 AWS

这种体系结构的一个优点是，站点完全位于负载均衡器之后，运行 web 服务器的实例永远无法从互联网上访问(甚至无法通过 ssh 或 http 访问)。因此，对站点的任何更新都需要使用 Systems Manager 进行部署。我稍后会对此进行发布…但是由于这种限制，VaultPress 只能通过 https 访问站点(用于备份)，这导致 web 实例上的额外负载。

要解决这个问题，我们需要允许 VaultPress SSH 直接访问 web 实例(至少是其中的一个)。通过 SSH 的唯一途径是通过 bastion 实例。

在这个 bastion 实例上，我在/etc/nginx/nginx.conf
中安装了 Nginx，配置如下

```
stream {
        server {
                listen 1234;
                proxy_pass 10.0.1.12:22;
        }
} 
```

其中`10.0.1.12`是负载平衡器后面的一个 web 实例的内部 IP。

在这个阶段(重载 Nginx 之后)，我将`vaultpress`用户添加到 web 实例中，并在`.ssh/authorized_keys`中为该用户设置 SSH 公钥。

为了手动测试，我添加了自己的公钥并连接到:

```
ssh -l vaultpress -p 1234 bastion.mydomain.com 
```

在那之后，我从 VaultPress 仪表板测试。不出意外:测试连接正常(SSH/SFTP)，一切正常！太好了，SSH 和 SFTP 设置都应该正常工作。

我在这里遇到的一个问题是，几个小时后/第二天，VaultPress 不知何故忘记了设置，我不得不重新添加连接信息。为什么？不知道！只要确保第二天检查它，以确保它还在那里！

由于在发生自动伸缩事件的情况下，IPs 可能会改变，所以我添加了一个 cron 作业，它每 5 分钟在 bastion 上运行一次，并更新 nginx 配置以指向一个 web 实例内部 IPs。一个简单的版本是这样的:

```
#!/bin/bash
IP="$(list-ips.sh | awk '{ print $1 }')"
sed -i -E "s/proxy_pass (.+):22/proxy_pass ${IP}:22/" /etc/nginx/nginx.conf
nginx -t && service nginx reload 
```

您还可以使用 CloudWatch 事件来添加规则，该规则在自动缩放发生时运行。指向一个 Lambda 函数，该函数调用系统管理器来更改 Nginx 中的 IP。下面是那个[https://docs . AWS . Amazon . com/auto scaling/ec2/user guide/cloud-watch-events . html # create-lambda-function](https://docs.aws.amazon.com/autoscaling/ec2/userguide/cloud-watch-events.html#create-lambda-function)的参考

哦，别忘了锁定 bastion 实例。在我的例子中，我在 EC2 控制台中设置了安全组，只允许以下内容:

```
Type: Custom TCP Rule
Protocol: TCP
Port Range: 1234
Source: 192.0.64.0/18
Description: VaultPress SSH Access 
```

希望有帮助！如果你对改进设置有任何想法，我欢迎讨论。