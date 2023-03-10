# 使用 Cobra + Viper CLI 解析文本文件

> 原文：<https://dev.to/adron/go-with-cobra--viper-cli-for-parsing-text-files-2fcf>

*第 1 部分，共 3 部分*-Go-Cobra+Viper CLI 中的编码会话，用于解析文本文件、检索 Twitter 数据、导出到各种文件格式以及导出到 Apache Cassandra。

**更新部分:(转到原始复合代码博客文章)**

1.  [用于解析文本文件的 Go - Cobra + Viper CLI 中的 Twitz 编码会话(本文)](http://compositecode.blog/2018/12/04/twitz-coding-session-in-go-cobra-viper-cli-for-parsing-text-files/)
2.  [Go-Cobra+Viper CLI 中的 Twitz 编码会话，初始安装 Twitter+Cassandra](http://compositecode.blog/2018/12/07/twitz-coding-session-in-go-cobra-viper-cli-with-initial-twitter-cassandra-installation/)
3.  [Go-Cobra 中的 Twitz 编码会话+ Viper CLI 总结+ Twitter 数据检索](http://compositecode.blog/2018/12/27/twitz-coding-session-in-go-cobra-viper-cli-wrap-up-twitter-data-retrieval/)

[https://www.youtube.com/embed/1iniLntln2A](https://www.youtube.com/embed/1iniLntln2A)

**[3:40](https://www.youtube.com/watch?v=1iniLntln2A&t=220s)** 陈述目标的应用。我浏览了一些我打算构建到这个应用程序中的特性，把它们输入到一个文本文档中。以下是我添加到列表中的项目。

1.  能够导入混合在一堆其他文本中的 Twitter 句柄的文本文件。
2.  清除列表的能力。
3.  能够将清理后的句柄列表导出到文本、csv、JSON、xml 或纯文本文件。
4.  使用 Twitter API 从简历、最新推文和其他相关信息中提取数据的能力。

[![github](img/da4b4c0689d2d48142d3c90ad97e439b.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--9cbrRPwD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://compositecode.files.wordpress.com/2018/12/github.png) [** 8:26 **](https://www.youtube.com/watch?v=1iniLntln2A&t=506s) 创建新的 Github 回购。使用 Github 创建新回购的一些提示和技巧。

*   Github 回购: [Twitz](https://github.com/Adron/twitz)
*   许可证:[阿帕奇-2.0](https://github.com/Adron/twitz/blob/master/LICENSE)
*   发布归档选项:[特性请求](https://github.com/Adron/twitz/issues/new?template=feature_request.md)或 [Bug 报告](https://github.com/Adron/twitz/issues/new?template=bug_report.md)。
*   [README.md](https://github.com/Adron/twitz/blob/master/README.md)
*   在 https://adron.github.io/twitz/的[为](https://adron.github.io/twitz/)[项目页面](https://adron.github.io/twitz/)提供皮肤的 [Jeckyll yml 文件](https://github.com/Adron/twitz/blob/master/_config.yml)。

[**9:46**](https://www.youtube.com/watch?v=1iniLntln2A&t=586s) Twitz 诞生了！呜呜
~~我得尽快重置我的 SSH 密钥。如果你不确定如何做，这是一个快速教程，否则跳到我继续项目设置和初始编码的部分。

[**12:40**](https://www.youtube.com/watch?v=1iniLntln2A&t=760s) 为@justForFunc 和 Francesc 在眼镜蛇上的那一集大声疾呼！

看看 [@francesc](https://dev.to/francesc) 的《只为 Func》。这是一个伟大的视频/播客系列的很多很多去！

*   推特上的 [@JustforFunc](https://twitter.com/justforfunc) 。
*   [只是为了 Youtube 上的 Func](https://www.youtube.com/channel/UC_BzFbxG2za3bp5NRRRXJSw)。
*   我提到的关于 [Cobra CLI 库](https://youtu.be/WvWPGVKLvR4)的那一集。

[**13:02**](https://www.youtube.com/watch?v=1iniLntln2A&t=782s) 回到项目设置。克隆 repo，获取初始 README.emd，。gitignore 文件设置，以及基本项目的相关资料。我还通过 Github 接口添加了 Github 的“问题”文件，并在稍后对这些更改进行了重新调整。
[**14:20**](https://www.youtube.com/watch?v=1iniLntln2A&t=860s) 增加了一些选项。gitignore 文件。
[**15:20**](https://www.youtube.com/watch?v=1iniLntln2A&t=920s) 在许可文件中设置日期和版权。
**[16:00](https://www.youtube.com/watch?v=1iniLntln2A&t=960s)** 进一步设置项目，移除 WIKIs、项目和保留问题的理由。
[**16:53**](https://www.youtube.com/watch?v=1iniLntln2A&t=1013s) 开盘后戈兰德涨了一次更新。在这里，我开始讲述如何在 Goland 中设置 Go 项目的具体细节，设置配置和相关资料。
[**17:14**](https://www.youtube.com/watch?v=1iniLntln2A&t=1034s)Goland 的`golang`命令行启动器的设置。
**[25:45](https://www.youtube.com/watch?v=1iniLntln2A&t=1545s)** 眼镜蛇简介(以及联想到毒蛇的第一次提及)。
**[26:43](https://www.youtube.com/watch?v=1iniLntln2A&t=1603s)** 安装带有`go get github.com/spf13/cobra/cobra/`的 Cobra 和 gotchas(记得设置路径等)。
[**29:50**](https://www.youtube.com/watch?v=1iniLntln2A&t=1790s) 使用 Cobra CLI 构建出命令行界面 app 的各种命令。
[**35:03**](https://www.youtube.com/watch?v=1iniLntln2A&t=2103s) 查看生成的代码，并对注释及其有用性进行评论。
**[36:00](https://www.youtube.com/watch?v=1iniLntln2A&t=2160s)** 通过 main func 将 Cobra 的最后几位与一些代码连接起来，供 CLI 执行。
**[48:07](https://www.youtube.com/watch?v=1iniLntln2A&t=2887s)** 我在这一点上开始接线起蝰蛇的配置。从这里开始，就是编码、编码、配置和更多的编码。

## 执行“twitz config”命令

**[1:07:20](https://www.youtube.com/watch?v=1iniLntln2A&t=4040s)** 确认配置并逐步执行`twitz config`命令。
**[1:10:40](https://www.youtube.com/watch?v=1iniLntln2A&t=4240s)** 第一次执行`twitz config`我描述了 Cobra 的文档字符串在哪里以及如何通过`--help`标志工作，并根据需要放入其他帮助文件中进行简短或详细的描述。

实现时的基本配置代码类似于会话结束时的样子。这段代码除了提供配置文件中配置变量的替换之外，没有做太多的事情。最后，我将在这个文件中添加更多内容，以便在设置 CLI 时更容易调试。

```
package cmd

import (
    "fmt"
    "github.com/spf13/cobra"
    "github.com/spf13/viper"
)

// configCmd represents the config command
var configCmd = &cobra.Command{
    Use:   "config",
    Short: "A brief description of your command",
    Long: `A longer description that spans multiple lines and likely contains examples
and usage of using your command. For the custom example:
Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.`,
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Printf("Twitterers File: %s\n", viper.GetString("file"))
        fmt.Printf("Export File: %s\n", viper.GetString("fileExport"))
        fmt.Printf("Export Format: %s\n", viper.GetString("fileFormat"))<span data-mce-type="bookmark" id="mce_SELREST_start" data-mce-style="overflow:hidden;line-height:0" style="overflow:hidden;line-height:0;"></span>
    },
}

func init() {
    rootCmd.AddCommand(configCmd)
} 
```

## 执行“twitz parse”命令

[**1:14:10**](https://www.youtube.com/watch?v=1iniLntln2A&t=4450s) 开始执行`twitz parse`命令。
**[1:16:24](https://www.youtube.com/watch?v=1iniLntln2A&t=4584s)** 盗梦空间对我的“helpers.go”文件进行了一些代码的整理和清理。
[**1:26:22**](https://www.youtube.com/watch?v=1iniLntln2A&t=5182s)REDEX 实现时间！
**[1:32:12](https://www.youtube.com/watch?v=1iniLntln2A&t=5532s)** 试水 REGEX101.com 现场。

当我发布这个应用程序开发会议的第二个视频时，我将发布完成的代码并写下一些关于其背后思想的细节。

这是最后一节课了。希望这个总结能对任何使用 Go 和 Cobra 构建 CLI 应用的人有所帮助。直到下一个会话，愉快的代码。

我在 Twitter [@Adron](https://twitter.com/Adron) 和 Twitch [@adronhall](https://twitch.tv/adronhall/) 上一直听金属和编码，写关于编码、学习和教授各种主题，从数据库技术到编码印章。感谢阅读！