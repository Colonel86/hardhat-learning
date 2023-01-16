这是 Hardhat Network Helpers 库的参考。 该库包含实用功能，可以更轻松、更安全地与 Hardhat 网络进行交互。

### Mining blocks
`mine([blocks], [options])`
以给定的时间间隔挖掘指定数量的块。

参数：
- `blocks`：要挖掘的块数。 默认为 1。
- `options.interval`：配置每个挖掘块的时间戳之间的间隔（以秒为单位）。 默认为 1。
Example:
```
// mine a new block
await helpers.mine();

// mine several blocks
await helpers.mine(1000);

// mine several blocks with a given interval between their timestamps
await helpers.mine(1000, { interval: 15 });
```
`mineUpTo(blockNumber)`
挖掘新块，直到最新的块号为 `blockNumber`。
Parameters:
- `blockNumber`：必须大于最新区块的编号。

Example:
```
await helpers.mineUpTo(1234);
```

### Manipulating accounts
`setBalance(address, balance)`
设置给定地址的余额。
Parameters:
- `address`：将编辑其余额的地址。
- `balance`：为给定地址设置的新余额，以 wei 为单位。
Example:
```
await helpers.setBalance(address, 100n ** 18n);
```
`setCode(address, code)`
修改存储在帐户地址中的合约字节码。
Parameters:
- `address`：应存储给定代码的地址。
- `code`：要存储的代码。

Example:
```
await helpers.setCode(address, "0x1234...");
```
`setNonce(address, nonce)`
通过覆盖它来修改帐户的随机数。
Parameters:
- `address`：要更改其随机数的地址。
- `nonce`：新的随机数。

Example:
```
await helpers.setNonce(address, 100);
```
`setStorageAt(address, index, value)`
写入帐户存储的单个位置。
Parameters:
- `address`：应存储代码的地址。
- `index`：存储中的索引。
- `value`：要存储的值。

Example:
```
await helpers.setStorageAt(address, storageSlot, newValue);
```
`getStorageAt(address, index, [block])`
检索位于给定地址、索引和块号的数据。
Parameters:
- `address`：要从中检索存储的地址。
- `index`：存储中的位置。
- `block`：块号，或者是`"latest"`、`"earliest"`或`"pending"`中的一个。 默认为`"latest"`。

返回：包含检索到的十六进制值的字符串。
Example:
```
await helpers.getStorageAt(address, storageSlot);
```
`impersonateAccount(address)`
允许 Hardhat Network 以给定地址签署后续交易。
Parameters:
`address`：要模拟的地址。

Example:
```
await helpers.impersonateAccount(address);
```
`stopImpersonatingAccount(address)`
阻止 Hardhat Network 冒充给定的地址。
Parameters:
- `address`: 停止模拟的地址。

Example:
```
await helpers.stopImpersonatingAccount(address);
```
### Time helpers
`latest()`
返回最新区块的时间戳。
Example:
```
await helpers.time.latest();
```
`latestBlock()`
返回最新块的编号。
Example:
```
await helpers.time.latestBlock();
```
`increase(amountInSeconds)`
在最新区块的时间戳之后挖掘一个新区块，其时间戳为 amountInSeconds。
Parameters:
- `amountInSeconds`：将下一个块的时间戳增加的秒数。

Returns：已开采区块的时间戳。
Example:
```
// advance time by one hour and mine a new block
await helpers.time.increase(3600);
```
`increaseTo(timestamp)`
挖出一个时间戳为 `timestamp` 的新区块。

Parameters:
- `timestamp`:必须大于最新块的时间戳。

Example:
```
await helpers.time.increaseTo(newTimestamp);
```
`setNextBlockTimestamp(timestamp)`
设置下一个块的时间戳，但不挖掘一个。
Parameters:
`timestamp`：可以是 Date 或 [Epoch Seconds](https://en.wikipedia.org/wiki/Unix_time)。 必须大于最新区块的时间戳。

Example:
```
// set the timestamp of the next block but don't mine a new block
await helpers.time.setNextBlockTimestamp(newTimestamp);
```

###  Snapshots
`takeSnapshot()`
在当前区块拍摄区块链状态的快照。

Returns: 具有`restore`方法的对象，可用于将网络重置为快照中的状态。

Example:
```
// take a snapshot of the current state of the blockchain
const snapshot = await helpers.takeSnapshot();

// after doing some changes, you can restore to the state of the snapshot
await snapshot.restore();
```

### Fixtures
```
loadFixture()
```
在设置所需网络状态的测试中很有用。

执行给定的函数并拍摄区块链的快照。 在随后使用相同函数调用 `loadFixture` 时，区块链将恢复到该快照，而不是再次执行该函数。

> 警告：不要将 `loadFixture` 与匿名函数一起使用，否则每次都会执行该函数而不是使用快照：

正确用法：`loadFixture(deployTokens)`
错误用法：`loadFixture(async () => { ... })`

Parameters:
`fixture`: 将用于设置夹具的功能。

Example:
```
async function deployContractsFixture() {
  const token = await Token.deploy(...);
  const exchange = await Exchange.deploy(...);

  return { token, exchange };
}

it("test", async function () {
  const { token, exchange } = await loadFixture(deployContractsFixture);

  // use token and exchanges contracts
})
```

### Other helpers
`dropTransaction(txHash)`
从内存池中删除给定的交易（如果存在）。

Parameters:
`txHash`：要从内存池中删除的交易哈希。

Returns: 如果成功则返回 true，否则返回 false。

Throws: 如果交易已经被开采。

Example:
```
await helpers.dropTransaction(
  "0x1010101010101010101010101010101010101010101010101010101010101010"
);
```

`setNextBlockBaseFeePerGas(baseFeePerGas)`
设置下一个区块的基本费用。

Parameters:
`baseFeePerGas`: 
使用新的基本费用。

`setPrevRandao(prevRandao)`
设置下一个块的 PREVRANDAO 值。

Parameters:
- `prevRandao`：要使用的新 PREVRANDAO 值。