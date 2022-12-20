# Elixir 中带有电子邮件通知的 Web 监视器

> 原文：<https://dev.to/bakenator/web-monitor-with-email-notifications-in-elixir-2ml9>

# 为什么要在仙丹里建这个？

首先也是最重要的是，这个应用程序是一个学习更多关于在 Phoenix 之外编写长生不老药代码的工具。

但是！Elixir 是一种优秀的网站监控工具语言。通过使用管理程序和外部邮件服务，我们只需要几行代码就可以得到一个高度可靠的工具。

# 监视器

为了检查我们的网站，我们将使用执行以下步骤的单个功能模块:

*   检查来自网站的响应
*   根据回复是否正常发送电子邮件
*   等待 1 小时
*   重新开始该功能

我还添加了一个成功案例的限制，这样，如果网站启动，电子邮件每天只发送一次。然而，当网站关闭时，电子邮件通知将每小时发出一次。

值得注意的最有趣的事情之一是我们如何用一个定时器重现相同的函数。在大多数其他语言中，您必须使用 cron 作业来获得预定的循环函数。Elixir 能够做到这一点要归功于其尾部调用优化的能力。

这里是模块
的完整代码

```
defmodule WebMonitor.SimpleMonitor do
  def start_link() do
    # boilerplate to prepare for :httpc.request
    Application.ensure_all_started(:inets)
    Application.ensure_all_started(:ssl)

    IO.inspect "Monitor Started"
    # Could use the Task module here, but spawn_link used for educational reasons
    pid = spawn_link(WebMonitor.SimpleMonitor, :check_site, [])
    {:ok, pid}
  end

  def check_site() do
    # make web request and set timeout to 10 seconds
    case :httpc.request(:get, {'https://bakerbaker.dev', []}, [{:timeout, 10000}], []) do
      {:ok, {{'HTTP/1.1', 200, 'OK'}, _headers, _body}} -> 
        if Time.utc_now().hour == 6 do
          # send email for alive website 
          WebMonitor.Mailer.send_good_email()
        end
      _ -> 
        # send email for down website
        WebMonitor.Mailer.send_bad_email()
    end
    # sleep for 1 hr
    :timer.sleep(60 * 60 * 1000)
    # run check function again
    check_site()
  end

  # returning informational object to the supervisor
  def child_spec([]) do
     %{
        id: __MODULE__,
        start: {__MODULE__, :start_link, []}
      }
  end
end 
```

在这个模块中有几个地方，我们可以加强我们的代码。一种方法是使用 Genserver 并向 check_site 函数提交递归调用。我在这里选择了显式 spawn_link 来提供一个手动监督独立进程的例子。

我们也可以使用任务而不是 spawn_link 来获得更好的调试/错误处理能力。

最后，我利用 HTTPoison 上的:httpc 模块作为学习的机会。这篇博文给了我灵感:[https://vir viil . github . io/2018/04/06/elixir-do-you-have-http-requests-you-do-them-error/](https://virviil.github.io/2018/04/06/elixir-do-you-have-http-requests-you-are-doing-them-wrong/)

# 发送邮件

在 SimpleMonitor 代码中，我使用一个邮件模块来发送电子邮件。这个模块是在 https://github.com/chrismccord/mailgun 用非常容易使用的 MailGun 库制作的

设置它需要创建一个免费的 mailgun 帐户。对于免费帐户，你必须确认每个收件人，但因为我是唯一的收件人，这不是一个问题。

我遵循设置教程，并使用库中默认的邮件模块作为指南。一旦简化，我的应用程序就会变成这样

```
defmodule WebMonitor.Mailer do
  # the :web_monitor atom is specific to this app's name
  @config domain: Application.get_env(:web_monitor, :mailgun_domain),
          key: Application.get_env(:web_monitor, :mailgun_key)

  use Mailgun.Client, @config

  @from "site_checker@bakerbaker.dev"

  def send_good_email() do
    send_email to: "andrew@bakerbaker.dev",
               from: @from,
               subject: "Site is up",
               html: "<strong>The site is good!</strong>"
  end

  def send_bad_email() do
    send_email to: "andrew@bakerbaker.dev",
               from: @from,
               subject: "Site is DOWN!",
               html: "<strong>The site is DOWN!</strong>"
  end
end 
```

几乎所有的工作都是由 Mailgun 库通过宏来完成的。

# 启动 App

需要将 SimpleMonitor 添加到监督树中，以便在应用程序启动时调用它。

这里是完整的应用程序代码

```
defmodule WebMonitor.Application do
  @moduledoc false

  use Application

  def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      {WebMonitor.SimpleMonitor, []}
    ]

    opts = [strategy: :one_for_one, name: WebMonitor.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

现在我们可以用`iex -S mix`运行测试应用程序，或者创建一个在服务器上永久运行的版本

# 更进一步

我想提供这个应用程序作为一个例子，供那些希望从 Phoenix 转向更多手动控制 Elixir 应用程序的人参考。通过自动电子邮件和定时功能，这款应用可以作为构建许多其他通知应用的开端。例如，一个应用程序定期检查 web API，并通过电子邮件发送结果。

希望你喜欢并学到了一些东西！用 Elixir 编码很有趣！