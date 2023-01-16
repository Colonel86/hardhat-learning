# Verifying your contracts
合同准备就绪后，下一步就是将其部署到实时网络并验证其源代码。

验证合约意味着公开其源代码以及您使用的编译器设置，这允许任何人编译它并将生成的字节码与部署在链上的字节码进行比较。 在像以太坊这样的开放平台中，这样做非常重要。

在本指南中，我们将解释如何在 [Etherscan](https://etherscan.io/) 浏览器中执行此操作，但还有其他方法可以验证合约，例如使用 [Sourcify](https://sourcify.dev/)。

#### 从 Etherscan 获取 API 密钥
您首先需要的是来自 Etherscan 的 API 密钥。 要获得一个，请访问他们的[站点](https://etherscan.io/login)，登录（如果没有帐户，则创建一个帐户）并打开“API keys”选项卡。 然后单击“add”按钮并为您正在创建的 API 密钥命名（例如“Hardhat”）。 之后，您将在列表中看到新创建的密钥。

打开您的 Hardhat 配置并添加您刚刚创建的 API key：
```
export default {
  // ...rest of the config...
  etherscan: {
    apiKey: "ABCDE12345ABCDE12345ABCDE123456789",
  },
};
```

#### 在 Goerli 测试网中部署和验证合约
我们将使用 [Goerli testnet](https://ethereum.org/en/developers/docs/networks/#goerli) 来部署和验证我们的合约，因此您需要在 Hardhat 配置中添加该网络。 这里我们使用 Alchemy 连接到网络，但如果需要，您可以使用替代的 JSON-RPC URL，例如 Infura。
```
// Go to https://www.alchemyapi.io, sign up, create
// a new App in its dashboard, and replace "KEY" with its key
const ALCHEMY_API_KEY = "KEY";

// Replace this private key with your Goerli account private key.
// To export your private key from Metamask, open Metamask and
// go to Account Details > Export Private Key.
// Beware: NEVER put real Ether into testing accounts
const GOERLI_PRIVATE_KEY = "YOUR GOERLI PRIVATE KEY";

export default {
  // ...rest of your config...
  networks: {
    goerli: {
      url: `https://eth-goerli.alchemyapi.io/v2/${ALCHEMY_API_KEY}`,
      accounts: [GOERLI_PRIVATE_KEY],
    },
  },
};
```
要在 Goerli 上部署，您需要将一些 Goerli 以太币发送到将要进行部署的地址。 您可以从水龙头获得测试网以太币，这是一项免费分发测试 ETH 的服务。 以下是 Goerli 的一些内容：
- [Chainlink faucet](https://faucets.chain.link/)
- [Alchemy Goerli Faucet](https://goerlifaucet.com/)

现在您已准备好deploy您的contract，但首先我们要使contract的源代码独一无二。 我们需要这样做的原因是上一节的示例代码已经在 Goerli 中进行了verify，因此如果您尝试verify它，您将得到一个错误。

打开您的合同并添加带有独特内容的评论，例如您的 GitHub 用户名。 请记住，无论您在此处包含什么，都将像其余代码一样，在 Etherscan 上公开可用：
```
// Author: @janedoe
contract Lock {
```
您现在可以使用新添加的 Goerli 网络运行部署脚本：
```
npx hardhat run scripts/deploy.ts --network goerli
```
记下地址和解锁时间并使用它们运行`verify`任务：
```
npx hardhat verify --network goerli <address> <unlock time>
```
> 如果你收到一条错误消息说地址没有字节码，这可能意味着 Etherscan 还没有为你的合约建立索引。 在这种情况下，请稍等片刻，然后重试。

任务成功执行后，您将看到一个链接，指向您的合同的公开验证代码。

要了解有关验证的更多信息，请阅读 [hardhat-etherscan](https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-etherscan) 文档。