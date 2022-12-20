# MacPorts 中的自定义存储库

> 原文：<https://dev.to/zerok/custom-repositories-in-macports-5183>

*这篇文章最初发表在[我的博客](https://zerokspot.com/weblog/2019/05/04/custom-repositories-in-macports/)上。*

为了在朋友之间分发定制工具，也为了某种程度上在工作中使用，我在[自制软件](https://brew.sh)中创建了一个[定制 tap](https://docs.brew.sh/Taps) 。这非常简单，因为它只需要一个观众可以访问的 Git 存储库。 [MacPorts](https://www.macports.org) 有类似的吗？

虽然 MacPorts 主要关注主端口树，但是您可以为该树指定多个源。你可以通过编辑`/opt/local/etc/macports/sources.conf` :
来实现

```
rsync://rsync.macports.org/macports/release/tarballs/ports.tar [default] 
```

任何自定义信号源都应添加到“默认”条目之前，因为 MacPorts 将按照此处指定的顺序遍历每个信号源。为了演示这个特性，我为 [exa](https://the.exa.website/) 创建了一个定制的 Portfile，我们现在将通过一个定制的源来分发它。

## 签名存档

虽然 MacPorts 并不严格要求将端口树作为归档文件分发，但这是它强制使用签名的唯一方式。因为那条路只是稍微长一点，让我们在这里多走一步。

为了签署任何东西，我们首先需要一个密钥对(由一个私钥和一个公钥组成)。

```
# Generate the private key
$ openssl genrsa -des3 -out privkey.pem 2048

# Derive the public key
$ openssl rsa -in privkey.pem -pubout -out pubkey.pem 
```

然后，我将公钥文件(`pubkey.pem`)放在了`/Users/zerok/src/github.com/zerok/macports-ports/`中。接下来，MacPorts 必须知道公钥可以用来验证人工制品。这是通过将其添加到`/opt/local/etc/macports/pubkeys.conf` :
来完成的

```
# MacPorts system-wide public key configuration file.

/opt/local/share/macports/macports-pubkey.pem
/Users/zerok/src/github.com/zerok/macports-ports/pubkey.pem 
```

## 新的来源

既然 MacPorts 知道如何验证我们的新源，我们需要创建一个归档来分发。对于我的 exa Portfile，我创建了以下存储库:[https://github.com/zerok/macports-ports](https://github.com/zerok/macports-ports)

这包含以下文件:

```
$ exa -l --tree
drwxr-xr-x    - zerok  4 May 10:05 .
.rw-r--r--  562 zerok  4 May  9:59 ├── Makefile
.rw-r--r-- 1.8k zerok  4 May  9:32 ├── privkey.pem
.rw-r--r--  451 zerok  4 May  9:32 ├── pubkey.pem
.rw-r--r--  566 zerok  4 May 10:01 ├── pubkey.pem.sig
.rw-r--r--  316 zerok  4 May 10:01 ├── README.md
drwxr-xr-x    - zerok  4 May  9:26 └── sysutils
drwxr-xr-x    - zerok  4 May  9:26    └── exa
.rw-r--r-- 1.2k zerok  4 May  9:26       └── Portfile 
```

Makefile 主要用于自动创建 ports-tree 档案及其签名:

```
all: dist/ports.tar.bz2 dist/ports.tar.bz2.rmd160

dist:
    mkdir -p dist

dist/ports.tar.bz2: dist
    mkdir dist/ports && \
    cp -R sysutils dist/ports/ && \
    cd dist && tar -cjvf ports.tar.bz2 ports && \
    rm -rf ports

dist/ports.tar.bz2.rmd160:
    openssl dgst -ripemd160 -sign privkey.pem -out dist/ports.tar.bz2.rmd160 dist/ports.tar.bz2.rmd160

clean:
    rm -rf dist

release:
    rsync -avz dist/* zerokspot.com:/srv/www/h10n.me/www/htdocs/macports-ports/ && \
    rsync -avz pubkey.* zerokspot.com:/srv/www/h10n.me/www/htdocs/macports-ports/

.PHONY: all clean release 
```

运行`make clean all release`现在生成一个归档文件，对其进行签名，并将其包括公钥(及其签名)上传到`https://h10n.me/macports-ports`。

万岁，我们现在有一个港口档案，我们终于可以使用。最后，更新`/opt/local/etc/macports/`中的 sources.conf，如下所示:

```
https://h10n.me/macports-ports/ports.tar.bz2
rsync://rsync.macports.org/macports/release/tarballs/ports.tar [default] 
```

这告诉 MacPorts，它应该首先在 h10n.me 源中寻找一个端口，然后再返回到默认端口。

```
# Download the sources:
$ sudo port sync

# Show the details of the new exa port:
$ port info exa
exa @0.8.0 (sysutils)

Description:          exa is a replacement for ls with additional features like color-coding for file types, Git integration, and extended attributes.
Homepage:             https://github.com/ogham/exa

Extract Dependencies: unzip
Platforms:            darwin
License:              MIT
Maintainers:          Email: zerok@macports.org 
```

## 好的和不太好的

虽然 MacPorts 提供多个包源的方法比 Homebrew 的要复杂一点，但我真的很喜欢归档文件*必须签名*(除非你只是在某个地方暴露一个文件夹)。就个人而言，我更喜欢 GnuPG 签名而不是 openssl/rmd160，因为它们更现代一点*并且*包含更多关于签名所有者的元数据。至少对于源代码公钥的分发，我仍然选择用 GnuPG 签名，这就是为什么在我的示例库中也有一个`pubkey.pem.sig`。

我相对来说是 MacPorts 的新手，所以我不知道我在这里描述的内容是否遵循了最佳实践。你已经被警告了😅