当 Hardhat 运行时，它会从当前工作目录开始搜索最近的 hardhat.config.ts 文件。 该文件通常位于项目的根目录中。 一个空的 hardhat.config.ts 足以让 Hardhat 工作。

您的整个 Hardhat 设置（即您的配置、插件和自定义任务）都包含在此文件中。

### 可用的配置选项
要设置您的配置，您必须从 hardhat.config.ts 中导出一个对象。

这个对象可以有像 defaultNetwork, networks, solidity, paths, mocha这样的条目。 例如：
```
module.exports = {
  defaultNetwork: "goerli",
  networks: {
    hardhat: {
    },
    goerli: {
      url: "https://eth-goerli.alchemyapi.io/v2/123abc123abc123abc123abc123abcde",
      accounts: [privateKey1, privateKey2, ...]
    }
  },
  solidity: {
    version: "0.5.15",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  },
  paths: {
    sources: "./contracts",
    tests: "./test",
    cache: "./cache",
    artifacts: "./artifacts"
  },
  mocha: {
    timeout: 40000
  }
}
```
### Networks configuration
网络配置字段是一个可选对象，其中网络名称映射到它们的配置。

Hardhat 中有两种网络：基于 JSON-RPC 的网络和内置的 Hardhat 网络。

您可以通过设置配置的 defaultNetwork 字段来自定义运行 Hardhat 时默认使用的网络。 如果省略此配置，其默认值为“hardhat”。

#### Hardhat Network
Hardhat 内置了一个名为 hardhat 的特殊网络。 使用此网络时，当您运行任务、脚本或测试智能合约时，将自动创建 [Hardhat Network](https://hardhat.org/hardhat-network/docs) 实例。

Hardhat Network 拥有一流的 Solidity 支持。 它始终知道正在运行哪些智能合约，确切地知道它们做了什么以及它们失败的原因。 点击[此处](https://hardhat.org/hardhat-network/docs)了解详情。

有关可配置内容的详细信息，请参阅 [Hardhat 网络配置参考](https://hardhat.org/hardhat-network/docs/reference#config)。

#### JSON-RPC based networks
这些是连接到外部节点的网络。 节点可以在您的计算机中运行，如 Ganache，或远程运行，如 Alchemy 或 Infura。

这种网络配置了具有以下字段的对象：
- `url`：节点的 url。 自定义网络需要此参数。
- `chainId`：一个可选数字，用于验证 Hardhat 连接到的网络。 如果不存在，则省略此验证。
- `from`：用作默认发件人的地址。 如果不存在，则使用节点的第一个帐户。
- `gas`：它的值应该是`"auto"`或一个数字。 如果使用数字，它将是每笔交易默认使用的气体限制。 如果使用`"auto"`，gas limit 将被自动估计。 默认值：`"auto"`。
- `gasPrice`：它的值应该是"auto"或一个数字。 此参数的行为类似于`gas`。 默认值：`"auto"`。
- `gasMultiplier`：由于估计过程的不确定性，用于乘以气体估计结果的数字使其有一些松弛。 默认值：`1`。
- `accounts`：此字段控制 Hardhat 使用哪些帐户。 它可以使用节点的帐户（通过将其设置为`"remote"`）、本地帐户列表（通过将其设置为十六进制编码的私钥数组）或使用 HD 钱包。 默认值：`"remote"`。
- `httpHeaders`：您可以使用此字段设置在发出 JSON-RPC 请求时要使用的额外 HTTP 标头。 它接受一个将标头名称映射到它们的值的 JavaScript 对象。 默认值：`undefined`。
- `timeout`：发送到 JSON-RPC 服务器的请求的超时时间（毫秒）。 如果请求花费的时间超过此时间，它将被取消。 默认值：本地主机网络为 `40000`，其余为 `20000`。
#### HD Wallet config
要将 [HD Wallet](https://github.com/ethereumbook/ethereumbook/blob/develop/05wallets.asciidoc#hd_wallets)与 Hardhat 一起使用，您应该将网络的帐户字段设置为具有以下字段的对象：

- `mnemonic`：需要一个带有钱包的助记短语的字符串。
- `path`：所有派生密钥的 HD(Hierarchical Deterministic-分层确定性钱包) 父级。 默认值：`"m/44'/60'/0'/0"`。
- `initialIndex`：要派生的地址的初始索引。 默认值：`0`
- `count`：要派生的帐户数。 默认值：`20`。
- `passphrase`：钱包的密码。 默认值：空字符串。
  
For example:
```
module.exports = {
  networks: {
    goerli: {
      url: "...",
      accounts: {
        mnemonic: "test test test test test test test test test test test junk",
        path: "m/44'/60'/0'/0",
        initialIndex: 0,
        count: 20,
        passphrase: "",
      },
    },
  },
};
```

#### Default networks object
```
{
    localhost: {
      url: "http://127.0.0.1:8545"
    },
    hardhat: {
      // See its defaults
    }
}
```
####  Solidity configuration
solidity config 是一个可选字段，可以是以下之一：
- 要使用的 solc 版本，例如 `"0.7.3"`
- 描述单个编译器配置的对象。 它包含以下键：
  - `version`：要使用的 solc 版本
  - `settings`：与“Input JSON”中的“settings”条目具有相同模式的对象。
- 描述多个编译器及其各自配置的对象。 它包含以下内容：
  - `compilers`：编译器配置对象的列表，如上所示。
  - `overrides`：编译器配置覆盖对象的可选映射。 这将文件名映射到编译器配置对象。 查看[编译指南](https://hardhat.org/hardhat-runner/docs/guides/compile-contracts)以了解更多信息。

#### Path configuration
您可以通过使用以下键向`paths`字段提供对象来自定义 Hardhat 使用的不同路径：
- `root`: Hardhat 项目的根源。 这个路径是从 hardhat.config.js 的目录解析出来的。 默认值：包含配置文件的目录。
- `sources`：存储合约的目录。 此路径是从项目的根目录解析的。 默认值：`./contracts`。
- `tests`：测试所在的目录。 此路径是从项目的根目录解析的。 默认值：`./test`。
- `cache`：Hardhat 用来缓存其内部内容的目录。 此路径是从项目的根目录解析的。 默认值：'./cache'。
- `artifacts`：存储编译工件的目录。 此路径是从项目的根目录解析的。 默认值：`./artifacts`。

#### Mocha configuration
您可以使用`mocha`条目配置测试的运行方式，它接受与 Mocha 相同的选项。

#### 从 Hardhat 的配置中快速集成其他工具
Hardhat 的配置文件将始终在任何任务之前运行，因此您可以使用它与其他工具集成，例如导入 `@babel/register`。