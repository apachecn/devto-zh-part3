# 在 UWP 导航到某个页面时，如何获取该页面的实例

> 原文：<https://dev.to/mhmd_azeez/how-to-get-an-instance-of-a-page-when-you-navigate-to-it-in-uwp-1ica>

默认情况下，当导航一个框架到一个页面时，UWP 不会给你一个页面实例。所以传递参数的唯一方式是使用`parameter`参数。但是拥有一个页面实例还有很多其他好处:例如，它允许子页面拥有自定义事件，父页面可以订阅这些事件。

然而,`Frame`有一个名为`Navigated`的事件，它可以帮助我们编写一个扩展方法来获得对实际实例的引用:

```
using Windows.UI.Xaml.Media.Animation;
using Windows.UI.Xaml.Navigation;

namespace Windows.UI.Xaml.Controls
{
    public static class FrameExtensions
    {
        /// <summary>
        /// Navigates to a page and returns the instance of the page if it succeeded,
        /// otherwise returns null.
        /// </summary>
        /// <typeparam name="TPage"></typeparam>
        /// <param name="frame"></param>
        /// <param name="transitionInfo">The navigation transition.
        /// Example: <see cref="DrillInNavigationTransitionInfo"/> or
        /// <see cref="SlideNavigationTransitionInfo"/></param>
        /// <returns></returns>
        public static TPage Navigate<TPage>(
            this Frame frame,
            NavigationTransitionInfo transitionInfo = null)
            where TPage : Page
        {
            TPage view = null;
            void OnNavigated(object s, NavigationEventArgs args)
            {
                frame.Navigated -= OnNavigated;
                view = args.Content as TPage;
            }

            frame.Navigated += OnNavigated;

            frame.Navigate(typeof(TPage), null, transitionInfo);
            return view;
        }
    }
} 
```

假设我们有一个名为`Page1` :
的页面

```
public sealed partial class Page1 : Page
{
    public Page1()
    {
        this.InitializeComponent();
    }

    public void Init(string param1, int param2)
    {
        // Initialize the page
    }
} 
```

您可以像这样导航到它:

```
Page1 page = frame.Navigate<Page1>();
// You can now do stuff with the page
// for example pass in as many parameters as you want
page.Init("Hello World", 42); 
```

**注意:** `Page1.Init`会在`Page1.OnNavigatedTo`之后、`Page1.Loading`之前被调用。