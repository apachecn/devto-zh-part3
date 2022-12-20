# 修复了运行“ng serve”时 Angular-cli 的“JavaScript 堆内存不足”错误

> 原文：<https://dev.to/andreisfedotov/fix-of-angular-cli-javascript-heap-out-of-memory-error-while-running-ng-serve--1jjh>

**第一个选项:**
运行`node`命令为:
`node --max_old_space_size=8048 ./node_modules/@angular/cli/bin/ng serve`
其中 *8048* 是以兆字节为单位的新内存限制(默认为~ *1500* )。

或者将命令添加到 *package.json* 文件中:

`"build-serve": "node --max_old_space_size=8048 ./node_modules/@angular/cli/bin/ng serve"`

并作为`npm run build-serve`运行

**第二种选择:**
快速有效解决*窗口*
打开`C:\Users\userName\%AppData%\Roaming\npm`
复制/粘贴以下代码到`ng.cmd` :

```
@IF EXIST "%~dp0\node.exe" (
    "%~dp0\node.exe" --max_old_space_size=8048 "%~dp0\node_modules\@angular\cli\bin\ng" %* 
) ELSE (
    @SETLOCAL 
    @SET PATHEXT=%PATHEXT:;.JS;=;% 
    node --max_old_space_size=8048 "%~dp0\node_modules\@angular\cli\bin\ng" %* 
) 
```

Enter fullscreen mode Exit fullscreen mode

UPD(2020 年 5 月 7 日)
**第三种选择:**
将其设置为环境变量。
`SET NODE_OPTIONS=--max_old_space_size=8048`
感谢 [@jonyadamit](https://dev.to/jonyadamit) 的分享

**干杯！**