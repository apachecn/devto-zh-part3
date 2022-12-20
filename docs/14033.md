# 避免 Ubuntu 上的完整/引导分区。

> 原文：<https://dev.to/cpu/avoiding-a-full-boot-partition-on-ubuntu-2aob>

今天我登录到我的一个 Ubuntu 16.04 LTS 服务器，发现了待定的安全更新。我立刻皱起了眉头，因为我总是将`apt`配置为[自动安装安全更新](https://help.ubuntu.com/lts/serverguide/automatic-updates.html.en)。

## 麻烦找上我

运行`apt-get`来手动安装待定的安全更新很快就揭示了我的问题的根源:

```
...
dpkg: error processing archive /var/cache/apt/archives/linux-image-4.4.0-141-generic_4.4.0-141.167_amd64.deb (--unpack):
 cannot copy extracted data for './boot/vmlinuz-4.4.0-141-generic' to '/boot/vmlinuz-4.4.0-141-generic.dpkg-new': failed to write (No space left on device)
... 
```

😱**设备**上没有剩余空间。😱

果然,`/boot`分区已满，由于其中一个安全更新是一个新的 Linux 内核，需要解压缩到那个位置，一切都陷入停顿。

```
~# df -h
Filesystem                         Size  Used Avail Use% Mounted on
udev                              1003M     0 1003M   0% /dev
tmpfs                              205M   22M  184M  11% /run
/dev/mapper/woodwidewiki--vg-root   47G  4.9G   39G  12% /
tmpfs                             1023M     0 1023M   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                             1023M     0 1023M   0% /sys/fs/cgroup
/dev/sda2                          473M  471M     0 100% /boot
/dev/sda1                          511M  4.5M  507M   1% /boot/efi
tmpfs                              205M     0  205M   0% /run/user/1000 
```

看着`/boot`，我看到它充满了旧的内核映像，小的`471M`分区很快就填满了(*是的，这里的一个教训是我可能应该做更大的`/boot`分区！😊*)。谢天谢地，运行`apt-get autoremove --purge`很简单，让`apt`智能地清除不在使用的旧内核(同时仍然保留一个以前的版本作为后备)。

## 积极主动

手动运行`apt-get autoremove`解决了眼前的问题，但是当更多的旧内核堆积起来的时候，如何防止这种情况再次发生呢？

令人欣慰的是，`apt`提供了一种让系统通过`APT::Periodic::AutocleanInterval`设置自动主动清理旧内核的方法。你可以在`man apt.conf`中阅读更多关于这个和其他`apt`的设置。

让我的系统每 7 天自动清除旧的内核只是编辑`/etc/apt/apt.conf.d/10periodic`和改变
的问题

```
APT::Periodic::AutocleanInterval "0"; 
```

至

```
APT::Periodic::AutocleanInterval "7"; 
```

<figure>

[![konmari hi-five](img/6c63e2383d08d49f9d51b3e6f1e8ec49.png)](https://i.giphy.com/media/2tTkLT44hhJn5Pu4Md/giphy.gif)

<figcaption>Tidy your /boot, transform your life</figcaption>

</figure>

现在，我知道我的`/boot`分区不会填满，不会阻碍重要的安全更新，我可以睡得更安稳了。知道问题永远解决了比今天才解决更令人满意。永远争取自动解决！🤖 ❤️

(Header image[Philip Halling](https://www.geograph.org.uk/profile/1837)并在知识共享许可下获得重用许可。)