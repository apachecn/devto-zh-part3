# 反应怀疑

> 原文：<https://dev.to/amankumar4all/reactjs-doubt-5937>

大家好
我需要你们的帮助
从 API 获得成功响应后，我想在浏览器中更改页面/加载新组件，但没有找到正确的方法。
所以请帮助解决这个问题。
例-
axios . post([https://api.staging.goplannr.com/v2/user/otp/generate](https://api.staging.goplannr.com/v2/user/otp/generate)，this.state)

```
 .then(response=> {

        console.log(response);

        return(

            <div>

                <OTP/>

                </div>

        )

    } 
```

OTP 是我的组件，但在浏览器中，它不工作，也没有显示任何响应。