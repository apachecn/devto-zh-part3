# macos 上的 Nokogiri 安装错误

> 原文：<https://dev.to/asaaki/nokogiri-installation-errors-on-macos-3h8b>

快速回答:

```
bundle config build.nokogiri --use-system-libraries && bundle install 
```

* * *

*这篇文章最初发布在[我的博客](https://markentier.tech/posts/2018/12/ruby-bundler-nokogiri-macos/)上。*

每隔一年左右，我希望(或需要)在我的 Macbook 上安装 Ruby 应用程序的依赖项，直接安装在主机上，而不是 VM 或容器上。主要是一个 Rails 应用。

而每次我们最“爱的”依赖寄托在我身上: [`nokogiri`](https://www.nokogiri.org/) 。我认为它在 Mac 上总是失败。(至少一次。)

因为我从来不会直接去找那些无法工作的文档，所以我通常会通过谷歌来寻找解决方案。对我来说，这比原本应该的要难，所以我把它写在这里作为提醒。

下次谷歌一下，希望能找到自己的博文，并会在最后做出同样的表情。又来了。

## 它是怎么失败的

尝试获取并构建依赖项:

```
# maybe a fancy Rails application
bundle install 
```

过了一会儿…

```
# snippet
An error occurred while installing nokogiri (1.8.5), and Bundler cannot continue.
Make sure that `gem install nokogiri -v '1.8.5' --source 'https://rubygems.org/'` succeeds before bundling.

In Gemfile:
  rails was resolved to 5.2.1.1, which depends on
    actioncable was resolved to 5.2.1.1, which depends on
      actionpack was resolved to 5.2.1.1, which depends on
        actionview was resolved to 5.2.1.1, which depends on
          rails-dom-testing was resolved to 2.0.3, which depends on
            nokogiri
# /snippet 
```

现在，如果你运行建议的…

```
gem install nokogiri -v '1.8.5' 
```

```
Building native extensions. This could take a while...
ERROR:  Error installing nokogiri:
  ERROR: Failed to build gem native extension.

# ... snip ...

Undefined symbols for architecture x86_64:
  "_iconv", referenced from:
      _main in conftest-451598.o
  "_iconv_open", referenced from:
      _main in conftest-451598.o
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
checked program was:
/* begin */
 1: #include "ruby.h"
 2:
 3: #include <stdlib.h>
 4: #include <iconv.h>
 5:
 6: int main(void)
 7: {
 8:     iconv_t cd = iconv_open("", "");
 9:     iconv(cd, NULL, NULL, NULL, NULL);
10:     return EXIT_SUCCESS;
11: }
/* end */ 
```

您也可以检查日志以供以后参考。

```
/Users/chris/.rbenv/versions/2.5.3/lib/ruby/gems/2.5.0/extensions/x86_64-darwin-17/2.5.0-static/nokogiri-1.8.5/gem_make.out
/Users/chris/.rbenv/versions/2.5.3/lib/ruby/gems/2.5.0/extensions/x86_64-darwin-17/2.5.0-static/nokogiri-1.8.5/mkmf.log 
```

这是一个无法与当前用于链接的 **iconv** 库一起工作的问题。

另外，另一个库也会引起一些麻烦:`libxml2`

那么输出可能看起来像…

```
Running 'compile' for libxml2 2.9.8... ERROR, review '/Users/chris/.rbenv/versions/2.5.3/lib/ruby/gems/2.5.0/gems/nokogiri-1.8.5/ext/nokogiri/tmp/x86_64-apple-darwin17.7.0/ports/libxml2/2.9.8/compile.log' to see what happened. Last lines are:
========================================================================
      _parseAndPrintFile in xmllint.o
  "_xmlXPathEval", referenced from:
      _doXPathQuery in xmllint.o
  "_xmlXPathFreeContext", referenced from:
      _doXPathQuery in xmllint.o
  "_xmlXPathFreeObject", referenced from:
      _doXPathQuery in xmllint.o
  "_xmlXPathIsInf", referenced from:
      _doXPathDump in xmllint.o
  "_xmlXPathIsNaN", referenced from:
      _doXPathDump in xmllint.o
  "_xmlXPathNewContext", referenced from:
      _doXPathQuery in xmllint.o
  "_xmlXPathOrderDocElems", referenced from:
      _parseAndPrintFile in xmllint.o
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation) 
```

## 如何修复

如果你注意输出，有时输出已经告诉你可以做什么。

*对于 libxml 案例:*

```
IMPORTANT NOTICE:

Building Nokogiri with a packaged version of libxml2-2.9.8
with the following patches applied:
    - 0001-Revert-Do-not-URI-escape-in-server-side-includes.patch
    - 0002-Fix-nullptr-deref-with-XPath-logic-ops.patch
    - 0003-Fix-infinite-loop-in-LZMA-decompression.patch

Team Nokogiri will keep on doing their best to provide security
updates in a timely manner, but if this is a concern for you and want
to use the system library instead; abort this installation process and
reinstall nokogiri as follows:

    gem install nokogiri -- --use-system-libraries
        [--with-xml2-config=/path/to/xml2-config]
        [--with-xslt-config=/path/to/xslt-config]

If you are using Bundler, tell it to use the option:

    bundle config build.nokogiri --use-system-libraries
    bundle install

Note, however, that nokogiri is not fully compatible with arbitrary
versions of libxml2 provided by OS/package vendors. 
```

因此，最后的命令是你应该为基于 bundler 的项目考虑的事情。

```
bundle config build.nokogiri --use-system-libraries
bundle install 
```

您可以查看配置:

```
bundle config 
```

```
Settings are listed in order of priority. The top value will be used.
gem.test
Set for the current user (/Users/chris/.bundle/config): "rspec"

gem.mit
Set for the current user (/Users/chris/.bundle/config): true

gem.coc
Set for the current user (/Users/chris/.bundle/config): true

build.nokogiri
Set for the current user (/Users/chris/.bundle/config): "--use-system-libraries" 
```

用于完成的文件`~/.bundle/config`(显示为 diff):

```
 ---
  BUNDLE_GEM__TEST: "rspec"
  BUNDLE_GEM__MIT: "true"
  BUNDLE_GEM__COC: "true"
+ BUNDLE_BUILD__NOKOGIRI: "--use-system-libraries" 
```

仅此而已。固定！

## Nokogiri 文档

如果我曾经查阅过 nokogiri 的文档，我也会更早得到提示:

> Nokogiri 将拒绝针对您的操作系统提供的某些版本的 libxml2、libxslt 进行构建，某些版本将导致神秘的问题。如果您试图这样做，编译器脚本会警告您。

它侧重于 libxml2，但是这些步骤也有助于解决 **libiconv** 问题。

🤦