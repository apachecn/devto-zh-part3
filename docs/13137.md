# 递归地从分页 API 中检索整个数据。

> 原文：<https://dev.to/nirmal_kumar/retrieve-entire-data-from-paginated-api-recursively-3pl4>

[![](img/300334c07fb14a73374a119cd0c46e57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dXQK8zC1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1536227284117-59ae8d1a69aa%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D800%26q%3D80)

在这篇文章中，我将展示如何使用递归方法从分页 API 中检索数据。

为了解释这个功能，我将使用一个我在 mockapi.io 中创建的虚拟 REST API。默认情况下，它返回 91 条记录。在实时情况下，数据集可能超过 10000 个数据项。不建议在单个 api 中查询整个数据集，因为这会增加服务器性能的负担，也会给客户端带来沉重的负载。推荐的方法是在客户端显示时对结果进行分页。这就是为什么大多数 api 在 API 中提供了一些额外的过滤器，以便只返回用户感兴趣的记录，而不是返回整个数据存储。

这里是 API 使用的一些常用过滤器。

1.offset，limit:“limit”确定从“offset”
开始检索多少条记录 2.page，limit:“page”根据每页的“limit”->记录数确定服务器需要返回哪些数据。

有关如何检索分页结果的更多详细信息，请参考相应的 API 开发人员手册。

示例 API 端点:

无分页:
[https://5b5cb0546a725000148a67ab.mockapi.io/api/v1/users](https://5b5cb0546a725000148a67ab.mockapi.io/api/v1/users)

同分页:[https://5b5cb0546a725000148a67ab.mockapi.io/api/v1/users?page=4 & limit=2](https://5b5cb0546a725000148a67ab.mockapi.io/api/v1/users?page=4&limit=2)

上述 API 端点对于正常用例来说是很好的。但是在这些需求中，您需要从 API 中检索所有数据，而 API 被设计为每次调用仅返回 50 条记录。在这个场景中，我们可以创建一个递归的分页 API 调用，并捕获整个数据集。

请记住，如果您没有正确处理这个问题，它可能会以无限循环结束，您的浏览器可能会挂起。在这个例子中，我限制了每页 20 个项目。

```
const limitPerPage=20;
const apiUrl="https://5b5cb0546a725000148a67ab.mockapi.io/api/v1/users";

const getUsers = async function(pageNo = 1) {

let actualUrl=apiUrl + `?page=${pageNo}&limit=${limitPerPage}`;
var apiResults=await fetch(actualUrl)
.then(resp=>{
return resp.json();
});

return apiResults;

}

const getEntireUserList = async function(pageNo = 1) {
  const results = await getUsers(pageNo);
  console.log("Retreiving data from API for page : " + pageNo);
  if (results.length>0) {
    return results.concat(await getEntireUserList(pageNo+1));
  } else {
    return results;
  }
};

(async ()=>{

    const entireList=await getEntireUserList();
    console.log(entireList);

})(); 
```

我使用了浏览器开发人员控制台来显示输出。请看这张 gif[http://recordit.co/p3FkQS7QpJ](http://recordit.co/p3FkQS7QpJ)

感谢阅读&快乐编码..