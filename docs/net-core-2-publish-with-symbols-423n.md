# 。NET Core 2 用符号发布

> 原文：<https://dev.to/akashkava/net-core-2-publish-with-symbols-423n>

# 缺少 pdb 文件

默认的点网发布行为忽略了所有的 pdb 文件，让你搞不清楚异常发生在哪里。我从来不喜欢在产品中省略 pdb，如果没有源代码，一个行号会做什么？

# 骇客

真正肮脏的黑客，发现后 2 小时挖掘 github 的问题。

# 代码

编辑您的 csproj 文件，并添加以下行。

```
 <Target Name="_ResolvePublishNuGetPackagePdbsAndXml" AfterTargets="RunResolvePublishAssemblies">
    <ItemGroup>
      <ResolvedFileToPublish Include="@(ResolvedAssembliesToPublish->'%(RootDir)%(Directory)%(Filename).pdb')" RelativePath="$([System.IO.Path]::ChangeExtension(%(ResolvedAssembliesToPublish.DestinationSubPath), '.pdb'))" DestinationSubPath="$([System.IO.Path]::ChangeExtension(%(ResolvedAssembliesToPublish.DestinationSubPath), '.pdb'))" Condition="'%(ResolvedAssembliesToPublish.PackageName)' != ''&#xD;&#xA;                    and Exists('%(RootDir)%(Directory)%(Filename).pdb')" />
      <ResolvedFileToPublish Include="@(ResolvedAssembliesToPublish->'%(RootDir)%(Directory)%(Filename).xml')" RelativePath="$([System.IO.Path]::ChangeExtension(%(ResolvedAssembliesToPublish.DestinationSubPath), '.xml'))" DestinationSubPath="$([System.IO.Path]::ChangeExtension(%(ResolvedAssembliesToPublish.DestinationSubPath), '.xml'))" Condition="'%(ResolvedAssembliesToPublish.PackageName)' != ''&#xD;&#xA;                    and Exists('%(RootDir)%(Directory)%(Filename).xml')" />
    </ItemGroup>
  </Target> 
```

Enter fullscreen mode Exit fullscreen mode

# 警告

它会发生完全的变化。网芯 3。当它发货时，我会在这里发布一个更新。