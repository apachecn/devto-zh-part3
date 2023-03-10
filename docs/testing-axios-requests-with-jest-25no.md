# 用 Jest 测试 Axios 请求

> 原文：<https://dev.to/benweiser/testing-axios-requests-with-jest-25no>

假设我们有一个简单的异步函数，它调用一些远程数据。我们传入查询和配置对象。要查看可以通过该请求配置传递的内容的示例，请参见[Axios 文档](https://github.com/axios/axios#request-config)。在这种情况下，我的查询只是配置中的`url`键，例如可能是 API 端点的`/user`。这个函数只是从 axios 返回数据(来自端点的实际响应体)。如果我们未能从端点获得响应，函数将会捕获并抛出一个控制台错误。

request.js

```
export const fetchData = async (query, config
) => {
  try {
    const { data } = await axios.request({
      method: 'get',
      url: encodeURI(query),
      ...config
    });

    return data;
  } catch (e) {
    console.error('Could not fetchData', e);
  }
}; 
```

现在让我们创建一个新文件`request.spec.js`我们要添加两个导入

```
import mockAxios from 'axios';
import { fetchData } from '../request'; 
```

为了测试这一点，我们需要使用一个模拟版本的 Axios，它可以方便地从核心库中导入。我们现在准备编写我们的测试

```
 it('should call a fetchData function', done => {
    fetchData('/test', {}).then(response => {
      expect(response).toEqual({
        data: {},
      });
    });
    expect(mockAxios.request).toHaveBeenCalledWith({
      method: 'get',
      url: '/test'
    });
    expect(mockAxios.request).toHaveBeenCalledTimes(1);
    expect(consoleErrorSpy).not.toHaveBeenCalled();
    done();
  }); 
```

首先，我们用一个测试查询和一个空配置调用 then 函数，以确保 then 测试我们的响应是正确的。现在我们正在测试`mockAxios.request`它是用我们期望的参数调用的，它只被调用了一次，并且我们的函数没有抛出错误。最后我们调用`done()`回调，让我们知道我们的异步操作已经完成。