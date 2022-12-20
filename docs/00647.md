# 使用命令行从 Playstation Store 搜索您最喜爱的 PS4 游戏

> 原文：<https://dev.to/lucasepe/search-your-favorite-ps4-games-from-playstation-store-using-the-command-line-1aa8>

# Go PS4

> 使用命令行在 Playstation Store 中搜索您最喜欢的 PS4 游戏。

*   免费[开源](https://github.com/lucasepe/go-ps4)软件
*   在 [Linux](https://github.com/lucasepe/go-ps4/releases/download/v1.0.0/ps4-linux-amd64) 、 [Mac OSX](https://github.com/lucasepe/go-ps4/releases/download/v1.0.0/ps4-darwin-amd64) 、 [Windows](https://github.com/lucasepe/go-ps4/releases/download/v1.0.0/ps4-windows-amd64.exe) 上工作
*   只有一个可移植的二进制文件

## 如何使用

### 列出可用选项

```
$ ps4 -h
PS Store CLI
Usage: go-ps4 [OPTIONS] [Game Title]
  -addons
        show also extra contents
  -free
        show only free titles
  -lang string
        language (it, en) (default "it")
  -weekly-deals
        show only weekly deals titles 
```

Enter fullscreen mode Exit fullscreen mode

### 只搜索免费游戏

```
$ ps4 -free
3on3 Freestyle...................................................... Gratuito
AirMech® Arena...................................................... Gratuito
A KING'S TALE: FINAL FANTASY XV..................................... Gratuito
anywhereVR.......................................................... Gratuito
APB Reloaded........................................................ Gratuito
...(more games).... 
```

Enter fullscreen mode Exit fullscreen mode

### 搜索特定游戏

```
$ ps4 "division 2"
Tom Clancy's The Division® 2 - Standard Edition..................... €69,99
Tom Clancy’s The Division®2 - Pacchetto di benvenuto................ €14,99
Pacchetto Valuta premium 500 per Tom Clancy's The Division®2........ €4,99
Pacchetto Valuta premium 1050 per Tom Clancy's The Division®2....... €9,99
Tom Clancy's The Division® 2 - Ultimate Edition..................... €119,99
Pacchetto Valuta premium 6500 per Tom Clancy's The Division®2....... €49,99 
```

Enter fullscreen mode Exit fullscreen mode

### 只显示每周交易

```
$ ps4 -weekly-deals 
```

Enter fullscreen mode Exit fullscreen mode