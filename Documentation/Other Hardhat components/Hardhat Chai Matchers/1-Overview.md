## 概述
@nomicfoundation/hardhat-chai-matchers为Chai断言库添加了特定于以太坊的功能，使您的智能合约测试易于编写和阅读。

除此之外，您可以断言合约触发了某些事件，或者它显示了特定的恢复，或者交易导致钱包的以太币或令牌余额发生了特定的变化。

### Installation
```
npm install --save-dev @nomicfoundation/hardhat-chai-matchers
```
### How can I use it?
只需在Hardhat配置中`require("@nomicfoundation/ Hardhat -chai-matchers")`，然后断言将在代码中可用。

必须显式地导入其他一些帮助程序，例如参数谓词和panic代码常量。下面将讨论这些问题。

### Why would I want to use it?
#### Events
您可以轻松编写测试来验证您的合约是否发出了特定事件。 例如，`await expect(contract.call()).to.emit(contract, "Event")` 将检测以下 Solidtity 代码发出的事件：
```
contract C {
    event Event();
    function call () public {
        emit Event();
    }
}
```
请注意，在 `expect(...).to.emit(...)` 之前需要`await`，因为验证需要从以太坊节点检索事件日志，这是一个异步操作。 如果没有最初的`await`，您的测试可能会在以太坊交易完成之前运行完成。

另请注意， `emit()` 的第一个参数是发出事件的合约。 如果你的合约调用了另一个合约，而你想检测来自内部合约的事件，你需要将内部合约传递给 `emit()`。

##### Events with Arguments
Solidity 事件可以包含参数，并且您可以在发出的事件中断言某些参数值的存在。 例如，断言事件发出某个无符号整数值：
```
await expect(contract.call())
  .to.emit(contract, "Uint")
  .withArgs(3);
```

有时您可能想要断言事件的第二个参数的值，但您想要允许第一个参数的任何值。 使用 withArgs 这很容易，因为它不仅支持特定值，还支持谓词。 例如，跳过检查第一个参数但断言第二个参数的值：
```
const { anyValue } = require("@nomicfoundation/hardhat-chai-matchers/withArgs");
await expect(contract.call())
  .to.emit(contract, "TwoUints")
  .withArgs(anyValue, 3);
```
谓词只是简单的函数，在调用时指示值是否应被视为成功匹配。 该函数将接收该值作为其输入，但不需要使用它。 例如，`anyValue` 谓词就是 `() => true`。

此包提供谓词 anyValue 和 anyUint，但您可以轻松创建自己的谓词：
```
function isEven(x: BigNumber): boolean {
  return x.mod(2).isZero();
}

await expect(contract.emitUint(2))
  .to.emit(contract, "Uint")
  .withArgs(isEven);
```
#### Reverts
您还可以轻松编写测试来断言合同调用是否已恢复（或未恢复）以及恢复时预期的错误数据类型。

