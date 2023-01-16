## Setting up a project
Hardhat 项目是安装了 hardhat 包和 hardhat.config.js 文件的 Node.js 项目。

要初始化 Node.js 项目，您可以使用 npm 或 yarn。 我们建议使用 npm 7 或更高版本：
```
npm init -y
```
然后你需要安装安全帽：
```
npm install --save-dev hardhat
```
如果你现在运行 npx hardhat，你会看到一些选项来帮助创建项目：
```
$ npx hardhat
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

Welcome to Hardhat v2.10.0

? What do you want to do? …
▸ Create a JavaScript project
  Create a TypeScript project
  Create an empty hardhat.config.js
  Quit
```
如果您选择创建一个空的 hardhat.config.js，Hardhat 将创建一个 hardhat.config.js，如下所示：
```
/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.9",
};
```
这足以使用默认项目结构运行 Hardhat。

#### 示例 Hardhat 项目
如果您选择创建 JavaScript 项目，一个简单的项目创建向导将询问您一些问题。 之后，向导将创建一些目录和文件并安装必要的依赖项。 这些依赖项中最重要的是 Hardhat Toolbox，这是一个插件，它捆绑了开始使用 Hardhat 所需的所有东西。

初始化的项目结构如下：
```
contracts/
scripts/
test/
hardhat.config.js
```
这些是 Hardhat 项目的默认路径。
- `contracts/` 是你的合同的源文件应该在的地方。
- `test/` 是你的测试应该去的地方。
- `scripts/` 是简单的自动化脚本所在的位置。
  
如果您需要更改这些路径，请查看[路径配置部分](https://hardhat.org/hardhat-runner/docs/config#path-configuration)。

#### Testing
在测试您的合约时，示例项目带有一些有用的功能：

- 内置的 [Hardhat Network](https://hardhat.org/hardhat-network/docs) 作为要测试的开发网络，以及操作该网络的 Hardhat Network Helpers 库。
- [Mocha](https://mochajs.org/) 作为测试运行器，[Chai](https://chaijs.com/) 作为断言库，[Hardhat Chai Matchers](https://hardhat.org/hardhat-chai-matchers) 用于扩展 Chai 与合约相关的功能。
- [ether.js](https://docs.ethers.io/v5/) 库与网络和合同进行交互。

如果您需要使用外部网络，如以太坊测试网、主网或其他特定节点软件，您可以使用 `hardhat.config.js` 中导出对象中的`networks`配置条目进行设置，这就是 Hardhat 项目管理设置的方式 .

您可以使用 --network CLI 参数来快速更改网络。

查看[网络配置部分](https://hardhat.org/hardhat-runner/docs/config#networks-configuration)，了解有关设置不同网络的更多信息。

#### Plugins and dependencies
Hardhat 的大部分功能都来自插件，因此请查看官方列表的[插件部分](https://hardhat.org/hardhat-runner/plugins)，看看是否有您感兴趣的插件。

要使用插件，第一步始终是使用 npm 或 yarn 安装它，然后在配置文件中要求它：
```
import "@nomicfoundation/hardhat-toolbox";

export default {
  solidity: "0.8.9",
};
```
插件对于 Hardhat 项目至关重要，因此请务必检查所有可用的插件并构建您自己的插件！

#### Setting up your editor
[Hardhat for Visual Studio Code](https://hardhat.org/hardhat-vscode) 是官方的 Hardhat 扩展，它为 VSCode 添加了对 Solidity 的高级支持。 如果您使用 Visual Studio Code，请尝试一下！
