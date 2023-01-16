## Compiling your contracts
要在 Hardhat 项目中编译合约，请使用内置`compile`任务：
```
$ npx hardhat compile
Compiling...
Compiled 1 contract successfully
```
默认情况下，编译后的工件将保存在 `artifacts/` 目录中，或者您配置的工件路径。 查看[路径配置部分](https://hardhat.org/hardhat-runner/docs/config#path-configuration)以了解如何更改它。 如果该目录不存在，将创建该目录。

初始编译后，Hardhat 将在您下次编译时尽量减少工作量。 例如，如果自上次编译以来您没有更改任何文件，则不会编译任何内容：
```
$ npx hardhat compile
Nothing to compile
```
如果您只修改了一个文件，则只会重新编译该文件和受其影响的其他文件。

要强制编译，您可以使用 `--force` 参数，或运行 `npx hardhat clean` 以清除缓存并删除工件。

#### Configuring the compiler
如果您需要自定义 Solidity 编译器选项，那么您可以通过 `hardhat.config.js` 中的 `solidity` 字段来实现。 使用此字段的最简单方法是通过设置编译器版本的简写，我们建议始终这样做：
```
module.exports = {
  solidity: "0.8.9",
};
```
我们建议始终设置编译器版本，以避免在发布新版本的 Solidity 时出现意外行为或编译错误。

> Hardhat 将自动下载您设置的 solc 版本。 如果您使用 HTTP 代理，则可能需要将 HTTP_PROXY 或 HTTPS_PROXY 环境变量设置为代理的 URL。

扩展的用法允许对编译器进行更多控制：
```
module.exports = {
  solidity: {
    version: "0.8.9",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
`settings`与可以传递给编译器的输入 JSON 中的`settings`条目具有相同的架构。 一些常用的设置是：
- `optimizer`：具有 `enabled` 和 `runs` 键的对象。 默认值：`{ enabled: false, runs: 200 }`。
- `evmVersion`：控制目标 evm 版本的字符串。 例如：`istanbul`、`berlin`或`london`。 默认值：由 `solc` 管理
  
如果您的任何合约具有您配置的编译器版本不满足的版本编译指示，则 Hardhat 将抛出错误。
> 如果您需要使用多个版本的 Solidity，请阅读本[指南](https://hardhat.org/hardhat-runner/docs/advanced/multiple-solidity-versions)。