最简单的情况断言发生了还原：
```
await expect(contract.call()).to.be.reverted;
```
或者，相反，那个没有：
```
await expect(contract.call()).not.to.be.reverted;
```
还原还可能包含一些错误数据，例如字符串、[panic code](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require)或[custom error](https://docs.soliditylang.org/en/v0.8.14/contracts.html#errors-and-the-revert-statement)，并且此包为所有这些提供了匹配器。

`revertedWith` 匹配器允许您断言还原的错误数据是否匹配特定字符串：
```
await expect(contract.call()).to.be.revertedWith("Some revert message");
await expect(contract.call()).not.to.be.revertedWith("Another revert message");
```
`revertedWithPanic` 匹配器允许您断言是否发生了特定panic code的恢复。 您可以通过其整数值（包括通过十六进制表示法，例如 `0x12`）或通过从此包导出的 `PANIC_CODES` 字典匹配紧急代码：
```
const { PANIC_CODES } = require("@nomicfoundation/hardhat-chai-matchers/panic");

await expect(contract.divideBy(0)).to.be.revertedWithPanic(
  PANIC_CODES.DIVISION_BY_ZERO
);

await expect(contract.divideBy(1)).not.to.be.revertedWithPanic(
  PANIC_CODES.DIVISION_BY_ZERO
);
```
您可以省略panic code以断言事务已使用任何panic code恢复。

`revertedWithCustomError` 匹配器允许您断言事务因特定[custom code](https://docs.soliditylang.org/en/v0.8.14/contracts.html#errors-and-the-revert-statement)而恢复：
```
await expect(contract.call()).to.be.revertedWithCustomError(
  contract,
  "SomeCustomError"
);
```
与事件一样，此匹配器的第一个参数必须指定定义自定义错误的协定。 如果您预计对不同合约的嵌套调用会出错，那么您需要将该不同合约作为第一个参数传递。

此外，正如事件可以有参数一样，自定义错误对象也可以，而且，就像事件一样，您可以断言这些参数的值。 为此，请使用相同的 `.withArgs()` 匹配器和相同的谓词系统：
```
await expect(contract.call())
  .to.be.revertedWithCustomError(contract, "SomeCustomError")
  .withArgs(anyValue, "some error data string");
```
最后，您可以断言调用在没有任何错误数据的情况下恢复（既不是原因字符串，也不是恐慌代码，也不是自定义错误）：
```
await expect(contract.call()).to.be.revertedWithoutReason();
```
#### Big Numbers
由于以太坊的 256 位本机整数大小，在 JavaScript 中使用以太坊智能合约可能会很烦人。 返回整数值的合约可能会产生大于 JavaScript 的最大安全整数值的数字，如果不事先熟悉 web3 框架使用的第 3 方大整数库，就很难编写关于此类值期望的断言。

该包增强了标准数值相等匹配器（`equal`, `above`, `within`等），以便您可以无缝地混合和匹配合同返回值与常规数字。 例如：
```
expect(await token.balanceOf(someAddress)).to.equal(1);
```
这些匹配器不仅支持[ethers' BigNumber](https://docs.ethers.io/v5/single-page/#/v5/api/utils/bignumber/)和原生的 JavaScript `Number`，还有[BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)、[bn.js](https://github.com/indutny/bn.js/) 和 [bignumber.js](https://github.com/MikeMcl/bignumber.js/)。

#### Balance Changes
通常，您正在测试的交易预计会对钱包的余额产生一些影响，无论是以太币余额还是某些 ERC-20 代币余额。 另一组匹配器允许您验证交易是否导致了这种余额变化：
```
await expect(() =>
  sender.sendTransaction({ to: someAddress, value: 200 })
).to.changeEtherBalance(sender, "-200");

await expect(token.transfer(account, 1)).to.changeTokenBalance(
  token,
  account,
  1
);
```
此外，您还可以同时检查多个地址的这些条件：
```
await expect(() =>
  sender.sendTransaction({ to: receiver, value: 200 })
).to.changeEtherBalances([sender, receiver], [-200, 200]);

await expect(token.transferFrom(sender, receiver, 1)).to.changeTokenBalances(
  token,
  [sender, receiver],
  [-1, 1]
);
```

#### 杂项字符串检查
有时您可能还需要验证十六进制字符串数据是否适用于它所使用的上下文。一些其他匹配器可以帮助您解决这个问题：

`properHex` 匹配器断言给定的字符串只包含有效的十六进制字符，并且它的长度（十六进制数字的数量）匹配它的第二个参数：
```
expect("0x1234").to.be.properHex(4);
```
`properAddress` 匹配器断言给定的字符串是适当长度的十六进制值（40 个十六进制数字）：
```
expect("0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266").to.be.a.properAddress;
```
properPrivateKey 匹配器断言给定的字符串是适当长度的十六进制值：
```
expect("0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80").to
  .be.a.properPrivateKey;
```
最后，`hexEqual` 匹配器接受两个十六进制字符串并比较它们的数值，而不考虑前导零或大小写数字：
```
expect("0x00012AB").to.hexEqual("0x12ab");
```

#### 深入挖掘

有关此包支持的所有匹配器的完整列表，请参阅[参考文档](https://hardhat.org/hardhat-chai-matchers/docs/reference)。

