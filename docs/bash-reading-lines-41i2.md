# Bash:读台词

> 原文：<https://dev.to/adzubla/bash-reading-lines-41i2>

从 bash 中的$文件中读取行:

```
while read LINE
do
    echo "$LINE"
done < $FILE 
```

从标准输入中读取:

```
while read LINE
do
    echo "$LINE"
done <&0 
```

从文件中读取行作为脚本参数，如果没有找到，从标准输入:

```
while read LINE
do
    echo "$LINE"
done < "${1:-/dev/stdin}" 
```

来源:http://mywiki.wooledge.org/BashFAQ/024