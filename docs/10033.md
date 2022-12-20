# 在 React Native 中创建一个 FormBuilder 组件(第 3 部分)

> 原文：<https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-3-1aib>

本系列内容:

*   [第 1 部分:创建新的 React 原生应用](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-intro--part-1-1m84)
*   [第 2 部分:创建一个简单的工资计算器表单](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-2-2dec)
*   第 3 部分:创建定制表单输入和按钮组件(当前)
*   [第四部分:关于`FormBuilder`组件的工作](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-4-5gc3)
*   [第 5 部分:即时启用/禁用表单按钮](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-5-535h)
*   [第 6 部分:创建注册表单](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-6-2ohd)
*   第 7 部分:增加对布尔字段类型的支持

# 第 3 部分:创建自定义表单输入和按钮组件

在创建 React 本地应用时，您总是会遇到希望在多个地方使用同一个 UI 元素的情况。通常，这些元素会以特定方式出现和表现。在不同的地方反复定制一个组件并不是一个好的做法，所以为了避免任何代码重复，建议将你的应用程序屏幕分成多个小组件，如果需要的话，这些组件可以在你的应用程序中的任何地方重复使用。

### 创建自定义 FormButton 组件

让我们以表单按钮为例。我们有两个，他们都:

*   接受头衔
*   接受点击按钮时触发的回调函数
*   有相同的风格。

我们可以很容易地定义一个定制的`FormButton`组件，它将满足所有这些需求，并且看起来像这样:

```
import React from 'react';
import PropTypes from 'prop-types';
import { TouchableOpacity, Text, StyleSheet } from 'react-native';

/**
 * A stateless function component which renders a button.
 *
 * @param {obj} props
 */
const FormButton = (props) => {
    const { children, onPress } = props;

    return (
        <TouchableOpacity style={styles.button} onPress={onPress}>
            <Text style={styles.buttonText}>{children}</Text>
        </TouchableOpacity>
    );
};

FormButton.propTypes = {
    onPress: PropTypes.func,
    children: PropTypes.string.isRequired,
};

FormButton.defaultProps = {
    onPress: f => f,
};

const styles = StyleSheet.create({
    button: {
        backgroundColor: '#FD6592',
        borderRadius: 3,
        height: 40,
        marginBottom: 15,
        justifyContent: 'center',
        alignItems: 'center',
    },
    buttonText: {
        color: '#FFF',
        fontWeight: 'bold',
        fontSize: 16,
    },
});

export default FormButton; 
```

关于创建这个新组件，我想提到以下几点:

