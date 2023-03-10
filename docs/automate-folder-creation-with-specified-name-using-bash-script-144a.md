# 使用 Bash 脚本自动创建指定名称的文件夹

> 原文：<https://dev.to/annlin/automate-folder-creation-with-specified-name-using-bash-script-144a>

我已经在终端中创建了一个具有特定命名模式的新文件夹。
一个名为`/dayX-title`的文件夹，其中包含一个`README.md`文件。

例如:`/codingclub/challenge/dayX-title/README.md`

```
linxea at tmo in ~/codingclub/challenge
$ ls day0-the-beginning-of-the-coding-club
day1-may-the-odds-be-in-your-favour
day2-so-far-so-good
day3-oh-man 
```

这就是我如何在一个新文件夹中创建一个新文件。我手动浏览文件夹列表以获得下一个计数。然后键入带破折号和破折号的标题。

```
$ touch day4-time-to-automate/README.md 
```

我越来越懒了，所以我们将编写一个 bash 脚本来做这件事。

我想写一个命令`./new time to automate`来创建带有 README.md 的`/day4-time-to-automate`。

### 步骤:

1.  遍历所有文件夹
2.  找出最大的天数
3.  用给定的输入文件名创建一个包含 README.md 文件的新文件夹

```
#!/bin/bash

# Take in all arguments in one variable
input="$*"
folderTitle=$input

# Check if there is input, otherwise exit script
if [[ -z "${folderTitle// }" ]]; then
  echo "Where is your awesome title yo?"
  exit 1
fi

# Replace space to -
folderTitle=${folderTitle// /-}

# Loop through the list of directories and find the biggest number
biggestNumber=-1
for dir in $(ls)
do
  number=`sed 's/[^0-9]//g' <<< $dir`
  if (( number > biggestNumber )); then
    biggestNumber=$number
  fi
done

# Increment the biggest number by 1
((biggestNumber=biggestNumber+1))

# Create a new folder and README.md file
newName=day$biggestNumber-$folderTitle
mkdir -p $newName
echo -e "TITLE: Day $biggestNumber $input\n" > $newName/README.md

# Print success message to make me feel good
echo Created $newName yo 🦄 
```

Tada，现在你只需要通过运行`chmod -x`使脚本可执行，然后运行带有 title 的命令`./new.sh`。

```
$ chmod -x new.sh # make the script executable
$ ./new.sh as usual this task takes longer than i expected 
```

上传我的脚本到 Repl.it，在[https://repl.it/@linxea/Create-New-Folder-and-File](https://repl.it/@linxea/Create-New-Folder-and-File)查看。您可能需要登录才能使用它。

### 值得花时间拿起 BASH 来自动化这样一件小事吗？

一个微小的肯定。

### 另一个问题改天再解决，

我想用`sed`提取出`day123-title-that-may-contain-123`之间的数字`123`，但是 regex 扰乱了我的思维。🤯我该怎么做？如何编写一个正则表达式，只返回在`day`和`-everything-else-after-dash`之间的数字？请像解释我 5 岁一样解释正则表达式。

### 结束