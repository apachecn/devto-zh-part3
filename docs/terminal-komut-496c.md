# 终端/命令

> 原文：<https://dev.to/mrturkmen/terminal-komut-496c>

## 简介:

本文通过对 linux 环境的进一步介绍，计划简要介绍 linux 环境下的命令。

## 中文索引:

*   [简介:T1](#%c3%96zet)
*   [中文索引:T1](#%c4%b0%c3%a7indekiler)
*   [简介](#giri%c5%9f)
    *   [为什么是 Linux？](#neden-linux)
*   [基础知识](#temeller)
    *   [此通知文件的注释](#bu-bilgilendirme-dosyas%c4%b1-i%c3%a7in-not)
    *   [通过 Windows 计算机输入](#windows-bilgisayar-%c3%bczerinden-giri%c5%9f-i%c3%a7in)
    *   [从 Mac 或 Linux 计算机访问](#mac-veya-linux-bilgisayarlardan-eri%c5%9fim)
    *   [登录后如果您想更改密码](#giri%c5%9f-yapt%c4%b1ktan-sonra-%c5%9fifrenizi-de%c4%9fi%c5%9ftirmek-isterseniz)
    *   [Listeleme](#listeleme)
    *   [文件和文件夹](#dosyalar-ve-klas%c3%b6rler)
*   [快捷键](#k%c4%b1sayollar)
*   [获取帮助](#yard%c4%b1m-alma)
*   [查找您要查找的文件的方法](#arad%c4%b1%c4%9f%c4%b1n%c4%b1z-dosyay%c4%b1-bulma-y%c3%b6ntemleri)
    *   [打个电话](#arama-yapmak)
    *   [在文件中搜索](#dosya-i%c3%a7erisinde-arama-yapmak)
*   [句子&版权所有](#%c4%b0zinler--hak-sahipli%c4%9fi)
    *   [用户权限的获取](#kullan%c4%b1c%c4%b1-haklar%c4%b1n%c4%b1n-al%c4%b1nmas%c4%b1)
    *   [所有权的变更](#hak-sahipli%c4%9finin-de%c4%9fi%c5%9ftirilmesi)
*   [实用的 Linux 命令](#kullan%c4%b1%c5%9fl%c4%b1-linux-komutlar%c4%b1)
*   [交易管理](#%c4%b0%c5%9flem-y%c3%b6netimi)
*   [读取文本文件](#text-dosyalar%c4%b1n%c4%b1-okumak)
*   [文本编辑器](#metin-d%c3%bczenleyicileri)
    *   [我们和我知道](#vi-ve-vim)
    *   [EMACS](#emacs)
    *   XEMACS
    *   [饮酒](#pico)
*   [我知道基础知识](#vim-temelleri)
    *   [基础知识](#temeller-1)
    *   [帮助](#yard%c4%b1m)
    *   [浏览文件(在 vim 中)](#dosya-i%c3%a7erisinde-gezme-vim-i%c3%a7erisinde)
    *   [图像(在我知道的范围内)](#g%c3%b6r%c3%bcnt%c3%bc-vim-i%c3%a7erisinde)
*   [存档和压缩](#ar%c5%9fivleme-ve-s%c4%b1k%c4%b1%c5%9ft%c4%b1rma)
    *   [查看归档](#ar%c5%9fivleri-g%c3%b6r%c3%bcnt%c3%bcleme)
    *   【T0 去除】T1
*   [简单安装操作](#basit-y%c3%bckleme-i%c5%9flemleri)
    *   [RPM 装载](#rpm-y%c3%bcklemeleri)
    *   [Debian 软件包的安装](#debian-paketlerinin-y%c3%bcklenmesi)
*   [设备](#cihazlar)
    *   [插入/取出 USB/软盘/cdrom](#takma-%c3%87%c4%b1karma-usbfloppycdrom)
*   [环境变量](#%c3%87evresel-de%c4%9fi%c5%9fkenler) [知识基础](#vim-temelleri)
    *   [基础知识](#temeller)
    *   [帮助](#yard%C4%B1m)
    *   [浏览文件(在 vim 中)](#dosya-i%c3%a7erisinde-gezme-vim-i%c3%a7erisinde)
    *   [图像(在我知道的范围内)](#g%c3%b6r%c3%bcnt%c3%bc-vim-i%c3%a7erisinde)
*   [存档和压缩](#ar%C5%9Fivleme-ve-s%C4%B1k%C4%B1%C5%9Ft%C4%B1rma)
    *   [查看归档](#ar%C5%9Fivleri-g%C3%B6r%C3%BCnt%C3%BCleme)
    *   【T0 去除】T1
*   [简单安装操作](#basit-y%C3%BCkleme-i%C5%9Flemleri)
    *   [RPM 装载](#rpm-y%C3%BCklemeleri)
    *   [Debian 软件包的安装](#debian-paketlerinin-y%C3%BCklenmesi)
*   [设备](#cihazlar)
    *   [插入/取出 USB/软盘/cdrom](#takma-%c3%87%c4%b1karma-usbfloppycdrom)
*   [环境变量](#%c3%87evresel-de%c4%9fi%c5%9fkenler)

## [简介](#giri%C5%9F)

#### 为什么是 Linux？

*   可让您轻松同时执行多项作业
*   可轻松进行远端作业
*   多个用户可以访问同一台服务器
*   终端可以访问系统上的多个资源
*   有接口的系统 g

## [基础知识](#temeller)

#### 此信息文件的注释

**所有命令都区分大小写。**

*   `"$"`表示命令的开始。
*   `"#"`表示命令结束。

#### [通过 Windows 计算机登录](#windows-bilgisayar-%C3%BCzerinden-giri%C5%9F-i%C3%A7in)

*   **PuTTY** 是通过 windows 计算机与 SSH 连接所必需的。
*   **在 PuTTY** 程序中，要连接到 SSH，您必须知道服务器的 IP 地址和端口号。

#### [从 Mac 或 Linux 计算机访问](#mac-veya-linux-bilgisayarlardan-eri%C5%9Fim)

由于这些计算机是基于 UNIX 的，因此您只需在终端上输入下面的命令即可，而无需使用额外的程序。

```
$ ssh <kullanıcı_adı>@<sunucu_adresi(IP)>
$ kullanıcı_adı: ...
$ şifre: ... 
```

Enter fullscreen mode Exit fullscreen mode

#### 登录后要更改密码吗

```
$ passwd # bu komutu kullanabilirsiniz. 
    # bu komut sayesinde giriş yapılan 
    # kullanıcı için yeni şifre 
    # belirleyebilirsiniz. 
```

Enter fullscreen mode Exit fullscreen mode

#### Listeleme

```
$ pwd # şu anda bulunduğunuz konumu çıktı olarak yazdırır
$ ls # bulunduğunuz konumdaki dosyaları ve klasörleri listeler
$ ll # ls komutunun eşdeşi olarak tanımlı bir ifadedir genelde "ls -alF" olarak kayıtlıdır bash profilinde

 $ ll -R # dosyalar/klasörler listelenir, klasörler içerisindeki dosyalarda listelenir
 $ ll -t # listeleme işlemi kronolojik şekilde gerçekleşir.
 $ stat <dosya_adı> # dosyaya ait bilgileri meta bilgileri listeler
 $ whoami # sizin sistem tarafından kim olduğunuzu söyler, yani kullanıcı adınızı listeler
 $ hostname # bağlı olduğunu makinenin URL ni yada IP sini gösterir 
```

Enter fullscreen mode Exit fullscreen mode

#### [文件和文件夹](#dosyalar-ve-klas%C3%B6rler)

```
 $ mkdir <klasör_adı> # belirlenen isimde klasör oluşturur
 $ cd <klasör_adı> # klasör adı tanımlanan klasöre gidersini.
 $ cd .. # üst klasöre gitmenizi sağlar
 $ cd ../../ # iki üst klasöre gitmenizi sağlar
 $ cd # ana klasörüne gidersiniz
 $ rmdir <klasör_adı> # klasörü siler
 $ rm <dosya_adı> # dosyayı siler
 $ rm -r <klasör_adı> # klasörü ve içerisindeki bütün dosyaları siler
 $ mv <dosyaadı1> <dosyaadı2> # isim değiştirmenizi sağlar, name
 $ mv <dosyaadı> <taşınacak_yol> # dosyayı belirtilen yere taşır
 $ cp <dosyaadı> <kopyalanacak_yol> # dosyayı belirtilen yere kopyalar, eğer klasör kopyalanacak ise -r parametresi eklenir. 
```

Enter fullscreen mode Exit fullscreen mode

## [快捷键 T1](#k%C4%B1sayollar)

```
 $ . # sadece nokta bulunduğunuz dizini ifade eder
 $ ~/ # kullanıcının ana dizinini ifade eder
 $ history # yazmış olduğunuz komutların kaydını tutar ve bu komut ile erişebilirsiniz
 $ !<komut_sıralaması> # daha önce yazmış oldunuz komutu sıralamasının numarasını vererek calıştırabilirsiniz.
 $ yukarı(asagı)_okları # geçmiş komutlar arasında gezmenizi sağlar
 $ <tamamlanmamış_yol_veya_dosyaadı> TAB # Tab a bastığınızda sistem otomatik tamamlama işlemini gerçekleştirir.
 $ <tamamlanmamış komut> SHIFT&TAB # komutu otomatik tamamlar
 $ Ctrl a # imlecin en başa gitmesini sağlar
 $ Ctrl e # imlecin en sona gitmesini sağlar
 $ Ctrl d # imlec altındaki karakteri siler
 $ Ctrl k # imlecin bulunduğu sağlar
 $ Ctrl y # Ctrl k ile alınan içerik yapıştırılır. 
```

Enter fullscreen mode Exit fullscreen mode

## [获取帮助](#yard%C4%B1m-alma)

```
 $ man # genel yardım
 $ man wc # wc komutu hakkında yardım almanızı sağlar
 $ wc --help # wc komutu hakkında yardım almanızı sağlar
 $ info wc # wc komutu hakkında detaylı bilgi almanız sağlanır
 $ apropos wc # wc komutuna ait bütün yardım dosyaları güncellenir. 
```

Enter fullscreen mode Exit fullscreen mode

## 查找您要查找的文件的方法

#### 的搜索

```
 $ find -name "*aramakistediğinizdesen*" # girdiğiniz desene göre bulunduğunuz dizinde arama yapmanızı sağlar.
 $ find /usr/local -name "*klas*" # isminin içerisinde klas geçen dosyaları ve klaksörleri listeler.
 $ find /usr/local -iname "*klas*" # yukarıdaki komutuna benzer şekilde, isminin içerisinde klas geçen dosyaları ve klasörleri listeler fakat bu durumda büyük veya küçük harfte olması dikkate alınmaz

 $ find ~ -type f -mtime -2 # 2 gün içerisinde değiştirilmiş bütün dosyaları listeler
 $ locate <aramakistediğinizdesen> # aradığınız dosyayı veya klasörü sistem genelinde arar.
 $ which <uygulama_adı> # uygulamanın nerede bulunduğunu gösterir
 $ whereis <uygulama_adı> # uygulamanın çalıştırılabilir dosyasının yerini gösterir
 $ dpkg -l | grep aramakistediğinizpaketismi # Debian paketleri içerisinde arama yaparak verilen desende bulunan paketleri listel 
```

Enter fullscreen mode Exit fullscreen mode

#### [在文件中搜索](#dosya-i%C3%A7erisinde-arama-yapmak)

```
 $ grep aranan_kelime dosya # dosya içerisinde aranan kelimenin nerelerde geçtiğini size aktarır
 $ grep -H aranan_kelime # -H çıktı dosyasını aranan kelimenin önüne koyar
 $ grep 'aranan_kelime' dosya | wc # burada iki komut birleştirilmiştir, yani grep komutunun çıktısı wc komutuna girdi olmaktadir yani aranan kelime verilen dosyada 5 kere geçiyor ise bu durumda sonuc 5 olarak dönmektedir. 
 $ find /home/kullanıcı_adı -name '*.txt' | xargs grep -c ^.* # verilen dizinde txt dosyalarını bularak bu dosyalar içerisindeki satır sayısını hesaplayarak çıktı vermektedir. 
```

Enter fullscreen mode Exit fullscreen mode

## 句子&的版权

```
 $ ls -al # bu komut çalıştırıldıgında buna benzer bir çıktı görünebilir : drwxrwxrwx 
```

Enter fullscreen mode Exit fullscreen mode

这些字母的含义如下。

*   **d:** 索引
*   **rwx**读取、写入、执行
*   **前三个(rwx)** 用户权限(u)
*   **第二个三个**组权限(g)
*   **第三个**表示其他权限(o)。**如果最初是 d，则说明该文件实际上是一个文件夹。**

允许用户和组写入和运行:

```
 $ chmod ug+rx dosya_ismi 
```

Enter fullscreen mode Exit fullscreen mode

#### 用户权限的获取

```
 $ chmod ugo-rwx dosya_ismi

 '+' izin eklemeyi sağlar
 '-' izin silmenizi sağlar

 $ chmod +rx dosya_ismi/ VEYA $ chmod 755 dosya_ismi/ 
```

Enter fullscreen mode Exit fullscreen mode

#### [所有权的变更](#hak-sahipli%C4%9Finin-de%C4%9Fi%C5%9Ftirilmesi)

```
 $ chown <kullanıcı_adı> <dosya veya klasör> # kullanıcı hak sahibini değiştirir
 $ chgrp <grup> <dosya veya klasör> # grup hak sahibini değiştirir
 $ chown <kullanıcı_adı>:<grup> <dosya veya klasör> # kullanıcı ve grup hak sahipliğini değiştirir 
```

Enter fullscreen mode Exit fullscreen mode

## 方便的 Linux 命令

```
 $ df # sistem diskinin ne kadar dolu ve boş olduğu bilgisini gösterir
 $ free # ne kadar önbellek (RAM) alanının boş/dolu olduğunu gösterir
 $ uname -a # işletim sistemine ait temel bilgileri gösterir
 $ bc # terminal üzerinden hesap makinesi kullanmanızı sağlar
 $ /sbin/ifconfig # sunucunun ağ bilgilerini listeler.
 $ ln -s orjinal_dosyaismi yeni_dosyaismi # orjinal dosyaya link oluşturur
 $ du -sh # bulunduğunuz konumdaki disk kullanım bilgilerini listeler
 $ du -sh * # bulunduğunuz konumdaki dosyaların/klasörlerin kullanım bilgilerini gösterir
 $ du -s * | sort -nr # sıralanmış şekilde dosyaların/klasörlerin kullanımlarını listeler 
```

Enter fullscreen mode Exit fullscreen mode

## 操作管理

```
 $ who # sisteme kimin girdiğini gösterir
 $ w # sistemde kimlerin olduğunu gösterir
 $ ps # arka planda çalışan işlemler hakkındaki bilgileri listeler.
 $ ps -e # sistemdeki bütün işlemleri listeler
 $ ps aux | grep <kullanıcı_adı> # kullanıcıya ait çalıştırılan işlemleri listeler
 $ top # CPU ve RAM değerlerinin kullanım bilgilerini gösterir
 $ mtop # birden fazla CPU için top komutunun yaptığını yapar
 $ Ctrl z <enter> bg or fg <enter> # çalışan işlemleri durdurur, arka plana atar (bg) veya ön plana getirir (fg)
 $ Ctrl c # yeni başlamış olan işlemi durdurur
 $ kill <işlem_no> # belirlenen işlemi sonlandırır, işlem ID sine göre belirlenir
 $ renice -n <önemlilik_değeri> # işlemin önemlilik değerini değiştirmenizi sağlar 
```

Enter fullscreen mode Exit fullscreen mode

## 读取文本文件

```
 $ less <dosya_ismi> # belirtilen dosyayı terminal üzerinden okumanızı sağlar G :dosyanın sonuna gider, g : dosyanın başına gider. 
 $ more <dosya_ismi> # dosya içeriğini gösterir çıkmak için q ya basılması gereklidir.
 $ cat <dosya_ismi> #dosya içeriğini terminale yazdırır 
```

Enter fullscreen mode Exit fullscreen mode

## [t1 文本编辑器](#metin-d%C3%BCzenleyicileri)

#### VI 和我都知道

基于终端的强大文本编辑器。Vi 通常适用于所有基于 linux 的系统，我知道 vi 是高级版。

#### EMACS

以图像为基础的文字编辑器。您必须了解此编辑器的键盘布局。适用于所有 linux 和 unix 系统。

#### xemac

EMACS 更高级，可以更好地控制拼写错误、web 和文本，但通常没有安装。

#### 醉汉

它是一个基于终端的简单文本编辑器，必须知道键盘布局。

## [我知道基础知识](#vim-temelleri)

#### [基础知识](#temeller)

```
 $ vim dosya_ismi # dosya_ismin de dosya oluşturur veya yazma modunda açar
 $ i # vim 'in içerisine girdikten sonra i açık olan dosyaya bir şeyler yazmanıza olanak sağlar.
 $ ESC # dosya düzenleme modundan çıkılır
 $ : # vim içerisinde kullanacağınız komutlar : ile başlar
 $ :w # vim içerisinde :w yaptığınızda yazdığınızı kaydeder.
 $ :q # bu komut vim den çıkmanızı sağlar
 $ :q! # hiç birşeyi kaydetmeden çıkmanızı sağlar.
 $ :wq # kaydederek çıkar
 $ R # vim içerisinde özellik değiştirmenizi sağlar
 $ r # imlecin bulunduğu karakteri değiştirmenizi sağlar
 $ q: # vim içerisinde yazdığınız komutların kaydını gösterir
 $ :w yeni_dosyaadı # yeni dosyaya kaydeder.
 $ :#,#w yeni_dosyaadı# belirlenen (#,#) aralıktaki metini yeni dosyaya kaydeder.
 $ :# belirlenen (#) satıra gitmenizi sağlar 
```

Enter fullscreen mode Exit fullscreen mode

#### [帮助](#yard%C4%B1m)

```
 $ vimtutor # vim içerisinde nasıl çalıştığına dair bilgileri içeren tur başlatılır.
 $ :help # vim içerisinde yardım açar, çıkmak için q komutu kullanılır.
 $ :help <konu> # belirlenen konu hakkında yardım açar
 $ :help <konu> CTRL-D # belirlenen konunun geçtiği bütün yardım dökümanını listeler.
 $ :<yukarı-asagı tusları> # daha önceki yaptığınız komutlar arasında gezmenizi sağlar. 
```

Enter fullscreen mode Exit fullscreen mode

#### [浏览文件(在 vim 中)](#dosya-i%C3%A7erisinde-gezme-vim-i%C3%A7erisinde)

```
 $ $ # bulunduğunuz satırın en sonuna gider
  $ A # bulunduğunuz satırın en sonuna yazma modunu açarak gider
  $ 0 (sıfır) # satırın başlangıcına gider
  $ CTRL-g # imlecin nerede olduğu ve o satır hakkında bilgi verir
  $ SHIFT-G # imleci dosyanın en sonuna getirir 
```

Enter fullscreen mode Exit fullscreen mode

#### [图像(在 vim 中)](#g%C3%B6r%C3%BCnt%C3%BC-vim-i%C3%A7erisinde)

```
 WRAPPING AND LINE NUMBERS

  $ :set nowrap # kelimelerin kaymamalarını sağlar
  $ :set number # satır numaralarını gösterir 
```

Enter fullscreen mode Exit fullscreen mode

## [存档和压缩](#ar%C5%9Fivleme-ve-s%C4%B1k%C4%B1%C5%9Ft%C4%B1rma)

```
 $ tar -cvf dosya_ismi.tar klasör/ # verilen klasör için arşiv oluşturur
 $ tar -czvf dosya_ismi.tgz klasör/ # verilen klasör için arşivlenmiş ve sıkıştırılmış dosya oluşturur. 
```

Enter fullscreen mode Exit fullscreen mode

#### [查看存档](#ar%C5%9Fivleri-g%C3%B6r%C3%BCnt%C3%BCleme)

```
 $ tar -tvf dosya_ismi.tar
 $ tar -tzvf dosya_ismi.tgz 
```

Enter fullscreen mode Exit fullscreen mode

#### [提取 T1](#%C3%A7%C4%B1kartma)

```
 $ tar -xvf dosya_ismi.tar
 $ tar -xzvf dosya_ismi.tgz
 $ gunzip dosya_ismi.tar.gz 
 $ tar zxf blast.linux.tar.Zs 
```

Enter fullscreen mode Exit fullscreen mode

## 简单的安装操作

#### RPM 加载

```
 $ rpm -i uygulama_ismi.rpm
 $ rpm --query <paket_ismi> ## RPM versiyonunu kontrol etme için 
```

Enter fullscreen mode Exit fullscreen mode

#### Debian 软件包的安装

```
 $ apt-cache search nmap # nmap adındaki uygulamayı debian deposundan arama yapar
 $ apt-cache show nmap # nmap hakkında tanımlamayı(bilgi) gösterir
 $ apt-get install nmap # nmap 'i sisteme kurar.
 $ apt-get update # sistemdeki uygulamaları ve servisleri günceller
 $ apt-get upgrade -u # uygulamaların yeni versiyonu var ise yükseltme yapar
 $ dpkg -i dosya.deb # indirilen debian dosyasının yüklenmesini sağlar
 $ aptitude # apt-get ile aynı işlemi görür
 $ aptitude search vim # vim programını debian deposunda arar 
```

Enter fullscreen mode Exit fullscreen mode

## 设备

#### [插入/取出 USB/软盘/cdrom](#takma-%C3%A7%C4%B1karma-usbfloppycdrom)

```
 $ mount /media/usb
 $ umount /media/usb
 $ mount /media/cdrom
 $ eject /media/cdrom
 $ mount /media/floppy 
```

Enter fullscreen mode Exit fullscreen mode

## [t1 环境变量](#%C3%A7evresel-de%C4%9Fi%C5%9Fkenler)

```
 $ xhost user@host # kullanıcı için çalıştırma izini ekler
 $ echo DISPLAY # ekranın ayarlarını gösterir
 $ export (setenv) DISPLAY=<lokal_IP>:0 # görüntü değişkeninin değerini değiştirir
 $ unsetenv DISPLAY # görüntü değişkenini siler
 $ printenv # kullanılan çevresel değişkenleri listeler
 $ $PATH # terminal üzerinde programların çalışmasını sağlayan yolları gösterir. 
```

Enter fullscreen mode Exit fullscreen mode