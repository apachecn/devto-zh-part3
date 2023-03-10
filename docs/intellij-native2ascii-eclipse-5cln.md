# 在 IntelliJ 中使属性的 native2ascii 转换与 Eclipse 兼容

> 原文：<https://dev.to/shiena/intellij-native2ascii-eclipse-5cln>

# intellijとeclipseのnative2ascii 変換の違い

在 IntelliJ 中，在`Settings`的`File Encodings`中打开`Transparent native-to-ascii conversion`后，Java 的属性文件会透明地进行 native2ascii 变换。

```
log.error=エラーが起きました 
```

```
log.error=\u30A8\u30E9\u30FC\u304C\u8D77\u304D\u307E\u3057\u305F 
```

但是，如果使用 Pleiades All in One 附带的 Properties Editor，则与 IntelliJ 不同，会转换为小写。

```
log.error=\u30a8\u30e9\u30fc\u304c\u8d77\u304d\u307e\u3057\u305f 
```

在 Eclipse 使用者占大部分的情况下一起开发的话，即使只变更 1 个文字，由于大小写的不同，几乎所有的值都会出现差异，很难分辨出真正的差异。

# 在 IntelliJ 中也变换为小写

IntelliJ 还允许进行特殊设置，例如 native2ascii 的大小写转换。
因为是利基，所以有点麻烦，但是在`idea.properties`中追加以下的值。

```
idea.native2ascii.lowercase=true 
```

## idea.propertiesの場所

在 Windows 和 Linux 上，它位于安装 IntelliJ 的文件夹中。

```
<IntelliJ IDEA installation folder>/bin/idea.properties 
```

在 Mac 上也同样位于安装了 IntelliJ 的文件夹中，但是复制到
`~/Library/Preferences/IntelliJIdea12/idea.properties`的话这个优先。
复制目标文件夹因版本而异，升级时请再次复制并设定，以免忘记。

```
~/Library/Preferences/IntelliJIdea12/idea.properties
/Applications/IntelliJ IDEA 12.app/bin/idea.properties 
```

这样，即使是使用 Eclipse 较多的开发也很放心。

# [T1】IntelliJ IDEA 2018.3 帮助](#intellij-idea-20183-help)

以上内容参考了 IntelliJ 的帮助。

*   [资源包](https://www.jetbrains.com/help/idea/resource-bundle.html)
*   [资源包](https://pleiades.io/help/idea/resource-bundle.html)