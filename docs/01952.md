# PHP 的内容趋势

> 原文：<https://dev.to/lbonanomi/content-trends-with-php-2n82>

很久以前，我和一群怪胎以及一个电缆调制解调器住在一个合租的房子里，所以我非常熟悉 [pfSense](https://www.pfsense.org/) 防火墙。关于 pfSense 的一件奇怪的事情是，它的许多系统脚本都是用 PHP 重写的；看到用 PHP 重新编写的脚本让我欣赏了包含电池的脚本语言的想法。

PHP 中形状更奇怪的电池之一是`metaphone()`，一种类似于(但更精确于)古老的 [soundex](https://en.wikipedia.org/wiki/Soundex) 的算法，用于生成字符串的近似语音发音。因为 Metaphone 隐含地忽略了数字和标点符号，所以它是文本字符串模糊匹配的便利工具。

这里有一个在系统或应用程序日志中发现内容趋势的有趣例子:

```
#!/bin/php

<?
    $keys = array();
    $counts = array();
    $total = 0;

    foreach (file($_SERVER['argv'][1]) as $line) {
        $total++;
        $lphone = metaphone($line);
        $keys[$lphone] = trim($line);
        $counts[$lphone] = $counts[$lphone] + 1;
    }

    arsort($counts);

    $topten = array_slice($counts, 0, 10, true);

    foreach ($topten as $comm => $count) {
        print round((($count * 100) / $total), 2)."%\t\"".$keys[$comm]."\"\n";
    }
?> 
```

这将让你得到类似于`/var/log/messages` :
的文件的“形状”

```
guidance: ~/trend.php /var/log/messages

64.82%  "May 24 13:52:29 guidance sshd[30083]: Did not receive identification string from 151.101.2.217"
11.55%  "May 24 13:49:34 guidance sshd[31567]: Received disconnect from 151.101.194.217: 11: disconnected by user"
10.27%  "May 24 13:12:55 guidance sshd[175458]: Accepted publickey for root from 151.101.130.217 port 49141 ssh2"
2.84%   "May 24 13:52:24 guidance sshd[29567]: Connection closed by 151.101.2.217 [preauth]"
1.21%   "May 24 13:48:13 guidance sshd[174773]: Received disconnect from 151.101.194.217: 11: Closed due to user request. [preauth]"
1.11%   "May 24 13:44:13 guidance altsshd[125993]: Received disconnect from 10.126.138.57: 11: disconnected by user"
0.96%   "May 24 13:39:43 guidance altsshd[105719]: Accepted publickey for op from 151.101.130.217 port 44578 ssh2"
0.95%   "May 24 13:50:32 guidance sodad[173222]: sodad-ipc_temp (PID 173222) exiting"
0.95%   "May 24 13:51:01 guidance sodad[194844]: sodad (PID 194844) starting"
0.42%   "May 24 13:41:59 guidance sshd[115807]: Authorized to lbonanomi, krb5 principal bonanomi@DEV.TO (krb5_kuserok)" 
```