# 维姆·🇹🇷

> 原文：<https://dev.to/mrturkmen/vim-109c>

*   [简介](#%c3%96zet)
*   [脚本的编写方法。](#script-nas%c4%b1l-yaz%c4%b1l%c4%b1r)
*   [VIM](#vim)
    *   [处理多个文件](#birden-fazla-dosya-%c3%bczerinde-%c3%a7al%c4%b1%c5%9fmak)
    *   [拼写检查&词典](#hece-kontrol%c3%bc--s%c3%b6zl%c3%bck)
    *   [打印文件](#dosyay%c4%b1-yazd%c4%b1rma)
    *   [合并/插入命令](#birle%c5%9ftime--ekleme-komutu)
    *   【T0 还原/还原】T1
    *   [复制&粘贴](#kopyalama--yap%c4%b1%c5%9ft%c4%b1rma)
    *   [删除/剪切(在正常模式下应用。我是说我不在命令行上。不是在 EXE 模式。](#silmekesme-normal-modda-uygulan%c4%b1r-yani-vim-komut-sat%c4%b1r%c4%b1nda-de%c4%9fil-exe-modunda-de%c4%9fil)
    *   [文件内部搜索(我知道)(这部分通常使用正则表达式)](#dosya-i%c3%a7erisinde-arama-vim-bu-k%c4%b1s%c4%b1mda-genelde-d%c3%bczenli-ifadeler-kullan%c4%b1l%c4%b1r)
    *   [用正则表达式进行文本管理](#d%c3%bczenli-ifadeler-ile-metin-y%c3%b6netimi)
    *   [HTML 编辑](#html-d%c3%bczenleme)
    *   [在我知道的范围内运行终端命令](#vim-i%c3%a7erisinden-terminal-komutu-%c3%a7al%c4%b1%c5%9ft%c4%b1rma)
    *   [使用 Vim 作为表编辑器](#tablo-d%c3%bczenleyicisi-olarak-vim-i-kullanmak)
    *   [更改我的设置](#vim-ayarlar%c4%b1n%c4%b1-de%c4%9fi%c5%9ftirmek)
*   [方便终端指令](#kullan%c4%b1%c5%9fl%c4%b1-terminal-komutlar%c4%b1)
    *   [方便的单行命令](#kullan%c4%b1%c5%9fl%c4%b1-tek-sat%c4%b1r-komutlar)
    *   [简单 Perl 命令](#basit-perl-komutlar%c4%b1)
    *   [WGET(执行终端链接的文件下载。](#wget-terminal-%c3%bczerinden-linki-verilen-dosya-indirimini-ger%c3%a7ekle%c5%9ftirir)
    *   [SCP(允许在机器之间进行安全复制。](#scp-%c4%b0ki-makine-aras%c4%b1nda-g%c3%bcvenli-kopyalama-i%c5%9flemi-sa%c4%9flar)
    *   [NFTP:(使您可以通过终端轻松完成文件传输操作)](#nftp--dosya-transfer-i%c5%9flemlerinizi-kolay-%c5%9fekilde-terminal-%c3%bczerinden-yapman%c4%b1z%c4%b1-sa%c4%9flar)

## 简介

关于 VIM 的命令和一些有用的 linux 命令，由终端使用的最常用的文字编辑程序

## 脚本的编写方法。

*   创建一个文件并在该文件的开头添加终端路径`#!/bin/bash`
*   在此路径下输入终端命令。

## -以后设置文件的权限。chmod +x 文件名

终端脚本

*   示例 bash 脚本##我知道

#### [处理多个文件](#birden-fazla-dosya-%C3%BCzerinde-%C3%A7al%C4%B1%C5%9Fmak)

```
$ vim *.txt # birden fazla txt ile dosyasını aynı anda açmanızı sağlar (eğer birden fazla dosya mevcut ise)
$ :wall veya :qall # bütün açık dosyalardan yaz veya çık komutudur.
$ vim -o *.txt # birden fazle txt dosyasını açar ve yatay düzlemde gösterir, dikey düzlem için -O parametresi kullanılara
$ :args *.txt # txt ile biten bütün dosyaları argument listesine aktarır.
$ :all # bütün dosyaları yatay düzlemde ayırır
$ CTRL-w # birden fazla pencere arasında gezmenizi sağlar
$ :split # aynı dosyayı iki farklı pencerede gösterir.
$ :split <acılacak_dosya> # dosyayı yeni bir pencerede açar
$ :vsplit # brden fazla pencereyi dikey komunda ayırır, tablolar için çok kullanışlıdır. ":set scrollbind " komutu ile açık dosyalar da aynı anda yukarı aşağı yapabilirsiniz. 
$ :close # bulunduğunuz pencereyi kapatır
$ :only # bulunduğunuz pencere hariç diğerlerinin tamamını kapatır. 
```

Enter fullscreen mode Exit fullscreen mode

#### 拼写检查&词典

```
$ aspell -c <dosya> # verilen dosyada heceleri kontrol eder, terminal komutudur
$ aspell -l <dosya> # terminal komutu
$ :! dict <cumle> # cümlenin anlamını kontrol etmenizi sağlar
$ :! wn 'cumle' -over # cümlenin eş anlamlılarını gösterir 
```

Enter fullscreen mode Exit fullscreen mode

#### [打印文件](#dosyay%C4%B1-yazd%C4%B1rma)

```
 $ :ha # bütün dosyayı yazdırır
 $ :#,#ha # (#,#) ile belirtilen alandaki metini yazdırır 
```

Enter fullscreen mode Exit fullscreen mode

#### [合并/插入命令](#birle%C5%9Ftime-ekleme-komutu)

```
 $ :r <dosya_ismi> # açık olan dosya içerisine,
                   # aynı dizinde olan başka bir dosyayı eklemek için bu komut kullanılabilir,
                   # imlecin hizasından sonra ekleme yapar 
```

Enter fullscreen mode Exit fullscreen mode

#### [还原/重做](#geri-alma-yeniden-alma)

```
 $ u # en son yaptığınız değişikliği geri alır
 $ U # yaptığınız bütün değişiklikleri geri alır
 $ CTRL-R # geri alınmış bir kısmı yeniden getirmenizi sağlar. 
```

Enter fullscreen mode Exit fullscreen mode

#### [复制&粘贴](#kopyalama-amp-yap%C4%B1%C5%9Ft%C4%B1rma)

```
 $ yy # imlecin bulunduğu satırı kopyalar, 2 satır kopyalamak için 2yy kullanılabilir.
 $ p # kesilen/kopyalanan içeriği imleçten başlayacak şekilde yapıştırır 
```

Enter fullscreen mode Exit fullscreen mode

#### [删除/剪切(在正常模式下应用。我是说我不在命令行上。不是在 EXE 模式。(请注意)](#silmekesme-normal-modda-uygulan%C4%B1r-yani-vim-komut-sat%C4%B1r%C4%B1nda-de%C4%9Fil-exe-modunda-de%C4%9Fil-)

```
 $ x # imlecin üzerinde bulunduğu karakteri siler.
 $ dw # imlecin bulunduğu kelimeyi sonuna kadar siler (Boşluklar dahil )
 $ de # imlecin bulunduğu kelimeyi sonuna kadar siler (Boşluklar hariç )
 $ cw # kelimenin geriye kalan kısmını siler ve sizi ekleme moduna alır, ekleme modundan ESC ile çıkabilirsiniz.
 $ c$ # bulunduğu satırı tamamen siler ve sizi ekleme moduna alır ekleme modundan ESC ile çıkabilirsiniz. 
 $ d$ # imlecten itibaren satırı siler e
 $ dd # satırı tamamen siler, imlecin nerede olduğunun önemi yoktur
 $ 2dd # ileriki 2 satırı siler, benzer sekilde 3dd : uc satır siler, 4dd: dort satır siler, (imlecten bagımsız)
    Koyma  

 $ p # kesilen/kopyalanan içeriği imleçten başlayacak şekilde yapıştırır 
```

Enter fullscreen mode Exit fullscreen mode

#### 文件搜索(Vim)(通常在本节中使用正则表达式)

```
 $ /aramak_istediğiniz_düzen # yazdığınız ifadeyi açık olan belge içerisinde arar ve hepsini işaretler
 $ ?aramak_istediğiniz_düzen # yazdığınız ifadeyi açık olan belge içerisinde arar ama işaretlemez, n ile ileriki kelimeyi görebilirsiniz. 
 $ :set ic # kelimelerin büyük/küçük harf ayrımını ortadan kaldırır
 $ :set hls # aranan ve bulunan kelimeleri vurgulu şekilde gösterir. 
```

Enter fullscreen mode Exit fullscreen mode

#### 正则表达式的文本管理

```
 $ :s/harf1/harf2/ # harf1, harf2 ile değiştirilir fakat sadece ilk karşılaşmada yapılır
 $ :s/harf1/harf2/g # bütün dosya içerisindeki harf1, harf2 ile değiştirilir.
 $ :s/harf1/harf2/gc # yukarıdaki işlemin aynısını onay alarak yapmak için "c" eklenir
 $ :#,#s/harf1/harf2/g # (#,#) arasındaki satırlarda bulunan harf1, harf2 ile değiştirilir.
 $ :%s/harf1/harf2/g # tüm dosyadaki harf1 ifadesi harf2 ile değiştirilir.
 $ :%s/\(harf1\)\(.*\)/\1/g # harf1 sonrakisindeki bütün satırları siler.
 $ :%s/\(SL\dm\d\d\d\d\d\.\d\)\(.*\)/\1\t\2/g # SL1m12345.1 ve tanımı arasına TAB boşluğu ekler 
 $ :%s/\n/ifade/g #Satır verilen ifade ile değiştirilir.
 $ :%s/\(^SL\dm\d\d\d\d\d.\d\t.\{-}\t.\{-}\t.\{-}\t.\{-}\t\).\{-}\t/\1/g # 5 ve 6.ncı TAB taki (5\. Kolondaki), içeriği "{-}" ile degiştirir. 
 $ :#,#s/\( \{-} \|\.\|\n\)/\1/g # (#,#) verilen aralıkta ne kadar cümle olduğunu hesaplar
 $ :%s/\(E\{6,\}\)/<font color="green">\1<\/font>/g # 6 dan fazla E geçen kısımları, HTML renkleri ile vurgular.
 $ :%s/\([A-Z]\)/\l\1/g # Büyük harfleri, küçük harfler ile değiştirir, '%s/\([A-Z]\)/\u\1/g' , bu ise küçük harfleri büyük harfler ile değiştirir.
 $ :g/ifade/ s/\([A-Z]\)/\l\1/g | copy $ # ifade yeni oluşturulan ifade ile değiştirilir eşdeğer olanlar copy $ ile yazdırılır. 
```

Enter fullscreen mode Exit fullscreen mode

#### [html 编辑](#html-d%C3%BCzenleme)

```
 -metini HTML formatına cevirme
 $ :runtime! syntax/2html.vim # vim içerisinde bu komutu çalıştırınız. 
```

Enter fullscreen mode Exit fullscreen mode

#### 内部运行终端命令

```
 $ :!<terminal_komutu> <ENTER> # terminal komutunu vim içerisinden çalıştırır
 $ :sh terminal ile vim arasında gezmenizi sağlar 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用 Vim 作为表格编辑器

```
 $ v # karakterleri seçmek için görsel mod başlatılır.
 $ V # satırları seçmek için görsel mod başlatılır.
 $ CTRL-V # blok görsel seçim yapmanızı sağlar.
 $ :set scrollbind # aynı ayna ayrılan iki ayrı dosyada gezinti yapmanızı sağlar. 
```

Enter fullscreen mode Exit fullscreen mode

#### [更改“我知道”设置](#vim-ayarlar%C4%B1n%C4%B1-de%C4%9Fi%C5%9Ftirmek)

**-vim 文件中的参数可以根据需要进行修改。**

## 方便的终端命令

```
 $ cat <dosya1> <dosya2> > <sonuc> # dosya1 ve dosya2 yi sonuc dosyasina kopyalar ve sonuc dosyasini olusturur.
 $ paste <dosya1> <dosya2> > <p_sonuc> # iki farklı kaynaktan gelen girdiyi, aralarında TAB boşluğu olacak şekilde aynı dosya (p_sonuc) içerisine yapıştırır.
 $ cmp <dosya1> <dosya2> # iki dosyanın aynı olup olmadıgını size bildirir.
 $ diff <dosya1> <dosya2> # iki dosya arasındaki farklılıkları gösterir
 $ head -<numara> <dosya> # verdiğiniz numara kadar ilk X satırı yazdırır.
 $ tail -<numara> <dosya> # verdiğiniz numara kadar son X satırı yazdırır. 
 $ split -l <numara> <dosya> # dosyanın satırılarını ayırır.
 $ csplit -f out dosya_ismi "%^>%" "/^>/" "{*}" # dosya_ismini > den itibaren birçok farklı küçük dosyalar oluşturur.
 $ sort <dosya_ismi> # dosya içerisindekileri sıralar -b argument kullanılırsa boşlukları yok sayar.
 $ sort -k 2,2 -k 3,3n girdi_dosyası > cıktı # -k argument i kolon için, -n sayısal olarak sıralar ve tablo şeklinde kaydeder. 
 $ sort girdi_dosyası | uniq > cıktı # uniq komutu aynı olan verileri dahil etmez.
 $ join -1 1 -2 1 <tablo1> <tablo2> # tablo1 ve tablo2 yi birleştirir, -1 dosya1, 1:kolon1; -2dosya2, col2\. 
 $ sort tablo1 > tablo1a; sort tablo2 > tablo2a; join -a 1 -t "`echo -e '\t'`" tablo1a tablo2a > tablo3 # '-a <tablo>' : verilen tablonun bütün kayıtlarını yazdırır. Normalde yazdırma işlemi iki tabloda ortak olan kısımları yazdırır. '-t "`echo -e '\t'`" ->' 
 : TAB boşluğu kullanarak tabloları çıktı dosyasına yazdırır. 
 $ cat tablom | cut -d , -f1-3 # cut komutu : tablonun belirlenen kısımları alır, -d alanların nasıl ayrılacağını belirtilsiniz. -d : burada , olarak belirlenmiştir, normalde TAB boşluk, -f tablonun kolonlarını belirtir, kolon 1 den 3 e. 
```

Enter fullscreen mode Exit fullscreen mode

#### 方便的单行命令

```
 $ for i in *.input; do mv $i ${i/isim\.eski/isim\.yeni}; done # isim.eski adındaki dosyanın ismini, isim.yeni olarak değiştirir. Komutu test etmek için, do mv komutu önüne "echo" konulabilir.  
 $ for i in *.girdi; do ./uygulama $i; done # bir çok dosya için verilen uygulamayı çalıştırır. 
 $ for i in *.girdi; do komut -d /veri/../veri_tabanı -i $i > $i.out; done # komut for döngüsü içerisinde *.girdi üzerinde çalışır ve *.out dosyası oluşturur. 
 $ for i girdi *.pep; do hedef -db /usr/../veri_tabanı -seed $i -out $i; done # hedef in üzerinde for döngüsü çalıştırılır ve çıktı dosyası yazdırılır.
 $ for j girdi 0 1 2 3 4 5 6 7 8 9; do grep -iH <ifade> *$j.seq; done # 
 verilen ifadeyi girdi > 10.000 dosyaya kadar arar ve ne kadar o ifade geçtiğini yazdırır.
 $ for i in *.pep; do echo -e "$i\n\n17\n33\n\n\n" | ./program $i > $i.out; done # etkileşimli programı çalıştırır ve girdi/çıktı sorar. 
```

Enter fullscreen mode Exit fullscreen mode

#### [t1 简单 Perl 命令](#basit-perl-komutlar%C4%B1)

```
 $ perl -p -i -w -e 's/ifade1/ifade2/g' girdi_dosyası # girdi dosyası içerisindekileri verilen ifadelere göre değişimini yapar. '-p' bu komut yedek bir dosya oluşturur 
 $ perl -ne 'print if (/ifade1/ ? ($c=1) : (--$c > 0)) ; print if (/ifade2/ ? ($d = 1) : (--$d > 0))' girdi_dosyası > cıktı_dosyası # ifade1 ve ifade2 içeren satırları ayrıştırır (parse eder.) 
```

Enter fullscreen mode Exit fullscreen mode

#### WGET(执行终端链接文件的折扣。(请注意)

```
 $ wget ftp://ftp.itu.edu.tr.... # verilen linkteki dosya wget komutunun çalıştırıldığı dizine iner. 
```

Enter fullscreen mode Exit fullscreen mode

#### SCP(提供机器之间的安全复制操作。(请注意)

```
 Genel Kullanım.
 $ scp kopyalanacak_dosya kopyalanacak_yer #
 Örnekler 
 Sunucudan dosya kopyalamak için (bilgisayarınızın terminalinden)
 $ scp kullanıcı@sunucu_ip:dosya_adı . # '.' en sona nokta koyulması, sunucu üzerindeki kopyalanacak dosyayı bulunduğunuz yere kopyalamasını sağlar. 
 Bilgisayarınızdan sunucuya kopyalama yapmak için. (Bilgisayar terminalinden)
 $ scp resim.jpg kullanıcı@sunucu_ip:~/belgeler/resimler/
 Sunucu üzerinde bulunan klasörü bilgisayarımıza kopyalamak için. (Bilgisayar terminalinden)
 $ scp -r kullanıcı@sunucu_ip:dizin/ ~/Masaustu
 Bilgisayar üzerinde bulunan klasörü sunucuya kopyalamak için. (Bilgisayar terminalinden)
 $ scp -r klasör/ kullanıcı@sunucu_ip:dizin/ 
```

Enter fullscreen mode Exit fullscreen mode

#### NFTP:(允许您通过终端轻松完成文件传输操作)

```
 $ open ncftp
 $ ncftp> open sunucu_url # sunucuya baglantı sağlanıyor..
 $ ncftp> cd /root/Masaustu. # masaustune gecildi
 $ ncftp> get resimler.gz # masaustunde bulunan resimler.gz indirildi.
 $ ncftp> bye # gule gule mesajı alındı 
```

Enter fullscreen mode Exit fullscreen mode