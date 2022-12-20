# 如何使用 Dagger 2 - Binds 注释

> 原文：<https://dev.to/aldok/how-to-use-dagger-2---binds-annotation-35ho>

Dagger 2 引入了新的注释@Binds。你可以用它来简化@Provides，它把这个:

```
@Module
class HomeModule {

    @Provides
    HomeView provideHomeView() {
        return new HomeViewImpl();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
@Module
abstract class HomeModule {

    @Binds
    abstract HomeView provideHomeView(HomeViewImpl impl);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 可以一起用@Binds 和@Provides 吗？

是的，你可以使用静态方法。

```
@Module
public abstract class MainActivityModule {

    @Provides
    static MainPresenter provideMainPresenter(MainView mainView, ApiService apiService) {
        return new MainPresenterImpl(mainView, apiService);
    }

    @Binds
    abstract MainView provideMainView(MainActivity mainActivity);
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Kotlin 上，你可以使用`companion object`来实现同样的事情。

```
@Module
abstract class MainApplicationModule {

    @Binds
    abstract fun provideApplication(application: Application): Context

    @Module
    companion object {
        @JvmStatic
        @Provides
        internal fun provideAgeMap(): Map<String, Int> {
            return mapOf("Luffy" to 17, "Shanks" to 37)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 裁决

@Binds 显然使组件声明更加简洁。和@Provides 结合的时候有点小问题。虽然这两种方法都有效。为了保持一致，我更喜欢使用@Provides。但是你也可以自己尝试一下，看看你更喜欢哪一个。