## 概述
Hardhat 是Ethereum软件的开发环境。 它由用于编辑、编译、调试和部署智能合约和 dApp 的不同组件组成，所有这些组件共同创建一个完整的开发环境。

Hardhat Runner 是您在使用 Hardhat 时与之交互的主要组件。 它是一个灵活且可扩展的任务运行器，可帮助您管理和自动化开发智能合约和 dApp 所固有的重复性任务。

Hardhat Runner 是围绕任务和插件的概念设计的。 每次从命令行运行 Hardhat 时，您都在运行一个任务。 例如，npx hardhat compile 运行内置的编译任务。 任务可以调用其他任务，允许定义复杂的工作流。 用户和插件可以覆盖现有任务，使这些工作流程可定制和可扩展。

本指南将引导您完成我们推荐设置的安装，但由于 Hardhat 的大部分功能都来自插件，您可以自由的自定义它或选择完全不同的路径。

## 安装
>提示
>
>Hardhat for Visual Studio Code 是官方的 Hardhat 扩展，它为 VSCode 添加了对 Solidity 的高级支持。 如果您使用 Visual Studio Code，请尝试一下！

Hardhat 通过项目中的本地安装来使用。 这样您的环境将是可重现的，并且您将避免将来的版本冲突。

要安装它，您需要通过转到一个空文件夹、运行 npm init 并按照其说明创建一个 npm 项目。 您可以使用其他包管理器，如 yarn，但我们建议您使用 npm 7 或更高版本，因为它可以更简单地安装 Hardhat 插件。

项目准备就绪后，您应该运行
```
npm install --save-dev hardhat
```
要使用本地安装的 Hardhat，您需要使用 npx 来运行它（即 npx hardhat）。
## 快速开始
>提示
>
>如果您使用的是 Windows，我们强烈建议您使用 WSL 2 来遵循本指南。

我们将探索创建 Hardhat 项目的基础知识，其中包含示例合约、该合约的测试以及用于部署它的脚本。

要创建示例项目，请在项目文件夹中运行 npx hardhat：
```
$ npx hardhat
Need to install the following packages:
  hardhat@2.12.4
Ok to proceed? (y) y
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.12.4 👷‍

? What do you want to do? … 
❯ Create a JavaScript project
  Create a TypeScript project
  Create an empty hardhat.config.js
  Quit

  ... ...
  ... ...
  ... ...

✨ Project created ✨

See the README.md file for some example tasks you can run

Give Hardhat a star on Github if you're enjoying it! 💞✨

     https://github.com/NomicFoundation/hardhat

Please take a moment to complete the 2022 Solidity Survey: https://hardhat.org/solidity-survey-2022
```

让我们创建 JavaScript 或 TypeScript 项目并通过这些步骤来编译、测试和部署示例合约。 我们建议使用 TypeScript，但如果您不熟悉它，请选择 JavaScript。

#### Running tasks
要首先快速了解可用的内容和正在发生的事情，请在项目文件夹中运行 npx hardhat：
、、、
$ npx hardhat
Hardhat version 2.12.4

Usage: hardhat [GLOBAL OPTIONS] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:

  --config              A Hardhat config file. 
  --emoji               Use emoji in messages. 
  --flamegraph          Generate a flamegraph of your Hardhat tasks 
  --help                Shows this message, or a task's help if its name is provided 
  --max-memory          The maximum amount of memory that Hardhat can use. 
  --network             The network to connect to. 
  --show-stack-traces   Show stack traces (always enabled on CI servers). 
  --tsconfig            A TypeScript config file. 
  --typecheck           Enable TypeScript type-checking of your scripts/tests 
  --verbose             Enables Hardhat verbose logging 
  --version             Shows hardhat's version. 


AVAILABLE TASKS:

  check                 Check whatever you need
  clean                 Clears the cache and deletes all artifacts
  compile               Compiles the entire project, building all artifacts
  console               Opens a hardhat console
  coverage              Generates a code coverage report for tests
  flatten               Flattens and prints contracts and their dependencies
  gas-reporter:merge
  help                  Prints this message
  node                  Starts a JSON-RPC server on top of Hardhat Network
  run                   Runs a user-defined script after compiling the project
  test                  Runs mocha tests
  typechain             Generate Typechain typings for compiled contracts
  verify                Verifies contract on Etherscan

To get help for a specific task run: npx hardhat help [task]
、、、

可用任务列表包括内置任务以及任何已安装插件附带的任务。 npx hardhat 是您找出可以运行哪些任务的起点。

