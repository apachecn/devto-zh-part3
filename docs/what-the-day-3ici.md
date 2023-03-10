# 今天是什么日子？(德语)

> 原文：<https://dev.to/kurzgedanke/what-the-day-3ici>

[![Futurama Fry Meme mit "What the.... day...?" als text.](img/06878bf6ce1b80efff2f280f5d376cc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lst2jYGi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cq9gl183q3wbvfb21jcb.png)

是第 1 天，是第 2 天……是第 4 天，国会对大多数人来说都是围绕着。然后呢？第五天？第六天？我不知道。
圣诞节和国会都彻底粉碎了这种令人不快的特性。这是非常愉快的，但如果不积极决定的话也很难再找到。
长篇故事短:我只是错过了一次讲座，因为我坚信是星期三而不是星期四。这甚至不是那么令人愉快。从挫折中，我想到了我的人生。我正在写一个肥大的机器人。

## 什么日子？

[![Ein Screenshot des What The Days Mastodon Accounts](img/1384c1e18e261346a5118126accf0edf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aI1FLqpQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gfhhotpdvy5uf1m7aogl.jpg) 
[当天是什么日子？](https://botsin.space/@whattheday)是一种每十二小时提供一次的桅杆，每隔八点钟和八点钟张贴一周的当前日期、日期和时间。它是开源的，可以在 [GitHub](https://github.com/KurzGedanke/whatTheDay) 上找到。

## 怎么写？

我很惊讶用 Python 写 Mastodon Bots 有多简单。基本上，我使用两个库。曾经有一次[【mastodon . py】](https://github.com/halcy/Mastodon.py)和[时间表](https://schedule.readthedocs.io/en/stable/)。Mastodon.py 只是 Mastodon 的 Python API 包装器，而这是撰写邮件所需的全部代码。

```
def post():
    '''
    Login for Mastdon and tooting a toot with the current weekday, date and time.
    '''
    weekday = time.strftime('%A')
    dateDMY =time.strftime('%d/%m/%Y')
    clock = time.strftime("%X")

    mastodon = Mastodon(
        api_base_url = 'https://botsin.space/',
        access_token = cred.accessToken
    )
    mastodon.log_in(
        cred.email,
        cred.password,
    )
    mastodon.status_post('Today is {}, the {} and it is {} o\'clock!'.format(weekday, dateDMY, clock), visibility='unlisted') 
```

前三个变量`dateDMY`、`dateDMY`和`clock`分别用于获取星期的当前日期、日期和时间。
dadd down 在 Mastodon 进行登录，其中`cred.$`指向另一个`.py`文件，以阻止我从 Git 获取凭证。
对`mastodon.status_post()`来说，`visibility='unlisted'`的论点仍然相关，因为 bot 放在当地时间表上时更容易看到饥饿。即使我的 Bot 在[https://botin . space/](https://botsin.space/)上运行，我也想加入这个□。

```
def main():
    '''
    Runs the schedule for the toots at 8 o'clock and 20 o'clock.
    '''
    schedule.every().day.at('08:00').do(post)
    schedule.every().day.at('20:00').do(post)
    while True:
        schedule.run_pending()
        time.sleep(1)

if __name__ == "__main__":
    main() 
```

最后一节是`schedule`图书馆的`main`功能，也是阅读和使用的权利。

所以基本上，我这里有一个在 40 行内运作的 Mastodon Bot。如果这不是一个写自己的动机，我也不知道。

最后，机器人在我家的 Raspberry Pi 上运行。由于我对坞站和系统并不完全了解，所以我在这个[询问 Ubuntu](https://askubuntu.com/questions/396654/how-to-run-the-python-program-in-the-background-in-ubuntu-machine#396655) 条目中，找到了`nohup`命令，从而使 Bot 在后台运行得非常舒适。不幸的是，当 Pi 重新启动时，我还必须重新启动 Bot。也许我可以投资一些码头或系统。

如果你还有什么问题可以在马斯敦跟我联系。[短思维@混沌.社会](https://chaos.social/@kurzgedanke)

否则，我祝你们在自己的 Bot 上写得愉快，为了不忘记今天的日期，[@ whattheday @ botzin . space](https://botsin.space/@whattheday)！