## Hardhat Network
Hardhat 内置了 Hardhat Network，这是一个专为开发而设计的本地以太坊网络节点。 它允许你部署你的合约、运行你的测试和调试你的代码，所有这些都在你本地机器的范围内。

### How does it work?
它作为进程内或独立守护进程运行，为 JSON-RPC 和 WebSocket 请求提供服务。

默认情况下，它会在收到的每笔交易中按顺序且没有延迟地挖掘一个块。

它由 `@ethereumjs/vm `EVM 实现支持，与 ganache、Remix 和 Ethereum Studio 使用的相同。

### 我该如何使用它？
默认情况下，如果您使用的是 Hardhat，那么您已经在使用 Hardhat Network。

当 Hardhat 执行您的测试、脚本或任务时，进程中的 Hardhat Network 节点会自动启动，并且 Hardhat 的所有插件（ethers.js、web3.js、Waffle、Truffle 等）将直接连接到该节点的提供者。

无需对您的测试或脚本进行任何更改。

Hardhat Network 只是另一个网络。 如果你想要明确，你可以运行，例如，`npx hardhat run --network hardhat scripts/my-script.js`。

#### 单机运行以支持钱包和其他软件
或者，Hardhat Network 可以独立运行，以便外部客户端可以连接到它。 这可能是 MetaMask、您的 Dapp 前端或脚本。 要以这种方式运行 Hardhat Network，请运行：
```
$ npx hardhat node
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========
Account #0: 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1: 0x70997970c51812dc3a010c7d01b50e0d17dc79c8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d
...
```

这将启动 Hardhat Network，并将其公开为 JSON-RPC 和 WebSocket 服务器。

然后，只需将您的钱包或应用程序连接到 `http://127.0.0.1:8545`。

如果你想将 Hardhat 连接到这个节点，你只需要使用 `--network localhost` 运行。

> 不要将主网以太币发送到 hardhat 节点显示的帐户地址。 这些地址和私钥是确定性的：它们对所有 Hardhat 用户都是相同的。 因此，这些私钥是众所周知的，因此可能有机器人在主网上监视这些地址，等待提取发送给它们的任何资金。 如果您将这些帐户中的任何一个添加到钱包（例如 Metamask），请非常小心，避免向它们发送任何主网以太币：考虑将帐户命名为“Hardhat - Unsafe”之类的名称，以防止出现任何错误。

### Why would I want to use it?
#### Solidity 堆栈跟踪
Hardhat Network 拥有一流的 Solidity 支持。 它始终知道正在运行哪些智能合约、它们究竟做了什么以及它们失败的原因。

如果交易或调用失败，Hardhat Network 将抛出异常。 此异常将有一个组合的 JavaScript 和 Solidity 堆栈跟踪：堆栈跟踪从 JavaScript/TypeScript 开始直到您调用合约，并继续完整的 Solidity 调用堆栈。

这是使用 TruffleContract 的 Hardhat Network 异常示例：
```
Error: Transaction reverted: function selector was not recognized and there's no fallback function
  at ERC721Mock.<unrecognized-selector> (contracts/mocks/ERC721Mock.sol:9)
  at ERC721Mock._checkOnERC721Received (contracts/token/ERC721/ERC721.sol:334)
  at ERC721Mock._safeTransferFrom (contracts/token/ERC721/ERC721.sol:196)
  at ERC721Mock.safeTransferFrom (contracts/token/ERC721/ERC721.sol:179)
  at ERC721Mock.safeTransferFrom (contracts/token/ERC721/ERC721.sol:162)
  at TruffleContract.safeTransferFrom (node_modules/@nomiclabs/truffle-contract/lib/execute.js:157:24)
  at Context.<anonymous> (test/token/ERC721/ERC721.behavior.js:321:26)
```

最后两行对应于执行失败事务的 JavaScript 测试代码。 其余的是 Solidity 堆栈跟踪。 这样您就可以确切地知道为什么您的测试没有通过。

#### 自动错误信息
Hardhat Network 始终知道您的交易或调用失败的原因，并使用此信息来简化您的合约调试。

