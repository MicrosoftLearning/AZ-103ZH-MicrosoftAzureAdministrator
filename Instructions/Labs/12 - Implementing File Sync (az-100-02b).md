---
lab:
    title: 'Azure 文件同步'
    module: '模块 12 - 数据服务'
---

# 实验：实现 Azure 文件同步

此实验室中的所有任务都是从 Azure 门户执行的，但练习 1 和练习 2 中的步骤是在到 Azure VM 的远程桌面会话中执行的。

实验室文件： 

-  **Labfiles\\Module_12\\Implementing_File_Sync\\az-100-02b_azuredeploy.json**

-  **Labfiles\\Module_12\\Implementing_File_Sync\\az-100-02b_azuredeploy.parameters.json**

### 场景
  
Adatum Corporation 在本地文件服务器中托管其文件共享。考虑到其将大部分工作负载迁移到 Azure 的计划，Adatum 正在寻找将其数据复制到 Azure 中可用的文件共享的最有效方法。要实现它，Adatum 将使用 Azure 文件同步。

### 目标
  
完成本实验室后，你将能够：

-  使用 Azure 资源管理器模板部署 Azure VM

-  准备 Azure 文件同步基础结构

-  实施和验证 Azure 文件同步


### 练习 0：准备实验室环境
  
本次练习的主要任务如下：

1. 使用 Azure 资源管理器模板部署 Azure VM


#### 任务 1：使用 Azure 资源管理器模板部署 Azure VM

