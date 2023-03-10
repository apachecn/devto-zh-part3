# 移动过去教程:伪代码

> 原文：<https://dev.to/aspittel/moving-past-tutorials-pseudocode-13a6>

编码中一个经常被忽视的部分是伪代码。伪代码包括思考解决问题的步骤，并用简单的语言而不是任何运行的编程语言来编写它们。

您可能遇到过“盯着一个空白的文本编辑器文件，不知道从哪里开始”的问题，这通常可以通过编写伪代码并认真思考手头的问题来解决。如果您在编写代码时感到停滞不前，您可能会想要开始编写一些伪代码。

首先，为了写伪代码，我们需要开始思考问题的各个部分。由于计算机是如此的字面意义，我们需要考虑每一个微小的细节，并把它包括进来——如果我们跳过一些东西，那么我们很可能会遇到问题。

首先，我们来想想带我的狗出去的步骤。

```
- I have to find my shoes.
    - If my dog steals said shoes, I have to retrieve them
    - If it's cold or I'm going on a long walk and I'm wearing sneakers or boots, I need to find socks.

- If I need socks, I put one sock on my foot and then the other on my other foot.
- Then, I put the left shoe on my left foot and then the right shoe on the right foot.
    - If they have laces, I tie each shoelace.

- I find Blair's leash, poop bags, and my keys 
    - they're usually on the counter, but sometimes she will move her leash to somewhere else and I'll have to go looking

- I clip on her leash. I then keep holding onto her leash

- I open the door to my apartment, then close it, then lock it.

- We walk to the elevator, press the down button and then wait.

- When the elevator door opens, I walk in and also make sure Blair follows me in. If she doesn't I need to pull her in or pick her up and carry her

- We hit the button to go down to the lobby of our building, wait for the elevator to get to the lobby, and then exit the elevator and the building

Etc. 
```

我可以继续下去，但我认为你在这一点上明白了——这里有几件事需要注意:我非常具体。我写下了每一个可能的步骤。我也写一些可能出错的事情——可能发生的边缘情况是什么？我该如何解决这些问题？

但是，作为第二步，我可以重构它，使它看起来更像真正的代码！

```
If it's cold:
    wear boots
If I'm going for a long walk:
    wear sneakers
If my shoes are not at the door:
    find them
else if my dog steals my shoes:
    chase her down and take them back
If I'm wearing sneakers or boots:
    get socks out of the drawer
    put one sock on my foot and then the other on my other foot
put on left shoe
put on right shoe
if the shoes have laces:
    tie the left shoe
    tie the right shoe

If Blair's leash, poop bags, and my keys are on the counter:
    grab them
else:
    find them in my apartment

Clip on Blair's leash
Hold on to her leash

Open the door to my apartment
Walk through it with Blair
Lock the door

Walk to the elevator
Press the down button
Wait for the elevator until it arrives

When the elevator arrives, get into it
Make sure Blair gets on too.

Press the button for the lobby
Wait for the elevator to get to the lobby
Exit the elevator
Exit the building 
```

这仍然不是代码，我们需要在未来弄清楚功能和组织，但是我们有一个起点。我们看到的不再是空白的文本编辑器，很多内容可以很容易地转换成实际的代码——比如条件语句。

## 为代码挑战

当然，遛狗并不是一个很常见的项目。让我们来讨论一下这对于一个真正的代码挑战来说会是什么样子——比如反转一个字符串。

我可能做的第一件事就是试着在纸上解决问题几次，看看我会如何在纸上解决问题——为了这个确切的原因，我甚至在桌子旁边放了一块白板！

所以，在这种情况下，我会注意到，我把单词的最后一个字母，加到我的反向字符串中。然后是我的倒数第二个字母，依此类推，直到没有其他字母可以添加到反转的字符串中。但是，还有另一种方法:我可以从字符串中取出第一个字母，并将其添加到我的新字符串的末尾，有效地做同样的事情。因此，对于“你好，世界”来说，进程看起来像这样:

```
h
eh
leh
lleh
olleh
 olleh
w olleh
ow olleh
row olleh
lrow olleh
dlrow olleh 
```

现在我已经在纸上解决了这个问题，我需要为它写伪代码。如果我在代码中重复做同样的事情，我知道我需要使用一个循环——在这种情况下，我肯定会对多个字母做同样的事情。

因此，伪代码可能看起来像这样

```
reversed_string = ''
for letter in my_string
    reversed_string = letter + reversed_string 
```

如果你需要更多的中间步骤，我们可以这样写:

```
reversed_string = my_string[0] + reversed_string
reversed_string = my_string[1] + reversed_string
reversed_string = my_string[2] + reversed_string
reversed_string = my_string[3] + reversed_string 
```

在这一点上，您可能会注意到模式，并能够从那里看到循环！你总是可以从更程序化的代码开始，并从那里添加抽象和逻辑来使代码变得枯燥，并使它越来越接近它的最终形式。

## 应用程序的伪代码

因此，在现实世界中，您可能会创建完整的应用程序或向应用程序添加功能，而不是整天解决代码挑战。在这种情况下，在概述了你的预期特性之后，就像我们在[上一篇文章](https://dev.to/aspittel/moving-past-tutorials-receiving-a-problem-to-solve-2hf8)中讨论的那样，你可以一次选择一个特性并为其编写伪代码。

例如:

```
User clicks on a button:
    A modal pops up with a form that has the fields name, address, and phone number

When the user submits the form:
    an AJAX request is sent to the server with the values from the form
    the modal closes
    if the form submission was successful:
        show an alert at the top of the page that says a new value was added
    else:
        show an alert with the errors 
```

一次做一个特性，而不是整个应用程序，这样如果一个规范改变了，你的工作就不会白费！

## 我向你挑战

选择一个代码挑战，并为其编写伪代码。一旦你完成了，从上一次的[中拿出这个项目，为它的一个特性写伪代码。](https://dev.to/aspittel/moving-past-tutorials-receiving-a-problem-to-solve-2hf8)

## 结论

伪代码对于开始思考问题是一个非常有用的工具。每个人看起来都不一样，对一些人来说，它看起来像编程语言，对另一些人来说，它看起来更像非编程语言。它甚至可以看起来不一样！为了给自己写一个大纲，我有时会在开始钻研代码之前写下我的函数名。找到适合你的方法。

如果你正看着一个空白的文本编辑器屏幕，不知道从哪里开始，伪代码是一个很好的起点，或者甚至后退一步，在纸上画出这个过程，详细观察你的步骤。