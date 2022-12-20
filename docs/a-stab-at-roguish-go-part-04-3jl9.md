# 流氓围棋的尝试第 4 部分

> 原文：<https://dev.to/shindakun/a-stab-at-roguish-go-part-04-3jl9>

# ATLG Rogue

## 填充地下城

我一直很开心不去想 API 或者工具，所以我们要坚持我们流氓式的代码一段时间。让我们回到深处吧！这一次，我们将把基本的生物创建代码添加到我们的地牢代码中。这将使我们有一个很好的地方开始添加一些代码，或者开始实际战斗，或者可能首先保存和加载游戏状态。我想我们会看到的。

现在，让我们开始吧。如果你还没有，你可能想看看上次的[代码。](https://dev.to/shindakun/a-stab-at-roguish-go-part-03-4f4i)

* * *

## 代码演练

这将与之前的帖子略有不同，因为我已经决定打破`main.go`的生物代码，希望让一切更有条理。目前，GitHub 回购有点落后，但我会努力更新，因为在一个地方看到所有内容可能会更容易。我们也将很快将演员代码从这里移出。

### main.go

我们开始添加我们的[【骰子】](https://github.com/shindakun/die)滚动码和我们新的`creature`目录。请注意，这只是一个子目录的`at`，这是如何在我的硬盘上，这可能会在未来发生变化，但现在，这就是它的方式。

```
package main

import (
  "fmt"
  "math/rand"
  "os"
  "time"

  "github.com/gdamore/tcell"
  "github.com/mattn/go-runewidth"
  "github.com/shindakun/at/creatures"
  "github.com/shindakun/die"
) 
```

Enter fullscreen mode Exit fullscreen mode

我正在做的基本想法是有一个`Actors`的片段，我们可以在游戏时间中每次前进时循环通过它。这段代码可能也会从 main 中取出，放在它自己的目录中。`Actors`持有一个`Actor`,这个结构包含了关于这个参与者的基本信息。它是内部 ID、位置(x，y)、它所在的“楼层”以及它的颜色。我们也看到了`creatures.Creature`的第一次使用，稍后我们将会看到。

```
// Actors struct contains slice of Actor
type Actors struct {
  Actors []Actor
}

// Actor strcut
type Actor struct {
  ID       string
  X        int
  Y        int
  Floor    int
  Color    tcell.Style
  Creature creatures.Creature
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只是让演员随意移动。在未来，我们会添加敌对行为的代码，可能还会添加一些基于现有`actor`或`creature`的特定代码。我大概可以把随机种子从这里拉出来，应该可以真正消除恐慌，这样我们就不会崩溃了...也许下次吧。

```
// Move moves the current actor randomly
func (a *Actor) Move(floor int, s tcell.Screen) {
  if floor == a.Floor {
    /*
        1
       4+2
        3
    */
    rand.Seed(time.Now().UTC().UnixNano())
    var xx, yy int
    d, err := die.Roll("1d4")
    if err != nil {
      panic("die roll")
    }
    switch d {
    case 1:
      xx = -1
    case 2:
      yy = 1
    case 3:
      xx = 1
    case 4:
      yy = -1
    }
    l, _, _, _ := s.GetContent(a.X+xx, a.Y+yy)
    if l == '#' {

    } else {
      a.X = a.X + xx
      a.Y = a.Y + yy
    }

  }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了`Move()`之外，我们还有一些“效用”函数，`Draw()`和`GetLocation()`。它们应该很明显。

```
// Draw draws the current Actor to the screen
func (a *Actor) Draw(s tcell.Screen, f int) {
  if f == a.Floor {
    emitStr(s, a.X, a.Y, a.Color, string(a.Creature.GetRune()))
  }
}

// GetLocation returns the coordinates of the current Actor
func (a *Actor) GetLocation() (x, y int) {
  return a.X, a.Y
} 
```

Enter fullscreen mode Exit fullscreen mode

`NewActor()`将返回一个我们可以添加到`Actors`片段中的演员。注意，我实际上还没有使用`ID`。我的想法是，我可以用它在切片中创建命名的生物。在这之后，我们回到一些类似于[第二篇文章](https://dev.to/shindakun/a-stab-at-roguish-go-part-02-5b45)的代码。

```
// NewActor creates a new actor
func NewActor(x, y, f int, color tcell.Style, c creatures.Creature) Actor {
  return Actor{
    X:        x,
    Y:        y,
    Floor:    f,
    Color:    color,
    Creature: c,
  }
}

type player struct {
  r      rune
  x      int
  y      int
  health int
  level  int
}

func emitStr(s tcell.Screen, x, y int, style tcell.Style, str string) {
  for _, c := range str {
    var comb []rune
    w := runewidth.RuneWidth(c)
    if w == 0 {
      comb = []rune{c}
      c = ' '
      w = 1
    }
    s.SetContent(x, y, c, comb, style)
    x += w
  }
}

func main() {
  debug := false

  player := player{
    r: '@',
    x: 3,
    y: 3,
  }

  var msg string

  mapp := [9][9]rune{
    {'#', '#', '#', '#', '#', '#', '#', '#', '#'},
    {'#', '.', '.', '.', '.', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '.', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '#', '.', '.', '.', '#'},
    {'#', '.', '.', '#', '#', '#', '.', '.', '#'},
    {'#', '.', '.', '.', '#', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '.', '.', '>', '.', '#'},
    {'#', '.', '.', '.', '.', '.', '.', '.', '#'},
    {'#', '#', '#', '#', '#', '#', '#', '#', '#'},
  }

  mapp2 := [9][9]rune{
    {'#', '#', '#', '#', '#', '#', '#', '#', '#'},
    {'#', '#', '.', '.', '.', '.', '.', '#', '#'},
    {'#', '.', '.', '.', '.', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '.', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '#', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '.', '.', '.', '.', '#'},
    {'#', '.', '.', '.', '.', '.', '<', '.', '#'},
    {'#', '#', '.', '.', '.', '.', '.', '#', '#'},
    {'#', '#', '#', '#', '#', '#', '#', '#', '#'},
  }

  level := 1
  current := mapp

  tcell.SetEncodingFallback(tcell.EncodingFallbackASCII)
  s, e := tcell.NewScreen()
  if e != nil {
    fmt.Fprintf(os.Stderr, "%v\n", e)
    os.Exit(1)
  }
  if e = s.Init(); e != nil {
    fmt.Fprintf(os.Stderr, "%v\n", e)
    os.Exit(1)
  }

  white := tcell.StyleDefault.
    Foreground(tcell.ColorWhite).
    Background(tcell.ColorBlack)
  grey := tcell.StyleDefault.
    Foreground(tcell.ColorGray).
    Background(tcell.ColorBlack)
  burlyWood := tcell.StyleDefault.
    Foreground(tcell.ColorBurlyWood).
    Background(tcell.ColorBlack)
  brown := tcell.StyleDefault.
    Foreground(tcell.ColorBrown).
    Background(tcell.ColorBlack)

  s.SetStyle(tcell.StyleDefault.
    Foreground(tcell.ColorWhite).
    Background(tcell.ColorBlack))
  s.EnableMouse()
  s.Clear() 
```

Enter fullscreen mode Exit fullscreen mode

我们将只构建一些参与者来填充我们现在拥有的两个级别。很快，我们将构建一些级别生成代码，并在该步骤中填充它。在这之后，我们进入我们的输入程序。

```
 a := &Actors{}
  a.Actors = append(a.Actors, NewActor(4, 3, 1, brown, &creatures.Pig{R: 'p', Health: 10, Description: "From the realm of Paradox... the Pig."}))
  a.Actors = append(a.Actors, NewActor(5, 2, 1, brown, &creatures.Pig{R: 'p', Health: 10, Description: "Oink."}))

  a.Actors = append(a.Actors, NewActor(4, 3, 2, brown, &creatures.Rat{R: 'r', Health: 10, Description: ""}))
  a.Actors = append(a.Actors, NewActor(5, 2, 2, brown, &creatures.Rat{R: 'r', Health: 10, Description: ""}))
  a.Actors = append(a.Actors, NewActor(4, 3, 2, brown, &creatures.Rat{R: 'r', Health: 10, Description: ""}))
  a.Actors = append(a.Actors, NewActor(5, 2, 2, brown, &creatures.Rat{R: 'r', Health: 10, Description: ""}))

  quit := make(chan struct{})
  go func() {
    for {
      x, y := s.Size()
      ev := s.PollEvent()
      switch ev := ev.(type) {
      case *tcell.EventKey:

        // arrows/named keys
        switch ev.Key() {
        case tcell.KeyRune:
          switch ev.Rune() { 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个非常快速的“查看”功能，它并不完美，但它现在可以工作。接下来，我们可以上下楼梯。请注意我们是如何为演员动作添加基本的`for`循环的。我们可能会在下一个版本的代码中将它提升到自己的功能中。

```
 case ':':
            g := current[player.x-1][player.y-1]
            if g == '.' {
              msg = "You see some dirt."
            } else if g == '>' {
              msg = "You see some stairs leading down."
            } else if g == '<' {
              msg = "You see some stairs leading up."
            }
          case '>':
            g := current[player.x-1][player.y-1]
            if g == '>' {
              level++
              s.Clear()
            }
          case '<':
            g := current[player.x-1][player.y-1]
            if g == '<' {
              level--
              s.Clear()
            }
          case 'h':
            r, _, _, _ := s.GetContent(player.x-1, player.y)
            if r == '#' {

            } else if player.x-1 >= 0 {
              player.x--
            }
            for i := range a.Actors {
              a.Actors[i].Move(level, s)
            }
          case 'l':
            r, _, _, _ := s.GetContent(player.x+1, player.y)
            if r == '#' {

            } else if player.x+1 < x {
              player.x++
            }
            for i := range a.Actors {
              a.Actors[i].Move(level, s)
            }
          case 'k':
            r, _, _, _ := s.GetContent(player.x, player.y-1)
            if r == '#' {

            } else if player.y-1 >= 0 {
              player.y--
            }
            for i := range a.Actors {
              a.Actors[i].Move(level, s)
            }
          case 'j':
            r, _, _, _ := s.GetContent(player.x, player.y+1)
            if r == '#' {

            } else if player.y+1 < y {
              player.y++
            }
            for i := range a.Actors {
              a.Actors[i].Move(level, s)
            }
          }
        case tcell.KeyEscape, tcell.KeyEnter:
          close(quit)
          return
        case tcell.KeyRight:
          r, _, _, _ := s.GetContent(player.x+1, player.y)
          if r == '#' {

          } else if player.x+1 < x {
            player.x++
          }
          for i := range a.Actors {
            a.Actors[i].Move(level, s)
          }
        case tcell.KeyLeft:
          r, _, _, _ := s.GetContent(player.x-1, player.y)
          if r == '#' {

          } else if player.x-1 >= 0 {
            player.x--
          }
          for i := range a.Actors {
            a.Actors[i].Move(level, s)
          }
        case tcell.KeyUp:
          r, _, _, _ := s.GetContent(player.x, player.y-1)
          if r == '#' {

          } else if player.y-1 >= 0 {
            player.y--
          }
          for i := range a.Actors {
            a.Actors[i].Move(level, s)
          }
        case tcell.KeyDown:
          r, _, _, _ := s.GetContent(player.x, player.y+1)
          if r == '#' {

          } else if player.y+1 < y {
            player.y++
          }
          for i := range a.Actors {
            a.Actors[i].Move(level, s)
          }

        case tcell.KeyCtrlD:
          debug = !debug
        case tcell.KeyCtrlL:
          s.Clear()
          s.Sync()
        }
      case *tcell.EventResize:
        s.Sync()
      }
    }
  }()

