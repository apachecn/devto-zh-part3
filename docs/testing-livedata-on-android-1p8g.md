# 在 Android 上测试 LiveData

> 原文：<https://dev.to/alediaferia/testing-livedata-on-android-1p8g>

*原载于[alediaferia.com](https://alediaferia.com/2018/12/17/testing-livedata-room-android/)T3】*

我最近开始开发一个 Android 应用程序，让我在学习[科特林](https://kotlinlang.org/)的旅途中保持动力。我最近的经验是使用[架构组件](https://developer.android.com/topic/libraries/architecture/)，这篇简短的博客文章将特别关注在使用 [`LiveData`](https://developer.android.com/jetpack/arch/livedata) 时对 DAO 进行单元测试。

# 什么是 LiveData？

`LiveData`是一个生命周期感知、可观察的数据容器，它将帮助您对数据源的变化做出反应。在我的例子中，我将它与 [`Room`](https://developer.android.com/jetpack/arch/room) 结合使用，以确保我的应用程序对数据库中可用的新数据做出反应。

# 我们简单的刀

在这篇博文中，我们假设有一个非常简单的 DAO，如下所示:

```
@Dao
interface PostDao {
    @Query("SELECT * from posts")
    fun getAll(): LiveData<List<Post>>

    @Insert
    fun insert(post: Post)
} 
```

这只是一把帮你取帖的刀。

如您所见，函数的返回类型不仅仅是简单的`List<Post>`，而是将它包装在一个 [`LiveData`](https://developer.android.com/reference/android/arch/lifecycle/LiveData) 实例中。这很棒，因为我们可以一次获得帖子列表的实例，然后 [*观察*的变化并对它们做出反应](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html#observe(android.arch.lifecycle.LifecycleOwner,%20android.arch.lifecycle.Observer%3CT%3E))。

# 来测试一下吧

Android 开发人员文档中有一个关于如何对 DAO 进行单元测试的简洁示例:

```
@RunWith(AndroidJUnit4::class)
class PostDaoReadWriteTest {
    private lateinit var postDao: PostDao
    private lateinit var db: TestDatabase

    @Before
    fun createDb() {
        val context = ApplicationProvider.getApplicationContext<Context>()
        db = Room.inMemoryDatabaseBuilder(
                context, TestDatabase::class.java).build()
        postDao = db.getPostDao()
    }

    @After
    @Throws(IOException::class)
    fun closeDb() {
        db.close()
    }

    @Test
    @Throws(Exception::class)
    fun writePostAndReadInList() {
        assertEquals(0, postDao.getAll().value?.size)
        val post = TestUtil.createPost(id=42)
        postDao.insert(post)
        assertEquals(1, posttDao.getAll().value?.size)
    }
} 
```

这个非常简单的测试的目的是测试数据是否被正确公开，并且一旦 post 被添加到数据库，它就会被`getAll()`调用反映出来。

不幸的是，当我们断言它时，`LiveData`实例的值将不会被填充，这将使我们的测试失败。这是因为`LiveData`使用面向生命周期的异步机制来填充底层数据，并期望注册一个观察者来通知数据变化。

## *观察*数据

`LiveData`提供了一种方便的观察方法，允许在数据变化时观察数据。我们可以用它来注册一个将断言期望值的观察者。

[`observe`](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html#observe(android.arch.lifecycle.LifecycleOwner,%20android.arch.lifecycle.Observer%3CT%3E)) 方法具有以下签名:

```
void observe(LifecycleOwner owner, Observer<T> observer) 
```

它期望一个 [`observer`](https://developer.android.com/reference/android/arch/lifecycle/Observer.html) 实例和一个 [`owner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) 实例作为它的生命周期。在我们的例子中，我们感兴趣的只是将观察者留在能够断言已更改数据的地方。我们不希望每次数据改变时都评估相同的断言。

## 拥有自己的生命周期

那么，我们能做的就是构建一个拥有自己生命周期的观察者实例。在处理完 [`onChange`](https://developer.android.com/reference/android/arch/lifecycle/Observer.html#onChanged(T)) 事件后，我们将把观察者的生命周期标记为销毁，让框架完成剩下的工作。

让我们看看观察者代码是什么样子的:

```
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.LifecycleOwner
import androidx.lifecycle.LifecycleRegistry
import androidx.lifecycle.Observer

/**  * Observer implementation that owns its lifecycle and achieves a one-time only observation  * by marking it as destroyed once the onChange handler is executed.  *  * @param handler the handler to execute on change.  */
class OneTimeObserver<T>(private val handler: (T) -> Unit) : Observer<T>, LifecycleOwner {
    private val lifecycle = LifecycleRegistry(this)
    init {
        lifecycle.handleLifecycleEvent(Lifecycle.Event.ON_RESUME)
    }

    override fun getLifecycle(): Lifecycle = lifecycle

    override fun onChanged(t: T) {
        handler(t)
        lifecycle.handleLifecycleEvent(Lifecycle.Event.ON_DESTROY)
    }
} 
```

这个 observer 实现接受一个*λ*，它将作为`onChange`事件的一部分被执行。一旦处理程序完成，它自己的生命周期将把自己标记为`ON_DESTROY`，这将触发从`LiveData`实例中删除的过程。

然后，我们可以在 LiveData 上创建一个扩展来利用这种观察器:

```
fun <T> LiveData<T>.observeOnce(onChangeHandler: (T) -> Unit) { 
    val observer = OneTimeObserver(handler = onChangeHandler) 
    observe(observer, observer)
} 
```

# 是时候修正我们的测试了

```
@RunWith(AndroidJUnit4::class)
class PostDaoReadWriteTest {
    private lateinit var postDao: PostDao
    private lateinit var db: TestDatabase

    @get:Rule
    val instantTaskExecutorRule = InstantTaskExecutorRule()

    @Before
    fun createDb() {
        val context = ApplicationProvider.getApplicationContext<Context>()
        db = Room.inMemoryDatabaseBuilder(
                context, TestDatabase::class.java).build()
        postDao = db.getPostDao()
    }

    @After
    @Throws(IOException::class)
    fun closeDb() {
        db.close()
    }

    @Test
    @Throws(Exception::class)
    fun writePostAndReadInList() {
        postDao.getAll().observeOnce {
          assertEquals(0, it.size)
        }

        val post = TestUtil.createPost(id=42)
        postDao.insert(post)

        postDao.getAll().observeOnce {
          assertEquals(1, it.size)
        }
    }
} 
```

这次有几件事需要注意。

首先，我们利用了一个 [`InstantTaskExecutorRule`](https://developer.android.com/reference/androidx/arch/core/executor/testing/InstantTaskExecutorRule) 。这是一个有用的实用规则，它负责将后台异步任务执行器与同步任务执行器进行交换。这对于能够确定性地测试我们的代码是至关重要的。*(如果你想了解更多 JUnit 规则，请查看[这个](https://github.com/junit-team/junit4/wiki/rules))*。

除此之外，我们现在利用已经编写的`LiveData`扩展来编写我们的断言:

```
postDao.getAll().observeOnce {
    assertEquals(0, it.size)
} 
```

我们只是通过将所有细节留在我们的 observer 实现中，以一种更加紧凑和富有表现力的方式进行了断言。我们现在确定性地断言`LiveData`实例，使得这种测试更容易读写。

# 结论

我希望这篇文章能帮助你更有效地为你的 DAO 编写测试。这是我最早使用 Kotlin 和 Android 的经验之一:请随意评论解决这个问题的更好方法。

###### 封面照片由[欧文·史密斯](https://unsplash.com/photos/cwqG1N1AtI0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/mobile-app-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

*原载于[alediaferia.com](https://alediaferia.com/2018/12/17/testing-livedata-room-android/)T3】*