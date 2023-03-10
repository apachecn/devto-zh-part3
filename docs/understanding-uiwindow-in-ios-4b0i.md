# 了解 iOS 中的 UIWindow

> 原文：<https://dev.to/onmyway133/understanding-uiwindow-in-ios-4b0i>

在本文中，我将分享我对 UIWindow 的了解

## [键窗](#keywindow)

一个应用程序可以有许多 UIWindow。键窗口是被指定来接收键盘和其他非触摸相关事件的窗口。一次只能有一个窗口是关键窗口。

调用 makeKeyAndVisible 或 makeKeyWindow 使 UIWindow 成为 KeyWindow。请注意，UIWindow 在默认情况下是隐藏的，因此 makeKeyAndVisible 使 UIWindow 成为 keyWindow，并将其 hidden 属性设置为 NO

## UIWindow 始终是纵向的

只需在应用程序中添加以下代码:difinishlaunchingwithoptions:

```
UIView *view = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
    view.backgroundColor = [UIColor greenColor];
    view.autoresizingMask = UIViewAutoresizingFlexibleRightMargin | UIViewAutoresizingFlexibleBottomMargin;
    [self.window addSubview:view];
    view.layer.zPosition = FLT_MAX; 
```

然后旋转设备/模拟器，你会看到绿色视图总是在位置{0，0}，

[![Screen Shot 2014-08-31 at 8.32.39 PM](img/f7610701c6f8b71e3ef15b2bf96bea28.png)](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-31-at-8.32.39-PM.png)
[![Screen Shot 2014-08-31 at 8.32.43 PM](img/4b5ec82a82a20d4cc873e4ac19eaa11e.png)T6】](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-31-at-8.32.43-PM.png)

