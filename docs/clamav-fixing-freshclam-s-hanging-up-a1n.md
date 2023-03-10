# ClamAV:修复 freshclam 的挂起

> 原文：<https://dev.to/nabbisen/clamav-fixing-freshclam-s-hanging-up-a1n>

这个帖子是关于 [ClamAV](https://www.clamav.net/) 的一个小技巧，实际上是 [ClamAV 非官方 Sigs](https://github.com/extremeshok/clamav-unofficial-sigs) 在我的情况下，故障排除。

前几天我发现 [`freshclam`](https://www.clamav.net/documents/signature-testing-and-management#freshclam) 被冻结在我的 [OpenBSD](https://www.openbsd.org/) 服务器里:

```
#  freshclam
ClamAV update process started at Fri Mar 22 22:32:33 2019
WARNING: Your ClamAV installation is OUTDATED!
WARNING: Local version: 0.100.2 Recommended version: 0.101.1
DON'T PANIC! Read https://www.clamav.net/documents/upgrading-clamav
main.cvd is up to date (version: 58, sigs: 4566249, f-level: 60, builder: sigmgr)
Downloading daily-25378.cdiff [100%]
Downloading daily-25379.cdiff [100%]
Downloading daily-25380.cdiff [100%] 
```

Enter fullscreen mode Exit fullscreen mode

等待或重试没有任何意义。

原因往往出现在数据库文件中。
尝试通过将原始文件移动到`/tmp` :
来更新它

```
#  # OpenBSD:
#  mv /var/db/clamav/daily.cld /tmp
#  # Manjaro Linux:
#  mv /var/lib/clamav/daily.cld /tmp 
```

Enter fullscreen mode Exit fullscreen mode

*注意:该目录取决于您的操作系统。*

运行`freshclam`并检查它是否工作正常:

```
#  freshclam
ClamAV update process started at Sat Mar 23 00:28:30 2019
WARNING: Your ClamAV installation is OUTDATED!
WARNING: Local version: 0.100.2 Recommended version: 0.101.1
DON'T PANIC! Read https://www.clamav.net/documents/upgrading-clamav
main.cvd is up to date (version: 58, sigs: 4566249, f-level: 60, builder: sigmgr)
Downloading daily.cvd [100%]
daily.cvd updated (version: 25396, sigs: 1528057, f-level: 63, builder: raynman)
bytecode.cvd is up to date (version: 328, sigs: 94, f-level: 63, builder: neo)
Database updated (6094400 signatures) from database.clamav.net (IP: xxx.xxx.xxx.xxx) 
```

Enter fullscreen mode Exit fullscreen mode

如果没问题，删除旧文件:

```
#  rm /tmp/daily.cld 
```

Enter fullscreen mode Exit fullscreen mode

如果这个解决方案有一天能帮助到某个人，我会很高兴:)
安全快乐。