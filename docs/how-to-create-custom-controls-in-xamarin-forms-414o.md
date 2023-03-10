# 如何在 Xamarin 中创建自定义控件？形式

> 原文：<https://dev.to/syncfusion/how-to-create-custom-controls-in-xamarin-forms-414o>

Xamarin。Forms 允许最终用户使用一个共享的 C#代码库在 Android、iOS 和 UWP 平台上创建本地用户界面。它在每个本机平台上提供了大量定制选项，因为 UI 是使用不同的本机控件**呈现器**类来呈现的。

Xamarin 内置了 40 多个页面、布局和控件。表单库，在 Android、iOS 和 UWP 上构建原生移动应用时，你几乎可以做任何事情。Xamarin 最大的优点是。表单是完全定制每个内置控件的能力。除此之外，您还可以使用特定于平台的 API 和自定义控件。您可以在 Xamarin 中重用您的自定义本机控件。使用**自定义渲染器**概念形成应用程序。

创建自定义控件有两种方法:

*   使用内置的布局和控件。
*   在 PCL/中使用伪包装类。NET 标准项目，具有可绑定的属性或在每个平台的渲染器中。

## 简单列表视图

在这篇博文中，我们将使用第一种方法创建自定义控件。Xamarin 中有许多自定义控件。然而，我最喜欢的是 ListView，我们使用的大多数应用程序都需要它。

如果您可以创建自己的 ListView，只用几行代码就可以呈现任何数据，那会怎么样？如果您可以提供选项来定制加载到其中的视图，而不需要太多的编码，会怎么样？这正是我们在这篇博文中将要看到的:一个简单的 ListView，像在手机中一样显示联系信息。

要创建 ListView，首先我们需要编写一个包含控件属性的类。任何 ListView 最棒的地方在于，当我们滚动时，它可以重用条目。换句话说，它只创建子对象，并在我们滚动时重新排列离开视口的项目。这里也采用了这种逻辑，因此我们需要一个滚动容器来控制这种逻辑流。

## 在列表视图中显示联系人信息

ListView 是一种数据绑定控件。因此，您必须创建一个数据模型来绑定到控件。

我们将创建一个简单的数据源，并将其绑定到我们的 ListView。在这种情况下，简单的“一些随机用户的联系方式细节”已经被建模，生成包含用户名、用户图像、呼叫时间和联系号码的随机联系方式细节。在现实世界中，您还可以将服务和数据库中的数据绑定到 ListView 控件中。

```
public class ContactInfo
{
    public string ContactName { get; set; }
    public string CallTime { get; set; }
    public string ContactImage { get; set; }
    public long ContactNumber { get; set; }
    public Color ContactImageColor { get; set; }
} 
```

*具有属性的模型类*

```
public class ContactsViewModel
{
    public ObservableCollection Contacts { get; set; }

    public ContactsViewModel()
    {
        GenerateContactDetails();
        Contacts = new ObservableCollection();

        foreach (var cusName in CustomerNames)
        {
            ContactInfo contact = new ContactInfo();
            ...
            Contacts.Add(contact);
        }
    }

    private void GenerateContactDetails()
    {
        ContactNumbers.Add("Kyle", 23564582);
        ContactNumbers.Add("Gina", 77656453);
        ...
        ContactNumbers.Add("Liam", 28726937);
        ContactNumbers.Add("Jacob", 39283738);
    }
} 
```

*填充模型集合*

## 实现逻辑

ListView 类包含定制定制 ListView 外观所需的属性和对**滚动**事件的订阅。自定义 ListView 中的属性有:

*   DataSource 绑定任何要呈现的集合。
*   item template—自定义需要在内部加载的视图。
*   ItemHeight 根据 ItemTemplate 中加载的视图自定义项目的高度。

下面的代码演示了我们的 **SimpleListView** 的具体实现，它包含一个自定义视图 **ScrollController** 作为它的内容。

