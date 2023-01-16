[@nomicfoundation/hardhat-network-helpers](https://www.npmjs.com/package/@nomicfoundation/hardhat-network-helpers) 为 [Hardhat Network](https://hardhat.org/hardhat-network) 的 JSON-RPC 功能提供了一个方便的 JavaScript 接口。

Hardhat Network 主要通过其 JSON-RPC API 公开其自定义功能。 请参阅其[参考文档](https://hardhat.org/hardhat-network/docs/reference#hardhat-network-methods)中提供的大量方法。 然而，对于易于阅读的测试和简短的脚本，与 JSON-RPC API 的接口过于嘈杂，需要冗长的语法和输入和输出数据的大量转换。

该软件包提供了方便的功能，可以快速轻松地与 Hardhat Network 进行交互。 设施包括挖掘达到特定时间戳或区块编号的区块的能力、操纵账户属性（余额、代码、随机数、存储）的能力、模拟特定账户的能力以及拍摄和恢复快照的能力。

### Installation
```
npm install --save-dev @nomicfoundation/hardhat-network-helpers
```
### Usage
要使用网络助手，只需将其导入您要使用的位置：
import { mine } from "@nomicfoundation/hardhat-network-helpers";
```
async function main() {
  // instantly mine 1000 blocks
  await mine(1000);
}
```
由于这不是 Hardhat 插件，因此您无需在配置中导入整个包。 您可以简单地在需要使用它们的地方导入每个特定的辅助函数。

有关此包提供的所有助手的完整列表，请参阅[参考文档](https://hardhat.org/hardhat-network-helpers/docs/reference)。