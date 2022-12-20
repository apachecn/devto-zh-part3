# 我第一个发布的应用程序(第二部分)

> 原文：<https://dev.to/davidsprogrammingadventures/my-first-published-app-part-2-3bgg>

## 关于

对于那些读过这篇文章但没有读过第 1 部分的人来说，这只是我个人编程和发布我的第一个应用程序的经验的汇编。虽然最终我会变得足够擅长写教程，但现在我只是用我最近的经验来练习。感谢大家抽出时间。

# 编码

我的应用程序 *OWL 抽认卡 level 1 en - cn* 总共使用了 6 个屏幕，在这一部分，我将回顾其中一些屏幕，并解释我是如何构建它的，以及我在这一过程中所做的一些改进。

### 标题屏幕

标题屏幕由文本视图、图像视图和一个转到下一个屏幕的按钮组成。它们都在线性布局(垂直或水平，取决于方向)内，并且该线性布局被包裹在约束视图内的滚动视图中。

我最初设置了滚动视图，试图为不同的方向和尺寸创建多个屏幕布局...它不起作用。我最终为每个需要的维度创建了一个单独的布局，但是当我写这个的时候，我注意到我忘记了清理旧的滚动视图...哎呀。

标题屏幕不是任何其他屏幕的父活动，因为我只希望它在用户进入应用程序时短暂出现，该屏幕旨在介绍应用程序首次启动时的基本信息，此后就没有必要了。我在标题屏幕上做的唯一有意义的编程是，该应用程序第一次启动时，会显示一个弹出自定义警报和 toast 消息。

自定义提醒对话框用于输入用户名并将其保存到默认的共享首选项中，这样，每次调用 onCreate()时，都会显示一个 *welcome back、*display name 自定义提示。

