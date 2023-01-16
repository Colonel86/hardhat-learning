在本节中，我们将探索为 Hardhat 创建插件，这是集成其他工具和扩展内置功能的关键组件。

#### Hardhat 中的插件到底是什么？
插件是一些可重用的配置。 您可以在插件中执行的任何操作也可以在您的配置文件中完成。 您可以在配置文件中测试您的想法，然后在准备好后将它们移至插件中。

在开发插件时，可用于集成新功能的主要工具是扩展 Hardhat 运行时环境、扩展 Hardhat 配置、定义新任务和覆盖现有任务，这些都是通过代码实现的配置操作。

您可以通过创建插件实现的一些示例包括：在`check`任务运行时运行 linter，为不同的文件使用不同的编译器版本或为您的合同生成 UML 图。

####  Extending the Hardhat Runtime Environment
让我们来看看创建一个插件的过程，该插件将新功能添加到 Hardhat Runtime Environment。 通过这样做，我们确保我们的新功能随处可用。 这意味着您的插件用户可以从tasks, tests, scripts, and the Hardhat console访问它。

Hardhat 运行时环境 (HRE) 通过扩展函数队列进行配置，您可以使用 `extendEnvironment()` 函数添加这些函数。 它接收一个参数，该参数是一个回调，将在 HRE 初始化后执行。 如果多次调用 `extendEnvironment`，它的回调将按顺序执行。

例如，将以下内容添加到 `hardhat.config.js`：
```
extendEnvironment((hre) => {
  hre.hi = "Hello, Hardhat!";
});
```
将使 `hi` 在环境可访问的任何地方可用。
```
extendEnvironment((hre) => {
  hre.hi = "Hello, Hardhat!";
});

task("envtest", async (args, hre) => {
  console.log(hre.hi);
});

module.exports = {};
```
Will yield:
```
$ npx hardhat envtest
Hello, Hardhat!
```
这实际上就是为 Hardhat 组装一个插件所需的全部。 现在 `hi` 可用于 Hardhat 控制台、您的 tasks, tests and other plugins。

### 使用 Hardhat TypeScript 插件样板
有关插件的完整示例，您可以查看 [Hardhat TypeScript 插件样板项目](https://github.com/NomicFoundation/hardhat-ts-plugin-boilerplate/)。

插件不需要用 TypeScript 编写，但我们建议这样做，因为我们的许多用户都在使用它。 在 JavaScript 中创建插件可能会给他们带来低劣的体验。

#### Extending the HRE
要了解如何在 TypeScript 中成功扩展 HRE，并为您的用户提供有关您的扩展的类型信息，请查看
[src/index.ts](https://github.com/NomicFoundation/hardhat-ts-plugin-boilerplate/blob/master/src/index.ts)在样板仓库中阅读[扩展 HRE 文档](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment#extending-the-hre)。

确保在主文件中保留类型扩展名，因为该约定用于不同的插件。

#### Extending the Hardhat config
样板项目还有一个关于如何扩展 Hardhat 配置的示例。

我们强烈建议在 TypeScript 中执行此操作并正确扩展配置类型。

有关如何将字段添加到 Hardhat 配置的示例，请参见src/index.ts

###  Plugin development best practices
#### Throwing errors from your plugins
为了在错误意味着中断任务执行时向用户显示更好的堆栈跟踪，请考虑抛出 `HardhatPluginError` 错误，可以在 `hardhat/plugins` 中找到。

如果您的错误源自您的用户代码，例如调用您的函数之一的测试或脚本，则不应使用 `HardhatPluginError。`

#### 优化您的插件以获得更好的启动时间
保持启动时间短对于提供良好的用户体验至关重要。

为此，Hardhat 及其插件将任何缓慢的导入或初始化延迟到最后一刻。 为此，您可以使用 `hardhat/plugins` 中的 `lazyObject` 和 `lazyFunction`。

有关如何使用它们的示例位于[https://github.com/NomicFoundation/hardhat-ts-plugin-boilerplate/blob/master/src/index.ts](https://github.com/NomicFoundation/hardhat-ts-plugin-boilerplate/blob/master/src/index.ts)

### 依赖项说明
知道何时使用`dependency`或 `peerDependency` 可能很棘手。 我们推荐这些文章以了解它们的区别。

如果您仍然有疑问，这些可能会有所帮助：
- Rule of thumb #1:Hardhat 必须是对等依赖。
- Rule of thumb #2:如果你的插件 P 依赖于另一个插件 P2，那么 P2 应该是 P 的对等依赖，而 P2 的对等依赖应该是 P 的对等依赖。
- Rule of thumb #3:如果您的用户可能require()一个非hardhat依赖项，那么它应该是一个对等依赖项。
- Rule of thumb #4:每个 `peerDependency` 也应该是一个 `devDependency`。

### 连接到用户的工作流程
要集成到用户现有的工作流程中，我们建议插件作者在有意义时覆盖内置任务。

建议覆盖的例子是：

- 预处理智能合约应该覆盖其中一个compile子任务。
- Linter 集成应该覆盖`check`任务。
- 生成中间文件的插件应该覆盖`clean`任务。

有关所有内置任务和子任务的列表，请查看[task-names.ts](https://github.com/NomicFoundation/hardhat/blob/main/packages/hardhat-core/src/builtin-tasks/task-names.ts)

