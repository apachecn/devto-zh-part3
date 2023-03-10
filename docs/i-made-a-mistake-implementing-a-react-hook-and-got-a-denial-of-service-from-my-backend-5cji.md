# 我在实现 React 钩子时犯了一个错误，从我的后端得到了一个拒绝服务

> 原文：<https://dev.to/adamwknox/i-made-a-mistake-implementing-a-react-hook-and-got-a-denial-of-service-from-my-backend-5cji>

React 的 useEffect 钩子在 Firebase 端点上的这个看似简单的用法在几分钟内意外地超出了我的 50k 读取/天限额。

```
function Page() {
  const [meetings, setMeetings] = useState([]);
  useEffect(() => {
    return firebase.firestore().collection('/meetings').onSnapshot(query => {
      setMeeting( query.docs.map(m => m.data()) );
    });
  });

  return (
    <ul>
      {meetings.map(m => <li>{m.title}</li>}
    </ul>
  )
} 
```

这种效果不依赖于任何状态变化，所以我省略了第二个参数。我没有意识到的是，每次调用`setMeeting`时，函数的主体都会被再次执行，导致数据获取出现循环。

所以是的。Firebase 的免费层提供了 50k 读取/天的限额，我在几分钟的开发工作中就超过了这个限额。追查起来也很痛苦。当我意识到 Chrome devtools 中的 Net 选项卡正在向 firebase 发送请求时，我不得不赶紧转到 perf 选项卡，进入“离线模式”。然后，我有时间查看其中一个请求的有效负载，并弄清楚请求的是什么数据。

修复方法是简单地添加一个空的方括号，以指示只应该运行一次，有点像只运行 componentDidMount 和 componentWillUnmount(从 firebase 调用返回的是我们想要卸载的侦听器)。

```
useEffect(() => {
  // return firebase...
}, []) // this guy 
```

在我修好它之后，我停下来思考潜在的问题。

1.  在效果挂钩中很容易忽略第二个参数。

2.  firebase / firestore 没有服务器端速率限制实现。因此，任何恶意用户，或者代码中的一个错误，都可以关闭一个免费层，或者向付费用户收取大量费用。谷歌控制台上的配额管理工具甚至没有一个很好的方法来可视化什么端点被敲，或者什么时候被敲。

3.  当你的使用配额在 Firebase 中被超过，你甚至不能访问你的管理面板。

4.  错误处理不抓这种东西。

黑客快乐，

* * *

参考

*   Andrew Gaines 在 Unsplash 上拍摄的照片
*   Firebase 是一个后端即服务，为实时数据存储解决方案提供了一个慷慨的免费层