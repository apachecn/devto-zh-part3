# React-Native/Expo/Cygwin“意外令牌”问题

> 原文：<https://dev.to/eightbit/react-nativeexpocygwin-unexpected-token-problem-2k6n>

大家好:我有一个非常棘手的问题。
安装 react-devtools 后，我无法再运行 expo-cli。
在我这么做之前，一切都很好。我现在得到了下面的错误。
除了不能运行 expo，我的 Powershell 权限也被修改为“受限”(修复)。为了解决这个问题，我甚至将我的注册表恢复到了前一天。
我已经卸载/重装了 node 和 yarn。

错误指示 Cygwin，我的电脑上没有安装(在注册表搜索中没有显示)，但是，搜索我的电脑，我看到 Cygwin 是用 Git 安装的:
C:\ Program Files \ Git \ usr \ share \ Cygwin
C:\ Program Files \ Git \ usr \ bin \ Cygwin-console-helper . exe
C:\ Program Files \ Android \ Android Studio \ bin \ lldb \ lib \ distutils \ cygwinccompiler . py
C:\ n

任何帮助都将不胜感激。
Windows 10
npm: 6.6.0
节点:v10.15.0
纱线:v1.13.0

错误
Expo start-Android
At C:\ Users \ name \ AppData \ Local \ Yarn \ bin \ Expo . PS1:5 char:13
+*CYGWIN*basedir =`cygpath -w "$basedir"`；；表达式或语句中的
+
意外标记“)”。
At C:\ Users \ name \ AppData \ Local \ Yarn \ bin \ Expo . PS1:8 char:3
+if[-x " $ basedir/pwsh "]；然后
+
在 if 语句中的“if”后缺少“(”。
At C:\ Users \ name \ AppData \ Local \ Yarn \ bin \ Expo . PS1:8 char:5
+if[-x " $ basedir/pwsh "]；然后“[”后的
+
缺少类型名。
位于 C:\ Users \ name \ AppData \ Local \ Yarn \ bin \ Expo . PS1:9 char:20
+...edir/pwsh" "$basedir/../Data/global/node_modules/。bin/expo.ps1" "$@ "...
+
意外的标记“" $basedir/../Data/global/node_modules/。bin/expo.ps1 " '中的表达式或语句。在 C:\ Users \ name \ AppData \ Local \ Yarn \ bin \ Expo . PS1:9 char:73
+...edir/pwsh" "$basedir/../Data/global/node_modules/。bin/expo.ps1" "$@"
+
表达式或语句中出现意外标记" $@ "。
+category info:parser error:(:)[]，parse exception
+FullyQualifiedErrorId:unexpected token