## 分叉其他网络

您可以启动一个分叉主网的 Hardhat Network 实例。 这意味着它将模拟具有与主网相同的状态，但它将作为本地开发网络工作。 这样您就可以与已部署的协议进行交互并在本地测试复杂的交互。

要使用此功能，您需要连接到存档节点。 我们推荐使用[Alchemy](https://alchemyapi.io/)。

### Forking from mainnet
尝试此功能的最简单方法是从命令行启动节点：
```
npx hardhat node --fork https://eth-mainnet.alchemyapi.io/v2/<key>
```
您还可以将 Hardhat Network 配置为始终执行此操作：
```
networks: {
  hardhat: {
    forking: {
      url: "https://eth-mainnet.alchemyapi.io/v2/<key>",
    }
  }
}
```
（请注意，您需要将 URL 的 `<key>` 部分替换为您的个人 Alchemy API 密钥。）

通过访问主网上存在的任何状态，Hardhat Network 将拉取数据并透明地公开它，就好像它在本地可用一样。

### 固定块
Hardhat Network 默认会从最近的主网区块中分叉出来。 虽然根据上下文这可能是可行的，但要设置依赖于分叉的测试套件，我们建议从特定的块号开始分叉。

有两个原因：
- 您的测试运行所针对的状态可能会在运行之间发生变化。 这可能会导致您的测试或脚本表现不同。
- 固定启用缓存。 每次从主网获取数据时，Hardhat Network 都会将其缓存在磁盘上以加快未来的访问速度。 如果您不固定块，每个新块都会有新数据，缓存将无用。 我们通过块固定测量了高达 20 倍的速度提升。

您将需要访问具有存档数据的节点才能正常工作。 这就是我们推荐 [Alchemy](https://alchemyapi.io/) 的原因，因为他们的免费计划包括存档数据。

要固定块号：
```
networks: {
  hardhat: {
    forking: {
      url: "https://eth-mainnet.alchemyapi.io/v2/<key>",
      blockNumber: 14390000
    }
  }
}
```
如果您正在使用`node`任务，您还可以使用 --fork-block-number 标志指定块号：
```
npx hardhat node --fork https://eth-mainnet.alchemyapi.io/v2/<key> --fork-block-number 14390000
```
### Custom HTTP headers
您可以添加额外的 HTTP 标头，这些标头将在对分叉节点发出的任何请求中使用。 这样做的一个原因是为了授权：您可以通过自定义 HTTP 标头使用不记名令牌，而不是在 URL 中包含您的凭据：
```
networks: {
  hardhat: {
    forking: {
      url: "https://ethnode.example.com",
      httpHeaders: {
        "Authorization": "Bearer <key>"
      }
    }
  }
}
```
### 冒充账户
Hardhat Network 允许您冒充任何地址。 这使您可以从该帐户发送交易，即使您无权访问其私钥。

最简单的方法是使用 ethers.getImpersonatedSigner 方法，它由 hardhat-ethers 插件添加到 ethers 对象：
```
const impersonatedSigner = await ethers.getImpersonatedSigner("0x1234567890123456789012345678901234567890");
await impersonatedSigner.sendTransaction(...);
```
或者，您可以使用 impersonateAccount 助手，然后获取该地址的签名者：
```
const helpers = require("@nomicfoundation/hardhat-network-helpers");

const address = "0x1234567890123456789012345678901234567890";
await helpers.impersonateAccount(address);
const impersonatedSigner = await ethers.getSigner(address);
```

### 自定义 Hardhat Network 的行为
一旦您获得了主网链状态的本地实例，下一步可能就是将该状态设置为测试的特定需求。 为此，您可以使用我们的 [Hardhat Network Helpers](https://hardhat.org/hardhat-network-helpers) 库，它允许您执行操作网络时间或修改帐户余额等操作。

### Resetting the fork
您可以在运行时操纵分叉以重置回新的分叉状态，从另一个块号分叉或通过调用 `hardhat_reset` 禁用分叉：
```
await network.provider.request({
  method: "hardhat_reset",
  params: [
    {
      forking: {
        jsonRpcUrl: "https://eth-mainnet.alchemyapi.io/v2/<key>",
        blockNumber: 14390000,
      },
    },
  ],
});
```
您可以通过传递空参数来禁用分叉：
```
await network.provider.request({
  method: "hardhat_reset",
  params: [],
});
```

这将重置 Hardhat Network，以[此处](https://hardhat.org/hardhat-network/docs/reference#initial-state)描述的状态启动新实例。

### 使用自定义硬分叉历史
如果你正在分叉一个不寻常的网络，并且如果你想在从该网络检索到的历史区块的上下文中执行 EVM 代码，那么你将需要配置 Hardhat Network 以了解将哪些硬分叉应用到哪些区块。 （如果您正在分叉一个知名网络，Hardhat Network 将根据公共网络的已知历史自动选择正确的硬分叉来执行您的 EVM 代码，因此您可以安全地忽略此部分。）

要为 Hardhat Network 提供自定义链的硬分叉激活历史记录，请使用 `networks.hardhat.chains` 配置字段：
```
networks: {
  hardhat: {
    chains: {
      99: {
        hardforkHistory: {
          berlin: 10000000,
          london: 20000000,
        },
      }
    }
  }
}
```
在这种情况下，“历史区块”是其编号在您分叉出的区块之前的区块。 如果您尝试在没有硬分叉历史的情况下在历史区块的上下文中运行代码，则会抛出错误。 大多数公共网络的已知硬分叉历史被假定为默认值。

如果您在非历史区块的上下文中运行代码，那么 Hardhat Network 将简单地使用其配置中 hardfork 字段指定的硬分叉，例如 networks: { hardhat: { hardfork: "london" } }，而不是咨询 硬分叉历史配置。

See also [the chains entry in the Hardhat Network configuration reference](https://hardhat.org/hardhat-network/docs/reference#chains).

### Troubleshooting
#### “项目 ID 无权访问存档状态”
在没有存档插件的情况下使用 Infura 时，您将只能访问最近区块中的区块链状态。 为避免此问题，您可以使用本地存档节点或提供存档数据的服务，如 Alchemy 或 Infura。

### See also
有关受支持内容的完整详细信息，请参阅 [Hardhat 网络配置参考](https://hardhat.org/hardhat-network/docs/reference#config)。