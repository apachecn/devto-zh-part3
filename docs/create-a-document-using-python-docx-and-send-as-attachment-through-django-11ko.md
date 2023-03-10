# 使用 python-docx 创建一个文档，并通过 django 作为附件发送

> 原文：<https://dev.to/bharathbk/create-a-document-using-python-docx-and-send-as-attachment-through-django-11ko>

我使用 docx 创建了一个文档，并试图以电子邮件附件的形式发送，但没有将文档保存在服务器上。以下是我的代码:

```
Document = document()
paragraph = document.add_paragraph("Test Content")
f = BytesIO()
document.save(f)
file_list = []
file_list.append(["Test.docx",f, "application/vnd.openxmlformats-officedocument.wordprocessingml.document"]
email = EmailMessage(subject = 'Test', body = 'Hi', to = ['test@test.com'], attachments = file_list)
email.send() 
```

我得到以下错误:

> TypeError:应为类似字节的对象，而不是 BytesIO

在线上`email.send()`

我试着把 BytesIO 转换成 StringIO，就像这里提到的

```
f = f.read()
f = StringIO(f.decode('UTF-8')) 
```

然后我得到了错误:

> TypeError:应为类似字节的对象，而不是 StringIO

我看了看来自[这个](https://stackoverflow.com/questions/46215083/create-word-document-and-then-attach-it-to-email-django)的解决方案，但是不明白`document`是如何作为附件发送的。

感谢任何帮助或指点。

谢谢！