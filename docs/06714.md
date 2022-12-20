# 减少 Redux 在 TypeScript 中没有库支持的动作创建器样板文件

> 原文：<https://dev.to/airtoxin/reduce-redux-s-action-creator-boilerplates-without-library-support-in-typescript-17oh>

```
// Define action-creators
export const fetchPostsRequest = () => ({
  type: "FETCH_POSTS" as const
});
export const fetchPostsSuccess = (posts: any) => ({
  type: "FETCH_POSTS_SUCCESS" as const,
  payload: { posts }
});
export const fetchPostsFailure = (error: Error) => ({
  type: "FETCH_POSTS_FAILURE" as const,
  error
});

// Define Action and ActionTypes
export type FetchPostAction = ReturnType<
  | typeof fetchPostsRequest
  | typeof fetchPostsSuccess
  | typeof fetchPostsFailure
>
export type FetchPostsActionTypes = FetchPostAction["type"];

// Let's use
const action: FetchPostAction = {
  type: "FETCH_POSTS_FAILURE"
}; // -> Property 'error' is missing 
```