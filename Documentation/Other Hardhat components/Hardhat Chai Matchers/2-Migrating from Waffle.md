本页介绍了如何从 Waffle 迁移到 Hardhat Chai Matchers，以及这样做的优势。 迁移应该只需要几分钟。

### How to migrate
`@nomicfoundation/hardhat-chai-matchers` 插件旨在替代`@nomiclabs/hardhat-waffle`插件。 要迁移，请按照以下说明进行操作：
1. 卸载 @nomiclabs/hardhat-waffle 和 ethereum-waffle 包：
```
npm uninstall @nomiclabs/hardhat-waffle ethereum-waffle
```
2. 然后安装 Hardhat Chai Matchers 插件：
```
npm install --save-dev @nomicfoundation/hardhat-chai-matchers
```
3. 在您的 Hardhat 配置中，导入 Hardhat Chai Matchers 插件并删除 `hardhat-waffle` 插件：
```
- import "@nomiclabs/hardhat-waffle";
+ import "@nomicfoundation/hardhat-chai-matchers";
```
4. 如果您没有显式导入 @nomiclabs/hardhat-ethers 插件（因为 Hardhat Waffle 插件已经导入它），则将其添加到您的配置中：
```
import "@nomiclabs/hardhat-ethers";
```
正在寻找 Waffle 的 loadFixture 的替代品？ 您可以在 [Hardhat Network Helpers](https://hardhat.org/hardhat-network-helpers/docs/reference#fixtures) 中找到我们的版本。

