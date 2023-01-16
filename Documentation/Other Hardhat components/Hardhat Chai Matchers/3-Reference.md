这是 Hardhat Chai Matchers 插件的参考。 这是一个 [chai](https://www.chaijs.com/) 插件，它添加了用于测试智能合约的新断言功能。

### Numbers
当使用@nomicfoundation/hardhat-chai-matchers 时，即使数字由不同类型表示，数字的相等比较也会起作用。 这意味着像这样的断言：
```
expect(await token.totalSupply()).to.equal(1_000_000);
```
将工作。 这些断言通常不起作用，因为 totalSupply() 返回的值是一个 [ethers BigNumber](https://docs.ethers.io/v5/single-page/#/v5/api/utils/bignumber/)，而 BigNumber 的实例将始终不同于普通数字。

支持的类型有：
- 纯 javascript 数字
- [BigInts](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
- [Ethers BigNumbers](https://docs.ethers.io/v5/single-page/#/v5/api/utils/bignumber/)
- [bn.js](https://github.com/indutny/bn.js/) 实例
- [bignumber.js](https://github.com/MikeMcl/bignumber.js/) 实例

当执行数组或对象的深度相等比较时，这也适用：
```
expect(await contract.getRatio()).to.deep.equal([100, 55]);
```

### Reverted transactions
包含几个匹配器来断言交易已恢复，以及恢复的原因。

`.reverted`
断言交易因任何原因被恢复，而不检查恢复的原因：
```
await expect(token.transfer(address, 0)).to.be.reverted;
```
`.revertedWith`
断言事务已使用特定原因字符串恢复：
```
await expect(token.transfer(address, 0)).to.be.revertedWith(
  "transfer value must be positive"
);
```
您还可以使用正则表达式：
```
await expect(token.transfer(address, 0)).to.be.revertedWith(
  /AccessControl: account .* is missing role .*/
);
```
`.revertedWithCustomError`
断言交易因特定的[custom error](https://docs.soliditylang.org/en/v0.8.14/contracts.html#errors-and-the-revert-statement)而恢复：
```
await expect(token.transfer(address, 0)).to.be.revertedWithCustomError(
  token,
  "InvalidTransferValue"
);
```
第一个参数必须是定义错误的contract。

如果错误有参数，可以添加 .withArgs 匹配器：
```
await expect(token.transfer(address, 0))
  .to.be.revertedWithCustomError(token, "InvalidTransferValue")
  .withArgs(0);
```
见[.withArgs](https://hardhat.org/hardhat-chai-matchers/docs/reference#.withargs)匹配器条目以了解更多信息。

`.revertedWithPanic`
断言事务已使用[panic code](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require)恢复：
```
await expect(token.transfer(address, 0)).to.be.revertedWithPanic();
```
可以传递一个可选参数来断言抛出了特定的panic code：
```
await expect(token.transfer(address, 0)).to.be.revertedWithPanic(0x12);
```
您还可以导入和使用 `PANIC_CODES` 字典：
```
import { PANIC_CODES } from "@nomicfoundation/hardhat-chai-matchers/panic";

await expect(token.transfer(address, 0)).to.be.revertedWithPanic(
  PANIC_CODES.DIVISION_BY_ZERO
);
```
`.revertedWithoutReason`
断言交易在没有返回原因的情况下被恢复：
```
await expect(token.transfer(address, 0)).to.be.revertedWithoutReason();
```
此匹配器与 `.reverted` 的不同之处在于，如果事务以原因字符串、custom error or panic code恢复，它将失败。 在 Solidity v0.8.0 之前，无故恢复的 Solidity 表达式的示例是 require(false)（没有原因字符串）和 assert(false)。 这也发生在气体不足的错误上。

###  Events
`.emit`
断言事务发出特定事件：
```
await expect(token.transfer(address, 100)).to.emit(token, "Transfer");
```
第一个参数必须是发出事件的合约。

如果事件有参数，可以添加 `.withArgs` 匹配器：
```
await expect(token.transfer(address, 0))
  .to.be.revertedWithCustomError(token, "InvalidTransferValue")
  .withArgs(100);
```
见[.withArgs](https://hardhat.org/hardhat-chai-matchers/docs/reference#.withargs)匹配器条目以了解更多信息。

### Balance change
这些匹配器可用于断言给定交易如何影响特定地址的以太币余额或 ERC20 代币余额。

所有这些匹配器都假设给定的交易是在其区块中挖掘的唯一交易。

`.changeEtherBalance`
断言地址的以太币余额发生了特定数量的变化：
```
await expect(
  sender.sendTransaction({ to: receiver, value: 1000 })
).to.changeEtherBalance(sender, -1000);
```
此匹配器忽略交易费用，但您可以将它们包含在 `includeFee` 选项中：
```
await expect(
  sender.sendTransaction({ to: receiver, value: 1000 })
).to.changeEtherBalance(sender, -22000, { includeFee: true });
```
`.changeTokenBalance`
断言某个地址的 ERC20 代币余额发生了特定数量的变化：
```
await expect(token.transfer(receiver, 1000)).to.changeTokenBalance(
  token,
  sender,
  -1000
);
```
第一个参数必须是代币的合约。
`.changeEtherBalances`
类似于 .changeEtherBalance，但允许同时检查多个地址：
```
await expect(
  sender.sendTransaction({ to: receiver, value: 1000 })
).to.changeEtherBalances([sender, receiver], [-1000, 1000]);
```
`.changeTokenBalances`
类似于 .changeTokenBalance，但允许同时检查多个地址：
```
await expect(token.transfer(receiver, 1000)).to.changeTokenBalances(
  token,
  [sender, receiver],
  [-1000, 1000]
);
```
### Other matchers
`.withArgs`
可以在 `.emit` 或 `.revertedWithCustomError` 匹配器之后使用以断言event/error's参数：
```
// events
await expect(token.transfer(address, 0))
  .to.be.revertedWithCustomError(token, "InvalidTransferValue")
  .withArgs(100);

// errors
await expect(token.transfer(address, 0))
  .to.be.revertedWithCustomError(token, "InvalidTransferValue")
  .withArgs(0);
```
如果您不关心其中一个参数的值，则可以使用 anyValue 谓词：
```
import { anyValue } from "@nomicfoundation/hardhat-chai-matchers/withArgs";

await expect(factory.create(9999))
  .to.emit(factory, "Created")
  .withArgs(anyValue, 9999);
```
谓词只是函数，如果值正确则返回 true，否则返回 false，因此您可以创建自己的谓词：
```
function isEven(x: BigNumber): boolean {
  return x.mod(2).isZero();
}

await expect(token.transfer(100)).to.emit(token, "Transfer").withArgs(isEven);
```
`.properAddress`
断言给定的字符串是一个正确的地址：
```
expect("0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266").to.be.properAddress;
```
`.properPrivateKey`
断言给定的字符串是正确的私钥：
```
expect("0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80").to
  .be.properPrivateKey;
```
`.properHex`
断言给定的字符串是一个特定长度的适当的十六进制字符串：
```
expect("0x1234").to.be.properHex(4);
```
`.hexEqual`
断言给定的字符串十六进制字符串对应于相同的数值：
```
expect("0x00012AB").to.hexEqual("0x12ab");
```