# 在 Unity 中使用 Daydream 控制器的 Gesture C API

> 原文：<https://dev.to/shiena/unity-daydream-gesture-c-api-1pb6>

# 前言

Daydream 有[Gesture C API](https://developers.google.com/vr/reference/android-ndk/group/gesture) ，关于触摸板可以使用以下功能。
这个 API 只提供给 Android NDK，所以可以从 Unity 处理。

*   滑动判定(开始、滑动中、结束、滑动)
*   滑动方向的判定(上下左右)
*   滑动速度( x 轴和 y 轴)
*   滑动移动量( x 轴和 y 轴)
*   长按判定

# 使用方法

**酱**

[从 https://gist.github.com/shiena/d2c bd2 BD 100c c 9e D7 db4B4 DFD 608 feca](https://gist.github.com/shiena/d2cbd2bd100cc9ed7db4b4dfd608feca)中复制两个源并将其放入 Unity 项目。

**使用方法**

```
using UnityEngine;

public class GestureTest
{
    [SerializeField] private GvrGesture gvrGesture;

    void Update()
    {
        var gestures = gvrGesture.GetGesture();
        if (gestures.Count > 0)
        {
            foreach (var g in gestures)
            {
                Debug.Log($"{g.GestureType}, {g.GestureDirection}, {g.Displacement.x}, {g.Displacement.y}, {g.Velocity.x}, {g.Velocity.y}");
            }
        }

        if (gvrGesture.LongPressed())
        {
            Debug.Log("Long pressed");
        }
    }
} 
```

| function | 方法 |
| --- | --- |
| 滑动判定 | GvrGesture::GetGesture() |
| 长按判定 | GvrGesture::LongPressed() |

如果没有这个 API 也能在 Unity 上实现同样的事情的话，那就太方便了。
好不容易才想支持 Unity Package Manager，但是因为无法将 Google VR SDK for Unity 指定为依赖方，所以放弃了。 很遗憾。

# 链接

*   [手势 C API](https://developers.google.com/vr/reference/android-ndk/group/gesture)
*   [https://gist . github . com/shiena/D2 CBD 2 BD 100 cc 9 ed 7 db 4 B4 DFD 608 feca](https://gist.github.com/shiena/d2cbd2bd100cc9ed7db4b4dfd608feca)