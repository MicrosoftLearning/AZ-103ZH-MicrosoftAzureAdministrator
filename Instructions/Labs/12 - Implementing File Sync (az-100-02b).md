---
lab:
    title: '实施Azure文件同步'
    module: '实施和管理存储'
---

# 逻辑阵列块：实现Azure文件同步

除了在与Azure VM的远程桌面会话中执行的练习1和练习2中的步骤之外，本实验中的所有任务都是从Azure门户执行的。

逻辑阵列块文件： 

-  **Allfiles/Labfiles/AZ-100.2/az-100-02b_azuredeploy.json**

-  **Allfiles/Labfiles/AZ-100.2/az-100-02b_azuredeploy.parameters.json**

### 方案
  
Adatum Corporation在本地文件服务器中托管其文件共享。考虑到其将大部分工作负载迁移到Azure的计划，Adatum正在寻找最有效的方法将其数据复制到Azure中可用的文件共享。要实现它，Adatum将使用Azure文件同步。

### 目标
  
完成本实验后，您将能够：

-  使用 Azure Resource Manager 模板部署Azure VM

-  准备Azure文件同步基础结构

-  实施和验证Azure文件同步


### 练习 0：准备逻辑阵列块环境
  
本练习的主要任务如下：

1. 使用 Azure Resource Manager 模板部署Azure VM


#### 任务 1：使用 Azure Resource Manager 模板部署Azure VM

