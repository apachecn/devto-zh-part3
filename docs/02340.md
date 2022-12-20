# 编码最佳实践，第一章:函数。

> 原文：<https://dev.to/levivm/coding-best-practices-chapter-one-functions-4n15>

我的目的不是说写代码的最好方法是什么，一些程序员有他们的方法，他们很好。我只是想和其他人分享我认为的代码最佳实践，以及如何改进几个方面，如就绪性、结构、组件、意义、调试、重构等。

# 第一章:功能

## 做一件事。

我想从最基本的规则开始，也是我认为的最佳实践:**一件事规则**。这将使我们的生活更容易，尝试创建您的功能来做一件事。

编写只做一件事的函数可能很难，但这应该是我们日常工作的最终目标。为什么？让我们看看这个

```
def create_user(email, password):
    try:
        user = User.objects.get(email=email)
    catch UserDoesNotExist:
        user = User.objects.create(
            email=email,
            password=password
        )
        auth_valid = authenticator(
            user=user,
            password=password
        )
        if auth_valid:
            user.last_login = datetime.now()
            user.save()
            msg_content = '<h2>Welcome {email}</h2>'.format(
                email=email
            ) 
            message = MIMEText(
                msg_content,
                'html'
            )
            message.update({
                'From': 'Sender Name <sender@server>',
                'To': email,
                'Cc': 'Receiver2 Name <receiver2@server>',
                'Subject': 'Account created'
            })
            msg_full = message.as_string()
            server = smtplib.SMTP('smtp.gmail.com:587')
            server.starttls()
            server.login(
                'sender@server.com',
                'senderpassword'
            )
            server.sendmail(
                'sender@server.com',
                [email],
                msg_full
            )
            server.quit()
            return user 
```

Enter fullscreen mode Exit fullscreen mode

**这个函数有什么作用？**

*   如果用户不存在，它会创建一个新用户。
*   它创建一个 HTML 模板。
*   它初始化 SMTP 连接器。
*   它发送一封电子邮件。

**为什么它是错的？**

1.  不仅仅是做一件事。
2.  这个函数的单元测试将会是巨大而混乱的，为什么呢？例如，测试逻辑应该同时考虑身份验证和欢迎电子邮件流程，而这些流程应该分开测试。
3.  更容易引入错误，比如说我们想改变欢迎邮件，我们需要修改我们的函数，所以，为了修复进程 B 而修改进程 A 是没有意义的。
4.  如果我们试图做多件事，这将增加我们的功能行数，失去准备。
5.  要做的事情多了，调试更难了。

也就是说，我们如何尝试修复或减少上面列出的问题？让我们重构我们的代码。

```
def signup(email, password):
    new_user = create_user(
        email,
        password
    )
    authenticate(user)
    send_welcome_email(email)

def create_user(email, password):
    try:
        user = User.objects.create(
            email=email,
            password=password
        )
        return user
    except UserAlreadyExists:
        raise Exception('User exists')

def authenticate(user, password):
    auth_valid = authenticator(
        user=user,
        password=password
    )
    if auth_valid:
        user.last_login = datetime.now()
        user.save()

def send_welcome_email(email):
    email_msg = create_welcome_email_msg(email)
    send_email_to(
        email,
        email_msg
    )

def create_welcome_email_msg(email):
    msg_content = '<h2>Welcome {email}</h2>'.format(
        email=email
    ) 
    message = MIMEText(
        msg_content,
        'html'
    )
    message.update({
        'From': 'Sender Name <sender@server>',
        'To': email,
        'Cc': 'Receiver2 Name <receiver2@server>',
        'Subject': 'Account created'
    })
    msg_full = message.as_string()
    return msg_full

def send_email_to(email, email_msg):
    server = smtplib.SMTP('smtp.gmail.com:587')
    server.starttls()
    server.login(
        'sender@server.com',
        'senderpassword'
    )
    server.sendmail(
        'sender@server.com',
        [email],
        msg_full
    )
    server.quit() 
```

