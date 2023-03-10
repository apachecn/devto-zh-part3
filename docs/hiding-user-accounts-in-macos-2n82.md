# 在 MacOS 中隐藏用户帐户

> 原文：<https://dev.to/ssbozy/hiding-user-accounts-in-macos-2n82>

## 在 MacOS 中隐藏用户账号

1.  从登录窗口隐藏用户帐户。—简单
2.  从登录窗口和系统偏好设置中隐藏用户帐户—高级

### 1。简单版本

简单版本在登录过程中隐藏用户帐户，使其不显示在用户帐户列表中。该帐户可以在“系统偏好设置”和/用户中看到。下面是实现这种效果的方法:

`sudo defaults write /Library/Preferences/com.apple.loginwindow HiddenUsersList -array-add <username_to_hide>`

现在你可以重新启动，上面提到的用户名将不会出现在登录窗口。如果您想恢复这种情况，可以运行以下命令

`sudo defaults delete /Library/Preferences/com.apple.loginwindow HiddenUsersList`

### 2。高级版本

有多种方法可以实现完全隐藏的用户帐户。此方法包括创建一个在“系统偏好设置”中不再可见的用户帐户，创建一个除/Users 之外的个人文件夹，并隐藏所有共享装载点。这是一个真实的忍者账号。

创建主目录不是/Users 的隐藏用户帐户

`sudo dscl . create /Users/dummy NFSHomeDirectory /var/dummy`
T1】

此命令删除长名称为“Dummy”的用户的公用文件夹共享点