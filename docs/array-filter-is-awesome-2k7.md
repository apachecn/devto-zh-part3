# Array.filter()太棒了

> 原文：<https://dev.to/bbarbour/array-filter-is-awesome-2k7>

这个周末，我在做我的 React 项目，在我的一个组件中，我需要从一个列表中删除。列表中的数据以数组的形式存储。

我在学习中学会了多种操作数组的方法。你有。pop()，。unshift()，。slice()，。拼接()和...。过滤器()。在选择最佳方法时，我的第一个决定是考虑 React。状态是不可变的。这意味着我不能直接影响它。好的，没问题——这意味着我可以放弃一些选择。

它留给我。slice()和。过滤器()。

我的本能是伸手去拿。切片()。但是，我决定真的去看看。filter()，因为我在 React 示例中见过其他人使用它。

在阅读了 MDN 上的文档后，我突然明白了为什么对于从数组中删除这样的事情来说，它是一个如此好的方法。

我已经在用了。map()迭代数组以呈现列表。这给了我一个参数。我想我可以把它作为一个道具和状态一起传递给我的组件。在组件内部，我只需要创建一个触发 onClick 的 handleDelete 函数。

在 deleteItem 内部。filter()接受一个回调函数，该函数根据条件返回一个布尔值。一旦整个数组求值完毕，它就抛出一个新数组，其中包含那些被确定为真的索引。

对于资深的 javascript 开发人员来说，这可能是一件很容易的事情，但是我很高兴我意识到了它的用处。filter()可以是。

下面是我的组件的一个例子:

```
const ItemList = ({ inventory }) => {
  const [list, setList] = inventory;
  const deleteItem = index => {
    const newInventory = list.filter(
      (item, itemIndex) => index !== itemIndex
    );
    return setList([...newInventory]);
  };
  return (
    <tbody>
      {list.length > 0 ? (
        list.map((item, index) => {
          return (
            <tr key={index}>
              <td>
                {item}{"  "}
                <div className="is-pulled-right">
                  <button
                    className="delete is-small"
                    onClick={e => deleteItem(index)}
                  />
                </div>
              </td>
            </tr>
          );
        })
      ) : (
        <tr>
          <td>
            <em>Empty.</em>
          </td>
        </tr>
      )}
    </tbody>
  );
};

export default ItemList; 
```

Enter fullscreen mode Exit fullscreen mode