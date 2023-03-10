# 如何将库链接到 sol-编译器生成的 Solidity 契约中

> 原文：<https://dev.to/liamzebedee/how-to-link-libraries-into-solidity-contracts-generated-by-sol-compiler-34b3>

Solidity 智能契约从源代码生成为字节码。然而，很像以前的 C 语言时代，**链接其他可能已经部署的库中的**有时是必要的。

当使用 Truffle 或 sol-compiler 生成 Solidity 工件时，有一个名为`bytecode`或`deployedBytecode`的字段。如果您有未链接的库并试图部署这些代码，您将得到类似于**无效契约字节码**的错误:

```
{ Error: invalid contract bytecode (arg="bytecode", value="0x608060405260016004554360075543600__$e66798aa9224cd2742272d2e7f8089dbe6$__ffffffffffffffffffffffffffffffffffffffff1660601b81526014018281526020019350505050604051602081830303815290604052805190602001209050939250505056fea165627a7a72305820fdb2e9c02d3de9057e4d439bc377d8c8ae842b83649e495e3303688bfe6f7e930029", version=4.0.26) 
```

如果您更深入地研究，您会发现有一个看起来非常不像十六进制的字符`$`，它是一个链接引用的开始。链接引用本身是一个字符串`__$e66798aa9224cd2742272d2e7f8089dbe6$__`。这曾经有一个更容易理解的名字，比如`____________________$Library$_`，但是它被改成了一个更神秘的算法。 [libraryHashPlaceholder](https://sourcegraph.com/github.com/ethereum/solc-js@40b8fc3e0b5f80b6629d6f9c4d7d19c190e6c388/-/blob/linker.js#L7) 方法创建了这种格式，但是我们给它的输入是什么呢？它的形式是`libraryName:lib`。

例如，我的`MerkleTreeVerifier`库的名字是:

```
libraryHashPlaceholder('/Users/liamz/Documents/open-source/0dex/packages/contracts/contracts/MerkleTreeVerifier.sol:MerkleTreeVerifier')
// '$1eb98b648b444978ea3820de6fcdeb48d6$' 
```

## 与 sol 编译器集成

现在，对于 0x 编译器，您需要做两件事。请注意，我还使用了 [abi-gen](https://www.npmjs.com/package/@0xproject/abi-gen) 来生成我们的契约的 TypeScript 包装。

1)启用`compiler.json`中的`metadata`选项。例如，这是我的:

```
{  "contractsDir":  "contracts",  "artifactsDir":  "build/artifacts",  "contracts":  "*",  "compilerSettings":  {  "optimizer":  {  "enabled":  false  },  "outputSelection":  {  "*":  {  "*":  [  "metadata",  "abi",  "evm.bytecode.object",  "evm.bytecode.sourceMap",  "evm.deployedBytecode.object",  "evm.deployedBytecode.sourceMap"  ]  }  }  }  } 
```

2)添加您的库并链接它们。

```
export function addLibrary(name: string, address: string) {
    let artifact = require(`@ohdex/contracts/lib/build/artifacts/${name}.json`);
    let metadata = JSON.parse(artifact.compilerOutput.metadata)

    let [k,v] = Object.entries(metadata.settings.compilationTarget)[0];
    let key = `${k}:${v}`;

    console.log(`Registered library ${name} to ${key}`)

    libraries[key] = address;
}

// Deployment of libraries
// Note that if the library already exists, we just add the address instead
// ....

// Your sol-compiler generated wrapper
import { MerkleTreeVerifierContract } from '@ohdex/contracts/lib/build/wrappers/merkle_tree_verifier';

let merkleTreeVerifier = await MerkleTreeVerifierContract.deployAsync(
    ...getDeployArgs('MerkleTreeVerifier', pe, account)
)
addLibrary('MerkleTreeVerifier', merkleTreeVerifier.address)

// Linking libraries to your own contracts
let json = require(`@ohdex/contracts/lib/build/artifacts/${name}.json`);
let bytecode = json.compilerOutput.evm.bytecode.object;
bytecode = linker.linkBytecode(bytecode, libraries) 
```

还有 tada！您已经链接了您的库并部署了您的合同！