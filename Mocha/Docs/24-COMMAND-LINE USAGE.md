`--allow-uncaught`

默认情况下，Mocha 将尝试捕获运行测试抛出的未捕获异常并将这些异常报告为测试失败。 使用 --allow-uncaught 禁用此行为并允许传播未捕获的异常。 通常会导致进程崩溃。

在调试特别难以跟踪的异常时，此标志很有用。

`--async-only, -A`
执行一条规则，测试必须以“异步”风格编写，这意味着每个测试都提供一个完成的回调或返回一个 Promise。 不合规的测试将被标记为失败。

`--bail, -b`
导致 Mocha 在遇到第一个测试失败后停止运行测试。 执行相应的“`after each`”和“`after all`”挂钩以进行潜在的清理。

`--bail` 并不意味着`--exit`。

`--check-leaks`
使用此选项让 Mocha 检查在运行测试时泄漏的全局变量。 通过 --global 选项指定可接受的全局变量（例如：--check-leaks --global jQuery --global MyLib）。

`--compilers`
--编译器在 v6.0.0 中被删除。 请参阅进一步的[解释和解决方法](https://github.com/mochajs/mocha/wiki/compilers-deprecation)。

`--dry-run`
v9.0.0 中的新功能报告测试而不执行任何测试，既不测试也不挂钩。

`--exit`
> Updated in v4.0.0.

如果您的测试在升级到 Mocha v4.0.0 或更新版本后挂起，请使用 --exit 进行快速（但不一定推荐）修复。

在 v4.0.0 版本之前，默认情况下，Mocha 会在执行完所有测试后强制退出自己的进程。 这种行为会引发一系列潜在问题； 它表示测试（或固定装置、线束、被测代码等）不能正确地自行清理。 最终，“脏”测试可能（但不总是）导致假阳性或假阴性结果。

如果服务器仍在侦听端口，或者套接字仍处于打开状态等，“Hanging”最常出现。它也可能是失控的 `setInterval()`，甚至是从未实现的错误 `Promise`。

v4.0.0（和更新版本）中的默认行为是 --no-exit，之前是 --exit。

“解决”问题的最简单方法是将 --exit 传递给 Mocha 进程。 调试可能很耗时——因为问题出在哪里并不总是很明显——但建议这样做。

为了确保您的测试不会留下混乱，这里有一些开始的想法：
- See the Node.js guide to debugging
- Try something like wtfnode
- Use the new async_hooks API (example)
- Use .only until you find the test that causes Mocha to hang

`--fail-zero`
v9.1.0 中的新功能如果没有遇到退出代码的测试，则测试运行失败：1。

`--forbid-only`
强制执行测试不能排他性的规则（不允许使用例如 describe.only() 或 it.only()）。

--forbid-only 导致 Mocha 在遇到独占（“only”）测试或套件时失败，并且它将中止进一步的测试执行。

`--forbid-pending`
强制执行不得跳过测试的规则（不允许在任何地方使用例如 describe.skip()、it.skip() 或 this.skip()）。

`--forbid-pending` 导致 Mocha 在遇到跳过的（“pending”）测试或套件时失败，并且它将中止进一步的测试执行。

`--global <variable-name>`
> 更新于 v6.0.0； 选项是 --global 和 --globals 现在是一个别名。

定义一个全局变量名。 例如，假设您的应用程序故意公开一个全局命名的应用程序和 YUI，您可能需要添加 `--global app --global YUI`。

--global 接受通配符。 您可以执行 --global '*bar'，它会匹配 foobar、barbar 等。您还可以传入 '*' 以忽略所有全局变量。

--global 可以接受逗号分隔的列表； --global app,YUI 等同于--global app --global YUI。

通过将此选项与 --check-leaks 结合使用，您可以指定您希望泄漏到全局范围内的已知全局变量的白名单。

`--retries <n>`
重试失败的测试 n 次。

Mocha 不会通过 defa 重试测试失败

`--slow <ms>, -s <ms>`
以毫秒为单位指定“慢”测试阈值。 Mocha 使用它来突出显示花费时间过长的测试用例。 “慢速”测试不被视为失败。

注意：执行一半“慢”时间的测试将使用默认的规范报告器以黄色突出显示； 执行整个“慢”时间的测试将以红色突出显示。

`--timeout <ms>, -t <ms>`
> v6.0.0 中的更新：使用检查标志调用 Mocha 时隐含 --timeout 0。 --timeout 99999999 不再需要。

指定测试用例超时，默认为两 (2) 秒（2000 毫秒）。 超过此时间的测试将被标记为失败。

要覆盖，您可以传递以毫秒为单位的超时时间，或带有 s 后缀的值，例如 --timeout 2s 和 --timeout 2000 是等效的。

要禁用超时，请使用 --timeout 0。

注意：同步（阻塞）测试也受超时限制，但在代码停止阻塞之前它们不会完成。 无限循环仍将是无限循环！

`--ui <name>, -u <name>`
--ui 选项允许您指定要使用的接口，默认为 `bdd`。

`--color, -c, --colors`
> 在 v6.0.0 中更新。 --colors 现在是--color 的别名。

“强制”启用颜色输出，或者通过 --no-color 强制禁用它。 默认情况下，Mocha 使用 supports-color 模块来决定。

在某些情况下，颜色输出将被以机器可读格式输出的某些报告显式抑制。

`--diff`
如果可能，在遇到断言失败时显示预期值和实际值之间的差异。

这个标志是不寻常的，因为它默认为 true； 使用 --no-diff 来抑制 Mocha 自己的差异输出。

一些断言库将提供自己的差异，在这种情况下，无论默认值如何，都不会使用 Mocha 的差异。

Mocha 自己的 diff 输出不符合任何已知标准，并且被设计为人类可读的。

默认情况下，字符串在生成差异之前被截断为 8192 个字符。 这是为了防止大字符串出现性能问题。

但是，在比较大字符串时，它会使输出更难解释。 因此，可以使用 --reporter-option maxDiffSize=[number] 配置此值。

值为 0 表示没有限制，默认为 8192 个字符。

`--full-trace`
启用“完整”堆栈跟踪。 默认情况下，Mocha 会尝试将堆栈跟踪提炼为噪音较小（但仍然有用）的输出。

在调试 Mocha 或 Node.js 本身的可疑问题时，此标志很有用。

`--inline-diffs`
启用“内联”差异，这是差异字符串的替代输出。

在处理大字符串时很有用。

如果断言库提供自己的差异输出，则不执行任何操作。

`--reporter <name>, -R <name>`
指定将使用的报告器，默认为 `spec`。

允许使用第三方reporters。 例如，[mocha-lcov-reporter](https://npm.im/mocha-lcov-reporter) 可以在安装后与 --reporter mocha-lcov-reporter 一起使用。

`--reporter-option <option>, -O <option>, --reporter-options <option>`
> 在 v6.0.0 中更新。 可以指定多次。 --reporter-options 现在是 --reporter-option 的别名。

以 <key>=<value> 格式提供特定于报告者的选项，例如 --reporter tap --reporter-option tapVersion=13。

并非所有reporters都接受选项。

可以指定为逗号分隔的列表。

`--opts <path>`
> Removed in v8.0.0. Please use configuration file instead.

`--package <path>`
指定 package.json 文件的显式路径（表面上包含 mocha 属性中的配置）。

默认情况下，Mocha 在当前工作目录或最近的祖先目录中查找 package.json，并将使用找到的第一个文件（无论它是否包含 mocha 属性）； 要抑制 package.json 查找，请使用 --no-package。

`--extension <ext>`
具有此扩展名的文件将被视为测试文件。 默认为 js。

指定 --extension 将删除 .js 作为测试文件扩展名； 使用 --extension js 重新添加它。 例如，要加载 .mjs 和 .js 测试文件，您必须提供 --extension mjs --extension js。

该选项可以多次给出。 该选项接受以逗号分隔的列表：--extension a,b 等同于--extension a --extension b。

--extension 现在支持多部分扩展（例如 spec.js）、前导点 (.js) 及其组合 (.spec.js)；

`--file <file|directory|glob>`
> 警告：--file 与并行模式不兼容。

明确包含要在其他测试文件之前加载的测试文件。 `--file` 的多次使用是允许的，并将按给定的顺序加载。

例如，如果您想声明要在所有其他测试文件的每个测试之前运行的挂钩，则很有用。

以这种方式指定的文件不受 `--sort` 或 `--recursive` 的影响。

以这种方式指定的文件应该包含一个或多个suites, tests or hooks。 如果不是这种情况，请考虑 `--require` 。

`--ignore <file|directory|glob>, --exclude <file|directory|glob>,`
显式忽略一个或多个测试文件、目录或 glob（例如，some/**/files*），否则将被加载。
可以指定多次。

> v10.0.0 中的新功能：在 Windows 中始终使用正斜杠 / 作为路径分隔符。

使用 `--file` 指定的文件不受此选项的影响。

`--recursive`
查找测试文件时，递归到子目录中。

请参阅 `--extension` 以定义哪些文件被视为测试文件。

`--require <module>, -r <module>`
在加载user interface或测试文件之前需要一个模块。 这对于：

- Test harnesses
- 增强内置或全局范围的断言库（例如 should.js）
- 编译器，例如通过 @babel/register 的 Babel 或通过 ts-node 的 TypeScript（使用 --require ts-node/register）。 请参阅 Babel 或 TypeScript 工作示例。

以这种方式需要的模块应该同步工作； Mocha 不会等待所需模块中的异步任务完成。

您不能使用 `--require` 来设置挂钩。 如果你想设置挂钩运行，例如，在每次测试之前，使用根挂钩插件。

`--sort, -S`
警告：--sort 与并行模式不兼容。

使用 Array.prototype.sort 对测试文件进行排序（按绝对路径）。

`--watch, -w`
重新运行文件更改测试。

--watch-files 和 --watch-ignore 选项可用于控制监视哪些文件的更改。

可以通过键入 ⓡ ⓢ ⏎（与 nodemon 相同的快捷方式）手动重新运行测试。
`--watch-files <file|directory|glob>`
设置 `--watch` 时要监视的路径或 glob 列表。 如果匹配给定 glob 的文件发生更改或添加或删除，mocha 将重新运行所有测试。

如果路径是目录，则将监视所有文件和子目录。

默认情况下，监视当前目录中具有 `--extension` 提供的扩展名之一且不包含在 node_modules 或 .git 文件夹中的所有文件。

该选项可以多次给出。 该选项接受逗号分隔的列表：`--watch-files a,b` 等同于`--watch-files a --watch-files b`

`--watch-ignore <file|directory|glob>`
要从监视中排除的路径或 glob 列表。 默认为 node_modules 和 .git。

要排除目录中的所有文件，最好使用 foo/bar 而不是 foo/bar/**/*。 后者仍将监视目录 `foo/bar` 但将忽略对该目录内容的所有更改。

该选项可以多次给出。 该选项接受逗号分隔的列表：--watch-ignore a,b 等同于--watch-ignore a --watch-ignore b
`--fgrep <string>, -f <string>`
使 Mocha 仅运行标题包含给定字符串的测试。

与 --grep 互斥。

`--grep <regexp>, -g <regexp>`
使 Mocha 仅运行与给定正则表达式匹配的测试，该正则表达式在内部编译为正则表达式。

例如，假设您有“api”相关测试，以及“app”相关测试，如以下代码片段所示； 可以使用 `--grep api` 或 `--grep app` 来运行一个或另一个。 这同样适用于套件或测试用例标题的任何其他部分，`--grep users` 也将有效，甚至`--grep GET`。

另一个带双引号的选项：`--grep "groupA|groupB"`。
对于更复杂的条件：`--grep "/get/i"`。 一些贝壳，例如 Git-Bash-for-Windows 可能需要：`--grep "'/get/i'"`。 使用 `ignoreCase /i`（尤其是 `/g` 和 `/y`）以外的标志可能会导致不可预知的结果。
```
describe('api', function () {
  describe('GET /api/users groupA', function () {
    it('respond with an array of users', function () {
      // ...
    });
  });
});

describe('app', function () {
  describe('GET /users groupB', function () {
    it('respond with an array of users', function () {
      // ...
    });
  });
});
```
Mutually exclusive with `--fgrep`.
`--invert`
使用 --grep 或 fgrep 指定的反向匹配。

需要 --grep 或 --fgrep（但不是两者）。
`--inspect, --inspect-brk, inspect`
启用 Node.js 的检查器。

使用 `--inspect` / `--inspect-brk` 启动 V8 检查器以与 Chrome 开发工具一起使用。

使用 inspect 启动 Node.js 的内部调试器。

所有这些选项都是相互排斥的。

暗示 --timeout 0。
`--parallel, -p`
使用 --parallel 标志在工作池中运行测试。

每个测试文件都将放入队列中，并在工作人员可用时执行。

注意：--parallel 对 Mocha 的行为有一定的影响，您必须注意这一点。 阅读有关[并行运行测试](https://mochajs.org/#parallel-tests)的更多信息。

`--jobs <count>, -j <count>`
使用 `--jobs <count>` 指定工作池中的最大进程数。

默认值是 CPU 核心数减去 1。

提示：使用 --jobs 0 或 --jobs 1 暂时禁用 --parallel。

除非与 --parallel 一起使用，否则无效。

