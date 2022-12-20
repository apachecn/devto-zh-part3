# 如何使用官方 mongodb-go-driver 连接 Go 和 MongoDB！

> 原文：<https://dev.to/kartikbudhiraja/how-to-connect-go-with-mongodb-using-official-mongodb-go-driver-5a59>

[![](img/371f155577fc7f69197666701d622322.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUzaVwub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AB4CJ2CIgFv3J2TDEu-8A8A.jpeg)

2018 年 12 月 13 日，MongoDB 发布了其官方 Go 驱动程序的测试版，准备让更广泛的 Go 和 MongoDB 社区对其进行测试。🤩MongoDB GO 驱动程序设置起来非常简单快捷，目前仍在 [WIP](https://jira.mongodb.org/projects/GODRIVER/issues/GODRIVER-861?filter=allopenissues) 中，但每天都在改进功能和特性。本教程基于 MongoDB 发布的官方教程，但我试图让它尽可能简单，并附有工作参考。

TLDR:源代码可以通过 CRUD 操作在这里找到

在本教程中，我们将涵盖:

*   安装 MongoDB Go 驱动程序
*   使用 Go 驱动程序连接 MongoDB
*   BSON 对象在围棋中的应用
*   执行 CRUD 操作

1.  **安装 MongoDB Go 驱动程序**

MongoDB Go 驱动程序由几个包组成。如果您只是使用 go get，您可以使用:
安装驱动程序

```
go get github.com/mongodb/mongo-go-driver 
```

其输出可能看起来像一个警告，类似于将 github.com/mongodb/mongo-go-driver: no Go 文件打包在(...).这是预期的结果。

如果你正在使用 ***govendor*** 包管理器，你需要使用以下命令安装主 mongo 包以及 bson 和 mongo/options 包:

```
govendor fetch github.com/mongodb/mongo-go-driver/mongo

govendor fetch go.mongodb.org/mongo-driver/bson

govendor fetch go.mongodb.org/mongo-driver/mongo/options 
```

所有需要的包都应该安装好了，现在让我们建立一个到 Mongo 的连接。

**2。设置连接**

一旦导入了 MongoDB Go 驱动程序，我们就可以使用客户机连接到 MongoDB 部署。连接(上下文)。我们需要建立一个新的客户端，我们需要通过我们的 mongo 数据库的 URI，而设置客户端。那我们只需要打电话给客户。连接(context)与上下文，这将建立我们的连接。

下面的代码为我们建立了一个连接:

> 为了保持简单，我将只提供正在使用的函数的代码片段，你可以在这里找到完整的源代码。

```
//Set up a context required by mongo.Connect
ctx, cancel := context.WithTimeout(context.Background(), 10\*time.Second)

//To close the connection at the end
defer cancel()

//We need to set up a client first
//It takes the URI of your database
client, error := mongo.NewClient(options.Client().ApplyURI("your\_database\_uri"))

if error != nil {
 log.Fatal(err)
}

//Call the connect function of client
error = client.Connect(ctx)

//Checking the connection
error = client.Ping(context.TODO(), nil)
fmt.Println("Database connected") 
```

**3。在围棋中使用 BSON 物体**

MongoDB 中的 JSON 文档存储在名为 BSON(二进制编码的 JSON)的二进制表示中。与将 JSON 数据存储为简单的字符串和数字的其他数据库不同，BSON 编码扩展了 JSON 表示，以包括其他类型，如 int、long、date、floating point 和 decimal128。这使得应用程序更容易可靠地处理、排序和比较数据。Go 驱动程序有两种类型来表示 BSON 数据:D 类型和原始类型。

D 系列类型用于使用本机 Go 类型简洁地构建 BSON 对象。这对于构造传递给 MongoDB 的命令特别有用。D 系列包括四种类型:

*   一份 BSON 的文件。这种类型应该在顺序很重要的情况下使用，比如 MongoDB 命令。
*   一个无序的地图。它与 D 相同，只是它不保持顺序。
*   答:BSON 阵列。
*   e:d 中的单个元素。

下面是一个使用 D 类型构建的过滤文档的例子，它可以用来查找 name 字段匹配 Alice 或 Bob 的文档:

```
bson.D{{
 "name", 
 bson.D{{
 "$in", 
 bson.A{"Alice", "Bob"}
 }}
}} 
```

**4。积垢操作**

对于 CRUD 和其他操作，我们需要使用集合对象，我们可以通过引用我们数据库中各自的集合来创建它，比如:

```
BooksCollection := client.Database("test").Collection("books") 
```

*   **插入**

对于创建，我们可以使用集合。InsertOne()用于单个条目，或者可以使用集合。接受对象切片的 InsertMany()。

```
/\*\*
\* Create - Adding a new book
\* res -\> the insert command returns the inserted id of the oject
\*/

res, err := BooksCollection.InsertOne(ctx, bson.M{"name": "The Go Language", "genre": "Coding", "authorId": "4"})

if err != nil {
log.Fatal(err)
} 
```

收藏中。InsertOne()，我们可以通过 bson 传递一个字符串对象。或者我们可以创建一个我们各自类型结构的对象并传递这个对象。

*   **改为**

为了查找文档，我们需要一个过滤文档以及一个指向结果可以解码成的值的指针。若要查找单个文档，请使用 collection。FindOne()。此方法返回单个结果，该结果可以解码为一个值。过滤器对象指定了我们在寻找什么。

```
filter := bson.D{{"name", "Book 1"}}

// create a value into which the result can be decoded
var result bookType

err = collection.FindOne(context.TODO(), filter).Decode(&result)
if err != nil {
 log.Fatal(err)
}

fmt.Printf("Found a single Book: %+v\n", result) 
```

若要查找多个文档，请使用 collection。查找()。该方法返回一个光标。光标提供了一个文档流，通过它我们可以一次迭代和解码一个文档。一旦游标耗尽，我们应该关闭游标。

```
cur, error := BooksCollection.Find(ctx, bson.D{{}})

var allbooks []\*bookType

//Loops over the cursor stream and appends to result array
for cur.Next(context.TODO()) {
var booksResultHolder bookType

err := cur.Decode(&bookResultHolder)

if err != nil {
log.Fatal(err)
}

allbooks = append(allbooks, &booksResultHolder)
}

//dont forget to close the cursor
defer cur.Close(context.TODO())

// Loop over the result array and perform whatever required
for \_, element := range allbooks {
book := \*element
fmt.Println(book)
} 
```

> *   如果你得到空的结果，检查你的类型结构的字段，你必须导出结构的字段，否则它们会被 mongodb-go-driver 包忽略。(每个字段应以大写字母开头，指明可以导出)
> 
> 类型用户结构{
> 
> 用户字符串`bson:"user" json:"user"`
> 
> 数据字符串`bson:"data" json:"data"`
> 
> 默认情况下，当从 MongoDB 转换/存储/检索结构值时，使用字段名。如果你想使用不同的名字，你可以使用 [*标签*](https://stackoverflow.com/questions/10858787/what-are-the-uses-for-tags-in-go/30889373#30889373) 来告诉字段应该映射到什么名字。
> 
> *   如果您的文档已经自动生成 id，此时您将无法通过 mongodb-go-server 获得这些 id，这是因为 id 是包装在自定义对象中的字符串，而此时驱动程序不支持它们。更多详细信息可以在这里找到[。](https://stackoverflow.com/questions/52024532/using-mongodb-go-driver-for-decoding-documents-into-structs-with-custom-type-fie)

*   **更新**

这个系列。UpdateOne()方法允许您更新单个文档。它需要一个过滤文档来匹配数据库中的文档，还需要一个更新文档来描述更新操作。这些可以像我们在阅读时创建过滤器对象一样来构建。

```
/\*\*
\* Update
\* Collection has functions like UpdateOne and UpdateMany
\* Returns the Matched and Modified Count
\*/

filter := bson.D{{"name", "Book 1"}}

// Need to specify the mongodb output operator too
newName := bson.D{
{"$set", bson.D{
{"name", "Updated Name of Book 1"},
}},
}

res, err := BooksCollection.UpdateOne(ctx, filter, newName)

if err != nil {
log.Fatal(err)
}

updatedObject := \*res

fmt.Printf("The matched count is : %d, the modified count is : %d", updatedObject.MatchedCount, updatedObject.ModifiedCount) 
```

*   **删除**

最后，我们可以使用 collection 删除文档。DeleteOne()或集合。DeleteMany()。这里我们可以传递 nil 作为过滤器参数，它将匹配集合中的所有文档或任何其他特定参数。我们也可以使用[集合。Drop()](https://godoc.org/github.com/mongodb/mongo-go-driver/mongo#Collection.Drop) 删除整个收藏。

```
filter = bson.D{{"name", "Updated Name of Book 2"}}

deleteResult, error := BooksCollection.DeleteOne(ctx, filter) 
```

### 下一步

本教程的源代码可以在[这里](https://github.com/kartik-budhiraja/go-tut-mongodb-go-driver)找到。

MongoDB Go 驱动程序的文档可以在 GoDoc 上找到。您可能对关于使用[聚合](https://godoc.org/github.com/mongodb/mongo-go-driver/mongo#Collection.Aggregate)或[事务](https://godoc.org/github.com/mongodb/mongo-go-driver/mongo#Session)的文档特别感兴趣。

希望这篇教程能让你的事情变得简单一点，祝大家编码愉快！👨🏽‍💻🍻

* * *