# 展开和折叠列表中的项目组。

> 原文：<https://dev.to/vier31/expand--collapse-groups-of-items-in-a-list-275g>

我本周的入场券:显示与版本堆栈同名的文档。

使用的技术:Javascript，React，lodash

一些背景。在应用程序中，用户可以上传文件，并将这些文件附加到消息中。由于我们的后端只关心每个上传文件的唯一 id，用户可以上传多个同名文件。保存文件的创建日期是上传时的时间戳。

如果我们现在要显示这些文件的列表，我们将会得到许多副本，这可能会很快失控。像这样:

```
|- oneFile.pdf      21.03.2019 16:34
|- oneFile.pdf      19.03.2019 14:23
|- anotherFile.pdf  18.03.2019 15:10
|- oneFile.pdf      14.03.2019 10:50 
```

Enter fullscreen mode Exit fullscreen mode

不太酷，特别是因为 95%的时候，用户不需要访问旧版本。

所以，我上周的任务就是改善这一点，只显示每个组的最新文件。每个有多个版本的文件旁边都应该显示一个按钮，单击该按钮将显示旧版本。

首先，让我们看一下我们的输入数据。我们从后端接收对文件的引用，作为数组中的对象，看起来像这样:

```
const files = [{
  id: 1,
  file_name: 'oneFile.pdf',
  created_at: '21.03.2019 16:34'
}, {
  id: 2,
  file_name: 'oneFile.pdf',
  created_at: '19.03.2019 14:23'
}, {
  id: 3,
  file_name: 'anotherFile.pdf',
  created_at: '18.03.2019 15:10'
}, {
  id: 4,
  file_name: 'oneFile.pdf',
  created_at: '14.03.2019 10:50'
}] 
```

Enter fullscreen mode Exit fullscreen mode

这是一个平面数组，包含将要显示的所有对象。

我做的第一件事，是根据文件名关键字对对象进行分组。为此，我使用了 lodash 中的 [groupBy()](https://lodash.com/docs/#groupBy) 函数。

```
const groupedFiles = groupBy(files, 'file_name')
// Expected output:
// { 
//  oneFile.pdf: {
//    { 
//      id: 1,
//      file_name: 'oneFile.pdf',
//      created_at: '21.03.2019 16:34
//    }, {
//      id: 2,
//      file_name: 'oneFile.pdf',
//      created_at: '19.03.2019 14:23'
//    } ...
//  },
//  anotherFile.pdf: { ... }
// } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们真的不需要那些钥匙了。为了显示一个列表，这个数组很好。Lodash 为此提供了一个函数: [values()](https://lodash.com/docs/#values) 。
Values()获取一个对象的值，并将它们放入一个数组中。正是我们需要的。

```
const groupedList = values(groupedFiles)
// Expected output:
// [{
//    { 
//      id: 1,
//      file_name: 'oneFile.pdf',
//      created_at: '21.03.2019 16:34
//    }, {
//      id: 2,
//      file_name: 'oneFile.pdf',
//      created_at: '19.03.2019 14:23'
//    } ...
//  }, { ... }
// } 
```

Enter fullscreen mode Exit fullscreen mode

当我们把这个打印到控制台时。它应该是这样的:

```
Array: [{{...}{...}{...}}, {{...}{...}}, ...] 
```

Enter fullscreen mode Exit fullscreen mode

干净利落。现在我们有了我们的文件组。接下来是渲染出列表。

这是包含我们的列表的应用程序组件。在这里，文件作为道具传递给列表组件。

```
import React from 'react';
import './App.css';
import List from './components/List'

const files = [{
  id: 1,
  file_name: 'oneFile.pdf',
  created_at: '21.03.2019 16:34'
}, {
  id: 2,
  file_name: 'oneFile.pdf',
  created_at: '19.03.2019 14:23'
}, {
  id: 3,
  file_name: 'anotherFile.pdf',
  created_at: '18.03.2019 15:10'
}, {
  id: 4,
  file_name: 'oneFile.pdf',
  created_at: '14.03.2019 10:50'
}]

const App = () => {
  return (
    <div className="App">
      <List files={files}/>
    </div>
  );
}
export default App; 
```

Enter fullscreen mode Exit fullscreen mode

现在来看列表本身。

```
import React, { useState } from 'react';
import { groupBy, values, orderBy, take, includes } from 'lodash';

const List = (props) => {

  // Take in the original array and group the files by filename 
  const groupedFiles = groupBy(props.files, 'file_name');
  const groupedList = values(groupedFiles);

  // Set a hook to manage the state of each list item.
  // Using an array, multiple items can get added. 
  // When an item is added to the list, the complete group will be rendered. 
  const [filter, setFilter] = useState([])

  // Here items are being added or excluded from the array in the hook.
  const toggleFilter = ({ file_name }) => {
    if (includes(filter, file_name)) {
      return setFilter(filter.filter(item => item !== file_name))
    } else {
      return setFilter(filter.concat(file_name))
    }
  }

  // This function takes one individual group and return the JSX elements to render the data
  const renderGroup = (group) => {

    // to make sure, that the most recent file is at the beginning of the group, sort by creation date
    const sortedGroup = orderBy(group, 'created_at', 'desc');

    // Only render the button on a list element that is the first of a group bigger than 1.
    // This could be done inline, but I think that it is cleaner this way.
    const renderButton = (file) => sortedGroup.indexOf(file) === 0 && group.length > 1;

    let files, buttonLabel;

    if (includes(filter, group[0].file_name)) {
      files = sortedGroup;
      buttonLabel = 'show less'
    } else {
      files = take(sortedGroup);
      buttonLabel = 'show more'
    }

    return files.map(file => (
      <li key={file.id}>
        <p>{file.file_name} - {file.created_at}</p>
        {/* We can render an element conditionally, by including it into a statement like the following. 
        The button gets rendered only when the renderButton() function returns true. Nifty. */}
        {renderButton(file) && <button onClick={() => toggleFilter(file)}>{buttonLabel}</button>}
      </li>
    ))
  }

  return (
    <ul>
      {groupedList.map(group => renderGroup(group))}
    </ul>
  );
};

export default List; 
```

Enter fullscreen mode Exit fullscreen mode

让我们走一遍。首先，我们的组件通过 props 接收未分组的列表。然后我们接收它们，按照上面讨论的方式对它们进行分组，最后将它们传递给渲染函数。

在 render 函数中，我们首先设置包含列表的元素。然后，我们将数组和组放在一起，并使用 map()对其进行迭代。在地图中，我们可以单独处理每个组。

现在我们需要决定是显示所有项目还是只显示最近的项目。因为我们要呈现一个按钮，让用户可以在两者之间切换，所以我们需要某种形式的状态管理。使用钩子的好机会。

因此，我们以一个条件语句结束，根据状态，传递组中的所有项目或只传递第一个项目。

最后，我们再次使用 map 来处理该组。这里，我们返回想要传递给列表组件的 JSX 元素。

为了不在所有元素上呈现按钮，我们将按钮元素包装在另一个条件语句中，以便按钮仅在组大于 1 并且元素位于数组的索引 0 处时呈现。

现在你知道了。显然没有做任何样式，但是我希望这个小例子展示了 map()、条件呈现和 React 中的钩子的一些真实用例，以设置一个组件来动态呈现一个项目列表。