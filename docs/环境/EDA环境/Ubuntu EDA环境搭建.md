# apt update 和 apt upgrade 的区别

在使用基于 Debian 的 Linux 发行版（如 Ubuntu）时，管理软件包是日常任务之一。`APT（Advanced Package Tool）`是用于处理这些任务的强大工具。其中，`apt update`和`apt upgrade`是两个常用的命令，但它们的功能和用途有所不同。下面将详细解释这两个命令的区别。

1. apt update

**功能**：更新本地包索引数据库。

**工作原理**：

- 当运行`apt update`时，APT 会从配置的软件源（repositories）中下载最新的包信息，并更新本地的包索引文件。这些文件包含了每个软件包的版本、依赖关系等信息。
- 这个过程不会安装或升级任何软件包；它只是确保你的系统知道有哪些可用的新版本软件包。

**使用场景**：

- 在执行任何涉及软件包安装的命令之前（如 `apt install` 或 `apt upgrade`），建议先运行 `apt update` 以确保你获取的是最新版本的软件包信息。

2. apt upgrade

**功能**：根据更新的包索引，升级已安装的软件包到最新版本。

- `apt install -y`：是确认安装的意思，加`-y`后不会提示你yes/no让你确认是否安装，会直接进行安装