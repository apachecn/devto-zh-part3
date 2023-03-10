# 在 Unity 中打开项目时加载与 Build Settings 相同的场景

> 原文：<https://dev.to/shiena/unity-build-settings-1b59>

# 前言

在多场景中制作的时候，每次打开项目时都很麻烦在 Hierarychy 中添加场景，所以我一口气打开了和 Build Settings 一样的场景。

# 脚本

在项目中创建`Editor`文件夹，只需配置以下脚本即可。
在 Unity2018.3.6f1 中确认了动作。

```
// MIT License
// 
// Copyright (c) 2019 shiena
// 
// Permission is hereby granted, free of charge, to any person obtaining a copy
// of this software and associated documentation files (the "Software"), to deal
// in the Software without restriction, including without limitation the rights
// to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
// copies of the Software, and to permit persons to whom the Software is
// furnished to do so, subject to the following conditions:
// 
// The above copyright notice and this permission notice shall be included in all
// copies or substantial portions of the Software.
// 
// THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
// IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
// FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
// AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
// LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
// OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
// SOFTWARE.

using System.IO;
using System.Reflection;
using UnityEditor;
using UnityEditor.SceneManagement;
using UnityEngine.Events;

namespace Editor
{
    public static class EditorApplicationUtility
    {
        private const BindingFlags BINDING_ATTR = BindingFlags.Static | BindingFlags.Instance | BindingFlags.NonPublic;

        private static readonly FieldInfo m_info = typeof(EditorApplication).GetField("projectWasLoaded", BINDING_ATTR);

        public static UnityAction projectWasLoaded
        {
            get { return m_info.GetValue(null) as UnityAction; }
            set
            {
                var functions = m_info.GetValue(null) as UnityAction;
                functions += value;
                m_info.SetValue(null, functions);
            }
        }
    }

    public static class BuildSettingsSceneLoader
    {
        [InitializeOnLoadMethod]
        private static void LoadBuildSettingsScenes()
        {
            EditorApplicationUtility.projectWasLoaded += () =>
            {
                var isFirstScene = true;
                foreach (var scene in EditorBuildSettings.scenes)
                {
                    if (!(scene.enabled && File.Exists(scene.path)))
                    {
                        continue;
                    }

                    if (isFirstScene)
                    {
                        EditorSceneManager.OpenScene(scene.path, OpenSceneMode.Single);

                        isFirstScene = false;
                    }
                    else
                    {
                        EditorSceneManager.OpenScene(scene.path, OpenSceneMode.Additive);
                    }
                }
            };
        }
    }
} 
```

# explanation

虽然没有做什么，但是 buitializeonloadmethodattribute 与 editor application.projectwasloaded 结合在打开项目时运行

# 参考链接

*   Unity 文档
    *   [InitializeOnLoadMethodAttribute](https://docs.unity3d.com/ScriptReference/InitializeOnLoadMethodAttribute.html)
    *   [editorbuildsettings . scenes](https://docs.unity3d.com/ScriptReference/EditorBuildSettings-scenes.html)
    *   [EditorSceneManager](https://docs.unity3d.com/ScriptReference/SceneManagement.EditorSceneManager.html)
*   [【Unity】汇总介绍 40 个可以在编辑器扩展中使用的回调★读取 Unity 项目时](http://baba-s.hatenablog.com/entry/2017/12/04/090000#Unity-%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%82%93%E3%81%A0%E6%99%82)