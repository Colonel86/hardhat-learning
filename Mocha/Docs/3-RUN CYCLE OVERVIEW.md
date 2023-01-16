以下是 Mocha 在 Node.js 中运行时的“执行流程”的中级概述； “不太重要”的细节已被省略。

在浏览器中，测试文件由 `<script>` 标签加载，调用 `mocha.run()` 从下面的第 9 步开始。

#### SERIAL MODE 串行模式
1. 用户（就是你）执行 mocha
2. 从配置文件加载选项（如果存在）
3. Mocha 处理提供的任何命令行选项（有关详细信息，请参阅配置合并部分）
4. 如果找到`node`可执行文件的已知标志：
    1. Mocha 将在子进程中生成节点，使用这些标志执行自身
    2. 否则，Mocha 不会生成子进程
5. Mocha 加载由 --require 指定的模块
    1. 如果以这种方式加载的文件包含已知的特定于 Mocha 的导出（例如，[root hook 插件](https://mochajs.org/#root-hook-plugins)），Mocha 会"registers"这些
    2. 如果不是，Mocha 将忽略 --require 模块的任何导出
6. Mocha 验证通过 --require 或其他方式加载的任何自定义报告程序或接口
7. Mocha 发现测试文件； 当没有给定文件或目录时，它会在相对于当前工作目录的`test`目录（但不是其子目录）中找到扩展名为 .js、.mjs 或 .cjs 的文件
8. （默认）bdd 接口加载测试文件没有特定的顺序，这些文件被赋予一个特定于接口的`global`上下文（这就是如何，例如， `describe()` 在测试文件中作为全局结束）
    1. 加载测试文件时，Mocha 会执行其所有套件并查找（但不执行）其中的任何挂钩和测试。
    2. 顶级钩子、测试和套件都是“隐形”根套件的成员； 整个过程只有一个根套件
9. Mocha 运行[global setup fixtures](https://mochajs.org/#global-setup-fixtures)，如果有的话
10. 从"root"套件开始，Mocha 执行：
11. 任何"before all"钩子（对于root套件，这只发生一次；参见[root hook plugins](https://mochajs.org/#root-hook-plugins)）
12. 对于每个测试，Mocha 执行：
    1. Any “before each” hooks
    2. 测试（并报告结果）
    3. Any “after each” hooks
13. 如果当前套间有子套间，对每个子套间重复10.步骤； 每个子套件继承其父套件中定义的任何“before each”和“after each”挂钩
14. 任何“after all”钩子（对于根套件，这只发生一次；参见[root hook plugins](https://mochajs.org/#root-hook-plugins)）
15. Mocha 打印最终摘要/结语（如果适用）
16. Mocha 运行[global teardown fixtures](https://mochajs.org/#global-teardown-fixtures)，如果有的话

#### PARALLEL MODE
1. 从上面的串行模式重复步骤 1 到 6，跳过报告器验证
2. 找到的所有测试文件都放入队列中（它们不被主进程加载）
3. Mocha 运行[global setup fixtures](https://mochajs.org/#global-setup-fixtures)，如果有的话
4. Mocha 创建了一个子进程池（“workers”）
5. 在worker运行它收到的第一个测试之前，worker通过以下方式“引导”自己：
    1. 加载所有 --require 模块
    2. 注册任何根钩子插件
    3. 忽略全球固定装置和自定义记者
    4. 断言内置或自定义接口有效
6. 当worker收到要运行的测试文件时，worker会为单个测试文件创建一个新的 Mocha 实例，并且：
7. 工人从上面重复第 8 步
8. worker从上面重复第 10 步，但注意worker不直接报告测试结果； 它将它们保存在内存缓冲区中
9. 当worker员完成测试文件时，缓冲结果返回到主进程，然后将它们提供给用户指定的报告者（默认为 spec）
10. worker使自己对池可用； 该池为worker提供了另一个要运行的测试文件（如果有的话）
11. Mocha 打印最终摘要/结语（如果适用）
12. Mocha 运行[global teardown fixtures](https://mochajs.org/#global-teardown-fixtures)，如果有的话