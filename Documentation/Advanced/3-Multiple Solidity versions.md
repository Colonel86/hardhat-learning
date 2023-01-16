Hardhat 支持使用不同的、不兼容的 solc 版本的项目。 例如，如果您有一个项目，其中一些文件使用 Solidity 0.5 而其他文件使用 0.6，您可以将 Hardhat 配置为使用与这些文件兼容的编译器版本，如下所示：
```
module.exports = {
  solidity: {
    compilers: [
      {
        version: "0.5.5",
      },
      {
        version: "0.6.7",
        settings: {},
      },
    ],
  },
};
```
此设置意味着编译指示 solidity ^0.5.0 的文件将使用 solc 0.5.5 编译，编译指示 solidity ^0.6.0 的文件将使用 solc 0.6.7 编译。

可能会发生一个文件可以使用多个已配置的编译器进行编译的情况，例如 pragma solidity >=0.5.0 的文件。 在这种情况下，将使用具有最高版本的兼容编译器（本例中为 0.6.7）。 如果您不希望这种情况发生，您可以通过使用覆盖为每个文件指定应该使用哪个编译器：
```
module.exports = {
  solidity: {
    compilers: [...],
    overrides: {
      "contracts/Foo.sol": {
        version: "0.5.5",
        settings: { }
      }
    }
  }
}
```
在这种情况下，contracts/Foo.sol 将使用 solc 0.5.5 进行编译，无论 solidity.compilers 条目中包含什么。

请记住：
- Overrides是完整的编译器配置，因此如果您使用任何其他设置，您也应该为覆盖设置它们。
- 即使您使用的是 Windows，也必须使用正斜杠 (/)。