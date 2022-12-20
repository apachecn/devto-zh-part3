# 干——不要重复

> 原文：<https://dev.to/apium_hub/dry-don-t-repeat-yourself-2pjj>

不要重复自己(干)是软件开发的一个原则，它的主要目标是避免代码重复。

> “在一个系统中，每一项知识都必须有一个单一的、明确的、权威的表述”
> 
> 安德鲁·亨特和戴维·托马斯:*《务实的程序员:从熟练工到大师*

基本上，当你发现自己一遍又一遍地写同样的代码时，可能会有更好的方法。

## **实际案例:不要重复自己**

我们来写一个例子，看看是否可以改进，做一些重构，避免重复。

这里有一个简单的报告类，它接收一些数据并通过控制台以格式化的方式打印出来。

```
 class Report
{
   public function show(array $data)
   {
       echo "Report: " . ucwords(strtolower($data["name"])) . "\n";
       echo "Product: " . ucwords(strtolower($data["product"])) . "\n";
       echo "Start date: " . date("Y/m/d", $data["startDate"]) . "\n";
       echo "End date: " . date("Y/m/d", $data["endDate"]) . "\n";
       echo "Total: " . $data["total"] . "\n";
       echo "Average x day: " . floor($data["total"] / 365) . "\n";
       echo "Average x week: " . floor($data["total"] / 52) . "\n";
   }
} 
```

这段代码还可以改进，但是我们暂时还可以。

对报告的新请求:应该保存到文件中。简单对吗？让我们做一些复制和粘贴，一些小的变化，几分钟后我们就完成了:

```
 class Report
{
   public function show(array $data)
   {
       echo "Report: " . ucwords(strtolower($data["name"])) . "\n";
       echo "Product: " . ucwords(strtolower($data["product"])) . "\n";
       echo "Start date: " . date("Y/m/d", $data["startDate"]) . "\n";
       echo "End date: " . date("Y/m/d", $data["endDate"]) . "\n";
       echo "Total: " . $data["total"] . "\n";
       echo "Average x day: " . floor($data["total"] / 365) . "\n";
       echo "Average x week: " . floor($data["total"] / 52) . "\n";
       echo "Average x month: " . floor($data["total"] / 12) . "\n";
   }

   public function saveToFile(array $data)
   {
       $report = '';
       $report .= "Report: " . ucwords(strtolower($data["name"])) . "\n";
       $report .= "Product: " . ucwords(strtolower($data["product"])) . "\n";
       $report .= "Start date: " . date("Y/m/d", $data["startDate"]) . "\n";
       $report .= "End date: " . date("Y/m/d", $data["endDate"]) . "\n";
       $report .= "Total: " . $data["total"] . "\n";
       $report .= "Average x day: " . floor($data["total"] / 365) . "\n";
       $report .= "Average x week: " . floor($data["total"] / 52) . "\n";
       $report .= "Average x month: " . floor($data["total"] / 12) . "\n";

       file_put_contents("./report.txt", $report);
   }
} 
```

等等等等。！！我们真的结束了吗？当然，请求得到了满足，但从技术角度来看，这似乎不太正确。它到处都是重复的(虽然代码只是几束线)。湿无处不在(每件事都写两遍:干的反义词)。

让我们在这里做一些重构。这两种方法基本上是一样的，唯一的区别是最终的输出。这里一个好的开始是提取尸体的新方法。这样我们就有了单一的事实来源:报告将只在一个独特的点上创建。其他方法将只负责决定如何处理报告。

```
 class Report
{
   public function show(array $data)
   {
       echo $this->createReport($data);
   }

   public function saveToFile(array $data)
   {
       file_put_contents("./report.txt", $this->createReport($data));
   }

   private function createReport(array $data): string
   {
       $report = '';
       $report .= "Report: " . ucwords(strtolower($data["name"])) . "\n";
       $report .= "Product: " . ucwords(strtolower($data["product"])) . "\n";
       $report .= "Start date: " . date("Y/m/d", $data["startDate"]) . "\n";
       $report .= "End date: " . date("Y/m/d", $data["endDate"]) . "\n";
       $report .= "Total: " . $data["total"] . "\n";
       $report .= "Average x day: " . floor($data["total"] / 365) . "\n";
       $report .= "Average x week: " . floor($data["total"] / 52) . "\n";
       $report .= "Average x month: " . floor($data["total"] / 12) . "\n";
       return $report;
   }
} 
```

现在好多了，对吧？还有别的吗？有一些小的重复需要修改。

例如，在报告和产品的名称上有相同的转换:

```
$report .= "Report: " . ucwords(strtolower($data["name"])) . "\n";
$report .= "Product: " . ucwords(strtolower($data["product"])) . "\n"; 
```

我们可以将这些转换提取到一个新的方法中(或者更好，提取到一个拥有自己的[单元测试](https://apiumhub.com/tech-blog-barcelona/top-benefits-of-unit-testing/)的库中):

```
 private function normalizeName($name): string
{
   return ucwords(strtolower($name));
} 
```

另一个重复:日期格式。

```
$report .= "Start date: " . date("Y/m/d", $data["startDate"]) . "\n";
$report .= "End date: " . date("Y/m/d", $data["endDate"]) . "\n"; 
```

让我们把它提取出来:

```
 private function formatDate($date): string
{
   return date("Y/m/d", $date);
} 
```

还有最后一个:平均计算。

```
 $report .= "Average x day: " . floor($data["total"] / 365) . "\n";
$report .= "Average x week: " . floor($data["total"] / 52) . "\n";
$report .= "Average x month: " . floor($data["total"] / 12) . "\n"; 
```

虽然计算不完全相同，但我们可以这样做:

```
 private function calculateAverage(array $data, $period): string
{
   return floor($data["total"] / $period);
} 
```

因此，最终的报告类可能是:

```
 class Report
{
   public function show(array $data)
   {
       echo $this->createReport($data);
   }

   public function saveToFile(array $data)
   {
       file_put_contents("./report.txt", $this->createReport($data));
   }

   private function createReport(array $data)
   {
       $report = '';
       $report .= "Report: " . $this->normalizeName($data["name"]) . "\n";
       $report .= "Product: " . $this->normalizeName($data["product"]) . "\n";
       $report .= "Start date: " . $this->formatDate($data["startDate"]) . "\n";
       $report .= "End date: " . $this->formatDate($data["endDate"]) . "\n";
       $report .= "Total: " . $data["total"] . "\n";
       $report .= "Average x day: " . $this->calculateAverage($data, 365) . "\n";
       $report .= "Average x week: " . $this->calculateAverage($data, 52) . "\n";
       $report .= "Average x month: " . $this->calculateAverage($data, 12) . "\n";
       return $report;
   }

   private function formatDate($date): string
   {
       return date("Y/m/d", $date);
   }

   private function calculateAverage(array $data, $period): string
   {
       return floor($data["total"] / $period);
   }

   private function normalizeName($name): string
   {
       return ucwords(strtolower($name));
   }
} 
```

## 三法则

这是一个简单的例子，但我试图说明避免重复的重要性以及我们如何处理它。当然，有时重复可能不那么容易发现。或者为了摆脱它，你可能需要一些更复杂的东西，比如应用一些设计模式。

一个重要的代码重构是三法则。重复一次同样的代码可能就可以了。但是当我们第三次构建相同的代码时，是时候重构和修复重复的代码了。

## 结论:不要重复自己

*   通过所有这些步骤，WET 已经从我们的代码中删除了。
*   如果报告需要任何更新，将只在一个单一的点上完成。不再需要在不同的地方改变相同的东西。
*   小的重复也被删除了。这些方法有有意义的名字来解释它们做什么。更好的代码。
*   这些方法可能会被提取到一些带有单元测试的助手库中。
*   下一次你写代码时，让我们记住 DRY 原则。
*   但是不要忘记三的规则！

如果你有兴趣收到更多关于不要重复自己原则的文章，别忘了在这里订阅我们的每月简讯[。](http://eepurl.com/cC96MY)

## 如果你觉得这篇关于不要重复自己原则的文章很有趣，你可能会喜欢…

函数式 PHP:第一种方法

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

帖子[干——不要重复自己](https://apiumhub.com/tech-blog-barcelona/dry-dont-repeat-yourself/)最先出现在 [Apiumhub](https://apiumhub.com) 上。