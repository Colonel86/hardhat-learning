### 支持硬分叉
- byzantium
- constantinople
- petersburg
- istanbul
- muirGlacier
- london
- arrowGlacier
- merge

### Config
#### Supported Fields
您可以在 networks.hardhat 配置中设置以下字段：
`chainId`
Hardhat Network 区块链使用的链 ID 号。 默认值：31337。
`from`
用作默认发件人的地址。 如果不存在，则使用 Hardhat Network 的第一个帐户。
`gas`
它的值应该是“auto”或一个数字。 如果使用数字，它将是每笔交易默认使用的气体限制。 如果使用“auto”，gas limit 将被自动估计。 默认值：与 blockGasLimit 相同的值。

请注意，当使用`ethers`时，将不会应用此值。
`gasPrice`
它的值应该是“auto”或一个数字（以wei为单位）。 此参数的行为类似于气体。 默认值：“auto”。

请注意，当使用`ethers`时，将不会应用此值。
`gasMultiplier`
由于估计过程的不确定性，用于乘以气体估计结果的数字使其有一些松弛。 默认值：1。

请注意，当使用ethers时，将不会应用此值。
`accounts`
该字段可以配置为以下之一：
- 描述 [HD wallet](https://hardhat.org/hardhat-network/docs/reference#hd-wallet-config)的对象。 这是默认设置。 它可以具有以下任何字段：
  - `mnemonic`：BIP39 定义的 12 或 24 字助记短语。 默认值：`"test test test test test test test test test test test junk"`
  - `initialIndex`：要派生的初始索引。 默认值：`0`
  - `path`：所有派生密钥的 HD 父级。 默认值：`"m/44'/60'/0'/0"`
  - `count`：要派生的帐户数。 默认值：`20`
  - `accountsBalance`：分配给每个派生帐户的余额（以 wei 为单位）的字符串。 默认值：`"10000000000000000000000"`（10000 ETH）。
  - `passphrase`：钱包的密码。 默认值：空字符串。
- Hardhat Network 将创建的一组初始帐户。 它们中的每一个都必须是具有 `privateKey` 和 `balance` 字段的对象。

`blockGasLimit`
在 Hardhat Network 的区块链中使用的区块 gas 限制。 默认值：`30_000_000`

`hardfork`
此设置会更改 Hardhat Network 的工作方式，以在给定的硬分叉上模仿以太坊的主网。 它必须是“byzantium”、“constantinople”、“petersburg”、“istanbul”、“muirGlacier”、“berlin”、“london”、“arrowGlacier”、“grayGlacier”和“merge”之一。 默认值：“merge”

`throwOnTransactionFailures`
一个布尔值，用于控制 Hardhat Network 是否抛出交易失败。 如果此值为`true`，Hardhat Network 将在交易失败时抛出 JavaScript 和 Solidity 堆栈跟踪组合。 如果为`false`，它将返回失败的交易哈希。 在这两种情况下，交易都被添加到区块链中。 默认值：`true`

`loggingEnabled`
控制 Hardhat Network 是否记录每个请求的布尔值。 默认值：进程内 Hardhat Network 提供程序为 `false`，Hardhat Network 支持的 JSON-RPC 服务器（即节点任务）为 `true`。

`initialDate`
设置区块链日期的可选字符串。 有效值是 [Javascript 的日期时间字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse#Date_Time_String_Format)。 默认值：如果不分叉另一个网络，则为当前日期和时间。 当分叉另一个网络时，使用你分叉的块的时间戳加上一秒。

`allowUnlimitedContractSize`
一个可选的布尔值，用于禁用 [EIP 170](https://eips.ethereum.org/EIPS/eip-170) 强加的合约大小限制。默认值：false

`forking`
描述[forking](https://hardhat.org/hardhat-network/docs/guides/forking-other-networks)配置的对象，可以具有以下字段：
- `url`：指向 JSON-RPC 节点的 URL，该节点具有您要分叉的状态。 该字段没有默认值。 必须提供 fork 才能工作。
- `blockNumber`：一个可选的数字来固定要从哪个块分叉。 如果未提供任何值，则使用最新的块。
- `enabled`：一个可选的布尔值，用于打开或关闭 fork 功能。 默认值：如果设置了 `url`，则为 `true`，否则为 `false`。

`chains`
配置链特定选项的对象。 每个键是一个代表链 ID 的数字，每个值是用该 ID 配置链的对象。 在内部对象中，支持以下字段：
  - hardforkHistory：一个对象，其键是代表硬分叉名称的字符串（例如“london”、“berlin”），其值是指定激活该硬分叉的块的数字。
  
默认值包括几个众所周知的链的配置（例如主网，chain ID 1）； 使用此字段仅在分叉不寻常的网络时有用。 用户可以覆盖某些链 ID 的默认值，同时保留其他链 ID 的默认值。 覆盖链 ID 的默认值将替换该链的整个配置。

有关详细信息，请参阅使用[自定义硬分叉历史记录](https://hardhat.org/hardhat-network/docs/guides/forking-other-networks#using-a-custom-hardfork-history)。

`minGasPrice`
交易必须具有的最低`gasPrice`。 如果“hardfork”是“london”或更新的版本，则此字段不得存在。 默认值：“0”。

`initialBaseFeePerGas`
第一个区块的 baseFeePerGas。 请注意，当分叉远程网络时，“第一个块”是紧接在您分叉出的块之后的那个。 如果“hardfork”不是“london”或更新的版本，则此字段不得存在。 默认值：如果不分叉，则为“1000000000”。 分叉远程网络时，如果远程网络使用EIP-1559，则第一个本地块将根据EIP使用正确的baseFeePerGas，否则使用“10000000000”。
- coinbase: 在新区块中用作 coinbase 的地址。 默认值：`"0xc014ba5ec014ba5ec014ba5ec014ba5ec014ba5e"`

#### Mining modes
您可以在 Hardhat Network 设置下配置挖矿行为：
```
networks: {
  hardhat: {
    mining: {
      auto: false,
      interval: 5000
    }
  }
}
```
在这个例子中，自动挖矿被禁用并设置了间隔挖矿，以便每 5 秒生成一个新块。 您还可以配置间隔挖掘以在随机延迟后生成新块：
```
networks: {
  hardhat: {
    mining: {
      auto: false,
      interval: [3000, 6000]
    }
  }
}
```
在这种情况下，将在 3 到 6 秒之间的随机延迟后挖掘一个新块。 例如，第一个块可以在 4 秒后被开采，第二个块可以在 5.5 秒后被开采，依此类推。

See also [Mining Modes](https://hardhat.org/hardhat-network/docs/explanation/mining-modes).

##### Manual mining
您可以像这样禁用两种挖掘模式：
```
networks: {
  hardhat: {
    mining: {
      auto: false,
      interval: 0
    }
  }
}
```
这意味着 Hardhat Network 不会挖掘新块，但您可以使用 `evm_mine` RPC 方法手动挖掘新块。 这将生成一个新块，其中将包含尽可能多的待处理交易。

##### Transaction ordering
Hardhat Network 可以用两种不同的方式对内存池交易进行排序。 它们的排序方式将改变内存池中的哪些交易被包含在下一个区块中，以及顺序。

第一种排序模式称为`priority`，模仿 Geth 的行为。 这意味着它根据支付给矿工的费用对交易进行优先排序。 这是默认值。

第二种排序模式称为`fifo`，使内存池交易按照它们到达的顺序排序。

您可以使用以下方式更改排序模式：
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
### console.log
- 您可以在calls和transactions中使用它。 它适用于view函数，但不适用于pure函数。
- 无论calls或transactions失败还是成功，它始终有效。
- 要使用它，您需要导入 `hardhat/console.sol`。
- 您可以按以下类型的任意顺序使用最多 4 个参数调用 `console.log`：
  - `uint`
  - `string`
  - `bool`
  - `address`
- 还有针对上述类型的单参数API，另外还有`bytes`, `bytes1`…最多`bytes32`:
  - `console.logInt(int i)`
  - `console.logUint(uint i)`
  - `console.logString(string memory s)`
  - `console.logBool(bool b)`
  - `console.logAddress(address a)`
  - `console.logBytes(bytes memory b)`
  - `console.logBytes1(bytes1 b)`
  - `console.logBytes2(bytes2 b)`
  - ...
  - `console.logBytes32(bytes32 b)`
- console.log实现了与Node.js的console.log中相同的格式化选项，后者反过来使用util.format。
  - Example: `console.log("Changing owner from %s to %s", currentOwner, newOwner)`
- `console.log`在标准 Solidity 中实现，然后在 Hardhat Network 中检测到。 这使得它的编译可以与任何其他工具（如 Remix、Waffle 或 Truffle）一起使用。
- `console.log`调用可以在其他网络中运行，例如mainnet、goerli、sepolia 等。它们在这些网络中什么都不做，但会消耗最少的 gas。
- `console.log`也可以通过 [Tenderly](https://tenderly.co/) 查看测试网和主网的输出
- `console.log`通过向众所周知的合约地址发送静态调用来工作。 在运行时，Hardhat Network 检测对该地址的调用，将输入数据解码为calls，并将其写入控制台。

### Initial State
Hardhat Network 默认初始化为以下状态：
- 一个全新的区块链，只有创世块。
- 20个账户，每个账户10000 ETH，生成助记词“test test test test test test test test test junk”。 他们的地址是：
  - 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
  - 0x70997970C51812dc3A010C7d01b50e0d17dc79C8
  - 0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC
  - 0x90F79bf6EB2c4f870365E785982E1f101E93b906
  - 0x15d34AAf54267DB7D7c367839AAf71A00a2C6A65
  - 0x9965507D1a55bcC2695C58ba16FB37d819B0A4dc
  - 0x976EA74026E726554dB657fA54763abd0C3a0aa9
  - 0x14dC79964da2C08b23698B3D3cc7Ca32193d9955
  - 0x23618e81E3f5cdF7f54C3d65f7FBc0aBf5B21E8f
  - 0xa0Ee7A142d267C1f36714E4a8F75612F20a79720
  - 0xBcd4042DE499D14e55001CcbB24a551F3b954096
  - 0x71bE63f3384f5fb98995898A86B02Fb2426c5788
  - 0xFABB0ac9d68B0B445fB7357272Ff202C5651694a
  - 0x1CBd3b2770909D4e10f157cABC84C7264073C9Ec
  - 0xdF3e18d64BC6A983f673Ab319CCaE4f1a57C7097
  - 0xcd3B766CCDd6AE721141F452C550Ca635964ce71
  - 0x2546BcD3c84621e976D8185a91A922aE77ECEc30
  - 0xbDA5747bFD65F08deb54cb465eB87D40e51B197E
  - 0xdD2FD4581271e230360230F9337D5c0430Bf44C0
  - 0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199
要自定义它，请查看[配置部分](https://hardhat.org/config#hardhat-network)。

### JSON-RPC methods support
#### Standard methods
`debug_traceTransaction`
获取已挖掘交易的调试痕迹。

要获得跟踪，请使用交易哈希作为参数调用此方法：
```
const trace = await hre.network.provider.send("debug_traceTransaction", [
  "0x123...",
]);
```
您还可以有选择地禁用步骤列表中的某些属性：
```
const trace = await hre.network.provider.send("debug_traceTransaction", [
  "0x123...",
  {
    disableMemory: true,
    disableStack: true,
    disableStorage: true,
  },
]);
```

###### Known limitations
- 您无法追踪使用早于 Spurious Dragon 的硬分叉的交易
- 不能保证消息的最后一步在 `gasCost` 属性中具有正确的值

#### Hardhat network methods
> 通过 [Hardhat Network Helpers](https://hardhat.org/hardhat-network-helpers) 库可以更轻松地使用这些方法中的大多数

`hardhat_addCompilationResult`
添加有关已编译合约的信息
`hardhat_dropTransaction`
从内存池中删除交易
`hardhat_impersonateAccount`
Hardhat Network 允许您发送模拟特定帐户和合约地址的交易。

要模拟帐户，请使用此方法，将要模拟的地址作为其参数传递：
```
await hre.network.provider.request({
  method: "hardhat_impersonateAccount",
  params: ["0x364d6D0333432C3Ac016Ca832fb8594A8cE43Ca6"],
});
```
如果您使用的是 [hardhat-ethers](https://github.com/NomicFoundation/hardhat/tree/main/packages/hardhat-ethers)，请在模拟帐户后调用 `getSigner`：
```
const signer = await ethers.getSigner("0x364d6D0333432C3Ac016Ca832fb8594A8cE43Ca6")
signer.sendTransaction(...)
```
call `hardhat_stopImpersonatingAccount`停止冒充。

`hardhat_getAutomine`
如果启用了自动挖掘，则返回 true，否则返回 false。 请参阅[挖矿模式](https://hardhat.org/hardhat-network/explanation/mining-modes)以了解更多信息。
`hardhat_metadata`
返回一个对象，该对象包含有关安全帽网络实例的元数据。 该对象包含：
- `clientVersion`：标识 Hardhat 版本的字符串，用于调试目的，不打算向用户显示。
- `chainId`：链的id。 用于签署交易。
- `instanceId`：一个 0x 前缀的十六进制编码的 32 字节 id，它唯一标识 Hardhat Network 的实例/运行。 多次运行 Hardhat Network（即使使用相同的版本和参数）将始终导致不同的 `instanceId。` 运行 `hardhat_reset` 将更改现有 Hardhat 网络的 `instanceId。`
- `latestBlockNumber`：Hardhat Network 中的最新区块编号。
- `latestBlockHash`：Hardhat Network 中最新区块的哈希值。
- `forkedNetwork`：包含有关分叉网络信息的对象。 该字段仅在 Hardhat Network 分叉另一条链时出现。 它的字段是：
  - chainId：被分叉的网络的chainId
  - forkBlockNumber：网络分叉出的块的编号。
  - forkBlockHash：网络分叉出的块的哈希值。

`hardhat_mine`
有时您可能希望将 Hardhat Network 的最新区块编号提前大量区块。 一种方法是多次调用 `evm_mine` RPC 方法，但如果您想挖掘数千个区块，这就太慢了。 hardhat_mine 方法可以在恒定时间内一次挖掘任意数量的块。 （无论开采多少块，它都表现出相同的性能。）

`hardhat_mine` 接受两个参数，这两个参数都是可选的。 第一个参数是要挖的块数，默认为1。第二个参数是每个块的时间戳之间的间隔，以秒为单位，也默认为1。（间隔仅适用于在 给定的方法调用，而不是之后挖掘的块。）

```
// mine 256 blocks
await hre.network.provider.send("hardhat_mine", ["0x100"]);

// mine 1000 blocks with an interval of 1 minute
await hre.network.provider.send("hardhat_mine", ["0x3e8", "0x3c"]);
```
请注意，通过这种方法开采的大多数区块（除了最后一个）在技术上可能不是有效区块。 具体来说，他们有一个无效的父哈希，coinbase 账户不会被记入区块奖励，baseFeePerGas 将是不正确的。 （由 hardhat_mine 生成的序列中的最后一个块将始终是完全有效的。）

另请注意，通过 `hardhat_mine` 创建的区块可能不会触发新区块事件，例如通过 `eth_newBlockFilter` 创建的过滤器和 WebSocket 订阅新区块事件。
`hardhat_reset`
请参阅[主网分叉指南](https://hardhat.org/hardhat-network/docs/guides/forking-other-networks#resetting-the-fork)
`hardhat_setBalance`
修改帐户余额。
For example:
```
await network.provider.send("hardhat_setBalance", [
  "0x0d2026b3EE6eC71FC6746ADb6311F6d3Ba1C000B",
  "0x1000",
]);
```
这将导致帐户 `0x0d20...000B` 的余额为 4096 wei。
`hardhat_setCode`
修改存储在帐户地址中的字节码。
For example:
```
await network.provider.send("hardhat_setCode", [
  "0x0d2026b3EE6eC71FC6746ADb6311F6d3Ba1C000B",
  "0xa1a2a3...",
]);
```
这将导致帐户 `0x0d20...000B` 成为字节码为 `a1a2a3...` 的智能合约。如果该地址已经是智能合约，则其代码将被指定的代码替换。
`hardhat_setCoinbase`
设置要在新块中使用的 coinbase 地址。
For example:
```
await network.provider.send("hardhat_setCoinbase", [
  "0x0d2026b3EE6eC71FC6746ADb6311F6d3Ba1C000B",
]);
```
这将导致账户 `0x0d20...000B` 在每个新区块中被用作矿工/coinbase。
`hardhat_setLoggingEnabled`
在 Hardhat Network 中启用或禁用日志记录
`hardhat_setMinGasPrice`
更改网络接受的最低 gas 价格（以 wei 为单位）
`hardhat_setNextBlockBaseFeePerGas`
设置下一个区块的基本费用。
For example:
```
await network.provider.send("hardhat_setNextBlockBaseFeePerGas", [
  "0x2540be400", // 10 gwei
]);
```
这只会影响下一个块； 根据 [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559)，基本费用将在每个后续区块中不断更新。
`hardhat_setPrevRandao`
设置下一个块的 PREVRANDAO 值。
For example:
```
await network.provider.send("hardhat_setPrevRandao", [
  "0x1234567812345678123456781234567812345678123456781234567812345678",
]);
```
这只会影响下一个块。 后续块的 PREVRANDAO 将继续计算为先前值的 keccak256 哈希。

`hardhat_setNonce`
通过覆盖它来修改帐户的随机数。
For example:
```
await network.provider.send("hardhat_setNonce", [
  "0x0d2026b3EE6eC71FC6746ADb6311F6d3Ba1C000B",
  "0x21",
]);
```

这将导致帐户 0x0d20...000B 的随机数为 33。

如果 nonce 小于当前值，则抛出 InvalidInputError。 此限制的原因是为了避免在多次使用相同随机数部署合约时发生冲突。

您只能使用此方法来增加帐户的随机数； 您不能设置低于帐户当前随机数的值。
`hardhat_setStorageAt`
写入帐户存储的单个位置。
For example:
```
await network.provider.send("hardhat_setStorageAt", [
  "0x0d2026b3EE6eC71FC6746ADb6311F6d3Ba1C000B",
  "0x0",
  "0x0000000000000000000000000000000000000000000000000000000000000001",
]);
```

这会将合约的第一个存储位置（索引 0x0）设置为 1。

智能合约的变量与其存储位置之间的映射并不简单，除非在一些非常简单的情况下。 例如，如果您部署此合同：
```
contract Foo {
  uint public x;
}
```
并且您将第一个存储位置设置为 1（如前面的代码片段所示），然后调用 `foo.x()` 将返回 1。

存储位置索引不得超过 2^256，写入的值必须恰好为 32 字节长。
`hardhat_stopImpersonatingAccount`
使用此方法可以在以前使用过后停止模拟帐户hardhat_impersonateAccount
，像：
```
await hre.network.provider.request({
  method: "hardhat_stopImpersonatingAccount",
  params: ["0x364d6D0333432C3Ac016Ca832fb8594A8cE43Ca6"],
});
```
#### Special testing/debugging methods
`evm_increaseTime`
Same as Ganache.
`evm_mine`
Same as Ganache
`evm_revert`
Same as Ganache.
`evm_setAutomine`
根据单个布尔参数启用或禁用在每个提交到网络的新事务中自动挖掘新块。 您可以使用
`hardhat_getAutomine`获取当前值。 另见[挖掘模式](https://hardhat.org/hardhat-network/explanation/mining-modes)。
`evm_setBlockGasLimit`
`evm_setIntervalMining`
启用（使用大于 0 的数字参数）或禁用（使用等于 0 的数字参数）以固定的毫秒间隔自动挖掘块，每个块都将包括所有待处理的交易。 另见[挖掘模式](https://hardhat.org/hardhat-network/explanation/mining-modes)。
`evm_setNextBlockTimestamp`
此方法的工作方式与 `evm_increaseTime` 类似，但会在下一个块中获取您想要的确切时间戳，并相应地增加时间。
`evm_snapshot`
与[Ganache](https://github.com/trufflesuite/ganache/blob/ef1858d5d6f27e4baeb75cccd57fb3dc77a45ae8/src/chains/ethereum/ethereum/RPC-METHODS.md#evm_snapshot)相同。

快照当前区块的区块链状态。 不带参数。 返回创建的快照的 ID。 快照只能还原一次。 evm_revert 成功后，不能再次使用相同的快照 ID。 如果您需要多次恢复到同一点，请考虑在每次 evm_revert 之后创建一个新快照。

#### Unsupported methods
`eth_compileLLL`
`eth_compileSerpent`
`eth_compileSolidity`
`eth_getCompilers`
`eth_getProof`
`eth_getUncleByBlockHashAndIndex`
`eth_getUncleByBlockNumberAndIndex`
`eth_getUncleCountByBlockHash`
`eth_getUncleCountByBlockNumber`
`eth_getWork`
`eth_hashrate`
`eth_protocolVersion`
`eth_signTransaction`
`eth_signTypedData`
`eth_signTypedData_v3`
`eth_submitHashrate`
`eth_submitWork`
### Limitations
#### Supported Solidity versions
Hardhat Network 可以运行任何智能合约，但它只能理解 Solidity 0.5.1 及更新版本。

如果您使用旧版本的 Solidity 或使用其他语言进行编译，则可以使用 Hardhat Network，但不会生成 Solidity 堆栈跟踪。
#### Solidity optimizer support
Hardhat Network 可以使用经过优化编译的智能合约，但这可能会导致堆栈跟踪的行号稍微偏离。

我们建议在测试和调试合约时不进行优化编译。