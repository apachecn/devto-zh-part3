# 快速命中:用一点 Rcpp 加速一个缓慢/平凡的任务

> 原文：<https://dev.to/hrbrmstr/quick-hit-speeding-up-a-slowmundane-task-with-a-little-rcpp-1a4g>

在$DAYJOB 的博客上，我排了一个帖子，展示如何使用我们新的 [`ropendata`](https://cran.r-project.org/web/packages/ropendata/) 包与我们的[开放数据](https://opendata.rapid7.com)门户的 API 一起工作。我不太确定它什么时候会被发布，所以如果你有兴趣看的话，请保持 RSS 阅读器固定在[https://blog.rapid7.com/](https://blog.rapid7.com/)上(我可能会在这里做一个小笔记，这样它就可以进入 R 周刊& R-bloggers)。

帖子中使用的示例数据是我在[最近的一篇帖子](https://dev.to/hrbrmstr/send-udp-probes-with-payloads-and-receiveprocess-responses-in-r-4keb)中谈到的公开版本，即发现的设备暴露了无处不在的发现协议。

我在工作中很幸运，因为我们已经对几乎所有令人讨厌的有效载荷数据进行了预处理，并且在 Athena 的拼花地图列中，所以一旦我们完全完成了一项新的研究，我真的不必进行太多的数据争论。

Ubiquiti 发现协议扫描结果的*公共数据*的格式与前一篇文章中的 base64 编码数据略有不同，因为有效载荷响应是十六进制编码的字符串；例如

```
0100009302000a002722bccf9db126fa9a02000a002722bdcf9dc0a80101010006002722bccf9d0a000400006ae40b000c626a732e6572656e696c646f0c00064147352d48500d00104d6f72726f5f446f757261646f5f30330e000102030022584d2e6172373234302e76352e362e332e32383539312e3135313133302e31373439100002e24514000d41697247726964204d35204850 
```

所以，每两个字符就是一个字节(例如`"01"`就是`0x01`)。

r 有一个很好的`strtoi()`函数，可以将十六进制编码的字节转换成原始值，但是它只对一个字节有效。我们可以用很多方法将一个字符串(如上所示)分割成一个长度为 2 的十六进制字符串的字符向量，其中一种方法是使用`stringi`包中的辅助函数:

```
library(stringi)
library(magrittr) # for %>%

x <- "0100009302000a002722bccf9db126fa9a02000a002722bdcf9dc0a80101010006002722bccf9d0a000400006ae40b000c626a732e6572656e696c646f0c00064147352d48500d00104d6f72726f5f446f757261646f5f30330e000102030022584d2e6172373234302e76352e362e332e32383539312e3135313133302e31373439100002e24514000d41697247726964204d35204850"

stri_sub(x, seq(1, stri_length(x), by = 2), length = 2)
## [1] "01" "00" "00" "93" "02" "00" "0a" "00" "27" "22" "bc" "cf" "9d" "b1" "26" "fa" "9a"
## [18] "02" "00" "0a" "00" "27" "22" "bd" "cf" "9d" "c0" "a8" "01" "01" "01" "00" "06" "00"
## [35] "27" "22" "bc" "cf" "9d" "0a" "00" "04" "00" "00" "6a" "e4" "0b" "00" "0c" "62" "6a"
## [52] "73" "2e" "65" "72" "65" "6e" "69" "6c" "64" "6f" "0c" "00" "06" "41" "47" "35" "2d"
## [69] "48" "50" "0d" "00" "10" "4d" "6f" "72" "72" "6f" "5f" "44" "6f" "75" "72" "61" "64"
## [86] "6f" "5f" "30" "33" "0e" "00" "01" "02" "03" "00" "22" "58" "4d" "2e" "61" "72" "37"
## [103] "32" "34" "30" "2e" "76" "35" "2e" "36" "2e" "33" "2e" "32" "38" "35" "39" "31" "2e"
## [120] "31" "35" "31" "31" "33" "30" "2e" "31" "37" "34" "39" "10" "00" "02" "e2" "45" "14"
## [137] "00" "0d" "41" "69" "72" "47" "72" "69" "64" "20" "4d" "35" "20" "48" "50" 
```

我们仍然需要通过`strtoi()`运行它，并将其转换成一个原始向量(至少对于这个用例是这样的):

```
stri_sub(x, seq(1, stri_length(x), by = 2), length = 2) %>%
  strtoi(base = 16) %>%
  as.raw()
## [1] 01 00 00 93 02 00 0a 00 27 22 bc cf 9d b1 26 fa 9a 02 00 0a 00 27 22 bd cf 9d c0 a8 01
## [30] 01 01 00 06 00 27 22 bc cf 9d 0a 00 04 00 00 6a e4 0b 00 0c 62 6a 73 2e 65 72 65 6e 69
## [59] 6c 64 6f 0c 00 06 41 47 35 2d 48 50 0d 00 10 4d 6f 72 72 6f 5f 44 6f 75 72 61 64 6f 5f
## [88] 30 33 0e 00 01 02 03 00 22 58 4d 2e 61 72 37 32 34 30 2e 76 35 2e 36 2e 33 2e 32 38 35
## [117] 39 31 2e 31 35 31 31 33 30 2e 31 37 34 39 10 00 02 e2 45 14 00 0d 41 69 72 47 72 69 64
## [146] 20 4d 35 20 48 50 
```

在我的一个系统上，单独使用完整的处理管道和样本字符串大约需要 170μs，这还不错。但是，如果我们有 50 万个这样的博客呢(就像工作博客帖子的情况一样)？我的意思是，*当然*，这只是大约一分半钟的处理时间(由于每一位输入的长度不同，所以会有一些差异)，但这是一个痛苦的交互式 1.5 分钟，我们仍然需要将这一段代码包装在一个带有一些矢量化的函数中，以便可以轻松使用。

这是一个很好的例子，说明使用少量 C++/Rcpp 引入的复杂性可能是合理的，特别是因为`BH`包——它为我们带来了来自 Boost C++库的大量功能——有一些方便的字符串实用程序，包括一个`boost::algorithm::unhex()`函数。

这里有一种在普通的 ol' R 会话中解决 C++/Rcpp 问题的方法:

```
library(Rcpp)

cppFunction(depends = "BH", '
  List dehexify_cpp(StringVector input) {

    List out(input.size()); // make room for our return value

    for (unsigned int i=0; i<input.size(); i++) { // iterate over the input 

      if (StringVector::is_na(input[i]) || (input[i].size() == 0)) {
        out[i] = StringVector::create(NA_STRING); // bad input
      } else if (input[i].size() % 2 == 0) { // likey to be ok input

        RawVector tmp(input[i].size() / 2); // only need half the space
        std::string h = boost::algorithm::unhex(Rcpp::as<std::string>(input[i])); // do the work
        std::copy(h.begin(), h.end(), tmp.begin()); // copy it to our raw vector

        out[i] = tmp; // save it to the List

      } else {
        out[i] =  StringVector::create(NA_STRING); // bad input
      }

    }

    return(out);

  }
', includes = c('#include <boost/algorithm/hex.hpp>')
) 
```

现在，我们的环境中有一个`dehexify_cpp()`函数，所以我们可以对任何有效的 R 数据使用它。让我们看看我们是否得到与`stringi` R 版本相同的结果:

```
dehexify_cpp(x)
## [[1]]
## [1] 01 00 00 93 02 00 0a 00 27 22 bc cf 9d b1 26 fa 9a 02 00 0a 00 27 22 bd cf 9d c0 a8 01
## [30] 01 01 00 06 00 27 22 bc cf 9d 0a 00 04 00 00 6a e4 0b 00 0c 62 6a 73 2e 65 72 65 6e 69
## [59] 6c 64 6f 0c 00 06 41 47 35 2d 48 50 0d 00 10 4d 6f 72 72 6f 5f 44 6f 75 72 61 64 6f 5f
## [88] 30 33 0e 00 01 02 03 00 22 58 4d 2e 61 72 37 32 34 30 2e 76 35 2e 36 2e 33 2e 32 38 35
## [117] 39 31 2e 31 35 31 31 33 30 2e 31 37 34 39 10 00 02 e2 45 14 00 0d 41 69 72 47 72 69 64
## [146] 20 4d 35 20 48 50 
```

除了它是一个列表(因为我们同时处理了矢量化)，它确实是相同的数据。

通过这一小段相当简单的 Rcpp/C++代码，我们获得了大约 4μs 的更快的执行时间。是的，这不是一个错误:*四*微秒。

我们将用工作中的有效载荷数据对它进行真实世界的测试:

```
# This assumes you have a "~/Data" directory. Put it somewhere
# else if you don't have a "~/Data" directory.

if (!file.exists("~/Data/dehexify-sample-data.txt.gz")) {
  download.file(
    url = "https://rud.is/dl/dehexify-sample-data.txt.gz", 
    destfile = "~/Data/dehexify-sample-data.txt.gz"
  )
}

char_hex_lines <- readr::read_lines("~/Data/dehexify-sample-data.txt.gz")

length(char_hex_lines)
## [1] 501926

res <- dehexify_cpp(char_hex_lines) 
```

在我的主开发系统上运行只需要一秒多一点的时间。但是，真的有效吗？我随机选择了索引`998`,所以让我们用另一篇博文中的工具来看看:

```
udpprobe::parse_ubnt_discovery_response(res[[998]])
## [Model: N5N; Firmware: XW.ar934x.v5.5.9.21734.140403.1801; Uptime: 13.1 (hrs) 
```

是的，它确实起作用了。

### 鳍

现在还是 2019 年初，如果你还没有下定任何决心，或者想要替代一个不太管用的决心(谁愿意开车去健身房？)另外，也许可以在列表中添加“Rcpp 实验”,因为少量的 Rcpp 就可以大大加快某些任务的速度。