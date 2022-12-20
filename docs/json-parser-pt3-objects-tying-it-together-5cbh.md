# JSON 解析器 Pt3:对象/把它绑在一起

> 原文：<https://dev.to/tttaaannnggg/json-parser-pt3-objects-tying-it-together-5cbh>

这是我们从第 2 章停止的地方。我们已经处理了数组，所以剩下要处理的唯一数据类型是对象！

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

简单回顾一下，我们基于 JSON 字符串中的第一个字符来处理原语。如果我们遇到一个数组，我们需要稍微复杂一点的逻辑，我们将它封装在自己的功能中。

我们的 parseArray 的目标是正确地处理一个数组，这个数组中可能有一些东西，我们可以通过对 JSONParser 的递归调用来处理每一个东西。

递归调用我们的 JSONParser 的好处是确保我们可以处理我们已经知道如何处理的任何类型的数据类型，包括数组本身，从而允许我们解析任意深度嵌套的数组(假设我们没有遇到溢出)。

那么，关于物体。我们希望对对象使用与数组类似的策略，但是它们会稍微复杂一点，因为它们操作的是键/值对。因此，我们需要根据逗号和冒号将内容分块。如果我们有一个这样的对象:`{"hello":"world","I'm":"here"}`并用逗号将它分开，我们将得到两个条目:`"hello":"world"`和`"I'm":"here"`。我们将不得不寻找冒号来进一步分隔这两部分，这样我们将得到键/值对`"hello"`和`"world"`、`"I'm"`和`"here"`。

由于我们的代码将使用一些与数组解析器相同的思想，我们将从复制并重命名它开始。

```
function parseObj(jstring){
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

所以，我们可以改变的第一件事就是函数输出的数据结构。我们希望构建一个最终返回的对象，而不是一个数组。

```
function parseObj(jstring){
  const output = {};
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`parseArr`函数中的大部分剩余内容可以保留下来，因为我们仍然要检查它是否为空，切断`{`和`}`，循环我们的 JSON 字符串，并维护我们的`stack`。

```
function parseObj(jstring){
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
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，在这一点上，我们能够迭代我们的对象的内容，维护一个堆栈，它将向我们提供嵌套深度的指示/我们是否应该将括号视为指示一个新数据段的开始，或者它是否只是字符串的一部分。(`["[]"]`不同于`[[]]`或`["[","]"]`)。

现在，最后的任务是从我们的字符串中获取键/值对，并将它们存储在我们的对象中。我们将初始化两个变量`key`和`val`来存储它们。

```
function parseObj(jstring){
  const output = [];
  if(jstring.length < 3) return output;
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  let key;
  let val;
//... and so on 
```

Enter fullscreen mode Exit fullscreen mode

我们将如何填充`key`和`val`？我们知道键和值都是 javascript 值，所以我们可以通过递归调用`JSONParser`来解析它们的值，只要我们知道 JSON 字符串的哪一部分对应于`key`，哪一部分对应于`value`。

让我们看一个示例对象:

`{"key1":"val1","key2":"val2"}`

很明显，我们碰到的第一个东西是一个键，并且这个键在第一个`:`结束。在第一个`:`之后，我们有第一个值，它在`,`结束。之后，我们又有了一个键，然后是一个以对象结尾结束的值。

因此，一个键将在对象的前面开始，或者直接在一个`,`之后开始，一个值将在一个`:`之后开始，并且在一个`,`或者对象的结尾结束。

知道了这一点，我们就可以最终填充我们的键和值，然后在我们的对象中分配它们。

```
function parseObj(jstring){
  const output = [];
  if(jstring.length < 3) return output;
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  let key;
  let val;
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
    if (valueStr[i] === ':'){
      key = JSONParser(valueStr.slice(start, i))
      start = i+1;
    }
    if (valueStr[i] === ',' || i === valueStr.length){
      val = JSONParser(valueStr.slice(start, i));
      start = i+1;
      output[key] = val;
    }
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 还有一件事！

最后，最后一部分是，如果我们的堆栈是清晰的，我们应该只向对象添加键/值对。否则，我们会遇到这样的问题:`{"hi":"{"}`或`{"one":{"two":"{"},"three":{"three":"}"}}`

所以，最后，我们将在存储我们的`key`或`val`之前检查我们的栈是否清空。

```
function parseObj(jstring){
  const output = [];
  if(jstring.length < 3) return output;
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  let key;
  let val;
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
    if (!stack.length){
      if (valueStr[i] === ':'){
        key = JSONParser(valueStr.slice(start, i))
        start = i+1;
      }
      if (valueStr[i] === ',' || i === valueStr.length){
        val = JSONParser(valueStr.slice(start, i));
        start = i+1;
        output[key] = val;
      }
    }
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当我们看到一个`{`时，我们只需要将这个函数挂接到我们的`JSONParser`函数中，我们就都准备好了！

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
  if(jstring[0] === 'u') return undefined;
  if(jstring[0] === 'n') return null;
  if(jstring.charCodeAt() >= 48 && jstring.charCodeAt() <= 57) return Number(jstring);
  if(jstring[0] === '[') return parseArray(jstring);
  if(jstring[0] === '{') return parseArray(jstring);
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

function parseObj(jstring){
  const output = [];
  if(jstring.length < 3) return output;
  const valueStr = jstring.slice(1, jstring.length-1)
  let start = 0;
  let key;
  let val;
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
    if (!stack.length){
      if (valueStr[i] === ':'){
        key = JSONParser(valueStr.slice(start, i))
        start = i+1;
      }
      if (valueStr[i] === ',' || i === valueStr.length){
        val = JSONParser(valueStr.slice(start, i));
        start = i+1;
        output[key] = val;
      }
    }
  }
  return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 注释和结论

*   我们假设我们得到了一个格式正确的 JSON 字符串
*   我们还假设没有多余的空白，因为默认情况下`JSON.parse`不包含多余的空白或换行符

除此之外，只要进来的 JSON 是由`JSON.parse`形成的，这个应该就能处理一切了！