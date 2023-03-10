# 用 Perl 提醒自己和他人 DST

> 原文：<https://dev.to/jacoby/reminding-myself-and-others-about-dst-with-perl-202a>

夏令时。

在我的推特上，对夏令时的反应从[【meh】](https://twitter.com/jcharles00/status/1103310240328679424)到[“该死！”](https://twitter.com/coreyseliger/status/1103325816883236866)，但知道它即将到来是很重要的，因为否则，你会比预定的约会晚一个小时。它悄悄靠近你总是很奇怪。

很多人不理解夏令时，只是知道它会在他们不期望的时候给他们的日常工作带来麻烦。要记住的是，这意味着你在夏天可以多花一个小时打高尔夫球、游泳或吃冰淇淋，所以在周一，你可以在黑暗中去上班，但回家时会有更多的阳光。这是为了休闲产业，而不是农民，因为奶牛和玉米不在乎你的时钟说什么。

如果你住在夏威夷和亚利桑那州，你就不必处理这些乱七八糟的事情。对你有好处。

那么，美国的夏令时是什么时候？几年前我看过它，我把它放在一个函数里，这个函数是*所以*不是我今天写的:

```
sub get_next_dst {
    my ($now) = shift ;

    my @dates ;
    push @dates, [2015, 3, 8] ;
    push @dates, [2015, 11, 1] ;
    push @dates, [2016, 3, 13] ;
    push @dates, [2016, 11, 6] ;
    push @dates, [2017, 3, 12] ;
    push @dates, [2017, 11, 5] ;
    push @dates, [2018, 3, 11] ;
    push @dates, [2018, 11, 4] ;
    push @dates, [2019, 3, 10] ;
    push @dates, [2019, 11, 3] ;
    push @dates, [2020, 3, 8] ;
    push @dates, [2020, 11, 1] ;
    push @dates, [2021, 3, 14] ;
    push @dates, [2021, 11, 7] ;
    push @dates, [2022, 3, 13] ;
    push @dates, [2022, 11, 6] ;
    push @dates, [2023, 3, 12] ;
    push @dates, [2023, 11, 5] ;
    push @dates, [2024, 3, 10] ;
    push @dates, [2024, 11, 3] ;
    push @dates, [2025, 3, 9] ;
    push @dates, [2025, 11, 2] ;
    push @dates, [2026, 3, 8] ;
    push @dates, [2026, 11, 1] ;
    push @dates, [2027, 3, 14] ;
    push @dates, [2027, 11, 7] ;
    push @dates, [2028, 3, 12] ;
    push @dates, [2028, 11, 5] ;
    push @dates, [2029, 3, 11] ;
    push @dates, [2029, 11, 4] ;

    for my $d (@dates) {
        my $dst = DateTime->new(
            year => $d->[0],
            month => $d->[1],
            day => $d->[2],
            hour => 1,
            minute => 59,
            second => 59,
            time_zone => 'America/Indiana/Indianapolis',
            ) ;

        my $duration = $now->delta_days($dst) ;
        my $delta = $duration->in_units('days') ;
        my $years_diff = $now->subtract_datetime($dst)->in_units('years') ;
        my $days_diff = $now->subtract_datetime($dst)->in_units('days') ;

        next if $years_diff != 0 ;
        next if $days_diff > 0 ;

        return $dst ;
        }
    croak 'Too Far' ;
    } 
```

这需要一个 [`DateTime`](https://metacpan.org/pod/DateTime) 对象，并将其与一个`DateTime`列表进行比较，对应于 DST 的来去时间。我在 2015 年这样做了，国会投票改变日期是很正常的，但迄今为止是准确的。

(我使用子例程签名，我可以一次生成数组的数组，而不是推每一个数组，我可以使用 map 更早地将它生成 DT 对象的数组。突然，我开始考虑用类似`DateTime::Events::DST::US`的东西来帮我处理这件事，然后重新进入暂停状态。我相信我也能从 [`DateTime::Event::Easter`](https://metacpan.org/pod/DateTime::Event::Easter) 那里抄袭很多东西。但是我现在不改了，因为管用。)

那么我们如何使用它呢？

```
# this code WILL be changed to make it better
my $now = DateTime->now()
    ->set_time_zone('America/Indiana/Indianapolis') ; # should be 'floating'

# MJD suggested using Fibonacci as the correct way to time notices
# but I went with this to give notification every day the day of
my %count = map { $_ => 1 } qw{ 1 2 3 4 5 6 10 25 50 100 150 200 } ;

my $dst = get_next_dst($now) ;
my $delta = $now->delta_days($dst)->in_units('days') ;
if ( $count{$delta} ) {
    alert_dst($delta) ;
    } 
```

而`alert_dst()`是做什么的？对我来说，它用一个模块发送 tweet，该模块包装并处理`Net::Twitter`的登录，这不是 [`Twitter::API`](https://metacpan.org/pod/Twitter::API) ，这不是你现在应该做的，所以我不会分享这段代码。

我*将*分享，然而，如果你[在 Twitter](https://twitter.com/jacobydave) 上关注我，你不仅会知道你距离下一次夏令时改变还有 100 天，你还会看到我写的新博客，就像这篇。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。