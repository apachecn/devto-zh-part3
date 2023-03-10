# 通过 WPF 使用 DirectManipulation

> 原文：<https://dev.to/garuma/using-directmanipulation-with-wpf-247k>

或者换句话说，如何在一个不是 UWP 的传统平台上享受平滑的缩放手势(没有双关语)。

如果你在 Windows 上做过 UI 开发，你可能在某个时候使用过`ScrollViewer`。如果你使用 UWP 版本的`ScrollViewer`，你会注意到，除了滚动事件之外，控件本身也处理缩放。然而，在 WPF，这种能力是完全没有的。

其中的主要原因是 WPF 是在 precision trackpad 和触摸屏都不普及的时代创建的(毕竟触摸是从 Windows 8 开始的)。相反，它的设计考虑到了手写笔支持，并为墨迹识别做了一些初步的迭代。由于 WPF 一直被搁置到最近，它的输入 API 从来没有重新粉刷过，从那以后事情确实发生了很大的变化。

对于 starter，Windows 上的低级输入策略现在有所不同。在 Win32 的保护下，来自触控板、触摸、手写笔和其他设备的输入事件现在都统一在一个单一的`WM_POINTER`事件类型下，而以前它们是分开的。然而，如果应用程序不支持它，操作系统会尝试将这些事件和手势转换成应用程序可能识别的某种等效组合。

对于我们的例子(缩放，更准确地说是捏缩放手势)，兼容性组合是“Ctrl +鼠标滚轮上/下”事件，这是应用程序以前可以实现的标准模式。问题是发送的鼠标滚轮事件只是增加/减少了一个默认值，这意味着你只能实现“锯齿状”缩放行为(至少你想自己制作动画)。这看起来有点像这样:

```
container.PreviewMouseWheel += HandleZoomGesture;

void HandleZoomGesture (object sender, MouseWheelEventArgs e)
{
    // Input emulation will make it look like the Ctrl key is pressed
    // when converting the pinch-to-zoom gesture
    if ((Keyboard.Modifiers & ModifierKeys.Control) == 0)
        return;

    var zoomDelta = ZoomGestureDelta * (((double)e.Delta) / Mouse.MouseWheelDeltaForOneLine);
    SetZoom (currentZoom + zoomDelta);
    e.Handled = true;
} 
```

这种兼容性行为可能适合您的情况，例如，一些浏览器(至少在我写这篇文章时是 Firefox)或 Visual Studio 文本编辑器仍然在实现这种行为。如果你想要更好的东西，请继续读下去。

事实证明，WPF *确实*更新了[的输入子系统](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/mitigation-pointer-based-touch-and-stylus-support)以使用这种新的统一`WM_POINTER`事件类型，但它从未被默认启用，今天仍然位于 appcontext 开关(`Switch.System.Windows.Input.Stylus.EnablePointerSupport`)下。尽管使用了新的事件，更新后的堆栈也愿意过滤掉并非严格来自触摸或手写笔的场景，从而完全绕过了对 precision trackpad 的支持。

