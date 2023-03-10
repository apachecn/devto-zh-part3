# 将对象与可观察对象联系起来

> 原文：<https://dev.to/sdball/connecting-objects-with-observable-3643>

今天让我们深入了解一下 Ruby 标准库中的[可观察模块](http://ruby-doc.org/stdlib-2.5.3/libdoc/observer/rdoc/Observable.html)。

# 火箭发射

假设我们正在发射一枚火箭。

幸运的是，我们有一个非常高的抽象层次来处理所有的火箭发射部分。我们只需要向 STDOUT 输出倒计时。好容易！

```
class Countdown
  attr_reader :starting_count

  def initialize(starting_count)
    @starting_count = starting_count
  end

  def run
    starting_count.downto(0) do |count|
      puts count
    end
  end
end

Countdown.new(5).run

$ ruby countdown.rb
5
4
3
2
1
0 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。

# 升空！

但是等等，我们需要通过在 0 后面发出一个特殊的“发射”字符串来触发发射。好吧。

```
class Countdown
  attr_reader :starting_count

  def initialize(starting_count)
    @starting_count = starting_count
  end

  def run
    starting_count.downto(0) do |count|
      puts count
    end
    puts "BLAST OFF"
  end
end

Countdown.new(5).run

$ ruby countdown.rb
5
4
3
2
1
0
BLAST OFF 
```

Enter fullscreen mode Exit fullscreen mode

好的完成了！

# 点火

但是等等，我们需要在数到三的时候触发点火程序。

```
class Countdown
  attr_reader :starting_count

  def initialize(starting_count)
    @starting_count = starting_count
  end

  def run
    starting_count.downto(0) do |count|
      puts count
      if count == 3
        puts "IGNITION"
      end
    end
    puts "BLAST OFF"
  end
end

Countdown.new(5).run

$ ruby countdown.rb
5
4
3
!!! IGNITION !!!
2
1
0
BLAST OFF 
```

Enter fullscreen mode Exit fullscreen mode

# 责任太大

这很有效，但是我们可怜的简单倒数班现在有很多责任。责任太大了！让我们把点火和爆炸的工作转移到其他班。

```
class Countdown
  attr_reader :starting_count,
              :ignition_control,
              :blast_off

  def initialize(starting_count)
    @starting_count = starting_count
    @ignition_control = IgnitionControl.new(3)
    @blast_off = BlastOff.new
  end

  def run
    starting_count.downto(0) do |count|
      puts count
      ignition_control.check(count)
      blast_off.check(count)
    end
  end
end

class IgnitionControl
  attr_reader :ignite_at

  def initialize(ignite_at=0)
    @ignite_at = ignite_at
  end

  def check(count)
    puts "!!! IGNITION !!!" if count == ignite_at
  end
end

class BlastOff
  def check(count)
    puts "BLAST OFF" if count == 0
  end
end

Countdown.new(5).run

$ ruby countdown.rb
5
4
3
!!! IGNITION !!!
2
1
0
BLAST OFF 
```

Enter fullscreen mode Exit fullscreen mode

# 知识太多

这很有效，但是我们可怜的倒数班仍然知道太多关于它的合作者的事情。它应该只关注倒计时，而不是设置点火和发射类。

与其将协作者硬编码成倒计时，不如让我们试着在运行时连接他们。

```
class Countdown
  attr_reader :starting_count, :listeners

  def initialize(starting_count)
    @starting_count = starting_count
    @listeners = []
  end

  def add_listener(listener)
    listeners << listener
  end

  def run
    starting_count.downto(0) do |count|
      puts count
      listeners.each do |listener|
        listener.update(count)
      end
    end
  end
end

class IgnitionControl
  attr_reader :ignite_at

  def initialize(ignite_at=0)
    @ignite_at = ignite_at
  end

  def update(count)
    puts "!!! IGNITION !!!" if count == ignite_at
  end
end

class BlastOff
  def update(count)
    puts "BLAST OFF" if count == 0
  end
end

countdown = Countdown.new(5)
ignition = IgnitionControl.new(3)
blastoff = BlastOff.new

countdown.add_listener(ignition)
countdown.add_listener(blastoff)

countdown.run

$ ruby countdown.rb
5
4
3
!!! IGNITION !!!
2
1
0
BLAST OFF 
```

Enter fullscreen mode Exit fullscreen mode

那好一点了。我们已经为我们的东西改变了 API，使之在运行时计数并添加它们，而不是硬编码到倒计时类中。

现在 Countdown 类从一个空的侦听器列表开始，它有一个允许添加新的侦听对象的方法。每个侦听器都应该有一个`update`方法，在倒计时的每一个数字时，它们都会被调用。还不错！

事实证明，使用这种方法，我们几乎已经实现了 Observable 的基本版本！

让我们切换到真实的东西。当我们这样做的时候，让我们把倒计时的 STDOUT 变成另一个观察者。

# 可观测的倒计时

```
require "observer"

class Countdown
  include Observable

  attr_reader :starting_count

  def initialize(starting_count)
    @starting_count = starting_count
  end

  def run
    starting_count.downto(0) do |count|
      changed
      notify_observers count
    end
  end
end

class TerminalOutput
  def update(count)
    puts count
  end
end

class IgnitionControl
  attr_reader :ignite_at
  def initialize(ignite_at=0)
    @ignite_at = ignite_at
  end

  def update(count)
    puts "!!! IGNITION !!!" if count == ignite_at
  end
end

class BlastOff
  def update(count)
    puts "BLAST OFF" if count == 0
  end
end

countdown = Countdown.new(5)
countdown.add_observer(TerminalOutput.new)
countdown.add_observer(IgnitionControl.new(3))
countdown.add_observer(BlastOff.new)
countdown.run

$ ruby countdown.rb
5
4
3
!!! IGNITION !!!
2
1
0
BLAST OFF 
```

Enter fullscreen mode Exit fullscreen mode

万岁！让我们更仔细地看看使用 Observable 是什么样子的。

*   会发出我们`include Observable`的信号。它处理添加包含被观察对象的数据结构。
*   当我们有一个更新要发出时，我们调用`changed`,它告诉 Observable 它实际上应该用通知来调用观察者。Observable 的一个很好的特性是，只有当类声明了一个变化时，才会发出通知。
*   我们用数据调用`notify_observers`

当从一个声明了一个`change`的可观察对象调用`notify_observers`时，它会在每个监听器上调用`update`,不管它被赋予了什么参数。

```
# simply call update on the observers
notify_observers

# send :hello to the observers
notify_observers :hello

# send :temperature and the current_temperature variable to the observers
notify_observers :temperature, current_temperature 
```

Enter fullscreen mode Exit fullscreen mode

有了 Observable，我们甚至可以轻松地做一些很酷的事情，比如在点火后跳过输出“2”。

```
def run
  starting_count.downto(0) do |count|
    changed unless count == 2
    notify_observers count
  end
end

$ ruby countdown.rb
5
4
3
!!! IGNITION !!!
1
0
BLAST OFF 
```

Enter fullscreen mode Exit fullscreen mode

当然，接下来我们会再次赋予倒计时更多的责任。但是也许倒计时*应该让*知道具体事件应该何时发生。谁知道呢？不是我！

# 许多信号

使用可观察模块，我们可以有几个(或一个)观察者从许多不同的地方获得通知。

```
require "observer"

class Sonar
  include Observable
  attr_reader :label

  def initialize(label)
    @label = label
  end

  def ping
    changed
    notify_observers "ping from SONAR #{label}"
  end
end

class Station
  attr_reader :label

  def initialize(label)
    @label = label
  end

  def update(signal)
    puts "Station #{label}: #{signal} detected"
  end
end

station = Station.new(1)

sonars = 1.upto(9999).map do |n|
  Sonar.new(n).tap { |sonar| sonar.add_observer(station) }
end

sonars.each do |sonar|
  sonar.ping
end

$ ruby many_signals.rb | tail
Station 1: ping from SONAR 9990 detected
Station 1: ping from SONAR 9991 detected
Station 1: ping from SONAR 9992 detected
Station 1: ping from SONAR 9993 detected
Station 1: ping from SONAR 9994 detected
Station 1: ping from SONAR 9995 detected
Station 1: ping from SONAR 9996 detected
Station 1: ping from SONAR 9997 detected
Station 1: ping from SONAR 9998 detected
Station 1: ping from SONAR 9999 detected 
```

Enter fullscreen mode Exit fullscreen mode

# 众多观察者

或者，我们可以让许多观察者都观察一个对象以获得通知。

```
require "observer"

class Sonar
  include Observable
  attr_reader :label

  def initialize(label)
    @label = label
  end

  def ping
    changed
    notify_observers "ping from SONAR #{label}"
  end
end

class Station
  attr_reader :label

  def initialize(label)
    @label = label
  end

  def update(signal)
    puts "Station #{label}: #{signal} detected"
  end
end

sonar = Sonar.new(1)
1.upto(9999) do |n|
  sonar.add_observer(Station.new(n))
end
sonar.ping

$ ruby many_stations.rb | tail
Station 9990: ping from SONAR 1 detected
Station 9991: ping from SONAR 1 detected
Station 9992: ping from SONAR 1 detected
Station 9993: ping from SONAR 1 detected
Station 9994: ping from SONAR 1 detected
Station 9995: ping from SONAR 1 detected
Station 9996: ping from SONAR 1 detected
Station 9997: ping from SONAR 1 detected
Station 9998: ping from SONAR 1 detected
Station 9999: ping from SONAR 1 detected 
```

Enter fullscreen mode Exit fullscreen mode

# 许多许多？两个都好

或者我们可以让许多观察者观察许多可观察的事物！

```
require "observer"

class Sonar
  include Observable
  attr_reader :label

  def initialize(label)
    @label = label
  end

  def ping
    changed
    notify_observers "ping from SONAR #{label}"
  end
end

class Station
  attr_reader :label

  def initialize(label)
    @label = label
  end

  def update(signal)
    puts "Station #{label}: #{signal} detected"
  end
end

COUNT = 100

stations = COUNT.times.map { |n| Station.new(n) }

sonars = COUNT.times.map do |n|
  Sonar.new(n).tap do |sonar|
    stations.each do |station|
      sonar.add_observer(station)
    end
  end
end

sonars.each { |sonar| sonar.ping }

$ ruby many_many.rb | tail
Station 90: ping from SONAR 99 detected
Station 91: ping from SONAR 99 detected
Station 92: ping from SONAR 99 detected
Station 93: ping from SONAR 99 detected
Station 94: ping from SONAR 99 detected
Station 95: ping from SONAR 99 detected
Station 96: ping from SONAR 99 detected
Station 97: ping from SONAR 99 detected
Station 98: ping from SONAR 99 detected
Station 99: ping from SONAR 99 detected 
```

Enter fullscreen mode Exit fullscreen mode

到处都是信号！哇！

Observable 模块是 Ruby 标准库中的一个很好的工具。如果你曾经发现自己在编写允许对象对其他对象的变化做出反应的机制，你很可能会发现可观察性正是你正在寻找的。