# 趴下小子。(按端口终止进程)

> 原文：<https://dev.to/f1lt3r/down-boy-kill-by-port-1cmb>

需要通过端口终止进程吗？试试这个...

[![Cute dog](img/3496795cc4aa1e22a35c0db6837d3469.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--85YvjbGl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mwjl1xsp7e57t311up5k.jpg)

## 设置

将这个 bash 脚本作为`downboy`保存到您的 bin 目录:

```
#!/bin/bash
for PORT in $@
do PID=$(lsof -i :$PORT | awk 'FNR ==2 {print $2}')
    echo "kill -9 $PID"
    kill -9 $PID
done 
```

使`downboy`可执行:

```
chmod +X ./downboy 
```

## 用法

现在你可以很容易地通过端口关闭任意数量的进程:

```
sudo downboy 80 8080 3000 
```

拜拜。