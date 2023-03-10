# 从在 Unity 中进入 AAR 的 AndroidManifest.xml 中删除要合并的元素

> 原文：<https://dev.to/shiena/unity-aar-androidmanifest-xml-2f63>

# 前言

在 Unity 中面向 Android 的插件中，有的 AAR 中含有 AndroidManifest.xml，可以通过 manifest merger 进行集成，所以不需要自己添加设置，非常方便。 相反，如果合并所有设置，则即使在不合适的情况下试图删除，[在用 Unity 构建 oculus go/quest APP 时去除不需要的权限](https://qiita.com/shiena/items/88217a3fc76370e77078)在合并 AndroidManifest.xml 之前，因此存在删除对象的节点 因此，使用[多个清单文件的合并| Android Developers](https://developer.android.com/studio/build/manifest-merge?hl=ja) 的`tools:node="remove"`可以删除节点。

# 取消 Oculus 的元数据的回调

以希望在一个项目中构建 GearVR / Oculus Go / Oculus Quest 和非 VR 的 Android APP 为例，尝试创建回调。

**OVRPlugin.aarに含まれているandroidmanifest.xml**

```
<?xml version="1.0" encoding="UTF-8"?>
<manifest android:versionCode="1" android:versionName="1.0.0" package="com.oculus.Integration" xmlns:android="http://schemas.android.com/apk/res/android">
    <uses-sdk android:targetSdkVersion="21"/>
    <application>
        <!-- ■■■■■この行を追加したくない！！！■■■■■ -->
        <meta-data android:value="vr_only" android:name="com.samsung.android.vr.application.mode"/>
    </application>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
</manifest> 
```

**ipostgenerategradleandroidprojectコールバックで書き換え後のAndroidManifest.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.oculus.UnitySample" xmlns:tools="http://schemas.android.com/tools" android:installLocation="auto">
  <supports-screens android:smallScreens="true" android:normalScreens="true" android:largeScreens="true" android:xlargeScreens="true" android:anyDensity="true" />
  <application android:theme="@style/UnityThemeSelector" android:icon="@mipmap/app_icon" android:label="@string/app_name">
    <activity android:name="com.unity3d.player.UnityPlayerActivity" android:label="@string/app_name" android:screenOrientation="landscape" android:launchMode="singleTask" android:configChanges="keyboard|keyboardHidden|navigation|orientation|screenLayout|screenSize|uiMode|density" android:hardwareAccelerated="false" android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen" android:resizeableActivity="false">
      <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
      <meta-data android:name="unityplayer.UnityActivity" android:value="true" />
    </activity>
    <meta-data android:name="unity.build-id" android:value="XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" />
    <meta-data android:name="unity.splash-mode" android:value="0" />
    <meta-data android:name="unity.splash-enable" android:value="False" />
    <meta-data android:name="android.max_aspect" android:value="2.1" />
    <!-- ■■■■■tools:node="remove"を追加したい！！！■■■■■ -->
    <meta-data android:name="com.samsung.android.vr.application.mode" tools:node="remove" />
  </application>
  <uses-feature android:glEsVersion="0x00030001" />
  <uses-feature android:name="android.hardware.opengles.aep" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-feature android:name="android.hardware.touchscreen" android:required="false" />
  <uses-feature android:name="android.hardware.touchscreen.multitouch" android:required="false" />
  <uses-feature android:name="android.hardware.touchscreen.multitouch.distinct" android:required="false" />
</manifest> 
```

添加`tools:node="remove"`的回调

```
#if UNITY_ANDROID
using System.IO;
using System.Text;
using System.Xml;
using UnityEditor;
using UnityEditor.Android;

public class ModifyUnityAndroidAppManifest : IPostGenerateGradleAndroidProject
{
    public void OnPostGenerateGradleAndroidProject(string basePath)
    {
        // VRをサポートしない、またはVRSDKにOculusがない場合にtools:node="remove"を使う
        var isVr = PlayerSettings.GetVirtualRealitySupported(BuildTargetGroup.Android);
        var vrSdks = PlayerSettings.GetVirtualRealitySDKs(BuildTargetGroup.Android);

        var hasOculus = Array.Exists(vrSdks, s => s == "Oculus");
        if (!(isVr && hasOculus))
        {
            var androidManifest = new AndroidManifest(GetManifestPath(basePath));
            androidManifest.RemoveOculusMetaData();
            androidManifest.Save();
        }
    }

    // Package ManagerのOculus (Android)のIPostGenerateGradleAndroidProjectコールバックの後で実行したいので大きめの値にする
    public int callbackOrder => 10;

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
    public readonly string ToolsXmlNamespace = "http://schemas.android.com/tools";

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

    private XmlAttribute CreateToolsAttribute(string key, string value)
    {
        var attr = CreateAttribute("tools", key, ToolsXmlNamespace);
        attr.Value = value;
        return attr;
    }

    // GearVR / Oculus Go / Oculus Quest 向けのmeta-dataノードを削除する
    public void RemoveOculusMetaData()
    {
        const string oculusMetaDataName = "com.samsung.android.vr.application.mode";
        var oculusMetaData = SelectNodes($"/manifest/application/meta-data[@android:name='{oculusMetaDataName}']", nsMgr);
        if (oculusMetaData != null)
        {
            // Package ManagerのOculus (Android)のIPostGenerateGradleAndroidProjectコールバックで追加されるので削除する
            foreach (XmlElement metaData in oculusMetaData)
            {
                ApplicationElement.RemoveChild(metaData);
            }
        }

        // /manifest/application に <meta-data android:name="com.samsung.android.vr.application.mode" tools:node="remove" /> を追加する
        var newMetaData = CreateElement("meta-data");
        newMetaData.Attributes.Append(CreateAndroidAttribute("name", oculusMetaDataName));
        newMetaData.Attributes.Append(CreateToolsAttribute("node", "remove"));
        ApplicationElement.AppendChild(newMetaData);
    }
}
#endif 
```

现在可以删除从 AAR 的 AndroidManifest.xml 中添加的节点。 这个回调可以操作 XML，所以不仅可以删除节点，还可以灵活地处理 intent-filter 的添加和属性的变更等。

# 参考链接

*   [在 Unity 中构建 oculus go/quest APP 时去除不必要的权限](https://qiita.com/shiena/items/88217a3fc76370e77078)
*   [多个清单文件的合并| Android Developers](https://developer.android.com/studio/build/manifest-merge?hl=ja)