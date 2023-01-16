在本指南中，我们将完成使用 Hardhat 创建脚本的步骤。 有关使用 Hardhat 的一般概述，请参阅[入门指南](https://hardhat.org/hardhat-runner/docs/getting-started)。

您可以编写自己的自定义脚本来使用 Hardhat 的所有功能。 一个典型的用例是为您的智能合约编写部署脚本。

有两种方法可以编写访问 [Hardhat 运行时环境](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment)的脚本。

#### Running scripts with the Hardhat CLI
您可以编写脚本来访问 [Hardhat Runtime Environment](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment) 的属性作为全局变量。

这些脚本必须通过 Hardhat 运行：npx hardhat run script.js。

这使得移植为其他工具开发的脚本并将变量注入全局状态变得容易。

#### 独立脚本：使用 Hardhat 作为库
第二个选项利用 Hardhat 的架构来提供更大的灵活性。 Hardhat 被设计为一个库，允许您发挥创造力并构建独立的 CLI 工具来访问您的开发环境。 这意味着通过简单地requiring它：
```
const hre = require("hardhat");
```
您可以访问所有任务和插件。 要运行这些脚本，您只需通过节点：`node script.js`。

为了尝试这一点，让我们看一个新的 Hardhat 项目。 运行 npx hardhat 并完成创建 JavaScript 项目的步骤。 完成后，您的项目目录应如下所示：
```
$ ls -l
total 400
drwxr-xr-x    3 fzeoli  staff      96 Jul 30 15:27 contracts
-rw-r--r--    1 fzeoli  staff     195 Jul 30 15:27 hardhat.config.js
drwxr-xr-x  502 fzeoli  staff   16064 Jul 30 15:31 node_modules
-rw-r--r--    1 fzeoli  staff  194953 Jul 30 15:31 package-lock.json
-rw-r--r--    1 fzeoli  staff     365 Jul 30 15:31 package.json
drwxr-xr-x    3 fzeoli  staff      96 Jul 30 15:27 scripts
drwxr-xr-x    3 fzeoli  staff      96 Jul 30 15:27 test
```
在 `scripts/` 中你会发现 `deploy.js`。 通读它的注释以更好地了解它的作用。

```
// We require the Hardhat Runtime Environment explicitly here. This is optional
// but useful for running the script in a standalone fashion through `node <script>`.
//
// You can also run a script with `npx hardhat run <script>`. If you do that, Hardhat
// will compile your contracts, add the Hardhat Runtime Environment's members to the
// global scope, and execute the script.
const hre = require("hardhat");

async function main() {
  const currentTimestampInSeconds = Math.round(Date.now() / 1000);
  const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
  const unlockTime = currentTimestampInSeconds + ONE_YEAR_IN_SECS;

  const lockedAmount = hre.ethers.utils.parseEther("1");

  const Lock = await hre.ethers.getContractFactory("Lock");
  const lock = await Lock.deploy(unlockTime, { value: lockedAmount });

  await lock.deployed();

  console.log(
    `Lock with 1 ETH and unlock timestamp ${unlockTime} deployed to ${lock.address}`
  );
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

Now run the script:
```
$ node scripts/deploy.js
Lock with 1 ETH deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```
通过访问顶部的 Hardhat 运行时环境，您可以独立运行脚本。

当通过 npx hardhat run 调用时，Hardhat 总是运行编译任务，但在独立的方式中，您可能希望手动调用编译以确保所有内容都已编译。 这可以通过调用 hre.run("compile") 来完成。 在 main 函数的开头添加以下行，然后使用节点重新运行脚本：
```
await hre.run("compile");
```

```
$ node scripts/deploy.js
Nothing to compile
Lock with 1 ETH deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

#### Hardhat arguments
运行独立脚本时，您仍然可以将参数传递给 Hardhat。 这是通过设置环境变量来完成的。 这些都是：
- `HARDHAT_NETWORK`：设置要连接的网络。
- `HARDHAT_SHOW_STACK_TRACES`：启用预期错误的 JavaScript 堆栈跟踪。
- `HARDHAT_VERBOSE`：启用 Hardhat 详细日志记录。
- `HARDHAT_MAX_MEMORY`：设置 Hardhat 可以使用的最大内存量。

例如，您可以执行 HARDHAT_NETWORK=localhost node script.js，而不是执行 npx hardhat --network localhost run script.js。