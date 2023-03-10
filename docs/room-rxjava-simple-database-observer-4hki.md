# 房间+rx Java——简单的数据库观察者

> 原文：<https://dev.to/rduriancik/room-rxjava-simple-database-observer-4hki>

最近我在做一个项目，需要根据本地数据库中的数据实时更新 android 应用 geofences。这意味着，如果一个项目被添加到数据库中，我必须为它创建一个地理围栏，或者如果项目被移除，则移除地理围栏。

为了建立数据库，我决定使用 [Room persistence 库](https://developer.android.com/topic/libraries/architecture/room)并利用 [RxJava](https://github.com/ReactiveX/RxJava) ，因为它已经是项目的一个重要部分。Room 在 SQLite 上提供了一个非常好的抽象层，很容易设置，在 RxJava 的帮助下，它允许异步观察数据。在这篇精彩的[文章](https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757)中，你可以找到更多关于如何使用 RxJava 进行查询的信息。

因此，我们的目标是观察服务中的数据库。我发现，Room 让我实时观察所有数据的唯一方法是创建一个查询，在每次发生更改时返回数据库中所有项目的列表。类似这样的东西👇

```
@Query(“SELECT * FROM Table”)
fun observeItems(): Flowable<List<Item>> 
```

但使用这种方法，我必须在服务中存储列表的副本，并与收到的列表进行比较，以找出哪些项目被删除，我们的 updated 补充道。

* * *

为了避免这种情况，这里有一个关于如何构建一个简单的数据库观察器的快速技巧，它会通知您正在进行的事件。出于演示的目的，我创建了一个演示待办事项应用程序(尽管逻辑可能更简单)。这里可以找到[。](https://github.com/rduriancik/Learning/tree/master/Android/RoomDbObserverDemo)

### 1。步骤

首先，我们开始创建我们的`Task`实体，它保存了我们需要知道的所有信息:

```
@Parcelize
@Entity
data class Task(
    var description: String,
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val created: Long = System.currentTimeMillis(),
    var isDone: Boolean = false
) : Parcelable 
```

### 2。步骤

第二步，我们设计了`TaskDao`接口，其中包含了所有的数据操作/检索方法，并设置了`TaskDatabase` :

```
@Dao
interface TaskDao {
    @Insert
    fun insertTask(task: Task): Completable

    @Update
    fun updateTask(task: Task): Completable

    @Query("SELECT * FROM Task")
    fun getAll(): Single<List<Task>>

    @Delete
    fun deleteTask(task: Task): Completable
 } 
```

```
@Database(entities = [Task::class], version = 1, exportSchema = false)
abstract class TaskDatabase : RoomDatabase() {
    abstract fun taskDao(): TaskDao
} 
```

我们观察器初始化最重要的方法是`getAll()`。它将允许我们在实时观测前检索所有保存的数据。如您所见，对于插入、删除和更新等基本操作，我选择了`Completable`作为返回类型，因为它最适合我。但是您可以使用任何您想要的类型，只要它通知您操作是否成功。

### 3。步骤

作为这一步的第一件事，我们创建了`DatabaseEventType` enum 类，它存储了我们想要观察的所有可能事件。在我的例子中，事件是`INSERTED`、`UPDATED`、`REMOVED`。

```
enum class DatabaseEventType {
    INSERTED,
    UPDATED,
    REMOVED
} 
```

然后，我们创建一个简单的泛型类，它将包装事件和导致事件的值。

```
data class DatabaseEvent<T>(val eventType: DatabaseEventType, val value: T) 
```

### 4。步骤

最后，当我们实现了所有必要的组件后，我们就可以最终构建我们的观察者了。基本上，我们在任务数据库上创建一个层，作为单点控制。

```
class TaskRepository private constructor(context: Context) {

    private val mTaskDao = Room.databaseBuilder(context, TaskDatabase::class.java, "tasks-database")
        .build()
        .taskDao()

    private val mObserverSubject = PublishSubject.create<DatabaseEvent<Task>>()

    fun addTask(task: Task): Completable {
        val insertEvent = DatabaseEvent(DatabaseEventType.INSERTED, task)
        return mTaskDao.insertTask(task)
            .doOnComplete { mObserverSubject.onNext(insertEvent) }
    }

    fun deleteTask(task: Task): Completable {
        val deleteEvent = DatabaseEvent(DatabaseEventType.REMOVED, task)
        return mTaskDao.deleteTask(task)
            .doOnComplete { mObserverSubject.onNext(deleteEvent) }
    }

    fun updateTask(task: Task): Completable {
        val updateEvent = DatabaseEvent(DatabaseEventType.UPDATED, task)
        return mTaskDao.updateTask(task)
            .doOnComplete { mObserverSubject.onNext(updateEvent) }
    }

    fun observeTasks(): Observable<DatabaseEvent<Task>> {
        return mTaskDao.getAll()
            .flatMapObservable { Observable.fromIterable(it) }
            .map { DatabaseEvent(DatabaseEventType.INSERTED, it) }
            .concatWith(mObserverSubject)
    }

    companion object {
        @Volatile
        private var instance: TaskRepository? = null

        fun getInstance(context: Context): TaskRepository {
            return instance ?: synchronized(this) {
                instance ?: TaskRepository(context.applicationContext).also { instance = it }
            }
        }
    }
} 
```

*mObserverSubject* 是 RxJava [PublishSubject](http://reactivex.io/RxJava/javadoc/io/reactivex/subjects/PublishSubject.html) 负责实时观察数据库事件。`addTask()`、`deleteTask()`、`updateTask()`三种方法都是利用`TaskDao`进行给定操作。当我们知道操作成功时(在我们的例子中是当`Completable`完成时),我们向这个主题提交一个`DatabaseEvent`对象。`PublishSubject`不保留/缓存项目，因此新订户不会收到任何过去的项目。当我们开始观察数据库时，我们想知道它的内容，我们必须通过`TaskDao`检索条目，并将它们映射到`DatabaseEvent`。这就是`observeTasks()`方法的目的。

* * *

就这样，我们构建了我们的数据库观察者。任何时候我们需要观察数据库，我们都可以调用`observeTask()`方法并订阅`Observable`。我希望这篇文章能帮助你简化你的项目，或者更好地理解如何利用 RxJava 和 Room。如果你有任何问题，欢迎在评论区提问。在我的下一篇文章中，我将解释如何用协程替换 RxJava。