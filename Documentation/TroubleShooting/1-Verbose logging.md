### 详细日志记录
您可以通过使用其 `--verbose` 标志运行 Hardhat 或通过将 HARDHAT_VERBOSE 环境变量设置为 true 来启用 Hardhat 的详细模式。

此模式将打印大量对调试非常有用的输出。 Hardhat 在详细模式下运行的示例是：
```
$ npx hardhat test --verbose
  hardhat:core:hre Creating HardhatRuntimeEnvironment +0ms
  hardhat:core:hre Running task test +93ms
  hardhat:core:hre Running task compile +1ms
  hardhat:core:hre Running task compile:get-compilation-tasks +0ms
  hardhat:core:hre Running task compile:solidity +0ms
  hardhat:core:hre Running task compile:solidity:get-source-paths +0ms
  hardhat:core:hre Running task compile:solidity:get-source-names +9ms
  hardhat:core:hre Running task compile:solidity:get-dependency-graph +4ms
  hardhat:core:hre Running task compile:solidity:get-compilation-jobs +10ms
  hardhat:core:tasks:compile The dependency graph was dividied in '1' connected components +0ms
  hardhat:core:hre Running task compile:solidity:get-compilation-job-for-file +1ms
  hardhat:core:compilation-job File '/tmp/hardhat-project/contracts/Greeter.sol' will be compiled with version '0.7.3' +0ms
  hardhat:core:compilation-job File '/tmp/hardhat-project/node_modules/hardhat/console.sol' added as dependency of '/tmp/hardhat-project/contracts/Greeter.sol' +0ms
  hardhat:core:hre Running task compile:solidity:get-compilation-job-for-file +13ms
  hardhat:core:compilation-job File '/tmp/hardhat-project/node_modules/hardhat/console.sol' will be compiled with version '0.7.3' +1ms
  hardhat:core:hre Running task compile:solidity:handle-compilation-jobs-failures +1ms
  hardhat:core:hre Running task compile:solidity:filter-compilation-jobs +0ms
  hardhat:core:tasks:compile '1' jobs were filtered out +15ms
  hardhat:core:hre Running task compile:solidity:merge-compilation-jobs +1ms
  hardhat:core:hre Running task compile:solidity:compile-jobs +1ms
  hardhat:core:tasks:compile No compilation jobs to compile +1ms
  hardhat:core:hre Running task compile:solidity:log:nothing-to-compile +0ms
  hardhat:core:hre Running task compile:solidity:log:compilation-result +6ms
  hardhat:core:hre Running task test:get-test-files +1ms
  hardhat:core:hre Running task test:setup-test-environment +0ms
  hardhat:core:hre Running task test:show-fork-recommendations +0ms
  hardhat:core:hre Running task test:run-mocha-tests +0ms


  Greeter
  hardhat:core:hre Creating provider for network hardhat +78ms
Deploying a Greeter with greeting: Hello, world!
Changing greeting from 'Hello, world!' to 'Hola, mundo!'
    ✓ Should return the new greeting once it's changed (769ms)


  1 passing (771ms)

  hardhat:core:cli Killing Hardhat after successfully running task test +0ms
```
Hardhat 使用[debug](https://github.com/visionmedia/debug)包来管理日志记录。 DEBUG 环境变量可用于打开详细日志记录并使用简单的通配符模式对其进行过滤。
