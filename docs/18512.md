# 消毒 Debian

> 原文：<https://dev.to/lestephane/sanity-checking-debian-1-2f4g>

*(这是我在 dev.to 上的第一篇帖子，试水，温柔一点)*

今天我想检查我系统上的文件是否与 debian 软件包中的文件不完全相同。所以经过一番研究，我决定使用 *debsums* 命令:

```
$ sudo debsums -s
debsums: changed file /usr/share/applications/code-url-handler.desktop (from code package)
debsums: changed file /usr/share/applications/code.desktop (from code package)
debsums: changed file /usr/share/applications/gedit.desktop (from gedit package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/__init__.cpython-35.pyc (from python3-cupshelpers package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/config.cpython-35.pyc (from python3-cupshelpers package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/cupshelpers.cpython-35.pyc (from python3-cupshelpers package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/installdriver.cpython-35.pyc (from python3-cupshelpers package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/openprinting.cpython-35.pyc (from python3-cupshelpers package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/ppds.cpython-35.pyc (from python3-cupshelpers package)
debsums: changed file /usr/lib/python3/dist-packages/cupshelpers/__pycache__/xmldriverprefs.cpython-35.pyc (from python3-cupshelpers package) 
```

继续重新安装受影响的软件包:

```
$ sudo debsums -s 2>&1 | \
  grep -oE "from .* package" | \
  sort | uniq | \
  while read PKG; do echo "$PKG" | cut -d ' ' -f 2;
  done;
code
gedit
python3-cupshelpers
$ sudo aptitude reinstall code gedit python3-cupshelpers
... 
```

并(再次)检查:

```
$ sudo debsums -s
debsums: changed file /usr/share/applications/code-url-handler.desktop (from code package)
debsums: changed file /usr/share/applications/code.desktop (from code package) 
```

呃。工作中有些事情我还没有完全理解。由于我无法证明花了这么多时间去追查这件事，我找到了该联系的人:

```
$ apt-cache show code | grep -E "Maintainer" | uniq -c
     50 Maintainer: Microsoft Corporation <vscode-linux@microsoft.com> 
```

然后我给他们发了一封邮件:

```
From: Le Stephane
To: vscode-linux@microsoft.com
Subject: /usr/share/applications/code(-url-handler).desktop files always show up as modified in debsums

Dear VS Code maintainers, 

I get debsums errors on files belonging to your .deb package, even after re-installing it. Can you advise?

$ sudo debsums -s
debsums: changed file /usr/share/applications/code-url-handler.desktop (from code package)
debsums: changed file /usr/share/applications/code.desktop (from code package)

$ dpkg -l code
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                                  Version                 Architecture            Description
+++-=====================================-=======================-=======================-===============================================================================
ii  code                                  1.29.1-1542309157       amd64                   Code editing. Redefined.

Best 
```

# 外卖(目前为止)

*   在 dev.to 上发帖很有趣，我会再做一次
*   有一个 *debsums* 命令非常酷
*   *debsums* + *智能重装*让我的系统再次焕然一新...
*   ...除了几个文件，原因不明
*   我现在还不需要找那个理由。但是我可以找到在哪里寻求帮助
*   同样，我也不需要写完我的文章。也许观众中有人会知道为什么我会有这种行为。如果没有，维护者可能会及时给我指出原因。然后我可以修改文章。
*   这篇文章是我的第一篇，也只是一个开始。如果我对安全性很认真的话，我还应该检查我的操作系统目录中当前存在的哪些文件不属于某个包。