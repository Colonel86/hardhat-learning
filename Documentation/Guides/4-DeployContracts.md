Deploying your contracts
在部署方面，目前还没有为 Hardhat 实现部署系统的官方插件。 我们正在做这件事。

同时，我们建议使用脚本或使用 [hardhat-deploy](https://github.com/wighawag/hardhat-deploy/tree/master) 社区插件部署您的智能合约。 您可以使用如下部署脚本从示例项目部署 `Lock` 合约：

```
import { ethers } from "hardhat";

async function main() {
  const currentTimestampInSeconds = Math.round(Date.now() / 1000);
  const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
  const unlockTime = currentTimestampInSeconds + ONE_YEAR_IN_SECS;

  const lockedAmount = ethers.utils.parseEther("1");

  const Lock = await ethers.getContractFactory("Lock");
  const lock = await Lock.deploy(unlockTime, { value: lockedAmount });

  await lock.deployed();

  console.log(`Lock with 1 ETH and unlock timestamp ${unlockTime} deployed to ${lock.address}`);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```
您可以按照以下步骤在`localhost`网络中部署：
1. 启动[local node](https://hardhat.org/hardhat-runner/docs/getting-started#connecting-a-wallet-or-dapp-to-hardhat-network)
```
npx hardhat node
```
2. 打开一个新终端并在`localhost`网络中部署智能合约
```
npx hardhat run --network localhost scripts/deploy.ts
```

作为一般规则，您可以使用以下方法从 Hardhat 配置中定位任何网络：
```
npx hardhat run --network <your-network> scripts/deploy.js
```