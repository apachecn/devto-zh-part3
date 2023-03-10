# 科特林安卓扩展の视图绑定を视图持有者でも良い感じに使うには

> 原文：<https://dev.to/kozmats/kotlin-android-extensions--view-binding--viewholder--39ia>

# 前言

到目前为止，NewsPicks 的工程师很少以总结的形式对外发表，我觉得里面的工程师很少有机会让我知道平时致力于什么样的技术，或者烦恼着什么。

因此，从今年开始，在 NewsPicks 上也以[NewsPicks Advent Calendar 2018](https://qiita.com/advent-calendar/2018/newspicks) 为名称开始了 Advent Calendar。

第一天，我想从开头的[@_koji_matsu_](https://twitter.com/_koji_matsu_) 主要负责的 Android 领域开始，对[Kotlin Android Extensions](https://kotlinlang.org/docs/tutorials/android-plugin.html) 进行报道。

# 新闻人物における·科特林

截至 2018 年 12 月，面向安卓提供了[NewsPicks](https://play.google.com/store/apps/details?id=com.newspicks) 、[NewsPicks 学院](https://play.google.com/store/apps/details?id=com.newspicks.academia)两个 APP。 其中，关于 NewsPicks，用 100% Kotlin 开发了所有的新代码，关于 NewsPicks 学院，用 100% Kotlin 进行了开发。

实际上，NewsPicks 学院的服务器端也是 100% Kotlin 开发的，以前本公司的[@monzou](https://medium.com/@monzou) 写过[报道](https://medium.com/@monzou/newspicks-kotlin-server-18804392946f)，内容非常有趣，请一定要读一下。

# 查看绑定

如果简单地在 Android 上使用 Kotlin 的话，我想最能感受到好处的莫过于使用 Kotlin Android Extensions 的 View Binding 了。 如果是大量使用 DataBinding 的现场，可以用 DataBinding 代替，但如果是现在主要使用 Jake 神的[Butter Knife](http://jakewharton.github.io/butterknife/) 的人，就像 Butter Knife 那样的 ViewBinding

具体来说，取得常见布局中定义的 View 的实例的以下代码为

```
final Button button = findViewById(R.id.button);
button.setOnClickListener(view -> {
  // do something
}) 
```

可以这样写。

```
button.setOnClickListener {
  // do something
} 
```

这里是突然出现的`button`的实例，这是`Kotlin Android Extensions`的功能，可以将 View 的 ID 名直接自动 Bind 到对应的变量名上。

此外，还具有在将实际 button 实例绑定到 button 变量时自动缓存实例的功能，以避免每次执行 findViewById 时性能下降。

# 在 RecycleView.ViewHolder 内使用 ViewBinding 时的问题点

这样，虽然 ViewBinding 能让用原始的 Java 开发 Android 时发生的`findViewById`变得清晰，但是缓存上一节写的实例的功能目前在`Activity`、`Fragment`、`View`内

也就是说，写了这样简单的 ViewHolder 的情况下，每次调用`HogeViewHolder#show()`，都会对`R.layout.hogehoge`内的 ID `R.id.title`的 View 产生`findViewById`。 (即没有实现保持 View 实例的作为 ViewHolder 的功能)

```
class HogeAdapter(private val context: Context) : RecyclerView.Adapter<RecyclerView.ViewHolder>() {

  override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
    // ViewHolder 生成
    return HogeViewHolder(LayoutInflater.from(context).inflate(R.layout.hogehoge, parent, false))
  }

  override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
    // ViewHolder 内の View インスタンスに対して表示を要求する
    (holder as HogeViewHolder).show()
}
  ....

  private inner class HogeViewHolder(val view: View) : RecyclerView.ViewHolder(view) {
    fun show() {
      // これで R.layou.hogehoge 内の R.id.title という View にアクセスはできるが…
      view.title.text = "hoghoge"
    }
  }
} 
```

# 布局容器支持

像这样，通常在 ViewHolder 内等进行使用`Kotlin Android Extenstions`的 ViewBinding 时，会存在意想不到的陷阱。

在这里，当然也可以抑制轻松的心情，使用`findViewById`好好保持 View 的实例。 例如这样的感觉

```
private class HogeViewHolder(val view: View) : RecyclerView.ViewHolder(view) {
  private val title: TextView? = null

  fun show() {
    if (title == null) title = view.findViewById(R.id.title)
    title?.text = "hoghoge"
  }
} 
```

只是这样会增加锅炉管密码，更重要的是明明放了 Kotlin Android Extensions，却感觉输了…… 在那里登场的是[LayoutContainer Support](https://kotlinlang.org/docs/tutorials/android-plugin.html#layoutcontainer-support) 。

但是，作为注意事项，LayoutContainer Support 为**Experimental Mode** ，因此需要考虑到将来也有很大的可能性进行变更。 ( NewsPicks 以最糟糕自己要负最后责任为由录用)

为了实际使用，为**Experimental Mode** ，所以首先在`build.gradle`中设定 experimentail 为 ON。

```
androidExtensions {
    experimental = true
} 
```

通过这样做，可以这样清晰地记述 ViewHolder。

```
private iclass HogeViewHodler(override val containerView: View) 
  : RecyclerView.ViewHolder(containerView), LayoutContainer {

  fun show() {
    // LayoutContainer を実装する事で ViewBinding がここでも使える　！ そしてきちんと View のキャッシュもされる！ ナイス!
    title.text = "hogehoge"     
  }
} 
```

因为这次会很长，所以没有记载实际上调查 Kotlin 的代码变换成了什么样的字节码的方法，但是实际上通过编译从 Kotlin 生成的字节码，findViewByid 部分会被很好地缓存

# 最后

NewsPicks 正在募集能让我们一起开发 Android 的成员。 因为是 NewsPicks、NewsPicks 学院共同成长的阶段，所以我觉得能感受到与自己相关的 APP 能被很多用户使用的乐趣。

此外，由于 NewsPicks APP 已经过了一段时间，特别是对于 news picks，我觉得内部体系结构需要进一步改进，以避免阻碍未来业务的发展。 我想把安卓的架构变成这样！ 也非常欢迎抱有这种想法的人。

我想磨练自己的本领！ 不仅仅是 APP，还想开发服务器端！ 也欢迎这样的人，所以请随时与[联系](https://www.wantedly.com/companies/newspicks/projects)