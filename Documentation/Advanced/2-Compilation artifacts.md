# 编译工件
使用 Hardhat 编译会为每个编译后的合约（不是每个 .sol 文件）生成两个文件：一个artifact和一个debug file。

artifact具有部署和与合约交互所需的所有信息。 这些与大多数工具兼容，包括 Truffle 的artifact格式。 每个artifact都包含一个具有以下属性的 json：
- `contractName`：带有合约名称的字符串。
- `abi`：[合约 ABI 的 JSON 描述](https://solidity.readthedocs.io/en/latest/abi-spec.html#abi-json)
- `bytecode`：未链接部署字节码的“0x”前缀十六进制字符串。 如果合约不可部署，则它有字符串“0x”。
- `deployedBytecode`：未链接的运行时/已部署字节码的“0x”前缀十六进制字符串。 如果合约不可部署，则它有字符串“0x”。
- `linkReferences`：字节码的链接引用 solc 返回的对象。 如果不需要链接合约，则此值包含一个空对象。
- `deployedLinkReferences`：已部署字节码的链接引用 solc 返回的对象。 如果不需要链接合约，则此值包含一个空对象。

**debug file**包含重现编译和调试合约所需的所有信息：这包括原始的 solc 输入和输出，以及用于编译它的 solc 版本。

### 构建信息文件
Hardhat 通过一次编译尽可能少的文件集来优化编译。 一起编译的文件具有相同的 solc 输入和输出。 由于在每个调试文件中包含此信息会造成有意义的浪费，因此此信息会在放置在 artifacts/build-info 中的构建信息文件中进行重复数据删除。 每个合约调试文件都包含其构建信息文件的相对路径，每个构建信息文件都包含 solc 输入、solc 输出和使用的 solc 版本。

您不应直接与这些文件进行交互。

### Reading artifacts
[HRE] 有一个带有辅助方法的工件对象。 例如，您可以通过调用 hre.artifacts.getArtifactPaths() 获取包含所有工件路径的列表。

您还可以通过调用 hre.artifacts.readArtifact("Bar") 使用合约名称读取工件，这将返回 Bar 合约的工件内容。 这只有在整个项目中只有一个名为 Bar 的合约时才有效； 如果有两个，它会抛出一个错误。 要消除这种情况的歧义，您必须使用合同的完全限定名称：
```
hre.artifacts.readArtifact("contracts/Bar.sol:Bar").
```

### Directory structure
artifacts/ 目录的结构遵循合约的原始目录结构。 例如，如果您的合同如下所示：
```
contracts
├── Foo.sol
├── Bar.sol
└── Qux.sol
```
那么您的artifact目录的结构将如下所示：
```
artifacts
└── contracts
    ├── Foo.sol
    │   ├── Foo.json
    │   ├── Foo.dbg.json
    │   ├── Foo2.json
    │   └── Foo2.dbg.json
    ├── Bar.sol
    │   ├── Bar.json
    │   └── Bar.dbg.json
    └── Qux.sol
        ├── Foo.json
        └── Foo.dbg.json
```
源代码中的每个 Solidity 文件都将在artifact结构中获得一个目录。 这些目录中的每一个都包含一个工件 (.json) 文件和一个针对该文件中每个合约的调试 (.dbg.json) 文件。 例如，Foo.sol 中包含两个合约。

两个 Solidity 文件可以有同名的合约，这种结构允许这样做。