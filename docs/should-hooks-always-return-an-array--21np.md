# 钩子应该总是返回一个数组吗？

> 原文：<https://dev.to/theianjones/should-hooks-always-return-an-array--21np>

看看盖茨比的 [`useStaticQuery`](https://www.gatsbyjs.org/docs/use-static-query/) ，它只是返回一个对象，而我会期望它返回一个数组。我最近一直在使用 urqls [`useQuery`](https://github.com/FormidableLabs/urql/blob/master/docs/getting-started.md#using-hooks) ，它返回一个数组，结果在数组的第一个点。

这只是一种偏好，还是应该成为我们图书馆应该遵循的惯例？