Enter fullscreen mode Exit fullscreen mode

**我们做了什么？**

好多了，对吧？

*   我们将我们的`create_user`函数分解成更小的函数，允许我们分离逻辑，并添加新的不同层次的抽象，如`signup function`。
*   我们将同样的原则(一物法则)应用于其他函数。因此，就绪性得到了提高，你可以轻松地进行单元测试，现在你可以更改你的欢迎电子邮件，而不需要接触任何关于用户创建过程的东西。

**提示** :
那么，我们怎样才能知道我们的函数不是只做一件事呢？最简单的方法是*如果你能从你的函数中提取一些逻辑，并用一个不同的逻辑名*把它放入一个新的函数中，很可能你的函数做了不止一件事。

## 抽象层次

为了确保我们的函数做一件事，我们应该在所有函数中保持相同的抽象级别。

在我们过去的例子中(代码预览 1)，我们可以发现不同的抽象层次，我们使用一个
高级函数`authenticator(user=user,password=password)`来认证用户，但是同时，我们从一个字符串`msg_content = '<h2>Welcome {email}</h2>'.format(email=email)`创建一个 HTML 模板。应该避免这种函数定义。

基本上，我们应该在创建函数的时候保持一致。这也有助于读者理解我们的功能是一个高层次的概念还是一个详细的过程。

## 函数内的节

另一种方法是判断你的函数是否在做不止一件事，如果你能把它分成不同的部分，把一堆行组合起来

```
def foo():
    # initialization
    # ...... several lines here
    # logic 1
    # ....... several lines here
    # Prepare return value
    # ....... several lines here 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们在同一层次上做不止一件事，为了避免这种情况，你可以提取其他函数中的每一段，然后一起使用，当然，这取决于你的函数具有的抽象层次。

## 识别级别

我发现自己试图理解超过两级缩进的函数。如果你也这样做了，我们就可以同意
在最坏的情况下最大值应该是 2。

如果你的函数在其他`if/else`块中有很多`if/else`块，那么你的函数肯定没有做任何事情。让我们看一个例子。

```
def buy_concert_ticket(user, ticket):

    ticket_price = ticket.price
    user_age = user.age

    if user.age >= 18:
        user_money = user.money
        if user_money >= ticket_price:
            seats = stadium.seats
            seat_available = None
            for seat in seats:
                if seat.is_available():
                    seat_available = seat
                    break

            if seat_available: 
                seat.owner = user
                user.money -= ticket.price
                print("Congratulations, you have a seat")
            else:
                print("There is not available seat")

        else:
            print("Sorry you don't have enough balance")
    else:
        print("You are not allowed to buy a ticket") 
```

Enter fullscreen mode Exit fullscreen mode

所以，这些函数允许用户购买一张票，但是在完成购买之前，它验证一些条件。
此功能不符合我们之前的规则:

*   它有 2 级以上的缩进/块。
*   它不止做一件事。
*   它有几个部分。
*   由于嵌套的 if/else 块，很难读取。

如何解决这个问题？
1。-我们应该通过移除嵌套的 if/else 块来减少缩进级别。

```
def buy_concert_ticket(user, ticket):

    ticket_price = ticket.price
    user_age = user.age
    user_money = user.money

    if not user.age >= 18:
        print("You are not allowed to buy a ticket")
        return 

    if not user_momey >= ticket_price:
        print("Sorry you don't have enough balance")
        return

    for seat in steats:
        if seat.is_available():
            seat.owner = user
            user.money -= ticket.price
            print("Congratulations, you have a seat")
            return

    print("There is no available seat")
    return 
