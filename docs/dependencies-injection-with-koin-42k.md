# 使用 Koin 进行依赖注入

> 原文：<https://dev.to/fevziomurtekin/dependencies-injection-with-koin-42k>

<center>![Crepe](img/6bf299efa1603e9b80801586e7d80d35.png)</center>

大家好，
在这篇文章里，我会告诉大家，在有 Dagger 这样的库和 S.O.L.I.D 五大支柱之一的依赖注入原理的情况下，我为什么更喜欢 Koin(中小型项目)。

## 什么叫依赖注入？

简单来说依赖注入，依赖注入主要用于依赖的控制和管理。

我们如何控制和管理依赖性？如果你问我们为什么要使用它，你可以从下面两篇文章中获得更多的详细信息。

<center>![](img/78e6adb7259d8bd213a25ed48d5d2294.png)
[A quick intro to Dependency Injection: what it is, and when to use it](https://medium.freecodecamp.org/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f)</center>

<center>![](img/b86b20fb98241e4f096aa35d726a7a5f.png)
[Dependency Injection in Android with Dagger 2 and Kotlin](https://www.raywenderlich.com/262-dependency-injection-in-android-with-dagger-2-and-kotlin)</center>

### 什么是 Koin？

Koin 是一个简单而强大的依赖注入库。是用科特林写的。

### 有匕首为什么还要用锦鲤？我为什么用 Koin？

我用 Koin 的原因；匕首；它对我来说是一个很难学习和练习的结构，因为形成了太多的类和混合的结构。我用 Koin 是因为它的结构更简单，重量轻。

匕首来说明什么时候应该优先选择 Koin 一个简单的项目，同时使用 Dagger 和 Koin 来创建两者之间的差异，让我们逐一检查。

### 锦鲤 VS 匕首

> Dagger 和 Koin 库将通过一个使用 MVVM 的项目进行比较。

*   **DI 包结构比较**

<center>![Crepe](img/c28ace935630cc7cf190e6b1e50766f5.png)</center>

在第一幅图中，我们看到 Dagger 使用的类和包结构，第二幅图创建了 Koin ins 依赖项。Koin 正在创建一个更简单、数量更少的类。

*   **编译前后的代码数量**

<center>![Crepe](img/80fee6651102bcce2ffa044bf4be2e95.png)</center>

如示例所示，硬币和匕首生成的代码数量之间存在一半的差异。这说明 Koin 是一个比较轻的结构。

*   **构建时间**

```
 Koin:
    BUILD SUCCESSFUL in 19s
    120 actionable tasks: 112executed, 7up-to-date

    Dagger:
    BUILD SUCCESSFUL in 21s
    118 actionable tasks: 112 executed, 7up-to-date 
```

Enter fullscreen mode Exit fullscreen mode

*   **设置/包和类结构**

***匕首***

 *对项目实施 Dagger Sdk

```
 kapt "com.google.dagger:dagger-compiler:$dagger_version"
    kapt "com.google.dagger:dagger-android-processor:$dagger_version"
    implementation "com.google.dagger:dagger:$dagger_version" 
```

Enter fullscreen mode Exit fullscreen mode

用于注入活动、片段和视图模型的类；

*   活动模块

```
 @Module
abstract class ActivityModule {
    @ContributesAndroidInjector(modules = [FragmentModule::class])
    abstract fun contributeMainActivity(): MainActivity

    //Add your other activities here
} 
```

Enter fullscreen mode Exit fullscreen mode

*   碎片模块

```
 @Module
    abstract class FragmentModule {
        @ContributesAndroidInjector
        abstract fun contributeNewsFragment(): NewsFragment

        @ContributesAndroidInjector
        abstract fun contributeNewDetailsFragment(): NewDetailsFragment

    } 
```

Enter fullscreen mode Exit fullscreen mode

*   视图模型模块

```
 @Module
    abstract class ViewModelModule {

        @Binds
        abstract fun bindViewModelFactory(factory: ViewModelFactory): ViewModelProvider.Factory

        @Binds
        @IntoMap
        @ViewModelKey(MainViewModel::class)
        abstract fun bindMainViewModel(mainViewModel: MainViewModel): ViewModel

        @Binds
        @IntoMap
        @ViewModelKey(NewsViewModel::class)
        abstract fun bindNewsViewModel(newsViewModel:  NewsViewModel): ViewModel

        @Binds
        @IntoMap
        @ViewModelKey(NewDetailsViewModel::class)
        abstract fun bindNewDetailsViewModel(newDetailsViewModel:  NewDetailsViewModel): ViewModel
    } 
```

Enter fullscreen mode Exit fullscreen mode

在创建了要注入到项目中的模块和组件之后；

```
 Class BaseApplication : Application(), HasActivityInjector { 
  @Inject
      lateinit var dispatchingAndroidInjector:    DispatchingAndroidInjector<Activity>

    override fun activityInjector() = dispatchingAndroidInjector

    fun initDagger() {
       DaggerAppComponent
          .builder()
          .application(this)
          .build()
          .inject(this)
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

***Koin***

i̇mplementation 把 Koin SDK 交给了这个项目

```
 implementation "org.koin:koin-android-viewmodel:$koin_version" 
```

Enter fullscreen mode Exit fullscreen mode

> 锦鲤与匕首的区别；我们没有为 Activity、fragment 和 viewmodule 创建单独的类，而是在一个类中完成所有工作。这将照顾到一个类中的一切，并防止分支。

*   应用模块

```
 package com.fevziomurtekin.di

    import androidx.room.Room
    import com.fevziomurtekin.hackernewsapp.data.room.*
    import com.fevziomurtekin.hackernewsapp.ui.main.MainViewModel
    import com.fevziomurtekin.hackernewsapp.ui.newdetails.NewsDetailsViewModel
    import com.fevziomurtekin.hackernewsapp.ui.news.NewsViewModel
    import com.fevziomurtekin.hackernewsapp.util.ApplicationSchedulerProvider
    import com.fevziomurtekin.hackernewsapp.util.SchedulerProvider
    import org.koin.android.architecture.ext.viewModel
    import org.koin.android.ext.koin.androidApplication
    import org.koin.dsl.module.applicationContext

    val appModule = applicationContext {

        viewModel { MainViewModel(get(),get()) }

        viewModel { NewsViewModel(get()) }

        viewModel { NewsDetailsViewModel(get()) }

        // ItemRepository providers.
        bean { ItemRepositoryImpl(get(),get()) as ItemRepository }

        bean { UserRepositoryImpl(get(),get()) as UserRepository }

        // provider to Room database.
        bean {
            Room.databaseBuilder(androidApplication(),Database::class.java,"hackernews-db")
                .allowMainThreadQueries()
                .fallbackToDestructiveMigration()
                .build()
        }

        bean { ApplicationSchedulerProvider() as SchedulerProvider }

        // Expose ItemDao directly
        bean { get<Database>().itemDao() as ItemDao }

        // Expose UserDao directly
        bean { get<Database>().userDao() }

    }

    val onlineNewsApp = listOf(appModule, remoteModule) 
```

Enter fullscreen mode Exit fullscreen mode

Koin 和在 AppModule 中写这篇文章时我们需要知道的 Koin 的组件

*   **get () →** 该组件通常用于注入构造方法(只能用 get()注入 App.module 中定义的结构)
*   **factory →** 该组件用于在您每次请求时提供新样品。
*   **bean →** 该组件在执行单次识别时使用。
*   当您想在不同的类中拥有同一个类的多个实例时，这个组件是必需的。

创建模块和组件后，Koin 才能启动；

```
 package com.fevziomurtekin.hackernewsapp

    import android.app.Application
    import com.fevziomurtekin.di.onlineNewsApp
    import org.koin.android.ext.android.startKoin

    class BaseApplication : Application(){

        override fun onCreate() {
            super.onCreate()

            startKoin(this, onlineNewsApp)

        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

> 将 Koini 注入我们的项目后，我们调用我们在 AppModule 中创建的 mainViewModel 作为例子；
> 
> *选择“按视图模型查看模型”(*

### 资源与结论

<center>![](img/283c3f968b7d8eb3b8a69bc81ee868d4.png)
[Koin Github](https://github.com/InsertKoinIO/koin)</center>

<center>![](img/283c3f968b7d8eb3b8a69bc81ee868d4.png)
[Getting started Koin Android](https://github.com/InsertKoinIO/getting-started-koin-android)</center>

我试图解释为什么我更喜欢 Koin，我如何使用它，以及如果我在一个简单的项目中使用 dagger 和 coin，我会花多少钱。更多信息请访问。然后

链接到我和 Koin 一起开发的项目:

<center>
![](img/3d1cb15f05d1c5b3ead378269f41d184.png)
[HackerNewsApp](https://github.com/fevziomurtekin/hackernewsapp)</center>*