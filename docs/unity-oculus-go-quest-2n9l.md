# 在 Unity 中构建 oculus go/quest APP 时移除不必要的权限

> 原文：<https://dev.to/shiena/unity-oculus-go-quest-2n9l>

# 前言

在 Unity 中制作 oculus go/quest APP 时，如果导入[Oculus Integration](https://assetstore.unity.com/packages/tools/integration/oculus-integration-82022) ，则由于 Oculus Lipsync 使用麦克风，因此在 AndroidManifest.xml 上赋予麦克风权限
不想授予该权限的情况下，有如下删除该脚本的方法。

*   [Unity 生产的 Oculus Go 用 APP 被拒绝“请删除麦克风的权限”时的处理方法【Unity】【Oculus Go】【拒绝】](http://kan-kikuchi.hatenablog.com/entry/Oculus_Microphone_Reject)
*   [在 oculus go APP 下，不允许对麦克风的权限](https://littlewing.hatenablog.com/entry/2018/09/04/170626)

但是也有找不到相应的脚本或者不能很好地删除的情况。 此时，利用在用 Gradle 构建之前回调的`IPostGenerateGradleAndroidProject`，可以从 AndroidManifext.xml 中删除权限。

*   [IPostGenerateGradleAndroidProject](https://docs.unity3d.com/ScriptReference/Android.IPostGenerateGradleAndroidProject.html)
*   [java -在 Unity 中使用自定义清单文件和权限？](https://stackoverflow.com/questions/43293173/use-custom-manifest-file-and-permission-in-unity)
*   [【Unity】解决硬件加速无法生效，即使改写 AndroidManifest 也会失效的问题](https://qiita.com/matsu_shita_ido/items/55e9feca9f79a3570f15)

# 解除麦克风权限的回调

因为可以直接使用 stackoverflow 的脚本，所以稍微更改一下就可以取消麦克风权限。
只对变更的部分写了评论。 如果通过 XPath 和 C#了解 XML 的操作的话，我觉得并不是特别难。

```
#if UNITY_ANDROID
using System.IO;
using System.Text;
using System.Xml;
using UnityEditor.Android;

public class ModifyUnityAndroidAppManifestSample : IPostGenerateGradleAndroidProject
{

    public void OnPostGenerateGradleAndroidProject(string basePath)
    {
        var androidManifest = new AndroidManifest(GetManifestPath(basePath));

        // マイク権限を取り除く
        androidManifest.RemoveMicrophonePermission();

        androidManifest.Save();
    }

    public int callbackOrder => 1;

    private string _manifestFilePath;

    private string GetManifestPath(string basePath)
    {
        if (string.IsNullOrEmpty(_manifestFilePath))
        {
            var pathBuilder = new StringBuilder(basePath);
            pathBuilder.Append(Path.DirectorySeparatorChar).Append("src");
            pathBuilder.Append(Path.DirectorySeparatorChar).Append("main");
            pathBuilder.Append(Path.DirectorySeparatorChar).Append("AndroidManifest.xml");
            _manifestFilePath = pathBuilder.ToString();
        }
        return _manifestFilePath;
    }
}

internal class AndroidXmlDocument : XmlDocument
{
    private string m_Path;
    protected XmlNamespaceManager nsMgr;
    public readonly string AndroidXmlNamespace = "http://schemas.android.com/apk/res/android";

    public AndroidXmlDocument(string path)
    {
        m_Path = path;
        using (var reader = new XmlTextReader(m_Path))
        {
            reader.Read();
            Load(reader);
        }
        nsMgr = new XmlNamespaceManager(NameTable);
        nsMgr.AddNamespace("android", AndroidXmlNamespace);
    }

    public string Save()
    {
        return SaveAs(m_Path);
    }

    public string SaveAs(string path)
    {
        using (var writer = new XmlTextWriter(path, new UTF8Encoding(false)))
        {
            writer.Formatting = Formatting.Indented;
            Save(writer);
        }
        return path;
    }
}

internal class AndroidManifest : AndroidXmlDocument
{
    private readonly XmlElement ApplicationElement;

    public AndroidManifest(string path) : base(path)
    {
        ApplicationElement = SelectSingleNode("/manifest/application") as XmlElement;
    }

    private XmlAttribute CreateAndroidAttribute(string key, string value)
    {
        var attr = CreateAttribute("android", key, AndroidXmlNamespace);
        attr.Value = value;
        return attr;
    }

    // マイク権限を取り除く
    public void RemoveMicrophonePermission()
    {
        var microphones = new[]
        {
            "//uses-permission[@android:name='android.permission.RECORD_AUDIO']",
            "//uses-feature[@android:name='android.hardware.microphone']"
        };
        var manifest = SelectSingleNode("/manifest");
        if (manifest == null)
        {
            return;
        }

        foreach (var microphone in microphones)
        {
            var node = manifest.SelectSingleNode(microphone, nsMgr);
            if (node != null)
            {
                manifest.RemoveChild(node);
            }
        }
    }
}
#endif 
```

这样就可以删除 Unity 自动授予的权限了。 我想麦克风权限也从构建 APP 生成的`[ProjectFolder]/Temp/gradleOut/src/main/AndroidManifext.xml`中被删除了。

但是从进入 AAR 的 AndroidManifest.xml 中合并的节点等，需要下功夫才能删除，所以想写在别的报道中。

# 参考链接

*   [Unity 生产的 Oculus Go 用 APP 被拒绝“请删除麦克风的权限”时的处理方法【Unity】【Oculus Go】【拒绝】](http://kan-kikuchi.hatenablog.com/entry/Oculus_Microphone_Reject)
*   [在 oculus go APP 下，不允许对麦克风的权限](https://littlewing.hatenablog.com/entry/2018/09/04/170626)
*   [IPostGenerateGradleAndroidProject](https://docs.unity3d.com/ScriptReference/Android.IPostGenerateGradleAndroidProject.html)
*   [java -在 Unity 中使用自定义清单文件和权限？](https://stackoverflow.com/questions/43293173/use-custom-manifest-file-and-permission-in-unity)
*   [【Unity】解决硬件加速无法生效，即使改写 AndroidManifest 也会失效的问题](https://qiita.com/matsu_shita_ido/items/55e9feca9f79a3570f15)