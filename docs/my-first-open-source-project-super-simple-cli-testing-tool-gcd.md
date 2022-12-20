# 我的第一个开源项目——超级简单的 cli 测试工具

> 原文：<https://dev.to/simonbaeumer/my-first-open-source-project-super-simple-cli-testing-tool-gcd>

## 简介

在本文中，我将介绍我的第一个开源项目`commander`。它根据简单的`yaml`文件中定义的测试来测试 cli 应用。

来看看:[https://github.com/SimonBaeumer/commander](https://github.com/SimonBaeumer/commander)

## 问题？

对于另一个项目，我需要一个具有以下能力的工具:

*   对不同的操作系统(主要是 osx、linux、windows)使用相同的工具
*   简单和容易的测试编写
*   简单安装(没有繁重的 lib 或语言安装)
*   独立于语言
*   请不要另一个 DSL

尤其是繁重的安装和语言独立性对我来说很重要，因为我想在不同的 docker 容器中测试一个应用程序。

可悲的是没有一个单一的工具。`bats`只对 bash 有效，go 的`icmd`和 python 的`expect`需要你安装一门语言并熟悉它。

## 为什么用`commander`？

*   易于安装
*   自动生成测试
*   快速执行
*   独立于平台
*   就`yaml`

## 安装

下载二进制文件，并授予它执行权限。如果你想从你的终端直接调用它，把它添加到你的`path`环境变量中。

### Linux & osx

```
# linux amd64
curl -L https://github.com/SimonBaeumer/commander/releases/download/v1.0.1/commander-linux-amd64 -o commander
chmod +x commander 

# osx amd64
curl -L https://github.com/SimonBaeumer/commander/releases/download/v1.0.1/commander-darwin-amd64 -o commander
chmod +x commander 
```

### windows amd64

```
curl  -L  https://github.com/SimonBaeumer/commander/releases/download/v1.0.1/commander-windows-amd64.exe  -o  commander.exe 
```

...或者手动下载并添加到您的`path`中。

## 快速启动！

```
# Let commander create your test!
$ .commander add echo hello dev.to
written to /current/working/dir/commander.yaml

# ... written to the default commander.yaml
$ cat commander.yaml
tests:
  echo hello dev.to:
    exit-code: 0
    stdout: hello dev.to

# Executes the generated commander config
$ commander test Starting test file commander.yaml...

✓ echo hello dev.to

Duration: 0.003s
Count: 1, Failed: 0 
```

## 一个完整的`commander.yaml`例子

正如你所看到的，测试套件很容易理解和编写。

```
tests:
  echo hello world:
    stdout:
      lines:
        1: hello world # assert a specifc line
      contains:
        - hello world # this is the default
      exactly: hello # Match stdout exactly
    # The same can be done for stderr
    exit-code: 0
    config:
      timeout: 5ms # set timeout for test
      dir: /tmp # set working dir
      env:
        SOME_ENV: value

  it should have a title: # You can also define a title to be more clear...
    command: echo title # ... and define your command under test here
    exit-code: 0 
```

## 定义默认测试配置

此外，您可以为所有测试定义一个默认配置。这可以被本地测试配置覆盖。

```
config: # Define the config globally
  timeout: 5ms
  dir: /tmp
  env:
    KEY: VALUE

tests:
  print env and dir:
    command: "echo  $KEY  $(pwd)"
    stdout: VALUE /tmp
    exit-code: 0 
```

## 调试测试

有时候你需要调试你的命令。这可以通过`--verbose`标志来完成。

```
# Generate the test
$ commander add echo hello dev.to

# Execute it with logging output
$ commander test --verbose
Starting test file commander.yaml...

2019/03/28 16:56:48 title: 'echo hello dev.to'  ExitCode:  0
2019/03/28 16:56:48 title: 'echo hello dev.to'  Stdout:  hello dev.to
2019/03/28 16:56:48 title: 'echo hello dev.to'  Stderr:  
✓ echo hello dev.to

Duration: 0.002s
Count: 1, Failed: 0 
```

## 正在执行

```
# Execute another file instead of the default
$ commander test another.yaml

# Filter tests
$ commander test commander.yaml "echo hello" 
```

## 生成测试

```
# Add a test to the default commander.yaml
$ ./commander add echo hello
written to /tmp/commander.yaml

# Write to a given file
$ ./commander add --file=test.yaml echo hello
written to test.yaml

# Write to stdout and file
$ ./commander add --stdout echo hello
tests:
  echo hello:
    exit-code: 0
    stdout: hello

written to /tmp/commander.yaml

# Only to stdout
$ ./commander add --stdout --no-file echo hello
tests:
  echo hello:
    exit-code: 0
    stdout: hello 
```

## 结论

有了`commander`,你可以轻松地生成你的测试用例，并在不同的平台上执行它们，而无需学习一门新的语言或安装烦人的库和工具。

贡献总是热烈欢迎！

我希望你们中的一些人可以在他们的项目中使用它:)

链接:[https://github.com/SimonBaeumer/commander](https://github.com/SimonBaeumer/commander)

注意:请对我的第一篇博文给予反馈！