警报初始化代码:
警报对话框。生成器警报=新警报对话框。建设者(本)；
最终编辑文本 edittext =新编辑文本(this)；
alert . set message(r . string . set _ display _ name _ alert _ message)；
alert . settitle(r . string . welcome _ to _ owl _ flash _ cards _ alert _ title)；
edittext . settext(mpreferences . getstring(settings activity。KEY _ PREF _ 编辑 _ 用户名，“”)；
alert . set view(edittext)；

它会创建一个编辑文本，如果用户名已经在默认的共享首选项中，它会显示该用户名，以便可以覆盖它。之后，它只设置正面警告按钮。

*alert . setpostivebutton(r . string . alert _ dialog _ ok _ button，new DialogInterface。OnClickListener()*

在正面警告中，它保存新的用户名并将 get_user_name 参数设置为 false，这样就不会每次都弹出警告。

这使我能够获得为回访用户填充问候消息所需的所有数据。

祝酒词很有趣，因为谷歌的指南通常说在发布前删除祝酒词，所以我试图为我的弹出消息找到一个类似 snackbar 的替代选择，但它的颜色库与 cardviews 颜色库有问题，我决定我想要 cardviews 颜色库，所以我不能使用 snack bar。

解决方案是一个简单的方法，生成 toast 消息，但是改变它的布局。为此，您需要创建一个 xml 布局，用该布局和一个视图组来扩展它。

*View layout = layoutinflate . inflate(r . layout . welcome _ custom _ toast _ message，(View group)findViewById(r . id . custom _ toast _ welcome _ message))；*

然后，您可以创建一个新的吐司，设置重力(可选)，持续时间，并在您的布局传递。
*toast . layout(布局)
并显示出来。这是一个非常容易和简单的修复方法，但我仍然必须研究为什么在已发布的应用程序中，toast 消息通常不被接受。*

 *### 主屏幕/菜单屏幕

菜单屏幕由 10 个按钮组成，(其中 9 个按钮导致相同的片段活动，但是用于填充它的目的数据不同，另外 1 个按钮导致它自己的活动，其本质上是一个长文本屏幕。)它还有一个选项菜单，可以转到设置活动或搜索活动。主菜单也用作我的应用程序中几乎所有屏幕的父活动。

9 个主要按钮是不同类别的闪存卡，以便于阅读，我处理的方式是将一个公共的静态 final int 传入 intent extras，这样当新活动收到 intent 时，它会使用正确的数据填充自己。我曾经用不止一个活动来处理每个按钮，但在通过谷歌进行认证测试后，我意识到我可以通过正确使用 Android ViewModel 来大大简化应用程序。

菜单屏幕有两个有趣的代码段。首先我希望我的按钮是完美的正方形，这样当它们在屏幕上上下滚动时，它们就很漂亮，很统一。为此，我必须基于我的一个按钮创建一个 *ViewTreeObserver* 对象，然后我为它设置了一个 *GlobalLayoutListener* 。在 *GlobalLayoutListener* 中，我可以使用按钮 *getMeasuredWidth()* 来获得最终的宽度，并将其作为高度传递给我的其他按钮，这样它们就会变成正方形。

例:
viewtree observer button sizeeobserver = manimal button . getviewtree observer()；
button sizeeobserver . addongglobal layout listener(new view tree observer)。ongglobal layout listener(){
@ override
public void ongglobal layout(){
manimal button . getviewtree observer()。removeongglobal layout listener(this)；
int width = manimal button . getmeapplicationwidth()；
mAnimalButton.setHeight(宽度)；

它的全部理由是，当你在 oncreate 中时，按钮宽度有时没有被设置，所以如果你调用 getMeasuredWidth()而没有观察器，宽度可能是空的，导致你的按钮消失。

在这次活动中，我必须做的第二件有趣的事情是初始化我的数据库。我通过测试发现，第一次安装该应用程序时，如果你直接进入抽认卡页面，它们会是空白的，你必须按向上键或后退键返回菜单，然后你可以再试一次。我认为没有用户会想第一次使用应用程序就返回，所以解决方案是尽早初始化数据库，以便当用户进入下一个屏幕时，任何预先填充的数据都已准备就绪。

### 抽认卡屏幕

所以这个屏幕由 2 个文本视图元素、1 个图像视图和 1 个图像按钮元素组成。文本视图用当前的英文或中文单词及其发音拼写填充。图像视图是基于文本视图所用语言的两个图像之一，图像按钮是播放文本视图中单词声音的播放按钮。

为了让每张卡看起来像闪存卡，我必须为我的主视图组设置一个 *onClickListener()* ，这允许我根据用户的点击来改变文本和图像视图/按钮。我为此使用了一个布尔值来决定使用哪种语言。

为了一次有不止一张卡片，我使用了一个 viewPager，它是在 activity 类中填充的。视图分页器通过为片段调用一个名为 newInstance 的静态函数来初始化我的片段，然后这个静态函数又调用各自的默认构造函数并返回片段。静态函数还接收从分页器传入的数据，并将其放入一个包中，以便 onCreateActivity 的片段可以解析它。

片段捆绑了额外的东西，起初让我感到困惑，因为我创建了一个包含多个表的数据库，并试图每次传入一个唯一的表，这种方法迫使我拥有多个片段和页面适配器。当我将我的数据库简化为 1 个表，但有一行是分类时，我能够减少大量创建片段的代码冗余。

在构建这个页面时，我发现 ViewObservers 很有趣，并且有多种选项可以存储和调用数据库中的数据。虽然教程可能缺乏一些细节，但是一些 android 文档代码已经足够让很多人开始学习了。

### 搜索页面

Android 有一个搜索小部件和文档来尝试使用它，但我想尝试一下自定义视图。所以我简单地创建了一个按钮和编辑文本字段的布局，并试图将它放入一个自定义视图中，结果被惹恼了，直接放在了搜索页面中。从那以后，我学会了如何解决我在自定义视图中遇到的问题，但那是以后的博客文章了。

搜索页面使用了一种有趣的技术，LiveObservers 与视图模型和数据库进行交互。

它包括初始化一个 live observer，还用搜索词设置了第二个 MutableLiveObserver。这样做是为了让 viewmodel 类可以将第一个 LiveObserver 保持为私有，而不必让用户直接与它交互，这基本上简化了它的工作，使它可以更有效地处理数据更改。

ex: **搜索活动**
mview model = viewmodelproviders . of(this)。get(cardviewmodel . class)；
最终观察者> cardObserver =新观察者>(){
@ Override
public void on changed(@可空列表卡片){ }
mview model . setsearchterm(mLastSearchTerm)；

**查看模型类**

public LiveData > msarchedcard =
transformations . switch map(mSearchTerm，search term->{
return mrepository . find card(search term)；
})；
私有可变数据 mSearchTerm =新可变数据<>()；

public void setsearch term(String term){
msearchterm . setvalue(term)；
}

LiveObserver 用于搜索活动的原因是为了让用户可以不止一次地搜索，并且我不必使用加载器和异步任务来检索新的结果或存储以前的结果。

总结了我第一个发布的应用程序的主要有趣之处，稍后我可能会在第 3 部分中添加关于通过 google play 发布的内容。*