# 代码日 4 的到来

> 原文：<https://dev.to/neilgall/advent-of-code-day-4-2ok4>

今天，我们面对的是事件数据，其中的事件不一定是有序的，它们包含的信息也是部分的。这种场景在通信协议和用户界面代码中很常见。我将这个问题分解如下:

1.  将输入数据解析为事件列表
2.  按时间对事件列表排序
3.  通过一个解释器运行事件，以汇集所有信息
4.  保持按分钟计数的睡眠，这样可以找到最频繁的

将这些事情合并到一个步骤中是可能的，但是我们不知道第 2 部分中会出现什么，将它分成独立的步骤可以保持代码清晰和灵活。

## 解析

当然，我将再次使用解析器组合子的真正方法。对于这些简单的例子来说，这看起来有些矫枉过正，但是在现实世界中，从长远来看，这是值得的。

事件模型是一个[和类型](https://chadaustin.me/2015/07/sum-types/) :

```
sealed class Event {
    abstract val time: LocalDateTime
    data class BeginShift(override val time: LocalDateTime, val guard: Int): Event()
    data class FallAsleep(override val time: LocalDateTime): Event()
    data class WakeUp(override val time: LocalDateTime): Event()
} 
```

Enter fullscreen mode Exit fullscreen mode

几个解析助手:

```
fun <T> Parser<T>.skip(c: Char): Parser<T> = followedBy(isChar(c))
fun <T> Parser<T>.skip(s: String): Parser<T> = followedBy(string(s))
val integer: Parser<Int> = INTEGER.map(String::toInt) 
```

Enter fullscreen mode Exit fullscreen mode

每个事件都有一个日期时间，幸运的是，对于`java.time.LocalDateTime`的一个构造函数方法:
来说，这些字段的顺序完全正确

```
val date: Parser<LocalDateTime> = isChar('[').next(sequence(
    integer.skip('-'),
    integer.skip('-'),
    integer.skip(' '),
    integer.skip(':'),
    integer.skip("] "),
    LocalDateTime::of
)) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个针对每种事件类型的解析器，以及一个选择它们的顶级解析器:

```
val beginShift: Parser<Event> =
    sequence(date.skip("Guard #"), integer.skip(" begins shift"), Event::BeginShift)

val fallAsleep: Parser<Event> =
    date.skip("falls asleep").map(Event::FallAsleep)

val wakeUp: Parser<Event> =
    date.skip("wakes up").map(Event::WakeUp)

val event: Parser<Event> =
    or(beginShift, fallAsleep, wakeUp) 
```

Enter fullscreen mode Exit fullscreen mode

你可以保留你的正则表达式。

## 口译事件

对此的完整设计是构建一个[自由单子](https://softwaremill.com/free-monads/)，其中事件成为不可变状态上的动作。根据我昨天在 Kotlin 中尝试使用不可变地图的经验，我将采取一种折中的方法。我将在部分状态上折叠事件列表，并使用副作用来改变地图。这种代码结构使得一切都非常清晰，并确保您已经考虑了每种状态下所有可能的事件。

首先是一个具有所有捕获信息的警卫模型:

```
data class Guard(val id: Int, val minutesAsleep: Int, val daysByMinute: Map<Int, Int>) 
```

Enter fullscreen mode Exit fullscreen mode

以及用于中间状态的另一种求和类型:

```
sealed class State {
    object Initial: State()
    data class OnDuty(val guard: Guard): State()
    data class Asleep(val guard: Guard, val sleepMinute: Int): State()
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们得到一个 BeginShift 事件时，我们进入 OnDuty 状态，它记住了 guard 对象。当他睡着时，我们进入睡眠状态，和守卫一起捕捉开始的一分钟。在唤醒事件中，我们获得了更新保护和继续所需的所有信息。

```
events.sorted().fold(State.Initial as State) { state, event ->
    when(event) {
        is Event.BeginShift -> {
            val guard = guards[event.guard] ?: Guard(event.guard, 0, mapOf())
            guards[guard.id] = guard
            State.OnDuty(guard)
        }
        is Event.FallAsleep -> when(state) {
            is State.OnDuty -> State.Asleep(state.guard, event.time.minute)
            else -> throw IllegalStateException("FallAsleep event in ${state}")
        }
        is Event.WakeUp -> when(state) {
            is State.Asleep -> {
                val guard = state.guard.sleep(state.sleepMinute .. event.time.minute - 1)
                guards[state.guard.id] = guard
                State.OnDuty(guard)
            }
            else -> throw IllegalStateException("WakeUp event in ${state}")
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里遇到的一件事是一个警卫在值班时可以睡好几次。因此唤醒事件的输出状态是 OnDuty，而不是 Initial。

## 第一部分

在构建了守卫模型之后，回答第 1 部分的问题相当于将策略 1 的描述直接翻译成代码:

```
fun part1(guards: Collection<Guard>): Int {
    val guard = guards.maxBy { g -> g.minutesAsleep }
    val minute = guard.daysByMinute.entries.maxBy { e -> e.value }.key
    return guard.id * minute
} 
```

Enter fullscreen mode Exit fullscreen mode

(为了清楚起见，我省略了一些错误处理——参见完整代码。)

## 第二部分

使用第二种策略寻找答案意味着对我们的数据进行一点重新组织。我们有按分钟排列的睡眠事件，但需要按分钟排列的睡眠事件。用一个小的 struct 来保持事情的清晰，应该很容易转换:

```
data class SleepEvent(val guard: Int, val minute: Int, val count: Int)

val sleepEvents = guards.flatMap { guard ->
    guard.daysByMinute.map { (minute, count) -> SleepEvent(guard.id, minute, count) }
} 
```

Enter fullscreen mode Exit fullscreen mode

又一次计数给出了我们需要的答案。

查看我在 GitHub 上的完整实现