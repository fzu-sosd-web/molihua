# 安装docker
## 完整脚本
    sudo apt update 
    sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common -y 
    
    sudo apt-get remove docker docker.io containerd runc -y 
    
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 
    
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" 
    
    sudo apt update 
    sudo apt install docker-ce docker-ce-cli containerd.io -y
### 1. 先使用命名去更新软件包索引

    sudo apt update
**PS**:APT软件包索引是记录了可用软件包的元数据、版本信息、依赖关系和其他相关信息。软件包索引是操作系统中的一个关键组成部分，用于帮助系统管理和维护已安装的软件包，以及在需要时安装新的软件包通过更新软件包索引，用户可以获取最新的软件包信息，包括新版本、安全更新和新功能，以便更轻松地管理和维护系统中的软件、

### 2. 然后再用curl指令导入docker仓库的GPG密钥并安装docker运行命令

    sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common -y 
**解释**：
- `apt-transport-https`：这是一个 APT（Advanced Package Tool）软件包管理器的传输插件，它允许通过 HTTPS 协议下载和安装软件包。HTTPS 是一种安全的通信协议，可确保在传输软件包时数据是加密的，因此更安全。

- `ca-certificates`：这是包含根证书和可信任的 SSL 证书的软件包。它是确保系统能够验证通过 HTTPS 提供的软件源的证书有效性的一部分。没有正确的 SSL 证书，系统可能无法建立安全的连接到外部资源。

- `curl`：curl 是一个用于在命令行中传输数据的工具，通常用于下载文件和与网络服务交互。在这种情况下，它可能被用于添加软件源或下载 GPG 密钥。

- `gnupg-agent`：这是 GNU Privacy Guard（GnuPG 或 GPG）的一个组件，用于管理加密密钥和数字签名。在软件包管理中，它通常用于验证下载的软件包和软件源的签名，确保它们没有被篡改。

- `software-properties-common`：这个软件包包含一些常用的软件包源管理工具和脚本，可以方便地添加、删除和管理软件源。

综合来说，运行这些命令是为了确保系统能够通过 HTTPS 安全地下载软件包、验证软件源的签名，并具备添加和管理新软件源的工具。这对于安装和更新软件非常重要，特别是在从不同来源获取软件包时。

### 3. 然后删除原有的docker服务（卸载与 Docker 相关的软件包和容器运行时组件，以彻底移除 Docker 容器平台及其关联的工具）
    sudo apt-get remove docker docker.io containerd runc -y 

### 4.安装 Docker 软件包
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
**解释**：
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg`：这部分使用 curl 命令从指定的 URL（https://download.docker.com/linux/ubuntu/gpg）下载 GPG 公钥。

`-f`：表示在请求失败时不显示错误消息，以保持输出简洁。  
`-s`：表示以静默模式运行，不显示进度信息或其他提示。  
`-S`：表示在发生错误时仍然显示错误消息，但仅在 -s 和 -f 都存在时有效。  
`-L`：表示要求 curl 遵循重定向，以确保从指定的 URL 下载 GPG 密钥。  
`|`：这是管道操作符，它将 curl 命令的输出传递给下一个命令。  
`sudo apt-key add -`：这部分使用 sudo 命令以超级用户权限来运行 apt-key add 命令，将 curl 命令下载的 GPG 公钥添加到系统的 GPG 密钥环中。  
`-` 表示从标准输入中读取 GPG 公钥数据。

总结起来，这个命令的目的是从 Docker 官方网站下载 Docker 软件仓库的 GPG 公钥，并将其添加到系统，以便在安装 Docker 软件包时，系统能够使用该密钥验证软件包的签名，确保软件包的完整性和真实性。这有助于防止恶意软件或篡改的软件包被安装到系统中。
### 5. 安装docker软件仓库
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" 
    sudo apt update 
    sudo apt install docker-ce docker-ce-cli containerd.io -y
**解释**：  
- `add-apt-repository`：这是一个 Ubuntu 的命令，用于添加新的 APT 软件包源。  
- `"deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`：这是软件包源的描述。它告诉系统从指定的 URL 
- `https://download.docker.com/linux/ubuntu` 下载 Docker 软件包，并使用当前系统的发行代号 $(lsb_release -cs)，通常是 Ubuntu 版本的代号，例如 "focal" 或 "bionic"。stable 表示使用 Docker 的稳定版本。这个命令将在系统中创建一个新的软件包源文件，以便后续通过 apt 包管理器安装 Docker。
- `sudo apt install docker-ce docker-ce-cli containerd.io -y`：
这个命令用于安装 Docker 软件包。具体来说，它安装了 Docker 的核心组件，包括 Docker 引擎 (docker-ce)、Docker 命令行工具 (docker-ce-cli) 和容器运行时 (containerd.io)。  
- `-y` 选项表示在提示是否安装时自动确认，以便无需手动确认安装过程。

## 解除sudo运行docker（推荐）
    sudo usermod -aG docker $USER
    newgrp docker
**解释**：$USER是保存您当前用户名的环境变量，newgrp命令使usermod命令更改在当前终端中生效。

# 删除docker
如果你保留Docker的数据我们建议你运行下面命令卸载Docker。

    sudo apt remove containerd.io docker-compose-plugin docker-ce


如果你删除了Docker所有数据，可以使用

    sudo apt purge containerd.io docker-compose-plugin docker-ce
卸载Docker。这也会把Docker的配置文件删除。

# 配置docker镜像源
### 1.创建或修改配置文件

    vim /etc/docker/daemon.json

### 编辑内容
    {
        "registry-mirrors": [
            "https://mirror.ccs.tencentyun.com",
            "https://docker.mirrors.ustc.edu.cn",
            "https://hub-mirror.c.163.com"
        ]
    }
vim: i编写模式，esc命令模式，:wq保存并退出