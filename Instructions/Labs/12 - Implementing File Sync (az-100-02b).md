---
lab:
    title: 'Azure 文件同步'
    module: '模块 12 - 数据服务'
---

# 实验室：实现 Azure 文件同步

本实验室中的所有任务都是在 Azure 门户中执行的，练习 1 和练习 2 中在远程桌面与 Azure VM 的会话中执行的步骤例外。

实验室文件： 

-  **Labfiles\\Module_12\\Implementing_File_Sync\\az-100-02b_azuredeploy.json**

-  **Labfiles\\Module_12\\Implementing_File_Sync\\az-100-02b_azuredeploy.parameters.json**

### 场景
  
Adatum Corporation 在本地文件服务器中托管其文件共享。考虑到其将大部分工作负荷迁移到 Azure 的计划，Adatum 正在寻找最有效的方法，将其数据复制到 Azure 中提供的文件共享。为了实现该目标，Adatum 使用 Azure 文件同步。

### 目标
  
完成本实验室后，你将能够：

-  使用 Azure 资源管理器模板部署 Azure VM

-  准备 Azure 文件同步基础结构

-  实现和验证 Azure 文件同步


### 练习 0：准备实验室环境
  
本练习的主要任务如下：

1. 使用 Azure 资源管理器模板部署 Azure VM


#### 任务 1：使用 Azure 资源管理器模板部署 Azure VM