当交易无故失败时，Hardhat Network 将在以下情况下创建明确的错误消息：
- Calling a non-payable function with ETH
- Sending ETH to a contract without a payable fallback or receive function
- Calling a non-existent function when there's no fallback function
- Calling a function with incorrect parameters
- Calling an external function that doesn't return the right amount of data
- Calling an external function on a non-contract account
- Failing to execute an external call because of its parameters (e.g. trying to send too much ETH)
- Calling a library without DELEGATECALL
- Incorrectly calling a precompiled contract
- Trying to deploy a contract that exceeds the bytecode size limit imposed by [EIP-170](https://eips.ethereum.org/EIPS/eip-170)

#### console.log
Hardhat Network 允许您通过从 Solidity 代码调用 `console.log()` 来打印日志消息和合约变量。

要使用它，您只需导入 'hardhat/console.sol' 并调用它。 它实现了与 Node.js 的 'console.log' 中相同的格式化选项，后者又使用 `util.format`。 例如：`console.log("Changing owner from %s to %s", currentOwner, newOwner)`。

无论调用或交易失败还是成功，它始终有效。 而且，因为它是在标准 Solidity 中实现的，所以它可以与任何工具或库一起使用，在完全支持的地方发出日志条目——Hardhat Network、Remix 和 Tenderly——并在其他任何地方优雅地退回到无操作——Remix、Waffle、Truffle 等——尽管它确实在实时网络上消耗少量气体。

您可以在示例项目中看到一个示例。 按照[快速入门](https://hardhat.org/hardhat-runner/docs/getting-started#quick-start)中的步骤进行尝试。 您还可以参考[参考文档](https://hardhat.org/hardhat-network/docs/reference#console.log)中的更多详细信息。

#### Mainnet forking
Hardhat Network 能够将主网区块链的状态复制到您的本地环境中，包括所有余额和已部署的合约。 这被称为"forking mainnet"。

在从主网分叉的本地环境中，您可以执行事务以调用主网部署的合约，或以与主网交互的任何其他方式与网络交互。 此外，您可以执行非分叉 Hardhat Network 支持的任何操作：查看控制台日志、获取堆栈跟踪或使用默认帐户部署新合约。

更一般地说，Hardhat Network 可用于分叉任何网络，而不仅仅是主网。 更进一步，Hardhat Network 可用于分叉任何 EVM 兼容的区块链，而不仅仅是以太坊。

您还可以使用分叉的 Hardhat Network 做其他事情。 查看我们的[指南](https://hardhat.org/hardhat-network/docs/guides/forking-other-networks)以了解更多信息。

#### Mining modes
Hardhat Network 可以配置为在收到每笔交易后立即自动挖掘区块，也可以将其配置为间隔挖掘，即定期挖掘新区块，并尽可能多地合并未决交易。

您可以使用其中一种模式，也可以同时使用或都不使用。 默认情况下，仅启用自动挖矿模式。

如果两种挖矿模式均未启用，则不会挖出新区块，但您可以使用 `evm_mine` RPC 方法手动挖出新区块。 这将生成一个新块，其中将包含尽可能多的待处理交易。

有关挖矿模式和内存池行为的更多详细信息，请参阅[挖矿模式](https://hardhat.org/hardhat-network/docs/explanation/mining-modes)。

#### Logging
Hardhat Network 使用其跟踪基础设施提供丰富的日志记录，帮助您开发和调试智能合约。

例如，一个成功的事务和一个失败的调用看起来像这样：
```
eth_sendTransaction
  Contract deployment: Greeter
  Contract address: 0x8858eeb3dfffa017d4bce9801d340d36cf895ccf
  Transaction: 0x7ea2754e53f09508d42bd3074046f90595bedd61fcdf75a4764453454733add0
  From: 0xc783df8a850f42e7f7e57013759c285caa701eb6
  Value: 0 ETH
  Gas used: 568851 of 2844255
  Block: #2 - Hash: 0x4847b316b12170c576999183da927c2f2056aa7d8f49f6e87430e6654a56dab0

  console.log:
    Deploying a Greeter with greeting: Hello, world!

eth_call
  Contract call: Greeter#greet
  From: 0xc783df8a850f42e7f7e57013759c285caa701eb6

  Error: VM Exception while processing transaction: revert Not feeling like it
      at Greeter.greet (contracts/Greeter.sol:14)
      at process._tickCallback (internal/process/next_tick.js:68:7)
```
使用 Hardhat Network 的节点（即 npx hardhat node）时默认启用此日志记录，但在使用进程内 Hardhat Network 提供程序时禁用。 查看 [Hardhat Network 的配置](https://hardhat.org/hardhat-network/docs/reference#config)以了解有关如何控制其日志记录的更多信息。

#### The debug_traceTransaction method
您可以使用 debug_traceTransaction RPC 方法获取已挖掘交易的调试跟踪。 返回的对象具有交易执行的详细描述，包括描述每个已执行操作码的步骤列表和此时 EVM 的状态。

如果您将[mainnet forking](https://hardhat.org/hardhat-network/docs/guides/forking-other-networks.html)与存档节点一起使用，即使您使用的节点不支持 `debug_traceTransaction`，您也可以从远程网络获取交易痕迹。

有关详细信息，请[参阅此方法的参考文档](https://hardhat.org/hardhat-network/docs/reference#debug-tracetransaction)。

#### 深入挖掘
这只是对 Hardhat Network 是什么的高级解释。 要深入挖掘，请参阅[参考文档](https://hardhat.org/hardhat-network/docs/reference)。