UIWindow 的坐标系总是纵向的。它通过设置其 rootViewController 的视图的变换来应用旋转。所以从 iOS 4 开始，建议应用要有 rootViewController。看到 rob [在旋转 UIView 坐标被交换但 UIWindow 的没有被交换后的精彩回答了吗？](http://stackoverflow.com/a/8598515/1418457)

## 键盘是一个窗口

请注意，UIApplication 有一个实例方法“windows”

> 应用程序的可见和隐藏窗口。(只读)
> 此属性包含当前与应用程序关联的 UIWindow 对象。此列表不包括由系统创建和管理的窗口，例如用于显示状态栏的窗口

你可以通过遍历这个窗口数组得到键盘窗口。从 SVProgressHUD 中提取的代码。注意，当显示键盘时，窗口数组包含另一个 UITextEffectsWindow 类型的窗口，即键盘

```
- (CGFloat)visibleKeyboardHeight {

    UIWindow *keyboardWindow = nil;
    for (UIWindow *testWindow in [[UIApplication sharedApplication] windows]) {
        if(![[testWindow class] isEqual:[UIWindow class]]) {
            keyboardWindow = testWindow;
            break;
        }
    }

    for (__strong UIView *possibleKeyboard in [keyboardWindow subviews]) {
        if([possibleKeyboard isKindOfClass:NSClassFromString(@"UIPeripheralHostView")] || [possibleKeyboard isKindOfClass:NSClassFromString(@"UIKeyboard")])
            return possibleKeyboard.bounds.size.height;
    }

    return 0;
} 
```

如前所述，UIWindow 的坐标系总是纵向的。键盘是一个窗口，所以它的框架总是(0 0；320×480 ),而不管设备方向如何。旋转设备时，系统会对键盘窗口应用旋转变换

用户将设备旋转至纵向

```
<UITextEffectsWindow: 0x8e3ecc0; frame = (0 0; 320 480); opaque = NO; gestureRecognizers = <NSArray: 0x8e3f240>; layer = <UIWindowLayer: 0x8e3ee40>> 
```

用户将设备旋转至横向

```
<UITextEffectsWindow: 0x8e3ecc0; frame = (0 0; 320 480); transform = [0, 1, -1, 0, -80, 80]; opaque = NO; gestureRecognizers = <NSArray: 0x8e3f240>; layer = <UIWindowLayer: 0x8e3ee40>> 
```

## 通知

如果你注册了 UIKeyboardWillShowNotification，就可以确认这个

```
[[NSNotificationCenter defaultCenter] addObserverForName:UIKeyboardWillShowNotification object:nil queue:nil usingBlock:^(NSNotification *note) {
        NSLog(@"%@", note);
    }]; 
```

用户将设备旋转到纵向

UIKeyboardFrameEndUserInfoKey = " ns rect:{ { 0，264}，{320，216 } } "；

用户将设备旋转为横向

UIKeyboardFrameEndUserInfoKey = " ns rect:{ { 0，0}，{162，480 } } "；

因此，在您的 UIKeyboardWillShowNotification 处理程序中，您应该将其转换为视图坐标系。从[键盘提取的代码“WillShow”和“WillHide”与旋转](http://stackoverflow.com/a/14351009/1418457)

```
- (void) keyboardWillShow:(NSNotification *)aNotification
{
     CGRect keyboardFrame = [[[aNotification userInfo] objectForKey:UIKeyboardFrameBeginUserInfoKey] CGRectValue];
    CGRect convertedFrame = [self.view convertRect:keyboardFrame fromView:self.view.window];

    ......
    /* Do whatever you want now with the new frame.
     * The width and height will actually be correct now
     */
    ......
} 
```

## 状态栏是一个窗口

如前所述，窗口数组不包含状态栏窗口。Statusbar 窗口属于 UIStatusBarWindow 类型，你可以用下面的代码(从 FLEX 中提取)
得到它

```
- (UIWindow *)statusWindow
{
    NSString *statusBarString = [NSString stringWithFormat:@"_statusBarWindow"];
    return [[UIApplication sharedApplication] valueForKey:statusBarString];
} 
```

和键盘窗口一样，状态栏框架是不变的。旋转设备时，系统会对状态栏应用旋转变换

用户将设备旋转至纵向

```
<UIStatusBarWindow: 0x8f61e30; frame = (0 0; 320 480); gestureRecognizers = <NSArray: 0x8f62e40>; layer = <UIWindowLayer: 0x8f620d0>> 
```

用户将设备旋转至横向

```
<UIStatusBarWindow: 0x8f61e30; frame = (0 0; 320 480); transform = [0, 1, -1, 0, 0, 0]; gestureRecognizers = <NSArray: 0x8f62e40>; layer = <UIWindowLayer: 0x8f620d0>> 
```

## 状态栏

UIApplication 有一个属性叫 statusBarFrame，总是在人像坐标系中报告。

```
CGRect rect = [UIApplication sharedApplication].statusBarFrame;
NSLog(@"statusBarFrame %@", NSStringFromCGRect(rect)); 
```

用户将设备旋转到纵向

状态栏{{0，0}，{320，20}}

用户将设备旋转至横向
状态条框{{300，0}，{20，480}}

如果你只想得到状态高度，不管方向如何，你可以只得到最大值

```
float statusBarHeight = MIN([UIApplication sharedApplication].statusBarFrame.size.height, [UIApplication sharedApplication].statusBarFrame.size.width); 
```

## 通知

旋转设备时，系统会发出 UIApplicationWillChangeStatusBarFrameNotification 和 UIApplicationDidChangeStatusBarFrameNotification。

现在让我们处理 UIApplicationDidChangeStatusBarFrameNotification 来查看

```
[[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationWillChangeStatusBarFrameNotification object:nil queue:nil usingBlock:^(NSNotification *note) {
        NSLog(@"%@", note);
    }];

    [[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationDidChangeStatusBarFrameNotification object:nil queue:nil usingBlock:^(NSNotification *note) {
        NSLog(@"%@", note);
    }]; 
```

我不知道为什么 WillChange 通知给我最终结果，而 DidChange 通知给我旧的结果。我说的结果是指 statusBarFrame
的值。我还看到没有库使用 DidChange 通知

## 如何创建预警视图

要创建 alertview，只需提供一个 UIViewController(较小的尺寸)。对于高级案例，您必须了解演示上下文。使用 alertview 的方法有点困难。我们一直想要的是一个简单的电话

```
[alertView show]; 
```

这并不是说某些视图可以出现在你的警告视图之上，所以它们违背了你作为警告视图的目的

奇特的方法是使用 UIWindow。我看到许多图书馆使用这两种方法中的一种

## 不使用 rootViewController 方法

一些库像 OLGhostAlertView，SVProgressHUD，WYPopoverController，MTStatusBarOverlay...不要使用 rootViewController。他们可以创建新的 UIWindow (MTStatusBarOverlay)或使用现有的 UIWindow，他们将子视图直接添加到窗口中，因此他们通过监听 UIApplicationDidChangeStatusBarOrientationNotification 或 UIApplicationWillChangeStatusBarFrameNotification 来处理方向

处理程序是这样的，提取自 SVProgressHUD。想法是手动获取方向，并对他们的视图应用旋转变换

```
- (void)handleOrientationChange:(NSNotification *)note
{
    UIInterfaceOrientation orientation = [[UIApplication sharedApplication] statusBarOrientation];

    CGRect orientationFrame = [UIScreen mainScreen].bounds;
    CGRect statusBarFrame = [UIApplication sharedApplication].statusBarFrame;

    if(UIInterfaceOrientationIsLandscape(orientation)) {
        float temp = orientationFrame.size.width;
        orientationFrame.size.width = orientationFrame.size.height;
        orientationFrame.size.height = temp;

        temp = statusBarFrame.size.width;
        statusBarFrame.size.width = statusBarFrame.size.height;
        statusBarFrame.size.height = temp;
    }

    switch (orientation) {
        case UIInterfaceOrientationPortraitUpsideDown:
            rotateAngle = M_PI;

            break;
        case UIInterfaceOrientationLandscapeLeft:
            rotateAngle = -M_PI/2.0f;

            break;
        case UIInterfaceOrientationLandscapeRight:
            rotateAngle = M_PI/2.0f;

            break;
        default: // as UIInterfaceOrientationPortrait
            rotateAngle = 0.0;

            break;
    }

} 
```

## 如何给现有窗口添加视图

图书馆有自己的策略来添加视图到现有的窗口

SVProgressHUD 尝试添加到前窗口

```
if(!self.overlayView.superview){
        NSEnumerator *frontToBackWindows = [[[UIApplication sharedApplication]windows]reverseObjectEnumerator];

        for (UIWindow *window in frontToBackWindows)
            if (window.windowLevel == UIWindowLevelNormal) {
                [window addSubview:self.overlayView];
                break;
            }
    } 
```

## 操作系统如何创建 AlertView

观看 WWDC 2014 年第 228 场，看看演示控制器内部，他们说

> 在幕后，框架代表你的应用程序创建一个窗口，但这是在 iOS 8 窗口旋转行为之前，所以这个窗口在技术上仍然是纵向的。

然后，我们将 action sheet 添加到该窗口中，并模仿当前视图的转换层次结构，以获得正确的方向。

## 使用 rootViewController 方法

有些库，像 FLEX，SIAlertView，...创建新的 UIWindow 并将 rootViewController 分配给该窗口。通过这种方式，方向被报告给 rootViewController。然后，他们只需将视图添加到 rootViewController.view，并在视图控制器中处理方向。

从 flexviewrexplorerviewcontroller 中提取的代码

```
- (NSUInteger)supportedInterfaceOrientations
{
    UIViewController *viewControllerToAsk = [self viewControllerForStatusBarAndOrientationProperties];
    NSUInteger supportedOrientations = [FLEXUtility infoPlistSupportedInterfaceOrientationsMask];
    if (viewControllerToAsk && viewControllerToAsk != self) {
        supportedOrientations = [viewControllerToAsk supportedInterfaceOrientations];
    }

    // The UIViewController docs state that this method must not return zero.
    // If we weren't able to get a valid value for the supported interface orientations, default to all supported.
    if (supportedOrientations == 0) {
        supportedOrientations = UIInterfaceOrientationMaskAll;
    }

    return supportedOrientations;
}

- (BOOL)shouldAutorotate
{
    UIViewController *viewControllerToAsk = [self viewControllerForStatusBarAndOrientationProperties];
    BOOL shouldAutorotate = YES;
    if (viewControllerToAsk && viewControllerToAsk != self) {
        shouldAutorotate = [viewControllerToAsk shouldAutorotate];
    }
    return shouldAutorotate;
}

- (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
{
    [...]
}

- (void)didRotateFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation
{
    [...]
} 
```

由于 FLEX 创建了新窗口，它必须参考应用程序的原始窗口来获得旋转功能。请注意 infoplistsupportedinterface orientationmask 和 viewControllerForStatusBarAndOrientationProperties，您可以从中了解很多信息

## UIWindow is a UIView

因为 UIWindow 是 UIView 的子类，所以你可以用 UIView 做很多你已经知道的事情。例如，FLEX 覆盖 pointInside:withEvent:通过其工具栏
拦截触摸

```
- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event
{
    BOOL pointInside = NO;
    if ([self.eventDelegate shouldHandleTouchAtPoint:point]) {
        pointInside = [super pointInside:point withEvent:event];
    }
    return pointInside;
} 
```

## iOS 8 中的  UIWindow

在 iOS 8 中，苹果引入了大小分类，“旋转是一种动画边界变化”。并且 UIWindow 在设备方向中报告

WWDC 2014 第 216 届会议用 UIKit 构建自适应应用

> Trait 环境是一个新的协议，能够返回它们当前的 Trait 集合，包括屏幕、窗口、视图控制器以及视图。

所有这些都能够将它们当前的特征集合返回给你，用于决定你的界面应该如何布局。

参见[IOs 8 中[ui screen main screen]. bounds . size 是否变得依赖于方向？](http://stackoverflow.com/questions/24150359/is-uiscreen-mainscreen-bounds-size-becoming-orientation-dependent-in-ios8/24153540#24153540)

UIScreen 现在面向界面:

*   [UIScreen bounds]现在面向界面
*   现在面向界面
*   状态栏框架通知是面向界面的
*   键盘框架通知是面向界面的

## 阅读更多

1.  [多个用户界面窗口](http://b2cloud.com.au/tutorial/multiple-uiwindows/)
2.  [在主窗口上创建新的 ui window](http://stackoverflow.com/questions/19995526/to-create-a-new-uiwindow-over-the-main-window)
3.  [在一个 iOS 应用中再增加一个 UIWindow 的优点、问题、例子？](http://stackoverflow.com/questions/8232398/advantages-problems-examples-of-adding-another-uiwindow-to-an-ios-app)
4.  [ui window 和 UIView 的区别](http://stackoverflow.com/questions/8233976/the-difference-between-a-uiwindow-and-a-uiview)
5.  [在 iOS 应用中使用多个 ui 窗口](http://shaune.com.au/using-multiple-uiwindows-in-ios-applications/)
6.  技术问题& A QA1688 为什么我的 UIViewController 不能随设备一起旋转？
7.  [旋转后，UIView 坐标交换，但 UIWindow 坐标不交换？](http://stackoverflow.com/questions/8598315/after-rotation-uiview-coordinates-are-swapped-but-uiwindows-are-not)
8.  [WWDC 2014 年第 214 场查看 iOS 8 中控制器的进步](https://developer.apple.com/videos/wwdc/2014/)

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️