*   我们已经将按钮样式从我们的`App.js`文件移到这个新组件中；
*   我们使用 [prop-types](https://www.npmjs.com/package/prop-types) 库来记录传递给组件的预期属性类型。有关如何使用 PropTypes 的更多信息，请查看 [React 的 PropTypes 类型检查](https://reactjs.org/docs/typechecking-with-proptypes.html)文档。请注意，在构建应用程序时，您不需要使用 PropTypes。我个人认为这是记录组件的一个非常好的方法，对于你或者其他任何将来需要使用你的代码的开发人员来说都是非常方便的。如您所见，我们的组件接受两个道具:`onPress`和`children`。因为`onPress`不是必需的道具，所以我们需要为它定义一个默认值，在我们的例子中是一个“什么都不做”的函数(或者更具体的说，是一个隐式返回的箭头函数)；
*   因为我们的组件除了向用户呈现某些东西之外什么也不做，所以我将它定义为一个无状态函数，而不是扩展了`React.Component`的`class`。同样，这只是一个偏好。事实上，我们马上要讨论的另一个定制组件(`FormTextInput`)被定义为`class`，只是为了演示这两种方法都可以使用。

现在，我们可以在应用程序的任何地方重复使用这个自定义按钮，如下所示:

```
// ...
import FormButton from 'path/to/FormButton';
// ...
render() {
    // ...
    return (
        <FormButton onPress={() => { console.log('Button pressed!'); }}>
            Press Me!
        </FormButton>
    );
} 
```

### 创建自定义 FormTextInput 组件

就像我们为表单按钮创建定制组件一样，我们也可以为表单文本输入创建一个组件。这样，如果我们有多个屏幕，其中有些表单需要文本输入，我们可以轻松地重用我们的自定义文本输入组件。让我们创建一个新组件，并将其命名为`FormTextInput`，它看起来像这样:

```
import React from 'react';
import PropTypes from 'prop-types';
import {
    View, TextInput, Text, StyleSheet,
} from 'react-native';

/**
 * A component which renders a TextInput with a label above it.
 * Note: This component can easily be written as a stateless function
 *      since it only includes the `render()` function and nothing else
 *      (see FormButton component as an example).
 */
class FormTextInput extends React.Component {
    render() {
        const { labelText, ...inputProps } = this.props;

        return (
            <View style={styles.inputWrapper}>
                {labelText && <Text style={styles.label}>{labelText}</Text>}
                <TextInput style={styles.textInput} blurOnSubmit {...inputProps} />
            </View>
        );
    }
}

FormTextInput.propTypes = {
    labelText: PropTypes.string,
};

FormTextInput.defaultProps = {
    labelText: null,
};

const styles = StyleSheet.create({
    inputWrapper: {
        marginBottom: 15,
        flexDirection: 'column',
    },
    textInput: {
        height: 40,
        borderColor: '#FFF',
        borderWidth: 1,
        borderRadius: 3,
        backgroundColor: '#FFF',
        paddingHorizontal: 10,
        fontSize: 18,
        color: '#3F4EA5',
    },
    label: {
        color: '#FFF',
        marginBottom: 5,
    },
});

export default FormTextInput; 
```

关于创建这个新组件，我想提到以下几点:

*   我们已经将文本输入样式从我们的`App.js`文件移到这个新组件中；

*   我们利用 JavaScript [rest](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) 和 [spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 操作符来收集所有默认`TextInput`的道具(例如`placeholder`、`keyboardType`等)。)我们将传递给`FormTextInput`组件，并将它们扩展到 React Native 的`TextInput`组件中。请注意这两行代码:

```
// Using the "rest" operator.
// You can read the following line like this:
// Hey, give me the "labelText" property's value from "this.props" object 
// and store it into a "labelText" variable. Then, take all of the remaining
// properties from "this.props" and store them into a "inputProps" variable 
// (which ends up being an object).
const { labelText, ...inputProps } = this.props;

// Using the "spread" operator
// Now we can simply take our "inputProps" object which contains
// all of the props we wanted to pass down to the "TextInput" component,
// and spread them out into it.
// (same as writing: "<TextInput placeholder={inputProps.placeholder}" />
<TextInput style={styles.textInput} blurOnSubmit {...inputProps} /> 
```

*   我们还使用`prop-types`库来记录传递给组件的预期属性类型。请注意，我们没有记录任何默认`TextInput`组件的属性。这是因为我们实际上无法控制它们，而 React Native 本身会处理这些问题。我们在这里记录的唯一道具是`labelText`，它是一个可选的自定义道具，我们用它在文本输入上方显示一个标签。看看我们在 JSX 检查`labelText`是否有价值的方式:

```
{labelText && <Text style={styles.label}>{labelText}</Text>} 
```

您可以这样阅读这一行:*如果 labelText 恰好是一个“truthy”值，那么呈现出文本组件。*

### 更新我们的`App.js`文件

现在是时候更新我们的主`App.js`文件来使用这些新创建的组件了。更新后的版本应该如下所示:

```
import React, { Component } from 'react';
import {
    StyleSheet, KeyboardAvoidingView, Text, Keyboard, Alert,
} from 'react-native';

import FormTextInput from './js/components/FormTextInput';
import FormButton from './js/components/FormButton';

export default class App extends Component {
    // ...
    render() {
        // ...
        return (
            <KeyboardAvoidingView behavior="padding" style={styles.container}>
                <Text style={styles.screenTitle}>Salary Calculator</Text>
                <FormTextInput
                    placeholder="$0"
                    keyboardType="numeric"
                    returnKeyType="done"
                    onChangeText={text => this.setState({ hourlyRate: text })}
                    value={hourlyRate}
                    labelText="Hourly Rate"
                />
                <FormTextInput
                    placeholder="0"
                    keyboardType="numeric"
                    returnKeyType="done"
                    onChangeText={text => this.setState({ hoursPerWeek: text })}
                    value={hoursPerWeek}
                />
                <FormButton onPress={this.handleSubmit}>Calculate</FormButton>
                <FormButton onPress={this.resetForm}>Reset</FormButton>
            </KeyboardAvoidingView>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
        paddingHorizontal: 20,
        backgroundColor: '#3F4EA5',
    },
    screenTitle: {
        fontSize: 35,
        textAlign: 'center',
        margin: 10,
        color: '#FFF',
    },
}); 
```

要查看完整的变更列表，请查看 GitHub 上的这个[提交。](https://github.com/vasilestefirta/react-native-form-builder/commit/f2cb16527f3d79a1aa903ee6d27acc44cc4a97e9)

* * *

如果你能走到这一步，那就太棒了👏。现在是时候进入第四部分了，在这里我们将开始定制一个`FormBuilder`组件，它将呈现出一个全功能的表单。👉