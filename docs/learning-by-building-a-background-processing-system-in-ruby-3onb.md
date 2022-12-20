# Ruby 后台处理系统构建学习

> 原文：<https://dev.to/appsignal/learning-by-building-a-background-processing-system-in-ruby-3onb>

在今天的帖子中，我们将实现一个天真的后台处理系统，以此来取乐！在这个过程中，我们可能会了解一些流行的后台处理系统的内部情况，比如 [Sidekiq](http://sidekiq.org) 。这种乐趣的产品绝不是为了生产使用。

假设我们的应用程序中有一个任务，加载一个或多个网站并提取它们的标题。由于我们对这些网站的性能没有任何影响，我们希望在我们的主线程之外(或者当前请求——如果我们正在构建一个 web 应用程序)但在后台执行任务。

## 封装任务

在我们进入后台处理之前，让我们构建一个服务对象来执行手头的任务。我们将使用 [OpenURI](https://ruby-doc.org/stdlib-2.6.2/libdoc/open-uri/rdoc/OpenURI.html) 和 [Nokogiri](https://nokogiri.org) 来提取标题标签的内容。

```
require 'open-uri' 
require 'nokogiri' 

class TitleExtractorService
  def call(url)
    document = Nokogiri::HTML(open(url))
    title = document.css('html > head > title').first.content
    puts title.gsub(/[[:space:]]+/, ' ').strip
  rescue
    puts "Unable to find a title for #{url}" 
  end
end 
```

调用服务打印给定 URL 的标题。

```
TitleExtractorService.new.call('https://appsignal.com')
# AppSignal: Application Performance Monitoring for Ruby on Rails and Elixir 
```

这和预期的一样，但是让我们看看是否可以改进一下语法，使它看起来和感觉上更像其他后台处理系统。通过创建一个`Magique::Worker`模块，我们可以向服务对象添加一些语法糖。

```
module Magique
  module Worker
    def self.included(base)
      base.extend(ClassMethods)
    end

    module ClassMethods
      def perform_now(*args)
        new.perform(*args)
      end
    end

    def perform(*)
      raise NotImplementedError
    end
  end
end 
```

该模块向 worker 实例添加了一个`perform`方法，向 worker 类添加了一个`perform_now`方法，以使调用变得更好。

让我们将该模块包含到我们的服务对象中。既然这样，我们也把它重命名为`TitleExtractorWorker`，把`call`方法改为`perform`。

```
class TitleExtractorWorker
  include Magique::Worker

  def perform(url)
    document = Nokogiri::HTML(open(url))
    title = document.css('html > head > title').first.content
    puts title.gsub(/[[:space:]]+/, ' ').strip
  rescue
    puts "Unable to find a title for #{url}"
  end
end 
```

调用仍然有相同的结果，但是更清楚地说明了发生了什么。

```
TitleExtractorWorker.perform_now('https://appsignal.com')
# AppSignal: Application Performance Monitoring for Ruby on Rails and Elixir 
```

## 实现异步处理

既然我们已经完成了标题提取，我们可以从过去的 Ruby Magic 文章中获取所有标题。为了做到这一点，让我们假设我们有一个包含所有过去文章的 URL 列表的`RUBYMAGIC`常量。

```
RUBYMAGIC.each do |url|
  TitleExtractorWorker.perform_now(url)
end

# Unraveling Classes, Instances and Metaclasses in Ruby | AppSignal Blog
# Bindings and Lexical Scope in Ruby | AppSignal Blog
# Building a Ruby C Extension From Scratch | AppSignal Blog
# Closures in Ruby: Blocks, Procs and Lambdas | AppSignal Blog
# ... 
```

我们得到了过去文章的标题，但是要把它们全部提取出来需要一段时间。这是因为我们会等到每个请求都完成后，再继续下一个请求。

让我们通过在 worker 模块中引入一个`perform_async`方法来改进这一点。为了加快速度，它为每个 URL 创建了一个新线程。

```
module Magique
  module Worker
    module ClassMethods
      def perform_async(*args)
        Thread.new { new.perform(*args) }
      end
    end
  end
end 
```

在将调用改为`TitleExtractorWorker.perform_async(url)`之后，我们几乎立刻得到了所有的标题。然而，这也意味着我们同时打开了 20 多个到 Ruby Magic 博客的连接。(*各位，很抱歉打扰了你们的博客！*😅)

*如果您正在跟随自己的实现并在长时间运行的流程之外进行测试(比如 web 服务器)，不要忘记在脚本的末尾添加类似`loop { sleep 1 }`的东西，以确保流程不会立即终止。*

## 排队任务

通过为每次调用创建一个新线程的方法，我们最终将达到资源极限(在我们这边和我们访问的网站上都是如此)。因为我们想成为好公民，所以让我们将实现改为异步的，但感觉不像拒绝服务攻击的东西。

解决这个问题的一个常见方法是使用生产者/消费者模式。一个或多个生产者将任务推送到队列中，而一个或多个消费者从队列中取出任务并处理它们。

队列基本上是一个元素列表。理论上，一个简单的数组就可以完成这项工作。然而，当我们处理并发时，我们需要确保一次只有一个生产者或消费者可以访问队列。如果我们对此不小心，事情将在混乱中结束——就像两个人试图同时挤过一扇门。

这个问题被称为[生产者-消费者问题](https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem)，有多种解决方案。幸运的是，这是一个非常常见的问题，Ruby 附带了一个合适的`Queue`实现，我们可以使用它而不必担心线程同步。

要使用它，让我们确保生产者和消费者都可以访问队列。我们通过向我们的`Magique`模块添加一个类方法并给它分配一个`Queue`的实例来做到这一点。

```
module Magique
  def self.backend
    @backend
  end

  def self.backend=(backend)
    @backend = backend
  end
end

Magique.backend = Queue.new 
```

接下来，我们修改我们的`perform_async`实现，将一个任务推到队列上，而不是创建它自己的新线程。一个任务被表示为一个散列，包括对 worker 类的引用以及传递给`perform_async`方法的参数。

```
module Magique
  module Worker
    module ClassMethods
      def perform_async(*args)
        Magique.backend.push(worker: self, args: args)
      end
    end
  end
end 
```

这样，我们就完成了制作方的工作。接下来，我们来看看消费端。

每个使用者都是一个单独的线程，从队列中取出任务并执行它们。不是像线程一样在一个任务后停止，而是消费者从队列中取出另一个任务并执行它，等等。这里是一个名为`Magique::Processor`的消费者的基本实现。每个处理器创建一个无限循环的新线程。对于每次迭代，它试图从队列中抓取一个新任务，创建 worker 类的一个新实例，并使用给定的参数调用它的`perform`方法。

```
module Magique
  class Processor
    def self.start(concurrency = 1)
      concurrency.times { |n| new("Processor #{n}") }
    end

    def initialize(name)
      thread = Thread.new do
        loop do
          payload = Magique.backend.pop
          worker_class = payload[:worker]
          worker_class.new.perform(*payload[:args])
        end
      end

      thread.name = name
    end
  end
end 
```

除了处理循环之外，我们还添加了一个叫做`Magique::Processor.start`的便利方法。这允许我们一次启动多个处理器。虽然命名线程不是真正必要的，但它将允许我们看到事情是否真的如预期的那样工作。

让我们调整`TitleExtractorWorker`的输出，以包含当前线程的名称。

```
puts "[#{Thread.current.name}] #{title.gsub(/[[:space:]]+/, ' ').strip}" 
```

为了测试我们的后台处理设置，我们首先需要在任务排队之前启动一组处理器。

```
Magique.backend = Queue.new
Magique::Processor.start(5)

RUBYMAGIC.each do |url|
  TitleExtractorWorker.perform_async(url)
end

# [Processor 3] Bindings and Lexical Scope in Ruby | AppSignal Blog
# [Processor 4] Building a Ruby C Extension From Scratch | AppSignal Blog
# [Processor 1] Unraveling Classes, Instances and Metaclasses in Ruby | AppSignal Blog
# [Processor 0] Ruby's Hidden Gems, StringScanner | AppSignal Blog
# [Processor 2] Fibers and Enumerators in Ruby: Turning Blocks Inside Out | AppSignal Blog
# [Processor 4] Closures in Ruby: Blocks, Procs and Lambdas | AppSignal Blog
# ... 
```

当这个运行时，我们仍然得到所有文章的标题。虽然它没有为每个任务使用单独的线程那么快，但仍然比没有后台处理的初始实现要快。由于添加了处理器名称，我们还可以确认所有处理器都在队列中工作。通过调整并发处理器的数量，可以在处理速度和现有资源限制之间找到平衡。

## 扩展到多个流程和机器

到目前为止，我们的后台处理系统的当前实现工作得足够好。尽管如此，它仍然局限于相同的过程。资源密集型任务仍然会影响整个进程的性能。最后一步，让我们看看如何在多个进程甚至多台机器上分配工作负载。

队列是生产者和消费者之间的唯一联系。目前，它使用的是内存实现。让我们从 Sidekiq 中获得更多灵感，使用 [Redis](http://redis.org) 实现一个队列。

Redis 支持列表，允许我们从列表中推送和获取任务。此外，Redis Ruby gem 是线程安全的，用于修改列表的 Redis 命令是原子的。这些属性使我们可以将它用于我们的异步后台处理系统，而不会遇到同步问题。

让我们创建一个 Redis 支持的队列，它实现了`push`和`shift`方法，就像我们之前使用的`Queue`一样。

```
require 'json'
require 'redis'

module Magique
  module Backend
    class Redis
      def initialize(connection = ::Redis.new)
        @connection = connection
      end

      def push(job)
        @connection.lpush('magique:queue', JSON.dump(job))
      end

      def shift
        _queue, job = @connection.brpop('magique:queue')
        payload = JSON.parse(job, symbolize_names: true)
        payload[:worker] = Object.const_get(payload[:worker])
        payload
      end
    end
  end
end 
```

由于 Redis 对 Ruby 对象一无所知，我们必须先将我们的任务序列化到 JSON 中，然后使用`lpush`命令将它们存储在数据库中，该命令将一个元素添加到列表的前面。

为了从队列中获取任务，我们使用了`brpop`命令，它从列表中获取最后一个元素。如果列表是空的，它将被阻塞，直到有新的元素可用。当没有任务可用时，这是暂停处理器的好方法。最后，从 Redis 中获得一个任务后，我们必须使用`Object.const_get`根据 worker 的名字来查找真正的 Ruby 类。

最后一步，让我们把事情分成多个过程。在生产者方面，我们唯一要做的事情是将后端更改为我们新实现的 Redis 队列。

```
# ...

Magique.backend = Magique::Backend::Redis.new

RUBYMAGIC.each do |url|
  TitleExtractorWorker.perform_async(url)
end 
```

在消费者方面，我们可以用下面几行代码来摆脱它:

```
# ...

Magique.backend = Magique::Backend::Redis.new
Magique::Processor.start(5)

loop { sleep 1 } 
```

执行时，消费者进程将等待新工作到达队列。一旦我们启动将任务推入队列的生产者进程，我们可以看到它们立即得到处理。

## 负责任地享受，不要把这个用在生产上

虽然我们让它远离您将在生产中使用的真实世界设置(所以不要！)，我们在构建后台处理器时采取了几个步骤。我们首先让一个流程作为后台服务运行。然后我们使它异步，并使用`Queue`来解决生产者-消费者问题。然后，我们使用 Redis 将该过程扩展到多个进程或机器，而不是在内存中实现。

如前所述，这是后台处理系统的简化实现。有很多东西遗漏了，没有明确处理。这些包括(但不限于)错误处理、多队列、调度、连接池和信号处理。

尽管如此，我们写这篇文章的时候很开心，希望你能喜欢这个后台处理系统。也许你甚至带走了一两件东西。

*客座撰稿人[贝内迪克特·德克](http://benediktdeicke.com)是一名软件工程师，也是 [Userlist.io](http://userlist.io) 的 CTO。另外，他正在写一本关于用 Ruby on Rails 构建 [SaaS 应用程序的书。你可以通过](http://saasguidebook.com) [Twitter](http://twitter.com/benediktdeicke) 联系 Benedikt。*