#### 编译你的contracts
接下来，如果您查看 contracts/ 文件夹，您会看到 Lock.sol：
```
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

// Uncomment this line to use console.log
// import "hardhat/console.sol";

contract Lock {
    uint public unlockTime;
    address payable public owner;

    event Withdrawal(uint amount, uint when);

    constructor(uint _unlockTime) payable {
        require(
            block.timestamp < _unlockTime,
            "Unlock time should be in the future"
        );

        unlockTime = _unlockTime;
        owner = payable(msg.sender);
    }

    function withdraw() public {
        // Uncomment this line, and the import of "hardhat/console.sol", to print a log in your terminal
        // console.log("Unlock time is %o and block timestamp is %o", unlockTime, block.timestamp);

        require(block.timestamp >= unlockTime, "You can't withdraw yet");
        require(msg.sender == owner, "You aren't the owner");

        emit Withdrawal(address(this).balance, block.timestamp);

        owner.transfer(address(this).balance);
    }
}
```
要编译它，只需运行：
```
$ npx hardhat compile
Generating typings for: 1 artifacts in dir: typechain-types for target: ethers-v5
Successfully generated 6 typings!
Compiled 1 Solidity file successfully
```
如果您创建了 TypeScript 项目，此任务还将使用 [TypeChain](https://www.npmjs.com/package/typechain) 生成 TypeScript 绑定。

```
$ cd typechain-types
$ ls
Lock.ts      common.ts    factories    hardhat.d.ts index.ts
```
#### Testing your contracts
您的项目附带使用 [Mocha](https://mochajs.org/)、[Chai](https://www.chaijs.com/) 和 [Ethers.js](https://docs.ethers.io/v5) 的测试。

如果你查看 test/ 文件夹，你会看到一个测试文件：
==本文档只关注typescript，如果想关注javascript，请前往[Hardhat官网](https://hardhat.org/hardhat-runner/docs/getting-started#overview)==
```
import { time, loadFixture } from "@nomicfoundation/hardhat-network-helpers";
import { anyValue } from "@nomicfoundation/hardhat-chai-matchers/withArgs";
import { expect } from "chai";
import { ethers } from "hardhat";

describe("Lock", function () {
  // We define a fixture to reuse the same setup in every test.
  // We use loadFixture to run this setup once, snapshot that state,
  // and reset Hardhat Network to that snapshot in every test.
  async function deployOneYearLockFixture() {
    const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
    const ONE_GWEI = 1_000_000_000;

    const lockedAmount = ONE_GWEI;
    const unlockTime = (await time.latest()) + ONE_YEAR_IN_SECS;

    // Contracts are deployed using the first signer/account by default
    const [owner, otherAccount] = await ethers.getSigners();

    const Lock = await ethers.getContractFactory("Lock");
    const lock = await Lock.deploy(unlockTime, { value: lockedAmount });

    return { lock, unlockTime, lockedAmount, owner, otherAccount };
  }

  describe("Deployment", function () {
    it("Should set the right unlockTime", async function () {
      const { lock, unlockTime } = await loadFixture(deployOneYearLockFixture);

      expect(await lock.unlockTime()).to.equal(unlockTime);
    });

    it("Should set the right owner", async function () {
      const { lock, owner } = await loadFixture(deployOneYearLockFixture);

      expect(await lock.owner()).to.equal(owner.address);
    });

    it("Should receive and store the funds to lock", async function () {
      const { lock, lockedAmount } = await loadFixture(
        deployOneYearLockFixture
      );

      expect(await ethers.provider.getBalance(lock.address)).to.equal(
        lockedAmount
      );
    });

    it("Should fail if the unlockTime is not in the future", async function () {
      // We don't use the fixture here because we want a different deployment
      const latestTime = await time.latest();
      const Lock = await ethers.getContractFactory("Lock");
      await expect(Lock.deploy(latestTime, { value: 1 })).to.be.revertedWith(
        "Unlock time should be in the future"
      );
    });
  });

  describe("Withdrawals", function () {
    describe("Validations", function () {
      it("Should revert with the right error if called too soon", async function () {
        const { lock } = await loadFixture(deployOneYearLockFixture);

        await expect(lock.withdraw()).to.be.revertedWith(
          "You can't withdraw yet"
        );
      });

      it("Should revert with the right error if called from another account", async function () {
        const { lock, unlockTime, otherAccount } = await loadFixture(
          deployOneYearLockFixture
        );

        // We can increase the time in Hardhat Network
        await time.increaseTo(unlockTime);

        // We use lock.connect() to send a transaction from another account
        await expect(lock.connect(otherAccount).withdraw()).to.be.revertedWith(
          "You aren't the owner"
        );
      });

      it("Shouldn't fail if the unlockTime has arrived and the owner calls it", async function () {
        const { lock, unlockTime } = await loadFixture(
          deployOneYearLockFixture
        );

        // Transactions are sent using the first signer by default
        await time.increaseTo(unlockTime);

        await expect(lock.withdraw()).not.to.be.reverted;
      });
    });

    describe("Events", function () {
      it("Should emit an event on withdrawals", async function () {
        const { lock, unlockTime, lockedAmount } = await loadFixture(
          deployOneYearLockFixture
        );

        await time.increaseTo(unlockTime);

        await expect(lock.withdraw())
          .to.emit(lock, "Withdrawal")
          .withArgs(lockedAmount, anyValue); // We accept any value as `when` arg
      });
    });

    describe("Transfers", function () {
      it("Should transfer the funds to the owner", async function () {
        const { lock, unlockTime, lockedAmount, owner } = await loadFixture(
          deployOneYearLockFixture
        );

        await time.increaseTo(unlockTime);

        await expect(lock.withdraw()).to.changeEtherBalances(
          [owner, lock],
          [lockedAmount, -lockedAmount]
        );
      });
    });
  });
});
```
您可以使用 npx hardhat test 运行测试：
```
 Lock
    Deployment
      ✔ Should set the right unlockTime (1776ms)
      ✔ Should set the right owner
      ✔ Should receive and store the funds to lock
      ✔ Should fail if the unlockTime is not in the future
    Withdrawals
      Validations
        ✔ Should revert with the right error if called too soon
        ✔ Should revert with the right error if called from another account
        ✔ Shouldn't fail if the unlockTime has arrived and the owner calls it
      Events
        ✔ Should emit an event on withdrawals
      Transfers
        ✔ Should transfer the funds to the owner


  9 passing (2s)
```
#### 部署你的contracts
接下来，要部署contract，我们将使用 Hardhat script。

在 scripts/ 文件夹中，您将找到一个包含以下代码的文件：
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
您可以使用 npx hardhat run 运行它：
```
npx hardhat run scripts/deploy.ts
Lock with 1 ETH and unlock timestamp 1703561517 deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3
```
#### 将wallet 或 Dapp 连接到 Hardhat Network
默认情况下，Hardhat 将在启动时启动一个新的 Hardhat Network 内存实例。 也可以以独立方式运行 Hardhat Network，以便外部客户端可以连接到它。 这可能是 MetaMask、您的 Dapp 前端或脚本。

要以这种方式运行 Hardhat Network，请运行 npx hardhat node：
```
$ npx hardhat node
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========

WARNING: These accounts, and their private keys, are publicly known.
Any funds sent to them on Mainnet or any other live network WILL BE LOST.

Account #0: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1: 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d

Account #2: 0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC (10000 ETH)
Private Key: 0x5de4111afa1a4b94908f83103eb1f1706367c2e68ca870fc3fb9a804cdab365a

Account #3: 0x90F79bf6EB2c4f870365E785982E1f101E93b906 (10000 ETH)
Private Key: 0x7c852118294e51e653712a81e05800f419141751be58f605c371e15141b007a6

Account #4: 0x15d34AAf54267DB7D7c367839AAf71A00a2C6A65 (10000 ETH)
Private Key: 0x47e179ec197488593b187f80a00eb0da91f1b9d0b13f8733639f19c30a34926a

Account #5: 0x9965507D1a55bcC2695C58ba16FB37d819B0A4dc (10000 ETH)
Private Key: 0x8b3a350cf5c34c9194ca85829a2df0ec3153be0318b5e2d3348e872092edffba

Account #6: 0x976EA74026E726554dB657fA54763abd0C3a0aa9 (10000 ETH)
Private Key: 0x92db14e403b83dfe3df233f83dfa3a0d7096f21ca9b0d6d6b8d88b2b4ec1564e

Account #7: 0x14dC79964da2C08b23698B3D3cc7Ca32193d9955 (10000 ETH)
Private Key: 0x4bbbf85ce3377467afe5d46f804f221813b2bb87f24d81f60f1fcdbf7cbf4356

Account #8: 0x23618e81E3f5cdF7f54C3d65f7FBc0aBf5B21E8f (10000 ETH)
Private Key: 0xdbda1821b80551c9d65939329250298aa3472ba22feea921c0cf5d620ea67b97

Account #9: 0xa0Ee7A142d267C1f36714E4a8F75612F20a79720 (10000 ETH)
Private Key: 0x2a871d0798f97d79848a013d4936a73bf4cc922c825d33c1cf7073dff6d409c6

Account #10: 0xBcd4042DE499D14e55001CcbB24a551F3b954096 (10000 ETH)
Private Key: 0xf214f2b2cd398c806f84e317254e0f0b801d0643303237d97a22a48e01628897

Account #11: 0x71bE63f3384f5fb98995898A86B02Fb2426c5788 (10000 ETH)
Private Key: 0x701b615bbdfb9de65240bc28bd21bbc0d996645a3dd57e7b12bc2bdf6f192c82

Account #12: 0xFABB0ac9d68B0B445fB7357272Ff202C5651694a (10000 ETH)
Private Key: 0xa267530f49f8280200edf313ee7af6b827f2a8bce2897751d06a843f644967b1

Account #13: 0x1CBd3b2770909D4e10f157cABC84C7264073C9Ec (10000 ETH)
Private Key: 0x47c99abed3324a2707c28affff1267e45918ec8c3f20b8aa892e8b065d2942dd

Account #14: 0xdF3e18d64BC6A983f673Ab319CCaE4f1a57C7097 (10000 ETH)
Private Key: 0xc526ee95bf44d8fc405a158bb884d9d1238d99f0612e9f33d006bb0789009aaa

Account #15: 0xcd3B766CCDd6AE721141F452C550Ca635964ce71 (10000 ETH)
Private Key: 0x8166f546bab6da521a8369cab06c5d2b9e46670292d85c875ee9ec20e84ffb61

Account #16: 0x2546BcD3c84621e976D8185a91A922aE77ECEc30 (10000 ETH)
Private Key: 0xea6c44ac03bff858b476bba40716402b03e41b8e97e276d1baec7c37d42484a0

Account #17: 0xbDA5747bFD65F08deb54cb465eB87D40e51B197E (10000 ETH)
Private Key: 0x689af8efa8c651a91ad287602527f3af2fe9f6501a7ac4b061667b5a93e037fd

Account #18: 0xdD2FD4581271e230360230F9337D5c0430Bf44C0 (10000 ETH)
Private Key: 0xde9be858da4a475276426320d5e9262ecfc3ba460bfac56360bfa6c4c28b4ee0

Account #19: 0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199 (10000 ETH)
Private Key: 0xdf57089febbacf7ba0bc227dafbffa9fc08a93fdc68e1e42411a14efcf23656e

WARNING: These accounts, and their private keys, are publicly known.
Any funds sent to them on Mainnet or any other live network WILL BE LOST.
```
这将向 Hardhat Network 公开一个 JSON-RPC 接口。 要使用它，请将您的钱包或应用程序连接到 http://127.0.0.1:8545。

如果您想将 Hardhat 连接到此节点，例如针对它运行部署脚本，您只需使用 --network localhost 运行它。

要尝试此操作，请使用 npx hardhat node 启动一个节点，然后使用network选项重新运行部署脚本：
```
npx hardhat run scripts/deploy.ts --network localhost

Lock with 1 ETH and unlock timestamp 1703571256 deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3
```
下列为npx hardhat nod启动本地节点终端输出的内容：
```
eth_chainId
eth_accounts
eth_blockNumber
eth_chainId (2)
eth_estimateGas
eth_getBlockByNumber
eth_gasPrice
eth_sendTransaction
  Contract deployment: Lock
  Contract address:    0x5fbdb2315678afecb367f032d93f642f64180aa3
  Transaction:         0x5beadefcc8635db397858eb174c8cd3abd5c83e1489c8e311a87727e34d32c92
  From:                0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
  Value:               1 ETH
  Gas used:            326016 of 326016
  Block #1:            0x4f2d6f5ce343046594c964660bca16336ded46f4addac10a849c877c45c523a9

eth_chainId
eth_getTransactionByHash
eth_chainId
eth_getTransactionReceipt
```
恭喜！ 您已经在上述创建的本地网络创建了一个项目并编译、测试和部署了一个智能合约。

[在 GitHub 上](https://github.com/NomicFoundation/hardhat)为我们的存储库加注星标，向我们表达爱意！️