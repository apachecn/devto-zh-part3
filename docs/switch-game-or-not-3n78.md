# 切换游戏还是不切换？

> 原文：<https://dev.to/pofigster/switch-game-or-not-3n78>

我是播客 CAGCast 的忠实粉丝，这是一个关于视频游戏的节目。几个月前，他们发明了一种叫做“切换游戏还是不切换游戏？”其中一个主持人会把假的任天堂 Switch 游戏加入到那周的新游戏列表中。这有什么好玩的？因为很多 Switch 游戏的名字都很可笑。《全能树:最后的梦想家》、《猎人的遗产:完美版》和《氧气喷射》都是上周上市的游戏。这是如此容易滑入可笑的游戏名称没有明显的什么是真实的，什么是虚假的。

我摆弄过使用马尔可夫链来生成新的文本(单词、推文等)。)并发现它非常快速和简单。马尔可夫链是一种在状态间随机移动的方式。现在，他们正常设置，没有记忆。所以，如果你有天气的马尔可夫链，你可能有三种状态，晴天，多云和雨天。每种状态(比如多云)都有可能转移到一个新的状态(晴天或雨天)或保持不变，总概率为 1(因为一切要么改变，要么保持不变，这是有保证的)。在这种情况下，先前的状态是雨天还是晴天并不重要，确定下一个状态只需要知道当前是阴天。

当在文本上使用马尔可夫链时，您可以认为这是生成一系列记号(字母、单词等)。)其中当前标记通知新标记的概率。如果我们对单词集['hi '，' hello '，' help']建立一个马尔可夫链，并对字母进行标记，那么当我们处于' h '状态时，将有 33%的机会移动到' I '，67%的机会移动到' e '，0%的机会移动到任何其他字母(或保持不变)。对于状态“l ”,有 33%的机会移动到“o ”, 33%的机会移动到“p ”, 33%的机会保持“l”。对于更复杂的词汇，这将变得更加复杂。另一件要考虑的事情是添加一个字符，它将生成第一个字母和一个终止序列，以便知道单词何时完成。所以我们可以把单词填充成['*hi`','*hello`'，' *help`']，这样我们就可以从'*'的状态开始一个新单词，只要我们生成一个'`'，我们就知道我们完成了。

我提到过马尔可夫链没有记忆，所以从单个字母的状态出发，即使在我们简单的词汇中，我们也可以把很多个 l 连在一起，这使得事物看起来不像单词。所以我们可以通过滑动字符窗口来建立记忆。而不是' *help ` '有'*'、' h '、' e '、' l '、' p '、' T0 '、' T3 '(我增加了填充以反映窗口的大小)。这有助于确保“ll”总是移动到“l”以外的字母。

给定一个词汇表，我们需要一种方法来识别所有滑动窗口和预测的下一个令牌。传统上，这是存储在一个矩阵中，但在我们的情况下，我们有这么多可能的当前状态和这么多的下一个状态(其中许多将是空的)，它可能不是有效的存储方式。在 Python 中，我使用了一个字典，将当前状态作为键，并将下一个令牌存储为列表。

在下面的代码中，希望你能理解我的过程。我取了一个任天堂 Switch 游戏名的列表(我在游戏名的完整列表上构建了我的列表)，并在每个列表的开头和结尾分别填充了我的“记忆”窗口“*”和“`”(这些字符很少使用，少数使用的“*”被我删除)。然后我遍历每个标题，将它分解成多个窗口，并使用那个键将下一个窗口添加到字典中。完成后，我有了一个包含大量重复的每个下一个字符的原始列表。因此，我计算每个下一个标记的累积概率(以支持随机选取下一个字符)。最后，我有了一个很好的 JSON 类型的字典结构，我把它写到一个文件中。

```
from collections import Counter
from functools import reduce
import re
import json

def parse_name(name, n, nl):
    name = "*"*n+name+"`"*n
    l = name.__len__()-n
    for i in range(l):
        try:
            nl[name[i:(i+n)]].append(name[i+n])
        except KeyError:
            nl[name[i:(i+n)]] = [name[i+n]]
    return nl

