# 一个简单的日志应用程序(使用 React、localStorage 和 fun)

> 原文：<https://dev.to/chaituknag/a-simple-journal-app-using-react-localstorage-and-fun-23j8>

# 前提

我使用 React、Bootstrap、Moment.js 和 localStorage 作为数据库，构建了一个类似于初级 Journal 应用程序的 CodePen 应用程序。

# 这个帖子是关于什么的

我只想分享几个你可能感兴趣的重要概念。

## JSON.stringify 和 JSON.parse

每当我们在 localStorage 中存储项目时，我们都必须将值字符串化，并在读取它的同时解析它。

```
const getEntriesFromStorage = () => JSON.parse(
    window.localStorage.getItem('journalEntries')
);
const setEntriesToStorage = items => 
    window.localStorage.setItem('journalEntries', JSON.stringify(items)); 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Array.prototype.slice 删除条目

当我们必须删除一个条目时，我们将使用以下两个特性在旧数组的基础上创建一个新数组:

1.  数组.原型.切片
2.  `...`传播算子

索引是数组中要删除的项的索引。

```
const newEntries = [...entries.slice(0, index), ...entries.slice(index+1)]; 
```

Enter fullscreen mode Exit fullscreen mode

## 高阶删除条目点击处理程序

为了使 delete 按钮对条目列表中的每个条目起作用，我们必须让事件处理程序知道它必须在 DB 中删除的条目，因此我们创建了一个高阶函数(该函数返回另一个充当实际事件处理程序的函数)。

```
 const handleDeleteClick = (index) => e => {
    deleteEntry(index);
  } 
```

Enter fullscreen mode Exit fullscreen mode

## Textarea 在页面上加载焦点

在 add Entry 表单中，我们希望将焦点放在 message textarea 上，这样我们就可以在页面加载时轻松地输入消息，我们可以做一个 useEffect，每次页面加载只运行一次，因为第二个参数是一个空数组。

```
 useEffect(() => {
    fieldRef.current.focus();
  }, []); 
```

Enter fullscreen mode Exit fullscreen mode

## 等一下。js。fromNow usage(立即使用)

当我们显示记录条目的日期和时间时，我们可以使用 Moment.js 的一个特殊特性。fromNow()，它将从记录条目到现在的持续时间转换为人类可读的消息，如“几分钟前”和“2 分钟前”。

```
const itemDate = moment(item.date).fromNow(); 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义挂钩(useJournal)

任何返回包含至少一个内置 React 挂钩的数组或对象的函数都称为自定义挂钩。在我们的例子中，我们在这个自定义钩子中抽象了对 localStorage 的读写功能，以便在使用它的组件中保持该功能的可重用性和整洁性。

```
function useJournal() {
  const [entries, setEntries] = useState([]);

  const getEntriesFromStorage = () => JSON.parse(
    window.localStorage.getItem('journalEntries')
  );
  const setEntriesToStorage = items => 
  window.localStorage.setItem('journalEntries', JSON.stringify(items));
  useEffect(() => {
    const entriesFromStorage = getEntriesFromStorage();
    if(entriesFromStorage) {
      setEntries(entriesFromStorage);
    }
  }, []);

  const storeEntry = (entry) => {
    const newEntries = [entry, ...entries];
    setEntries(newEntries);
    setEntriesToStorage(newEntries);
  }

  const removeEntry = (index) => {
    const newEntries = [...entries.slice(0, index), ...entries.slice(index+1)];
    setEntries(newEntries);
    setEntriesToStorage(newEntries);
  }

  return [entries, storeEntry, removeEntry];
} 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

看一下 CodePen(下面)以了解那里做了什么，并建议任何可能极大地改进示例的简单更改。

[https://codepen.io/skola17/embed/NVrBKj?height=600&default-tab=result&embed-version=2](https://codepen.io/skola17/embed/NVrBKj?height=600&default-tab=result&embed-version=2)