1. 在实验室虚拟机中启动 Microsoft Edge，浏览到 [**http：//portal.azure.com**](http://portal.azure.com) 的 Azure 门户，并使用您打算在本实验室中使用的 Azure 订阅中具有所有者角色的 Microsoft 帐户登录。

1. 在 Azure 门户中，请导航到**新建**边栏选项卡。

1. 在**新建**边栏选项卡，在 Azure 市场中搜索**模板部署**。

1. 使用搜索结果列表，导航到**“自定义部署”**边栏选项卡。

1. 在**自定义部署**边栏选项卡上，选择**在编辑器中生成自己的模板**。

1. 从**编辑模板**边栏选项卡加载模板文件 **az-100-02b_azuredeploy.json**。 

   > **注意**：查看模板的内容，并注意它定义了使用单个数据磁盘托管 Windows Server 2016 数据中心的 Azure VM 的部署。

1. 保存模板并返回到**自定义部署**边栏选项卡。 

1. 从**自定义部署**边栏选项卡，导航到**编辑参数**边栏选项卡。

1. 从**编辑参数**边栏选项卡，加载参数文件 **az-100-02b_azuredeploy.parameters.json**。 

1. 保存参数并返回到**自定义部署**边栏选项卡。 

1. 从**自定义部署**边栏选项卡，使用以下设置初始化模板部署：

    - 订阅：你在此实验室中使用的订阅的名称

    - 资源组：新资源组的名称 **az1000201b-RG**

    - 位置：最接近实验室位置的 Azure 区域的名称，在那里您可以配置 Azure VM

    - VM 大小：**Standard_DS1_v2**

    - VM 名称： **az1000201b-VM1**

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

    - 虚拟网络名称： **az1000201b-vnet1**

   > **注意**：若要标识可在其中配置 Azure VM 的 Azure 区域，请参阅[**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **备注**：无需等待部署完成即可继续进行下一项练习。你将在本实验室的下一项练习中使用此次部署中包含的虚拟机。

   > **注意**：请记住 Azure VM **az1000201b-VM1** 的用途是在我们的方案中模拟本地文件服务器。

> **结果**：完成本练习后，您就完成了 Azure VM **az1000201b-VM1** 的模板部署，您将在本实验室的下一个练习中使用。


### 练习 1：准备 Azure 文件同步基础结构

本练习的主要任务如下：

1. 创建 Azure 存储帐户和文件共享

1. 准备 Windows Server 2016 以供 Azure 文件同步使用

1. 运行 Azure 文件同步评估工具


#### 任务 1：创建 Azure 存储帐户和文件共享

1. 在 Azure 门户中，导航到**新建**边栏选项卡。

1. 从**“新建”**边栏选项卡，搜索 Azure 市场中的**“存储帐户”**。

1. 使用搜索结果列表导航到**创建存储帐户**边栏选项卡。

1. 从**“创建存储账户”**边栏选项卡，使用以下设置创建新的存储帐户： 

    - 订阅：您在上一个任务中选择的订阅

    - 资源组：新资源组名称**az1000202b-RG**

    - 存储帐户名称：由小写字母和数字构成的一个长度在 3 到 24 个字符之间的唯一有效字符串

    - 位置：你在上一个任务中选择的 Azure 区域的名称

    - 性能：**标准**

    - 帐户类型：**存储（通用 v1）**

    - 复制：**本地冗余存储 (LRS)**

    - 连接方法：**公用终结点（所有网络）**
    
    - 需要安全传输：**已禁用**
    
    - 大型文件共享：**禁用**
    
    - Blob 软删除：**禁用**
    
    - 分层命名空间：**已禁用**

   > **注意**：等待预配存储帐户，然后继续执行下一步。

1. 在 Azure 门户中，导航到代表新配置的存储帐户的边栏选项卡。

1. 从存储帐户边栏选项卡，显示其“文件共享”边栏选项卡。

1. 在存储帐户**文件共享**边栏选项卡中，使用以下设置创建新的文件共享：

    - 名称：**az10002bshare1**

    - 配额：无


#### 任务 2：准备 Windows Server 2016 以供 Azure 文件同步使用

   > **注意**：在开始此任务之前，请确保您在练习 0 中开始的模板部署已完成。 

1. 在 Azure 门户中，导航到 **az1000201b-VM1** 边栏选项卡。

1. 从 **az1000201b-VM1** 边栏选项卡，通过 RDP 协议连接到 Azure VM，并在提示登录时提供以下凭据：

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

1. 在 Azure VM 的 RDP 会话中，在服务器管理器中，导航到**文件和存储服务**，找到连接到 Azure VM 的数据磁盘，将其初始化为 **GPT** 磁盘，并使用**新建卷向导**使用以下设置创建占用整个磁盘的单个卷：

    - 驱动器号：**S**

    - 文件系统：**NTFS**

    - 分配单元大小：**默认**

    - 卷标签：**数据**

1. 在 RDP 会话中，以管理员身份启动 Windows PowerShell 会话。 

1. 在 Windows PowerShell 控制台中，通过运行以下命令设置文件共享：

```powershell
   $directory = New-Item -Type Directory -Path 'S:\az10002bShare'

   New-SmbShare -Name $directory.Name -Path $directory.FullName -FullAccess 'Administrators' -ReadAccess Everyone   

   Copy-Item -Path 'C:\WindowsAzure\*' -Destination $directory.FullName –Recurse
```

   > **备注**：为了使用示例数据填充文件共享，我们使用 C:\\WindowsAzure 文件夹中的内容，该文件夹应包含约 100 MB 的文件**

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装最新的 Az PowerShell 模块：

```powershell
   Install-Module -Name Az -AllowClobber
```

   > **备注**：出现提示时，确认您要继续从 PSGallery 存储库进行安装。


#### 任务 3：运行 Azure 文件同步评估工具

1. 在 Azure VM 的 RDP 会话中，通过运行以下命令从 Windows PowerShell 控制台安装最新版本的包管理和 PowerShellGet：

```powershell
   Install-Module -Name PackageManagement -Repository PSGallery -Force

   Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

   > **备注**：出现提示时，确认您要继续安装 NuGet 提供程序。

1. 重新启动 PowerShell 会话。

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装 Azure 文件同步 PowerShell 模块：

```powershell
   Install-Module -Name Az.StorageSync -AllowClobber -Force
```

1. 从 Windows PowerShell 控制台，检查 S:\az10002bShare 文件共享与 Azure 文件同步是否不存在任何兼容性问题：

```powershell
   Invoke-AzStorageSyncCompatibilityCheck -Path 'S:\az10002bShare'
```

1. 查看结果并验证没有兼容性问题。

> **结果**：完成本练习后，您就创建了 Azure 存储帐户和文件共享，然后请准备 Windows Server 2016 以供 Azure 文件同步使用，并运行 Azure 文件同步评估工具


### 练习 2：准备 Azure 文件同步基础结构

本练习的主要任务如下：

1. 部署存储同步服务

1. 安装 Azure 文件同步代理

1. 使用存储同步服务注册 Windows Server

1. 创建同步组和云终结点

1. 创建服务器终结点

1. 验证 Azure 文件同步操作


#### 任务 1：部署存储同步服务

1. 在 Azure VM 的 RDP 会话中，在服务器管理器中，导航到“本地服务器”视图并暂时关闭 **IE 增强安全配置**。

1. 在 Azure VM 的 RDP 会话中，启动 Internet Explorer，浏览到 Azure 门户 [**http://portal.azure.com**](http://portal.azure.com)，并使用您之前在本实验室中使用的相同 Microsoft 帐户登录。

1. 在 Azure 门户中，导航到**新建**边栏选项卡。

1. 从**新建**边栏选项卡，在 Azure 市场中搜索 **Azure 文件同步**。

1. 使用搜索结果，导航到“**部署存储同步**”边栏选项卡。

1. 从“**部署存储同步**”边栏选项卡，创建存储同步服务并采用以下设置：

    - 订阅：您在上一个任务中选择的订阅

    - 资源组：新资源组名称**az1000203b-RG**

    - 存储同步服务名称：**az1000202b-ss**
    
    - 区域：你在本练习前面部分创建的存储帐户所在的 Azure 区域


#### 任务2：安装 Azure 文件同步代理。

1. 在 RDP 会话中，启动另一个 Internet Explorer 实例，浏览到 Microsoft 下载中心 [**https://go.microsoft.com/fwlink/?linkid=858257**](https://go.microsoft.com/fwlink/?linkid=858257) 并下载 Azure 文件同步代理 Windows Installer 文件 **StorageSyncAgent_WS2016.msi**。

1. 下载完成后，使用默认设置运行存储同步代理安装向导，安装 Azure 文件同步代理。

1. Azure 文件同步代理安装完成后， **Azure 文件同步 - 服务器注册**向导会自动启动。


#### 任务 3：使用存储同步服务注册 Windows Server

1. 从 **Azure 文件同步 - 服务器注册**向导的起始页面，使用您之前在本实验室中使用的相同 Microsoft 帐户登录。

1. 在 **Azure 文件同步 - 服务器注册**向导的**选择存储同步服务**页面，指定以下设置进行注册：

    - Azure 订阅：你用于本实验室的订阅名称

    - 资源组： **az1000203b-RG**

    - 存储同步服务：**az1000202b-ss**

1. 出现提示时，使用您之前在本实验室中使用的相同 Microsof t帐户再次登录。


#### 任务 4：创建同步组和云终结点

1. 在 Azure VM 的 RDP 会话中，在 Azure 门户中，导航到 **az1000202b-ss** 存储同步服务边栏选项卡。

1. 从 **az1000202b-ss** 存储同步服务边栏选项卡，导航到**同步组**边栏选项卡，并使用以下设置创建新的同步组：

    - 同步组名称： **az1000202b-syncgroup1**

    - Azure 订阅：你用于本实验室的订阅名称

    - 存储帐户：您在上一个练习中创建的存储帐户的资源 ID

    - Azure 文件共享：**az10002bshare1**


#### 任务 5：创建服务器终结点

1. 在 Azure VM 的 RDP 会话中，在 Azure 门户中，从 **az1000202b-ss** 存储同步服务边栏选项卡，导航到 **az1000202b-syncgroup1** 边栏选项卡。

1. 从 **az1000202b-syncgroup1** 边栏选项卡，导航到**添加服务器终结点**边栏选项卡，并使用以下设置创建新的服务器终结点：

    - 注册服务器：**az1000201b-VM1**

    - 路径：**S:\\az10002bShare**

    - 云分层：**已启用**

        - 始终在卷上保留指定的可用空间百分比：**15**

        - 仅缓存在指定天数内访问或修改的文件：**30**

    - 离线数据传输**已禁用**


#### 任务 6：验证 Azure 文件同步操作

1. 在 Azure VM 的 RDP 会话中，在 Azure 门户的 **az1000202b-syncgroup1** 边栏选项卡上，监视服务器终结点 **az100021b-VM1** 的运行状况，它会从**正在配置**更改为**待处理**，并最终变为一个绿色的复选标记。

   > **注意**：几分钟后你应该能够继续下一步了。 

1. 在 Azure 门户中，导航到先前在实验室中创建的存储帐户对应的边栏选项卡，切换到**“文件”**选项卡，然后单击**“az10002bshare1”**。

1. 在**“az10002bshare1”**边栏选项卡上，单击**“连接”**。

1. 从**“连接”**边栏选项卡，将从 Windows 计算机连接到文件共享的 PowerShell 命令复制到剪贴板中。

1. 在 RDP 会话中，启动 Windows PowerShell ISE 会话。 

1. 在 Windows PowerShell ISE 会话中，打开脚本窗格并将其粘贴到本地剪贴板的内容中。

1. 将 ` -Persist` 开关添加到包含 `New-PSDrive` cmdlet 的行的末尾。

1. 执行该脚本并验证其输出是否确认 Z: 驱动器成功映射到 Azure 存储文件服务共享。

1. 在 RDP 会话中，启动文件资源管理器，导航到 Z: 驱动器，并验证它包含与 S:\\az10002bShare 相同的内容

1. 显示 Z: 驱动器上各个文件夹的“属性”窗口，查看“安全”选项卡，并注意这些条目表示分配给 S: 驱动器上相应文件夹的 NTFS 权限。


> **结果**：完成本练习后，您就部署完成了存储同步服务，安装了 Azure 文件同步代理，使用存储同步服务注册了 Windows Server，创建了同步组和云终结点，创建了服务器终结点并验证了 Azure 文件同步操作。


## 练习 3：移除实验室资源

#### 任务1：打开 Cloud Shell

1. 在门户顶部，单击**“Cloud Shell”**图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择**“Bash”**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键列出你在本实验室中创建的所有资源组：

```sh
   az group list --query "[?starts_with(name,'az100020')].name" --output tsv
```

1. 验证输出中是否仅包含在本练习中创建的资源组。将在下一个任务中删除这些资源组。

#### 任务2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键以删除你在本实验室中创建的资源组

```sh
   az group list --query "[?starts_with(name,'az100020')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **结果**：在本练习中，将删除本实验室中使用的资源。
