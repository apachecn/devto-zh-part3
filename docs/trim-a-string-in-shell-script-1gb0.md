# 在 shell 脚本中修剪字符串

> 原文：<https://dev.to/adzubla/trim-a-string-in-shell-script-1gb0>

仅仅使用 echo 命令就很棒:

```
$ str="  a string  "
$ echo "[$str]"
[  a string  ]

$ new=$(echo $str)
$ echo "[$new]"
[a string] 
```

要修剪整个文件:

```
sed 's/^[ \t]*//;s/[ \t]*$//' < input_file 
```