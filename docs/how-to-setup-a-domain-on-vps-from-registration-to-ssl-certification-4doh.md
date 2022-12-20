# 如何在 VPS 上设置域:从注册到 SSL 认证

> 原文：<https://dev.to/allenwoods/how-to-setup-a-domain-on-vps-from-registration-to-ssl-certification-4doh>

[![Server Room](img/625fa9dcdb299a53d3b96de4ae0c4be4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n2AEq_y---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.maintech.com/wp-content/uploads/2016/09/iStock_70297009_XXXLARGE.jpg)

# 永远没有“正确”的时间

这篇文章是为那些一直在考虑托管他们的第一个域名，但可能会退缩的人准备的。合适的项目会出现，合适的时机会到来，*但还不是*。

经过这个过程后，我可以告诉你，没有比现在更好的时间来开始托管你的第一个域名。

> 预测未来的最好方法是创造未来
> ——*亚伯拉罕·林肯*

本演练面向那些选择手动操作和*“边做边学”*的人。如果这不是您的方法，您仍然可以使用我所讨论的简单的一键版本。

**免责声明:** `This tutorial is written from a Mac user's perspective.`

有许多不同的方法可以达到相同的结果，但是为了本文的目的，我们将讨论通过 [Namecheap](https://www.namecheap.com) 的域名注册和通过 [DigitalOcean](https://www.digitalocean.com) 的 VPS 托管。

准备好了吗？**走吧！**

# 名字里有什么

不幸的是，整个过程中最难的部分是我无法帮助你的——**选择你的域名**。虽然我不能帮你想出确切的名字，但我可以分享我收到的建议，让你更容易想到。

### 个人还是职业？

为了有个好的开始，我们必须直接:<u>你是哪种程序员</u>？

你是否以职业为重，希望开发和维护一系列应用程序，作为获得一个职位的简历推介？

你是否对代码充满热情，并发现自己为了理解和学习新事物的快感，不断地投入到超出任务或日常活动的项目中？

差异归结于承诺和时间尺度——这两个重要因素将指导如何选择你的第一个域名。例如，个人网站可能会适应你职业道路上的变化，而专业网站更有可能代表一个特定领域内的特定叙述。

|   | 个人的 | 专业的 |
| --- | --- | --- |
| 基于 | 你的法定姓名或昵称。 | 与众不同、易于记忆的标识符。 |
| 最适合 | 开发人员将技术视为他们整个职业生涯的一个章节，而不是一种激情。 | 一个对代码本身充满热情的工程师。 |
| 典型用途 | 求职时继续推销。 | 展示正在进行的工作，以建立一个在线存在和策划的品牌形象，代表你对代码的热情。 |
| **注** | 可以被热情的程序员用来围绕他们的在线状态建立一个 [Asch 现象](https://en.wikipedia.org/wiki/Asch_conformity_experiments),但是最好使用独特的合法名字或昵称。 | 更难想出来，通常更难注册。 |

理想的情况是将这两种类型的领域都作为一种“包罗万象”的方法，但是我建议从你的热情水平开始，并以此选择一个领域开始。

### 检查可用性

很多次，很多次，我脑海中有一个关于域名的完美想法，却发现它不可用。这曾经意味着在我去检查的网站上遇到登陆页面，但是我最近发现仅仅因为一个网站看起来是未注册的并不意味着它是未注册的。

这让我想到了我们的第一条公理:

注册域名比未注册域名更贵。

除非你真的打算收回你的投资，并有能力支付要价，尽可能尝试购买未注册的域名。

为了确保您的网站实际上是可用的，而不是私人停放的，我建议采取以下步骤:

1.  [WHOIS (ICANN)](https://whois.icann.org/en)
    *   看看是否有人注册了这个域名。
2.  [Namechk](https://namechk.com/)
    *   如果这个网站可用，看看这个名字在社交媒体平台上会有多大的影响力。

一旦你确定了你的热情水平和你选择的域名，是时候做出承诺并购买了。

如果你读到这里，你知道你已经准备好了。让我们开始吧！

# 登记

我经历的域名经纪人是[Namecheap.com](https://www.namecheap.com)。我强烈推荐他们注册一个域名，因为整个过程非常简单易懂。

也就是说，有些东西我们应该检查一下，哪些东西你*需要*购买，哪些东西你*不需要*购买。

### 购买什么

注册域名时，您只需支付或选择以下项目:

*   域名本身。
*   谁是看守

最后一点很重要。WHOIS Guard 有效地保护您的电子邮件、地址和法定姓名免受 WHOIS 搜索。如果不选择此项，您的个人信息将不会被匿名化，并且会被公开。

在本指南后面输入“网站管理员”类型的电子邮件时，您可以使用本服务为您提供的特殊“WHOIS”保护电子邮件地址，而不是已有的网站管理员电子邮件地址来保持匿名。

Namecheap 确实为您提供了一个有效期为一年的 SSL 证书，但这是在您的域上设置 HTTPS 所需的 *<u>而不是</u>* 。*(我学到了敬酒不吃吃罚酒。)*

> 在本文末尾，我们将介绍如何配置 Certbot 通过[Letsencrypt.org](https://letsencrypt.org)生成 SSL 证书。

你不需要设置比这更高级的东西来开始。现在您的域名是新注册的，注册可能需要 72 小时才能完成。同时，让我们建立我们的主机！

# 在数字海洋上创建水滴

在我做出决定之前，我研究了许多主机提供商。我最终选择的主要供应商有:

*   [AWS EC2](https://aws.amazon.com/ec2/)
*   [数字海洋](https://www.digitalocean.com/)
*   里诺德
*   [媒体模板](https://mediatemple.net/)
*   [OVH](https://www.ovh.com/world/)

我选择 DigitalOcean 是因为他们的名字不断被其他开发者在网络开发的帖子中提及和推荐，也因为他们的免费文档非常有深度。

我是一个非常“边做边学”的人，DigitalOcean 的服务主要是自己动手，这感觉很适合我想要的——通过自己动手来发展技能。

也就是说，这可能会因为一个特殊的原因而令人生畏。

### Linux

这需要某种人来完成这个教程。更简洁地说，当你打开 bash，只在你的终端上看到这个消息时，你会感到兴奋多于恐惧:

```
$ 
```

这是不太继续下去。幸运的是，网上有大量关于 Linux 使用的资源，社区也非常欢迎和支持。

这就引出了我们的第二条公理:

你的技能越有活力，你就越有价值。

你不需要已经了解 Linux，但是把你自己放在一个必须学习它的位置上，应该和你开始这种体验的愿望捆绑在一起。

能够导航和使用 Linux，以及能够说你管理你自己的 VPS，比不知道那些第一手的支持技术要好。

现在，我已经在这些兔子洞里旅行了很久，为你做了所有的研究。向前，到命令行！

### 居高临下(Ubuntu)

有很多 Linux 发行版——被称为*“distros”*——但是在这个演练中我们将把重点放在 Ubuntu Linux 的服务器版本上。

Linux 以不同的粒度级别进行版本控制，一直到每晚构建变更的频率。然而，我们希望在 VPS 上使用的版本类型叫做长期支持，简称 LTS。

在撰写本文时，当前的 LTS 版本是 2018 年 4 月发布的 Ubuntu Server 18.04。这是我们将选择预安装到我们的 VPS 上的内容。

LTS 版本在长达两年的时间内保持可靠，需要对服务器进行的更新和维护要少得多。我们或许应该借此机会重温一下我们的第三条公理:

始终使用 LTS 版本。

不仅有 LTS 版本的 Linux，还有 NodeJS 和其他框架。理想情况下，您应该尽可能地识别和使用 LTS 版本化的堆栈，以减少维护项目的难点。或者，您可以为您的应用程序创建 Docker 容器。

如果你想了解更多关于 Docker 的信息，请阅读下面这篇文章，作者是我在西雅图熨斗学校的一位同学:

马修·布罗菲的《码头工人基础知识》

在开始之前要经历很多，所以让我们一步一步来。首先，我们需要在您的本地机器上做一些事情。

### 使用安全外壳(SSH)

我们所做的关于如何与我们的副总裁互动的一切都应该加密以保持安全。为了实现这一点，我们使用安全 Shell，或*“SSH”*来连接。为了做好准备，在 DigitalOcean 上购买 VPS 之前，我们需要生成一个新的 SSH 密钥对。

打开电脑上的终端，输入以下命令:

```
ssh-keygen -f ~/your-domain-name-here-rsa -t rsa -b 4096 
```

> 更多信息，请参见: [SSH Keygen](https://www.ssh.com/ssh/keygen/)

请务必用您新注册的网站名称替换`your-domain-name-here`。

如果提示您创建新密钥将覆盖现有密钥，请取消。同意覆盖现有密钥是一种永久性的破坏性行为，将会永久删除被覆盖的密钥。

现在我们有了在数字海洋上创建水滴所需的钥匙，让我们开始建造我们的城堡。

### 选择一个液滴类型

一般来说，你应该根据自己的舒适程度、可用时间、预算和项目需求来确定你需要的主机数量。

我还想说，应该有一些通过经济压力来激励参与的因素，这样，由于“必须为之付费”，就可以保证越来越多的人使用自愿保护计划。

就我而言，我知道我想要构建一些应用程序，这些应用程序将使用计算上更昂贵的功能来完成一些事情，并且需要实时传输数据来实现我想要完成的一些结果。为此，通过获得他们每月 20 美元的期权，我测量了两次，削减了一次。

在撰写本文时，这是 DigitalOcean 在其 20 美元的 VPS 等级中提供的内容:
[![4 GB Memory, 2 vCPU, 80 GB SSD, 4 TB Transfer](img/83ea56161ebdd23de65c0978da9ee33a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4guo6And--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jt92xzno7ii8h1du6ylu.png)

选择符合您标准的液滴配置。按照设置过程，但是一旦你看到一个标题为“添加 SSH 密钥”的选项按钮出现在屏幕上，等到你已经阅读了下一步，然后再继续。

*   导航至:[数字海洋水滴定价](https://www.digitalocean.com/pricing/)

### 绑定 SSH

回到 bash 终端，输入下面的命令:

```
pbcopy < ~/.ssh/id_rsa.pub 
```

这会将您的个人 SSH 密钥拷贝到剪贴板，所以在您将此密钥附加到 Droplet 之前，请不要选择和拷贝任何文本。

单击 DigitalOcean 上的“添加 SSH 密钥”按钮，并将密钥文本粘贴到出现的表单中。如果成功粘贴文本，您应该会看到一个非常大的加密数据哈希。

> 更多信息，请参见:[如何将 SSH 公钥上传到数字海洋帐户](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/to-account/)

我们以这种方式将 SSH 密钥绑定到 Droplet 的原因是因为它做了三件非常重要的事情。

1.  它在创建 Droplet 时禁用密码验证。
2.  它从一开始就通过 SSH 安全地使用 Droplet。
3.  它会阻止通过电子邮件发送任何与我们的 VPS 相关的登录信息。

这确保了在 VPS 被激活时，人们甚至无法尝试在 VPS 上进行密码访问，更不用说在我们能够登录之前了。

默认情况下，如果没有附加 SSH 密钥，VPS 将启用密码身份验证，这不太安全，需要手动停用。虽然 DigitalOcean 确实允许您在 Droplet 本身的管理编辑器中从浏览器添加 SSH 密钥，但它还要求我们以一种简单得多的方式添加 SSH 密钥。

### IPv6 的重要性

确保您已经在 VPS 选项中选择了“IPv6”复选框。这将生成一个更长的 IP 地址，一旦旧的 IPv4 标准在未来几年被淘汰，它将允许您的服务器被访问。

DigitalOcean 提供的许多技术以及一般的网络技术都受益于 IPv6 连接的可用性，因此激活它是一个不错的选择。

至此，我们已经准备好和我们的 VP 进行第一次沟通，进入正题。请务必从 DigitalOcean 复制您的 IPv4 地址，并准备登录到您的服务器！

### 成为超级用户

为了清楚起见，我将把必要的命令压缩成连贯的块。对于那些不熟悉语法的人来说，所有以`#`符号开头的行都是描述性的注释，而不是 bash 命令。

```
ssh root@<your_server_ipv4>
# Confirm "yes" to connect.

adduser <your_first_name>
# Type a strong password and skip everything else.

usermod -aG sudo <your_first_name>
ufw app list
# You should see OpenSSH listed.

ufw allow OpenSSH
ufw enable
# Type yes to confirm.

ufw status
# You should see OpenSSH and Open SSH (v6) ALLOW from Anywhere.

rsync --archive --chown=<your_first_name>:<your_first_name> ~/.ssh /home/<your_first_name> 
```

在 bash 终端中，按 Command+T 创建一个新的并行选项卡。**在第一个终端选项卡中保持登录为“root”用户。**这一点非常重要，以防您的用户帐户配置过程中出现任何问题。

在并行选项卡中，输入以下内容:

```
ssh <your_first_name>@<your_server_ipv4> 
```

如果您在第二个选项卡中登录到您的用户帐户，您现在可以切换回您的“根”会话并继续。在“root”用户选项卡中输入以下内容:

```
sudo nano /etc/ssh/sshd_config
# Press the down arrow to scroll the editor until you see PermitRootLogin.
# Edit the line to read: PermitRootLogin no

# Press the down arrow to scroll the editor until you see PasswordAuthentication.
# Edit the line to read: PasswordAuthentication no

# Press Ctrl + X, then Y and Enter to save and exit the editor.

sudo systemctl restart ssh 
```

切换回您的用户帐户选项卡，并输入以下内容:

```
logout
ssh <your_first_name>@<your_server_ipv4> 
```

如果到目前为止一切顺利，没有任何障碍，那么您现在可以安全地注销“root”用户终端并关闭所有终端选项卡。

简单总结一下我们刚刚做的事情:

*   我们获得了访问我们的副总裁。
*   我们创建了一个新用户。
*   我们给了我们的用户帐户与“root”(sudo)相同的权限。
*   除了 SSH 登录之外，我们提高了防火墙的防护能力。
*   我们允许我们的用户帐户 SSH 访问。
*   我们取消了“root”用户再次登录的权限。

我们删除“root”用户的原因是因为对服务器的大多数攻击都来自“root”登录。通过阻止该用户访问系统，我们为系统增加了一层安全性。

既然我们的用户帐户已经被授予了相同的权限，我们就不再需要“root”来访问了。

> 更多信息，请参见:[Ubuntu 18.04 的初始服务器设置](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)

# 设置服务器

现在我们的 VPS 已经安全了，是时候让系统更新和必要的软件跟上速度了。使用您的用户帐户登录，并输入以下命令:

```
sudo apt-get update
sudo apt-get upgrade
# Press Y and Enter to confirm.

sudo apt-get install curl imagemagick subversion wget lynx iperf rsync
# Press Y and Enter to confirm.

sudo apt-get install unrar unzip iptraf nmap tcpdump sysstat zip
# Press Y and Enter to confirm.

sudo apt remove cmdtest
# This might say cmdtest doesn't exist, but better to be sure.
# We remove this application to allow for installation of Yarn.

sudo nano ~/.bash_aliases
# Inside the editor, add this line (without leading # symbol):
# alias node=nodejs
# Press Ctrl + X, Y and Enter to save and exit the editor. 
```

> 更多信息，请参见:[如何启用。bash_aliases](http://www.pkill-9.com/enabling-bash_aliases/)

| 关于 Ubuntu 上的 NodeJs |
| --- |
| nodejs 的别名在 Ubuntu Server 上的工作方式是，一个名为“NodeJS”的独立别名必须指向 VPS 上的 NodeJS 安装。 |

我们在上面最后一个文件编辑过程中使用的解决方法是使用名为“node”的新别名指向“nodejs”命令，从而将 node 命令修补为与本地环境一致。|

通过 HTTP 提供我们的第一个页面的基础工作已经接近完成。我们只需要再完成两个主要的安装。

### 安装 Nginx

如果您使用堆栈(如 MERN 或类似的堆栈)托管更高级的项目，除了本演练之外，您还需要进一步阅读这些主题。现在，我们只想在 HTTPS 提供一个静态页面。这是我们第一次经历这个过程，所以我们将坚持基本原则。

我们 VPS 使用的 HTTP 服务器叫做*“Nginx”*。安装是以下一系列命令:

```
sudo apt update
sudo apt install nginx
sudo ufw app list
# The output should show new options for Nginx Full, Nginx HTTP, and HTTPS.

sudo ufw allow 'Nginx HTTP'
sudo ufw status
# The output should show ALLOW from Anywhere for Nginx HTTP and Nginx HTTP (v6).

systemctl status nginx
# The output should include "Active: active (running)". 
```

> 更多内容请见:[如何在 Ubuntu 18.04 上安装 Nginx](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)

现在我们已经有了 Nginx 设置，我们需要有一些服务和服务它的方法。Nginx 是一个工具，但它不会自己运行。我们需要为它的运行设置一个服务器块，并将我们的“hello world”上传到服务器。

### 上传你的 Hello World

是时候在 VPS 上放置你的第一个静态页面了。在您的 VPS 登录终端会话中遵循以下顺序:

```
sudo mkdir -p /var/www/yourdomainname.com/html
# Be sure to replace "yourdomainname" with your domain name.

sudo chown -R $<your_first_name>:$<your_first_name> /var/www/example.com/html
sudo chmod -R 755 /var/www/yourdomainname.com 
```

在指向本地机器的第二个终端选项卡中，输入以下命令，将本地根文件夹转移到 Nginx 正在使用的 VPS 根文件夹:

```
scp -r /local/root <your_user>@<your_ipv4>:/var/www/yourdomainname.com/html 
```

> 更多信息，请参见:[使用 SCP 复制目录](https://stackabuse.com/copying-a-directory-with-scp/)

如果这是成功的，你应该看到你的文件上传到服务器。太好了，你的文件已经上传到网上了。但是使用 IPv4 地址来访问它们并不是最有吸引力的选择。让我们解决这个问题。

# 将您的域绑定到您的主机

默认情况下，Namecheap 会把你的域名存放在他们的域名服务器上。这些地址将您的注册域名关联为一个空闲的“停车”页面，该页面显示一封信函，提示您的域名可能会被出售。

我们需要更改这些名称服务器，以反映我们在数字海洋上的 VPS，以及将我们的域名绑定到 VPS 本身的 IP。让我们来看一下完成这项工作所需的步骤。

### 域名服务器、IP 和 SSL

登录你的域名账户，点击“管理”你的域名。进入仪表板后，向下滚动到“名称服务器”并输入以下地址:

| 名称服务器 |
| --- |
| ns1.digitalocean.com |
| ns2.digitalocean.com |
| ns3.digitalocean.com |

向下滚动，直到你找到一个名为“停车页面”的选项，并禁用它。这将解除域名与模板的链接，模板上写着“该网站可能出售”，该模板由 Namecheap 自动托管。

现在域名将指向数字海洋上的域名服务器，你的 VPS 的实际数据将被定位并提供给你的访问者。绑定的后半部分将在 DigitalOcean 上进行。登录您的 DigitalOcean 帐户，并将您的域名添加到您的仪表板。

在该域的管理功能中，您需要创建以下记录:

| 类型 | 主机名 | 价值 | 晶体管-晶体管逻辑。 |
| --- | --- | --- | --- |
| 民用航空局 | [www.yourdomain.com](http://www.yourdomain.com) | 授权:**letsencrypt.org**[`issue`] | Three thousand six hundred |
| AAAA | [www.yourdomain.com](http://www.yourdomain.com) | 指向 **IPv6** | Three thousand six hundred |
| A | [www.yourdomain.com](http://www.yourdomain.com) | 指向 **IPv4** | Three thousand six hundred |
| 纳秒 | [www.yourdomain.com](http://www.yourdomain.com) | 前往 ns1.digitalocean.com | One thousand eight hundred |
| 纳秒 | [www.yourdomain.com](http://www.yourdomain.com) | 前往 ns2.digitalocean.com | One thousand eight hundred |
| 纳秒 | [www.yourdomain.com](http://www.yourdomain.com) | 前往 ns3.digitalocean.com | One thousand eight hundred |

通过在每个记录条目的仪表板中选择您的 Droplet，绑定到您的 VPS。通过在 DigitalOcean 上的这些字段中输入`@`符号，IPv6 和 IPv4 地址会自动输入。此外，填写此部分时，`issue`标志是数字海洋仪表板中的下拉菜单选项。

我们刚刚做的是完成我们注册的域名和我们的 VPS 之间的链接，以及通过[Letsencrypt.org](https://letsencrypt.org/)启用 SSL 认证。但是启用 SSL 认证的可能性并不等同于被认证。让我们为“hello world”配置并生成一个 SSL 证书。

# SSL 认证

完成这个过程有两个步骤。安装 Certbot 并生成 SSL 密钥对。这被证明是我在我的 VPS 上设置这个的经历中最棘手的部分，但幸运的是，我能够让你走上正确的道路，尽可能地简单。

### 安装证书机器人

[Letsencrypt.org](https://letsencrypt.org/)提供了一个使用 OpenSSL 发布和更新 SSL 证书的实用程序。从 VPS 登录会话终端，安装过程如下:

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-dns-digitalocean
sudo certbot --nginx
sudo certbot -a certbot-dns-digitalocean -i nginx -d "*.example.com" -d example.com --server https://acme-v02.api.letsencrypt.org/directory 
```

> 更多信息，请参见:[LTS Ubuntu 18.04 上的 Nginx](https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx)

现在 Certbot 已经安装在 VPS 上，我们必须生成并使用 SSL 密钥对来启用它。按照这些命令完成我们的 SSL 认证:

```
sudo apt install python-certbot-nginx
sudo nano /etc/nginx/sites-available/yourdomainname.com
# Locate lines that begin with: server_name
# These lines should read: server_name yourdomain.com www.yourdomain.com;
# If this is not how it reads, update the lines to match.

sudo nginx -t
# The output should show no errors.

sudo systemctl reload nginx
sudo ufw status
# Output should show ALLOW from Anywhere on OpenSSH and Nginx HTTP

sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
sudo ufw status
# Confirm that Nginx HTTP has been disabled from the ALLOW from Anywhere list. 
```

如果一切顺利，下面的命令将是该过程的最后一步:

```
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com 
```

> 更多信息，请参见:[如何在 Ubuntu 18.04 上使用 Let's Encrypt 保护 Nginx】](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04)

# 结论

这是一段不平凡的旅程。如果你已经调整好了自己的节奏并完成了这个过程，请起立鼓掌，因为这确实是一个相当“复杂”的任务。

这篇文章的主要观点是告诉你给自己设定雄心勃勃但可以实现的目标是多么重要。是的，这是困难和复杂的，但是完成了一些事情，推动你超越了你之前对个人极限的认知，这一切都是值得的。

我希望任何从新手的角度阅读这篇文章的人在阅读完这篇文章后会有更大的自信，并开始学习一套全新的技能。我没有这方面的经验，你可能也没有。然而，我们在这里。

很难说你会从这里走多远，但很有可能会非常远。你已经开始掌握一个重要的概念，一句名言很好地概括了这个概念:

> 不管你是否相信你能做一件事，你都是对的。
> —亨利·福特

主持愉快！

> 如果你有问题，我会尽我所能在评论中回答。你可以在超级任务的[参观我自己简陋的随意涂鸦的“你好世界”。](https://www.thesupertask.com)