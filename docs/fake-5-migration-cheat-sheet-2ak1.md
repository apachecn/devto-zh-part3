# 假 5 迁移备忘单

> 原文：<https://dev.to/jeremyabbott/fake-5-migration-cheat-sheet-2ak1>

# 假 5 迁移备忘单

大家节日快乐！

[![Pikachu in a Santa hat](img/416ba417f06bc2975999a0c3e0c61f6a.png "Happy holidays from Pikachu")](https://res.cloudinary.com/practicaldev/image/fetch/s--lw_-b_Nh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kxv8wyiqsopwpt72n876.png)

当您开始从 FAKE 4 和 FAKE 5 迁移时，您会发现仅仅打开`FAKE`名称空间是不够的。此外，如果像我一样，反对警告让你抓狂，你会想尽快摆脱它们。我制作了下面的备忘单来加速您的假迁移(并帮助我更新我仍然需要更新的其他脚本。)

## 功能/模块变化

以下是我不得不更新不止一次的函数。其中大多数至少需要引用一个 nuget 包和/或打开一个以前不必打开的模块。

下面的每个头都是一个伪 4 函数的名字。下面的代码片段展示了如何在 FAKE 4 中使用它，以及如何在 FAKE 5 中完成相同的任务。

### `Target`

```
// =======================================
// Before
// =======================================

Target "Foo" (fun _ -> 
    printfn "Foo to the Bar"
)

// =======================================
// After
// =======================================

open Fake.Core // From the Fake.Core.Target nuget package

Target.create "Foo" (fun _ ->
    printfn "Foo to the Bar"
) 
```

Enter fullscreen mode Exit fullscreen mode

#### 目标操作员

所有的假自定义操作符现在都在它们自己的模块中。

```
open Fake.Core.TargetOperators // From the Fake.Core.Target nuget package
"Clean"
    ==> "Restore"
    ==> "Build" 
```

Enter fullscreen mode Exit fullscreen mode

**重要提示**:假 4 目标操作符(来自 FakeLib)对假 5 目标不起作用(反之亦然)。这意味着将目标转换为假 5 意味着使用假 5 中的操作符。

### `FullName`

```
// =======================================
// Before
// =======================================

let path = "./foo" |> FullName // Auto-opened from Fake.FileSystemHelper

// =======================================
// After
// =======================================

open Fake.IO 

let path = "./foo" |> Path.getFullName // From the Fake.IO.FileSystem the nuget package 
```

Enter fullscreen mode Exit fullscreen mode

### `getBuildParam`

```
// =======================================
// Before
// =======================================

// In FAKE 4 getBuildParam implicitly called getBuildParamOrDefault with empty string as the default

let userName = getBuildParam "userName" // Auto-opened from Fake.EnvironmentHelper

// =======================================
// After
// =======================================

open Fake.Core

let userName = 
    // From nuget package Fake.Core.Environment
    Environment.environVarOrDefault "UserName" "" 
```

Enter fullscreen mode Exit fullscreen mode

**一些额外的注释**

在 FAKE 5 中，向您的假脚本传递参数略有不同。在假 4 中，您可能会有类似

`./build.sh <targetName> <buildParam>=foo`，根据您的用例，您现在有几个不同的选项:

```
# create an environment variable that's scoped to the current shell session.
export myKey="my value"
./build.sh build 
```

Enter fullscreen mode Exit fullscreen mode

```
let myKey = Environment.environVarOrDefault "myKey" "" 
```

Enter fullscreen mode Exit fullscreen mode

如果你想在命令行上传递参数，你必须用下面的语法来传递它们:

```
# using the FAKE dotnet CLI tool
fake run build.fsx -t build --arg 
```

Enter fullscreen mode Exit fullscreen mode

然后在 build.fsx:

```
Target.create "Foo" (fun p ->
    printfn "args: %A" p.Context.Arguments

    printfn "%A " p
)

Target.runOrDefaultWithArguments "Foo" 
```

Enter fullscreen mode Exit fullscreen mode

您需要使用`Target.runOrDefaultWithArguments`而不是`Target.runOrDefault`来以这种方式访问参数。另外，目标函数签名现在是`string -> TargetParameter -> unit -> unit`。你必须使用`TargetParameter`(上面例子中的`p`)来访问它。`Context.Arguments`是一个`string list`。您可以使用模块 [`Fake.Core.CommandLineParsing`](https://fake.build/core-commandlineparsing.html) 来解析目标参数。

### `parseAllReleaseNotes`

```
// =======================================
// Before
// =======================================
open Fake.ReleaseNotesHelper

let releaseNotes = File.ReadAllLines "RELEASE_NOTES.md"

let releaseNotesData =
     releaseNotes
     |> parseAllReleaseNotes

let release = List.head releaseNotesData

// =======================================
// After
// =======================================

open Fake.Core

let release = 
    // From Fake.Core.ReleaseNotes nuget package
    ReleaseNotes.load "RELEASE_NOTES.md" 
```

Enter fullscreen mode Exit fullscreen mode

### `SemVerHelper.parse`

```
// =======================================
// Before
// =======================================

let packageVersion = SemVerHelper.parse release.NugetVersion // From Fake.SemVerHelper

// =======================================
// After
// =======================================

open Fake.Core
let semVer =
    // From Fake.Core.SemVer nuget package
    SemVer.parse release.NugetVersion
printfn "semver.Parse: %s" <| semVer.ToString() 
```

Enter fullscreen mode Exit fullscreen mode

### `isUnix`

```
// =======================================
// Before
// =======================================

let pathSep = if isUnix then """\""" else "/" // From Fake.EnvironmentHelper

// =======================================
// After
// =======================================
open Fake.Core

let pathSep = 
    // From the nuget package Fake.Core.Environment
    if Environment.isUnix then """\""" else "/" 
```

Enter fullscreen mode Exit fullscreen mode

### `ProcessHelper.tryFindFileOnPath`

```
// =======================================
// Before
// =======================================

let fooExe = ProcessHelper.tryFindFileOnPath "foo"

// =======================================
// After
// =======================================

open Fake.Core

let fooExe =
    // From the nuget package Fake.Core.Process
    ProcessUtils.tryFindFileOnPath "foo" 
```

Enter fullscreen mode Exit fullscreen mode

### `DotNetCli.GetDotNetSDKVersionFromGlobalJson()`

```
// =======================================
// Before
// =======================================

let dotnetcliVersion = DotNetCli.GetDotNetSDKVersionFromGlobalJson()

// =======================================
// After
// =======================================

open Fake.DotNet

// After
// From the nuget package Fake.DotNet.Cli
// In the namespace Fake.DotNet and module DotNet
let dotnetcliVersion = DotNet.CliVersion.GlobalJson 
```

Enter fullscreen mode Exit fullscreen mode

### `CleanDirs`

```
// =======================================
// Before
// =======================================

// Auto-opened from Fake.FileHelper
CleanDirs [exampleDir]

// =======================================
// After
// =======================================

open Fake.IO

//From the nuget package Fake.IO.FileSystem
Shell.cleanDirs [deployDir] 
```

Enter fullscreen mode Exit fullscreen mode

### [T3`ExecProcess`/`Shell.Exec`](#-raw-execprocess-endraw-raw-shellexec-endraw-)

```
// =======================================
// Before
// =======================================

let result = Shell.Exec("foo", "--version")
if result <> 0 failwith "boom"

// =======================================
// After
// =======================================

open Fake.Core

let result =
    // From the NuGet Package Fake.Core.Process
    CreateProcess.fromRawCommand "dotnet" ["--info"]
    |> Proc.run
if result.ExitCode <> 0 then failwith "boom" 
```

Enter fullscreen mode Exit fullscreen mode

### `build`

```
// =======================================
// Before
// =======================================
open Fake

Target "MSBuild" (fun _ ->
    let setMsBuildParams defaultParams =
        { defaultParams with
            Targets=["Build"]
            Properties = ["Configuration", "Debug"]
        }
    // Auto-opened from Fake.MSBuildHelper
    build setMsBuildParams "Foo.sln"
)

// =======================================
// After
// =======================================

open Fake.DotNet

Target.create "MSBuild" (fun _ ->
    let setMsBuildParams defaultParams =
        { defaultParams with
            Targets=["Build"]
            Properties = ["Configuration", "Debug"]
        }
    MSBuild.build setMsBuildParams "Foo.sln"
) 
```

Enter fullscreen mode Exit fullscreen mode