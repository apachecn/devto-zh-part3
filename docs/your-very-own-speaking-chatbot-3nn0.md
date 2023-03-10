# 你自己会说话的聊天机器人

> 原文：<https://dev.to/victorqribeiro/your-very-own-speaking-chatbot-3nn0>

# [mychatbot](#mychatbot)

一个简单的 JavaScript 聊天机器人，使用了[正则表达式](https://en.wikipedia.org/wiki/Regular_expression)和[网络语音 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API)

在这里有一个现场版本

## 基本规则

现在只有几条规定。机器人可以回答“你叫什么名字”、“你多大了”、“今天是星期几”、“明天是星期几”。但是你当然可以增加它。

## 如何编写规则

我建议看一看正则表达式，看看它是如何工作的。但是，让我们假设我们有兴趣知道用户是否在询问我们的年龄(我们是聊天机器人)。所以，像“你多大了？”你用下面的正则表达式翻译:

```
/.*how.*old.*you.*/ 
```

机器人会将规则理解为:任何事情如何如何 T2 任何事情旧 T4 任何事情你任何事情。

很简单，嗯？！

因此，机器人实际上只是一个规则的集合，这些规则被定义为对象:

```
{rule: /.*how.*old.*you.*/, response: "I don't know how old I am"} 
```

你可以想象一下，但是将响应设置为数字 1 并编写一个函数来处理这个响应:

```
{rule: /.*what.*do.*you.*think.*about(.*)/, response: 1, action: dealWithQuestion}

function dealWithQuestion(question){
  return "I don't like "+ question + " very much!";
} 
```

在这种情况下，亲子关系意味着你想捕捉*之后发生的任何事情，你认为*怎么样。所以，如果问题是“你觉得椰子怎么样？”答案会是“我不太喜欢椰子！”。

这里的代码是[这里的](https://github.com/victorqribeiro/myChatBot)