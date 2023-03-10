# 亲爱的 VueJS 开发者，VeeValidate 可能不值得

> 原文：<https://dev.to/sduduzog/dear-vuejs-devs-veevalidate-might-not-be-worth-it-3j9l>

事实上，我不会把 Vuelidate 排除在外的。我想在注册时验证用户的输入，所以我知道最好用一个经过测试的包来验证。

## Vuelidate 表面上很伟大但是...

不过，当你像我一样使用 typescript 时，就不会这样了。我稍后会谈到这一点，但长话短说，试图让它与我一起工作是一场噩梦。

## 我猜 VeeValidate 是最好的。只是因为...

当运行`npm build`并得到我的入口包超过推荐大小的警告时，我惊慌失措。我对性能产生了偏执，因为我在项目中做出的选择反映了我作为程序员的性格，其他的则是安全性和可访问性。

在我提到的两个库中，Vuelidate 在与 typescript 项目集成时存在类型问题。这个问题已经被发布了好几次，开发者可能会给出解决方案。简而言之，这里有一段话总结了这个问题。让我感动的部分是下面的引用。

> GitHub 上有更多的问题在讨论 TypeScript 集成，并且多个开发人员再次尝试向项目添加类型，在几个月的时间里提供概念证明或第一个版本。回购所有者再次兴奋起来。两个月前，他再次声明他将“很快推出正式的打字员包”。直到今天，什么也没发生。

有趣的是，正是这篇文章让我认识了 VeeValidate。在我看到构建输出之前，我对它的实现和易用性很满意。

在我的整个项目中，我只验证了 4 个输入字段，仅此而已。但是捆绑的 VeeValidate 比我的应用中捆绑的所有其他 node_modules 包都大。甚至比 *Vue* 大至少 1/3，如果不是更多的话。我试着延迟加载它，但是它没有被注入到使用它的组件中。

当然，创作者承认[这一点，甚至为它提供了可能的解决方案([https://Bai anat . github . io/vee-validate/concepts/bundle-size . html # minimal-bundle](https://baianat.github.io/vee-validate/concepts/bundle-size.html#minimal-bundle))]*但是*我经历了这一切，并没有打算解决我正在寻找的解决方案的问题。

我的问题的答案已经摆在我面前了。

v-bind |计算属性|正则表达式

```
<input
          type="text" 
          name="email"
          @blur="touch('email')"
          :class="{error: !emailValid}"
          placeholder="Email" 
          v-model="email"> 
```

Enter fullscreen mode Exit fullscreen mode

与其他字段一样，当它失去焦点时，意味着它已经被触摸。所以

```
private touch(item: string) {
    this.validate = true;
    if (!this.dirty.includes(item)) {
      this.dirty.push(item);
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了在输入框中显示一个错误，我检查它是否被触摸过(脏的),以及给定的任何输入是否与电子邮件模式匹配。

```
private get emailValid() {
    if (this.dirty.includes('email')) {
      const valid = /^([\w\.]+@[a-zA-Z_]+?(\.[a-zA-Z]{2,6}){1,2})$/.test(this.email);
      if (valid) {
        this.dirty.splice(this.dirty.indexOf('email'));
      }
      return valid;
    }
    return true;
  } 
```

Enter fullscreen mode Exit fullscreen mode

因此，受其他验证器的启发，为了获得洞察力，我计算了一系列错误，如下所示

```
private get errors(): string[] {
    const field: string[] = [];
    if (!this.namesValid) {
      field.push('names');
    }
    if (!this.emailValid) {
      field.push('email');
    }
    if (!this.passwordValid) {
      field.push('password');
    }
    if (!this.passwordConfirmed) {
      field.push('password_confirm');
    }
    return field;
  } 
```

Enter fullscreen mode Exit fullscreen mode

但仅此还不够。没有接触输入字段并不意味着它们仍然有效，为了解决这个问题，我必须再做一次检查

```
private get empty(): string[] {
    const field: string[] = [];
    if (this.names.length === 0) {
      field.push('names');
    }
    if (this.email.length === 0) {
      field.push('email');
    }
    if (this.password.length === 0) {
      field.push('password');
    }
    if (this.pwd2.length === 0) {
      field.push('password_confirm');
    }
    return field;
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，当表格提交后，我把所有的东西都打包到这里

```
private signUp(): void {
    if (this.loading) {
      return;
    }
    if (this.empty.length !== 0) {
      return;
    }
    if (this.errors.length !== 0) {
      return;
    }
    this.loading = true;
    createUser(this.names, this.email, this.password)
      .then((response) => {
        this.toSignin();
      })
      .catch(this.handleNetworkError);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 得出结论

我知道在我提到的库的一些细节上，我可能被误导了，也许我的解决方案不如它们好，但是重新发明这个轮子是值得的。我为大多数富裕的潜在网站访问者减少了几毫秒的下载时间，我也为下一个十亿用户减少了一秒钟和一些变化