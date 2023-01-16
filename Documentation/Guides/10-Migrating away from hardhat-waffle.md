过去我们推荐的设置包括使用我们的 [hardhat-waffle](https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-waffle) 插件的 [Waffle](https://getwaffle.io/)。

我们现在建议改用我们的 [Hardhat Chai Matchers](https://hardhat.org/hardhat-chai-matchers) 和 [Hardhat Network Helpers](https://hardhat.org/hardhat-network-helpers)。

迁移到这些包只需要几分钟，因为它们被设计为直接替代品。 如果这样做，您将获得更多功能，例如支持 Solidity 自定义错误和原生 bigint 支持，以及更可靠的测试体验。

要了解如何开始使用它们，请阅读[本指南](https://hardhat.org/hardhat-chai-matchers/docs/migrate-from-waffle)。

#### Using the Hardhat Toolbox
您可以通过安装 [@nomicfoundation/hardhat-toolbox](https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-toolbox) 获得我们推荐的设置，这是一个拥有您所需一切的插件。

使用它时，您将能够：
- 使用 [ethers.js](https://docs.ethers.io/v5/) 和 [hardhat-ethers](https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-ethers) 插件部署合约并与之交互。
- 使用 [Mocha](https://mochajs.org/)、[Chai](https://chaijs.com/) 和我们自己的 [Hardhat Chai Matchers](https://hardhat.org/hardhat-chai-matchers) 插件测试您的合约。
- 通过我们的 [Hardhat Network Helpers](https://hardhat.org/hardhat-network-helpers) 与 Hardhat Network 互动。
- 使用 [hardhat-etherscan](https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-etherscan) 插件验证合约的源代码。
- 使用 [hardhat-gas-reporter](https://github.com/cgewecke/hardhat-gas-reporter) 插件获取有关您的合约所用气体的指标。
- 使用 [solidity-coverage](https://github.com/sc-forks/solidity-coverage) 衡量你的测试覆盖率
- 而且，如果您使用的是 TypeScript，请使用 [Typechain](https://github.com/dethcrypto/TypeChain/) 为您的合同获取类型绑定。