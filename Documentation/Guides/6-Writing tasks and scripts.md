# Writing tasks and scripts
Hardhat 的核心是一个任务运行器，可让您自动化开发工作流程。 它带有一些内置任务，如`compile`和`test`，但您也可以添加自己的自定义任务。

本指南将向您展示如何使用tasks和 scripts扩展 Hardhat 的功能。 它假定您已经初始化了一个示例项目。 如果您还没有这样做，请先阅读本[指南](https://hardhat.org/hardhat-runner/docs/guides/project-setup)。

####  Writing Hardhat Tasks
让我们编写一个非常简单的任务来打印可用帐户列表，并探索它是如何工作的。

复制此任务定义并将其粘贴到安全帽配置文件中：
```
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});
```
现在你应该可以运行它了：
```
npx hardhat accounts
```

我们正在使用`task`函数来定义我们的新任务。 它的第一个参数是任务的名称，这是我们在命令行中用来运行它的名称。 第二个参数是任务的描述，当你使用 `npx hardhat help` 时会打印出来。

第三个参数是在您运行任务时执行的异步函数。 它接收两个参数：
1. 带有任务参数的对象。 我们还没有定义任何东西。
2. Hardhat 运行时环境或 HRE，其中包含 Hardhat 及其插件的所有功能。 您还可以找到在任务执行期间将其所有属性注入到全局命名空间中。

您可以在此功能中自由地做任何您想做的事情。 在这种情况下，我们使用 `ethers.getSigners()` 来获取所有配置的帐户并打印它们的每个地址。

您可以向您的任务添加参数，Hardhat 将为您处理它们的解析和验证。

您还可以覆盖现有任务，这样您就可以更改 Hardhat 不同部分的工作方式。

要了解有关任务的更多信息，请阅读本[指南](https://hardhat.org/hardhat-runner/docs/advanced/create-task)。

#### Writing Hardhat scripts
您可以编写脚本并使用 Hardhat 运行它们。 他们可以利用 Hardhat 运行时环境来访问 Hardhat 的所有功能，包括任务运行器。

这是一个与我们的`accounts`任务相同的脚本。 使用以下内容在`scripts`目录中创建一个 `accounts.js` 文件：
```
async function main() {
  const accounts = await ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```
并使用内置的运行任务运行它：
```
npx hardhat run scripts/accounts.js
```
请注意，我们使用的是ethers.js而不导入它。 这是可能的，因为 Hardhat Runtime Environment 中可用的所有内容在脚本中也全局可用。

要了解有关脚本的更多信息，包括如何在不使用 Hardhat 的 CLI 的情况下运行它们，请阅读本[指南](https://hardhat.org/hardhat-runner/docs/advanced/scripts)。

#### 在tasks和scripts之间进行选择
在tasks和scripts之间进行选择取决于您。 如果您不确定应该使用哪一个，您可能会发现这很有用：
1. 如果您想自动化不需要参数的工作流程，scripts可能是最佳选择。
2. 如果您要自动化的工作流程需要一些参数，请考虑创建 Hardhat task。
3. 如果您需要从另一个具有自己的 CLI 的工具访问 Hardhat 运行时环境，例如[jest](https://jestjs.io/)或者[nbd](https://www.npmjs.com/package/ndb)
，你应该写一个script。 确保显式导入 Hardhat 运行时环境，以便可以使用该工具而不是 Hardhat 的 CLI 运行它。
4. 如果你觉得 Hardhat 的参数处理达不到你的需要，你应该写一个脚本。 只需显式导入 Hardhat 运行时环境，使用您自己的参数解析逻辑（例如使用[yargs](https://yargs.js.org/))，并将其作为[独立的 Node.js 脚本运行](https://hardhat.org/hardhat-runner/docs/advanced/scripts#standalone-scripts:-using-hardhat-as-a-library)。