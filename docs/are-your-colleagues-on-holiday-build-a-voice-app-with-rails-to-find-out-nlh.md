# 你的同事在度假吗？用 Rails 构建一个语音应用程序来找出答案

> 原文：<https://dev.to/vonagedev/are-your-colleagues-on-holiday-build-a-voice-app-with-rails-to-find-out-nlh>

[![Dialing a phone](img/d6579ff819f64fb828bb4f95d552f63f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w0rfofaj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2018/10/dialingtelephonekeypadco_536224original-1200x600.jpg)

Nexmo 的[语音 API](https://developer.nexmo.com/voice/voice-api/overview) 允许你在云端建立实时语音通信。通过 Nexmo Voice API，您可以使用现有的 web 框架建立语音通信，记录和存储呼入电话，创建电话会议，并以 40 种语言和各种口音发送文本到语音的消息。

如今，人们发现自己越来越多地在全球分布的团队中工作。你的一部分同事在自己国家的国庆节离开他们的办公桌是很平常的事情。然而，你怎么能知道你的同事居住的所有国家的法定假日呢？

在这篇博文中，我们将创建一个 Rails 应用程序，帮助您确定您的同事是否在国家法定假日。当我们完成后，我们将有一个任何人都可以调用的应用程序，在经过简单的菜单提示后，将通知该人是否有一个特定国家或世界范围的国家假日。

## 先决条件

*   [红宝石 2.5.3+](https://www.ruby-lang.org/en/downloads/)
*   [轨道 5.2.2+](https://guides.rubyonrails.org/getting_started.html)
*   [ngrok](https://ngrok.io)
*   Nexmo 账户

本教程将带你从头开始这个过程。如果您想看到完成的代码，您可以克隆这个项目的 [git 库](https://github.com/Nexmo/rails-vapi-holiday-checker)。

## 开始一个 Rails 项目

我们将初始化一个没有数据库的新 Rails API 项目。我们在没有数据库的情况下开始项目的原因是我们没有持久化任何数据。从您的控制台运行以下命令:

```
rails new rails-holiday-checker --api --skip-active-record 
```

Enter fullscreen mode Exit fullscreen mode

继续前进，转到我们的命令已经创建的新目录:`cd rails-holiday-checker`，用您喜欢的文本编辑器打开项目。

我们现在准备开始构建我们的应用程序。

## 配置导轨

### 安装依赖项

我们要做的第一件事是安装项目需要的依赖项。打开`Gemfile`并添加以下内容:

```
gem 'holidays' 
```

Enter fullscreen mode Exit fullscreen mode

保存文件后，从控制台运行`bundle install`。我们已经安装了 [Ruby holidays gem](https://github.com/holidays/holidays) 来扩展 Ruby 内置的带有假日查找的日期和时间类的功能。

### 控制器动作

至此，我们已经准备好定义控制器动作了。让我们在`/app/controllers`中创建一个名为`holidays_controller.rb`的新控制器，并在其中创建我们的动作。然而，在我们这样做之前，让我们花点时间讨论一下我们的应用程序的流程。这将让我们更清楚地理解我们行动背后的逻辑。

我们的程序将工作的方式是，用户将拨入我们的应用程序，并被提示检查特定日期或今天的假期。如果用户选择检查今天，他们将被引导到另一个菜单，询问他们希望在哪里检查:美国，英国或世界各地。如果用户选择检查特定日期，他们将被引导到一个菜单选项，要求他们输入日期。用户输入日期后，会被要求选择哪个国家度假:美国、英国或全球。作为此流程的结果，我们需要以下操作(以及接下来要定义的路线):

*   `#answer`:确定用户是想要选择一个日期，还是想要检查今天
*   `#date_type`:提示用户输入一个特定的日期，如果这是他们在`#answer`中选择的，或者将他们重定向到`#country_choice`
*   `#country_choice`:提示用户选择一个国家；美国、英国或全球
*   `#holiday_output`:通过调用我们将创建的名为`#holiday_lookup`的私有方法来检查假期，并以文本到语音的格式向用户提供答案
*   `#event`:从 Nexmo API 接收响应并打印到控制台的动作

现在我们已经了解了应用程序的流程和我们需要的控制器动作，让我们创建它们:

```
class HolidaysController < ActionController::API

    BASE_URL = ""

    def answer
        render json: [
            { 
                :action => 'talk', 
                :text => 'Find out if your colleagues are on a holiday. Press 1 to enter a date or 2 for today.'
            },
            {
                :action => 'input',
                :eventUrl => ["#{BASE_URL}/date_type"]
            }
        ].to_json
    end

    def date_type
        dtmf = params['dtmf'] || parsed_body['dtmf']

        if dtmf == '1'
            render json: [
                {
                    :action => 'talk',
                    :text => 'Please enter a date on your keypad in the following format: four digits for the year, 
                    followed by two digits for the month and two digits for the day, followed by the hash symbol. 
                    For example, January 2nd, 2019 would be 2 0 1 9 0 1 0 2.',
                    :bargeIn => true
                },
                {
                    :action => 'input',
                    :submitOnHash => true,
                    :maxDigits => 8,
                    :eventUrl => ["#{BASE_URL}/country_choice"]
                }
            ].to_json
        elsif dtmf == '2'
            render json: [
                {
                    :action => 'talk',
                    :text => 'Please enter 1 for the US, 2 for the UK or 3 for worldwide holidays.'
                },
                {
                    :action => 'input',
                    :eventUrl => ["#{BASE_URL}/holiday_output"]
                }
            ].to_json
        else
            render json: [{:action => 'talk', :text => 'I did not recognize your selection. Please call back and try again.'}].to_json
        end
    end

    def country_choice
        date = ((params['dtmf'] || parsed_body['dtmf']) || '')

        render json: [
            {
                :action => 'talk',
                :text => 'Please enter 1 for the US, 2 for the UK or 3 for worldwide holidays.'
            },
            {
                :action => 'input',
                :eventUrl => ["#{BASE_URL}/holiday_output?date=#{date}"]
            }
        ].to_json
    end

    def holiday_output
        country_input = params['dtmf'] || parsed_body['dtmf']
        date_input = params['date'] || ''

        if country_input == '1'
            holidays = holiday_lookup(date_input, :us) 
        elsif country_input == '2'
            holidays = holiday_lookup(date_input, :gb)
        elsif country_input == '3'
            holidays = holiday_lookup(date_input, :all)
        else
            render json: [
            { 
                :action => 'talk', 
                :text => 'I did not recognize the number you entered. Please call back and try again.'
            }
        ].to_json
        end

        if (holidays.length == 0 || holidays.nil?)
            render json: [
                {
                    :action => 'talk',
                    :text => 'Your colleagues are not on a holiday today. Feel free to message them on Slack! Goodbye.'
                }
            ].to_json
        else
            render json: [
                {
                    :action => 'talk',
                    :text => "Your colleagues are not at their desk today. It is #{holidays[0][:name]}. Goodbye."
                }
            ].to_json
        end
    end

    def event
        puts params
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到`BASE_URL`变量是空的。一旦您设置了 ngrok 并拥有外部可访问的 ngrok URL，请将`BASE_URL`更改为您的 ngrok 地址。

我们控制器动作的大部分工作是创建 [Nexmo 呼叫控制对象(NCCO)](https://developer.nexmo.com/voice/voice-api/overview/curl#about-nexmo-call-control-objects) 来指示呼叫期间发生的事情。NCCO 的格式是 JSON，最简单的是由一个`:action`和一个`:text`键值对组成。在`#date_type`控制器动作中，我们还使用额外的参数，如`:submitOnHash`、`:bargeIn`和`:maxDigits`，使用户可以输入多个数字响应，通过输入散列键来表示他们已经完成，并在消息结束前开始输入他们的响应。

如上所述，正如控制器代码所示，我们还需要构建一个方法，根据提供的参数执行假日查找。让我们开始吧:

```
class HolidaysController < ActionController::API

...

    private

    def holiday_lookup(date_input, country)
        with_date = false
        (date_input == '' || date_input.nil?) ? with_date = false : with_date = true

        if with_date
            year = date_input[0, 4].to_i
            month = date_input[4..5].to_i
            day = date_input[6..7].to_i
        end

        if country != :all
            with_date ? Holidays.on(Date.civil(year, month, day), country) : Holidays.on(Date.today, country)
        else
            with_date ? Holidays.on(Date.civil(year, month, day)) : Holidays.on(Date.today)
        end
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

`#holiday_lookup`方法有两个参数:`date_input`和`country`。它创建一个`with_date`变量来保存一个布尔标志。如果用户没有提供日期，那么`with_date`标志是`false`，但是如果他们提供了，那么它被设置为`true`。如果用户提供了一个特定的日期，该方法然后设置`year`、`month`和`date`值。这些值用于检查特定日期是否有假日，或者如果`with_date`为`false`，则检查今天是否有假日。此外，该方法还检查用户是否要求在特定国家或全球度假，并执行适当的搜索。

### 定义我们的路线

设置 Rails 应用程序需要做的最后一件事是定义路线。打开`/config/routes.rb`并添加以下内容:

```
get '/answer', to: 'holidays#answer'

post '/event', to: 'holidays#event'

post '/dtmf', to: 'holidays#dtmf'

post '/date_type', to: 'holidays#date_type'

post '/country_choice', to: 'holidays#country_choice'

post '/holiday_output', to: 'holidays#holiday_output'

post '/event', to: 'holidays#event' 
```

Enter fullscreen mode Exit fullscreen mode

我们的 Rails 应用程序现在已经可以使用了，但是我们仍然需要设置我们的 Nexmo 凭证。然而，在我们这样做之前，我们需要用 ngrok 创建一个外部可访问的 URL，Nexmo API 可以用它来与我们的应用程序进行交互。下面的[博文](https://www.nexmo.com/blog/2017/07/04/local-development-nexmo-ngrok-tunnel-dr/)详细介绍了设置 ngrok 的必要步骤，如果你以前没有这样做过的话。

现在我们已经有了 Rails 应用程序和 ngrok 外部 URL 信息，我们可以设置我们的 Nexmo 帐户了。

## 设置我们的 Nexmo 账户

为了让我们的语音应用程序工作，我们需要一个 Nexmo 帐户，一个 Nexmo 提供的电话号码，一个 Nexmo 应用程序，最后，我们需要将我们的应用程序链接到我们的电话号码。

### 创建 Nexmo 账户

您可以免费创建一个 Nexmo 帐户，作为额外奖励，您的帐户将获得 2 欧元，以开始使用您的新应用程序。在您的网络浏览器中导航至[https://dashboard.nexmo.com/sign-up](https://dashboard.nexmo.com/sign-up)，并完成注册步骤。完成后，您将进入 Nexmo 控制面板。

### 购买电话号码

在 Nexmo 仪表板上，点击左侧菜单上的`Numbers`菜单项。您将看到出现三个选项:

[![](img/ae6e39371c260bee600c53c9ae128821.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bry5Jp33--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/02/Screen-Shot-2019-02-25-at-13.07.32.png)

点击`Buy numbers`选项，你将被引导到一个页面，在这里你可以选择一个国家，功能，类型，以及你想要的四位数字。

[![](img/551b374b554d461feac30b1a189d45ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oYhnrDnh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/02/buy_numbers_nexmo_dashboard.png)

出于我们的目的:选择您当前所在的国家，以便您可以拨打本地电话；选择`Voice`选择功能，选择手机或座机类型。您不需要为`Number`文本字段输入任何内容。当你点击`Search`，你会看到一个电话号码列表。

点击橙色`Buy`按钮选择一个，在确认提示中再次点击橙色`Buy`按钮。

您现在拥有一个 Nexmo 电话号码。我们的下一步是在 Nexmo 仪表板中创建一个应用程序。

### 创建一个 Nexmo 应用

从左侧菜单中，点击`Voice`菜单项。您将在`APPLICATIONS`下看到以下四个选项:

[![](img/4504dd0abf1f5c183e7a55b515caeaf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_QMd8rld--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/02/voice_menu_options_dashboard.png)

点击`Create an application`选项，您将被引导到一个页面，在这里您可以设置一个新的 Nexmo 应用程序。

用以下内容完成表格:

*   `Application name`文本字段输入`holidays-checker`
*   `Event URL`文本字段输入您的 ngrok URL: `https://[ngrok url here]/event`
*   `Answer URL`文本字段再次输入您的 ngrok URL:`https://[ngrok url here]/answer`

对于这个应用程序，我们不需要公钥/私钥对，但尽管如此，生成它们是一个好主意。当您单击链接`Generate public/private key pair`时，公钥将填充`Public key`文本区域，私钥将自动下载到您的浏览器保存下载的位置。您可以将私钥保存在您应用程序的根文件夹中，但是要确保将`./private.key`添加到您的`.gitignore`文件中，以免将其提交给版本控制并将其公开。

完成后，点击蓝色的`Create Application`按钮。

### 将您的 Nexmo 号码链接到您的 Nexmo 应用程序

既然我们已经有了一个 Nexmo 语音号码和一个 Nexmo 应用程序，剩下要做的就是将两者连接起来。

点击左侧菜单中的`Numbers`菜单选项，然后点击`Your numbers`。您将被引导到一个页面，上面列出了您购买的电话号码，这是我们刚刚购买的一个号码。点击页面右侧数字旁边的齿轮图标，会弹出一个菜单。这里要完成的唯一步骤是从`Voice`区域下的下拉列表中选择您的新应用程序，然后按蓝色的`Ok`按钮。

[![](img/77b9705a9bcbbf7fe07d6a4864fdf20f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z2jdJ11e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/02/configure_number_dashboard.png)

现在，您已经将新的 Nexmo 语音号码链接到新的 Nexmo 应用程序，最后一步，您就可以运行您的应用程序了！

## 启动您的应用程序

在 ngrok 服务器仍在后台运行的情况下，继续从控制台运行`rails s`。这将在`https://localhost:3000`启动您的 Rails 服务器。您的应用程序中没有视图，因此与它交互的唯一方式是调用它；继续拨打您的新 Nexmo 电话号码。您应该会听到您的应用程序应答呼叫，并询问您来自`#answer`控制器方法的第一个提示。请随意尝试不同的选项，玩玩您的应用程序。现在，您已经有了一个全功能的假日检查器，它带有 Rails 和 Nexmo Voice API。恭喜你！

## 更多资源

如果您有兴趣了解更多关于 Nexmo Voice API 的信息，请查看以下资源:

*   [Nexmo 语音 API 参考](https://developer.nexmo.com/api/voice)
*   [如何使用 Rails 接收正在进行的呼叫的语音呼叫事件](https://www.nexmo.com/blog/2017/12/19/receive-voice-call-events-call-progress-ruby-rails-dr/)
*   [如何使用 Rails 处理呼入电话](https://www.nexmo.com/blog/2017/12/19/receive-voice-call-events-call-progress-ruby-rails-dr/)
*   [使用 Nexmo 语音 API 建立家庭热线](https://www.nexmo.com/blog/2018/11/20/build-a-family-hotline-dr/)