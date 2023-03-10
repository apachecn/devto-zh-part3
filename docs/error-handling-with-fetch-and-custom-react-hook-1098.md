# 用 Fetch(和自定义 React 挂钩)处理错误

> 原文：<https://dev.to/p4lm/error-handling-with-fetch-and-custom-react-hook-1098>

javascript 的`fetch`功能让我印象深刻的一点是，它看起来往往如此简单。

```
fetch('/something.json')
  .then(res => res.json())
  .then(json => {
    // do something useful here with json...
  }); 
```

当我在一个拉取请求中看到同样的事情时，我并没有被深深打动。不，仅仅因为这在晴天有效并不意味着你就完了。您还需要错误处理！明确如何处理错误比让用户盯着一个无限旋转的东西要好得多。

由于对于非 2xx 响应，`fetch`不会将您扔进`catch`子句，您需要检查`ok`属性或检查`status`以获得特定的状态代码。但是`then`和`catch`可以使用相同的函数进行错误处理。

```
let isLoading = true;
let hasError = false;
let data = {};

function handleFetchResponse(response) {
  hasError = !response.ok;
  isLoading = false;
  return response.ok && response.json ? response.json() : data;
}

function fetchData() {
  return fetch(url)
    .then(handleFetchResponse)
    .catch(handleFetchResponse);
}

fetchData().then(data => {
  // do something useful here with data...
}); 
```

当然，这完全取决于您的应用程序，但对我来说，这是最小的错误处理。为了让团队在整个应用程序中使用它，我发现有必要将其封装成一个可重用的函数。我目前在 React 代码库中工作，所以这是我写的自定义钩子。

```
import { useEffect, useState } from "react";

/*  Example
    initialUrl: "/_api/jobs"
    initialData: [] //usually empty array or object
*/
export const useOurApi = (initialUrl, initialData) => {
  const [url, setUrl] = useState(initialUrl);
  const [isLoading, setIsLoading] = useState(true);
  const [hasError, setHasError] = useState(false);
  const [fetchedData, setFetchedData] = useState(initialData);

  useEffect(() => {
    let unmounted = false;

    const handleFetchResponse = response => {
      setHasError(!response.ok);
      setIsLoading(false);
      return response.ok && response.json ? response.json() : initialData;
    };

    const fetchData = () => {
      setIsLoading(true);
      return fetch(url, { credentials: 'include' })
        .then(handleFetchResponse)
        .catch(handleFetchResponse);
    };

    if (initialUrl)
      fetchData().then(data => !unmounted && setFetchedData(data));

    return () => {
      unmounted = true;
    };
  }, [url]);

  return { isLoading, hasError, setUrl, data: fetchedData };
}; 
```

这样，当使用这个数据获取函数时，您可以获得一个现成的错误指示器和一个加载指示器。在一个(简体) *Jobs.jsx* 中这样使用。

```
import React from "react";
import { useOurApi } from "../Common/Services/HttpService";
import { Spinner } from "../Common/Components/Spinner";
import { ErrorMessage } from "../Common/Components/ErrorMessage";
import { JobFeed } from "./JobFeed";

export default function Jobs() {
  const url = `/_api/jobs`;
  const { data, isLoading, hasError } = useOurApi(url, {});

  if (isLoading) return <Spinner />;

  if (hasError)
    return <ErrorMessage message={`Failed to fetch open jobs 😟`} />;

  return (
    <div className="our-grid">
      <JobFeed jobs={data} />
    </div>
  );
} 
```