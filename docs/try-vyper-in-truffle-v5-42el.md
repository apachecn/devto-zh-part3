# 试试松露 v5 的 Vyper。

> 原文：<https://dev.to/sot528/try-vyper-in-truffle-v5-42el>

[松露 v5](https://truffleframework.com/blog/truffle-v5-has-arrived) 支持[薇儿](https://vyper.readthedocs.io/en/latest/index.html)。
我做了 [Docker image](https://hub.docker.com/r/sot528/truffle-vyper-test) 来试一试。( [Dockerfile](https://github.com/sot528/truffle-vyper-test) )

# 如何使用

```
docker run -it sot528/truffle-vyper-test 
```

# [容器中的](#in-the-container)

它拆箱 [vyper-example-box](https://github.com/truffle-box/vyper-example-box) 。所以可以用下面。

```
$ truffle test Using network 'test'.

  Contract: VyperStorage
    ✓ ...should store the value 89\. (114ms)

  1 passing (129ms) 
```

它工作了。
通过编辑`./contracts/VyperStorage.vy`和`truffle compile`，你可以尝试任何事情。

```
stored_data: uint256

@public
def set(new_value : uint256):
    self.stored_data = new_value

@public
@constant
def get() -> uint256:
    return self.stored_data 
```

测试文件是`./test/vyperStorage.js`。

```
const VyperStorage = artifacts.require("VyperStorage");

contract("VyperStorage", () => {
  it("...should store the value 89.", async () => {
    const storage = await VyperStorage.deployed();

    // Set value of 89
    await storage.set(89);

    // Get stored value
    const storedData = await storage.get();

    assert.equal(storedData, 89, "The value 89 was not stored.");
  });
}); 
```

如果您看到下面的错误，那么您应该删除`build`中的所有文件。

```
Error parsing /code/contracts/VyperStorage.vy: ParsedContract.sol:1:1: ParserError: Expected pragma, import directive or contract/interface/library definition.
stored_data: uint256
^---------^
Compilation failed. See above.
Truffle v5.0.0 (core: 5.0.0) 
```