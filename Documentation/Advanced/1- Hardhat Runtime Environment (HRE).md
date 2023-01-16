### Overview
Hardhat 运行时环境，简称 HRE，是一个包含 Hardhat 在运行任务、测试或脚本时公开的所有功能的对象。 实际上，Hardhat 就是 HRE。

当您需要 Hardhat (const hardhat = require("hardhat")) 时，您将获得 HRE 的一个实例。

在初始化期间，Hardhat 配置文件实质上构建了一个要添加到 HRE 的列表。 这包括任务、配置和插件。 然后，当任务、测试或脚本运行时，HRE 始终存在并可用于访问其中包含的任何内容。

HRE 的职责是集中协调所有 Hardhat 组件。 这种架构允许插件注入在神罗可访问的任何地方都可用的功能。

### Using the HRE
默认情况下，HRE 允许您以编程方式访问任务运行器和配置系统，并导出 [EIP1193](https://eips.ethereum.org/EIPS/eip-1193) 兼容的以太坊提供程序。

插件可以扩展 HRE。 例如，[hardhat-ethers](https://github.com/NomicFoundation/hardhat/tree/main/packages/hardhat-ethers) 添加了一个 Ethers.js 实例，使其可用于任务、测试和脚本。

#### As global variables
在运行任务、测试或脚本之前，Hardhat 将 HRE 注入全局范围，将其所有字段转换为全局变量。 当任务执行完成时，这些全局变量将被删除，恢复它们的原始值（如果有的话）。

#### 明确地
不是每个人都喜欢神奇的全局变量，Hardhat 也不会强迫您使用它们。 一切都可以在任务、测试和脚本中明确完成。

在编写测试或脚本时，您可以使用 require("hardhat") 来导入 HRE。 您可以在从[任务外部访问 HRE 中阅读更多相关信息](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment#accessing-the-hre-from-outside-a-task)。

您可以在定义任务时显式导入配置 API，并显式接收 HRE 作为您的操作的参数。 您可以在[创建自己的任务](https://hardhat.org/hardhat-runner/docs/advanced/create-task)中阅读更多相关信息。

### 从任务外部访问 HRE
HRE 可以从任何 JavaScript 或 TypeScript 文件中使用。 为此，您只需使用 require("hardhat") 导入它。 您可以这样做以更好地控制您的开发工作流程、创建您自己的工具，或者将 Hardhat 与来自 node.js 生态系统的其他开发工具一起使用。

直接使用 [Mocha](https://www.npmjs.com/package/mocha) 而不是 `npx hardhat test` 运行测试可以通过在其中显式导入 HRE 来完成，如下所示：
```
const hre = require("hardhat");
const assert = require("assert");

describe("Hardhat Runtime Environment", function () {
  it("should have a config field", function () {
    assert.notEqual(hre.config, undefined);
  });
});
```

这样，为 Hardhat 编写的测试就是普通的 Mocha 测试。 这使您可以从您喜欢的编辑器中运行它们，而无需任何特定于 Hardhat 的插件。 例如，您可以[使用 Mocha Test Explorer 从 Visual Studio Code 运行它们](https://hardhat.org/hardhat-runner/docs/advanced/vscode-tests)。

###  Extending the HRE
HRE 仅提供用户和插件开发人员在 Hardhat 之上开始构建所需的核心功能。 在您的项目中使用它直接与以太坊交互可能比预期的要难一些。

当您使用更高级别的库（如 [Ethers.js](https://docs.ethers.io/) 或 [ethereum-waffle](https://www.npmjs.com/package/ethereum-waffle)）时，一切都会变得更容易，但这些库需要一些初始化才能工作，而且这可能会重复进行。

Hardhat 让您可以连接到 HRE 构造，并使用新功能对其进行扩展。 这样，您只需初始化所有内容一次，您的新功能或库将在使用 HRE 的任何地方可用。

您可以通过将 HRE 扩展器添加到队列中来完成此操作。 这个扩展器只是一个同步函数，它接收 HRE 并使用您的新功能向其添加字段。 这些新字段也将在运行时注入到全局范围内。

例如，将 Web3.js 实例添加到 HRE 可以通过安装 web3 包并将以下内容添加到 hardhat.config.js 来完成：
```
extendEnvironment((hre) => {
  const Web3 = require("web3");
  hre.Web3 = Web3;

  // hre.network.provider is an EIP1193-compatible provider.
  hre.web3 = new Web3(hre.network.provider);
});
```