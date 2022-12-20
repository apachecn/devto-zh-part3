# 用 Java 创建电报机器人:从概念到部署

> 原文：<https://dev.to/codegym_cc/creating-a-telegram-bot-in-java-from-conception-to-deployment-3a8c>

[![Telegram bot with Java](img/6d4e601da5097cedd83f54460991abeb.png "Telegram bot with Java")](https://res.cloudinary.com/practicaldev/image/fetch/s--A4tabXl5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2yk3893hh64s05bcl0lq.png)

那么什么是机器人呢？你可以在这里阅读更多关于这个[的内容。](https://core.telegram.org/bots)

首先，您需要查看用于开发 Telegram 机器人的库的官方文档(以下称为“API”)。在这里可以找到[。](https://core.telegram.org/bots/api)

那里的一切都非常容易接近和清晰。看来我们可以只写代码，并高兴！但事情没这么简单。在花了很多时间搜索之后，我发现了一些关于机器人开发的知识，例如，如何制作键盘，如何操作`CallbackQuery`等等。但是，我没有找到完整、详尽的 Java bot 开发指南。促使我写了这篇文章。

有很多网站可以让你创建自己的易于部署的机器人。但事实是，大部分创建的机器人都提供参考信息之类的。我们的机器人是一个成熟的网络应用程序。您可以绑定数据库、执行各种 API 请求、解析网站、执行复杂的计算等等。可能性只受到你想象力的限制。我希望以上有助于澄清我要写的内容。

在 Telegram 上注册一个机器人非常容易。该过程在上面链接的文档中有详细描述。对于我们的应用程序，您只需要知道 bot 的名称和注册 bot 时收到的令牌。

基本上，机器人只是一个基于控制台的 web 应用程序。没有前端，只是纯命令处理。如果你想掌握 **Hibernate** 或者学习如何解析 **JSON** ，那么这个项目正适合你。让我们首先向 pom.xml 添加一个依赖项(我假设您使用的是 Maven)。你可以这样做:

```
<dependency>
            <groupId>org.telegram</groupId>
            <artifactId>telegrambots</artifactId>
            <version>3.5</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个继承了`TelegramLongPollingBot`类的 Bot 类，并覆盖它的方法:

```
public class Bot extends TelegramLongPollingBot {

    /**
     * Method for receiving messages.
     * @param update Contains a message from the user.
     */
    @Override
    public void onUpdateReceived(Update update) {
    String message = update.getMessage().getText();
    sendMsg(update.getMessage().getChatId().toString(), message);
    }

    /**
     * Method for creating a message and sending it.
     * @param chatId chat id
     * @param s The String that you want to send as a message.
     */
    public synchronized void sendMsg(String chatId, String s) {
        SendMessage sendMessage = new SendMessage();
        sendMessage.enableMarkdown(true);
        sendMessage.setChatId(chatId);
        sendMessage.setText(s);
        try {
            sendMessage(sendMessage);
        } catch (TelegramApiException e) {
            log.log(Level.SEVERE, "Exception: ", e.toString());
        }
    }

    /**
     * This method returns the bot's name, which was specified during registration.
     * @return bot name
     */
    @Override
    public String getBotUsername() {
        return "BotName";
    }

    /**
     * This method returns the bot's token for communicating with the Telegram server
     * @return the bot's token
     */
    @Override
    public String getBotToken() {
        return "BotToken";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

而现在主要方法的内容:

```
public static void main(String[] args) {
        ApiContextInitializer.init();
        TelegramBotApi telegramBotApi = new TelegramBotApi();
        try {
            telegramBotApi.registerBot(Bot.getBot());
        } catch (TelegramApiRequestException e) {
            e.printStackTrace();
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

填充完`getBotUsername()`和`getBotToken()`方法后，启动 bot。目前，它只是将我们发送给它的任何消息重定向给我们，有点像一面“镜子”。它是这样工作的:当你启动应用程序时，它开始向电报服务器发送请求，每 n 秒一次，地址如下:【https://api.telegram.org/BotToken/getMe】，其中 BotToken 是你的 bot 的令牌。作为响应，它接收包含所有消息的 JSON。这些消息都由库处理，并作为一个`Update`对象传递给`OnUpdateReceived(Update update)`方法。这就是我们的工作。电报机器人的魅力就在于此:它们可以在任何计算机上运行，测试它只需要启动应用程序，而不需要在每次更改后都将它部署到主机上。这样很方便。

当然，你可以配置一个机器人来使用 webhooks 工作。你可以在网上找到方向。为简单起见，我们将使用长轮询。

如何处理消息以及发送什么作为响应，只受语言和库的能力限制。其他的一切都取决于你。你可以做一个机器人来帮你搜索 YouTube 视频。你可以做一个机器人，它会把你每天发送给自己的东西发送给你，就像一年前的时间胶囊一样。或者你可以学习整合 CRM 系统，为小企业制作机器人——你只是被你的想象力所限制。

继续前进。使用过机器人的人都知道，使用以“`/`”字符开头的命令与它们进行交互是很方便的，例如`/start`。但是有一个更方便的方法:按钮。有两种按钮:出现在输入栏下面的按钮(`ReplyKeyboardMarkup`)和直接位于链接信息下面的按钮(`InlineKeyboardMarkup`)。您可以从文档中对它们的描述中获得基本的理解。

## ReplyKeyboardMarkup

实际上，这是一个按钮数组的数组:`List<KeyboardRow <KeyboardButton>>`。下面是创建键盘的示例代码:

```
public synchronized void setButtons(SendMessage sendMessage) {

        // Create a keyboard
        ReplyKeyboardMarkup replyKeyboardMarkup = new ReplyKeyboardMarkup();
        sendMessage.setReplyMarkup(replyKeyboardMarkup);
        replyKeyboardMarkup.setSelective(true);
        replyKeyboardMarkup.setResizeKeyboard(true);
        replyKeyboardMarkup.setOneTimeKeyboard(false);

        // Create a list of keyboard rows
        List<KeyboardRow> keyboard = new ArrayList<>();
        // First keyboard row
        KeyboardRow keyboardFirstRow = new KeyboardRow();

        // Add buttons to the first keyboard row
        keyboardFirstRow.add(new KeyboardButton("Hi"));

        // Second keyboard row
        KeyboardRow keyboardSecondRow = new KeyboardRow();
        // Add the buttons to the second keyboard row
        keyboardSecondRow.add(new KeyboardButton("Help");

        // Add all of the keyboard rows to the list
        keyboard.add(keyboardFirstRow);
        keyboard.add(keyboardSecondRow);
        // and assign this list to our keyboard
        replyKeyboardMarkup.setKeyboard(keyboard);
    } 
```

Enter fullscreen mode Exit fullscreen mode

在传递消息后，我们在`sendMsg()`方法中调用这个方法。这就是我们如何为这个信息设置一个键盘。当我们向用户发送这条消息时，他会看到我们的消息文本和两个按钮，分别是“*你好*”和“*帮助*”，一个在另一个下面。

当点击其中一个按钮时，机器人会收到一条包含按钮文本的消息。所以，如果客户端点击“ *Help* ”，机器人会收到一条“Help”的消息。对机器人来说，似乎是客户端自己写了“ *Help* ”并将文本发送给机器人。然后你处理这些信息。

## InlineKeyboardMarkup

这也是数组的数组。它类似于前面的标记，但是这里的逻辑有点不同。这种类型的键盘附加在特定的消息上，只为它而存在。这里有一个设置直列键盘的方法:

```
private void setInline() {
        List<List<InlineKeyboardButton>> buttons = new ArrayList<>();
        List<InlineKeyboardButton> buttons1 = new ArrayList<>();
        buttons1.add(new InlineKeyboardButton().setText("Button").setCallbackData(17));
        buttons.add(buttons1);

        InlineKeyboardMarkup markupKeyboard = new InlineKeyboardMarkup();
        markupKeyboard.setKeyboard(buttons);
    } 
```

Enter fullscreen mode Exit fullscreen mode

在`List`中创建一个`List`，并将 inline 按钮添加到第一行。这个按钮可以包含一个 URL，一个到频道的链接，或者一个`CallbackQuery`，我稍后会写这个。这是我们设置按钮文本的地方，这是用户将看到的，然后我们设置将发送到机器人的数据。在我们的例子中，用户看到“Hi”，当按钮被按下时，数字 17 将被发送给机器人。这是我们的`CallbackQuery`。

关于`CallbackQuery`的几句话。为了从`Update`对象中获得这些数据，您需要执行`update.getCallbackQuery()`。该方法返回一个`CallbackQuery`，从中可以访问传递给 bot 的数据。不要试图通过`update.getMessage().getText()`方法得到这个数据——你会得到一个`NullPointerException`。

```
@Override
    public void onUpdateReceived(Update update) {
        if(update.hasMessage()) {
            ThreadClass thread = new ThreadClass(update.getMessage());
        } else  if(update.hasCallbackQuery()) {
            AnswerCallbackThread answerThread = new AnswerCallbackThread(update.getCallbackQuery());
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果有消息，我们将它发送到一个新线程进行处理；如果有一个`CallbackQuery`，我们发送到适当的线程进行处理。您可以向一个`CallbackQuery`发送响应。Telegram 中的每个对象都有自己的 id。要向特定的`CallbackQuery`发送响应，您只需要知道它的 id，它是我们从相应的对象中获得的。为了发送响应，我们调用这个方法:

```
public synchronized void answerCallbackQuery(String callbackId, String message) {
        AnswerCallbackQuery answer = new AnswerCallbackQuery();
        answer.setCallbackQueryId(callbackId);
        answer.setText(message);
        answer.setShowAlert(true);
        try {
            answerCallbackQuery(answer);
        } catch (TelegramApiException e) {
            e.printStackTrace();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**重要**:回复`CallbackQuery`的文字长度不得超过 200 个字符！

发送这样的响应后，客户端将看到一个包含消息的弹出窗口。该窗口可以在出现几秒钟后消失，也可以一直存在，直到用户按下 OK。为了切换模式，我们调用`answer.setShowAlert(true)`方法。如果您将`true`传递给该方法，该窗口将一直存在，直到按下 OK 键。如果你传递 false，那么它会在 5 秒后消失。

这些都是电报机器人库的基本特性。如果你愿意，你可以学习如何发送多媒体，地理定位等等。

让我们继续将我们的机器人部署到一个托管平台。

对于我的项目，我选择了 Heroku。我认为它是一个相当方便的托管平台，有自己的 CLI。这是免费的，但是在这个计划中，你的机器人将在 30 分钟没有收到请求后休眠。当收到请求时，它将被唤醒。这发生得如此之快，您甚至不会注意到(当然，除非数据库连接没有重新启动)。免费计划受到 5MB 数据库、100MB 磁盘空间、每月 2TB 数据和 1 dyno 的限制。dyno 是您的运行应用程序。

我马上会说，是部署给我带来了困难，因为我以前从未部署过我的应用程序。

在部署过程中，Heroku 需要一个名为 Procfile 的文件(不带扩展名)。我们在项目根中创建它。在里面，我们写`worker: sh target/bin/workerBot`，其中 **workerBot** 是在`pom.xml`中指定的名称。一个由 Maven 应用汇编器插件(appassembler-maven-plugin)生成的 sh 脚本将会启动。该脚本描述了如何启动编译后的 jar 文件。要启动的类的名称在`<mainClass>`和`</mainClass>`之间指定，而脚本的名称在`pom.xml` :
中的`<name>`和`</name>`之间给出

```
...
<build>
    <plugins>
        ...
       <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>appassembler-maven-plugin</artifactId>
            <version>1.1.1</version>
            <configuration>
                <assembleDirectory>target</assembleDirectory>
                <programs>
                    <program>
                        <mainClass>com.home.server.TelegramBot</mainClass>
                        <name>workerBot</name>
                    </program>
                </programs>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>assemble</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build> 
```

Enter fullscreen mode Exit fullscreen mode

在开始这个过程之前，您应该在 Heroku 上注册，并安装 Git 和 Heroku CLI。如果您的应用程序需要一个数据库，那么不要忘记在创建新应用程序时添加所需的数据库。此外，您需要确定数据库的主机、用户名、密码和端口，然后在您的应用程序中指明它们。接下来，在部署之前，使用 Maven 构建您的项目。

```
mvn clean install 
```

Enter fullscreen mode Exit fullscreen mode

**首先，我们转到项目目录，用命令`git init`初始化存储库。然后我们将我们的项目添加到这个存储库中。**T3】

```
git add . 
```

Enter fullscreen mode Exit fullscreen mode

**然后我们提交变更**

```
git commit -m "First commit in the project" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，你需要登录 heroku。在命令行
上写下以下内容

```
heroku login 
```

Enter fullscreen mode Exit fullscreen mode

**输入您在注册时创建的凭证。之后，在 heroku 上确定您的存储库的 URL。您可以在设置中这样做。然后我们写**

```
git remote add heroku [url] 
```

Enter fullscreen mode Exit fullscreen mode

为您的存储库添加了一个远程 heroku 存储库。接下来我们写

```
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

**然后我们等待…如果应用程序部署成功，执行下面的命令**

```
heroku ps:scale worker=1 
```

Enter fullscreen mode Exit fullscreen mode

就这样，您的应用程序启动并运行了。如果不是，请仔细查看日志。很可能是应用程序中的错误导致了它的失败。

谢谢你看了这么长的文章。我希望有人会发现它很有用，并且它会在开发过程中给我带来麻烦的地方为您节省大量时间。

之前发表在 [CodeGym 博客](https://codegym.cc/groups/posts/50-creating-a-telegram-bot-in-java--from-conception-to-deployment)上。