```
public class SimpleListView : ScrollView, IDisposable 
{
    private ScrollController controller;
    public int ItemHeight { get; set; }
    public DataTemplate ItemTemplate { get; set; }

    public static readonly BindableProperty DataSourceProperty =
        BindableProperty.Create("DataSource", typeof(IList), typeof(SimpleListView), null, BindingMode.TwoWay);

    public IList DataSource
    {
        get { return (IList)GetValue(DataSourceProperty); }
        set { this.SetValue(DataSourceProperty, value); }
    }

    public SimpleListView()
    {
        this.Orientation = ScrollOrientation.Vertical;
        this.ItemHeight = 60;
        this.controller = new ScrollController() { ListView = this };
        this.Scrolled += this.controller.ListView_Scrolled;
        this.Content = this.controller;
    }

    public void Dispose()
    {
        this.Scrolled -= this.controller.ListView_Scrolled;
    }
} 
```

### 在 ListView 中加载初始视图

首先，我们需要创建适合视图区域的视图。这可以在 ScrollController 类的 OnMeasure 重写中完成。通过将 ListView 的高度除以项目高度，获得适合视图的项目数。在列表中维护创建的视图及其索引和 BindingContext 信息。

```
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
    if (isFirstTime)
        CreateView((int)Math.Ceiling(ListView.Height / this.ListView.ItemHeight));

    var totalheight = this.ListView.DataSource.Count * this.ListView.ItemHeight;
    return (new SizeRequest(new Size(widthConstraint, totalheight)));
}

public void CreateView(int count)
{
    for (int i = 0; i < count; i++)
    {
        ItemView template = new ItemView
        {
            Content = (View)this.ListView.ItemTemplate.CreateContent(),
            BindingContext = this.ListView.DataSource[i],
            IsEnsured = false,
            ItemIndex = -1,
        };
        viewItems.Add(template);
        this.Children.Add(template);
    }
    isFirstTime = false;
} 
```

列表中维护的已创建视图可以基于 LayoutChildren 覆盖中的索引按顺序排列。

```
protected override void LayoutChildren(double x, double y, double width, double height)
{
    double yPosition;
    foreach (var item in viewItems)
    {
        yPosition = item.ItemIndex * this.ListView.ItemHeight;
        item.Layout(new Rectangle(x, yPosition, width, this.ListView.ItemHeight));
    }
} 
```

### 滚动时重用列表视图中的项目

简单地说，只要它不在视图中，就选择它。则该项目的索引和数据被修改并基于滚动方向重新排列在列表的顶部或底部。

我们需要维护一个属性来存储偏移量值，并在滚动偏移量改变时调用 OnSizeAllocated 方法来刷新视图。

下面阐释了重用 ListView 控件中的项的代码逻辑。

```
internal void ListView_Scrolled(object sender, ScrolledEventArgs e)
{
    this.ScrollY = e.ScrollY;
}

internal double ScrollY
{
    get { return this.scrollY; }
    set
    {
        if (this.scrollY != value)
        {
            this.scrollY = value;
            this.OnSizeAllocated(this.Width, this.Height);
        }
    }
}

protected override void OnSizeAllocated(double width, double height)
{
    var start = (int)Math.Floor(ScrollY / this.ListView.ItemHeight);
    int noOfItems = (int)Math.Ceiling(ListView.Height / this.ListView.ItemHeight);

    foreach (var item in viewItems)
        item.IsEnsured = false;

    for (int i = start; i < start + noOfItems; i++) { var row = this.viewItems.FirstOrDefault(x => x.ItemIndex == i);
        if (row == null)
        {
            row = this.viewItems.FirstOrDefault(x => ((x.ItemIndex < start || x.ItemIndex > (start + noOfItems - 1)) && !x.IsEnsured));
            if (row == null)
            {
                CreateView(1);
                row = this.viewItems.FirstOrDefault(x => ((x.ItemIndex < start || x.ItemIndex > (start + noOfItems - 1)) && !x.IsEnsured));
            }
            if (row != null)
            {
                row.ItemIndex = i;
                row.BindingContext = this.ListView.DataSource[i];
                row.IsEnsured = true;
            }
        }
        else
            row.IsEnsured = true;
    }
    base.OnSizeAllocated(width, height);
} 
```

## 将联系人信息绑定到自定义列表视图

设计您的模板，并为您的模板定义合适的项目高度。然后在视图中呈现自定义 ListView。

下面的代码演示了一个简单的模板，用于呈现我们前面生成的联系信息。

