# 可能的错误:导入错误:没有名为 zipfile 的模块

> 原文：<https://dev.to/philiplavoie/ansible-error-importerror-no-module-named-zipfile-1abf>

你好世界，

这可能更多的是在提醒我自己...然而，如果你碰巧使用 Ansible 2.0，Ubuntu 18.04.1 LTS，并且在你的目标上安装了 Python 的最小当前安装，你会得到这个结果:

```
10.0.3.73 | FAILED! => {
    "changed": false,
    "module_stderr": "Shared connection to 10.0.3.73 closed.\r\n",
    "module_stdout": "Traceback (most recent call last):\r\n  File \"/home/ubuntu/.ansible/tmp/ansible-tmp-1548966135.59-128122597125522/ping.py\", line 17, in <module>\r\n    import zipfile\r\nImportError: No module named zipfile\r\n",
    "msg": "MODULE FAILURE",
    "rc": 1
} 
```

Enter fullscreen mode Exit fullscreen mode

不是你，是他们，这是一个可怕的错误。

我在跑:`ansible web -i inventory -m ping -u ubuntu -k`

我的库存文件看起来像:

```
[allservers]
10.0.3.202
10.0.3.19
10.0.3.73

[database]
10.0.3.202

[web]
10.0.3.19
10.0.3.73 
```

Enter fullscreen mode Exit fullscreen mode

这是我需要做的来修复它...在你的清单中添加这样的比特:

```
[allservers:vars]
ansible_python_interpreter=/usr/bin/python3

[database:vars]
ansible_python_interpreter=/usr/bin/python3

[web:vars]
ansible_python_interpreter=/usr/bin/python3 
```

Enter fullscreen mode Exit fullscreen mode

现在你的跑步应该会更快乐一点:

```
$ ansible allservers -i inventory -m ping -u ubuntu -k
SSH password:
10.0.3.73 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
10.0.3.19 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
10.0.3.202 | SUCCESS => {
    "changed": false,
    "ping": "pong"
} 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！