Hardhat 有一个配套的 npm 包，作为 `npx hardhat` 的简写，同时，它可以在您的终端中启用命令行完成。

这个软件包是 `hardhat-shorthand`，它安装了一个名为 `hh` 的全局可访问二进制文件，它运行本地安装的 `hardhat`。

### Installation
```
npm install --global hardhat-shorthand
```

执行此操作后，运行 `hh` 将等同于运行 npx hardhat。 例如，您可以运行 `hh compile` 而不是运行 `npx hardhat compile`。

#### 安装命令行补全
要启用自动完成支持，您还需要使用 `hardhat-completion` 安装 shell 补全脚本，它随 `hardhat-shorthand` 一起提供。 运行 `hardhat-completion install` 并按照说明安装完成脚本：
```
$ hardhat-completion install
✔ Which Shell do you use ? · zsh
✔ We will install completion to ~/.zshrc, is it ok ? (y/N) · true
=> Added tabtab source line in "~/.zshrc" file
=> Added tabtab source line in "~/.config/tabtab/zsh/__tabtab.zsh" file
=> Wrote completion script to /home/fvictorio/.config/tabtab/zsh/hh.zsh file

      => Tabtab source line added to ~/.zshrc for hh package.

      Make sure to reload your SHELL.
```
要尝试一下，请打开一个新终端，转到 Hardhat 项目的目录，然后尝试键入 hh，然后按 tab 键：


### Context
出于最佳实践，Hardhat 项目使用 npm 包 `hardhat` 的本地安装，以确保从事该项目的每个人都使用相同的版本。 这就是为什么您需要使用 npx 或 npm 脚本来运行 Hardhat。

这种方法的缺点是无法直接为 hardhat 命令提供自动完成建议，并且会使 CLI 命令变长。 这是 hh 解决的两个问题。

### 故障排除
#### “自动补全不起作用”
首先，确保您使用 `hardhat-completion install` 安装了自动完成脚本，然后重新加载您的 shell 或打开一个新终端重试。

如果仍有问题，请确保您的 Hardhat 配置没有任何问题。 您只需运行 `hh` 即可完成此操作。 如果该命令打印出帮助消息，则说明您的配置没有问题。 如果没有，您将看到问题所在。