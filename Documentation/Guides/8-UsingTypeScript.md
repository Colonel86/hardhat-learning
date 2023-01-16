# Using TypeScript
在本指南中，我们将完成使用 TypeScript 获取 Hardhat 项目的步骤。 这意味着您可以在 [TypeScript](https://www.typescriptlang.org/) 中编写 Hardhat 配置、任务、脚本和测试。

有关使用 Hardhat 的一般概述，请参阅[入门指南](https://hardhat.org/hardhat-runner/docs/getting-started)。

### 启用 TypeScript 支持
如果您的配置文件以 `.ts` 结尾并使用有效的 TypeScript 编写，Hardhat 将自动启用其 TypeScript 支持。 这需要一些更改才能正常工作。

#### Installing dependencies
> 如果你安装了@nomicfoundation/hardhat-toolbox使用 npm 7 或更高版本，您无需执行这些步骤。

Hardhat 在底层使用 TypeScript 和 ts-node，所以你需要安装它们。 为此，请打开您的终端，转到您的 Hardhat 项目，然后运行：
```
npm install --save-dev ts-node typescript
```
为了能够用 TypeScript 编写测试，您还需要这些包：
```
npm install --save-dev chai @types/node @types/mocha @types/chai
```
#### TypeScript configuration
您可以轻松地将 JavaScript Hardhat 配置文件转换为 TypeScript 配置文件。 让我们看看这是如何从一个新的 Hardhat 项目开始的。

打开您的终端，转到一个空文件夹，运行 `npx hardhat`，然后完成创建 JavaScript 项目的步骤。 完成后，您的项目目录应如下所示：
```
$ ls -l
total 1200
drwxr-xr-x    3 pato  wheel      96 Oct 20 12:50 contracts/
-rw-r--r--    1 pato  wheel     567 Oct 20 12:50 hardhat.config.js
drwxr-xr-x  434 pato  wheel   13888 Oct 20 12:52 node_modules/
-rw-r--r--    1 pato  wheel  604835 Oct 20 12:52 package-lock.json
-rw-r--r--    1 pato  wheel     460 Oct 20 12:52 package.json
drwxr-xr-x    3 pato  wheel      96 Oct 20 12:50 scripts/
drwxr-xr-x    3 pato  wheel      96 Oct 20 12:50 test/
```
然后，您应该按照上面[安装依赖项部分](https://hardhat.org/hardhat-runner/docs/guides/typescript#installing-dependencies)中提到的步骤进行操作。

现在，我们要将配置文件从 `hardhat.config.js` 重命名为 `hardhat.config.ts`，只需运行：
```
mv hardhat.config.js hardhat.config.ts
```
我们需要对您的配置进行一次更改以使其与 TypeScript 一起工作：您必须使用 `import/export` 而不是 `require/module.exports`。

通过使用 TypeScript，您还可以键入您的配置，这将使您免于拼写错误和其他错误。

例如，示例项目的配置由此而来：
```
require("@nomicfoundation/hardhat-toolbox");

/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.17",
};
```
into this:
```
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";

const config: HardhatUserConfig = {
  solidity: "0.8.17",
};

export default config;
```
最后，您需要创建一个tsconfig.json文件。 这是我们推荐的：
```
{
  "compilerOptions": {
    "target": "es2020",
    "module": "commonjs",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```
这真的就是它所需要的。 现在您可以在 TypeScript 中编写配置、测试、任务和脚本。
#### Type-checking your project
出于性能原因，Hardhat 不会在您运行任务时对您的项目进行类型检查。 您可以使用 --typecheck 标志显式启用类型检查。

例如，如果您运行 `npx hardhat test` 并且您的其中一个测试出现编译错误，那么无论如何都会执行测试任务。 但是，如果您运行 `npx hardhat test --typecheck`，Hardhat 将在开始运行测试之前检测并抛出编译错误。

由于类型检查会增加大量开销，我们建议仅在您的 CI 或预提交/预推送挂钩中执行此操作。

#### 在 TypeScript 中编写测试和脚本
使用 JavaScript 时，[Hardhat Runtime Environment](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment) 中的所有属性都被注入到全局范围中。 当使用 TypeScript 时，全局范围内没有任何内容可用，您将需要使用 import { ethers } from "hardhat" 显式导入所有内容。

按照[入门指南](https://hardhat.org/hardhat-runner/docs/getting-started)创建一个 TypeScript 项目，以获得有关如何使用 TypeScript 编写测试和脚本的完整示例。

#### Type-safe smart contract interactions
> 如果你安装了@nomicfoundation/hardhat-toolbox你可以跳过这部分，因为它包括
@typechain/hardhat

如果您希望 Hardhat 为您的智能合约生成类型，您应该安装和使用@typechain/hardhat. 它根据 ABI 生成输入文件 (*.d.ts)，并且几乎不需要配置。

#### 支持路径映射
Typescript 允许通过[paths](https://www.typescriptlang.org/tsconfig#paths)配置选项定义自定义[path mappings](https://www.typescriptlang.org/docs/handbook/module-resolution.html#path-mapping)：

```
{
  compilerOptions: {
    paths: { "~/*": ["src/*"] },
    // ...Other compilerOptions
  },
}
```
要在运行 Hardhat 测试或脚本时支持此选项，您需要安装包 [tsconfig-paths](https://www.npmjs.com/package/tsconfig-paths) 并将其注册到您的 `hardhat.config.ts` 中：
```
import { HardhatUserConfig } from "hardhat/config";

// This adds support for typescript paths mappings
import "tsconfig-paths/register";

const config: HardhatUserConfig = {
  // Your type-safe config goes here
};

export default config;
```

#### 直接使用 ts-node 运行测试和脚本
在没有 CLI 的情况下运行 Hardhat 脚本时，您需要使用 `ts-node` 的 `--files` 标志。

这也可以通过 `TS_NODE_FILES=true` 启用。