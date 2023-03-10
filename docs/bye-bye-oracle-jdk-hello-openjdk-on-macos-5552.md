# 拜拜甲骨文 JDK，你好 macos 上的 OpenJDK

> 原文：<https://dev.to/ramx/bye-bye-oracle-jdk-hello-openjdk-on-macos-5552>

读完这篇文章后，我想从我的系统中移除 Oracle JDK 8，安装 OpenJDK 11。

*   在我开始之前，我用`java -version`检查了我的 java 版本

[![checking version of java ](img/e23c82d33a5a832ba9e9d6012df7a8d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2q1F3TCz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gmolx7dv4o0dn0wwktrp.png)

*   第一步是删除 Oracle JDK，我在这里找到了[指令](https://stackoverflow.com/questions/19039752/removing-java-8-jdk-from-mac)。

```
sudo rm -rf /Library/Java/JavaVirtualMachines/jdk1.8.0_151.jdk

sudo rm -rf /Library/PreferencePanes/JavaControlPanel.prefPane
sudo rm -rf /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
sudo rm -rf /Library/LaunchAgents/com.oracle.java.Java-Updater.plist
sudo rm -rf /Library/PrivilegedHelperTools/com.oracle.java.JavaUpdateHelper
sudo rm -rf /Library/LaunchDaemons/com.oracle.java.Helper-Tool.plist
sudo rm -rf /Library/Preferences/com.oracle.java.Helper-Tool.plist 
```

[![removing oracle jdk](img/d07409bfb6f78e45888afd11d09a1605.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J7vZ-hh7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffxf78ebfi96e4i17lxa.png)

*   卸载后安装很容易`brew cask install java`

[![installing OpenJDK using brew](img/922c5210a9c044f0b674801e60cfce80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H10seczj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5kxdc9f78c6kkvzu82na.png)

*   再次安装并检查 java 版本

[![installation and version check](img/660b419a428b55920002ef843996049d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QJRBnDCQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0bndylwbsazs4pv5591z.png)

就是这样！

干杯:)