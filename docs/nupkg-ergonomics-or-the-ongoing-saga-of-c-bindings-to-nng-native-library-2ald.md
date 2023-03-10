# Nupkg 人机工程学，或者说，正在进行的 C#绑定到 NNG 本地库的传奇

> 原文：<https://dev.to/jeikabu/nupkg-ergonomics-or-the-ongoing-saga-of-c-bindings-to-nng-native-library-2ald>

我们之前创建的包使用起来很痛苦:

*   包含特定于平台的二进制文件的 nupkg `runtimes/`文件夹不会被复制到明显的地方。在安装时，所有内容都进入 nuget 缓存；在 OSX `~/.nuget/packages/subor.nng.netcore/`，在 Windows `%USERPROFILE%/.nuget/packages/subor.nng.netcore/`
*   在 Visual Studio 中，如果将 [**属性- >构建- >平台目标**设置为`x86`或`x64`](//./nupkg-containing-native-libraries-1576#consuming-nupkg) ，那么正确的共享库将被复制到输出文件夹中

此外，由于开发和 nupkg 环境之间的脱节，我们还遇到了 nupkg 无法工作的问题。我们希望使用与 nuget 相同的文件夹结构进行开发。

## 将“运行”置于运行时

添加一个 [`.props/.target`文件](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package#including-msbuild-props-and-targets-in-a-package)大概是不可避免的。当 Nuget 安装一个包时，它会将包的`build/`文件夹中的`<Import>`文件放到项目中；`.props`在项目顶部，`.targets`在底部。

我们的包是 [Subor.nng.NETCore](https://www.nuget.org/packages/Subor.nng.NETCore/) ，所以创建`build/Subor.nng.NETCore.targets` :

```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <ItemGroup>
    <Content Include="$(MSBuildThisFileDirectory)..\runtimes\**">
      <Link>runtimes\%(RecursiveDir)%(Filename)%(Extension)</Link>
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </Content>
  </ItemGroup>
</Project> 
```

Enter fullscreen mode Exit fullscreen mode

这将所有的`runtimes/`文件复制到输出目录。

将`.targets`文件包含在 nupkg 中，在`nng.NETCore.csproj` :

```
<ItemGroup>
  <Content Include="build\**">
    <PackagePath>build\%(RecursiveDir)%(Filename)%(Extension)</PackagePath>
    <Visible>false</Visible>
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </Content>
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何使用我们包的项目都将`<Import>`这个`.targets`并将我们特定于平台的`runtimes/`文件复制到它的输出文件夹中。

几个在线资源提到创建`install.ps1`，但是[在 Nuget 3.1](https://docs.microsoft.com/en-us/nuget/release-notes/nuget-3.1#deprecated) 中被否决了。

## 一举多得

要求。NET Standard 2.0 可能是一个苛刻的要求。有了一些[放置得当的`#if`块](https://github.com/subor/nng.NETCore/commit/d9c9f7a92ee1d32f032bd5df94b3215222cf93a9)，就很容易把要求降低到。净标准 1.5。但是，这需要我们的包[支持多目标框架](https://docs.microsoft.com/en-us/nuget/create-packages/supporting-multiple-target-frameworks)。

### 走错了路

创建附加项目，如`nng.NETCore.15.csproj` :

```
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard1.5</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <Compile Include="..\nng.NETCore\ **\*.cs" Exclude="..\nng.NETCore\obj\** \*.cs" />
  </ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

`Exclude`属性是为了避免生成的`AssemblyInfo.cs`会导致:

```
obj/Debug/netstandard1.5/nng.NETCore.15.AssemblyInfo.cs(10,12): error CS0579: Duplicate 'System.Reflection.AssemblyCompanyAttribute' attribute [/Users/jake/projects/nng.NETCore/nng.NETCore.15/nng.NETCore.15.csproj] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们采用这种方法，可能应该[将 AssemblyInfo 元数据移动到项目](https://stackoverflow.com/questions/42138418/equivalent-to-assemblyinfo-in-dotnet-core-csproj)中。

然而，`dotnet pack`不包含输出程序集`bin/$(Configuration)/netstandard1.5/nng.NETCore.dll`——它不是主项目的依赖项。

因此，我们专门为打包创建了一个项目:

```
<PropertyGroup>
    <PackageId>Subor.nng.NETCore</PackageId>
    <!-- SNIP -->
</PropertyGroup>
<ItemGroup>
    <ProjectReference Include="..\nng.Shared\nng.Shared.csproj" />
    <ProjectReference Include="..\nng.Shared.15\nng.Shared.15.csproj" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

但是这导致了“多重定义”的符号:

```
Bus.cs(9,29): error CS0433: The type 'Defines' exists in both 'nng.Shared.15, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' and 'nng.Shared, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' [/XXX/nng.NETCore/nng.NETCore/nng.NETCore.csproj] 
```

Enter fullscreen mode Exit fullscreen mode

NB:我们后来发现了[可能修复这个](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/how-to-have-a-project-reference-without-referencing-the-actual-binary/)的东西。

该去上网了。我们需要一种方法在 nupkg 中包含不相关的程序集。原来这是一个重复出现的痛点:

*   [https://stack overflow . com/questions/44976879/msbuild-multiple-dll-in-a-single-nu get-package](https://stackoverflow.com/questions/44976879/msbuild-multiple-dll-in-a-single-nuget-package)
*   [https://github.com/NuGet/Home/issues/3891](https://github.com/NuGet/Home/issues/3891)
*   [https://medium . com/@ xavierpenya/building-nu get-packages-with-dot net-core-943 F2 fa 2 F4 ca](https://medium.com/@xavierpenya/building-nuget-packages-with-dotnet-core-943f2fa2f4ca)

可用的解决方案归结为:

1.  使用[TargetsForTfmSpecificBuildOutput](https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets#advanced-extension-points-to-create-customized-package)(*yikes*)
2.  使用一个 [nuspec 文件](https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets#packing-using-a-nuspec)
3.  每个依赖项一个`nupkg`(当前推荐)

试图将一个 nuspec 文件添加到我们已有的文件中:

```
<PropertyGroup>
    <PackageId>Subor.nng.NETCore</PackageId>
    <!-- SNIP -->
    <RepositoryUrl>https://github.com/subor/nng.NETCore</RepositoryUrl>
    <!-- Added the below -->
    <NuspecFile>nng.NETCore.nuspec</NuspecFile>
  </PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

`dotnet pack`产生神秘的错误:

```
/XXX/.nuget/packages/nuget.build.packaging/0.2.2/build/NuGet.Build.Packaging.targets(377,3): error : Path cannot be the empty string or all whitespace. [/XXX/nng.NETCore/nng.NETCore/nng.NETCore.csproj] 
```

Enter fullscreen mode Exit fullscreen mode

显然，您不能将 nuspec 文件与 PackageReference 格式混合使用。事实上，无论如何，我们都无法让`dotnet pack`使用 NET Core 2.1.302 的 nuspec。但这无关紧要，因为我们反正找错了对象。

### 正确的方式

。NET 有一个多目标框架机制: [`<TargetFrameworks>`](https://docs.microsoft.com/en-us/dotnet/standard/frameworks#how-to-specify-target-frameworks) (注意“s”)。

将项目更改为:

```
<PropertyGroup>
    <TargetFrameworks>netstandard1.5;netstandard2.0</TargetFrameworks>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

并且`dotnet pack` …产生了一个神秘的错误:

```
/XXX/nng.NETCore/nng.Shared/nng.Shared.csproj : error MSB4057: The target "_GetBuildOutputFilesWithTfm" does not exist in the project. 
```

Enter fullscreen mode Exit fullscreen mode

这让我们困惑了一段时间:

*   谷歌没有找到任何有用的东西
*   已安装。网络核心 1.1
*   `<TargetFramework>netstandard1.5`(无“s”)有效
*   在装有 Visual Studio 2017 的 Windows 10 机器上测试，同样错误
*   一个新的空项目成功了

困惑。原来是自我破坏。`<TargetFrameworks>`被:
打破

```
<PackageReference Include="NuGet.Build.Packaging" Version="0.2.2" /> 
```

Enter fullscreen mode Exit fullscreen mode

不知道为什么要添加它，但是删除它会使`dotnet pack`生成一个针对两个框架的`nupkg`:

```
| ____ lib
| | ____ netstandard2.0
| | | ____ nng.NETCore.dll
| | ____ netstandard1.5
| | | ____ nng.NETCore.dll
| ____ build
| | ____ Subor.nng.NETCore.targets
| ____... 
```

Enter fullscreen mode Exit fullscreen mode

哦…现在不包括项目依赖项`nng.Shared.dll`。显然`NuGet.Build.Packaging`一直在这么做。

现在我们又兜了一圈。需要在一个`nupkg`中使用我们上面概述的解决方案之一的多个组件。具有讽刺意味的是，我们无意中重新发现了一些我已经知道的东西。两个月前，我读了(并评论了)[一篇关于同样事情的帖子](https://dev.to/jamesmh/what-ive-learned-so-far-building-coravel-open-source-net-core-tooling---part-1-1h7k)。

## 项目结构

我们有两个托管程序集:

*   `nng.NETCore.dll`
*   `nng.Shared.dll`

包的消费者需要一个对 **nng 的引用。共享的**然后加载 **nng。NETCore** 在运行时(它随后加载特定于平台的本地库)。

我们将创建两个 nupkgs:

*   [Subor.nng.NETCore.Shared](https://www.nuget.org/packages/Subor.nng.NETCore.Shared/)
    *   包含`lib/netstandard2.0/nng.Shared.dll`(将作为参考添加)
*   [Subor.nng.NETCore](https://www.nuget.org/packages/Subor.nng.NETCore/)
    *   参考资料 **Subor.nng.NETCore.Shared**
    *   `runtimes/any/lib/netstandard2.0/`中的`nng.NETCore.dll`(参见[关于 TFM 等的文献。](https://docs.microsoft.com/en-us/nuget/create-packages/supporting-multiple-target-frameworks#architecture-specific-folders))

在 nng。NETCore.csproj:

```
<PropertyGroup>
    <OutputPath>runtimes\any\lib\</OutputPath>
    <!-- Including assembly as part of runtimes/ so don't want it placed in lib/ -->
    <IncludeBuildOutput>false</IncludeBuildOutput>
  </PropertyGroup>

  <ItemGroup>
    <ProjectReference Include="..\nng.Shared\nng.Shared.csproj">
      <Private>false</Private>
    </ProjectReference>
  </ItemGroup>

  <!-- Must be run after build so output assembly is in runtimes/ -->
  <Target Name="Runtimes" AfterTargets="Build">
    <ItemGroup>
      <Content Include="runtimes\**">
        <PackagePath>runtimes</PackagePath>
        <Visible>false</Visible>
      </Content>
    </ItemGroup>
  </Target>
</Project> 
```

Enter fullscreen mode Exit fullscreen mode

`<OutputPath>`是`runtimes/any/lib/`所以我们的开发路径和打包时是一样的。由于默认情况下`<AppendTargetFrameworkToOutputPath>`和`<AppendRuntimeIdentifierToOutputPath>`处于开启状态，因此`netstandard2.0/`或`netstandard1.5/`会被自动追加。

`<ProjectReference>`到 nng。共享将变成包引用；将 **Subor.nng.NETCore** 包添加到项目中，也会自动添加 **Subor.nng.NETCore.Shared** 。`<Private>false</Private>`确保`nng.Shared.dll`不会被复制到 nng。NETCore 输出；一旦打包，它们就不会存在于同一个文件夹中。

`<Target Name="Runtimes" AfterTargets="Build">`强制`<Content>`在构建之后打包**。没有这个`nng.NETCore.dll`就不会和其余的`runtimes/`一起被复制。**

在`nng.Shared.csproj`中没有做什么特别的事情。

## 多平台多目标

也有可能是目标。NET 框架:

```
<TargetFrameworks>netstandard1.5;netstandard2.0;net461</TargetFrameworks> 
```

Enter fullscreen mode Exit fullscreen mode

在 OSX 上建立这个失败:

```
/usr/local/share/dotnet/sdk/2.1.302/Microsoft.Common.CurrentVersion.targets(1179,5): error MSB3644: The reference assemblies for framework ".NETFramework,Version=v4.6.1" were not found. To resolve this, install the SDK or Targeting Pack for this framework version or retarget your application to a version of the framework for which you have the SDK or Targeting Pack installed. Note that assemblies will be resolved from the Global Assembly Cache (GAC) and will be used in place of reference assemblies. Therefore your assembly may not be correctly targeted for the framework you intend. [/Users/jake/projects/nng.NETCore/nng.NETCore/nng.NETCore.csproj] 
```

Enter fullscreen mode Exit fullscreen mode

可以使用一个条件`PropertyGroup`(来自[跨平台 TargetFrameworks](https://weblog.west-wind.com/posts/2017/Sep/18/Conditional-TargetFrameworks-for-MultiTargeted-NET-SDK-Projects-on-CrossPlatform-Builds) ):

```
<PropertyGroup Condition=" '$(OS)' == 'Windows_NT' ">
    <TargetFrameworks>netstandard1.5;netstandard2.0;net461</TargetFrameworks>
</PropertyGroup>
<PropertyGroup Condition=" '$(OS)' != 'Windows_NT' ">
    <TargetFrameworks>netstandard1.5;netstandard2.0</TargetFrameworks>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

还需要有条件的`PackageReference`因为[系统。Runtime.Loader 在中不可用。NET 框架](https://github.com/dotnet/corefx/issues/22142) :

```
<ItemGroup Condition="'$(TargetFramework)' == 'netstandard1.5' or '$(TargetFramework)' == 'netstandard2.0' ">
      <PackageReference Include="system.runtime.loader" Version="4.3.0" />
  </ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

然后在源文件:

```
#if (NETSTANDARD1_5 || NETSTANDARD1_6 || NETSTANDARD2_0) //...
#endif 
```

Enter fullscreen mode Exit fullscreen mode

用[。NET Standard 2.1 就在眼前](https://blogs.msdn.microsoft.com/dotnet/2018/11/05/announcing-net-standard-2-1/)这变得难以控制。

看起来可以用[属性函数](https://docs.microsoft.com/en-us/visualstudio/msbuild/property-functions)简化。最终从[这个 SO](https://stackoverflow.com/questions/25456161/using-conditional-symbol-inside-csproj) 那里找到了一个像样的解决方案。在项目:

```
<PropertyGroup Condition="'$(TargetFramework)'=='netstandard1.5' or '$(TargetFramework)'=='netstandard1.6' or '$(TargetFramework)'=='netstandard2.0' or '$(TargetFramework)'=='netstandard2.1'">
    <DefineConstants>NNG_NETSTANDARD1_5_AND_UP</DefineConstants>
</PropertyGroup>

<!-- Conditional PackageReference -->
<ItemGroup Condition="$(DefineConstants.Contains('NNG_NETSTANDARD1_5_AND_UP'))">
    <PackageReference Include="system.runtime.loader" Version="4.3.0" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

在 C#中:

```
#if NNG_NETSTANDARD1_5_AND_UP //...
#endif 
```

Enter fullscreen mode Exit fullscreen mode

更容易管理。

## 零零碎碎

杂七杂八的 nupkg 笔记，并不真正保证自己的职位。

### 测试

[关于添加本地包源的微软文档](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package#testing-package-installation)。

清除本地 nuget 缓存:

```
nuget locals all -clear 
```

Enter fullscreen mode Exit fullscreen mode

[这就使得](https://stackoverflow.com/a/44463578/10176713)在一个项目中添加一个包源:

```
<PropertyGroup>
  <RestoreSources>$(RestoreSources);/XXX/projects/multitargettest;https://api.nuget.org/v3/index.json</RestoreSources>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

### 什么时候来**不**多目标

我们一直试图通过从直接包含源代码项目转移到 nuget 包来精简 sln 文件。其中之一是阿帕奇节俭。节俭包含 3 个项目: [2 个目标。NET 框架 3.5 和 4.5](https://github.com/apache/thrift/tree/master/lib/csharp/src) ，以及[一个目标。网标 2.0](https://github.com/apache/thrift/tree/master/lib/netcore/Thrift) 。

我们使用。NET Framework 3.5 库在我们的 Unity 3D SDK 中，并使用。NET Standard 2.0 库放在我们代码库的其余部分(目标是。NET Framework 4.6.1 或。网标 2.0)。

我们创建了一个`nuspec`来打包 3 个不相关的项目，而不触及原来的项目:

```
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2012/06/nuspec.xsd">
  <metadata>
    <!-- ... -->
  </metadata>
  <files>
    <file src="thrift\lib\netcore\src\bin\Release\Thrift.dll" target="lib/netstandard2.0/Thrift.dll" />
    <file src="thrift\lib\csharp\src\bin\Release\Thrift.dll" target="lib/net35/Thrift.dll" />
    <file src="thrift\lib\csharp\src\bin\Release\Thrift45.dll" target="lib/net45/Thrift.dll" />
  </files>
</package> 
```

Enter fullscreen mode Exit fullscreen mode

但是在将 nuget 包添加到一些项目之后，我们遇到了编译错误。在`obj/project.assets.json` :

```
"Subor.Thrift/0.11.0.3":  {  ...  "compile":  {  "lib/net45/Thrift.dll":  {}  },  "runtime":  {  "lib/net45/Thrift.dll":  {}  }  }, 
```

Enter fullscreen mode Exit fullscreen mode

我们的。NET Framework 4.6.1 项目选择使用 4.5 程序集而不是。NET 标准 2.0 程序集。

出于好奇，我们尝试从包中移除 4.5 程序集。当面临 NF 3.5 和 NS 2.0 之间的选择时，。网标胜出。但是，最后我们决定最好还是把。NET 框架和[。NET 标准程序集](https://www.nuget.org/packages/Subor.Thrift.NETCore/)，这样最终用户可以在 NF3.5/NF4.5/NS2.0 之间进行选择。

### 内容文件

在仔细阅读堆栈溢出时，遇到了这个关于“ContentFiles”的问题。

我很感兴趣，因为这是 nupkg 的一个方面，我们还没有使用。找到以下有用的资源:

*   [https://blog . nu get . org/2016 01 26/nu get-content files-demystified . html](https://blog.nuget.org/20160126/nuget-contentFiles-demystified.html)
*   [https://github.com/NuGet/Home/issues/2060](https://github.com/NuGet/Home/issues/2060)
*   [https://github.com/NuGet/Home/issues/1908](https://github.com/NuGet/Home/issues/1908)
*   [https://docs . Microsoft . com/en-us/nu get/reference/nu spec # including-content-files](https://docs.microsoft.com/en-us/nuget/reference/nuspec#including-content-files)