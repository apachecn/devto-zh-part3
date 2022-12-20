# neo4j 对软件核心的挑战

> 原文：<https://dev.to/l04db4l4nc3r/challenges-of-neo4j-at-the-heart-of-software-2dbi>

**简介**

Neo4j 是一个 noSQL 数据库，它以图形的形式存储和表示数据。图中的每个节点都有一个特定的标签，用于标识节点的类型。两个节点之间的边指定了两个节点之间的关系。neo4j 的最佳用例是高度嵌入的关系查询。

注意，你可以阅读我关于[建立 go-neo4j 生态系统](https://medium.com/@angadsharma1016/optimizing-go-neo4j-concurrency-patterns-810dff25f88f)的文章作为入门。

**采用的障碍**

大约在去年，我开始着手我最大的项目之一。由于我的学习机制只涉及在现实生活项目中使用新技术，我非常热衷于尝试图形数据库。就在那时，我遇到了后来成为我项目支柱的 neo4j。但是达到这一步并不容易，主要是因为以下障碍:

*   缺乏关于 golang 中 neo4j 驱动程序的全面文档(我的项目就是用它编写的)。
*   与 SQL 数据库相关的总体写性能问题。
*   缺乏复杂的索引机制。
*   垃圾收集暂停。

[![Graph story](img/bd4edde021aa27060428f9c44f373947.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--67HkFAF7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6rs8gyz6hba7b3syzxn8.png)

**了解原因**

Neo4j 是一个易于阅读的数据库。这意味着它在读取数据时速度极快。以至于它为实时图形用例铺平了道路。

虽然读起来很友好，但写仍然是有代价的。主要是因为以下原因:

*   Neo4j 遵循主从架构，写操作总是在主机上完成。即使您尝试在从机上写入，它也会在内部级联到主机。当主人去世时，会自动选出一个新的主人。

[![Master](img/08c22e2a24fe393c07ed92654642a0de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dK-qKZCn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qipsitci6vyhc65m955s.jpg)

*   所有数据都驻留在每台机器上。这是为了保护引用完整性。一旦数据集搜索查询变得大于可用的 RAM，系统将会显著变慢。

*   Neo4j 也没有针对搜索进行优化。与 elasticsearch 等技术相比，情况更是如此。

*   参见关于垃圾收集器暂停的要点[这里的](https://qr.ae/TWrgCn)

**解决应用层面的一些问题**

为了解决写延迟的问题，我确保我写的所有函数并发执行。但这并没有阻止仍然存在瓶颈的事实，因为写入总是在一个节点上完成。这就提出了相互排斥的问题。

虽然 neo4j 不提供数据读锁，但它提供写锁。但是在许多驱动程序实现中，锁是显式的。所以我花了很多时间调试我的代码，寻找导致我的批量写崩溃的错误。然后我在应用层本身实现了`mutex locks`。事情就像下面这样简单:

```
// create a new node with given label and participant data struct (FOR COORDINATORS)
func CreateParticipant(e Event, label string, c chan error, mutex *sync.Mutex) {

    mutex.Lock()

        // Critical section begins

    _, err := con.ExecNeo(`MATCH(a:EVENT) WHERE a.name=$EventName
    CREATE (n:INCHARGE {name:$name, registrationNumber:$registrationNumber,
        email:$email, phoneNumber:$phoneNumber, gender: $gender})<-[:`+label+`]-(a) `, map[string]interface{}{
        "EventName":          e.Name,
        "name":               e.GetField(label, "Name"),
        "registrationNumber": e.GetField(label, "RegistrationNumber"),
        "email":              e.GetField(label, "Email"),
        "phoneNumber":        e.GetField(label, "PhoneNumber"),
        "gender":             e.GetField(label, "Gender"),
    })
    if err != nil {
        c <- err
        return
    }

        // Critical section ends

    mutex.Unlock()

    log.Printf("Created %s node", label)
    c <- nil
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我一次执行数百个 goroutines 的权力，而不用担心相互排斥。尽管这可以在数据库级别上实现，但当时并不明显。项目时间表要求我遵循*让它工作*的思想，但在这个过程中我学到了很多关于 neo4j 的知识。

**结论**

在 SQLites 和 Mongooses 的世界中，Neo4j 是一个相对较新的概念，它有可能成为最具表现力的数据库之一，尤其是在数据可视化的萌芽阶段。虽然它有自己的注意事项，但其中很多都可以在应用程序级别上处理。虽然一些驱动程序实现的文档还有很多需要改进的地方，但是最终，没有什么是有针对性的 google 搜索不能解决的。