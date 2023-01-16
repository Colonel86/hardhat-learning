## 扁平化你的合约
Hardhat 带有一个内置的扁平化任务，可以让你组合多个 Solidity 文件的源代码。

#### Flattening all your files
如果您在不传递任何其他参数的情况下使用 flatten 任务，则项目中的所有 Solidity 文件都将合并：
```
$ npx hardhat flatten
// Sources flattened with hardhat v2.12.3 https://hardhat.org

// File contracts/Bar.sol

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Bar {}

// File contracts/Qux.sol

...
```

结果将打印到标准输出。 您可以使用 > 重定向运算符创建包含扁平化源的文件：
```
$ npx hardhat flatten > Flattened.sol

$ cat Flattened.sol
// Sources flattened with hardhat v2.12.3 https://hardhat.org

// File contracts/Bar.sol

...
```

### Flattening specific files
`flatten`任务可以接收到您要flatten的文件的路径：
```
npx hardhat flatten contracts/Foo.sol
```
在这种情况下，结果将包含 Foo.sol 的源代码及其所有传递依赖项（它导入的文件，以及这些文件导入的文件，等等）。

您还可以使用多个文件：
```
npx hardhat flatten contracts/Foo.sol contracts/Bar.sol
```

但如果 Bar.sol 是 Foo.sol 的依赖项，那么结果将与前面的示例相同。

如前一节所述，您可以将输出重定向到某个文件：
```
npx hardhat flatten contracts/Foo.sol > Flattened.sol
```
### 循环依赖
具有循环依赖的项目目前无法展开。 如果这是您需要的东西，请对[此问题](https://github.com/NomicFoundation/hardhat/issues/1486)进行投票或评论。