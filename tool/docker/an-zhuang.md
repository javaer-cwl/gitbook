# 🏹 安装

### **一、Windows 系统安装 Docker**

#### **1. 系统要求**

* Windows 10 64位（专业版、企业版或教育版，版本 1903 或更高）
* 启用 **Hyper-V** 和 **WSL 2**（Windows Subsystem for Linux 2）

#### **2. 安装步骤**

1. **启用 Hyper-V 和 WSL 2**
   *   打开 PowerShell（管理员权限），运行以下命令：

       ```powershell
       dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V /all /norestartwsl --install
       ```
   * 重启电脑。
2. **下载 Docker Desktop**
   * 访问 Docker 官网：https://www.docker.com/products/docker-desktop
   * 下载适用于 Windows 的 Docker Desktop 安装包。
3. **安装 Docker Desktop**
   * 双击下载的安装包，按照提示完成安装。
   * 安装完成后，启动 Docker Desktop。
4. **验证安装**
   *   打开 PowerShell 或命令提示符，运行以下命令：

       ```bash
       docker --version
       ```
   * 如果显示 Docker 版本信息，说明安装成功。

***

### **二、macOS 系统安装 Docker**

#### **1. 系统要求**

* macOS 10.15 或更高版本（Catalina、Big Sur、Monterey 等）

#### **2. 安装步骤**

1. **下载 Docker Desktop**
   * 访问 Docker 官网：https://www.docker.com/products/docker-desktop
   * 下载适用于 macOS 的 Docker Desktop 安装包。
2. **安装 Docker Desktop**
   * 双击下载的 `.dmg` 文件，将 Docker 图标拖到 Applications 文件夹。
   * 打开 Applications 文件夹，双击 Docker 图标启动 Docker Desktop。
3. **验证安装**
   *   打开终端，运行以下命令：

       ```bash
       docker --version
       ```
   * 如果显示 Docker 版本信息，说明安装成功。

***

### **三、Linux 系统安装 Docker**

#### **1. 系统要求**

* 支持大多数 Linux 发行版（Ubuntu、Debian、CentOS、Fedora 等）
* 需要 root 权限或 sudo 权限

#### **2. 安装步骤（以 Ubuntu 为例）**

1.  **卸载旧版本（可选）**

    ```bash
    sudo apt-get remove docker docker-engine docker.io containerd runc
    ```
2.  **更新包索引**

    ```bash
    sudo apt-get update
    ```
3.  **安装依赖包**

    ```bash
    sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
    ```
4.  **添加 Docker 官方 GPG 密钥**

    ```bash
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    ```
5.  **设置稳定版仓库**

    ```bash
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```
6.  **安装 Docker Engine**

    ```bash
    sudo apt-get updatesudo apt-get install docker-ce docker-ce-cli containerd.io
    ```
7.  **启动 Docker 服务**

    ```bash
    sudo systemctl start dockersudo systemctl enable docker
    ```
8.  **验证安装**

    ```bash
    docker --version
    ```

    * 如果显示 Docker 版本信息，说明安装成功。
9.  **非 root 用户使用 Docker（可选）**

    ```bash
    sudo usermod -aG docker $USER
    ```

    * 注销并重新登录，使更改生效。

***

### **四、验证 Docker 是否正常工作**

在任意系统上，运行以下命令验证 Docker 是否正常工作：

```bash
docker run hello-world
```

如果看到 "Hello from Docker!" 的消息，说明 Docker 安装并运行成功。

