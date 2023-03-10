# JSON 解析器 pt2:数组

> 原文：<https://dev.to/tttaaannnggg/json-parser-pt2-arrays-2gbn>

在我们上次停止的地方，我们有一个为原语工作的 JSON 解析器

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
  if(jstring[0] === 'u') return undefined;
  if(jstring[0] === 'n') return null;
  if(jstring.charCodeAt() >= 48 && jstring.charCodeAt() <= 57) return Number(jstring); 
} 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，对吧？问题是，当我们开始考虑复合数据类型时，我们的过程变得更加复杂。数组和对象可以任意深度地嵌套在一起，这样我们就可以得到一个类似如下的 JSON 字符串:

```
{
  "a":12,
  "b":[1,2,3,{"c": false},[[[]]]],
  "d":{"hi":undefined},
  "e":{"f":[{},{},{}]},
  "g":"g"
} 
```

Enter fullscreen mode Exit fullscreen mode

区分字符串的开始和结束，或者如何区分键和值，或者数组和对象的开始和结束有点困难。我们会有一些技巧来处理这个问题。

### 空数组

首先，我们可以通过`[`来识别一个数组。我们在这里的功能最终会变得稍微复杂一些，所以我们将编写一个单独的方法，并在我们的 JSON 字符串上调用它来解决这个问题。

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
  if(jstring[0] === 'u') return undefined;
  if(jstring[0] === 'n') return null;
  if(jstring.charCodeAt() >= 48 && jstring.charCodeAt() <= 57) return Number(jstring); 
  if(jstring[0] === '[') return parseArray(jstring)
}