```

Enter fullscreen mode Exit fullscreen mode

你看，我们可以删除所有的`if/else blocks`，所以我们现在的最大缩进级别是 2。现在，函数
有了更多的准备。

**Pro-Tip** :如果你尝试使用解释过的规则来制作更简单的函数，你将不再需要为了函数逻辑的目的而使用`else`。

2.-让我们确定我们的函数所做的事情，并将它们提取到单独的函数中。重构后的代码应该是这样的。

```
def buy_concert_ticket(user, ticket):

    if not user_has_legal_age(user):
        print("You are not allowed to buy a ticket")
        return

    if not user_has_enough_balance(user, ticket):
        print("Sorry you don't have enough balance")
        return

    available_seat = get_available_seat()

    if not available_seat:
        print("There is not available seat")
        return

    buy_seat(user, available_seat)

    return

def user_has_legal_age(user):
    user_age = user.age

    if not user.age >= 18:
        return False

    return True

def user_has_enough_balance(user, ticket):
    user_money = user.money
    ticket_price = ticket.price

    if user_momey >= ticket_price:
        return True

    return False

def get_available_seat():
    seats = stadium.seats
    for seat in seats:
        if seat.is_available():
            return seat

def buy_seat(user, seat):
    seat.owner = user
    user.money -= ticket.price
    print("Congratulations, you have a seat")
    return 
```

Enter fullscreen mode Exit fullscreen mode

我们能够从现有函数中提取四个新函数，这意味着我们试图做得太多了。

现在，我们的主要功能`buy_concert_ticket`可以使用“自然语言”进行阅读。当然，我使用了准确的名称(我们稍后会谈到这一点)，但是拥有少量的行，仅仅一级额外的缩进，对实现这个结果有很大的帮助。

3.-你认为我们不能把功能变小吗？还是减少章节？是的，让我们看看。

```
def buy_concert_ticket(user, ticket):

    if not user_can_buy_a_ticket(user, ticket):
        return

    buy_available_seat(user)

    return

def user_can_buy_a_ticket(user, ticket):

    if not user_has_legal_age(user, ticket):
        print("You are not allowed to buy a ticket")
        return False

    if not user_has_enough_balance(user, ticket):
        print("Sorry you don't have enough balance")
        return False

    return True

def user_has_legal_age(user):
    user_age = user.age

    if not user.age >= 18:
        return False

    return True

def user_has_enough_balance(user, ticket):
    user_money = user.money
    ticket_price = ticket.price

    if user_momey >= ticket_price:
        return True

    return False

def buy_available_seat(user):
    available_seat = get_available_seat()

    if not available_seat:
        print("There is not available seat")

    buy_seat(user, available_seat)
    return

def get_available_seat():
    seats = stadium.seats
    for seat in seats:
        if seat.is_available():
            return seat

def buy_seat(user, seat):
    seat.owner = user
    user.money -= ticket.price
    print("Congratulations, you have a seat")
    return 
```

Enter fullscreen mode Exit fullscreen mode

我们可以对我们的主函数做更多的修改，现在我们把它从 21 行减少到只有 4 行。我们只是应用了相同的原则，试图抽象我们的逻辑和识别部分/块，以便创建新的功能。这将成为一个迭代过程。

那么，我们得到了什么？

*   任何试图阅读我们函数的人只需几秒钟就能理解我们在做什么。
*   你可以像读“自上而下的故事”一样阅读整个过程。
*   现在，实现单元测试将变得更加容易，因为我们将购票过程的每一步都视为一个单一的功能。
*   如果我们想要在我们的流程中添加额外的需求，识别要更改的函数就足够了，例如:为一个购买者添加另一个限制，我们只需要在`user_can_buy_ticket()`函数中更改我们的逻辑，而不涉及其他任何东西。

注意:我知道，这样做很费时间，但是相信我，即使你由于截止日期等原因不能迭代那么多次。至少试着做一次。总是第一次迭代带来好的结果。

仅此而已。在下一章，我将更多地讨论如何定义函数参数以提高易用性，并给出其他提示。

> 如果你喜欢我的内容或者对你有帮助，你可以通过[请我喝咖啡](https://www.buymeacoffee.com/R3i9IQJ)
> 来激励我写更多的内容，这些内容最初发布在我的个人[博客](https://levipy.com/coding-best-practices-chapter-one-functions)