1. 从逻辑阵列块虚拟机启动Microsoft Edge，浏览到Azure门户 [**http:////portal.azure.com**](http://portal.azure.com) 并使用在您打算在本实验中使用的Azure订阅中具有所有者角色的Microsoft帐户登录。

1. 在Azure门户中，导航到 **新** 卡.

1. 来自 **新** 卡片，搜索Azure Marketplace **模板部署**。

1. 使用搜索结果列表导航到 **自定义部署** 卡。

1. 在 **自定义部署** 卡片，选择 **在编辑器中构建自己的模板**。

1. 来自 **编辑模板** 卡片，加载模板文件 **AZ-100-02b_azuredeploy.json**。 

   > **注意**: 查看模板的内容，并注意它定义了使用单个数据磁盘托管Windows Server 2016 Datacenter的Azure VM的部署。

1. 保存模板并返回到 **自定义部署** 卡。 

1. 来自 **自定义部署** 卡片，导航到 **编辑参数** 卡。

1. 来自 **编辑参数** 卡片，加载参数文件 **AZ-100-02b_azuredeploy.parameters.json**。 

1. 保存参数并返回 **自定义部署** 卡。 

1. 来自 **自定义部署** 卡片，使用以下设置启动模板部署：

    - 订阅：你用于本逻辑阵列块的订阅名称

    - 资源组：新资源组的名称 **az1000201b-RG**

    - 位置：最靠近逻辑阵列块位置的Azure区域的名称以及可以在其中配置Azure VM的位置

    - VM 大小： **Standard_DS1_v2**

    - Vm名称： **az1000201b-VM1**

    - 管理员用户名： **学员**

    - 管理员密码： **Pa55w.rd1234**

    - “虚拟网络名称”(Virtual Network Name) 为: **az1000201b-vnet1**

   > **注意**: 要标识可以配置Azure VM的Azure区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注意**: 不要等待第二台虚拟机预配完成，请继续进行下一个练习您将在本实验的下一个练习中使用此部署中包含的虚拟机。

   > **注意**: 请记住Azure VM的用途 **az1000201b-VM1** 是在我们的方案中模拟本地文件服务器。

> **结果**: 完成本练习后，您已启动Azure VM的模板部署 **az1000201b-VM1** 您将在本实验的下一个练习中使用。


### 练习 1：准备Azure文件同步基础结构

本练习的主要任务如下：

1. 创建Azure存储帐户和文件共享

1. 准备Windows Server 2016以与Azure文件同步一起使用

1. 运行Azure文件同步评估工具


#### 任务 1：创建Azure存储帐户和文件共享

1. 在Azure门户中，导航到 **New** 卡.

1. 来自 **新** 卡片，搜索Azure Marketplace **存储帐户 - blob，文件，表，队列**。

1. 使用搜索结果列表导航到 **创建存储帐户** 卡。

1. 来自 **Create storage account** 卡, 使用以下设置创建新的存储帐户： 

    - 订阅：您在上一个任务中选择的订阅

    - 资源组：新资源组的名称 **az1000202b-RG**

    - 存储帐户名称：3到24个字符之间的任何有效，唯一名称，由小写字母和数字组成

    - 位置：您在上一个任务中选择的Azure区域的名称

    - 性能： **标准**

    - 帐户类型： **存储（通用v1）**

    - 复制： **本地冗余存储（LRS）**

    - 需要安全转移： **已禁用**

    - 允许访问： **所有网络**

    - 分层命名空间： **已禁用**

   > **注意**: 等待配置存储帐户，但继续执行下一步。

1. 在Azure门户中，导航到代表新配置的存储帐户的卡片。

1. 从存储帐户卡片中，显示其文件服务的属性。

1. 从存储帐户 **文件** 边栏选项卡中，使用以下设置创建新文件共享：

    - 名称： **az10002bshare1**

    - 配额：无


#### 任务 2：准备Windows Server 2016以与Azure文件同步一起使用

   > **注意**: 在开始此任务之前，请确保您在练习0中启动的模板部署已完成。 

1. 在Azure门户中，导航到 **az1000201b-VM1** 卡。

1. 来自 **az1000201b-VM1** 卡片，通过RDP协议连接到Azure VM，并在提示登录时提供以下凭据：

    - 管理员用户名： **学员**

    - 管理员密码： **Pa55w.rd1234**

1. 在Azure VM的RDP会话中，在服务器管理器中，导航到 **文件和存储服务**，找到连接到Azure VM的数据磁盘，将其初始化为 **GPT** 磁盘，并使用 **新卷向导** 使用以下设置创建占用整个磁盘的单个卷：

    - 驱动器号： **S**

    - 文件系统： **NTFS**

    - 分配单位大小： **默认**

    - 体积标识： **Data**

1. 在RDP会话中，以管理员身份启动Windows PowerShell会话。 

1. 在Windows PowerShell控制台中，通过运行以下命令设置文件共享：

   ```
   $ directory = New-Item -Type Directory -Path'S：\ az10002bShare'

   New-SmbShare -Name $ directory.Name -Path $ directory.FullName -FullAccess'Administrators'-ReadAdccess Everyone   

   Copy-Item -Path'C：\ WindowsAzure \ *'-Destination $ directory.FullName -Recurse
   ```

   > **注意**: 要使用示例数据填充文件共享，我们使用 C：\\WindowsAzure 文件夹的内容，该文件夹应包含大约100 MB的文件

1. 在Windows PowerShell控制台中，通过运行以下命令安装最新的AzureRM模块：

   ```
   Install-Module AzureRM
   ```

   > **注意**: 出现提示时，确认您要继续从PSGallery存储库进行安装。


#### 任务 3：运行Azure文件同步评估工具

1. 在Azure VM的RDP会话中，通过运行以下命令，从Windows PowerShell控制台安装最新版本的Package Management和PowerShellGet：

   ```
   Install-Module -Name PackageManagement -Repository PSGallery -Force

   Install-Module -Name PowerShellGet -Repository PSGallery -Force
   ```

   > **注意**: 出现提示时，确认您要继续安装NuGet提供程序。

1. 重新启动PowerShell会话。

1. 在Windows PowerShell控制台中，通过运行以下命令安装Azure File Sync PowerShell模块：

   ```
   Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
   ```

1. 在Windows PowerShell控制台中，通过运行以下命令安装Azure File Sync PowerShell模块：

   ```
   Invoke-AzStorageSyncCompatibilityCheck -Path'S：\ az10002bShare'
   ```

1. 查看结果并验证是否未找到兼容性问题。

> **结果**: 完成本练习后，您已创建Azure存储帐户和文件共享，准备Windows Server 2016以与Azure文件同步一起使用，并运行Azure文件同步评估工具


### 练习 2：准备Azure文件同步基础结构

本练习的主要任务如下：

1. 部署Storage Sync服务

1. 安装Azure文件同步代理

1. 使用Storage Sync Service注册Windows Server

1. 创建同步组和云端点

1. 创建服务器端点

1. 验证Azure文件同步操作


#### 任务 1：部署Storage Sync服务

1. 在Azure VM的RDP会话中，在服务器管理器中，导航到“本地服务器”视图并暂时关闭 **IE增强安全配置**。

1. 在Azure VM的RDP会话中，启动Internet Explorer，浏览到Azure门户 [**http://portal.azure.com**](http://portal.azure.com) 并使用您之前在本实验中使用的相同Microsoft帐户登录。

1. 在Azure门户中，导航到 **新** 卡.

1. 来自 **新** 卡片，搜索Azure Marketplace **Azure文件同步**。

1. 使用搜索结果列表导航到 **部署存储同步** 卡。

1. 来自 **部署存储同步** 卡片，使用以下设置创建存储同步服务：

    - 名称： **az1000202b-SS**

    - 订阅：您在上一个任务中选择的订阅

    - 资源组：新资源组的名称 **az1000203b-RG**

    - 位置：本练习前面创建存储帐户的Azure区域的名称


#### 任务 2：安装Azure文件同步代理。

1. 在RDP会话中，启动另一个Internet Explorer实例，浏览到Microsoft下载中心 [**https://go.microsoft.com/fwlink/?linkid=858257**](https://go.microsoft.com/fwlink/?linkid=858257) 并下载Azure文件同步代理Windows Installer文件 **StorageSyncAgent_V5_WS2016.msi**。

1. 下载完成后，使用默认设置运行Storage Sync Agent安装向导以安装Azure文件同步代理。

1. Azure文件同步代理安装完成后， **Azure文件同步 - 服务器注册** 向导会自动启动。


#### 任务 3：使用Storage Sync Service注册Windows Server

1. 从最初的页面 **Azure文件同步 - 服务器注册** 向导，使用您之前在本实验中使用的相同Microsoft帐户登录。

1. 在 **选择存储同步服务** 的页面 **Azure文件同步 - 服务器注册** 向导，指定以下设置进行注册：

    - 订阅：你用于本逻辑阵列块的订阅名称。

    - 资源组： **az1000203b-RG**

    - 存储同步服务： **az1000202b-SS**

1. 出现提示时，使用您之前在本实验中使用的相同Microsoft帐户再次登录。


#### 任务 4：创建同步组和云端点

1. 在Azure VM的RDP会话中，在Azure门户中，导航到 **az1000202b-SS** 存储同步服务卡片。

1. 来自 **az1000202b-SS** 存储同步服务卡片，导航到 **同步组** 卡片并使用以下设置创建新的同步组：

    - 同步组名称： **az1000202b-syncgroup1**

    - 订阅：你用于本逻辑阵列块的订阅名称。

    - 存储帐户：您在上一个练习中创建的存储帐户的资源ID

    - Azure文件共享： **az10002bshare1**


#### 任务 5：创建服务器端点

1. 在Azure VM的RDP会话中，在Azure门户中，来自 **az1000202b-SS** 存储同步服务卡片，导航到 **az1000202b-syncgroup1** 卡。

1. 来自 **az1000202b-syncgroup1** 卡片，导航到 **添加服务器端点** 卡片并使用以下设置创建新的服务器端点：

    - 注册服务器： **az1000201b-VM1**

    - 路径： **S：\\ az10002bShare**

    - 云分层： **已启用**

        - 始终保留卷上指定的可用空间百分比： **15**

        - 仅缓存在指定天数内访问或修改的文件： **30**

    - 串行数据传输 **已禁用**


#### 任务 6：验证Azure文件同步操作

1. 在Azure VM的RDP会话中，在Azure门户中，监视服务器端点的运行状况 **az100021b-VM1** 在...上 **az1000202b-syncgroup1** 卡片，因为它改变了 **供应** 至 **有待** 并最终成为一个绿色的复选标记。

   > **注意**: 几分钟后你应该能够继续下一步了。 

1. 在Azure门户中，导航到 **az10002bshare1** 卡片和显示器 **连** 卡。

1. 来自 **连** 卡片，将从Windows计算机连接到文件共享的PowerShell命令复制到剪贴板中。

1. 在RDP会话中，启动Windows PowerShell ISE会话。 

1. 在Windows PowerShell ISE会话中，打开脚本窗格并将其粘贴到本地剪贴板的内容中。

1. 执行该脚本并验证其输出是否确认Z：驱动器成功映射到Azure存储文件服务共享。

1. 在RDP会话中，启动文件资源管理器，导航到Z：驱动器，并验证它包含与 S：\\az10002bShare 相同的内容

1. 显示Z：驱动器上各个文件夹的“属性”窗口，查看“安全”选项卡，并注意这些条目表示分配给S：驱动器上相应文件夹的NTFS权限。


> **结果**: 完成本练习后，您已部署Storage Sync Service，安装Azure文件同步代理，使用Storage Sync Service注册Windows Server，创建同步组和云端点，创建服务器端点以及验证Azure文件同步操作。
