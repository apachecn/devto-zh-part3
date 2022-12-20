# 用 GTest 进行参数化测试

> 原文：<https://dev.to/sandordargo/parameterized-testing-with-gtest-50l9>

**你想每天出一道 C++题，为下一次面试做准备吗？免费注册[每日新闻采访](https://www.dailycppinterview.com/)！**

对于我们部门的一个最新的道场，我们选择了一个相对简单的形来帮助新人加入。我们在随机风格的[闰年形](http://codingdojo.org/kata/LeapYears/)上工作，这意味着我们只用了一台电脑——我们有 9 个人。

我们还应用了一些额外的约束，比如如果每三分钟后我们的测试不是绿色的(除了红色阶段，当我们不得不写一个失败的测试)，我们必须用`git reset --hard`清除我们的更改。

即使有一些没有提到的额外限制，这个形也不需要花一个半小时来实现，所以我们有额外的时间来尝试一些新的东西。根据标题你可能已经猜到了，我们正在 GoogleTest 中试验[参数化测试。](https://github.com/sandordargo/parameterizedTestExamplesCpp)

## 如何在没有参数化测试的情况下，让我们的测试不那么重复？

要回答的第一个问题是什么是参数化测试，但在此之前，让我们看看为什么我们需要它们。

*如果你想直接进入参数化测试，[跳到下一节](#parameterizedtests)。*

假设您有几个非常相似的测试，如下所示:

```
#include <gtest/gtest.h>  
#include <LeapYearCalendar.h>  
TEST(LeapYearTests, 1IsOdd_IsNotLeapYear) {
  LeapYearCalendar leapYearCalendar;
  ASSERT_FALSE(leapYearCalendar.isLeap(1));
}

TEST(LeapYearTests, 711IsOdd_IsNotLeapYear) {
  LeapYearCalendar leapYearCalendar;
  ASSERT_FALSE(leapYearCalendar.isLeap(711));
}

TEST(LeapYearTests, 1989IsOdd_IsNotLeapYear) {
  LeapYearCalendar leapYearCalendar;
  ASSERT_FALSE(leapYearCalendar.isLeap(1989));
}

TEST(LeapYearTests, 2013IsOdd_IsNotLeapYear) {
  LeapYearCalendar leapYearCalendar;
  ASSERT_FALSE(leapYearCalendar.isLeap(2013));
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所观察到的，只有两件事情发生了变化:

*   输入
*   以及预期的结果。

[重构一点](https://amzn.to/2EhV5s8)减少代码重复不是很好吗？

毫无疑问，这将是令人敬畏的！

但是怎么做呢？

你可能会从不同的方向出发。

### 使用夹具

让代码干燥的一个可能方法是创建一个 fixture，去掉`Foo`的初始化。

```
 #include <gtest/gtest.h>  
#include <LeapYearCalendar.h>  
class LeapYearFixtureTests : public ::testing::Test {
protected:
    LeapYearCalendar leapYearCalendar;
};

TEST_F(LeapYearFixtureTests, 1IsOdd_IsNotLeapYear) {
    ASSERT_FALSE(leapYearCalendar.isLeap(1));
}

TEST_F(LeapYearFixtureTests, 711IsOdd_IsNotLeapYear) {
    ASSERT_FALSE(leapYearCalendar.isLeap(711));
}

TEST_F(LeapYearFixtureTests, 1989IsOdd_IsNotLeapYear) {
    ASSERT_FALSE(leapYearCalendar.isLeap(1989));
}

TEST_F(LeapYearFixtureTests, 2013IsOdd_IsNotLeapYear) {
    ASSERT_FALSE(leapYearCalendar.isLeap(2013));
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个进步，我们不再需要在每个测试中实例化`leapYearCalendar`,它由 fixture 执行。我们可能决定不再修改，但是代码看起来还是很重复。

### 美好的旧`for`循环

另一个选择是在测试用例中创建一个年份列表，并对其进行迭代。

```
#include <gtest/gtest.h>  
#include <LeapYearCalendar.h>  
TEST(LeapYearIterationTest, OddYearsAreNotLeapYears) {
    LeapYearCalendar leapYearCalendar;
    auto oddYears = std::vector<int>{1, 3, 711, 2013};
    for (auto oddYear :  oddYears) {
        ASSERT_FALSE(leapYearCalendar.isLeap(oddYear));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就重复性而言，在我看来，这段代码更好，更密集，但可读性很强。但是它有一个很大的缺陷！一个好的单元测试应该只有一个逻辑断言——和往常一样，也有一些例外。另一方面，在这种情况下，我们有多个不同的断言，不应该合并成一个。

我们可能会说这是一个理论问题，但它也有一个实际问题。假设我们的第二次迭代测试失败了。接下来会发生什么？我们的测试被停止，所有其他值将不会被测试。我们错过了其他 4 个值的反馈。

你可能会说我们可以通过使用宏`EXPECT_FALSE`来解决这个问题，但是你将得到的错误信息并不是最优的。

```
[ RUN      ] LeapYearIterationTest.OddYearsAreNotLeapYears
/home/sdargo/personal/dev/LeapYear/tests/TestLeapyearIteration.cpp:8: Failure
Value of: leapYearCalendar.isLeap(oddYear)
  Actual: true
Expected: false
[  FAILED  ] LeapYearIterationTest.OddYearsAreNotLeapYears (0 ms) 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至不知道哪个迭代失败了！

## 参数化测试，它们是什么？[参数化测试]

我们能把干循环的优点和独立测试的优点结合起来，而没有缺点吗？

不完全是。但是使用来自 GoogleTest 的参数化测试绝对是您应该考虑的一个选项..

我们有两种不同的方法来使用这个特性。一种方法是从头开始构建我们的测试，另一种方法是在一个 [`FIXTURE`](https://github.com/google/googletest/blob/master/googletest/samples/sample5_unittest.cc) 的基础上构建它们，就像我们在引入一个通用的`leapYear`变量时已经看到的那样。让我们一个一个来看这两个选项。

### 编写没有夹具的参数化测试

在这种情况下，我们没有现有的夹具，也不需要。

让我们继续测试[闰年形](http://codingdojo.org/kata/LeapYears/)。

首先，我们需要创建我们的参数化测试类。让我们称它为`LeapYearParametrizedTests`，它从`::testing::TestWithParam<T>`继承而来。`T`是一个模板参数，它将是我们想要传递到每次迭代中的参数类型。让我们从一个简单的例子开始，这里的参数类型是 integer。

```
class LeapYearParameterizedTestFixture :public ::testing::TestWithParam<int> {
protected:
    LeapYearCalendar leapYearCalendar;
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个包含断言的测试用例。

```
TEST_P(LeapYearParameterizedTestFixture, OddYearsAreNotLeapYears) {
    int year = GetParam();
    ASSERT_FALSE(leapYearCalendar.isLeap(year));
} 
```

Enter fullscreen mode Exit fullscreen mode

对于一个普通的单元测试，我们使用`TEST()`宏和`TEST_F()`夹具，我们必须使用`TEST_P()`进行参数化测试。
作为第一个参数，我们必须传递测试类的名称，作为第二个参数，我们必须为我们的测试代表的内容选择一个好的名称。

为了从值列表中检索参数(我们将在几秒钟内定义)，我们必须使用`GetParam()`。

到目前为止，一切顺利！现在，我们不需要其他任何东西，而是用——最好是——多个输入来调用我们的用例。

```
INSTANTIATE_TEST_CASE_P(
        LeapYearTests,
        LeapYearParameterizedTestFixture,
        ::testing::Values(
                1, 711, 1989, 2013
        )); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们调用`INSTANTIATE_TEST_CASE_P`宏，首先使用一个惟一的名称来实例化测试套件。这个名称可以区分多个实例化。在测试输出中，实例化名称——在本例中是`LeapYearTests`——作为前缀被添加到测试套件名称`LeapYearParameterizedTestFixture`中。

最后但同样重要的是，我们必须列出我们想要测试的不同输入。

*从 1.10 版本开始`INSTANTIATE_TEST_CASE_P`被替换为`INSTANTIATE_TEST_SUITE_P`！*

瞧，就这么简单！这是完整的例子。我包含了一个闰年实现，所以如果你有 GTest，你可以很容易地运行它。你也可以参考 [my GitHub repo](https://github.com/sandordargo/parameterizedTestExamplesCpp) 获取编译和运行它的代码和说明。

```
#include <gtest/gtest.h>  
#include <LeapYearCalendar.h>  
class LeapYearParameterizedTestFixture :public ::testing::TestWithParam<int> {
protected:
    LeapYearCalendar leapYearCalendar;
};

TEST_P(LeapYearParameterizedTestFixture, OddYearsAreNotLeapYears) {
    int year = GetParam();
    ASSERT_FALSE(leapYearCalendar.isLeap(year));
}

INSTANTIATE_TEST_CASE_P(
        LeapYearTests,
        LeapYearParameterizedTestFixture,
        ::testing::Values(
                1, 711, 1989, 2013
        )); 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看输出:

```
[----------] 4 tests from LeapYearTests/LeapYearParameterizedTestFixture
[ RUN      ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/0
[       OK ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/0 (0 ms)
[ RUN      ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/1
[       OK ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/1 (0 ms)
[ RUN      ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/2
[       OK ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/2 (0 ms)
[ RUN      ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/3
[       OK ] LeapYearTests/LeapYearParameterizedTestFixture.OddYearsAreNotLeapYears/3 (0 ms)
[----------] 4 tests from LeapYearTests/LeapYearParameterizedTestFixture (0 ms total) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以观察到每个测试名称由 3 部分组成:

*   套房名称
*   测试名称
*   从 0 开始的每次迭代的次数

如果你想要多个测试场景，你必须为每个场景创建一个套件，因为套件中的每个测试都会被触发。我们可以有把握地假设，不同的测试会用相同的输入产生不同的结果。

### 编写基于现有夹具的参数化测试

可能你已经有了一个可用的测试夹具，比如这个:

```
class LeapYearTestFixtureToBeParameterized : public ::testing::Test
{
protected:
  LeapYearCalendar leapYearCalendar;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，非常简单，fixture 本身只是帮助避免在每个不同的测试用例中声明闰年对象。丢失它没什么大不了的，但是您可能有一个更复杂的设置。

提醒一下，下面是真正紧凑的夹具测试:

```
TEST_F(LeapYearTestFixtureToBeParameterized, 1996_IsDivisibleBy4_ShouldBeALeapYear) {
  ASSERT_TRUE(leapYearCalendar.isLeap(1996));
}

TEST_F(LeapYearTestFixtureToBeParameterized, 1700_IsDivisibleBy100AndNotBy400_ShouldNotBeALeapYear) {
  ASSERT_FALSE(leapYearCalendar.isLeap(1700));
}

TEST_F(LeapYearTestFixtureToBeParameterized, 1600_IsDivisibleBy400_ShouldBeALeapYear) {
  ASSERT_TRUE(leapYearCalendar.isLeap(1600));
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，首先我们决定有一个夹具，我们可以很好地命名我们的测试用例，以记录为什么有些是闰年，而有些不是闰年。

然后，我们认为有一些用例，我们想用许多不同的值来测试。百米...我们做什么呢

我们可以在这里或者在另一个文件中创建我们的参数化测试，这没关系。但我们将无法访问`leapYearCalendar`。

抛开丑陋的全局变量，我们还能做什么？

我们可以继承`::testing::WithParamInterface<T>`而不是`::testing::TestWithParam<T>`！

```
class LeapYearTestFixtureToBeParameterized : public ::testing::Test
{
protected:
  LeapYearCalendar leapYearCalendar;
};

class LeapYearParametrizedTestFixtureBasedOnFixture :
  public LeapYearTestFixtureToBeParameterized,
  public ::testing::WithParamInterface<int> {
}; 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果你不需要单独的夹具，你可以把两个类合并成一个:

```
class LeapYearParametrizedFixture :
        public ::testing::Test,
        public ::testing::WithParamInterface<int> {
protected:
    LeapYear leapYearCalendar;            
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可能会说拥有一个参数化的 fixture 没有多大意义。毕竟，我们说过每个测试需要不同的套件，所以没有什么可分享的，不会有不同的测试。

因此，从 fixture 继承可能更有意义。在 fixture 中，我们删除了一些代码重复，在参数化套件中，我们可以从 fixture 的代码中受益。

```
class LeapYearTestFixtureToBeParameterized : public ::testing::Test
{
protected:
    LeapYear leapYearCalendar;
};

TEST_F(LeapYearTestFixtureToBeParameterized, 1996_IsDivisibleBy4_ShouldBeALeapYear) {
    ASSERT_TRUE(leapYearCalendar.isLeap(1996));
}

TEST_F(LeapYearTestFixtureToBeParameterized, 1700_IsDivisibleBy100AndNotBy400_ShouldNotBeALeapYear) {
    ASSERT_FALSE(leapYearCalendar.isLeap(1700));
}

TEST_F(LeapYearTestFixtureToBeParameterized, 1600_IsDivisibleBy400_ShouldBeALeapYear) {
    ASSERT_TRUE(leapYearCalendar.isLeap(1600));
}

class LeapYearParameterizedTestFixture :
        public LeapYearTestFixtureToBeParameterized,
        public ::testing::WithParamInterface<int> {
protected:
    LeapYear leapYearCalendar;            
};

TEST_P(LeapYearParameterizedTestFixture, OddYearsAreNotLeapYears) {
    int year = GetParam();
    ASSERT_FALSE(leapYearCalendar.isLeap(year));
}

INSTANTIATE_TEST_CASE_P(
        LeapYearTests,
        LeapYearParameterizedTestFixture,
        ::testing::Values(
                1, 711, 1989, 2013
        )); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道为什么我们使用`WithParamInterface<T>`而不是`TestWithParam<T>`，这里有答案。`TestWithParam<T>`继承了`Test`和`WithParamInterface<T>`的优点。我们在上一个例子中继承的 fixture 已经从`Test`继承了。所以我们从父母那里继承了`Test`,这就成了一个模糊的基础。

[![Test Class Hierarchy](img/2947abe6ea45563ab7e3c20b9d5a8d68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ltm7UF4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7B%20site.baseurl%20%7D%7D/assets/img/test-class-hierarchy.jpg)

### 如何给同一个测试用例传递多个参数？

假设您有两个想要参数化的输入，或者您想要同时传递输入和输出！你能做什么？

您不能向`TestWithParam<T>`传递一个以上的模板参数，但是您总是可以传递一个`std::pair`，或者更好的是一个具有任意多个成员的`std::tuple`。

这里有一个例子:

```
class LeapYearMultipleParametersTests :public ::testing::TestWithParam<std::tuple<int, bool>> {
protected:
    LeapYearCalendar leapYearCalendar;
};

TEST_P(LeapYearMultipleParametersTests, ChecksIfLeapYear) {
    bool expected = std::get<1>(GetParam());
    int year = std::get<0>(GetParam());
    ASSERT_EQ(expected, leapYearCalendar.isLeap(year));
}

INSTANTIATE_TEST_CASE_P(
        LeapYearTests,
        LeapYearMultipleParametersTests,
        ::testing::Values(
                std::make_tuple(7, false),
                std::make_tuple(2001, false),
                std::make_tuple(1996, true),
                std::make_tuple(1700, false),
                std::make_tuple(1600, true))); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`GetParam()`检索元组。为了获得一个元组的元素，我们可以使用 [std::get](https://en.cppreference.com/w/cpp/utility/tuple/get) 。或者我们甚至可以从 C++17 开始使用结构化投标:

```
auto [year, expected] = GetParam(); 
```

Enter fullscreen mode Exit fullscreen mode

单元测试有多个目标。一方面，当你改变代码时，他们给你信心。您的覆盖率越高，您就越有信心您的更改不会引入 bug。

另一方面，单元测试也记录了你的代码，它给出了关于它应该如何被使用和如何表现的最好的文档。与书面文档不同，它不会过时，因为它不会再被编译。

传入的元组越大，参数化测试记录的代码就越少。每增加一个新参数，就更难理解当前测试的内容，如果出现故障，就更难理解哪里出了问题。

我不是说参数化测试是邪恶的。我只是说它有自己的妥协。

## 外卖

在本文中，我们发现了如何用 GoogleTest 编写参数化测试。当然，GoogleTest 并不是唯一可以用来以简单方式实现这种测试的库， [boost::unit_test](https://www.boost.org/doc/libs/1_64_0/libs/test/doc/html/boost_test/tests_organization/test_cases/param_test.html) 和 [Catch2](https://github.com/catchorg/Catch2/tree/master/docs) 也有这个不错的特性。在后面的文章中，我可能会展示它们。

参数化测试是从测试套件中消除代码重复的一个很好的工具。当您想对许多不同的输出测试本质上相同的行为时，它们就派上了用场。

正如我们所看到的，我们也可以将输出参数化，但是参数化测试的主要缺点就出现了。测试套件有一个名称，对于每组参数，它将是完全相同的名称。如果有一个失败了，你就不会从测试名中得到提示。

有时这是可以接受的，有时你想寻找另一种解决方案。

你可以从这个 GitHub 库下载并实验上面的例子。

## 连接更深

如果你喜欢这篇文章，请

*   点击“喜欢”按钮，
*   [订阅我的简讯](http://eepurl.com/gvcv1j)
*   让我们在推特上联系吧！