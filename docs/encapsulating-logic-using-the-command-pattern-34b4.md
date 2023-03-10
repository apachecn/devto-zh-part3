# 使用命令模式封装逻辑

> 原文：<https://dev.to/danielhutchinson/encapsulating-logic-using-the-command-pattern-34b4>

关注点分离(SoC)，您可能已经听说过这个术语。简而言之，这是一个重要的设计原则，在这个原则中，你将你的代码分成单独的不同的部分。一个具有良好 SoC 的软件通常被称为模块化的。模块化代码具有可重用、易于协作、易于测试、易于控制应用范围等优点。确保代码模块化的一个好方法是实现命令模式。

> 这是以 Typescript 实现的设计模式系列文章的第一篇。本系列假设您对面向对象(OO)原则有基本的了解，并且熟悉类和接口。给出的例子都是用 Typescript 编写的，但是这里概述的概念适用于大多数支持 OO 概念的编程语言。

## 我们要解决的问题是什么？

设计模式是工具，就像其他工具一样，我们应该用它来解决问题……比方说，我们正在为一台电视制作一个遥控器，它有一堆按钮，可以调节音量，或者设置遥控器所连接的任何活动电视的电源状态。

你可能一开始会加入一堆 if else 语句…

```
export default class RemoteControl {
    private tv: Television;

    constructor(activeTelevision: Television) {
        this.tv = activeTelevision;
    }

    public pressButton(button: string): void {
        if(button === 'VolumeUp') {
            this.tv.adjustVolumeUp();
        }
        else if(button === 'VolumeDown') {
            this.tv.adjustVolumeDown();
        }
        else if(button === 'PowerOff') {
            this.tv.powerOff();
        }

        //... and so on
    }
} 
```

太好了！有用！

但是…有点乱。我已经看到这个解决方案不会有很好的扩展性。

我们没有命令的封装，更不用说将来每次我们想要添加一个新的按钮命令时，我们都需要向 RemoteControl 类添加一个新的 else if。不太好…

> 软件实体(类、模块、函数等。)应该对扩展开放，但对修改关闭 <small>-实线中的 O。</small>

如果我们能够扩展遥控器的行为而不必以任何方式修改它的代码，那就太好了。

在这一点上，我们应该后退一步，看看我们的设计。

## 进入命令模式

命令模式是一种行为设计模式，它允许我们将动作的请求者与接收者分离开来。

在我们的例子中，请求者(或客户机)是 RemoteControl 类，接收者是 Television 类。

为此，我们在设计中引入了命令对象:

*   我们为遥控器需要执行的每条逻辑创建一个新命令
*   我们创建一个调用程序，在运行时设置这些命令
*   当按钮被按下时，我们的 RemoteControl 告诉调用者调用响应该动作的命令

在这个场景中，我们已经将`RemoteControl`对象从`Television`对象中分离出来。

让我们在实践中看看这一点。

## 实现

首先，我们需要为命令对象
创建一个接口

```
export default interface Command {
    execute(): void;
} 
```

我们需要在每个命令对象上实现一个简单的接口 Command，上面有一个返回 void 的方法调用。

让我们创建一个具体的命令来调高电视音量。

```
export default class VolumeUpCommand implements Command {
    private tv: Television;

    constructor(tv: Television) {
        this.tv = tv;
    }

    public execute(): void {
        this.tv.adjustVolumeUp();
    }
} 
```

所以我们有一个实现命令接口的类，当构造这个命令时，我们将接受接收器的一个实例，在这个例子中，是电视类。当在我们的新命令上调用 execute 时，它将简单地在 tv 对象上调用`adjustVolumeUp`。

好了，让我们创建一个调用程序来调用这个命令。

```
export default class RemoteControlButton {
    private command: Command;

    public setCommand(command: Command): void {
        this.command = command;
    }

    public onButtonPressed(): void {
        this.command.execute();
    }
} 
```

我们创建了一个带有名为 command 的字段的`RemoteControlButton`类，毫不奇怪，它包含任何符合 Command 接口的类。我们还添加了一个 setCommand 方法，可以用来设置命令！

命令仍然在这里，但是它现在要做的就是调用 execute 来执行类分配的任何命令。有了这一改变，RemoteControlButton 不再需要了解电视机。我们已经成功地将这些结构相互分离。

## 我们做了什么！？

我们将一个请求封装为一个对象。有了这些请求，我们就能够创建接受这些请求作为参数的其他对象。

这意味着我们的具体命令完全可以在任意数量的对象之间重用和共享。

但是对于一个完整的解决方案来说仍然有一点缺失…我们需要创建客户端。

## 分配命令

我们需要一个创建和分配多个具体命令的解决方案。最后，让我们重新访问我们的 RemoteControl 类，并修改它来创建必要命令的具体实现，并将它们分配给它的按钮。为了简洁起见，我将只添加三个按钮，在现实世界中，您的解决方案可能要复杂得多。

首先，我们将把之前创建的`VolumeUpCommand`实现到客户机中。

```
export default class RemoteControl {
    public volumeUpButton: RemoteControlButton;

    private tv: Television;

    constructor(activeTelevision: Television) {
        this.tv = activeTelevision;

        this.volumeUpButton = new RemoteControlButton();
        this.volumeUpButton.setCommand(new VolumeUpCommand(tv));
    }
} 
```

当`volumeUpButton`的`onButtonPressed`方法被调用时(从 GUI 或类似的地方)，它将`execute`这个`VolumeUpCommand`。

现在让我们再创建两个命令，并将它们分配给`RemoteControl`客户端类。

```
export default class VolumeDownCommand implements {
    private tv: Television;

    constructor(tv: Television) {
        this.tv = tv;
    }

    public execute(): void {
        this.tv.adjustVolumeDown();
    }
}

export default class PowerOffCommand implements {
    private tv: Television;

    constructor(tv: Television) {
        this.tv = tv;
    }

    public execute(): void {
        this.tv.powerOff();
    }
}

export default class RemoteControl {
  public volumeUpButton: RemoteControlButton;
  public volumeDownButton: RemoteControlButton;
  public powerOffButton: RemoteControlButton;

  constructor(tv: Television) {
    this.volumeUpButton = new RemoteControlButton();
    this.volumeDownButton = new RemoteControlButton();
    this.powerOffButton = new RemoteControlButton();

    this.volumeUpButton.setCommand(new VolumeUpCommand(tv));
    this.volumeDownButton.setCommand(new VolumeDownCommand(tv));
    this.powerOffButton.setCommand(new PowerOffCommand(tv));
  }
} 
```

太好了！现在，在封装得很好的逻辑块中添加尽可能多的命令变得非常简单，这些逻辑块可以独立于遥控器本身的实现进行维护和测试！

虽然我们可能比第一个使用 if else 语句的解决方案拥有更多的代码，但我们的新设计更具可维护性、可伸缩性，并且包含更多可重用的组件。

## 活生生的例子

我已经嵌入了我们在上面创建的一个完整的工作示例:

[https://codesandbox.io/embed/n97ynm82pl?runonclick=1](https://codesandbox.io/embed/n97ynm82pl?runonclick=1)

> 这是以 Typescript 实现的设计模式系列文章的第一篇。回头见！