```
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:CustomControl"
             x:Class="CustomControl.MainPage"
             Padding="5,5,10,5>

    <ContentPage.BindingContext>
        <local:ContactsViewModel x:Name="viewModel" />
    </ContentPage.BindingContext>

    <local:SimpleListView ItemHeight="60"
                          DataSource="{Binding Contacts}">
        <local:SimpleListView.ItemTemplate>
            <DataTemplate>
                <StackLayout>
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="60" />
                            <ColumnDefinition Width="*" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <ContentView Padding="5, 0, 5, 0">
                            <Label Text="{Binding ContactImage}" BackgroundColor="{Binding ContactImageColor}" TextColor="White" FontSize="32"  HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" HorizontalTextAlignment="Center" VerticalTextAlignment="Center" />
                        </ContentView>
                        <StackLayout Grid.Column="1">
                            <Label LineBreakMode="NoWrap" TextColor="#474747" Text="{Binding ContactName}" FontSize="16" FontAttributes="Bold" VerticalTextAlignment="Center" />
                            <Label TextColor="#474747" LineBreakMode="NoWrap" Text="{Binding ContactNumber}" FontSize="12" VerticalTextAlignment="Center" HorizontalTextAlignment="Start" />
                        </StackLayout>
                        <Label Grid.Column="2" TextColor="#474747" LineBreakMode="NoWrap" Text="{Binding CallTime}" FontSize="12" VerticalTextAlignment="Center" HorizontalTextAlignment="Start" />
                    </Grid>
                    <BoxView BackgroundColor="Gray" HeightRequest="1" />
                </StackLayout>
            </DataTemplate>
        </local:SimpleListView.ItemTemplate>
    </local:SimpleListView>
</ContentPage> 
```

**<u>注</u>** :前面代码示例中的自定义 ListView 创建完全用 XAML 逻辑解释。但是，您也可以通过后面的代码来实现这一点。

祝贺您在应用程序中配置了一个简单的定制 ListView。只要按照这些步骤运行示例，就会在您的视图中呈现一个漂亮的 ListView。

[![Xamarin Forms Custom List View](img/1f0a254ab4a09c034c082085abc2851a.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--cT7zZ4kH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Xamarin-Forms-Custom-List-View.png)

*Xamarin 表单自定义列表视图*

前面的屏幕截图显示了定制 ListView 的结果。但是，您可能希望添加更多的定制选项和高级功能。要查看这些，您可以参考 Syncfusion [ListView](https://www.syncfusion.com/products/xamarin/listview) 和 [DataGrid](https://www.syncfusion.com/products/xamarin/datagrid) 组件页面。

## 结论

在这篇博文中，我们介绍了如何在 Xamarin.Forms 中创建和配置一个简单的定制控件，即带有可定制项目高度的定制 ListView 和模板。请务必下载[示例应用程序](https://github.com/SyncfusionExamples/simple-listview-in-xamarin-forms/)来部署一个可随时运行的示例，并体验在 Xamarin 中创建一个定制控件是多么容易。表格！

如果您是当前客户，我们邀请您查看 Xamarin 的 [Syncfusion ListView 和 DataGrid 组件。如果你不是目前的客户，你可以随时](https://www.syncfusion.com/support/directtrac/downloads)[下载我们的免费评估](https://www.syncfusion.com/downloads/xamarin)来看看他们的行动。您还可以在 [Google Play](https://play.google.com/store/apps/details?id=com.syncfusion.samplebrowser) 和 [App Store](https://itunes.apple.com/us/app/syncfusion-controls-explorer/id1109963411?mt=8) 上探索 ListView 和 DataGrid 示例，并在我们的文档中了解高级功能( [ListView](https://help.syncfusion.com/xamarin/sflistview/overview) 、 [DataGrid](https://help.syncfusion.com/xamarin/sfdatagrid/overview) )。

如果您有任何问题或需要澄清，请在下面的评论区告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/) 或[反馈门户](https://www.syncfusion.com/feedback/xamarin-forms)联系我们。我们很乐意为您提供帮助！

如何在 Xamarin 中创建自定义控件？表单首先出现在 [Syncfusion 博客](https://blog.syncfusion.com)上。