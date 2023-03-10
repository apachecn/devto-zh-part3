# 如何创建带验证的自定义表单并在 React Native 中滚动到无效逻辑(第一部分:验证)

> 原文：<https://dev.to/lfkwtz/how-to-create-custom-forms-with-validation-and-scroll-to-invalid-logic-in-react-native-part-one-validation-269o>

想要保持最新状态？现在看看 React Native，双周刊 [React Native 简讯](https://reactnativenow.com/)

* * *

当我们[在 2017 年末开始第一次重写](https://medium.com/lawnstarter-engineering/react-native-one-year-later-what-weve-learned-c23dc13cec80)到 React Native 时，我们知道我们的应用程序将有相当多的表格需要我们的提供商填写——特别是当第一次进入我们的平台时。

在 web 上，我们非常熟悉各种 web APIs 和库，它们提供表单验证和滚动到第一个无效输入的能力——由于存在一些长表单，我们坚持要有这种能力——但 React Native 当时并不太可用，这就是我们最终滚动自己的表单的原因。

[![scroll to invalid logic was crucial for this long form](img/4a7757c98baa3bfad1410d66d85b43ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AQ1gpVAQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/26pna4808y7ijhvij4kn.gif)

在接下来的几篇文章中，我们将回顾我们如何构建我们的逻辑，在这种方法中需要注意的一些注意事项，以及最后可以对它进行的一些改进。

如果你想向前跳，看看本教程创建的演示应用程序，你可以在这里这样做[。](https://github.com/lawnstarter/react-native-form-helpers/tree/5bf9f1f27be1628927af1eea4753edb1265ec76a/demoApp)

## 验证

第一步是创建一个服务，这样我们就可以根据定义的验证逻辑字典来验证我们的输入。为此，我们首先需要安装 [Validate.js](https://validatejs.org/) 作为助手库。这个库的好处在于它是一个纯 Javascript 解决方案，所以不需要担心任何特定于框架的约束——所以如果您最终想要将验证移植到 web 或不同的移动框架，您可以这样做。

设置 Validate.js 的第一步是创建一个字典，包含所有不同类型的输入字段以及每个字段的验证规则。通读关于这方面的[文档](https://validatejs.org/#validators)肯定会有帮助——但是这里有一个我们的一些规则的例子来给你一些背景:

```
export const validationDictionary = {
  bool: {
    presence: {
      allowEmpty: false,
      message: "^This is required"
    }
  },

  day: {
    presence: {
      allowEmpty: false,
      message: "^This is required"
    },
    numericality: {
      greaterThan: 0,
      lessThanOrEqualTo: 31,
      message: "^Must be valid"
    }
  },

  email: {
    presence: {
      allowEmpty: false,
      message: "^This is required"
    },
    email: {
      message: "^Email address must be valid"
    }
  },
}; 
```

[现在我们已经有了字典设置](https://github.com/lawnstarter/react-native-form-helpers/blob/5bf9f1f27be1628927af1eea4753edb1265ec76a/demoApp/validation/dictionary.js)，让我们看看表单上的本地状态是什么样子。

```
this.state = {
  inputs: {
    first_name: {
      type: "generic",
      value: ""
    },
    last_name: {
      type: "generic",
      value: ""
    },
    birthday_month: {
      type: "month",
      value: ""
    },
    birthday_day: {
      type: "day",
      value: ""
    }
  }
}; 
```

如您所见，我们已经将所有的`inputs`嵌套在状态的输入对象中，以保持它的组织性。然后，每个单独的输入都有自己的对象，其中嵌套了两个关键部分。首先是`type`，它是一个字符串表示，链接到我们想要对输入执行的验证类型。接下来是`value`，输入值将存储在这里。还要注意，每个输入的键都唯一地绑定到 reference 中的输入。

接下来，让我们创建一些助手方法，我们将利用它们来验证我们的输入。为了便于解释这些助手方法是如何工作的——假设我们正在验证一个定义为`day`的输入，它允许从 1 到 31 的整数，而用户刚刚在输入中键入了 32。

```
function validateInput({ type, value }) {
  const result = validatejs(
    {
      [type]: value
    },
    {
      [type]: validationDictionary[type]
    }
  );

  if (result) {
    return result[type][0];
  }

  return null;
} 
```

首先，我们将设置主助手，它将验证我们的输入，名为`validateInput`。这个方法有两个参数，`type`(‘day’)和`value` (32)。在这个方法中，我们立即用两个参数调用 Validate.js 第一个是我们的`type`和`value`的一个对象，第二个是我们希望从字典中检查的验证的一个对象(确保您的`validationDictionary`存在，以便这个帮助器引用)。如果该调用产生一个结果——在本例中是这样，因为我们的字典强制使用 1 到 31 之间的数字，那么将返回错误`"Must be valid"`。

```
function getInputValidationState({ input, value }) {
  return {
    ...input,
    value,
    errorLabel: input.optional
      ? null
      : validateInput({ type: input.type, value })
  };
} 
```

接下来，我们将创建一个名为`getInputValidationState`的助手方法。该方法将通过首先展开所有现有参数来更新`state.inputs.day`对象，然后通过调用我们刚刚创建的`validateInput`方法来显式更新`errorLabel`参数(只要输入没有被标记为可选的)。

```
function onInputChange({ id, value, cb = () => {} }) {
  const { inputs } = this.state;
  this.setState(
    {
      inputs: {
        ...inputs,
        [id]: getInputValidationState({
          input: inputs[id],
          value
        })
      }
    },
    cb
  );
} 
```

最后，我们需要一个 helper 方法，我们将把它直接链接到我们输入的 change 事件中(即`TextInput`上的`onChangeText`属性)。该方法不仅会更新 state.inputs.day.value，还会实际执行验证并返回所有相关信息。为此，我们将创建一个接受三个参数的`onInputChange`助手。首先是一个 id，它是输入的惟一键的字符串表示形式，在本例中为`day`。接下来，输入`value` (32)。最后，一个可选的回调方法。我们需要将这个方法绑定到我们的构造函数，这样它就可以访问 state，这将允许我们为特定的输入提取整个对象。

```
this.onInputChange = validationService.onInputChange.bind(this); 
```

[现在我们已经设置好了我们的助手](https://github.com/lawnstarter/react-native-form-helpers/blob/5bf9f1f27be1628927af1eea4753edb1265ec76a/demoApp/validation/service.js)，让我们回到表单的设置。我们刚刚创建的服务的美妙之处在于，它可以验证任何数据结构，只要您的字典被设置为解析它(再次，参考[文档](https://validatejs.org/#validators)以获得关于复杂规则的更多帮助)。但是为了简单起见，我将使用我们的`onInputChange`方法来验证一些`TextInput`组件。

```
 renderError(id) {
    const { inputs } = this.state;
    if (inputs[id].errorLabel) {
      return <Text style={styles.error}>{inputs[id].errorLabel}</Text>;
    }
    return null;
  }

  render() {
    return (
      <View style={styles.container}>
        <ScrollView>
          <View>
            <Text>First Name</Text>
            <TextInput
              style={styles.input}
              onChangeText={value => {
                this.onInputChange({ id: "first_name", value });
              }}
            />
            {this.renderError("first_name")}
          </View> 
```

下面是我们的表单设置好后的一个片段。本质上，我们有四个输入，它们都有验证检查，还有一个有用的`renderError`方法，用来查看我们每个输入上的`errorLabel`键是否正确。如果是，我们会显示错误。

[![](img/7d759c1077d6635af86e94fca9055dc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D94qx1XO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ucelecfzh4aaqjlicp5.png)

很好，现在我们已经验证了单个输入——但是如果我们想一次验证整个表单呢？让我们创建一个名为`getFormValidation`的新助手方法来完成这项工作。

```
function getFormValidation() {
  const { inputs } = this.state;

  const updatedInputs = {};

  for (const [key, input] of Object.entries(inputs)) {
    updatedInputs[key] = getInputValidationState({
      input,
      value: input.value
    });
  }

  this.setState({
    inputs: updatedInputs
  });
} 
```

对于这个助手方法，我们将迭代整个`inputs`对象，并对每个输入进行验证检查。当我们运行这个验证时，我们重新构建`inputs`对象，然后将其设置为 state。确保将这个方法也绑定到构造函数，然后在窗体上抛出一个按钮，并测试调用这个方法。

[![](img/f18b21fd4c17776a1c47af3a59b3f9b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PcyDeYsc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wpxwnzl2adwybxn500n9.gif)

现在[我们的表单已经用我们的基本验证逻辑设置好了](https://github.com/lawnstarter/react-native-form-helpers/blob/5bf9f1f27be1628927af1eea4753edb1265ec76a/demoApp/App.js)。

## 下一步

这是第一部分的全部内容。在第二部分中，我们将回顾如何设置“滚动到无效”逻辑，这样我们就可以确保如果我们有一个很长的表单，而我们的用户忘记在顶部输入的字段中输入内容，我们会将他们带到表单上的那个位置。