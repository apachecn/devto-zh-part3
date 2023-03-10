# Elixir: Elixir 1.9 中的新功能

> 原文：<https://dev.to/gumi/elixir-elixir-1-9-1dmp>

在 2019 年 4 月 29 日在 plataforma tec 博客上公开的“[what’s new in elixir–apr/19](http://blog.plataformatec.com.br/2019/04/whats-new-in-elixir-apr-19/)”中，介绍了对 v1.9.0-dev 添加的新功能。 本文将补充官方文档等信息对其进行说明。

# [追加](#-raw-mix-release-endraw-%E3%81%AE%E8%BF%BD%E5%8A%A0)`mix release`

新配备了[`mix release`](https://hexdocs.pm/mix/master/Mix.Tasks.Release.html)。 除了基本版本外，还支持运行时设置、使用模板文件定制、定制步骤等。

`mix release`组装当前项目的自完成发行。

```
MIX_ENV=prod mix release
MIX_ENV=prod mix release NAME 
```

Enter fullscreen mode Exit fullscreen mode

发布后，可以将其打包并部署到目标中。 目标必须在同一操作系统( OS )的发行版和版本中运行`mix release`命令的计算机上移动。

释放可以在`mix.exs`文件的`def project`中通过`:releases`键进行设定。

```
def project do
  [
    releases: [
      demo: [
        include_executables_for: [:unix],
        applications: [runtime_tools: :permanent]
      ],

      ...
    ]
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

# [不使用](#-raw-mix-new-endraw-%E3%81%A7%E8%A8%AD%E5%AE%9A%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%92%E3%81%A4%E3%81%8F%E3%82%89%E3%81%AA%E3%81%84)`mix new`制作设定文件

首先，不再用`mix new`来做`config/config.exs`。 因为一直以来，库及其作者都不希望依赖配置文件(“[avoid application configuration](https://hexdocs.pm/elixir/master/library-guidelines.html#avoid-application-configuration))。 其次，`mix new --umbrella`不制作每个子 APP 应用的设定。 所有设置都将在安布雷拉项目的根目录下声明。 理由请参考“[no longer generate config for mix new](https://github.com/elixir-lang/elixir/pull/8932)”。

# 在 Elixir 中增加新的`Config`模块

发布可能会有自己的设定。 此外，您不希望依赖构建工具的 Mix。 因此，`use Mix.Config`为柔和的不推荐( soft-deprecated ) (关于柔和的不推荐，请参考“[Elixir :版本间的兼容性和不推荐](https://dev.to/gumi/elixir--254a)”)。 接下来，请将 Elixir 新配备的[`Config`模块](https://hexdocs.pm/elixir/master/Config.html)作为`import Config`使用。

兼容性将通过以下两个部分被打破(请参阅“[Add config providers](https://github.com/elixir-lang/elixir/pull/8957) ”) :

*   添加设置模块
    *   `Config`
    *   `Config.Reader`
    *   `Config.Provider`
*   在发行版中添加`Config.Provider`支持

| old | new |
| --- | --- |
| 使用 Mix。配置 | 导入配置 |
| Mix.Config.merge/2 | Config.Reader.merge/2 |
| 混合。Config.read！/2 | 配置。Reader.read！/2 |
| 混合。配置.评估！/2 | 配置。Reader.read_imports！/2 |
| Mix.Config.persist/1 | Application.put_all_env/2 |

# 记录器的性能提高

记录器简化了消息被丢弃时的处理。 Erlang/OTP v21.2 和更高版本还提高了整体性能。

# 其他新功能

## `~U`添加杉菊苷

增加了制作 UTC [`DateTime`](https://hexdocs.pm/elixir/master/DateTime.html)的[`~U`](https://hexdocs.pm/elixir/master/Kernel.html#sigil_U/2)(参照“[Add ~U sigil for UTC date times](https://github.com/elixir-lang/elixir/pull/8824) ”)。 另外，没有`~u`。

## 新的函数

为了简化配置文件，添加了以下三个新函数(请参见“[add support for a default value in system.get _ env/1](https://github.com/elixir-lang/elixir/pull/3798)”) :

*   [T2`System.get_env/2`](https://hexdocs.pm/elixir/master/System.html#get_env/2)
*   [T2`System.fetch_env/1`](https://hexdocs.pm/elixir/master/System.html#fetch_env/1)
*   [T2`System.fetch_env!/1`](https://hexdocs.pm/elixir/master/System.html#fetch_env!/1)

## 细微的变更

有关详细变更，请参考“[Changelog for Elixir v1.9](https://github.com/elixir-lang/elixir/blob/master/CHANGELOG.md) ”。