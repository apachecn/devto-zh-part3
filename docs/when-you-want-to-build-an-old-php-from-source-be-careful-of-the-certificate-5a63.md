# 当你想从源代码构建一个旧的 PHP 时，要小心证书！

> 原文：<https://dev.to/masatana/when-you-want-to-build-an-old-php-from-source-be-careful-of-the-certificate-5a63>

最近我需要从源代码中构建一个旧的 PHP (php-5.6.34)。
不知何故，我可以`configure`使用 openssl，并`make`使用它。

然后我只是键入`make test`来检查构建的结果，我得到了下面的错误:

```
=====================================================================
FAILED TEST SUMMARY
---------------------------------------------------------------------
Bug #48182: ssl handshake fails during asynchronous socket connection [ext/openssl/tests/bug48182.phpt]
Bug #54992: Stream not closed and error not returned when SSL CN_match fails [ext/openssl/tests/bug54992.phpt]
Bug #65538: SSL context "cafile" supports stream wrappers [ext/openssl/tests/bug65538_001.phpt]
Bug #65538: SSL context "cafile" supports phar wrapper [ext/openssl/tests/bug65538_003.phpt]
capture_peer_cert context captures on verify failure [ext/openssl/tests/capture_peer_cert_001.phpt]
Testing peer fingerprint on connection [ext/openssl/tests/openssl_peer_fingerprint_basic.phpt]
Peer verification enabled for client streams [ext/openssl/tests/peer_verification.phpt]
Capture SSL session meta array in stream context [ext/openssl/tests/session_meta_capture.phpt]
Basic bitwise stream crypto context flag assignment [ext/openssl/tests/stream_crypto_flags_001.phpt]
TLSv1.1 and TLSv1.2 bitwise stream crypto flag assignment [ext/openssl/tests/stream_crypto_flags_002.phpt]
Server bitwise stream crypto flag assignment [ext/openssl/tests/stream_crypto_flags_003.phpt]
Specific protocol method specification [ext/openssl/tests/stream_crypto_flags_004.phpt]
Allow host name mismatch when "verify_host" disabled [ext/openssl/tests/stream_verify_peer_name_002.phpt]
Host name mismatch triggers error [ext/openssl/tests/stream_verify_peer_name_003.phpt]
===================================================================== 
```

我刚刚构建了源代码，为什么会有这么多错误？

挖掘源代码，然后我注意到上面所有的测试代码都使用了`bug54992-ca.pem`证书。这用于测试 SSL/TLS 连接。

是的，`bug54992-ca.pem`太老了，没效果！

这已经解决了。我找到了 PHP 源码的上游补丁；[https://github . com/PHP/PHP-src/commit/687 dad 3674 e 9 af 12 CEC 914 BDC 250 a 157680076 ca](https://github.com/php/php-src/commit/687dad3674e9af12cec914bdc250a157680076ca)

手动挑选补丁后，上述测试错误消失了。