loop:
  for {
    select {
    case <-quit:
      break loop
    case <-time.After(time.Millisecond * 50):
    }

    // s.Clear()
    dbg := fmt.Sprintf("player x: %d y: %d", player.x, player.y)
    if debug == true {
      var yy int
      if player.y == 0 {
        _, yy = s.Size()
        yy--
      } else {
        yy = 0
      }
      emitStr(s, 0, yy, white, dbg)
    } 
```

Enter fullscreen mode Exit fullscreen mode

在我们将任何东西渲染到屏幕上之前，我们需要确保我们考虑到了我们当前所处的水平。一旦生成代码就绪，这应该变得更好，而不仅仅是一个`if`语句——并且我决定如何存储一切。

```
 if level == 1 {
      current = mapp
    } else if level == 2 {
      current = mapp2
    }

    // draw "map"
    var color tcell.Style
    for i := 0; i < 9; i++ {
      for j := 0; j < 9; j++ {
        if current[i][j] == '#' {
          color = grey
        }
        if current[i][j] == '.' {
            color = burlyWood
        }

        emitStr(s, i+1, j+1, color, string(current[i][j]))
      }
    }
    emitStr(s, 0, 0, white, msg)

    for i := range a.Actors {
      a.Actors[i].Draw(s, level)
    }

    emitStr(s, player.x, player.y, white, string(player.r))
    s.Show()
  }

  s.Fini()
} 
```

Enter fullscreen mode Exit fullscreen mode

这就把我们带到了`main`下面，现在我们来看看导入生物目录。我有几个不同的生物，但在这一点上它们或多或少是一样的，所以我不会把它们都包括进来。其余的很快就会上传到 GitHub 上。

* * *

首先，我们有`creatures.go`。这可能不是最好的处理方式，但我决定尝试一下，看看效果如何。每个生物都会满足一个接口。目前，这意味着它们必须具备四种功能。如你所见，它们都是不言自明的。

### 生物/生物. go

```
package creatures

