# 如何:将 WSL Ubuntu 18.04 升级到 18.10

> 原文：<https://dev.to/david_j_eddy/how-to-upgrade-wsl-ubuntu-18-04-to-18-10-203>

最初发布在我的[博客](https://blog.davidjeddy.com/2019/03/29/how-to-upgrade-wsl-ubuntu-18-04-to-18-10/)上。

在得知 Ubuntu 19.04 已经发布后，我开始思考“WSL Ubuntu 可以升级吗？”。TL；大卫:是的，但是。我不是 Linux 专家，也不了解 Linux 的内部运作。在大多数情况下，我都是 T2 的忠实用户。把这次升级往好里想是一次实验，往坏里想是一场灾难。

## 命令

令人惊讶的是，实际的升级过程和典型的 Ubuntu 升级没有什么变化。一系列 4 个命令，等待，出现 snapd 重试/中止选项时重新启动，最后一个命令，最后一个确认`lsb_release -a`。

```
sudo su
apt update -y
apt upgrade -y
apt dist-upgrade
# now wait...
# When the 'snapd could not reach the snap store' error appears. Close the WSL window, then start a new session.
apt-get autoremove
lsb_release -a
```

<figure>[![](img/31c29adf6a8f1bc314f44d1f06f35273.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C0-7wlSb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/09/Are-You-Fucking-Kidding-Me-Rage-Face-Meme-Temp-1462x1440.png) 

<figcaption>有时候事情真的很*那*容易。</figcaption>

</figure>

## 但是...snapd (systemd)问题

WSL 不支持很多 Linux 系统服务。事实上，据我所知，WSL 并不支持系统服务。这给 dbus、snapd 和许多其他东西带来了问题。谢天谢地，似乎 Ubuntu 的核心进程并不需要它。因此，从 18.04 升级到 18.10 似乎不会对 WSL Ubuntu 的稳定性或可用性产生任何负面影响。虽然，像任何实验性的东西一样， [YMMV](https://www.urbandictionary.com/define.php?term=ymmv)

如果你觉得这很有帮助，有一个整洁的提示，或者想谈谈计算；在下面留言。我很想听听你的经历。T2】

## 资源

*   [h](https://www.zdnet.com/article/how-to-upgrade-to-ubuntu-linux-18-04/)t[TPS://www . zdnet . com/article/how-to-upgrade-to-Ubuntu-Linux-18-04/](https://www.zdnet.com/article/how-to-upgrade-to-ubuntu-linux-18-04/)
*   [https://forum . snap craft . io/t/windows-subsystem-for-Linux/216/15](https://forum.snapcraft.io/t/windows-subsystem-for-linux/216/15)
*   [https://askubuntu . com/questions/686239/how-do-I-check-the-version-of-Ubuntu-I-am-running](https://askubuntu.com/questions/686239/how-do-i-check-the-version-of-ubuntu-i-am-running%20)