1. 从实验室虚拟机启动 Microsoft Edge，浏览到 Azure 门户 ([**http://portal.azure.com**](http://portal.azure.com))，然后使用 Microsoft 帐户登录，该帐户具有要在本实验室中使用的 Azure 订阅的所有者角色。

1. 在 Azure 门户中，导航到 **“新建”** 边栏选项卡。

1. 在 **新建** 边栏选项卡中，在 Azure 市场中搜索 **模板部署**。

1. 使用搜索结果列表导航到 **自定义部署** 边栏选项卡。

1. 在 **自定义部署** 边栏选项卡中，选择 **在编辑器中生成自己的模板**。

1. 在 **编辑模板** 边栏选项卡中，加载模板文件 **az-100-02b_azuredeploy.json**。 

   > **注意**：查看模板内容，并注意其定义了使用单个数据磁盘托管 Windows Server 2016 Datacenter 的 Azure VM 部署。

1. 请保存模板并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡。

1. 在 **编辑参数** 边栏选项卡中，加载参数文件 **az-100-02b_azuredeploy.parameters.json**。 

1. 保存参数并返回到 **自定义部署** 边栏选项卡。 

1. 在 **自定义部署** 边栏选项卡中，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1000201b-RG** 的名称

    - 位置：最靠近实验室位置的 Azure 区域的名称以及可以在其中预配 Azure VM 的位置

    - VM 大小：**Standard_DS1_v2**

    - VM 名称：**az1000201b-vm1**

    - 管理员用户名：**Student**

    - 管理员密码：**Pa55w.rd1234**

    - 虚拟网络名称：**az1000201b-vnet1**

   > **注意**：若要标识可以预配 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注意**：不必等待部署完成，请继续进行下一项练习。你将在本实验室的下一项练习中使用此部署中包含的虚拟机。

   > **注意**：请记住，Azure VM **az1000201b-vm1** 的用途是在我们的场景中模拟本地文件服务器。

> **结果**：完成本次练习后，已启动 Azure VM **az1000201b-vm1** 的模板部署，将在本实验室的下一项练习中使用该模板部署。


### 练习 1：准备 Azure 文件同步基础结构

本练习的主要任务如下：

1. 创建 Azure 存储帐户和文件共享

1. 准备将 Windows Server 2016 与 Azure 文件同步配合使用

1. 运行 Azure 文件同步评估工具


#### 任务 1：创建 Azure 存储帐户和文件共享

1. 在 Azure 门户中，导航到 **新建** 边栏选项卡.

1. 从 **新建** 边栏选项卡中，搜索 Azure 市场中的 **存储帐户**。

1. 使用搜索结果列表导航到 **创建存储帐户** 边栏选项卡。

1. 从 **创建存储账户** 边栏选项卡中，使用以下设置创建新的存储帐户： 

    - 订阅：在上一个任务中选择的同一订阅

    - 资源组：新资源组 **az1000202b-RG** 的名称

    - 存储帐户名称：任意有效的名称，长度为 3 到 24 个字符，由小写字母和数字组成

    - 位置：在上一个任务中选择的 Azure 区域名称

    - 性能：**标准**

    - 帐户类型：**存储（常规用途 v1）**

    - 复制：**本地冗余存储 (LRS)**

    - 需要安全传输：**禁用**

    - 允许从其中访问：**所有网络**

    - 分层命名空间：**禁用**

   > **注意**：等待预配存储帐户，然后继续执行下一步。

1. 在 Azure 门户中，导航到代表新预配的存储帐户的边栏选项卡。

1. 从存储帐户边栏选项卡中，显示其文件服务的属性。

1. 从存储账户 **“文件”** 边栏选项卡中，使用以下设置创建新的文件共享：

    - 名称：**az10002bshare1**

    - 配额：无


#### 任务 2：准备将 Windows Server 2016 与 Azure 文件同步配合使用

   > **注意**: 在开始此任务之前，请先确保已完成在练习 1 中启动的模板部署。 

1. 在 Azure 门户中，导航到 **az1000201b-VM1** 边栏选项卡。

1. 从 **az1000201b-vm1** 边栏选项卡中，通过 RDP 协议连接到 Azure VM，并在系统提示登录时提供以下凭据：

    - 管理员用户名：**Student**

    - 管理员密码：**Pa55w.rd1234**

1. 在与 Azure VM 的 RDP 会话中，在服务器管理器中导航到 **文件和存储服务**，找到附加到 Azure VM 的数据磁盘，将其初始化为 **GPT** 磁盘，并使用 **“新建卷向导”** 创建占用整个磁盘的单个卷，其设置如下：

    - 驱动器号？**S**

    - 文件系统？**NTFS**

    - 分配单元大小：**默认**

    - 卷标签：**Data**

1. 在 RDP 会话中，以管理员身份启动 Windows PowerShell 会话。 

1. 在 Windows PowerShell 控制台中，通过运行以下命令设置文件共享：

   ```powershell
   $directory = New-Item -Type Directory -Path 'S:\az10002bShare'

   New-SmbShare -Name $directory.Name -Path $directory.FullName -FullAccess 'Administrators' -ReadAccess Everyone   

   Copy-Item -Path 'C:\WindowsAzure\*' -Destination $directory.FullName –Recurse
   ```

   > **注意**：为了使用示例数据填充文件共享，我们使用 *C:\\WindowsAzure* 文件夹中的内容，该文件夹应包含约 100 MB 的文件

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装最新的 Az PowerShell 模块：

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

   > **注意**：系统发出提示时，确认是否要继续从 PSGallery 存储库进行安装。


#### 任务 3：运行 Azure 文件同步评估工具

1. 在与 Azure VM 的 RDP 会话中，通过运行以下命令，从 Windows PowerShell 控制台安装最新版本的包管理和 PowerShellGet：

   ```powershell
   Install-Module -Name PackageManagement -Repository PSGallery -Force

   Install-Module -Name PowerShellGet -Repository PSGallery -Force
   ```

   > **注意**：系统发出提示时，确认是否要继续安装 NuGet 提供程序。

1. 重启 PowerShell 会话。

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装 Azure 文件同步 PowerShell 模块：

   ```powershell
   Install-Module -Name Az.StorageSync -AllowClobber -Force
   ```

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装 Azure 文件同步 PowerShell 模块：

   ```powershell
   Invoke-AzStorageSyncCompatibilityCheck -Path 'S:\az10002bShare'
   ```

1. 查看结果并验证是否未发现任何兼容性问题。

> **结果**: 完成本次练习后，即已创建 Azure 存储帐户和文件共享，准备将 Windows Server 2016 与 Azure 文件同步配合使用，并运行 Azure 文件同步评估工具


### 练习 2：准备 Azure 文件同步基础结构

本练习的主要任务如下：

1. 部署存储同步服务

1. 安装 Azure 文件同步代理

1. 使用存储同步服务注册 Windows Server

1. 创建同步组和云终结点

1. 创建服务器终结点

1. 验证 Azure 文件同步操作


#### 任务 1：部署存储同步服务

1. 在与 Azure VM 的 RDP 会话中，在服务器管理器中导航到“本地服务器”视图并暂时关闭 **IE 增强的安全配置**。

1. 在与 Azure VM 的 RDP 会话中，启动 Internet Explorer，浏览到 Azure 门户 ([**http://portal.azure.com**](http://portal.azure.com))，并使用之前在本实验室中使用的同一 Microsoft 帐户登录。

1. 在Azure门户中，导航到 **新** 卡.

1. 来自 **新** 卡片，搜索Azure Marketplace **Azure文件同步**。

1. 使用搜索结果列表导航到 **部署存储同步** 卡。

1. 来自 **部署存储同步** 卡片，使用以下设置创建存储同步服务：

    - 名称：**az1000202b-ss**

    - 订阅：在上一个任务中选择的同一订阅

    - 资源组：新资源组 **az1000203b-RG** 的名称

    - 位置：在本练习的稍早部分，在其中创建了存储帐户 Azure 区域名称


#### 任务 2：安装 Azure 文件同步代理。

1. 在 RDP 会话中，启动另一个 Internet Explorer 实例，浏览到 Microsoft 下载中心 ([**https://go.microsoft.com/fwlink/?linkid=858257**](https://go.microsoft.com/fwlink/?linkid=858257)) 并下载 Azure 文件同步代理 Windows Installer 文件 **StorageSyncAgent_V6_WS2016.msi**。

1. 下载完成后，使用默认设置运行存储同步代理安装向导，安装 Azure 文件同步代理。

1. Azure 文件同步代理安装完毕后，**Azure 文件同步 - 服务器注册** 向导自动启动。


#### 任务 3：使用存储同步服务注册 Windows Server

1. 在 **Azure 文件同步 - 服务器注册** 向导的初始页面，使用之前在本实验室中使用的同一 Microsoft 帐户登录。

1. 在 **Azure 文件同步 - 服务器注册”** 向导的 **选择存储同步服务** 页面，指定以下设置以进行注册：

    - 订阅：你用于本逻辑阵列块的订阅名称。

    - 资源组：**az1000203b-RG**

    - 存储同步服务：**az1000202b-ss**

1. 系统发出提示时，使用之前在本实验室中使用的同一 Microsoft 帐户再次登录。


#### 任务 4：创建同步组和云终结点

1. 在Azure VM的RDP会话中，在Azure门户中，导航到 **az1000202b-SS** 存储同步服务卡片。

1. 来自 **az1000202b-SS** 存储同步服务卡片，导航到 **同步组** 卡片并使用以下设置创建新的同步组：

    - 同步组名称：**az1000202b-syncgroup1**

    - 订阅：你用于本逻辑阵列块的订阅名称。

    - 存储帐户：在上一个练习中创建的存储帐户的资源 ID

    - Azure 文件共享：**az10002bshare1**


#### 任务 5：创建服务器终结点

1. 在Azure VM的RDP会话中，在Azure门户中，来自 **az1000202b-SS** 存储同步服务卡片，导航到 **az1000202b-syncgroup1** 卡。

1. 来自 **az1000202b-syncgroup1** 卡片，导航到 **添加服务器端点** 卡片并使用以下设置创建新的服务器端点：

    - 注册的服务器：**az1000201b-vm1**

    - 路径： **S:\\az10002bShare**

    - 云分层：**已启用**

        - 始终保留卷上指定的可用空间百分比：**15**

        - 仅缓存在指定天数内访问或修改的文件：**30**

    - 串行数据传输 **禁用**


#### 任务 6：验证 Azure 文件同步操作

1. 在Azure VM的RDP会话中，在Azure门户中，监视服务器端点的运行状况 **az100021b-VM1** 在...上 **az1000202b-syncgroup1** 卡片，因为它改变了 **供应** 至 **有待** 并最终成为一个绿色的复选标记。

   > **注意**: 应在几分钟后继续执行下一步。 

1. 在 Azure 门户中，导航到先前在实验室中创建的存储帐户对应的边栏选项卡，切换到 **文件** 选项卡，然后单击 **az10002bshare1**。

1. 在 **az10002bshare1** 边栏选项卡上，单击 **连接**。

1. 在 **“连接”** 边栏选项卡中，将从 Windows 计算机连接到文件共享的 PowerShell 命令复制到剪贴板中。

1. 在 RDP 会话中，启动 Windows PowerShell ISE 会话。 

1. 在 Windows PowerShell ISE 会话中，打开脚本窗格并将该命令粘贴到本地剪贴板的内容中。

1. 将 `-Persist` 开关添加到包含 `New-PSDrive` cmdlet 的行的末尾。

1. 执行脚本并验证其输出是否确认 Z: 驱动器成功映射到 Azure 存储文件服务共享。

1. 在 RDP 会话中，启动文件资源管理器，导航到 Z: 驱动器，并验证其是否包含与 S:\\az10002bShare 相同的内容

1. 显示 Z: 驱动器上各个文件夹的“属性”窗口，查看“安全性”选项卡，并注意这些项表示分配给 S: 驱动器上相应文件夹的 NTFS 权限。


> **结果**: 完成本次练习后，就已部署了存储同步服务，安装了 Azure 文件同步代理，使用存储同步服务注册了 Windows Server，创建了同步组和云终结点，创建了服务器终结点并验证了 Azure 文件同步操作。
