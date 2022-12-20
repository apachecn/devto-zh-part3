# 用反应钩谴责反跳

> 原文：<https://dev.to/gabe_ragland/debouncing-with-react-hooks-jci>

今天，我将向您展示如何构建一个 useDebounce React 钩子，它可以非常容易地对 API 调用进行去抖，以确保它们不会过于频繁地执行。我还用我们的钩子做了一个演示。它搜索漫威漫画 API，并使用 useDebounce 来防止 API 调用在每次击键时被触发。

[![demo screenshot](img/ce794b79b385069606ea71fe14795762.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6HL729WB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rytlzua50rzgbhsikvxt.gif)

很漂亮吧？好了，现在来看代码！

首先，让我们弄清楚我们希望如何使用我们的钩子，我们可以让它指导或实际实现钩子逻辑。我们将设计这个钩子去反跳组件的渲染函数中的任何值，而不是去反跳 API 请求的调用。然后，我们将把它与`useEffect`结合起来，每当输入值改变时，就发出一个新的 API 请求。这个代码示例假设您对`useState`和`useEffect`钩子有些熟悉，您可以在 [React 钩子文档](https://reactjs.org/docs/hooks-intro.html)中了解到。

```
import React, { useState, useEffect } from 'react';
import useDebounce from './use-debounce';

// Usage
function App() {
  // State and setter for search term
  const [searchTerm, setSearchTerm] = useState('');
  // State and setter for search results
  const [results, setResults] = useState([]);
  // State for search status (whether there is a pending API request)
  const [isSearching, setIsSearching] = useState(false);

  // Now we call our hook, passing in the current searchTerm value.
  // The hook will only return the latest value (what we passed in) ...
  // ... if it's been more than 500ms since it was last called.
  // Otherwise, it will return the previous value of searchTerm.
  // The goal is to only have the API call fire when user stops typing ...
  // ... so that we aren't hitting our API rapidly.
  const debouncedSearchTerm = useDebounce(searchTerm, 500);

  // Here's where the API call happens
  // We use useEffect since this is an asynchronous action
  useEffect(
    () => {
      // Make sure we have a value (user has entered something in input)
      if (debouncedSearchTerm) {
        // Set isSearching state
        setIsSearching(true);
        // Fire off our API call
        searchCharacters(debouncedSearchTerm).then(results => {
          // Set back to false since request finished
          setIsSearching(false);
          // Set results state
          setResults(results);
        });
      } else {
        setResults([]);
      }
    },
    // This is the useEffect input array
    // Our useEffect function will only execute if this value changes ...
    // ... and thanks to our hook it will only change if the original ...
    // value (searchTerm) hasn't changed for more than 500ms.
    [debouncedSearchTerm]
  );

  // Pretty standard UI with search input and results
  return (
    <div>
      <input
        placeholder="Search Marvel Comics"
        onChange={e => setSearchTerm(e.target.value)}
      />

      {isSearching && <div>Searching ...</div>}

      {results.map(result => (
        <div key={result.id}>
          <h4>{result.title}</h4>
          <img
            src={`${result.thumbnail.path}/portrait_incredible.${
              result.thumbnail.extension
            }`}
          />
        </div>
      ))}
    </div>
  );
}

// API search function
function searchCharacters(search) {
  const apiKey = 'f9dfb1e8d466d36c27850bedd2047687';
  const queryString `apikey=${apiKey}&titleStartsWith=${search}`;
  return fetch(
    `https://gateway.marvel.com/v1/public/comics?${queryString}`,
    {
      method: 'GET'
    }
  )
    .then(r => r.json())
    .then(r => r.data.results)
    .catch(error => {
      console.error(error);
      return [];
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，看起来很不错！现在让我们构建实际的钩子，这样我们的应用程序就可以工作了。

```
import React, { useState, useEffect } from 'react';

// Our hook
export default function useDebounce(value, delay) {
  // State and setters for debounced value
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(
    () => {
      // Set debouncedValue to value (passed in) after the specified delay
      const handler = setTimeout(() => {
        setDebouncedValue(value);
      }, delay);

      // Return a cleanup function that will be called every time ...
      // ... useEffect is re-called. useEffect will only be re-called ...
      // ... if value changes (see the inputs array below). 
      // This is how we prevent debouncedValue from changing if value is ...
      // ... changed within the delay period. Timeout gets cleared and restarted.
      // To put it in context, if the user is typing within our app's ...
      // ... search box, we don't want the debouncedValue to update until ...
      // ... they've stopped typing for more than 500ms.
      return () => {
        clearTimeout(handler);
      };
    },
    // Only re-call effect if value changes
    // You could also add the "delay" var to inputs array if you ...
    // ... need to be able to change that dynamically.
    [value] 
  );

  return debouncedValue;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！我们现在有了一个反跳钩子，可以用来反跳组件主体中的任何值。然后去抖值可以包含在`useEffect`的输入数组中，而不是非去抖值，以限制该效果被调用的频率。

也请查看我的 [React 代码库生成器](https://divjoy.com?ref=devtodebounce)。它会给你一个很好的用户界面，认证，数据库，支付等等。成千上万的 React 开发者使用它来快速构建和启动应用程序。