# 用几行代码阅读 YAMLy config

> 原文：<https://dev.to/detunized/read-yamly-config-with-a-few-lines-of-code-4heo>

*原贴于[detunized.net](https://detunized.net/posts/2019-02-25-read-yamly-config-with-a-few-lines-of-code/)T3】*

我正在开发一个 C#库，在一个简单的示例应用程序中，我需要加载一个配置文件。它不需要很花哨或者非常高效。像 INI、JSON、TOML 或 YAML 这样的软件就可以了。我不想有任何依赖，不想麻烦用户安装任何库。不幸的是。NET 在其标准库中没有提供这些。有 XML，但我受不了。

所以我想，我可以在几分钟内编写一个简单的文本配置文件解析器。为什么不试一试。我只需要字符串键和值。最好有评论。大概是这样的:

```
# Login username
username: dude@lebowski.com
# User password
password: no one will guess
# URL
url: https://lebowski.com:443/index.html 
```

这实际上是 YAML 的一个子集。非常干净，可读性强。为此编写一个解析器会有多难。通常情况下，每次我对自己说这样的话时，我都会在心理上为自己的巨大低估做好准备。看起来十分钟的任务，可能会变成一周的项目。奇怪的是，这次没有。多亏了非常棒的运行时库和令人敬畏的 LINQ 支持，我在 3 分钟内就有了一个完全可用的解决方案:

```
Dictionary<string, string> ReadConfig(string filename)
{
    return File
        .ReadAllLines(filename)
        .Select(line => line.Trim())
        .Where(line => line.Length > 0 && !line.StartsWith("#"))
        .Select(line => line.Split(new[] {':'}, 2))
        .Where(parts => parts.Length == 2)
        .ToDictionary(parts => parts[0].Trim(), parts => parts[1].Trim());
} 
```

这个功能不是疯狂高效，但谁在乎。它非常健壮，只要可以读取文件，它就不会出错。但是，如果出现语法错误，它没有任何错误报告。它会简单地忽略它。对我来说，这已经足够好了。

让我们看看这是如何工作的。首先，我阅读文件。这个调用将返回一个字符串数组，每行一个:

```
File.ReadAllLines(filename) 
```

接下来，我修剪两端的所有空白。在 LINQ 的`Select`与其他地方的`map`几乎一样，它通过对每个元素应用一个函数来转换序列:

```
.Select(line => line.Trim()) 
```

接下来，我过滤掉所有空白的或者以`#`开头的行。`Where`通过保留满足给定谓词的元素来过滤序列:

```
.Where(line => line.Length > 0 && !line.StartsWith("#")) 
```

接下来，我在第一个冒号上拆分每一行。如果该行的其余部分有更多的冒号，它们将不会被拆分，并成为值的一部分。那是故意的:

```
.Select(line => line.Split(new[] {':'}, 2)) 
```

接下来，我过滤掉所有没有被分成两部分的行。这是语法错误会被忽略和丢弃的地方:

```
.Where(parts => parts.Length == 2) 
```

最后一步，我将两个元素的数组转换成一个字典。在 C#中被称为字典的东西在其他语言中可能被称为*对象*、*映射*或*哈希映射*。它是一个键值存储或关联数组。在这一步中，我还修剪了键上的任何尾随空格和值上的任何前导空格(其他结尾已经被修剪了):

```
.ToDictionary(parts => parts[0].TrimEnd(), parts => parts[1].TrimStart()); 
```

完成了。在几行和一条语句中，我读取并解析了一个配置文件。

JavaScript 具有非常相似的函数式编程能力，因此可以在 JS 中镜像这个解决方案。[像往常一样](https://www.destroyallsoftware.com/talks/wat)，这里有一些问题。在这种情况下，JS `String.split`函数表现怪异。与我尝试的所有其他语言相比，limit 参数的工作方式不同。JavaScript 中的`split`不是返回最后一个元素中的剩余行，而是截断输入。[窟](https://www.destroyallsoftware.com/talks/wat)？！为了解决这个问题，我必须在将数组转换为对象的最后一个`reduce`之前，在代码行中把分开的尾部`join`放回一起。

```
function readConfig(filename) {
    return require("fs")
        .readFileSync(filename, "utf-8")
        .split("\n")
        .map(x => x.trim())
        .filter(x => x.length > 0 && !x.startsWith("#"))
        .map(x => x.split(":"))
        .filter(x => x.length > 1)
        .map(x => [x[0], x.slice(1).join(":")])
        .reduce((a, x) => (a[x[0].trimEnd()] = x[1].trimStart(), a), {})
} 
```

JavaScript 有对 JSON 的本地支持，所以使用自己的配置格式可能是愚蠢的，因为 JSON 可以在一个简短的语句中读取。但是不支持注释。

我认为 Ruby 版本是最干净的，尽管它实际上是一样的:

```
def read_config filename
    File
        .readlines("config.yaml")
        .map(&:strip)
        .reject { |x| x.empty? || x.start_with?("#") }
        .map { |x| x.split ":", 2 }
        .select { |x| x.size == 2 }
        .map { |k, v| [k.rstrip, v.lstrip] }
        .to_h
end 
```

Ruby 同时支持 YAML 和 JSON。这样会更容易些

```
YAML.load_file "config.yaml" 
```

但是我不得不引用一些值，因为 YAML 在空格和特殊字符方面不够灵活。

我该怎么做？我不会！我不想淹没在`if` s、`for` s、`err` s、`nil` s，就对写代码和`go get`一些包说不吧。