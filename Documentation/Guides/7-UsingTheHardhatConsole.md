# Using the Hardhat console
Hardhat 内置了一个交互式 JavaScript 控制台。 您可以通过运行 `npx hardhat console` 来使用它：
```
$ npx hardhat console
Welcome to Node.js v12.10.0.
Type ".help" for more information.
>
```
`compile`任务将在打开控制台提示之前被调用，但您可以使用 `--no-compile` 参数跳过它。

控制台的执行环境与任务、脚本和测试的执行环境相同。 这意味着配置已经处理完毕，[Hardhat Runtime Environment](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment) 已经初始化并注入到全局范围内。

例如，您可以在全局范围内访问`config`对象：
```
> config
{
  solidity: { compilers: [ [Object] ], overrides: {} },
  defaultNetwork: 'hardhat',
  ...
}
>
```
如果您遵循了[入门指南](https://hardhat.org/hardhat-runner/docs/getting-started)或安装了@nomiclabs/hardhat-ethers，则 `ethers` 对象：
```
> ethers
{
  Signer: [Function: Signer] { isSigner: [Function] },
  ...
  provider: EthersProviderWrapper {
  ...
  },
  ...
  getSigners: [Function: getSigners],
  ...
  getContractAt: [Function: bound getContractAt] AsyncFunction
}
>
```
任何已注入 [Hardhat Runtime Environment](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment) 的东西都将在全局范围内神奇地可用。

或者，如果您是更明确的开发人员，您可以改为明确要求 HRE：
```
> const hre = require("hardhat")
> hre.ethers
{
  Signer: [Function: Signer] { isSigner: [Function] },
  ...
  provider: EthersProviderWrapper {
  ...
  },
  ...
  getSigners: [Function: getSigners],
  ...
  getContractAt: [Function: bound getContractAt] AsyncFunction
}
```
#### History
您还会注意到，该控制台具有您期望大多数交互式终端都具有的方便的历史记录功能，包括跨不同会话。 按向上箭头键尝试一下。 Hardhat 控制台只是 Node.js 控制台的一个实例，因此您在 Node.js 中使用的任何内容也都可以使用。

#### 异步操作和top-level wait
与以太坊网络交互，因此与您的智能合约交互，是异步操作。 因此，大多数 API 和库都使用 JavaScript 的 Promise 来返回值。

为了使事情变得更简单，Hardhat 的控制台支持top-level `await`语句（例如 console.log(await ethers.getSigners())）。