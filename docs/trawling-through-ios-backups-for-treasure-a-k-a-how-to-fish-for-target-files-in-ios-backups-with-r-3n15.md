# 用 R 搜索 iOS 备份中的宝藏(也称为如何在 iOS 备份中搜索目标文件)

> 原文：<https://dev.to/hrbrmstr/trawling-through-ios-backups-for-treasure-a-k-a-how-to-fish-for-target-files-in-ios-backups-with-r-3n15>

在最近的[上一篇文章](https://dev.to/hrbrmstr/quick-hit-handling-cocoa-core-data-timestamps-in-r-31fi-temp-slug-8546253)中，我厚颜无耻地谈论了我如何获得目标 SQLite 文件的“困难部分”,该文件包含了我周末痴迷的“割草历史”。因此，我们将在这篇文章中讨论如何做到这一点(在 iOS 备份中查找内容),以及如果你在 macOS 中这样做，如何处理一些“陷阱”。

### macOS(刀)

科特·维尔、伯德·布莱克特和马克·布利茨坦创作了一些令人惊叹的歌词，鲍比·达林对这些歌词做了一些非常非常好的诠释:

我提出这一点是为了谈论 macOS 的切割、撕咬和危险边缘，这是苹果在保护你的隐私和让敏感文件和目录远离恶意软件的利齿方面的某种混合尝试(并重新推销我的 [`{mactheknife}`包](https://git.sr.ht/~hrbrmstr/mactheknife))。你可以在[电子照明公司](https://eclecticlight.co/?s=sandbox)更全面地阅读苹果的新保护措施。出于这篇博客的目的，苹果的 macOS 沙箱政策意味着你必须做一些额外的步骤来获得与 iOS 备份相关的文件夹和文件的访问权限(这是`~/Library/Application Support/MobileSync/Backup/`)。

如果你想让 RStudio、R 和任何用 Rscript 运行的程序访问这些沙箱区域，你需要启用这些程序和可执行文件的“全磁盘访问”。首先你需要打开`System Preferences > Security & Privacy`，然后激活`Privacy`标签。保持窗口打开，并轻按锁以解锁设置。

[![Full Disk Access settings panel in macOS](img/f1c12c3dbefce2b5f335eb382771cb44.png)](https://i1.wp.com/rud.is/b/wp-content/uploads/2019/06/full-disk-access.png?ssl=1)

macOS 中的全磁盘访问设置面板

添加 RStudio 很容易。只需激活 Finder 并点击`Cmd` `Shift` `A`，然后找到“RStudio”应用程序并将其拖动到您在上一步中打开的窗格+标签中。回到 Finder，点击`Cmd` `Shift` `G`并粘贴到`/Library/Frameworks/R.framework/Resources/bin`并转到那个文件夹。将`R`和`Rscript`分别拖入上述步骤中的窗格+选项卡。最后(这花了我一分钟时间)你*也*需要(再次，在 Finder 中)点击`Cmd` `Shift` `G`并粘贴到`/Library/Frameworks/R.framework/Versions/3.6/Resources/bin/exec`中，并将*可执行文件*拖到安全&隐私/全磁盘访问窗格+标签中。完成所有这些操作后，锁定“系统偏好设置”面板并将其关闭。

需要注意的是，您刚刚给了“R”和任何从您的用户空间调用 R 的东西完全(嗯，*几乎是*)访问您系统上每个沙箱区域的权限。R 是一种通用编程和脚本语言，这意味着任何知道您添加了这些可执行文件的恶意代码都可以使用 R 来读写您系统上的任何区域。

同样需要注意的是，我不得不在最后一个条目中使用`3.6`和`Current`符号链接，这意味着你需要在安装每个新的 R 版本时都这样做。

我希望安装传统 Windows 操作系统的人不要跳过这一部分，因为你需要去[这里](http://bfy.tw/Nvfc)来找出你的 iOS 备份文件夹在哪里，以浏览这篇文章的其余部分。

### 偷偷摸摸的在拐角处

Windows 用户希望至少阅读前一部分的最后一点，以找出他们的 iOS 备份在哪里。在 macOS 上那是`~/Library/Application Support/MobileSync/Backup/`。你需要一个本地备份(现在大多数人只使用 iCloud 备份),苹果会告诉你怎么做。

一旦你知道你有一个(**未加密的**)备份，只需进入你的 iOS 备份目录，按日期列出文件，并记下最近备份的名称/路径。现在我们可以找点乐子了。

```
library(XML) # to read plist (property list) files
library(tidyverse) # for printing and access to sqlite dbs

# replace this with the relative path to your most recent backup dir
mb <- "~/Library/Application Support/MobileSync/Backup/28500cd31b9580aaf5815c695ebd3ea5f7455628-20190601-165737"

list.files(mb, pattern = ".*\\.(db|plist)$")
## [1] "Info.plist" "Manifest.db" "Manifest.plist" "Status.plist" 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码为 iOS 备份寻找一些关键的元数据文件。

*   `Info.plist`在您的设备上有信息
*   在 SQLite 数据库的备份中有大量关于所有文件的信息
*   `Manifest.plist`备份中有一些额外的元数据，包括备份中包含的应用程序
*   `Status.plist`包含有关备份状态的信息

让我们来看看 plist:

```
info_p <- file.path(mb, "Info.plist")
file.copy(info_p, "/tmp", overwrite = TRUE)
system2("plutil", args=c("-convert", "xml1", "-o", "/tmp/Info.plist", "/tmp/Info.plist"))

info <- XML::readKeyValueDB("/tmp/Info.plist")

str(info)
## List of 11
## $ Device Name : chr REDACTED
## $ Display Name : chr REDACTED
## $ ICCID : chr REDACTED
## $ IMEI : chr REDACTED
## $ IPBE Backup Version: int 1
## $ Last Backup Date : POSIXct[1:1], format: "2019-06-01 21:23:02"
## $ Phone Number : chr REDACTED
## $ Product Type : chr REDACTED
## $ Product Version : chr REDACTED
## $ Serial Number : chr REDACTED
## $ Unique Identifier : chr REDACTED

status_p <- file.path(mb, "Status.plist")
file.copy(status_p, "/tmp", overwrite = TRUE)
system2("plutil", args=c("-convert", "xml1", "-o", "/tmp/Status.plist", "/tmp/Status.plist"))

status <- XML::readKeyValueDB("/tmp/Status.plist")

str(status)
## List of 6
## $ BackupState : chr "new"
## $ Date : POSIXct[1:1], format: "2019-06-01 21:22:53"
## $ IsFullBackup : logi FALSE
## $ SnapshotState: chr "finished"
## $ UUID : chr REDACTED
## $ Version : chr "3.3"

mainf_p <- file.path(mb, "Manifest.plist")
file.copy(mainf_p, "/tmp", overwrite = TRUE)
system2("plutil", args=c("-convert", "xml1", "-o", "/tmp/Manifest.plist", "/tmp/Manifest.plist"))

manifest <- XML::readKeyValueDB("/tmp/Manifest.plist")

str(manifest, 1)
## List of 8
## $ Applications :List of 745
## $ BackupKeyBag : chr __truncated__
## $ Date : POSIXct[1:1], format: "2019-06-01 20:57:40"
## $ IsEncrypted : logi FALSE
## $ Lockdown :List of 12
## $ SystemDomainsVersion: chr "24.0"
## $ Version : chr "10.0"
## $ WasPasscodeSet : logi TRUE 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我们正在制作这些文件的副本(除非您知道自己在做什么，否则不要直接使用系统管理的文件)，并且将二进制属性列表转换成纯文本 XML 属性列表，这样我们就可以用`XML::readKeyValueDB()`函数读取它们。

这些信息中的大部分对这篇博客文章来说是毫无用处的，但是我想你可能想看看系统知道的关于你的设备的隐藏的东西。我们想要检查的是 John Deere 应用程序和数据是否已纳入备份。`Applications`槽是应用元数据的名称列表。让我们看看里面有没有迪尔式的东西:

```
grep("deere", names(manifest$Applications), ignore.case = TRUE, value = TRUE)
## key 
## "com.deere.mowerplus"

str(manifest$Applications$com.deere.mowerplus, 1)
## List of 4
## $ CFBundleIdentifier : chr "com.deere.mowerplus"
## $ CFBundleVersion : chr "180"
## $ ContainerContentClass: chr "Data/Application"
## $ Path : chr "/var/containers/Bundle/Application/30DF2640-A9AA-43A0-AD87-932CA513D75A/MowerPlus.app" 
```

Enter fullscreen mode Exit fullscreen mode

赞成！这意味着我们应该在 Manifest SQLite 数据库中找到“割草机”数据。

现在，我们可以尝试跟踪 UUIDs，但是我们也可以尝试一种不那么麻烦的方法。让我们复制一份清单数据库，看看里面有什么:

```
mainf_d <- file.path(mb, "Manifest.db")
file.copy(mainf_d, "/tmp", overwrite = TRUE)
## [1] TRUE

(manifest_db <- src_sqlite("/tmp/Manifest.db"))
## src: sqlite 3.22.0 [/private/tmp/Manifest.db]
## tbls: Files, Properties 
```

Enter fullscreen mode Exit fullscreen mode

我们希望得到(希望)一个包含割草数据的 SQLite 文件，所以我们可能关心`Files`表。我们来看看那个表的结构:

```
(fils <- tbl(manifest_db, "Files"))
## # Source: table<Files> [?? x 5]
## # Database: sqlite 3.22.0 [/private/tmp/Manifest.db]
## fileID domain relativePath flags file
## <chr> <chr> <chr> <int> <blob>
## 1 c1da4199a18d0b5… AppDomain-com… "" 2 <raw 437 B>
## 2 7426ac0386e2887… AppDomain-com… Library 2 <raw 444 B>
## 3 a6393e739e1ad37… AppDomain-com… Library/WebKit 2 <raw 444 B>
## 4 c54f5c77a5e970b… AppDomain-com… Library/WebKit/Websit… 2 <raw 458 B>
## 5 578f2c96f219e95… AppDomain-com… Library/WebKit/Websit… 2 <raw 465 B>
## 6 c8833032ce7c9e9… AppDomain-com… Library/WebKit/Websit… 2 <raw 481 B>
## 7 6af21902e595f7c… AppDomain-com… Library/WebKit/Websit… 2 <raw 468 B>
## 8 4c1c49324646af0… AppDomain-com… Library/WebKit/Websit… 2 <raw 471 B>
## 9 d0636bf9b5ba2ae… AppDomain-com… Library/WebKit/Websit… 2 <raw 468 B>
## 10 0b6bb30c8abaa4e… AppDomain-com… Library/Preferences 2 <raw 458 B>
## # … with more rows 
```

Enter fullscreen mode Exit fullscreen mode

如果你有大量的应用程序，这是一个相当大的大海捞针。不过，我们也许可以缩小范围，我们将从查看那个`domain`列包含什么开始:

```
distinct(fils, domain)
## # Source: lazy query [?? x 1]
## # Database: sqlite 3.22.0 [/private/tmp/Manifest.db]
## domain                                 
## <chr>                                  
## 1 AppDomain-Outils-OBD-Facile.EOBD-Facile
## 2 AppDomain-ch.threema.iapp              
## 3 AppDomain-co.humanco.Human             
## 4 AppDomain-co.ortatech.colr-app         
## 5 AppDomain-co.vero.app                  
## 6 AppDomain-com.7thg.Tides               
## 7 AppDomain-com.AerLingus                
## 8 AppDomain-com.BloomSky.BloomSky        
## 9 AppDomain-com.PunchThrough.LightBlue   
## 10 AppDomain-com.agilebits.onepassword-ios
## # … with more rows 
```

Enter fullscreen mode Exit fullscreen mode

因此，这些是特定于应用程序的，每个应用程序中的`-`后面的位看起来像上面的`CFBundleIdentifier`。让我们确认一下:

```
filter(fils, lower(domain) %like% "%com.deere.mowerplus%") %>% 
  distinct(domain)
## # Source: lazy query [?? x 1]
## # Database: sqlite 3.22.0 [/private/tmp/Manifest.db]
## domain                       
## <chr>                        
## 1 AppDomain-com.deere.mowerplus 
```

Enter fullscreen mode Exit fullscreen mode

赞成！让我们看看那里有什么文件(希望有一个好的 SQLite 数据库):

```
filter(fils, domain == "AppDomain-com.deere.mowerplus") %>%
  select(relativePath) %>% 
  collect() 
## # A tibble: 14 x 1
## relativePath                                                     
## <chr>                                                            
## 1 ""                                                               
## 2 Library                                                          
## 3 Library/Preferences                                              
## 4 Library/Application Support                                      
## 5 Library/Application Support/com.crashlytics                      
## 6 Documents                                                        
## 7 Library/GoAppPersistentStore-GoMow.sqlite                        
## 8 Library/googleanalytics-aux-v4.sql                               
## 9 Library/googleanalytics-v3.sql                                   
## 10 Library/Preferences/com.deere.mowerplus.plist                    
## 11 Library/Application Support/ActivityCards.sqlite                 
## 12 Library/Application Support/com.crashlytics/CLSUserDefaults.plist
## 13 Library/googleanalytics-v2.sql                                   
## 14 Library/Application Support/MowTracking.sqlite 
```

Enter fullscreen mode Exit fullscreen mode

原来最后一个就是我们要找的。现在我们只需要一点关键的元数据就可以了:

```
filter(fils, relativePath == "Library/Application Support/MowTracking.sqlite") %>% 
  select(fileID, relativePath)
## # Source: lazy query [?? x 2]
## # Database: sqlite 3.22.0 [/private/tmp/Manifest.db]
## fileID relativePath                           
## <chr> <chr>                                  
## 1 ad0009ec04c44b544d37bfc7ab343869… Library/Application Support/MowTrackin… 
```

Enter fullscreen mode Exit fullscreen mode

那个`fileID`映射到非常难看的目录树，那是 iOS 备份文件夹的其余部分(你可能会看着它，想知道“见鬼？!").顶层是一个 2 位数的十六进制前缀，下面是文件(可能是出于性能原因，但有点模糊也不会有什么坏处)。我们将得到整个字符串:

```
filter(fils, relativePath == "Library/Application Support/MowTracking.sqlite") %>% 
  select(fileID)
## # Source: lazy query [?? x 1]
## # Database: sqlite 3.22.0 [/private/tmp/Manifest.db]
## fileID                                  
## <chr>                                   
## 1 ad0009ec04c44b544d37bfc7ab3438697d23d618 
```

Enter fullscreen mode Exit fullscreen mode

然后将割草数据库复制到某个安全的地方:

```
file.copy(
  file.path(mb, "ad/ad0009ec04c44b544d37bfc7ab3438697d23d618"),
  "/tmp/mowtrack.sqlite",
  overwrite = TRUE
)
## [1] TRUE

mow <- src_sqlite("/tmp/mowtrack.sqlite")

mow
## src: sqlite 3.22.0 [/private/tmp/mowtrack.sqlite]
## tbls: Z_METADATA, Z_MODELCACHE, Z_PRIMARYKEY, ZACTIVITY, ZDEALER,
## ZMOWALERT, ZMOWER, ZMOWLOCATION, ZSMARTCONNECTOR, ZUSER

tbl(mow, "ZMOWLOCATION") %>% 
  glimpse()
## Observations: ??
## Variables: 16
## Database: sqlite 3.22.0 [/private/tmp/mowtrack.sqlite]
## $ Z_PK <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 1…
## $ Z_ENT <int> 8, 8, 8, 8, 8, 8, 8, 8, 8, 8, 8, 8, 8, 8, 8,…
## $ Z_OPT <int> 1, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ ZISPAUSEDPOINT <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
## $ ZORDER <int> 1, 2, 0, 11, 20, 58, 38, 43, 30, 25, 21, 10,…
## $ ZSESSION <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ ZSESSION2 <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
## $ ZALTITUDE <dbl> 42.64804, 42.70590, 40.99661, 39.54770, 38.2…
## $ ZCOURSE <dbl> 358.242188, 332.226562, 18.281250, 260.85937…
## $ ZHORIZONTALACCURACY <dbl> 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5,…
## $ ZLATITUDE <dbl> 43.25913, 43.25914, 43.25913, 43.25915, 43.2…
## $ ZLONGITUDE <dbl> -70.80069, -70.80069, -70.80069, -70.80067, …
## $ ZSPEED <dbl> 0.0000000, 0.4250179, 0.5592341, 0.3802792, …
## $ ZTIMESTAMP <dbl> 581100271, 581100272, 581100270, 581100281, …
## $ ZVERTICALACCURACY <dbl> 6, 6, 8, 6, 4, 4, 4, 3, 4, 4, 4, 6, 4, 4, 4,…
## $ ZKLVDATA <blob> <NA>, <NA>, <NA>, <NA>, <NA>, <NA>, <NA>, <… 
```

Enter fullscreen mode Exit fullscreen mode

### (鲨鱼)鳍

即使你没有我目前痴迷的这个割草机应用程序，你现在也有了如何在你的 iOS 设备上访问任何应用程序存储的 SQLite 数据库的初级读本。光是这一点就可能为你挖掘出一些有趣的项目。另外，您还了解了如何使用 R/RStudio 对 iOS 备份进行一些简单的取证。

如果你做了自己的搜索，发现了一些有趣的东西，一定要写博客或发微博，并在评论中添加链接。