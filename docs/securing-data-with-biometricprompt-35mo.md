# 使用生物计量提示保护数据

> 原文：<https://dev.to/ranilch/securing-data-with-biometricprompt-35mo>

在 Android 9 Pie 之前，实现指纹扫描对话框的选项有:

*   从头开始构建您自己的
*   复制 Google 的[示例实现](https://github.com/googlesamples/android-FingerprintDialog)
*   使用一些非官方的图书馆

不幸的是，这两种选择都不太好。当然，除非你有非常具体的 UI 需求，并且**需要**来构建你自己的 UI。但是对于大多数应用程序来说，有一个通用的对话框就足够了，如果有一个官方的就更好了。

幸运的是，自从 Android 9 Pie 以来，Google 已经引入了生物计量提示来解决这个问题。希望这篇文章能帮助你整合它。

平心而论，关于这个问题已经有一些很棒的博客文章了。但是除了荣誉之外，我遇到的人缺少一些东西，所以我希望这篇文章能填补这些空白。

Mark Ellison 的文章很棒，但是他的例子只适用于 Android 9 Pie，并没有解决加密数据的问题。 [Natig Babayev 的帖子](https://medium.com/mindorks/fingerprint-authentication-using-biometricprompt-compat-1466365b4795)也很精彩，但它没有展示如何封装它，也没有解决加密问题。

一如既往，我欢迎，并实际上寻求反馈，所以请随意批评。

## 基础知识

由于大多数 Android 设备制造商要么努力，要么故意阻止，将他们的设备升级到新的 Android 版本，我们仍然被迫支持旧的 Android 版本。

幸运的是，谷歌正在用他们的 **AndroidX** 库(以前被称为支持库)做繁重的工作

因此，首先你需要添加一个**附加依赖项**来利用旧版本 Android 上的生物识别提示。

```
implementation 'androidx.biometric:biometric:1.0.0-alpha04' 
```

### 基本组件

生物识别提示中涉及的主要类是，多么令人惊讶，叫做`androidx.biometric.BiometricPrompt`。您可以使用其唯一的公共构造函数来创建实例:

```
/**
 * ...
 * 
 * @param fragmentActivity A reference to the client's activity.
 * @param executor An executor to handle callback events.
 * @param callback An object to receive authentication events.
 */
public BiometricPrompt(@NonNull FragmentActivity fragmentActivity,
                       @NonNull Executor executor, 
                       @NonNull AuthenticationCallback callback) 
```

第三个参数:`AuthenticationCallback`是一个抽象的静态类，有三个非抽象的方法，所以从技术上来说我们不需要重写其中的任何一个。

所以为了一个简单的“hello-world”提示，我们可以传递一个匿名对象，而不用覆盖任何东西。

```
BiometricPrompt(fragmentActivity, executor, 
    object : BiometricPrompt.AuthenticationCallback() {}) 
```

其次，我们需要描述提示符是什么样子的。这就是`BiometricPrompt.PromptInfo`发挥作用的地方。它方便地提供了一个构建器类:

```
val cancelString = activity.getString(android.R.string.cancel)
BiometricPrompt.PromptInfo.Builder()
    .setTitle("Prompt Title") // required
    .setSubtitle("Prompt Subtitle")
    .setDescription("Prompt Description: lorem ipsum")
    .setNegativeButtonText(cancelString) // required
    .build() 
```

只能配置这么多。这应该是不言自明的。只有*标题*和*否定按钮文本*是必需的。

要启动提示符，我们所要做的就是调用 authenticate 方法。

```
val biometricPrompt = BiometricPrompt(...)
val promptInfo = BiometricPrompt.PromptInfo.Builder()...

biometricPrompt.authenticate(promptInfo) 
```

<figure>![](img/77edad426e81694703c2aa7498749548.png)

<figcaption>left: BiometricPrompt on Android 7 Nougat / right: BiometricPrompt on Android 9 Pie</figcaption>

</figure>

* * *

## 加密

在我看来，加密数据首先是使用生物认证的核心。现在我们有了一个有效的提示，我们应该研究一下。

**⚠️ *重要声明* ⚠️我不是深入的安全专家！代码，特别是选择的加密参数，是基于谷歌之前提到的[样本](https://github.com/googlesamples/android-FingerprintDialog)，只是适应了新的生物计量提示。**

### 键&密码

为了加密数据，我们需要一个**密钥**和一个**密码**。

```
fun createKey(): SecretKey {
  val algorithm = KeyProperties.KEY_ALGORITHM_AES
  val provider = "AndroidKeyStore"
  val keyGenerator = KeyGenerator.getInstance(algorithm, provider)

  val keyName = "MY_KEY"
  val purposes = KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT
  val keyGenParameterSpec = KeyGenParameterSpec.Builder(keyName, purposes)
          .setBlockModes(BLOCK_MODE)
          .setEncryptionPaddings(PADDING)
          .setUserAuthenticationRequired(true)
          .build()

  keyGenerator.init(keyGenParameterSpec)
  return keyGenerator.generateKey()
}

fun getEncryptCipher(key: Key): Cipher {
  val algorithm = KeyProperties.KEY_ALGORITHM_AES
  val blockMode = KeyProperties.BLOCK_MODE_CBC
  val padding = KeyProperties.ENCRYPTION_PADDING_PKCS7
  return Cipher.getInstance("$algorithm/$blockMode/$padding").apply { 
    init(Cipher.ENCRYPT_MODE, key) 
  }
} 
```

这里的主要内容是密钥创建期间的`setUserAuthenticationRequired(true)`方法调用:

> 设置该密钥是否仅在用户通过身份验证后才被授权使用。
> 
> 默认情况下，无论用户是否经过身份验证，该密钥都被授权使用。

这基本上意味着:要使用密钥，你必须提供一种形式的认证，即你的指纹。如需进一步解释，请参考 [Android 文档](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder#setUserAuthenticationRequired(boolean))🔍

由于没有指定密钥长度，Android 使用默认的密钥长度:对于 AES，它被设置为 128 位。有关 Android 上加密技术的更多信息，请参考[文档](https://developer.android.com/guide/topics/security/cryptography)🔍

### 使用密码器进行加密

要使用密码加密数据，只需调用它的`doFinal(byte[] input)`方法。为了以后的解密，我们需要保持密码的初始化向量。

```
val clearTextData: ByteArray
val encryptedData: ByteArray = cipher.doFinal(clearTextData)

// save encrypted data & init vector: i.e. in shared preferences
val dat = Base64.encodeToString(encryptedData, Base64.DEFAULT)
val iv  = Base64.encodeToString(cipher.iv, Base64.DEFAULT)
sharedPreferences.edit {
    putString(ENCRYPTED_DATA, dat)
    putString(INITIALIZATION_VECTOR, iv)
} 
```

## 解密

要解密加密数据，你需要

*   以前创建的 SecretKey
*   先前使用的初始化向量
*   解密密码

```
fun getKey(): Key? {
  val keyStore: KeyStore = KeyStore.getInstance("AndroidKeyStore").apply { 
    load(null) 
  }
  return keyStore.getKey("MY_KEY", null)
}

fun getDecryptCipher(key: Key, iv: ByteArray): Cipher {
  val algorithm = KeyProperties.KEY_ALGORITHM_AES
  val blockMode = KeyProperties.BLOCK_MODE_CBC
  val padding = KeyProperties.ENCRYPTION_PADDING_PKCS7
  return Cipher.getInstance("$algorithm/$blockMode/$padding").apply { 
    init(Cipher.DECRYPT_MODE, key, IvParameterSpec(iv)) 
  }
} 
```

### 使用密码器解密

这非常简单，类似于加密调用

```
val encryptedData: ByteArray = getEncryptedData()
val clearTextData: ByteArray = cipher.doFinal(encryptedData) 
```

* * *

## “叮叮”不同

就像我前面提到的，我不是一个深入的安全专家。对于像我(和其他人)这样的人来说，谷歌创造了 Tink，目的是使加密更简单，更难被滥用。

有了 Tink 的 Android 风格`com.google.crypto.tink:tink-android:1.2.2`，整个加密/解密可以分解成更简单、更容易理解的代码:

```
Config.register(TinkConfig.LATEST)

val keysetHandle = AndroidKeysetManager.Builder()
        .withSharedPref(activity, TINK_KEYSET_NAME, null)
        .withKeyTemplate(AeadKeyTemplates.AES256_GCM)
        .withMasterKeyUri(MASTER_KEY_URI)
        .build().keysetHandle

val aead = AeadFactory.getPrimitive(keysetHandle)
val encrypted = aead.encrypt("SECRET!".toByteArray(), null)
val cleartext = aead.decrypt(encrypted, null) 
```

我们之前看到的很多内部信息现在都被隐藏了:密码、初始化向量、加密参数等等。

不幸的是，之前讨论的标志`setUserAuthenticationRequired`没有设置，并且目前不可能将这个 AEAD(带有相关数据的认证加密)传递给生物计量提示进行“认证”。

为此，我在 Github 上创建了一个[问题。当有关于此事的消息时，我会写一篇后续文章。](https://github.com/google/tink/issues/211)

* * *

## 把事情联系在一起

现在我们有了一个工作提示，并了解了如何加密数据，我们可以将两者结合起来。只需将加密/解密所需的密码传递给生物计量提示:

```
val promptCrypto = BiometricPrompt.CryptoObject(cipher)
biometricPrompt.authenticate(promptInfo, promptCrypto) 
```

你传入的 CryptoObject 将被“解锁”(还记得我们在创建密钥时设置的标志)并被传递给之前看到的`AuthenticationCallback`
的`onAuthenticationSucceeded`回调方法

```
override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) {
    super.onAuthenticationSucceeded(result)
    result.cryptoObject?.cipher?.let { cipher ->
        // encrypt or decrypt data with cipher
    }
} 
```

### 处理错误

除了 succeeded 回调方法之外，还有另外两种针对失败尝试的回调方法:

`onAuthenticationFailed` *【当生物特征有效但未被识别时调用】*。这可能不是你需要解决的问题，因为提示符中已经实现了一个默认动画。

`onAuthenticationError` *"遇到不可恢复的错误且操作完成时调用。不会对该对象执行任何进一步的操作"*

错误回调方法提供了可以用来处理不同错误和显示反馈的`int errCode, CharSequence errString`。例如

```
override fun onAuthenticationError(errorCode: Int, 
                                   errorString: CharSequence) {
    super.onAuthenticationError(errorCode, errString)
    when (errorCode) {
        ...
        BiometricPrompt.ERROR_TIMEOUT -> // user fell asleep ;-)
        ...
    }
} 
```

参考 [Android 文档，查看所有可用的错误代码](https://developer.android.com/reference/android/hardware/biometrics/BiometricPrompt.html#BIOMETRIC_ERROR_CANCELED)。

### 为了方便而建造

不。尽管有很多不同的错误可以使用生物识别提示来处理，但我认为在大多数情况下，你只需要处理两种情况。加密/解密都有效👍或者没有👎

那么，为什么不实现一个支持类，为这两种情况提供方便，并封装我们之前讨论的所有内容。API 建议:

```
fun encryptPrompt(data: ByteArray,
                  failedAction: () -> Unit,
                  successAction: () -> Unit)

fun decryptPrompt(failedAction: () -> Unit,
                  successAction: (ByteArray) -> Unit) 
```

所以在`AuthenticationCallback`的`succeeded`和`error`回调中我们可以做到以下几点:

#### 加密大小写

```
fun onAuthenticationSucceeded(result: AuthenticationResult) {
    super.onAuthenticationSucceeded(result)
    result.cryptoObject?.cipher?.let { resultCipher ->
        val iv = resultCipher.iv
        val encryptedData = resultCipher.doFinal(data)
        saveEncryptedData(encryptedData, iv)
        activity.runOnUiThread { successAction() }
    }
}

fun onAuthenticationError(errCode: Int, errStr: CharSequence) {
    super.onAuthenticationError(errCode, errStr)
    Log.d(TAG, "Authentication error. $errStr ($errCode)")
    activity.runOnUiThread { failedAction() }
} 
```

#### 解密案例

```
fun onAuthenticationSucceeded(result: AuthenticationResult) {
    super.onAuthenticationSucceeded(result)
    result.cryptoObject?.cipher?.let { cipher ->
        val encrypted = getEncryptedData()
        val decryptedData = cipher.doFinal(encrypted)
        activity.runOnUiThread { successAction(decryptedData) }
    }
}

fun onAuthenticationError(errCode: Int, errStr: CharSequence) {
    super.onAuthenticationError(errCode, errStr)
    Log.d(TAG, "Authentication error. $errStr ($errCode)")
    activity.runOnUiThread { failedAction() }
} 
```

### 用法

这将使您能够方便地使用生物识别提示:

```
val promptManager = BiometricPromptManager(fragmentActivity)
promptManager.encryptPrompt(
    data = secureText.toByteArray(),
    failedAction = { showToast("encrypt failed") },
    successAction = { showToast("encrypt success") }
)

promptManager.decryptPrompt(
    failedAction = { showToast("decrypt failed") },
    successAction = { showToast("decrypt success: $it") }
) 
```

### 替代品

当然，如果你需要更详细的错误处理或者不喜欢这种风格，你也可以

*   使用自定义响应代码和/或在一个回调方法中完成所有工作
*   添加不同的回调操作(针对不同的错误)
*   将错误代码传递给 failedAction

* * *

## 问题

不幸的是，我在 Android 8 和更早版本的一个片段中使用 AndroidX 生物识别提示时遇到了以下问题。

```
java.lang.IllegalStateException: FragmentManager is already executing transactions 
```

据 Stackoverflow 上的其他人说，这个问题似乎是在最新的 alpha04 版本中引入的

*   [https://stack overflow . com/questions/55683300/Android-crashed-after-updating-androidx-biometric-to-1-0-0-alpha 04](https://stackoverflow.com/questions/55683300/android-crashed-after-updating-androidx-biometric-to-1-0-0-alpha04)
*   [https://stack overflow . com/questions/55934108/fragment manager-is-already-executing-transactions-when-executing-biometric prompt](https://stackoverflow.com/questions/55934108/fragmentmanager-is-already-executing-transactions-when-executing-biometricprompt)

要“解决”这个问题:您可以使用处理程序人工延迟调用，或者恢复到 alpha03 版本。

```
Handler().postDelayed({
        // launch biometric prompt for Fragment 
}, 0) 
```

* * *

完整的代码可以在 Github 上找到:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[安卓-样本-生物识别-提示](https://github.com/stravag/android-sample-biometric-prompt)

### 生物识别提示示例应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# 安卓-样本-生物识别-提示

生物识别提示示例应用程序

</article>

[View on GitHub](https://github.com/stravag/android-sample-biometric-prompt)

编码快乐！

* * *

[![](img/1727c187ca740efdac8522e2f3b1985a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1VdXQu0f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iu22ph3ksl2bsooujzi7.gif)