# 使用。pfx 和。中的安全 cer。网

> 原文：<https://dev.to/praneetnadkar/using-pfx-and-cer-for-security-in-net-2clp>

安全已经成为我们生活中不可或缺的一部分。我们首先倾向于检查手机、家里、银行账户等等的安全性。软件也是如此。

安全性是任何软件开发都要考虑的一个要点。要在应用程序中实现任何安全性，有许多方法可以实现。其中一个最常见的术语是“密码学”。根据 wiki,“密码学是对安全通信技术的实践和研究”。这种安全通信的实践可以通过许多方法或算法来实现。在 crypto 中要实现的一个基本功能是加密。

在密码术中，加密是对消息或信息进行编码的过程，其编码方式使得只有授权方才能访问它。加密本身并不能防止干扰，但会阻止潜在的拦截者获取可理解的内容。有许多算法可以实现这一点。一种方法是使用认证的公钥和私钥。

让我解释一下我们将在这里看到的两种文件格式。[链接](https://stackoverflow.com/questions/2292495/what-is-the-difference-between-a-cer-pvk-and-pfx-file)

## 什么是 PFX？

在密码学中，PKCS #12 定义了一种存档文件格式(. p12 或。pfx ),用于将许多加密对象存储为一个文件。它通常用于将私钥与其 X.509 证书捆绑在一起，或者捆绑信任链的所有成员。Windows 使用。PKCS #12 文件的 pfx。该文件可以包含各种加密信息，包括证书、证书链、根机构证书和私钥。它的内容可以用密码保护(用密码)，以保持私钥的私密性，并保持根证书的完整性。

## 什么是 CER？

Windows 使用。X.509 证书的 cer 扩展。这些可以是“二进制的”(ASN.1 DER)，或者可以用 Base-64 编码并应用页眉和页脚(PEM)；Windows 将识别这两种情况。为了验证证书的完整性，您必须使用发行者的公钥来检查其签名……这又是另一个证书。

## 使用 pfx 和 cer。网络应用

我一直在使用这些文件对一些 web 服务中的数据进行加密和解密。可以使用 Windows 操作系统中的实用程序生成 pfx 和 vcer 文件。然而，如果有某种方法，我们可以通过编程来自动化这一点，那就太好了。

为此，我使用了一个 [API 弹力城堡](https://www.bouncycastle.org/)。这简直是这些家伙的惊人之作。

在 C#中，我创建了一个控制台应用程序，并在项目中安装了 bouncy castle。要安装 nuget 包，请使用[Install-Package bouncy castle-Version 1 . 8 . 1](https://www.nuget.org/packages/BouncyCastle/)

安装完成后，您可以使用下面的代码创建一个 pfx 文件。

```
using Data.Helpers;
using Org.BouncyCastle.Asn1;
using Org.BouncyCastle.Asn1.X509;
using Org.BouncyCastle.Crypto;
using Org.BouncyCastle.Crypto.Generators;
using Org.BouncyCastle.Crypto.Operators;
using Org.BouncyCastle.Crypto.Prng;
using Org.BouncyCastle.Math;
using Org.BouncyCastle.Security;
using Org.BouncyCastle.X509;
using System;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates;

namespace CreateKeyHolders
{
 public static class CertificateBuilder
 {
 /// <summary>
 /// Creates the certificate based on the subject name
 /// </summary>
 /// <param name="certificate">The certificate object with custom values for the certificate</param>
 /// <returns></returns>
 public static bool GeneratePfx(CertificateAttributes certificate)
 {
 var isCertificateCreated = false;
 try
 {
 var keyPairGenerator = new RsaKeyPairGenerator();
 keyPairGenerator.Init(new KeyGenerationParameters(new SecureRandom(new CryptoApiRandomGenerator()), 1024));
 var kp = keyPairGenerator.GenerateKeyPair();
 var signatureFactory = new Asn1SignatureFactory(certificate.Algorithm.GetDescription(), kp.Private);
 var gen = new X509V3CertificateGenerator();
 var certName = new X509Name("CN=" + certificate.SubjectName);
 var serialNo = BigInteger.ProbablePrime(120, new Random());
 //sets the serial no for the certificate : add a custom serial no if you have one
 gen.SetSerialNumber(serialNo);
 //set the subject name : can be custom here
 gen.SetSubjectDN(certName);
 //sets the issuer name
 gen.SetIssuerDN(certName);
 gen.SetNotAfter(DateTime.Now.AddYears(100));
 gen.SetNotBefore(DateTime.Now.Subtract(new TimeSpan(7, 0, 0, 0)));
 //set the public key for the certificate
 gen.SetPublicKey(kp.Public);

gen.AddExtension(
 X509Extensions.AuthorityKeyIdentifier.Id,
 false,
 new AuthorityKeyIdentifier(
 SubjectPublicKeyInfoFactory.CreateSubjectPublicKeyInfo(kp.Public),
 new GeneralNames(new GeneralName(certName)),
 serialNo));

gen.AddExtension(
 X509Extensions.ExtendedKeyUsage.Id,
 false,
 new ExtendedKeyUsage(new List { new DerObjectIdentifier("1.3.6.1.5.5.7.3.1") }));

var newCert = gen.Generate(signatureFactory);
 var createdCertificate = DotNetUtilities.ToX509Certificate(newCert);
 var byteArray = createdCertificate.Export(X509ContentType.Pkcs12, certificate.Password);
 var byteArray1 = createdCertificate.Export(X509ContentType.Cert, certificate.Password);

//Save the pfx file here
 Helper.WriteAllBytes("C:\\certs\\" + certificate.SubjectName + ".pfx", byteArray);

//save the cert here
 if (certificate.SaveCerFile)
 Helper.WriteAllBytes("C:\\certs\\" + certificate.SubjectName + ".cer", byteArray1);

isCertificateCreated = true;
 }
 catch (Exception exception)
 {
 var logger = new Logger.FileLogger();
 logger.Log(exception);
 }

return isCertificateCreated;
 }
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

证书的属性如主题名称、发行者名称、序列号是定义证书的属性。请非常谨慎地使用这些值。

该代码的用法如下:

```
var result = CertificateBuilder.GeneratePfx("appCert", "passowrd"); 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 pfx 文件和一个 cer 文件，文件名为 appcert，密码在参数中提到。我们现在可以在应用程序中使用这些创建的文件来加密和解密数据。

我们现在将了解如何使用这些文件来实现安全性。

您可以为此使用或修改以下代码:

```
class Program
 {
 static void Main()
 {
 //var result = CertificateBuilder.GeneratePfx("test", "test@1234");

const string DecryptCertPath = @"E:\.pfx";
 const string EncryptCertPath = @"E:\.cer";

const string input = "The quick brown fox jumps over a lazy dog.";
 var array = Helper.GetBytes(input);
 var ecertificate2 = ReadPfx.GetCertificate(EncryptCertPath);
 var encrypted = Encrypt.EncryptData(array, ecertificate2);
 var encryptedbase64 = Convert.ToBase64String(encrypted);
 Console.WriteLine(encryptedbase64);
 var decryptedArray = Convert.FromBase64String(encryptedbase64);
 var dcertificate2 = ReadPfx.GetCertificate(DecryptCertPath);
 var decrypted = Decrypt.DecryptData(encrypted, dcertificate2);
 var decryptedString = Helper.GetStringFromByte(decrypted);
 Console.WriteLine(Environment.NewLine);
 Console.WriteLine(decryptedString);
 Console.WriteLine("==================================================");
 Console.ReadKey();
 }
 } 
```

Enter fullscreen mode Exit fullscreen mode