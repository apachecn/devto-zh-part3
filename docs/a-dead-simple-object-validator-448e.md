# 一个死的简单对象验证器

> 原文：<https://dev.to/droidmakk/a-dead-simple-object-validator-448e>

正如我所说的，这是简单的核心

> 将验证对象与模型进行比较，并返回其无效/不存在。

```
async function validator(requestObject, validatingObj){

  let paramsError = {
    missingParams: [],
    invalidParams: [],
    message: ''
  }

  function constructMessage(_paramsError){
    let missingParamsMsg = `Missing the following parameters ${_paramsError.missingParams.join()}`;

    let invalidParamsMsg = `The Following parameters are invalid ${_paramsError.invalidParams.join()}`;

    return missingParamsMsg + '. ' + invalidParamsMsg;
  }

  await Object.keys(validatingObj).map(validParam => {
  Object.keys(requestObject).includes(validParam) 
    ? typeof validatingObj[validParam] === typeof requestObject[validParam] ?
       '' : paramsError.invalidParams.push(validParam) : paramsError.missingParams.push(validParam);
  })

  paramsError.message = constructMessage(paramsError);

  return paramsError;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看一个如何使用它的例子。

```
let requestObject = { username: 'one', mailid: 123123, rides: [] };
let validatingObj = {
  username: "",
  mobile: 0,
  mailid: "",
  age: 0,
  rides: { driverid: 0, carModel: '' }
}

//validating object with the request object
validator(requestObject, validatingObj).then(paramsError => {
  console.info('paramsError',paramsError);
}).catch(err => {
  console.error("\nError",err)
}) 
```

Enter fullscreen mode Exit fullscreen mode