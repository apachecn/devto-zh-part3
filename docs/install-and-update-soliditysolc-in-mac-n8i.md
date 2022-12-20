# 在 Mac 中安装和更新 Solidity(solc)

> 原文：<https://dev.to/sot528/install-and-update-soliditysolc-in-mac-n8i>

您可以安装和更新 solc:

```
brew update
brew upgrade
brew tap ethereum/ethereum
brew install solidity
brew link --overwrite solidity 
```

# 错误

如果你得到了下面的错误，你必须更新 xcode。

```
==> Installing solidity from ethereum/ethereum
Error: Your Xcode (9.0) is too outdated.
Please update to Xcode 10.1 (or delete it).
Xcode can be updated from the App Store. 
```