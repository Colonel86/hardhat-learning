> Hardhat for Visual Studio Code 是官方的 Hardhat 扩展，它为 VSCode 添加了对 Solidity 的高级支持。 如果您使用 Visual Studio Code，请尝试一下！

您可以使用其 Mocha 集成扩展之一从 Visual Studio Code 运行测试。 我们推荐使用 [Mocha 测试资源管理器](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)。

要使用 Mocha 测试资源管理器，您需要安装它并按照这些说明进行操作。

通过运行以下命令在本地安装 Mocha：
```
npm install --save-dev mocha
```
然后，您只需要在项目的根目录中创建一个名为 `.mocharc.json` 的文件，其中包含以下内容：
```
{
  "require": "hardhat/register",
  "timeout": 40000
}
```
> 直接从 Visual Studio Code 运行测试不会自动编译你的合约。 确保手动编译它们。

### Running TypeScript tests
如果您使用 TypeScript 编写测试，则应改用此 `.mocharc.json`：
```
{
  "require": "hardhat/register",
  "timeout": 40000,
  "_": ["test/**/*.ts"]
}
```