还好有出路。可能会允许不基于 UWP 的外部应用程序仍然受益于这种手势支持(又名浏览器)，一个通用的低级 API 确实存在，名为 [DirectManipulation](https://docs.microsoft.com/en-us/windows/desktop/api/_directmanipulation/) 。这个 API 在 HWND 级别上运行，通过让您传递那些`WM_POINTER`消息，然后这些消息在不同的线程上被处理，DirectManipulation 处理所有识别手势和计算要应用的正确转换的繁重工作。

它通常应该与另一个与 Windows compositor 交互的低级 API[direct composition](https://docs.microsoft.com/en-us/windows/desktop/directcomp/directcomposition-concepts)一起使用，但是您仍然可以通过简单地提取您需要的值(也称为内容比例)来手动使它工作。

DirectManipulation API 在框架中并不是现成的，但是你可以使用有绑定的 [SharpDX](https://github.com/sharpdx/SharpDX) 或者一个类似于库的 IDL 上的`tlbimp`的工具。或者你可以把我做的这个绑定交给我[。](https://gist.github.com/garuma/0c25992777c19f5239c73e5b6d66a074)

为了方便起见，这里有一个“最小”的包装类，灵感来自于 [Chrome 的用法](https://github.com/chromium/chromium/blob/master/content/browser/renderer_host/direct_manipulation_win.cc)，它实现并使用了 DirectManipulation 的一个子集，这样你就可以将它插入到你的 WPF 应用程序中(包括对理解`WM_POINTER`事件的支持):

```
using System;
using System.Runtime.InteropServices;
using System.Windows;
using System.Windows.Interop;
using DirectManipulation;

class PointerBasedManipulationHandler : IDirectManipulationViewportEventHandler, IDisposable
{
    const int ContentMatrixSize = 6;
    // Actual values don't matter
    static tagRECT DefaultViewport = new tagRECT { top = 0, left = 0, right = 1000, bottom = 1000 };

    HwndSource hwndSource;
    IDirectManipulationManager manager;
    IDirectManipulationViewport viewport;
    Size viewportSize;

    uint viewportEventHandlerRegistration;
    float lastScale;
    float[] matrix = new float[ContentMatrixSize];
    IntPtr matrixContent;

    float lastTranslationX, lastTranslationY;

    public PointerBasedManipulationHandler ()
    {
        matrixContent = Marshal.AllocCoTaskMem (sizeof (float) * ContentMatrixSize);
    }

    public event Action<float> ScaleUpdated;
    public event Action<float, float> TranslationUpdated;

    public HwndSource HwndSource {
        get => hwndSource;
        set {
            var first = hwndSource == null && value != null;
            var oldHwndSource = hwndSource;
            if (oldHwndSource != null)
                oldHwndSource.RemoveHook (WndProcHook);
            if (value != null)
                value.AddHook (WndProcHook);
            this.hwndSource = value;
            if (first && value != null)
                InitializeDirectManipulation ();
        }
    }

    IntPtr Window => hwndSource.Handle;

    void InitializeDirectManipulation ()
    {
        this.manager = (IDirectManipulationManager)Activator.CreateInstance (typeof (DirectManipulationManagerClass));
        var riid = typeof (IDirectManipulationUpdateManager).GUID;
        riid = typeof (IDirectManipulationViewport).GUID;
        this.viewport = manager.CreateViewport (null, Window, ref riid) as IDirectManipulationViewport;

        var configuration = DIRECTMANIPULATION_CONFIGURATION.DIRECTMANIPULATION_CONFIGURATION_INTERACTION
            | DIRECTMANIPULATION_CONFIGURATION.DIRECTMANIPULATION_CONFIGURATION_SCALING
            | DIRECTMANIPULATION_CONFIGURATION.DIRECTMANIPULATION_CONFIGURATION_TRANSLATION_X
            | DIRECTMANIPULATION_CONFIGURATION.DIRECTMANIPULATION_CONFIGURATION_TRANSLATION_Y
            | DIRECTMANIPULATION_CONFIGURATION.DIRECTMANIPULATION_CONFIGURATION_TRANSLATION_INERTIA;
        viewport.ActivateConfiguration (configuration);
        viewportEventHandlerRegistration = viewport.AddEventHandler (Window, this);
        viewport.SetViewportRect (ref DefaultViewport);
        viewport.Enable ();
    }

    public void Dispose ()
    {
        viewport.RemoveEventHandler (viewportEventHandlerRegistration);
        Marshal.FreeCoTaskMem (matrixContent);
        HwndSource = null;
    }

    public void SetSize (Size size)
    {
        this.viewportSize = size;
        viewport.Stop ();
        var rect = new tagRECT {
            left = 0,
            top = 0,
            right = (int)size.Width,
            bottom = (int)size.Height
        };
        viewport.SetViewportRect (ref rect);
    }

    // Our custom hook to process WM_POINTER event
    IntPtr WndProcHook (IntPtr hwnd, int msg, IntPtr wParam, IntPtr lParam, ref bool handled)
    {
        if (msg == WM_POINTERDOWN || msg == DM_POINTERHITTEST) {
            var pointerID = GetPointerId (wParam);
            var pointerInfo = default (POINTER_INFO);
            if (!GetPointerInfo (pointerID, ref pointerInfo))
                return IntPtr.Zero;
            if (pointerInfo.pointerType != POINTER_INPUT_TYPE.PT_TOUCHPAD &&
                pointerInfo.pointerType != POINTER_INPUT_TYPE.PT_TOUCH)
                return IntPtr.Zero;

            viewport.SetContact (pointerID);
        } else if (msg == WM_SIZE && manager != null) {
            if (wParam == SIZE_MAXHIDE
                || wParam == SIZE_MINIMIZED)
                manager.Deactivate (Window);
            else
                manager.Activate (Window);
        }
        return IntPtr.Zero;
    }

    void ResetViewport (IDirectManipulationViewport viewport)
    {
        viewport.ZoomToRect (0, 0, (float)viewportSize.Width, (float)viewportSize.Height, 0);
        lastScale = 1.0f;
        lastTranslationX = lastTranslationY = 0;
    }

    public void OnViewportStatusChanged ([In, MarshalAs (UnmanagedType.Interface)] IDirectManipulationViewport viewport, [In] DIRECTMANIPULATION_STATUS current, [In] DIRECTMANIPULATION_STATUS previous)
    {
        if (previous == current)
            return;

        if (current == DIRECTMANIPULATION_STATUS.DIRECTMANIPULATION_READY)
            ResetViewport (viewport);
    }

    public void OnViewportUpdated ([In, MarshalAs (UnmanagedType.Interface)] IDirectManipulationViewport viewport)
    {
    }

    public void OnContentUpdated ([In, MarshalAs (UnmanagedType.Interface)] IDirectManipulationViewport viewport, [In, MarshalAs (UnmanagedType.Interface)] IDirectManipulationContent content)
    {
        content.GetContentTransform (matrixContent, ContentMatrixSize);
        Marshal.Copy (matrixContent, matrix, 0, ContentMatrixSize);

        float scale = matrix[0];
        float newX = matrix[4];
        float newY = matrix[5];

        if (scale == 0.0f)
            return;

        var deltaX = (newX - lastTranslationX);
        var deltaY = (newY - lastTranslationY);

        bool ShallowFloatEquals (float f1, float f2)
            => Math.Abs (f2 - f1) < float.Epsilon;

        if ((ShallowFloatEquals (scale, 1.0f) || ShallowFloatEquals (scale, lastScale))
            && (Math.Abs (deltaX) > 1.0f || Math.Abs (deltaY) > 1.0f)) {
            TranslationUpdated?.Invoke (-deltaX, -deltaY);
        } else if (!ShallowFloatEquals (lastScale, scale)) {
            ScaleUpdated?.Invoke (scale);
        }

        lastScale = scale;
        lastTranslationX = newX;
        lastTranslationY = newY;
    }

    #region Native methods
    const int WM_POINTERDOWN = 0x0246;
    const int DM_POINTERHITTEST = 0x0250;
    const int WM_SIZE = 0x0005;

    static readonly IntPtr SIZE_MAXHIDE = new IntPtr (4);
    static readonly IntPtr SIZE_MINIMIZED = new IntPtr (1);

    uint GetPointerId (IntPtr wParam) => (uint)(unchecked((int)wParam.ToInt64 ()) & 0xFFFF);

    [DllImport ("user32.dll", EntryPoint = "GetPointerInfo", SetLastError = true)]
    internal static extern bool GetPointerInfo ([In] uint pointerId, [In, Out] ref POINTER_INFO pointerInfo);

    [StructLayout (LayoutKind.Sequential, CharSet = CharSet.Unicode)]
    internal struct POINTER_INFO
    {
        internal POINTER_INPUT_TYPE pointerType;
        internal uint pointerId;
        internal uint frameId;
        internal POINTER_FLAGS pointerFlags;
        internal IntPtr sourceDevice;
        internal IntPtr hwndTarget;
        internal POINT ptPixelLocation;
        internal POINT ptHimetricLocation;
        internal POINT ptPixelLocationRaw;
        internal POINT ptHimetricLocationRaw;
        internal uint dwTime;
        internal uint historyCount;
        internal int inputData;
        internal uint dwKeyStates;
        internal ulong PerformanceCount;
        internal POINTER_BUTTON_CHANGE_TYPE ButtonChangeType;
    }

    [StructLayout (LayoutKind.Sequential, CharSet = CharSet.Unicode)]
    internal struct POINT
    {
        internal int X;
        internal int Y;
    }

    internal enum POINTER_INPUT_TYPE : uint
    {
        PT_POINTER = 0x00000001,
        PT_TOUCH = 0x00000002,
        PT_PEN = 0x00000003,
        PT_MOUSE = 0x00000004,
        PT_TOUCHPAD = 0x00000005
    }

    [Flags]
    internal enum POINTER_FLAGS : uint
    {
        POINTER_FLAG_NONE = 0x00000000,
        POINTER_FLAG_NEW = 0x00000001,
        POINTER_FLAG_INRANGE = 0x00000002,
        POINTER_FLAG_INCONTACT = 0x00000004,
        POINTER_FLAG_FIRSTBUTTON = 0x00000010,
        POINTER_FLAG_SECONDBUTTON = 0x00000020,
        POINTER_FLAG_THIRDBUTTON = 0x00000040,
        POINTER_FLAG_FOURTHBUTTON = 0x00000080,
        POINTER_FLAG_FIFTHBUTTON = 0x00000100,
        POINTER_FLAG_PRIMARY = 0x00002000,
        POINTER_FLAG_CONFIDENCE = 0x000004000,
        POINTER_FLAG_CANCELED = 0x000008000,
        POINTER_FLAG_DOWN = 0x00010000,
        POINTER_FLAG_UPDATE = 0x00020000,
        POINTER_FLAG_UP = 0x00040000,
        POINTER_FLAG_WHEEL = 0x00080000,
        POINTER_FLAG_HWHEEL = 0x00100000,
        POINTER_FLAG_CAPTURECHANGED = 0x00200000,
        POINTER_FLAG_HASTRANSFORM = 0x00400000,
    }

    internal enum POINTER_BUTTON_CHANGE_TYPE : uint
    {
        POINTER_CHANGE_NONE,
        POINTER_CHANGE_FIRSTBUTTON_DOWN,
        POINTER_CHANGE_FIRSTBUTTON_UP,
        POINTER_CHANGE_SECONDBUTTON_DOWN,
        POINTER_CHANGE_SECONDBUTTON_UP,
        POINTER_CHANGE_THIRDBUTTON_DOWN,
        POINTER_CHANGE_THIRDBUTTON_UP,
        POINTER_CHANGE_FOURTHBUTTON_DOWN,
        POINTER_CHANGE_FOURTHBUTTON_UP,
        POINTER_CHANGE_FIFTHBUTTON_DOWN,
        POINTER_CHANGE_FIFTHBUTTON_UP
    }
    #endregion
} 
```

要使用这个助手，在某个地方实例化它，并把托管可缩放内容的`HwndSource`传递给它。您可以使用 [PresentationSource 来实现。AddSourceChangedHandler](https://docs.microsoft.com/en-us/dotnet/api/system.windows.presentationsource.addsourcechangedhandler?view=netframework-4.7.2) 方法像这样:

```
PointerBasedManipulationHandler manipulationHandler = new PointerBasedManipulationHandler ();

PresentationSource.AddSourceChangedHandler (container, HandleSourceUpdated);

void HandleSourceUpdated (object sender, SourceChangedEventArgs e)
{
    if (manipulationHandler != null && e.NewSource is System.Windows.Interop.HwndSource newHwnd)
        manipulationHandler.HwndSource = newHwnd;
} 
```

最后，订阅处理程序的`ScaleUpdated` / `TranslationUpdated`事件，以对 DirectManipulation 更改做出反应。例如，您可以使用一个 [ScaleTransform](https://docs.microsoft.com/en-us/dotnet/api/system.windows.media.scaletransform?view=netframework-4.7.2) 作为您的容器 [RenderTransform](https://docs.microsoft.com/en-us/dotnet/api/system.windows.uielement.rendertransform?view=netframework-4.7.2) ，并在接收到事件时设置相应的值。另外，不要忘记通过调用`SetSize`方法来设置画布的大小(例如从`SizeChanged`事件连接)。

**现在(在帖子的最后)这里是最后一个重要的警告**。如果您想让任何 DirectManipulation 集成工作，您必须做两件事情之一。由于 DirectManipulation 采用输入委托的方式，您不能让传统的 WPF 手写笔输入堆栈在您的应用程序内部运行(这是默认设置)。因此，您有两个选择:

*   禁用原生手写笔/触摸支持，改为仅依赖 DirectManipulation:在 app.config 中设置`Switch.System.Windows.Input.Stylus.DisableStylusAndTouchSupport=true`开关或以编程方式设置。
*   启用与 DirectManipulation 兼容的基于指针的手写笔/触摸输入堆栈:在 app.config 中设置`Switch.System.Windows.Input.Stylus.EnablePointerSupport=true`开关，或者以编程方式设置(在早期)。