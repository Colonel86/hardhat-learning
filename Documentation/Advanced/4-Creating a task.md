## Creating a task
本指南将探索在 Hardhat 中创建任务，这是用于自动化的核心组件。

task是具有一些关联元数据的 JavaScript 异步函数。 Hardhat 使用此元数据为您自动执行某些操作。 处理参数解析、验证和帮助消息。

您在 Hardhat 中可以做的所有事情都被定义为一项task。 开箱即用的默认操作是内置task，它们是使用您作为用户可用的相同 API 实现的。

要查看项目中当前可用的任务，请运行 `npx hardhat`：

```
$ npx hardhat
Hardhat version 2.9.10

Usage: hardhat [GLOBAL OPTIONS] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:

  --config              A Hardhat config file.
  --emoji               Use emoji in messages.
  --help                Shows this message, or a task's help if its name is provided
  --max-memory          The maximum amount of memory that Hardhat can use.
  --network             The network to connect to.
  --show-stack-traces   Show stack traces.
  --tsconfig            A TypeScript config file.
  --verbose             Enables Hardhat verbose logging
  --version             Shows hardhat's version.


AVAILABLE TASKS:

  check         Check whatever you need
  clean         Clears the cache and deletes all artifacts
  compile       Compiles the entire project, building all artifacts
  console       Opens a hardhat console
  flatten       Flattens and prints contracts and their dependencies
  help          Prints this message
  node          Starts a JSON-RPC server on top of Hardhat Network
  run           Runs a user-defined script after compiling the project
  test          Runs mocha tests

To get help for a specific task run: npx hardhat help [task]
```
您可以创建其他任务，这些任务将显示在此列表中。 例如，您可以创建一个任务来重置开发环境的状态，或者与您的合同交互，或者打包您的项目。

让我们来看看创建一个与智能合约交互的过程。

Hardhat 中的任务是异步 JavaScript 函数，可以访问 Hardhat 运行时环境，公开其配置和参数，以及对其他任务和可能已注入的任何插件对象的编程访问。

对于我们的示例，我们将使用`@nomicfoundation/hardhat-toolbox`其中包括与我们的合约进行交互的 `ethers.js` 库。

Task创建代码可以放在 hardhat.config.js 中，或者任何你调用的配置文件中。 这是创建简单task的好地方。 如果您的task更复杂，将代码拆分为多个文件并从配置文件中获取它们也是完全有效的。

