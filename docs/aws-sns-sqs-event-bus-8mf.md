# AWS 社交网络和 SQS 事件总线

> 原文：<https://dev.to/dropconfig/aws-sns-sqs-event-bus-8mf>

即使你有一个整体架构，就像我们在[https://dropconfig.com](https://dropconfig.com)做的那样，你也希望异步地做事情，并且能够对事件做出响应。

例如，如果创建了一个用户，我们可能希望:

*   更新帐单信息
*   向用户发送电子邮件
*   向邀请他们的人发送电子邮件

在一个函数中做这些事情很好，但是我们可以用事件总线做得更好。

### SNS

AWS SNS 是一项让你发布订阅消息的服务。你可以把消息传递给 HTTP 端点或者 AWS lambdas 甚至 SQS。它很容易将信息发送到一个地方，并通过长时间传递给任何想听的人。

### SQS

AWS SQS 是一个非常简单的队列。SNS 可以通过点击 AWS 控制台中的按钮向其写入消息。然后我们可以以任何我们想要的方式从队列中读取。

### 事件

首先，我们希望开始向 SNS 发送事件，这样它就可以将消息转发给我们所有的队列。

我将在所有代码示例中使用 nodejs

```
// We are assuming we already created a topic in the AWS console.
const sns = new aws.SNS({region: "us-east-1"});

const params = {
    Message: JSON.stringify({
        type: "some-event-type",
        data: {
            "some": "data"
        }
    }),
    TopicArn: SnsTopic //Create a topic in the SNS console and get the ARN for this.
}
sns.publish(params).promise() //We can asyn await or just do promise stuff here. 
```

如你所见，发送信息非常容易。
我们可能想要设置一些`MessageAttributes`，但是我们将在稍后讨论。

### 事件处理程序

我们正在进行设置，以便 SNS 将这些内容发布到 SQS。我们还希望每个要运行的任务类型都有一个队列。

例如，计费任务将具有与电子邮件任务分开的队列。

您可以设置 AWS lambda 从您的队列中读取数据，AWS 将根据需要扩展它们。

但是如前所述，我们希望将大部分代码保存在节点应用程序中，那么我们如何读取 SQS 并对事件做出反应呢？

我们首先需要一些东西来管理队列和运行工人

```
//We have a max number of workers that can run at once
//We don't want to melt our server.
const MAX_WORKERS = 10;
let currentWorkers = 0;
async function createTaskRunner(sqs, queue, server) {
  let running = false;

  // we have a copy of this running async for each task-type
  async function start() {
    running = true;
    while (running) {
      // This is soft target, because we may reenter here
      // while async waiting on the tasks. In other words:
      // - workers == 10
      // - we are good, we poll the task
      // - polling takes longer than a second
      // - and workers == MAX_WORKERS
      // - we poll again
      //
      // So this will overshoot a little.
      if (current_workers <= MAX_WORKERS) {
        const task = await checkQueue(sqs, queue);
        if (task) {

          // this is run async here to allow
          // running multiple workers of the same type
          // in parallel
          runTask(task, queue, sqs, server);
        }
      }
      await wait(1000);
    }
  }

  return {
    start,
    stop: () => {
      running = false
    }
  }
} 
```

这个函数将管理队列，并调用 workers 来处理队列中出现的事件。接下来，我们要定义`checkQueue`来检查是否有任务要运行。

```
async function checkQueue(sqs, queue) {
  const params = {
    QueueUrl: queue.url,
    MaxNumberOfMessages: 1,
    //WaitTimeSeconds is important. 
    //The `await` will wait until it gets something from the queue or 20 seconds has passed before returning. 
    //This way we don't keep running over and over and over as fast as possible.
    WaitTimeSeconds: 20,
  }
  const res = await sqs.receiveMessage(params).promise();
  if (res.Messages && res.Messages.length) {
    const message = res.Messages[0];
    let messageBody;
    try {
      const data = JSON.parse(message.Body);
      messageBody = JSON.parse(data.Message);
    }
    catch (e) {
      messageBody = message.Body
    }

    const task = {
      id: message.MessageId,
      receipt: message.ReceiptHandle,
      queue: queue.url,
      data: messageBody,
      message: message
    }
    return task;
  } else {
    return null;
  }

} 
```

现在让我们看看`runTask`我们把它放在一个独立于`createTaskRunner`的函数中，这样我们就可以一次有多个工人在一个队列中。

```
async function runTask(task, queue, sqs, server) {
  workers = workers + 1
  const taskSummary = {
    type: queue.type,
    id: task.id
  }

  try {
    const complete = await queue.handler(task, queue, sqs, server)
    if (complete) {

      await sqs.deleteMessage({
        QueueUrl: queue.url,
        ReceiptHandle: task.receipt
      }).promise();
    }
    else {
      //We even create events in our event handlers.
      server.createEvent(TASK_WORKER_FAILED, {
          taskSummary,
          complete
      });
    }
  } catch (e) {
    server.createEvent(TASK_WORKER_FAILED, {
        taskSummary,
        e
    });
  }
  workers = workers - 1

} 
```

任务工作器被定义为一个无状态函数，它从`runTask`函数获取数据，完成它的任务，并返回该事件是否成功。

让我们看一个发送电子邮件的例子。

```
exports.handler = (task, queue, sqs, server) => {
   let to;
   let message;

   // We can handle multiple types of events here.
   if(task.data.type === "USER_CREATED"){
        to = task.data.data.user.email;
        message = "WELCOME TO THE SITE!"
   }
   if(task.data.type === "USER_COMMENT"){
        to = task.data.data.post.creator.email;
        message = "SOMEONE COMMENTED ON YOUR POST";
   }

   if(to && message){
        //This is obviously simplified. 
        //Sending emails is not part of this article.
        sendEmail(to, message);

   }

   //If we get a event type we don't care about we just ignore it.
   return true;

} 
```

所以现在我们只剩下最后一块要拼了。正在初始化任务队列。

```
const taslQueues = [
    {name: "email", "url": "https://url-to-email-queue.aws.amazon.com/", handler: require("./emailHandler")}
    {name: "billing", "url": "https://url-to-billing-queue.aws.amazon.com", handler: require("./billingHandler")}
]
async function init(sqs, server, tasks) {
  const runners = [];
  taskQueues.forEach(async (queue) => {
    const runner = await createTaskRunner(sqs, queue, server);
    runners.push(runner);
    runner.start();
  })
} 
```

现在我们有两个任务运行程序在各自的队列中监听消息。

### 将消息限制到队列

假设你有很多邮件在传来传去，但是你的邮件队列只关心其中的几类，而忽略了其他的。
幸运的是，SNS 支持我们过滤进入我们队列的消息[https://docs . AWS . Amazon . com/SNS/latest/DG/SNS-message-filtering . html](https://docs.aws.amazon.com/sns/latest/dg/sns-message-filtering.html)

我们只需要使用我之前提到的`MessageAttributes`来完成这个任务。

```
const sns = new aws.SNS({region: "us-east-1"});

const params = {
    Message: JSON.stringify({
        type: "some-event-type",
        data: {
            "some": "data"
        }
    }),

    // We add in a message attribute to filter on.
    MessageAttributes: {
        "event-type": {
            DataType: "String",
            StringValue: "some-event-type"
         }
    },
    TopicArn: SnsTopic //Create a topic in the SNS console and get the ARN for this.
}
sns.publish(params).promise() //We can asyn await or just do promise stuff here. 
```

和订阅的订阅过滤器策略。
它将只接收所列事件类型的事件。

```
{
  "event-type": [
    "some-event-type"
  ]
} 
```

这是关于如何传递信息的更高层次的概述。

## 其他好处

### 数据湖

现在我们有了事件总线，可以将收到的每个事件存储到某个文档存储中(实际上我们只是登录到 cloudwatch)。允许您查询发生的任何事件，甚至重放它们！

### 活动提要

假设我们有一些显示帖子何时被评论的活动提要。

我们可以对数据库进行查询，但这可能会变得复杂。或者，如果我们将一些事件存储在像 mongodb 这样的文档存储中，我们可以只请求类型为`USER_COMMENT`的事件，并按顺序返回它们！

### 传入 webhooks

在 DropConfig 中，我们使用条带。我们不必在 webhook http 处理程序中处理 webhooks stripe 发送给我们的输入，而是可以从它们创建事件。这将它们放入总线，也许我们的计费处理器可以从那里取走它们。

真的很简单

```
server.route({
    path: "/api/webhooks/incoming/stripe",
    method: "POST",
    options: {
        handler: (req) => {
            server.createEvent("STRIPE_INCOMING_WEBHOOK", req.payload);
            return true
        }
    }
}) 
```

现在，任何关心的人都可以监听`STRIPE_INCOMING_WEBHOOK`事件并做出相应的反应。

如果你能做到这一步，感谢你的阅读！请看看 https://dropconfig.com，我想你会喜欢的。

如有任何问题，欢迎随时评论！