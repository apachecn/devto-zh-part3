# 我从 keepassxc 到 pass 的迁移(密码存储)

> 原文：<https://dev.to/marceloandrade/my-migration-from-keepassxc-to-pass-password-store-4cd3>

# 背景

我最初的方法是使用密码管理器 [keepassxc](https://keepassxc.org/) 来保证我的电脑中长长的密码/秘密列表的安全，这种方法效果很好，与我的手机分享的方法是将 kdbx 文件放在 Dropbox 中，并使用 KeePassDroid 应用程序，以便能够在旅途中使用我的密码。

# 为什么迁移？

我现在的设置开始出现一些问题，主要问题是我必须为密码管理器打开一个窗口，我必须在那里搜索和复制/粘贴数据，或者要求为我执行自动输入，我开始不喜欢这样，因为作为一名开发人员，我通常只在两个窗口中工作:浏览器和终端，所以这就是为什么我开始寻找替代方案。第二个问题是，当我试图用 mutt 配置 gmail 时，我不得不把我的密码以纯文本格式放在一个文件中。

# 被选中的人

当我设置我的 mutt/mbsync/msmtp 从终端使用 gmail(另一篇文章的主题)时，我发现你必须在纯文本配置文件中输入你的凭据，我不想这样做，其他一些人也没有这样做，他们有一个解决方案: [pass](https://www.passwordstore.org/) 使用这个简单的 cli 实用程序，我不再需要在配置文件中编写密码，它可以自动从我的存储中使用密码，而无需干预。

## 优势过关

*   它遵循 Unix 的理念，做好一件事，并且可以组合。
*   每个密码都存在于一个 gpg 加密文件中，该文件可以使用标准的 unix 命令 cp、mv、rm 来组织
*   它具有复制到内置剪贴板的功能
*   它使用`git`跟踪密码更改。这太棒了，因为这样我可以毫不费力地将我的密码分发给其他设备。
*   在迁移之前，我检查了它是否支持从其他密码管理器导入，并且确实有一个支持 25 个不同管理器的扩展 [pass-import](https://github.com/roddhjav/pass-import#description)
*   也有很好的兼容客户端列表，我会为我的手机使用安卓密码商店。
*   它有几个浏览器扩展，无需打开另一个窗口就可以很容易地在网站上填写/提交

# 初始设置

所有的设置都是在装有 Pop 的 linux 机器上完成的！OS 18.10。

## 配置 GPG

首先，我们需要创建一个 gpg 密钥，pass 将使用它来加密/解密密码文件。

```
% gpg --gen-key 
```

Enter fullscreen mode Exit fullscreen mode

它将要求一个真实的密钥名和一个电子邮件，以及一个至少 8 个字符的密码，并有一个数字。

```
⏚ [marcelo:~] % gpg --gen-key

gpg (GnuPG) 2.2.8; Copyright (C) 2018 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: Pass Migration
Email address: pass.migration@gmail.com
You selected this USER-ID:
    "Pass Migration <pass.migration@gmail.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key B9E32CDD4190FC22 marked as ultimately trusted
gpg: revocation certificate stored as '/home/marcelo/.gnupg/openpgp-revocs.d/23434EEF535B0E7C702E36A9B9E32CDD4190FC22.rev'
public and secret key created and signed.

pub   rsa3072 2019-04-11 [SC] [expires: 2021-04-10]
      23434EEF535B0E7C702E36A9B9E32CDD4190FC22
uid                      Pass Migration <pass.migration@gmail.com>
sub   rsa3072 2019-04-11 [E] [expires: 2021-04-10] 
```

Enter fullscreen mode Exit fullscreen mode

检查 gpg 密钥是否存在:

```
% gpg -k

/home/marcelo/.gnupg/pubring.kbx
--------------------------------
pub   rsa3072 2019-04-11 [SC] [expires: 2021-04-10]
      23434EEF535B0E7C702E36A9B9E32CDD4190FC22
uid           [ultimate] Pass Migration <pass.migration@gmail.com>
sub   rsa3072 2019-04-11 [E] [expires: 2021-04-10] 
```

Enter fullscreen mode Exit fullscreen mode

## Init 商店

初始化 pass 中的存储的命令如下，您需要在前面的步骤中创建的密钥 id 作为参数:

```
% pass init "Pass Migration" 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的~/中创建一个目录。password-store/并将写一个. gpg-id，这样 pass 就知道从现在开始用什么密钥来加密/解密了。

```
mkdir: created directory '/home/marcelo/.password-store'
Password store initialized for Pass Migration (migration) 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化存储中的 git

要使用 git 跟踪所有密码更改，您只需在目录中初始化 repo:

```
% pass git init

Initialized empty Git repository in /home/marcelo/.password-store/.git/ 
```

Enter fullscreen mode Exit fullscreen mode

这允许您保留密码的历史记录，并有助于将存储分发到其他设备，并使它们保持同步。

## 添加 git 遥控器

为了备份/分发您的密码，您可以为设置一个遥控器。密码存储库，你需要改变<>之间的数据

```
% pass git remote add origin git@github.com:<your-user>/<your-repo>.git
% pass git push -u master 
```

Enter fullscreen mode Exit fullscreen mode

此后，当 pass 中发生任何事情时，它将自动为每个更改创建一个提交，您只需要执行推/拉操作来同步它

```
% pass git push
% pass git pull 
```

Enter fullscreen mode Exit fullscreen mode

# 日常使用的密码存储

## 添加新密码

插入命令允许您手动输入要保存的密码。

```
% pass insert Personal/testing
Enter password for Personal/testing:******
Retype password for Personal/testing:******
[master bf1bc88] Add given password for Personal/testing to store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 Personal/testing.gpg 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，pass 创建了 testing.gpg 文件并提交给 repo。

如果您希望 pass 为您生成一个密码，请使用 generate 命令:

```
% pass generate Personal/testing2
[master 8b0da79] Add generated password for Personal/testing2.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 Personal/testing2.gpg
The generated password for Personal/testing2 is:
kqc9b7v=c7~e)D/vIBScEz;%u 
```

Enter fullscreen mode Exit fullscreen mode

假设 pass 加密所有文件，您可以向文件添加一些其他元数据，如用户名、URL、机密问题答案等。为此，您应该在多行中使用 insert，您唯一需要记住的是，第一行始终是密码。

```
% pass insert -m Personal/testing-multiline
Enter contents of Personal/testing-multiline and press Ctrl+D when finished:

thisismypassword
url: http://example.com
login: marceloandrade

question1: xhdjahdfasdjfkasjdfaks
question2: fjasdkfj9dfas0dfa9d8f7
[master 3a133ec] Add given password for Personal/testing-multiline to store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 Personal/testing-multiline.gpg 
```

Enter fullscreen mode Exit fullscreen mode

## 消耗密码

要使用密码，只需执行`pass Personal/testing`即可，它将回显您的密码，如果您不想在终端中显示密码，请传递-c 参数以复制到剪贴板`pass -c Personal/testing`，这将使您的密码在剪贴板中保留 45 秒，足以粘贴到您需要的位置。

## 更新密码

您可以使用`pass edit Personal/testing-multiline`打开一个临时文件中的解密内容，您可以使用默认编辑器进行更改并保存&退出。

```
[master fa72ec0] Edit password for Personal/testing-multiline using nvim.
 1 file changed, 0 insertions(+), 0 deletions(-)
 rewrite Personal/testing-multiline.gpg (100%) 
```

Enter fullscreen mode Exit fullscreen mode

## 在 git 中直接修改密码有差异

编辑完密码后，您可以直接用 git 检查更改了什么:

```
% pass git log 
```

Enter fullscreen mode Exit fullscreen mode

要复制您进行更改的提交的 sha1

```
% pass git show fa72ec0a9e51459e8223a6872647f3388ff8a96f
commit fa72ec0a9e51459e8223a6872647f3388ff8a96f (HEAD -> master)
Author: Marcelo Andrade R
Date:   Thu Apr 11 12:11:11 2019 -0500

    Edit password for Personal/testing-multiline using nvim.

    diff --git a/Personal/testing-multiline.gpg b/Personal/testing-multiline.gpg
    index 1c957b8..7dfa9a4 100644
    --- a/Personal/testing-multiline.gpg
    +++ b/Personal/testing-multiline.gpg
    @@ -1,4 +1,4 @@
    -thisismypassword
    +thisismypassword2
     url: http://example.com
     login: marceloandrade 
```

Enter fullscreen mode Exit fullscreen mode

## 删除密码

要删除使用`pass rm Personal/testing`并将从商店中删除。

```
Are you sure you would like to delete Personal/testing? [y/N] y
removed '/home/marcelo/.password-store/Personal/testing.gpg'
[master 151f502] Remove Personal/testing from store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 Personal/testing.gpg 
```

Enter fullscreen mode Exit fullscreen mode

# 具体从 keepassxc 迁移

## 安装通行证-导入

转到 [pass-import github repo](https://github.com/roddhjav/pass-import/) 并以最适合您的需求和环境的方式安装它，我克隆了它，make，然后 sudo make install

## 从 keepassxc 生成 file.csv

从 KeePassXC 应用程序中，用您的主密码打开您的数据库，然后转到菜单数据库/导出到 CSV 文件...这将把文件写到磁盘，确保它是安全的，因为你所有的密码都是明文写在这个文件里的。

## 运行通行证导入 keepassxc 文件. csv

导入工具非常容易使用:

```
% pass import keepassxc /path/to/file.csv 
```

Enter fullscreen mode Exit fullscreen mode

该命令会将每个条目插入到存储中，并对每个条目进行提交。

## 撕碎文件

因为 file.csv 确实不是保存密码的安全方式，所以请确保安全地删除它:

```
% shred -u file.csv 
```

Enter fullscreen mode Exit fullscreen mode

# 
 [T3】
结论](#conclusion) 

这篇文章有点长，如果有不清楚的地方，我很乐意回答。也许我会写另一篇关于 android 应用配置的文章来使用 git repo。

# 资源

1.  [通过](https://www.passwordstore.org/)
2.  [通过导入扩展](https://github.com/roddhjav/pass-import/)