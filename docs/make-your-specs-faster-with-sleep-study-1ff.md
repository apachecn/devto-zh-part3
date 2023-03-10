# 通过睡眠研究让你的眼镜看得更快

> 原文：<https://dev.to/joeyschoblaska/make-your-specs-faster-with-sleep-study-1ff>

大型应用程序的测试套件变慢有很多原因。其中之一——代码中错误的`sleep`语句——很容易修复，使用`rspec-sleep_study` gem，很容易找到。

任何时候你发现一个规范需要几秒钟才能完成，你就应该立即怀疑。尤其是当被测试的代码与另一个服务集成或者进行任何类型的网络调用时。

```
Top 4 slowest examples (40.14 seconds, 96.0% of total time):
  IntercomClient::post when an error is raised when it's a retryable error retries
    20.04 seconds ./spec/services/data/intercom_client_spec.rb:45
  IntercomClient::get when an error is raised when it's a retryable error retries
    20.01 seconds ./spec/services/data/intercom_client_spec.rb:23
  IntercomClient::get when an error is raised raises the error
    0.07472 seconds ./spec/services/data/intercom_client_spec.rb:17
  IntercomClient::post when an error is raised raises the error
    0.01893 seconds ./spec/services/data/intercom_client_spec.rb:39

Finished in 41.82 seconds (files took 8.88 seconds to load)
4 examples, 0 failures 
```

这通常表明您的代码中某处有一个`sleep`,您的规范在等待休眠完成时被阻塞了。

这个问题的解决方案通常很简单:您需要在您的规范中剔除睡眠语句或睡眠间隔。但是，如何找出哪些规范被阻塞了，以及阻塞在哪里，以便修复它们呢？这就是睡眠研究的切入点。

Sleep Study 使用 Ruby 的 [TracePoint](https://ruby-doc.org/core-2.0.0/TracePoint.html) 类来包装代码执行的每个 C 函数调用和返回。如果它检测到一个`sleep`函数，它会记录花费在睡眠上的时间，然后打印一份报告，显示规格和负责的代码行。

```
$ gem install rspec-sleep_study
$ rspec --format RSpec::SleepStudy spec/services/data/intercom_client_spec.rb

...

The following examples spent the most time in `sleep`:
  20.013 seconds: ./spec/services/data/intercom_client_spec.rb:23
    - 20.013 seconds: /opt/apps/vendor/ruby/2.5.0/gems/retryable-2.0.4/lib/retryable/configuration.rb:36
  20.001 seconds: ./spec/services/data/intercom_client_spec.rb:45
    - 20.001 seconds: /opt/apps/vendor/ruby/2.5.0/gems/retryable-2.0.4/lib/retryable/configuration.rb:36 
```

现在我们知道我们的规范确实阻塞了睡眠，并且我们知道在代码中哪里可以找到:在我们的客户端类中的某个地方，我们调用了`Retryable`。

```
def self.get(endpoint)
  Retryable.retryable(:tries => 5, :sleep => RETRY_SLEEP) do
    JSON.parse RestClient.get(endpoint, HEADERS)
  end
end

def self.post(endpoint, data)
  Retryable.retryable(:tries => 5, :sleep => RETRY_SLEEP) do
    JSON.parse RestClient.post(endpoint, data.to_json, HEADERS)
  end
end 
```

既然我们已经发现了这一点，我们可以通过删除那个`RETRY_SLEEP`常量:
来使我们的规范运行得更快

```
before do
  stub_const("IntercomClient::RETRY_SLEEP", 0)
end 
```

```
Top 4 slowest examples (0.08328 seconds, 4.3% of total time):
  IntercomClient::post when an error is raised raises the error
    0.0358 seconds ./spec/services/data/intercom_client_spec.rb:39
  IntercomClient::get when an error is raised when it's a retryable error retries
    0.02302 seconds ./spec/services/data/intercom_client_spec.rb:23
  IntercomClient::get when an error is raised raises the error
    0.0135 seconds ./spec/services/data/intercom_client_spec.rb:17
  IntercomClient::post when an error is raised when it's a retryable error retries
    0.01096 seconds ./spec/services/data/intercom_client_spec.rb:45

Finished in 1.94 seconds (files took 7.54 seconds to load)
4 examples, 0 failures 
```

一行代码的修改节省了我们 40 秒的构建时间！

需要注意的是，在每一个 C 函数周围放置跟踪点并不是免费的，睡眠研究将使你的规范运行得更慢。只是偶尔使用它来寻找已经潜入您代码中的新睡眠。把那些睡觉的时间掐掉，打开一个显示你测试速度的 PR，然后绕着办公室跑一圈并击掌庆祝。