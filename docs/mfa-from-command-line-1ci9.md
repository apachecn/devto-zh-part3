# 来自命令行的 MFA

> 原文：<https://dev.to/abdallah/mfa-from-command-line-1ci9>

MFA、2FA、两步验证等。现在到处都是。这是件好事。

使用手机获取认证码的问题是，你需要随时将它放在手边(至少当你想登录时)，并且你必须阅读代码，然后输入代码(太多步骤)

一种可能的替代方法是使用命令行`oathtool`

这是我的片段，我在我的`.bashrc`
中添加了下面一行

```
function mfa () { oathtool --base32 --totp "$(cat ~/.mfa/$1.mfa)" | xclip -sel clip ;} 
```

一些准备工作:

```
sudo apt install oathtool xclip<br>mkdir ~/.mfa<br> 
```

如果你想在基于手机的 MFA 生成器和 shell 上使用同一个帐户，请同时设置它们。只需使用生成的字符串在 [Google Authenticator](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2) 中设置账户(作为一个例子)并将其添加到`~/.mfa/account_name.mfa`

使用`xclip`自动将 6 位验证码复制到剪贴板。你可以粘贴它。

以上设置在 Ubuntu 中有效。没有在其他系统上试过。