（如果您正在编写一个添加任务的 Hardhat 插件，它们也可以从单独的 npm 包中创建。在我们的[构建插件部分](https://hardhat.org/hardhat-runner/docs/advanced/building-plugins)中了解有关通过插件创建任务的更多信息。）

**配置文件总是在启动时执行，然后再发生任何其他事情。** 记住这一点很好。 我们将加载 Hardhat 工具箱并将我们的任务创建代码添加到其中。

对于本教程，我们将创建一个任务以从终端获取帐户余额。 您可以使用 Hardhat 的配置 API 执行此操作，该 API 在 hardhat.config.js 的全局范围内可用：
```
require("@nomicfoundation/hardhat-toolbox");

task("balance", "Prints an account's balance").setAction(async () => {});

/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.9",
};
```

保存文件后，您应该能够在 Hardhat 中看到任务：
```
$ npx hardhat
Hardhat version 2.9.10

Usage: hardhat [GLOBAL OPTIONS] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:

  --config           	A Hardhat config file.
  ...


AVAILABLE TASKS:

  balance           	Prints an account's balance
  check             	Check whatever you need
  clean             	Clears the cache and deletes all artifacts
  ...

To get help for a specific task run: npx hardhat help [task]
```
现在让我们实现我们想要的功能。 我们需要从用户那里得到账户地址。 我们可以通过向我们的任务添加一个参数来做到这一点：
```
task("balance", "Prints an account's balance")
  .addParam("account", "The account's address")
  .setAction(async () => {});
```
当您向任务添加参数时，Hardhat 将为您处理其帮助消息：
```
$ npx hardhat help balance
Hardhat version 2.9.10

Usage: hardhat [GLOBAL OPTIONS] balance --account <STRING>

OPTIONS:

  --account     The account's address

balance: Prints an account's balance

For global options help run: hardhat help
```
现在让我们获取帐户余额。 [Hardhat Runtime Environment](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment) 将在全球范围内可用。 通过使用 Hardhat 工具箱中包含的 Hardhat 的 [ether.js 插件](https://github.com/NomicFoundation/hardhat/tree/main/packages/hardhat-ethers)，我们可以访问 ethers.js 实例：
```
task("balance", "Prints an account's balance")
  .addParam("account", "The account's address")
  .setAction(async (taskArgs) => {
    const balance = await ethers.provider.getBalance(taskArgs.account);

    console.log(ethers.utils.formatEther(balance), "ETH");
  });
```

最后，我们可以运行它：
```
$ npx hardhat balance --account 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
10000.0 ETH
```
这就是您的第一个功能齐全的 Hardhat 任务，它允许您以简单的方式与以太坊区块链进行交互。

## Advanced usage
您可以在 `hardhat.config.js` 文件中创建自己的任务。 Config API 将在全局环境中可用，具有定义任务的功能。 如果您希望保持明确并利用编辑器的自动完成功能，您还可以使用 require("hardhat/config") 导入 API。

创建任务是通过调用`task`函数来完成的。 它将返回一个 `TaskDefinition` 对象，该对象可用于定义任务的参数。

您可以定义的最简单的任务是
```
task(
  "hello",
  "Prints 'Hello, World!'",
  async function (taskArguments, hre, runSuper) {
    console.log("Hello, World!");
  }
);
```
`task`的第一个参数是任务名称。 第二个是它的描述，用于在 CLI 中打印帮助消息。 第三个是接收以下参数的异步函数：
- `taskArguments`：是一个对象，带有已解析的任务 CLI 参数。 在本例中，它是一个空对象。
- `hre` 是 Hardhat 运行时环境。
- runSuper 仅在您覆盖现有任务时才相关，我们将在接下来了解这一点。 它的目的是让你运行原始任务的动作。

定义操作的参数是可选的。 Hardhat Runtime Environment 和 runSuper 也将在全球范围内可用。 我们可以这样重写我们的“hello”任务：
```
task("hello", "Prints 'Hello, World!'", async () => {
  console.log("Hello, World!");
});
```
#### 任务的动作要求
编写任务的唯一要求是，在它启动的每个异步进程完成之前，其操作返回的 Promise 不得 resolve。

这是其操作不符合此要求的任务示例：
```
task("BAD", "This task is broken", async () => {
  setTimeout(() => {
    throw new Error(
      "This task's action returned a promise that resolved before I was thrown"
    );
  }, 1000);
});
```
这个其他任务使用 `Promise` 来等待超时触发：
```
task("delayed-hello", "Prints 'Hello, World!' after a second", async () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("Hello, World!");
      resolve();
    }, 1000);
  });
});
```
手动创建 Promise 看起来很有挑战性，但如果您坚持使用 async/await 和基于 Promise 的 API，则不必这样做。 例如，您可以使用 npm 包[delay](https://www.npmjs.com/package/delay)对于 `setTimeout` 的承诺版本。

#### Defining parameters
Hardhat 任务可以接收带值的命名参数（例如 --parameter-name parameterValue）、没有值的标志（例如 --flag-name）、位置参数或可变参数。 可变参数的作用类似于 JavaScript 的剩余参数。 Config API 任务函数返回一个对象，其中包含定义所有这些方法的方法。 一旦定义，Hardhat 将控制解析参数、验证它们并打印帮助消息。

向 hello 任务添加一个可选参数可以如下所示：
```
task("hello", "Prints a greeting")
  .addOptionalParam("greeting", "The greeting to print", "Hello, World!")
  .setAction(async ({ greeting }) => console.log(greeting));
```

并将与 npx hardhat hello --greeting Hola 一起运行
##### 位置参数限制
位置参数和可变参数不必命名，并且具有编程语言的通常限制：

- 没有位置参数可以跟随可变参数
- 必需/强制参数不能跟在可选参数之后。

不遵守这些限制将导致在加载 Hardhat 时抛出异常。

##### 类型验证
Hardhat 负责验证和解析为每个参数提供的值。 您可以声明参数的类型，Hardhat 将获取 CLI 字符串并将它们转换为您想要的类型。 如果此转换失败，它将打印一条错误消息来解释原因。

通过 `types` 对象，Config API 中提供了许多类型。 该对象在处理您的 `hardhat.config.js` 之前被注入全局范围，但您也可以使用 const { types } = require("hardhat/config") 显式导入它并利用编辑器的自动完成功能。

为其参数之一定义类型的任务示例是
```
task("hello", "Prints 'Hello' multiple times")
  .addOptionalParam(
    "times",
    "The number of times to print 'Hello'",
    1,
    types.int
  )
  .setAction(async ({ times }) => {
    for (let i = 0; i < times; i++) {
      console.log("Hello");
    }
  });
```
使用 npx hardhat hello --times notanumber 调用它会导致错误。
#### Overriding tasks
定义与现有任务同名的任务将覆盖现有任务。 这对于更改或扩展内置和插件提供的任务的行为很有用。

任务覆盖的工作方式与扩展类时覆盖方法非常相似。 您可以设置自己的操作，该操作可以调用被覆盖的操作。 覆盖任务时的唯一限制是您不能添加或删除参数。

任务覆盖顺序很重要，因为操作只能使用 runSuper 函数调用立即覆盖的定义。

覆盖内置任务是自定义和扩展 Hardhat 的好方法。 要知道要覆盖哪些任务，请查看 [src/builtin-tasks](https://github.com/NomicFoundation/hardhat/tree/main/packages/hardhat-core/src/builtin-tasks)。

##### The runSuper function
`runSuper` 是一个可用于覆盖任务操作的函数。 它可以作为任务的第三个参数接收或直接从全局对象中使用。

runSuper 是一个可用于覆盖任务操作的函数。 它可以作为任务的第三个参数接收或直接从全局对象中使用。

此函数的工作方式类似于 [JavaScript's super keyword](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super)：它调用任务先前定义的操作。

如果任务没有覆盖先前的任务定义，则调用 `runSuper` 将导致错误。 要检查调用它是否会失败，您可以使用`boolean`字段 `runSuper.isDefined`。

`runSuper` 函数接收一个可选参数：一个带有任务参数的对象。 如果未提供此参数，则将使用调用它的操作接收到的相同任务参数。

#### Subtasks
创建具有大量逻辑的任务很难扩展或自定义它们。 制作多个相互调用的小而集中的任务是允许扩展的更好方法。 如果您以这种方式设计您的任务，那么只想更改其中一小部分的用户可以覆盖您的一个子任务。

例如，compile任务被实现为多个任务的流水线。 它只调用 `compile:get-source-paths`、`compile:get-dependency-graph` 和 `compile:build-artifacts` 等子任务。 我们建议在中间任务前加上它们的主要任务和一个冒号。

为避免帮助消息因大量中间任务而变得混乱，您可以使用子任务配置 API 函数定义这些任务。 子任务函数的工作方式几乎与任务完全一样。 唯一的区别是用它定义的任务不会包含在帮助消息中。

要运行子任务或任何任务，您可以使用 `run` 函数。 它有两个参数：要运行的任务的名称，以及一个带有参数的对象。

这是运行子任务的任务示例：
```
task("hello-world", "Prints a hello world message").setAction(
  async (taskArgs, hre) => {
    await hre.run("print", { message: "Hello, World!" });
  }
);

subtask("print", "Prints a message")
  .addParam("message", "The message to print")
  .setAction(async (taskArgs) => {
    console.log(taskArgs.message);
  });
```

