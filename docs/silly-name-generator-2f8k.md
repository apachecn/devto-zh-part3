# 愚蠢的名字生成器

> 原文：<https://dev.to/zeeter/silly-name-generator-2f8k>

# 目录

1.  [简介](#org8fe4880)
2.  [名单](#org23be92e)
    1.  [名字](#org1cf212e)
    2.  [姓氏](#orgdcd08b3)
3.  [选择名字](#orgfbbe1ca)
4.  [主](#org3d8d59a)

# 简介

这是李·沃恩的[不切实际的 Python](https://nostarch.com/impracticalpythonprojects) 的第一本书。
我的目标是做这本书里的每一个项目，以我自己的风格作为一个文学项目。
因此，请善待我，我只是一个旨在学习的爱好者。

# 姓名列表

## 第一个名字

首先，双关语。我们需要一个列表供 python 程序选择。在 python 中，用列表或元组最容易做到这一点。这本书使用了一个元组，因为它在执行过程中从不
改变，所以不需要列表。
诚然，我复制粘贴了这一部分和姓氏，因为我不想把它们都打出来。

```
firstnames = ('Baby Oil', 'Bad News', 'Big Burps', "Bill 'Beenie-Weenie'",
     "Bob 'Stinkbug'", 'Bowel Noises', 'Boxelder', "Bud 'Lite' ",
     'Butterbean', 'Buttermilk', 'Buttocks', 'Chad', 'Chesterfield',
     'Chewy', 'Chigger', 'Cinnabuns', 'Cleet', 'Cornbread', 'Crab Meat',
     'Crapps', 'Dark Skies', 'Dennis Clawhammer', 'Dicman', 'Elphonso',
     'Fancypants', 'Figgs', 'Foncy', 'Gootsy', 'Greasy Jim', 'Huckleberry',
     'Huggy', 'Ignatious', 'Jimbo', "Joe 'Pottin Soil'", 'Johnny',
     'Lemongrass', 'Lil Debil', 'Longbranch', '"Lunch Money"', 'Mergatroid',
     '"Mr Peabody"', 'Oil-Can', 'Oinks', 'Old Scratch', 'Ovaltine',
     'Pennywhistle', 'Pitchfork Ben', 'Potato Bug', 'Pushmeet',
     'Rock Candy', 'Schlomo', 'Scratchensniff', 'Scut',
     "Sid 'The Squirts'", 'Skidmark', 'Slaps', 'Snakes', 'Snoobs',
     'Snorki', 'Soupcan Sam', 'Spitzitout', 'Squids', 'Stinky',
     'Storyboard', 'Sweet Tea', 'TeeTee', 'Wheezy Joe',
     "Winston 'Jazz Hands'", 'Worms') 
```

## 姓氏

和名字的想法一样。而是为了姓氏！

```
lastnames = ('Appleyard', 'Bigmeat', 'Bloominshine', 'Boogerbottom',
    'Breedslovetrout', 'Butterbaugh', 'Clovenhoof', 'Clutterbuck',
    'Cocktoasten', 'Endicott', 'Fewhairs', 'Gooberdapple', 'Goodensmith',
    'Goodpasture', 'Guster', 'Henderson', 'Hooperbag', 'Hoosenater',
    'Hootkins', 'Jefferson', 'Jenkins', 'Jingley-Schmidt', 'Johnson',
    'Kingfish', 'Listenbee', "M'Bembo", 'McFadden', 'Moonshine', 'Nettles',
    'Noseworthy', 'Olivetti', 'Outerbridge', 'Overpeck', 'Overturf',
    'Oxhandler', 'Pealike', 'Pennywhistle', 'Peterson', 'Pieplow',
    'Pinkerton', 'Porkins', 'Putney', 'Quakenbush', 'Rainwater',
    'Rosenthal', 'Rubbins', 'Sackrider', 'Snuggleshine', 'Splern',
    'Stevens', 'Stroganoff', 'Sugar-Gold', 'Swackhamer', 'Tippins',
    'Turnipseed', 'Vinaigrette', 'Walkingstick', 'Wallbanger', 'Weewax',
    'Weiners', 'Whipkey', 'Wigglesworth', 'Wimplesnatch', 'Winterkorn',
    'Woolysocks') 
```

# 选择一个名字

要选择东西，需要用到 python 的 random 模块。Random 在生成 sudo 随机数方面做了很多巧妙的事情。
Random.choice 是我们这次要用的。

```
import random 
```

选择名称后，它们将被返回以便显示。

```
def chooser(firstnames,lastnames):
    firstname = random.choice(firstnames)
    lastname = random.choice(lastnames)
    return (firstname, lastname) 
```

# 主

最后，我们把它们放在一起。从技术上来说，我应该写一个“主”程序块，但是这个程序也可以。选择器在外面，以防你需要它。

本质上，这个程序从随机列表中选择两个名字，将它们返回给变量
firstname 和 lastname，然后打印它们。

我个人认为原作者习惯了许多换行符，但无论如何。

```
# Check sillyname.org for details
 # Imports
import sys
import random

# Internal Functions
def chooser(firstnames,lastnames):
    firstname = random.choice(firstnames)
    lastname = random.choice(lastnames)
    return (firstname, lastname)

# Main script

def main():
    print("Welcome to the Pysch 'side kick name picker'!\n")
    print("A name just like Sean would pick for Gus:\n\n")
    firstnames = ('Baby Oil', 'Bad News', 'Big Burps', "Bill 'Beenie-Weenie'",
     "Bob 'Stinkbug'", 'Bowel Noises', 'Boxelder', "Bud 'Lite' ",
     'Butterbean', 'Buttermilk', 'Buttocks', 'Chad', 'Chesterfield',
     'Chewy', 'Chigger', 'Cinnabuns', 'Cleet', 'Cornbread', 'Crab Meat',
     'Crapps', 'Dark Skies', 'Dennis Clawhammer', 'Dicman', 'Elphonso',
     'Fancypants', 'Figgs', 'Foncy', 'Gootsy', 'Greasy Jim', 'Huckleberry',
     'Huggy', 'Ignatious', 'Jimbo', "Joe 'Pottin Soil'", 'Johnny',
     'Lemongrass', 'Lil Debil', 'Longbranch', '"Lunch Money"', 'Mergatroid',
     '"Mr Peabody"', 'Oil-Can', 'Oinks', 'Old Scratch', 'Ovaltine',
     'Pennywhistle', 'Pitchfork Ben', 'Potato Bug', 'Pushmeet',
     'Rock Candy', 'Schlomo', 'Scratchensniff', 'Scut',
     "Sid 'The Squirts'", 'Skidmark', 'Slaps', 'Snakes', 'Snoobs',
     'Snorki', 'Soupcan Sam', 'Spitzitout', 'Squids', 'Stinky',
     'Storyboard', 'Sweet Tea', 'TeeTee', 'Wheezy Joe',
     "Winston 'Jazz Hands'", 'Worms')

    lastnames = ('Appleyard', 'Bigmeat', 'Bloominshine', 'Boogerbottom',
    'Breedslovetrout', 'Butterbaugh', 'Clovenhoof', 'Clutterbuck',
    'Cocktoasten', 'Endicott', 'Fewhairs', 'Gooberdapple', 'Goodensmith',
    'Goodpasture', 'Guster', 'Henderson', 'Hooperbag', 'Hoosenater',
    'Hootkins', 'Jefferson', 'Jenkins', 'Jingley-Schmidt', 'Johnson',
    'Kingfish', 'Listenbee', "M'Bembo", 'McFadden', 'Moonshine', 'Nettles',
    'Noseworthy', 'Olivetti', 'Outerbridge', 'Overpeck', 'Overturf',
    'Oxhandler', 'Pealike', 'Pennywhistle', 'Peterson', 'Pieplow',
    'Pinkerton', 'Porkins', 'Putney', 'Quakenbush', 'Rainwater',
    'Rosenthal', 'Rubbins', 'Sackrider', 'Snuggleshine', 'Splern',
    'Stevens', 'Stroganoff', 'Sugar-Gold', 'Swackhamer', 'Tippins',
    'Turnipseed', 'Vinaigrette', 'Walkingstick', 'Wallbanger', 'Weewax',
    'Weiners', 'Whipkey', 'Wigglesworth', 'Wimplesnatch', 'Winterkorn',
    'Woolysocks')
    while True:
    firstname, lastname = chooser(firstnames, lastnames)
    print("{} {}".format(firstname, lastname, file=sys.stderr))
    print("\n\n")

    try_again=input("\n\nTry again? (press n to quit)\n")
    if try_again.lower() == 'n':
        break

    input("press Enter to exit")

if __name__ == '__main__':
    main()
    sys.exit() 
```

要使用它，只需从命令行运行 sillyname.py。
像这样:

```
./sillyname.py 
```

它会输出类似这样的内容:

```
./sillyname.py
Welcome to the Pysch 'side kick name picker'!

A name just like Sean would pick for Gus:

Chesterfield Sugar-Gold

Try again? (press n to quit) 
```

总的来说，我希望你喜欢这个小阅读。