本节介绍使用 Hardhat 时的常见问题以及如何解决这些问题。

### 编译大型项目时出现内存不足错误
如果您的项目有很多智能合约，编译它们可能需要比 Node 默认允许的内存更多的内存并崩溃。

如果您遇到此问题，可以使用 Hardhat 的 --max-memory 参数：
```
npx hardhat --max-memory 4096 compile
```

如果您发现自己一直在使用它，您可以在 .bashrc（如果使用 bash）或 .zshrc（如果使用 zsh）中使用环境变量设置它：export HARDHAT_MAX_MEMORY=4096。