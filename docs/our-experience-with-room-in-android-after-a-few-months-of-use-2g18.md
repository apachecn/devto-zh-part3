# 经过几个月的使用，我们对 Android 中 Room 的体验

> 原文：<https://dev.to/apium_hub/our-experience-with-room-in-android-after-a-few-months-of-use-2g18>

在 Android 中使用 **Room 几个月后，我们遇到了一些问题，与市场上的其他 ORM(如 Hibernate)相比，这些问题确实阻碍了我们的开发。发现的一些问题如下:**

## 房间中的安卓问题

*   不允许手动插入查询，只能用@Insert 注释方法，并定义替换策略以防有冲突。
*   它不允许将外键定义为实体内的集合。例如，如果我们有一个相册实体，我们不能在其中定义一个列表作为外键，而是强制我们在 track Track 实体中放置一个注释为外键的 albumId 字段。这使得相册表中的插入不是原子的，因为您必须将它们分别插入到两个表中(也不允许自定义插入查询，如第一点所示)。
*   它不允许在插入中定义返回类型。换句话说，如果我们想要在一个主键被定义为 String 的表中插入，插入方法将总是返回 Long(或者在我们插入几个元素的情况下返回 List)，这个 Long 就是 rowId。
*   通过不允许实体中的外键列表，为了进行读取，我们必须创建一个视图(以数据类的形式),其中包括我们想要读取的实体的关系，因此我们通过每次读取相同的实体来复制数据库模型，具有只有一个 ID 的特征实体

```
@Entity(tableName = FeaturedDb.FEATURED_TABLE_NAME)
data class FeaturedEntity(
 @PrimaryKey @ColumnInfo(name = FeaturedDb.FEATURED_ID_FIELD_NAME) var id: String
) 
```

然后我们有 N 个实体通过一个外键引用这个特性(在这种情况下，它将是唯一的相册):

```
data class AlbumEntity(
 @PrimaryKey @ColumnInfo(name = AlbumDb.ALBUM_ID_FIELD_NAME) var id: String,
 var title: String,
 @ColumnInfo(name = AlbumDb.FEATURED_ID_KEY_FK) var featuredId: String? = null
) 
```

如果我们想在单个查询中通过 ID 读取 FeaturedEntity，我们必须有一个中间模型，例如:

```
data class FeaturedDbDto(
 @Embedded var featuredEntity: FeaturedEntity,
 @Relation(
     entity = AlbumEntity::class,
     parentColumn = "id",
     entityColumn = "featuredId"
 ) var albums: List = emptyList()
) 
```

这些只是我们遇到的一些困难，尽管无疑还有许多其他困难，这些困难将使我们在未来的项目中重新思考 Android 中的空间的使用。

然而，并不是一切都那么糟糕。Android 中的 Room 也给了我们一些工具，让我们的生活变得更加轻松。

## 房间里的安卓福利

*   大量使用注释意味着我们不必编写任何代码来与数据库交互。举例来说，这是在使用 Realm 时所遗漏的东西。
*   Android 中的 Room 提供了与 LiveData 和 RxJava 的现成集成，这使得创建反应式应用程序变得更加容易，而不必处理可观察对象的创建，这允许我们观察数据库中的变化以立即做出反应。

帖子[我们在使用了几个月后对 Android 中的 Room 的体验](https://apiumhub.com/tech-blog-barcelona/room-android/)首先出现在 [Apiumhub](https://apiumhub.com) 上。