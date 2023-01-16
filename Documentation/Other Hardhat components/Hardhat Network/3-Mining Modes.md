Hardhat Network 可以配置为在收到每笔交易后立即自动挖掘区块，也可以将其配置为间隔挖掘，即定期挖掘新区块，并尽可能多地合并未决交易。

您可以使用其中一种模式，也可以同时使用或都不使用。 默认情况下，仅启用自动挖矿模式。

当禁用自动挖掘时，每个发送的交易都会添加到内存池中，其中包含将来可以挖掘的所有交易。 默认情况下，Hardhat Network 的内存池遵循与 Geth 相同的规则。 这意味着，除其他事项外，交易按支付给矿工的费用（然后按到达时间）排列优先级，无效交易将被丢弃。 除了默认的内存池行为外，还可以使用替代的 [FIFO 行为](https://hardhat.org/hardhat-network/docs/reference#transaction-ordering)。

### Mempool behavior
当禁用自动挖掘时，每个发送的交易都会添加到内存池中，其中包含将来可以挖掘的所有交易。 默认情况下，Hardhat Network 的内存池遵循与 Geth 相同的规则。 这意味着，除其他外，：

- 首先包含具有较高 gas 价格的交易
- 如果可以包含两笔交易并且都向矿工提供相同的总费用，则首先包含最先收到的交易
- 如果交易无效（例如，其随机数低于发送它的地址的随机数），则交易将被丢弃。

您可以使用`pending`块标签获取将包含在下一个块中的待处理交易列表：
```
const pendingBlock = await network.provider.send("eth_getBlockByNumber", [
  "pending",
  false,
]);
```
#### Mining transactions in FIFO order
Hardhat Network 的内存池订购交易的方式是可定制的。 默认情况下，它们按照 Geth 的规则进行优先排序，但您可以改为启用 FIFO 行为，这可确保交易按照发送的相同顺序添加到块中，这对于从其他网络重新创建块很有用。

您可以在您的配置中启用 FIFO 行为：
```
networks: {
  hardhat: {
    mining: {
      mempool: {
        order: "fifo"
      }
    }
  }
}
```

### 删除和替换事务
内存池中的交易可以使用[dropTransaction](https://hardhat.org/hardhat-network-helpers/docs/reference#droptransaction(txhash))网络助手：
```
const txHash = "0xabc...";
await helpers.dropTransaction(txHash);
```
您还可以通过发送一个新交易来替换交易，该新交易具有与内存池中已有交易相同的随机数，但汽油价格更高。 请记住，就像在 Geth 中一样，要使其发挥作用，新的天然气/费用价格必须至少比当前交易的天然气价格高出 10%。

### Configuring Mining Modes
请参阅[挖矿模式配置参考](https://hardhat.org/hardhat-network/docs/reference#mining-modes)以了解要在 Hardhat 配置文件中放置的内容。

#### Using RPC methods
您可以使用两种 RPC 方法在运行时更改挖掘行为：`evm_setAutomine` 和 `evm_setIntervalMining`。 例如，要禁用自动挖掘：
```
await network.provider.send("evm_setAutomine", [false]);
```
并启用间隔挖掘：
```
await network.provider.send("evm_setIntervalMining", [5000]);
```