def calc_percent(n):
    ct = dict(Counter(n))
    p = [v / sum(ct.values()) for v in ct.values()]
    cp = reduce(lambda c, x: c+[c[-1] + x], p, [0])[1:]
    ky = list(ct.keys())
    output = {}
    for ir in range(cp.__len__()):
        output[ky[ir]] = cp[ir]
    return output

def gen_markov(known, n):
    next_letter = {}
    for k in known:
        k = re.sub("[\*\(\)]", "", k)
        next_letter = parse_name(k, n, next_letter)

    for i in next_letter.keys():
        next_letter[i] = calc_percent(next_letter[i])

    with open("markov_probabilities_{}.json".format(str(n)), 'w') as outfile:
        json.dump(next_letter, outfile, indent=4, sort_keys=True)
    return 0

known_games = ["SEGA AGES Phantasy Star","RollerCoaster Tycoon Adventures","ACA NEOGEO METAL SLUG 5","Hyperide: Vector Raid","Peace, Death! Complete Edition","InkyPen","Almightree: The Last Dreamer","TRYBIT LOGIC","Firewatch","Rain World","Nippon Marathon",Omensight: Definitive Edition","Atari Flashback Classics","Donut County","Venture Towns","Lazy Galaxy: Rebel Story","City Builder","Oxyjet","Knights of Pen and Paper Bundle","RAZED","MIND: Path to Thalamus","Race Arcade","Blue Rider","Clue: The Classic Mystery Game","Hunter's Legacy: Purrfect Edition","Party Arcade","Starman","Mahjong Solitaire Refresh","Sheltered","Knights of Pen & Paper 2 Deluxiest Edition","Big Bash Boom","GRIS","Arcade Archives ATHENA"]  _ = gen_markov(known_games, 4) 
```

现在我有了运行马尔可夫过程所需的数据，我需要一种生成新名字的方法。加载我们之前保存的 json 文件，生成我们的起始状态(这里是“****”)，并使用一个`while`循环继续运行，直到到达终止状态。拥有一个字典的好处是，我们可以快速找出所有可能的下一个状态(我们已经删除了所有的 0)，生成一个随机数，并找出大于该随机数的第一个值的密钥。

```
import json
import random

def choose_letter(s, p):
    r = random.random()
    p = probs[s]
    i = min([i for i, x in enumerate([r < l for l in list(p.values())]) if x])
    return list(p.keys())[i]

def gen_title(title, n, p):
    keep_going = True
    while keep_going:
        if title[-n:] == "`"*n:
            keep_going = False
        else:
            title = title + choose_letter(title[-n:], p)
    return title[n:-n]

memory_length = 4

with open('markov_probabilities_{}.json'.format(str(memory_length))) as jsonfile:
    probs = json.load(jsonfile)

start = '*' * memory_length
out = gen_title(start, memory_length, probs)
print(out) 
```

现在，有很多其他的东西需要被做来帮助改善这一点。例如，我们不希望我们的进程产生一个实际的游戏名称，甚至是一个太接近的名称。我们也不想要太长的游戏名或者太多的标点符号。因此，我对你在我的 GitHub repo 中看到的内容进行了一些检查。

为了最终的实现，我用 node.js、Express、Angular (1.6，我知道...)，和 MySQL 在一些可爱的[数字海洋](https://m.do.co/c/92b8439f5af7)硬件上。我甚至遵循了一个教程，将我的 node.js 应用程序“dockerized ”(稍后可能会有另一篇文章)。整个项目在 [GitHub](https://github.com/bmewing/switch_game_or_not) 上，我还没有使用功能分支或任何 CI/CD 的东西，所以请不要太难判断。此外，我会感谢反馈，特别是关于网站开发的东西。

总的来说，这是一次有趣的经历。我和主持人分享了这个应用程序，他们很喜欢它(现在我希望在节目中大声喊出来)，所以我们将看看它是否有腿。在短短几天内从想法到执行总是很棒的。对我来说，最令人沮丧的部分是网站开发部分。我通过制作 API 获得了 node.js 和 express 的一些内容，但不是全部。Angular 对我来说总是一个令人沮丧的经历，但是由于 web 开发不是我的日常工作，甚至不是我的副业，现在很难证明学习其他东西是正确的。