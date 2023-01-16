#### Testing contracts
[编译合约](https://hardhat.org/hardhat-runner/docs/guides/compile-contracts)后，下一步是编写一些测试来验证它们是否按预期工作。

本指南解释了我们推荐的在 Hardhat 中测试合约的方法。 它依靠以太币连接到 Hardhat Network，并依靠 Mocha 和 Chai 进行测试。 它还使用我们的自定义 Chai 匹配器和我们的 Hardhat Network Helpers 来简化编写干净的测试代码。 这些包是 Hardhat Toolbox 插件的一部分； 如果您按照之前的指南进行操作，则应该已经安装了它们。

虽然这是我们推荐的测试设置，但 Hardhat 非常灵活：您可以自定义方法或使用其他工具采用完全不同的路径。

#### Initial setup
在本指南中，我们将为示例项目编写一些测试。 如果您还没有完成，请去[初始化它](https://hardhat.org/hardhat-runner/docs/guides/project-setup)。

我们建议您[使用 TypeScript](https://hardhat.org/hardhat-runner/docs/guides/project-setup) 以获得更好的自动完成功能并更早地捕获可能的错误。 本指南假定您使用的是 TypeScript，但您可以单击代码示例的选项卡来切换它们的语言。

该设置在 `test/Lock.ts` 文件中包含一些示例测试，但暂时忽略它们。 相反，创建一个 `test/my-tests.ts` 文件。 在本指南中，我们将仅通过运行 `npx hardhat test test/my-tests.ts` 来运行这些，而不仅仅是 `npx hardhat test`。

#### A simple test
在接下来的部分中，我们将为示例项目附带的 `Lock` 合约编写一些测试。 如果您还没有阅读它，请查看 `contracts/Lock.sol` 文件。

对于我们的第一个测试，我们将部署 `Lock` 合约并断言 `unlockTime()` getter 返回的解锁时间与我们在构造函数中传递的解锁时间相同：
```
import { expect } from "chai";
import hre from "hardhat";
import { time } from "@nomicfoundation/hardhat-network-helpers";

describe("Lock", function () {
  it("Should set the right unlockTime", async function () {
    const lockedAmount = 1_000_000_000;
    const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
    const unlockTime = (await time.latest()) + ONE_YEAR_IN_SECS;

    // deploy a lock contract where funds can be withdrawn
    // one year in the future
    const Lock = await hre.ethers.getContractFactory("Lock");
    const lock = await Lock.deploy(unlockTime, { value: lockedAmount });

    // assert that the value is correct
    expect(await lock.unlockTime()).to.equal(unlockTime);
  });
});
```
首先我们导入我们要使用的东西：[expect](https://www.chaijs.com/api/bdd/)来自 `chai` 的函数用于编写我们的断言、[Hardhat 运行时环境](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment) (hre) 以及与 Hardhat 网络交互的[网络助手](https://hardhat.org/hardhat-network-helpers)。 之后我们使用 describe 和 it 函数，它们是用于描述和分组测试的全局 Mocha 函数。 （您可以在[此处](https://mochajs.org/#getting-started)阅读有关摩卡的更多信息。）

测试本身是 `it` 函数的回调参数中的内容。 首先，我们设置要锁定的数量（以 wei 为单位）和解锁时间的值。 对于后者，我们使用`time.latest`
，一个网络助手，它返回最后一个开采区块的时间戳。 然后我们部署合约本身：首先我们得到一个[ContractFactory](https://docs.ethers.io/v5/single-page/#/v5/api/contract/contract-factory/)对于 `Lock` 合约，然后我们部署它，将解锁时间作为其构造函数参数传递。 我们还传递一个带有交易参数的对象。 这是可选的，但我们将使用它通过设置`value`字段来发送一些 ETH。

最后，我们检查合约中 `unlockTime()` getter 返回的值是否与我们部署它时使用的值相匹配。 由于合约上的所有函数都是异步的，我们必须使用 `await` 关键字来获取它的值； 否则，我们会将一个promise与一个数字进行比较，而这总是会失败。

#### Testing a function that reverts
在前面的测试中，我们检查了 getter 函数是否返回了正确的值。 这是一个只读函数，可以在不支付费用且没有任何风险的情况下调用。 然而，其他函数可以修改合约的状态，例如 `Lock` 合约中的 `withdraw` 函数。 这意味着我们希望满足一些前提条件才能成功调用此函数。 如果你看它的第一行，你会看到一些为此目的的 require 检查：
```
function withdraw() public {
  require(block.timestamp >= unlockTime, "You can't withdraw yet");
  require(msg.sender == owner, "You aren't the owner");
```
第一条语句检查解锁时间是否已到，第二条语句检查调用合约的地址是否是其所有者。 让我们从为第一个前提条件编写测试开始：
```
it("Should revert with the right error if called too soon", async function () {
  // ...deploy the contract as before...

  await expect(lock.withdraw()).to.be.revertedWith("You can't withdraw yet");
});
```
在之前的测试中，我们使用了`.to.equal`，它是 Chai 的一部分，用于比较两个值。 这里我们使用`.to.be.revertedWith`
，它断言事务还原，并且还原的原因字符串等于给定的字符串。 .to.be.revertedWith 匹配器不是 Chai 本身的一部分； 相反，它是由[Hardhat Chai Matchers](https://hardhat.org/hardhat-chai-matchers) 插件添加的，该插件包含在我们使用的示例项目中。

请注意，在之前的测试中我们编写了 expect(await ...)，但现在我们正在执行 await expect(...)。 在第一种情况下，我们以同步方式比较两个值； 内部 await 就在那里等待检索值。 在第二种情况下，整个断言是异步的，因为它必须等到交易被挖掘出来。 这意味着 expect 调用返回一个我们必须等待的承诺。

#### 操纵网络时间
我们正在部署解锁时间为一年的 `Lock` 合约。 如果我们想编写一个测试来检查解锁时间过后会发生什么，我们不能等待那么长的时间。 我们可以使用更短的解锁时间，比如 5 秒，但这是一个不太现实的值，而且在测试中等待的时间仍然很长。

解决方案是模拟时间的流逝。 这可以通过[time.increaseTo](https://hardhat.org/hardhat-network-helpers/docs/reference#increaseto(timestamp))网络助手，它使用给定的时间戳挖掘一个新块：
```
it("Should transfer the funds to the owner", async function () {
  // ...deploy the contract...

  await time.increaseTo(unlockTime);

  // this will throw if the transaction reverts
  await lock.withdraw();
});
```
正如我们提到的，调用 `lock.withdraw()` 会返回一个 Promise。 如果交易失败，promise将被拒绝。 在这种情况下使用 `await` 会抛出异常，因此如果事务恢复，测试将失败。

#### Using a different address
`withdraw` 函数完成的第二项检查是该函数是由合约所有者调用的。 默认情况下，部署和函数调用是使用第一个配置的帐户完成的。 如果我们想检查是否只有所有者可以调用某些函数，我们需要使用不同的帐户并验证它是否失败。

`ethers.getSigners()` 返回一个包含所有已配置帐户的数组。 我们可以使用合约的 `.connect` 方法用不同的账户调用函数并检查交易是否reverts：
```
it("Should revert with the right error if called from another account", async function () {
  // ...deploy the contract...

  const [owner, otherAccount] = await ethers.getSigners();

  // we increase the time of the chain to pass the first check
  await time.increaseTo(unlockTime);

  // We use lock.connect() to send a transaction from another account
  await expect(lock.connect(otherAccount).withdraw()).to.be.revertedWith(
    "You aren't the owner"
  );
});
```
在这里，我们再次调用一个函数并断言它返回正确的原因字符串。 不同之处在于我们使用 `.connect(anotherAccount)` 从不同的地址调用该方法。

#### Using fixtures
到目前为止，我们已经在每个测试中部署了 `Lock` 合约。 这意味着在每次测试开始时我们必须获取合约工厂然后部署合约。 这对于单个合约来说可能没问题，但是，如果您有更复杂的设置，每个测试在开始时都会有几行来设置所需的状态，并且大多数时候这些行都是相同的。

在典型的 Mocha 测试中，这种重复代码是通过 `beforeEach` 挂钩处理的：
```
import { Contract } from "ethers";

describe("Lock", function () {
  let lock: Contract;
  let unlockTime: number;
  let lockedAmount = 1_000_000_000;

  beforeEach(async function () {
    const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
    unlockTime = (await helpers.time.latest()) + ONE_YEAR_IN_SECS;

    const Lock = await ethers.getContractFactory("Lock");
    lock = await Lock.deploy(unlockTime, { value: lockedAmount });
  });

  it("some test", async function () {
    // use the deployed contract
  });
});
```
但是，这种方法有两个问题：
- 如果您必须部署许多合约，您的测试将会变慢，因为每个合约都必须发送多个交易作为其设置的一部分。
- 在 `beforeEach` 挂钩和您的测试之间共享这样的变量是丑陋且容易出错的。

Hardhat Network Helpers 中的 `loadFixture`helper 解决了这两个问题。 这个助手接收一个fixture，一个将链设置为某个所需状态的函数。 第一次调用 `loadFixture` 时，将执行fixture。 但是第二次，loadFixture 不会再次执行 fixture，而是将网络状态重置为 fixture 执行后的状态。 这样速度更快，并且它会撤消之前测试所做的任何状态更改。

这是我们的两个第一个测试在使用fixture时的样子：

```
describe("Lock", function () {
  async function deployOneYearLockFixture() {
    const lockedAmount = 1_000_000_000;
    const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
    const unlockTime = (await time.latest()) + ONE_YEAR_IN_SECS;

    const Lock = await ethers.getContractFactory("Lock");
    const lock = await Lock.deploy(unlockTime, { value: lockedAmount });

    return { lock, unlockTime, lockedAmount };
  }

  it("Should set the right unlockTime", async function () {
    const { lock, unlockTime } = await loadFixture(deployOneYearLockFixture);

    // assert that the value is correct
    expect(await lock.unlockTime()).to.equal(unlockTime);
  });

  it("Should revert with the right error if called too soon", async function () {
    const { lock } = await loadFixture(deployOneYearLockFixture);

    await expect(lock.withdraw()).to.be.revertedWith("You can't withdraw yet");
  });
});
```
fixture 函数可以返回任何你想要的东西，loadFixture 助手会返回它。 我们建议像我们在此处所做的那样返回一个对象，这样您就可以只提取您关心该测试的值。

#### Other tests
您还可以测试其他几项内容，例如[发出了哪些事件](https://hardhat.org/hardhat-chai-matchers/docs/reference#.emit)或[所涉及地址的余额如何变化](https://hardhat.org/hardhat-chai-matchers/docs/reference#balance-change)。 检查 `test/Lock.js` 文件以查看其他示例。

#### 测量代码覆盖率
安全帽工具箱包括[`solidity-coverage`](https://github.com/sc-forks/solidity-coverage)用于测量项目中测试覆盖率的插件。 只需运行`coverage`任务，您就会得到一份报告：
```
npx hardhat coverage
```
#### Using the gas reporter
安全帽工具箱还包括[hardhat-gas-reporter](https://github.com/cgewecke/hardhat-gas-reporter)插件以根据测试的执行获取使用了多少 gas 的指标。 执行`test`任务并设置 REPORT_GAS 环境变量时运行气体报告器：
```
REPORT_GAS=true npx hardhat test
```
#### Running tests in parallel
您可以使用 `--parallel` 标志并行运行测试：
```
npx hardhat test --parallel
```

或者，在 Hardhat 配置的 `mocha` 部分使用 `parallel: true`。

大多数时候，串行或并行运行测试应该产生相同的结果，但在某些情况下，并行运行的测试会表现不同：

在串行模式下，所有测试文件共享 Hardhat Runtime Environment 的同一个实例，但在并行模式下，情况并非总是如此。 Mocha 使用一组工作人员来执行测试，每个工作人员都从自己的 HRE 实例开始。 这意味着如果一个测试文件部署了一个契约，那么该部署将存在于其他一些测试文件中，而在其他测试文件中则不存在。

`.only` 修饰符在并行模式下不起作用。 作为替代方案，您可以使用 --grep 运行特定测试。

由于并行模式使用更多的系统资源，单个测试的持续时间可能会更长，因此某些测试可能会因此开始超时。 如果遇到此问题，您可以在 Hardhat 配置的 Mocha 部分增加测试超时或在测试中使用 `this.timeout()`。

执行测试的顺序是不确定的。

还有一些与并行模式相关的其他限制。 您可以在 Mocha 的文档中阅读更多关于它们的信息。 如果您在使用并行模式时遇到问题，您可以查看他们的并行模式故障排除部分。