type Creature interface {
  GetRune() rune
  GetHealth() int
  GetDescription() string
  TakeDamage(int)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 生物/猪. go

这种处理生物的界面风格的直接缺点是，目前它们都实现了或多或少相同的代码。这让我想放弃它，重新设计它的工作方式。嗯，也许在我们图放地图生成或战斗之后。同样，代码很容易理解，我们实现函数来满足我们的接口。这允许我们从所说的生物中调用生物和我们需要的功能。例如，`p.GetRune`返回`'p'`。

```
package creatures

// Pig struct
type Pig struct {
  R           rune   `json:"r,omitempty"`
  Health      int    `json:"health,omitempty"`
  Description string `json:"description,omitempty"`
}

// GetRune returns rune
func (p *Pig) GetRune() rune {
  return p.R
}

// GetHealth returns int
func (p *Pig) GetHealth() int {
  return p.Health
}

// GetDescription returns string
func (p *Pig) GetDescription() string {
  return p.Description
}

// TakeDamage applies damage to health
func (p *Pig) TakeDamage(i int) {
  p.Health = p.Health - i
} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

这次差不多了，真的，我被打败了。我想我有点不舒服，所以我们暂时就到此为止。如果你有任何问题或者有什么不清楚的地方，请在评论中告诉我。

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content container-lg" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [建造下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
| [使用 REST API](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-01/main.go) |
| [继续休息冒险](https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-02/main.go) |
| [现在发送休息请求](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-03/main.go) |
| [REST API 和模板上的位](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-04/main.go) |
| [再次通过 API 发送电子邮件](https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-05/main.go) |
| [让我们模块化吧！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i) | [src](https://github.com/shindakun/mailgunner) |
| [让我们再一次模块化！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd) | [src](https://github.com/shindakun/mailgunner) |
| [构建开发日志第 1 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-01/main.go) |
| [构建开发日志第 2 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-02-179c) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-02/main.go) |
| [构建开发日志第 3 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-03/main.go) |
| [构建开发日志第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-04/main.go) |
| [构建开发日志第 5 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-05/main.go) |
| [按扩展名 01 列出文件](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [按扩展名 02 列出文件](https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [开发至 API 01](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devtoapi-01/main.go) |
| [开发至 API 02](https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g) | 参见上面的代码 |
| [发行人](https://dev.to/shindakun/attempting-to-learn-go-issuer-01-5f0k) |

…</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *