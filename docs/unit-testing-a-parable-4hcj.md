# 单元测试的寓言

> 原文：<https://dev.to/carlfish/unit-testing-a-parable-4hcj>

某一天，某个地方，一个开发人员正在编写一个简单的帮助函数来计算两个整数之间的(整数)中点。

```
object Util {
  def average(a: Int, b: Int): Int = (a + b) / 2
} 
```

他们知道，如果没有单元测试，他们的代码评审员就会找他们，所以他们尽职尽责地写了一个。

```
class UtilSpec extends FreeSpec with Matchers {
  import Util._

  "calculating an average" - {
    "comes up with the right result" in {
      average(2, 4) shouldBe 3
    }
  }
} 
```

它过去了。

```
[info] UtilSpec:
[info] calculating an average
[info] - comes up with the right result
[info] All tests passed. 
```

开发者已经实现了 100%的测试覆盖率！但他们仍然感到内疚。这是有史以来最糟糕的“快乐之路”测试！当然，他们需要更多的测试来证明不同类型输入的功能。

所以我们的开发者用更多的测试来减轻他们的负罪感。

```
"calculating an average" - {
  "comes up with the right result" in {
    average(2, 4) shouldBe 3
    average(4, 2) shouldBe 3
  }

  "works if either of the numbers is zero" in {
    average(0, 0) shouldBe 0
    average(0, 4) shouldBe 2
    average(4, 0) shouldBe 2
  }

  "works for positive and negative numbers" in {
    average(-2, -4) shouldBe -3
    average(-4, 2) shouldBe -1
    average(2, -2) shouldBe 0
  }

  "rounds in the expected direction" in {
     average(2, 3) shouldBe 2
     average(-2, -3) shouldBe -2
  }
} 
```

他们也通过了。

```
[info] UtilSpec:
[info] calculating an average
[info] - comes up with the right result
[info] - works if one of the numbers is zero
[info] - works for positive and negative numbers
[info] - rounds in the expected direction
[info] All tests passed. 
```

“喏，这样好多了！”四个测试，不下十个独立的断言，显示代码在各种不同的情况下都能工作。这应该会让评论者高兴。

另一个开发者路过，越过第一个开发者的肩膀，问他们是否可以配对。经过短暂的思考，新的一双眼睛建议删除第一个开发人员编写的所有测试，用一个测试来代替它们:

```
class UtilSpec extends FreeSpec with PropertyChecks with Matchers {
  import Util._

  "calculating an average" - {
    "returns a result between the two operands" in {
      forAll { (x: Int, y: Int) ⇒
        average(x, y) should (be >= Math.min(x, y) and be <= Math.max(x, y))
      }
    }
  }
} 
```

“等一下”，第一个开发者说。“那不是测试正确的事情！我可以写几十个不同的函数来满足这个不变量，但不计算平均值！”

“但是我坐下来之前就知道了”，第二个开发者说。"这些函数都不是你写的."

```
[info] UtilSpec:
[info] calculating an average
[info] - returns a result between the two operands *** FAILED ***
[info]   TestFailedException was thrown during property evaluation.
[info]     Message: -1073741821 was not greater than or equal to 7
[info]     Location: (UtilSpec.scala:19)
[info]     Occurred when passed generated values (
[info]       arg0 = 7, // 8 shrinks
[info]       arg1 = 2147483647
[info]     )
[info] *** 1 TEST FAILED *** 
```