function parseArray(jstring){
  const output = [];
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 单项数组

我们现在将关注 parseArray。数组的一个关键特性是它们可以在其中保存项目。让我们来看一个情况，我们的数组中有一个元素。

```
JSON.stringify([1]) // returns '[1]' 
```

Enter fullscreen mode Exit fullscreen mode

所以，第一个和最后一个字符是左方括号和右方括号，中间的值只是一个原语。我们已经写了一个可以处理原语的函数，为什么不直接调用它呢？

```
//...cont from JSONParser above
function parseArray(jstring){
  const output = [];
  if(jstring.length > 2){
    const valueStr = jstring.slice(1, jstring.length-1)
    const value = JSONParser(valueStr)
    output.push(value)
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将处理一个只有一个元素的数组。我们取我们的字符串，去掉第一个和最后一个字符(分别是`[`和`]`，然后通过 JSON 解析器发回结果值，这样我们就可以将它识别为任何数据类型，并将它的值推送到数组中。

这样做的一个令人兴奋的副作用是，现在我们可以期望在我们的`JSONParser`中返回 0 项和单项数组，它实际上可以用于嵌套数组，如`[[[]]]`或`[[[[1]]]]`！恭喜——你刚刚了解了为什么递归很酷。

不错，但还不够好。我们需要处理多项数组。让我们继续构建和思考我们的方法。

### 多项数组

既然我们知道了如何处理单个项目的数组，我们只需要知道如何识别和界定数组中的每个项目，这样我们就可以对每个项目应用我们的`JSONParser`。

问题是，什么分隔数组中的元素？答案是逗号。我们可以找到位于逗号之间的字符，将它们切掉，并将它们反馈给`JSONParser`函数以返回数据，然后将它们推送到我们的`output`数组中。

```
//...cont from JSONParser above
function parseArray(jstring){
  const output = [];
  if(jstring.length > 2){
    const valueStr = jstring.slice(1, jstring.length-1)
    let start = 0;
    for(let i = 0; i <= valuesStr.length; i++){
      if(valueStr[i] === ',' || i === valuesStr.length){
        const curVal = JSONParser(valuesStr.slice(start, i));
        output.push(curVal);
        start = i+1;
      }
    }  
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的策略有一些缺陷，但这里是到目前为止它是如何工作的。为了解析一个字符串化的值，我们需要知道该值从哪里开始，在哪里结束。在这种情况下，我们使用一个变量`start`来跟踪一个值的开始，当我们到达一个`,`或者到达字符串的末尾时，我们认为我们到达了它的末尾。它适用于 JSON 字符串，如下所示:

`'["abc","def","ghi"]'`

`'[123,[],null]'`

你能猜出他们有什么共同点吗？你能猜出我从这些例子中排除了什么吗？事实是，我们只有部分答案。

现在，考虑这样的字符串:“玛丽挥手，然后说你好”。我们这里有个逗号！`'["Mary waved, then said hello", "John waved, then said goodbye"]'`将解析出`["Mary wave", undefined, "John wave", undefined]`，这是*而不是我们想要的*。对于嵌套数组或嵌套对象，我们也会遇到类似的问题。因此，字符串化的值在哪里结束的问题是一个非常重要的问题，而且比我们预期的要复杂得多。在某些数据类型中，我们需要将字符简单地视为字符，而不是特殊的标记。

### 有时候一个字符只是一个字符

让我们来谈谈在什么情况下我们可能会遇到逗号，而这些逗号并没有在数组中分隔一个单独的项。

*   字符串内部
*   嵌套数组内部
*   嵌套对象内部

所有这些情况都有一些共同点:它们都有特殊的特征。字符串以`"`开头，数组以`[`开头，对象以`{`开头。所以，让我们创建一些东西来判断我们是否正在进入一个。我们可以用一个对象来跟踪什么是什么。

```
const openings = {
  '"': true,
  '[': true,
  '{': true
} 
```

Enter fullscreen mode Exit fullscreen mode

这个语法可能有点不清楚，所以我会告诉你我为什么这样做。如果我想检查一个字符是否是一个“开始”字符——也就是说，它开始了某件事情，我应该稍微不同地对待它的内容，我可以简单地使用条件，`if (openings[char])`来这样做。如果我用任何其他键访问`openings`，它将被评估为`undefined`，这将是假的，从而避免触发我的条件。

所以，如果`(openings[char])`，我们知道一件事情已经开始了。但是我们怎么知道它在哪里结束？在设计解决方案时，我从 Javascript 的调用堆栈中获得了灵感。换句话说，我想我应该建立一个堆栈。

### 叠加起来

当我们点击开始字符时，我们将它们推到堆栈中，当我们点击结束字符时，我们将从堆栈中弹出项目。一旦我们完全清空了堆栈，我们将知道我们已经到达了一个条目的最后一个字符，并且可以将该字符串解析为一个值。这有点复杂，但是我会带你完成这个过程。

我们将使用一个数组作为堆栈。

`const stack = []`

例如，让我们再看看我们的问题数组。

`'["Mary waved, then said hello", "John waved, then said goodbye"]'`

我们做的第一件事是去掉了`[`和`]`。那就只剩下`'"Mary waved, then said hello","John waved, then said goodbye"'`了。

我们在 JSON 字符串中看到的第一个字符是`"`，所以我们将把它压入堆栈。

`['"'] //value of stack`

现在我们的堆栈中有一个双引号，我们知道我们必须忽略逗号，直到我们遇到一个匹配的双引号，因为它们都是同一个字符串的一部分。最终，我们到达了`Hello"`的结尾，并看到了匹配的双引号。此时，我们可以从堆栈中`pop`我们的值，和 JSON parse(" Mary wave，then said hello ")，它是一个子字符串，从我们的引号开始，到我们的引号结束。

引号很容易，因为它们使用相同的字符开始和结束，所以我们只需检查`(stack[stack.length-1] === '"')`。不过，对于方括号和花括号，我们必须检查匹配对。现在，我们可以将`openings`修改为与左/右大括号对应的键/值对，并将对应的大括号/引号推送到我们的堆栈中，以便于比较。

```
//...cont from JSONParser above

const openings = {
  '"': '"',
  '[': ']',
  '{': '}'
};

const stack = [];

function parseArray(jstring){
  const output = [];
  if(jstring.length < 3) return output; //small refactor to reduce nesting conditionals
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  for(let i = 0; i <= valueStr.length; i++){
    if(stack[stack.length-1] === valueStr[i]){
      stack.pop(); //pop needs to come first to ensure that we're not pushing our '"' to the stack if we've already got a '"' sitting there.
    } else if(openings[valueStr[i]]){
      stack.push(openings[valueStr[i]]);
    }
    if (!stack.length && valueStr[i] === ',' || i === valueStr.length) {
      const curVal = JSONParser(valueStr.slice(start, i));
      start = i+1;
    }
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的`parseArray`取决于几件事:

*   我们是在打开一个字符串/对象/数组吗？
*   我们是在一个字符串/对象/数组的中间吗？
*   我们是否关闭了我们的字符串/对象/数组并打了一个逗号？

如果我们满足所有这些条件，我们将解析我们的值，然后将它推送到我们的数组，最后返回我们的数组。我们还没有在`JSONParser`中编写任何功能来处理对象，所以这些将返回`undefined`。

不过，还有最后一点功能需要添加。字符串中可能存在转义字符。例如，' \ " '是一个有效的字符串，不应该导致我们将' \ " '压入堆栈，或者如果它已经在那里，就弹出它。如果我们不考虑这样的数组，我们可能会得到一些不愉快的带有不平衡括号或引号的行为:`["\"", "\]"]`

这里有两个逻辑组成了我们的功能。因为我们使用`stack`作为我们的看门人来决定我们是否应该把字符看作它们自己的值，或者一个更大的值的一部分，我们将简单地利用堆栈来跳过一个字符。

第一个逻辑是，如果这是我们当前的字符，我们将把“\”压入堆栈。第二个逻辑是，如果它是最后一个被推入堆栈的内容，我们将弹出它，并跳到下一个字符。我们实际上需要以相反的顺序来做这件事，因为我们可以对反斜杠进行转义。如果我们有一串`"\\a"`，我们想跳过第二个`\`，而不是跳过`a`。

总而言之，我们的函数现在看起来是这样的:

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
  if(jstring[0] === 'u') return undefined;
  if(jstring[0] === 'n') return null;
  if(jstring.charCodeAt() >= 48 && jstring.charCodeAt() <= 57) return Number(jstring);
  if(jstring[0] === '[') return parseArray(jstring);
}

const openings = {
  '"': '"',
  '[': ']',
  '{': '}'
};

const stack = [];

function parseArray(jstring){
  const output = [];
  if(jstring.length < 3) return output;
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  for(let i = 0; i <= valueStr.length; i++){
// PLEASE NOTE: all instances of '\\ ' should actually be '\\'
// Dev.to's syntax highlighting doesn't appropriately account for the fact that the second backslash is escaped by the first.
    if(stack[stack.length-1] === '\\  '){ 
      stack.pop();
      continue;
    } else if(valueStr[i] === '\\  '){
      stack.push('\\  ');
    }
    if(stack[stack.length-1] === valueStr[i]){
      stack.pop();
    } else if(openings[valueStr[i]]){
      stack.push(openings[valueStr[i]]);
    }
    if (!stack.length && valueStr[i] === ',' || i === valueStr.length) {
      const curVal = JSONParser(valueStr.slice(start, i));
      output.push(curVal);
      start = i+1;
    }
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我们递归地调用 JSONParser 和 parseArray，这允许我们的函数处理任意深度的嵌套(至少在遇到堆栈溢出之前)。我们的最后一个任务是添加一个处理对象的方法，如果我们有效地设计它，它将会覆盖任何留下的空白。

## 补遗

数组解析器实际上有问题。我尝试了一个`[["a","b"],["["],[]]`的测试用例，反而得到了`[["a","b"],['["],']]`。

当时的情况是，我们没有正确地避免括号嵌套在字符串中的情况。

我解决这个问题的方式有点难看，但是很有效。本质上，在从堆栈中推出或弹出任何东西之前，我们应该检查堆栈中的最后一个东西是否是`"`，这样我们就可以确保不从堆栈中推出或弹出任何东西，除非我们已经找到了匹配的`"`并知道我们已经不在字符串中了。

```
function parseArray(jstring){
  const output = [];
  if(jstring.length < 3) return output;
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  for(let i = 0; i <= valueStr.length; i++){
// PLEASE NOTE: all instances of '\\ ' should actually be '\\'
// Dev.to's syntax highlighting doesn't appropriately account for the fact that the second backslash is escaped by the first.
    if(stack[stack.length-1] === '\\  '){ 
      stack.pop();
      continue;
    } else if(valueStr[i] === '\\  '){
      stack.push('\\  ');
    }
    if(stack[stack.length-1] === valueStr[i] && stack[stack.length-1] !== '"' || 
      stack[stack.length-1] === valueStr[i] && valueStr[i] === '"'){
      stack.pop();
    } else if(openings[valueStr[i]] && stack[stack.length-1] !== '"'){
      stack.push(openings[valueStr[i]]);
    }
    if (!stack.length && valueStr[i] === ',' || i === valueStr.length) {
      const curVal = JSONParser(valueStr.slice(start, i));
      output.push(curVal);
      start = i+1;
    }
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将处理[对象](https://dev.to/tttaaannnggg/json-parser-pt3-objects-tying-it-together-5cbh)