# 我如何设置 Linux web 服务器并部署我的静态站点

> 原文：<https://dev.to/halldorstefans/how-i-set-up-linux-web-server-and-deployed-my-static-site-pgk>

*最初发表于 2020 年 1 月 13 日[halldorstefans.com](https://www.halldorstefans.com/how-i-set-up-linux-web-server-deployed-my-static-site/)我的博客上*

在这篇文章中，我将详细介绍我部署 Linux 服务器的步骤，使它成为 web 服务器，并在其上部署我的个人网站。我做这个练习的主要目的是做一个 Linux 项目并学习。
我将使用 DigitalOcean 作为云提供商，但我假设这些说明可以转移到其他云提供商，尽管我没有尝试过。
创建服务器和初始设置
这个文档展示了如何创建服务器，或者像 DigitalOcean 称之为 Droplet。
我选择了一个 Ubuntu 18.04 的镜像，使用最便宜的标准计划，没有备份。我不认为此时需要备份选项。
一旦服务器启动并运行，我们就可以开始初始设置了。这些步骤包括创建另一个用户、设置基本防火墙和启用外部访问。我还建议在服务器上设置 SSH 密钥。
域名和 DNS
因为我正在建立一个 web 服务器，希望能部署一个站点，所以我需要一些域名，所以我用 Namecheap 买了一个。你也可以从 GoDaddy 或者其他域名提供商那里购买。
然后，您可以在 DigitalOcean 中创建一个 DNS 服务，然后确保域名注册商指向 DigitalOcean 名称服务器。
Nginx 和 SSL 证书
现在我们要把它做成一个 web 服务器。我们可以使用 Nginx 或 Apache，我决定使用 Nginx。没有具体原因，就是想试试。
在这里我们看到如何安装 Nginx 和其他调整。然后用加密来保护它。
“部署”我的网站
我承认这一步不是最专业的:D，但我只是想尽快将我的代码上传到服务器，看看是否一切正常。
边想:
我在想是不是应该为它设置一些输送管道。对于这样一个小项目来说，这当然有点高，但是为了学习，为什么不呢？
好了，回到部署。
我所做的是使用 FileZilla 将代码从我的本地计算机移动到服务器上的路径:
/var/www/example . com/html/index . html
中，而不是路径中的 example.com，我有我之前购买的域。
成功了！是的，成功了:)我真的很喜欢这个练习，我觉得在我的 Linux 服务器上托管我的网站很酷。接下来的步骤我想做几件事，我可能会以这个站点/服务器为基础。
Linux 强化
服务器监控和配置
创建发布/交付管道
你尝试过设置 web 服务器吗？有什么事情你会做得不一样吗？你对下一步有什么其他想法吗？

感谢您的阅读。对于我的每周更新，你可以注册我的时事通讯。

在这篇文章中，我将详细介绍我部署 Linux 服务器的步骤，使它成为 web 服务器，并在其上部署我的个人网站。

我这次练习的主要目的是做一个 Linux 项目，学习。

我将使用 DigitalOcean 作为云提供商，但我假设这些说明可以转移到其他云提供商，尽管我没有尝试过。

## 创建服务器和初始设置

[这个文档](https://www.digitalocean.com/docs/droplets/how-to/create/)展示了如何创建服务器，或者像 DigitalOcean 称之为 Droplet。

我选择了 Ubuntu 18.04 映像，使用最便宜的标准计划，没有备份。我不认为此时需要备份选项。

一旦服务器启动并运行，我们就可以开始初始设置了。[这些步骤](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)包括创建另一个用户、设置基本防火墙和启用外部访问。

我还建议[在服务器上设置 SSH 密钥](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1804)。

## 域名和 DNS

因为我正在建立一个 web 服务器，希望能部署一个站点，所以我需要一些域。所以我买了一个用[命名的便宜的](https://www.namecheap.com/)。你也可以从 [GoDaddy](https://uk.godaddy.com/) 或者其他域名提供商那里购买。

然后你可以[在数字海洋中创建一个 DNS 服务](https://www.digitalocean.com/docs/networking/dns/quickstart/)，然后确保[域名注册商指向数字海洋域名服务器](https://www.digitalocean.com/community/tutorials/how-to-point-to-digitalocean-nameservers-from-common-domain-registrars#registrar-namecheap)。

## Nginx 和 SSL 证书

现在我们要把它做成一个网络服务器。我们可以使用 [Nginx 或者 Apache](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations) ，我决定使用 Nginx。没有具体原因，就是想试试。

[这里](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)我们看看如何安装 Nginx 和其他调整。然后[用加密](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04)来保护它。

## “部署”我的站点

我承认这一步不是最专业的:D，但我只是想尽可能快地将我的代码上传到服务器，看看是否一切正常。

一边想道:

我在想，我是否应该为它建立一些运输管道。对于这样一个小项目来说，这当然有点高，但是为了学习，为什么不呢？

好了，回到部署。

我所做的是，我使用 FileZilla 将代码从我的本地计算机移动到服务器上的路径中，**/var/www/example . com/html/index . html**，而不是路径中的*example.com*，我使用了我之前购买的域。

## 成功了！

是的，成功了:)我真的很喜欢这个练习，我发现在我的 Linux 服务器上托管我的站点很酷。

## 下一步

接下来我想做几件事，我可能会使用这个站点/服务器作为基础。

*   Linux 强化
*   服务器监控和配置
*   创建发布/交付渠道

你试过设置一个网络服务器吗？有什么事情你会做得不一样吗？

你对下一步有什么其他想法吗？

* * *

感谢您的阅读。对于我的每周更新，你可以注册我的[时事通讯。](https://email.halldorstefans.com/)