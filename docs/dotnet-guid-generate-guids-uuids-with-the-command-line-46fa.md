# dotnet-guid:用命令行生成 guid/uuid

> 原文：<https://dev.to/sebnilsson/dotnet-guid-generate-guids-uuids-with-the-command-line-46fa>

我参与过一些项目，虽然不是很多，但是生成 guid/uuid 很重要。为此，我曾使用 https://guidgenerator.com 的[等在线工具，但当我换机时，浏览器的自动补全功能失去了那个链接。](https://guidgenerator.com)

至少出于这个原因，而且主要是为了好玩，我决定写一篇[。NET Core Global Tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools) 可以快速生成一个或多个 guid/uuid，可以是任何需要的格式。

## 安装

下载[。NET Core SDK 2.1](https://aka.ms/DotNetCore21) 或更高版本。 [`dotnet-guid`](https://www.nuget.org/packages/dotnet-guid) 的安装。NET 全局工具，使用命令行:

```
dotnet tool install -g dotnet-guid 
```

## 用法

```
Usage: guid [arguments] [options]

Arguments:
  Count Defines how may GUIDs/UUIDs to generate. Defaults to 1.

Options:
  -?|-h|--help Show help information
  -n Formatted as 32 digits:
                00000000000000000000000000000000
  -d Formatted as 32 digits separated by hyphens:
                00000000-0000-0000-0000-000000000000
  -b Formatted as 32 digits separated by hyphens, enclosed in braces:
                {00000000-0000-0000-0000-000000000000}
  -p Formatted as 32 digits separated by hyphens, enclosed in parentheses:
                (00000000-0000-0000-0000-000000000000)
  -x Formatted as four hexadecimal values enclosed in braces,
                where the fourth value is a subset of eight hexadecimal values that is also enclosed in braces:
                {0x00000000,0x0000,0x0000,{0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00}}
  -e Defines if the GUIDs/UUIDs should be empty, using zero-values only.
  -u Defines if the GUIDs/UUIDs generated should be upper-cased letters. 
```

## 例子

要获得单个 GUID/UUID，只需键入:

```
guid 
```

获取 3 个随机的 guid/uuid，字母大写，用括号格式化:

```
guid 3 -b -u 
```

你可以在 GitHub 上找到[源代码，在 Nuget](https://github.com/sebnilsson/DotnetGuid) 上找到[包，在 MyGet](https://www.nuget.org/packages/dotnet-guid/) 上找到[的最新版本。](https://www.myget.org/feed/sebnilsson/package/nuget/dotnet-guid)

你可以找到更多的列表。NET Core Global Tools on GitHub ，由 Nate McMaster 维护。