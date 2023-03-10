# React、Redux 和 API 的第四部分:Redux(干的)

> 原文：<https://dev.to/patrickgordon/react-redux-and-apis-part-four-redux-dry-2803>

*原帖可以在我的博客上找到*

在[的上一篇文章](https://www.patrick-gordon.com/posts/react-redux-apis-part-three/)中，我们看了如何使用 redux 与我们的 API 接口。在这篇文章中，我们将看看如何使代码更加可重用，这样我们就不必重复了。

这是上一篇文章中的例子。它使用 thunks 允许我们分派三个不同的动作，并使用`fetch`调用 API。

```
// postsActions.js

const loadPosts = () => async (dispatch, getState) => {
    dispatch({
        type: "Posts.LOAD_POSTS_REQUEST"
    });

    const fetchConfig = {
        method: "GET",
        headers: new Headers({ "Content-Type": "application/json" }),
        mode: "cors"
    }

    const response = await fetch(`https://jsonplaceholder.typicode.com/posts/`, fetchConfig);

    if (response.ok) {
        try { 
            const data = await response.json();
            dispatch({
                type: "Posts.LOAD_POSTS_SUCCESS",
                payload: {
                    data
                }
            });
            return;
        } catch (error) {
            dispatch({
                type: "Posts.LOAD_POSTS_FAILURE"
            });
        }
    }

    dispatch({
        type: "Posts.LOAD_POSTS_FAILURE"
    });
} 
```

让我们将 API 的调用抽象成一个名为`apiHelpers.js`的新文件。此函数将一个对象作为其唯一的参数，该参数包含以下参数:

1.  config——覆盖 fetchConfig，比如使用哪个 REST 方法
2.  分派 thunk 有权访问的分派
3.  端点——要查询的 API 的端点
4.  types——放入 tuple 的每个 redux 动作使用的三个字符串。它们在一个数组中，所以顺序非常重要——请求，成功，失败。

```
// apiHelper.js
const callAPI = async (args) => {
    const { 
        config, 
        dispatch,
        endpoint, 
        types 
    } = args;

    const [request, success, failure] = types;
    const url = `https://jsonplaceholder.typicode.com/${endpoint}`;

    const fetchConfig = {
        headers: new Headers({ "Content-Type": "application/json" }),
        mode: "cors",
        ...config
    }

    dispatch({
        type: request
    });

    const response = await fetch(url, fetchConfig);

    if (response.ok) {
        try { 
            const data = await response.json();
            dispatch({
                type: success,
                payload: {
                    data
                }
            });
            return;
        } catch (error) {
            dispatch({
                type: failure
            });
        }
    }

    dispatch({
        type: failure
    });
}

export callAPI; 
```

通过为类型引入元组，我们能够非常容易地在其他操作中重用它。下面是更新后的`posts`动作:

```
// postsActions.js
const loadPosts = () => async dispatch => {
    callAPI({ 
        config: { method: "GET" },
        dispatch,
        endpoint: "posts",
        types: ["Posts.LOAD_POSTS_REQUEST", "Posts.LOAD_POSTS_SUCCESS", "Posts.LOAD_POSTS_FAILURE"]
    });
} 
```

以及新添加的评论动作:

```
// commentsActions.js

const loadComments = () => async dispatch => {
    callAPI({ 
        config: { method: "GET" },
        dispatch,
        endpoint: "comments",
        types: ["Comments.LOAD_COMMENTS_REQUEST", "Comments.LOAD_COMMENTS_SUCCESS", "Comments.LOAD_COMMENTS_FAILURE"]
    });
} 
```

# 接下来:

在下一篇文章中，我们将深入介绍 Redux，引入一个中间件来处理 API 调用，并讨论这种方法的一些好处。