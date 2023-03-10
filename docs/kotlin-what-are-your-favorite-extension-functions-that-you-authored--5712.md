# Kotlin:你最喜欢自己编写的扩展函数是什么？

> 原文：<https://dev.to/vishnuharidas/kotlin-what-are-your-favorite-extension-functions-that-you-authored--5712>

*我的博客上也发表在这里:[https://iamvishnu.com/posts/kotlin-extension-functions](https://iamvishnu.com/posts/kotlin-extension-functions)T3】*

Kotlin 有一个很好的特性，叫做[“扩展函数”](https://kotlinlang.org/docs/reference/extensions.html)——向现有类添加新功能，无需继承或编写大量样板代码。Kotlin 中允许使用扩展函数和扩展属性。

下面是一个对`Float`类的扩展示例，它添加了一个名为`asDollars()`的新函数，将浮点数打印为货币值:

```
fun Float?.asDollars() = "USD%.2f".format(this ?: 0.0F) 
```

现在我可以在代码的任何地方写:

```
val money = 42.0F
println( money.asDollars() ) // will print "USD42.00" 
```

**注意:**一个扩展函数并不真正修改现有的类。相反，接收器对象在编译时作为第一个参数传递。更多信息，请阅读:[静态解析扩展函数](https://kotlinlang.org/docs/reference/extensions.html#extensions-are-resolved-statically)。

**这里是我自己的一些扩展，我在 Android 的许多项目中使用过:**

### 1。一些警报对话

```
fun Context.showAlertDialog(
    title: String,
    message: String,
    ok: Pair<String,()->Unit>,
    cancel: Pair<String,()->Unit>? = null){

    val builder = AlertDialog.Builder(this)
        .setTitle(title)
        .setMessage(message)
        .setCancelable(false)
        .setPositiveButton(ok.first) { _,_ -> ok.second() }

    cancel?.let{
        builder.setNegativeButton(it.first) { _, _ -> it.second() }
    }

    builder.create().show()
}

fun Context.showConfirmDialog(
    title: String,
    message: String,
    ok: Pair<String,()->Unit>,
    cancel: Pair<String,()->Unit>? = null
) = showAlertDialog(title, message, ok, cancel)

fun Context.showErrorDialog(message: String, action: () -> Unit){
    showAlertDialog(
        title = "Error",
        message = message,
        ok = "OK" to action
    )
} 
```

活动(或片段)的使用情况:

```
fun onResume(){
    showAlertDialog(
        title = "Welcome",
        message = "Hello, welcome to the show! Do you want to proceed?",
        ok = "Yes" to { nextScreen() },
        cancel = "No" to { finish() }
    )

    // showing an error
    showError("No internet connection. Please try later") { finish() }
} 
```

### 2。一个简单的点击监听器

```
inline fun View.onClick(crossinline f: ()-> Unit) = this.setOnClickListener { f() } 
```

用法:

```
btnNext.onClick { nextScreen() } 
```

### 3。一些`EditText`验证

```
fun isValidText(s: String?) = s!=null && !s.isEmpty()
fun isValidEmail(s: String?) = isValidText(s) &&  android.util.Patterns.EMAIL_ADDRESS.matcher(s).matches()

fun EditText.trimmedText() = this.text?.trim().toString()
fun EditText.isValidText() = isValidText(this.trimmedText())
fun EditText.isValidEmail() = isValidEmail(this.trimmedText()) 
```

用法:

```
isValidForm = editEmail.isValidEmail() && editUsername.isValidText() 
```

你为自己的项目创作的最喜欢的扩展功能/属性是什么？