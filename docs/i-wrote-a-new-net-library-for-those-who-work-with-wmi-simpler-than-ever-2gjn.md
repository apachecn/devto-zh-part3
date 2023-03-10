# 新的。NET 图书馆为那些谁与 WMI 工作。比以往任何时候都简单。

> 原文：<https://dev.to/nicoriff/i-wrote-a-new-net-library-for-those-who-work-with-wmi-simpler-than-ever-2gjn>

ORMi 是一个新的。我写的用来和 WMI(Windows Management Instrumentation)交流的. NET 库。这些年来，我已经开发了几十个 WMI 兼容的应用程序，我总是得出这样的结论。NET 标准库非常差，使得开发人员编写太多代码来获得简单的值。这就是 ORMi 的立场。让每一个 WMI 工作起来更加容易，让开发人员写出可读性更强、更容易理解和维护的代码。
从 ORMi 的第一个版本开始，我增加了一些改进，比如异步方法、WMI 观察器和 WMI 方法支持。

任何和 WMI 共事过的人。NET 可以告诉你，WMI 的工作可能会令人头痛。WMI 的类名并不是越简单越直观。它们有着复杂且非常规的名字。我敢打赌，你绝不会想在你的项目中给一个类起这个名字。这是 ORMi 使之更简单、更直观的一件事。

让我向你展示它是如何工作的:

为了获得 ORMi 的完整体验，定义我们的模型并映射到 WMI 类是很重要的。假设我们想要获得连接到 PC 的设备列表。这很简单:

```
[WMIClass("Win32_PnPEntity")]  
public class Device  
{  
    public string Name { get; set; }  

    public string Description { get; set; }  

    [WMIProperty("Status")]  
    public string StatusName { get; set; }  
} 
```

上面的类通过使用一些属性得到了增强。 *WMIClass* 告诉 ORMi】设备代表的 WMIClass 的名称是什么。 *WMIProperty* 属性告诉我们哪个 WMI 属性将被映射到那个属性。ORMi 是这样工作的:如果没有属性，那么就用成员名来做映射。如果已经设置了属性，则使用属性名称。

然后，正确设置模型后，我们可以查询 WMI 来获得我们想要的实例:

```
static void Main(string[] args)  
{  
    WMIHelper helper = new WMIHelper("root\\CimV2");
    List<Device> devices = helper.Query<Device>().ToList();  
} 
```

例如，如果您只想搜索鼠标设备，可以这样查询:

```
List<Device> devices = helper.Query<Device>().Where(p => p.Description.Contains("Mouse")).ToList(); 
```

ORMi 还支持所有的 CRUD 操作。您可以用非常简单的方式添加、修改或删除实例。

```
 Person person = new Person
     {
         FirstName = "John",
         Lastname = "Doe",
         DocumentNumber = "9995",
     };
 helper.AddInstance(person); 
```

本例中定义的类是这样定义的:

```
[WMIClass("Lnl_CardHolder")]
public class Person
{
    public string Lastname { get; set; }
    public string FirstName { get; set; }

    [WMIProperty( Name = "SSNO", SearchKey = true)]
    public string DocumentNumber { get; set; }

    [WMIIgnore]
    public int Age { get; set; }
} 
```

异步方式也支持所有这些操作。

欢迎您提出改进、修复建议并为项目做出贡献！。

项目存储库和文档:[https://github.com/nicoriff/ORMi](https://github.com/nicoriff/ORMi)

可以通过 NuGet 下载:[https://www.nuget.org/packages/ORMi/](https://www.nuget.org/packages/ORMi/)