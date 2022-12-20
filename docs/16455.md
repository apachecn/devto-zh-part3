# 在 C#中使用 EmailValidation

> 原文：<https://dev.to/praneetnadkar/using-emailvalidation-in-c-4n22>

# 在 C 中使用 EmailValidation

有一天我在做电子邮件验证，我看到了这个漂亮的 nuget 包，叫做 EmailValidation。

所以这里是到它的链接。

现在， [MSDN 博客](https://blogs.msdn.microsoft.com/testing123/2009/02/06/email-address-test-cases/)页面有一个简短的列表，上面有几个电子邮件地址，可以用来验证这一点。

我只是复制并检查了一下。这个包很好用！

所以如果有人在寻找这个，试试这个。

```
static void Main()
{

var listOfValidEmails = new List<string>
{
“email@domain.com”,
“firstname.lastname@domain.com”,
“email@subdomain.domain.com”,
“firstname+lastname@domain.com”,
“email@123.123.123.123”,
“email@[123.123.123.123]”,
“\”email\”@domain.com”,
“1234567890@domain.com”,
“email@domain-one.com”,
“_______@domain.com”,
“email@domain.name”,
“email@domain.co.jp”,
“firstname-lastname@domain.com”
};

var listOfInvalidEmails = new List<string>
{
“plainaddress”,
“#@%^%#$@#$@#.com”,
“@domain.com”,
“Joe Smith <email@domain.com>”,
“email.domain.com”,
“email@domain@domain.com”,
“.email@domain.com”,
“email.@domain.com”,
“email..email@domain.com”,
“あいうえお@domain.com”,
“email@domain.com (Joe Smith)”,
“email@domain”,
“email@-domain.com”,
“email@domain.web”,
“email@111.222.333.44444”,
“email@domain..com”,
};

foreach (var email in listOfValidEmails)
{
Console.WriteLine($”{email} is {(EmailValidator.Validate(email) ? “valid” : “invalid”)}!”);
}

Console.WriteLine(“==============================================================================”);

foreach (var email in listOfInvalidEmails)
{
Console.WriteLine($”{email} is {(EmailValidator.Validate(email) ? “valid” : “invalid”)}!”);
}

Console.ReadKey();
} 
```

Enter fullscreen mode Exit fullscreen mode

使用起来更快，就像导入一个 nuget，你就可以开始了。在我看来，这涵盖了大多数电子邮件验证的场景。

我想尝试的一件有趣的事情是对所有这些使用正则表达式，然后尝试使用模式匹配来检查两件事。一是所有的电子邮件场景都包括在内，二是哪种方法更快。