# 稳定排序及其解决方法

> 原文：<https://dev.to/jacoby/stable-sort-and-ways-around-it-4ge1>

有一种叫做 [Last.fm](https://www.last.fm/) 的服务可以让你储存你听的歌曲。我已经开始将 Last.fm 的数据导出到一个本地数据库，那里有我从 2008 年 1 月 1 日起在*搜到的*(听了并向他们的系统报告)的所有歌曲。这里我们有前四个，按`id`排序。

```
[
  {
    "artist": "Planes Mistaken For Stars",
    "id": "++2oW+aGn4m9JoPRd5tgXMRuZY4",
    "plays": 1,
    "song": "Police Story / Wasted"
  },
  {
    "artist": "Chairlift",
    "id": "++9JAle7S9huuAw0bUPH2Nx15ds",
    "plays": 1,
    "song": "Crying in Public - Alternate Version"
  },
  {
    "artist": "Ali Farka Touré",
    "id": "++D58rizJf3r8hBYEsI9iChDM0k",
    "plays": 3,
    "song": "Erdi"
  },
  {
    "artist": "fIREHOSE",
    "id": "++ehP6ysoH1N0JHUJB0GhNAx7I4",
    "plays": 1,
    "song": "From One Cums One"
  }
] 
```

但这不是一个有用的分类，是吗？我能想象出两种更好的排序方式:根据播放次数和艺术家/歌曲。可能是艺术家/发行年份/歌曲，但我目前没有所有这些信息。

因为作弊的演示，我*知道*我已经听了[克里斯·斯里](https://www.christhile.com/)的歌 86 遍。(不包括 Punch Brothers 或 Nickel Creek 或 duet 项目。)而且因为专辑，我知道我听过好几首歌的次数是一样的。

```
[
  {
    "artist": "Chris Thile",
    "id": "XgLBRBSQ4jKrrDn2GZ0C7W+rFbY",
    "plays": 1,
    "song": "Trail's End"
  },
  {
    "artist": "Chris Thile",
    "id": "xsoV1/NPsRcbiL47F6JNFLbpBPw",
    "plays": 1,
    "song": "Shadow Ridge"
  },
  {
    "artist": "Chris Thile",
    "id": "z8f9Kr1ZDO+NwweoYp474ebzBwU",
    "plays": 6,
    "song": "Wayside (Back in Time)"
  },
  {
    "artist": "Chris Thile",
    "id": "zvDn5OFuvO99CyfCeBizrlxeHuE",
    "plays": 6,
    "song": "I'm Yours If You Want Me"
  },
  {
    "artist": "Chris Thile",
    "id": "ZWvpfxcBiOKDaLCMCQ+2PyvICeU",
    "plays": 1,
    "song": "Ready for Anything"
  }
] 
```

所以，我们想要的是他的歌曲列表，首先按播放次数排序，然后按歌曲名称的字母顺序排序。

```
#!/usr/bin/env perl

use strict;
use warnings;
use utf8;
use feature qw{ postderef say signatures state switch };
no warnings qw{ experimental::postderef
    experimental::smartmatch
    experimental::signatures };

use JSON;
use List::Util qw{uniq};

my $json = JSON->new->pretty->canonical;
my $file = 'lastfm.json';
if ( -f $file && open my $fh, '<', $file ) {
    my $text = join '', <$fh>;
    close $fh;

    my $obj = $json->decode($text);
    my $bigger->@* =
        grep { $_->{plays} > 19 } $obj->@*; # at least 10 plays per song

    my $thile->@* =
        grep { defined $_->{artist} && $_->{artist} eq 'Chris Thile' }
        $obj->@*;

    # reminder that you read this line back to front, so we are
    # sorting on song name first, than play count
    say join "\n", map { join "\t", $_->{plays}, $_->{song} }
        sort { $b->{plays} <=> $a->{plays} }
        sort { $a->{song} cmp $b->{song} }
        $thile->@*;
}

# 30    Heart in a Cage
# 6 Brakeman's Blues
# 6 How to Grow a Woman From the Ground
# 6 I'm Yours If You Want Me
# 6 Stay Away
# 6 Wayside (Back in Time)
# 5 Cazadero
# 5 If The Sea Was Whiskey
# 5 O Santo De Polvora
# 5 The Beekeeper
# 5 The Eleventh Reel
# 5 You're An Angel and I'm Gonna Cry
# 4 Dead Leaves and the Dirty Ground
# 3 Alderaanian Melody
# ...
# There are 86 songs in total, so we're cutting this down 
```

Perl 提供了`stable sort`，我们可以像这样对排序进行堆栈，知道如果不进行排序，顺序会得到保留。

那么，JavaScript 呢？

```
#!/usr/bin/env node

const fs = require("fs");
var text = fs.readFileSync("lastfm.json", "utf8");
var obj = JSON.parse(text);

var thile = obj
  .filter(t => t.artist === "Chris Thile")
  .sort((a, b) => {
    if (a.song > b.song) {
      return 1;
    }
    if (a.song < b.song) {
      return -1;
    }
    return 0;
  })
  .sort((a, b) => b.plays - a.plays);

console.log(thile.map(t => [t.plays, t.song].join("\t")).join("\n"));

// 30   Heart in a Cage
// 6    Brakeman's Blues
// 6    I'm Yours If You Want Me
// 6    Wayside (Back in Time)
// 6    Stay Away
// 6    How to Grow a Woman From the Ground
// 5    You're An Angel and I'm Gonna Cry
// 5    O Santo De Polvora
// 5    If The Sea Was Whiskey
// 5    Cazadero
// 5    The Eleventh Reel
// 5    The Beekeeper
// 4    Dead Leaves and the Dirty Ground
// 3    Watch 'at Breakdown
// 3    Alderaanian Melody

// W before A? S before H? 
```

在我们按剧排序之前，我们先按歌名排序。我们可以*看到*这一点。发生了什么事？

> 我希望 JS 能提供稳定的排序，但是委员会甚至在 1996 年都不能就此达成一致，你可以在高于 API 的一些成本上实现稳定
> 
> –[布伦丹·艾奇(@布伦丹·艾奇)](https://twitter.com/BrendanEich/)

(我正在和 Gizmo 讨论这个问题，并向 Brendan 插了一句话，他回答了。我们生活在一个可以问“嘿，语言创造者？你的语言怎么是这样的？”并得到答案。多酷啊。)

所以，我们的目标是自己稳定它，我知道怎么做。

```
#!/usr/bin/env node

const fs = require("fs");
var text = fs.readFileSync("lastfm.json", "utf8");
var obj = JSON.parse(text);

var thile = obj
  .filter(t => t.artist === "Chris Thile")
  .sort((a, b) => {
    /*
      the sort for plays comes first, but instead the short form
      we just handle the greater or less then cases, letting the 
      equals case drop through...  
     */
    if (a.plays < b.plays) { return 1; }
    if (a.plays > b.plays) { return -1; }
    /*
      ... to here, where we can do greater than and less than on
      strings. I don't GET or APPROVE of that, but I am given no
      other choice, and so we sort on song title.
     */
    if (a.song > b.song) { return 1; }
    if (a.song < b.song) { return -1; }
    /*
      And we return 0 if everything else is even.
     */
    return 0;
  });
console.log(thile.map(t => [t.plays, t.song].join("\t")).join("\n"));

// 30   Heart in a Cage
// 6    Brakeman's Blues
// 6    How to Grow a Woman From the Ground
// 6    I'm Yours If You Want Me
// 6    Stay Away
// 6    Wayside (Back in Time)
// 5    Cazadero
// 5    If The Sea Was Whiskey
// 5    O Santo De Polvora
// 5    The Beekeeper
// 5    The Eleventh Reel
// 5    You're An Angel and I'm Gonna Cry
// 4    Dead Leaves and the Dirty Ground
// 3    Alderaanian Melody 
```

给你。我是否暗示过我是一个沮丧的曼陀林演奏者？

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。