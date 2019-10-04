---
lab:
    title: “实施和管理存储”
    module: “模块 03 - Azure 存储”
---

# 实验室：实施和管理存储

本练习中的所有任务都是从 Azure 门户（包括 PowerShell Cloud Shell 会话）执行的，但练习 2 中的任务 2 除外，包括从远程桌面会话到 Azure VM 执行的步骤

   > **注意**：不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件： 

-  **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json**

-  **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json**

### 场景
  
Adatum Corporation 希望利用 Azure 存储来托管其数据

### 目标
  
完成本实验室后，你将能够：

-  使用 Azure 资源管理器模板部署 Azure VM

-  实施和使用 Azure Blob 存储

-  实施和使用 Azure 文件存储


### 练习 0：准备实验室环境
  
本练习的主要任务如下：

1. 使用 Azure 资源管理器模板部署 Azure VM


#### 任务 1：使用 Azure 资源管理器模板部署 Azure VM

1. 从实验室虚拟机启动 Microsoft Edge，在 [**http://portal.azure.com**](http://portal.azure.com) 上浏览至 Azure 门户，并使用在你计划用于本实验室的 Azure 订阅中具有“所有者”角色的 Microsoft 帐户进行登录。

1. 在 Azure 门户中，导航到 **“订阅”** 边栏选项卡。

1. 从 **“订阅”** 边栏选项卡，导航到显示 Azure 订阅属性的边栏选项卡。

1. 从显示订阅属性的边栏选项卡中，导航到 **“资源提供程序”** 边栏选项卡。

1. 在 **“资源提供程序”** 边栏选项卡上，注册以下资源提供程序（如果这些资源提供程序尚未注册）：
- Microsoft.Network
- Microsoft.Compute
- Microsoft.Storage

**注意：** 此步骤注册 Azure 资源管理器 Microsoft.Network、Microsoft.Compute 和 Microsoft.Storage 资源提供程序。这是使用Azure 资源管理器模板部署由这些资源提供程序管理的资源时所需的一次性操作（每个订阅）（如果这些资源提供程序尚未注册）。

1. 在 Azure 门户中，导航到 **“创建资源”** 边栏选项卡。

1. 从 **“创建资源”** 边栏选项卡，在 Azure 市场中搜索 **“模板部署”**，并选择 **“模板部署(使用自定义模板部署)”**

1. 单击 **“创建”**。

1. 在 **“自定义部署”** 边栏选项卡中，单击 **“在编辑器中构建自己的模板”** 链接。如果不显示此链接，请改为单击 **“编辑模板”**。

1. 从 **“编辑模板”** 边栏选项卡，加载模板文件 **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json**. 

   > **注意**：请查看模板的内容，并注意其定义了托管 Windows Server 2016 Datacenter 的 Azure VM 的部署。

1. 请保存模板并返回 **“自定义部署”** 边栏选项卡。 

1. 从 **“自定义部署”** 边栏选项卡，导航到 **“编辑参数”** 边栏选项卡。

1. 从“编辑参数”**Edit parameters** 边栏选项卡加载参数文件 **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json**。 

1. 保存参数并返回到 **“自定义部署”** 边栏选项卡。 

1. 从 **“自定义部署”** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1000201-RG** 的名称

    - 位置：最靠近实验室位置的 Azure 区域的名称以及可以在其中预配 Azure VM 的位置

    - VM 大小：**Standard_DS2_v2**

    - VM 名称：**az1000201-vm1**

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

    - 虚拟网络名称：**az1000201-vnet1**

   > **注意**：若要标识可以预配 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注意**：不必等待部署完成，请继续进行下一项练习。你将在本实验室的第二项练习中使用虚拟机 **az1000201-vm1**。

> **结果**：完成此练习后，你已启动Azure VM **az1000201-vm1** 的模板部署，你将在本逻辑阵列块的第二次练习中使用它。


### 练习 1：实施和使用 Azure Blob 存储

本练习的主要任务如下：

1. 创建 Azure 存储帐户

1. 查看 Azure 存储帐户的配置设置

1. 管理 Azure 存储 Blob 服务

1. 在 Azure 存储帐户之间复制容器和 blob

1. 使用共享访问签名 (SAS) 密钥访问 blob


#### 任务 1：创建 Azure 存储帐户

1. 在 Azure 门户中，导航到 **“创建资源”** 边栏选项卡。

1. 从 **“创建资源”** 边栏选项卡，在 Azure 市场内搜索 **“存储帐户”**。

1. 使用搜索结果列表导航到 **“创建存储帐户”** 边栏选项卡。

1. 从 **“创建存储账户”** 边栏选项卡，使用以下设置创建新的存储帐户： 

    - 订阅：在上一个任务中选择的同一订阅

    - 资源组：新资源组 **az1000202-RG** 的名称

    - 存储帐户名称：由小写字母和数字构成的一个长度在 3 到 24 个字符之间的唯一有效字符串

    - 位置：你在上一个任务中选择的 Azure 区域的名称

    - 性能：**标准**

    - 帐户类型：**存储（通用 v1）**

    - 复制：**本地-冗余存储 (LRS)**

1. 单击 **“查看并创建”**，然后单击 **“创建”**。

1. 不要等待配置存储帐户，而是继续执行下一步。

1. 在 Azure 门户中，导航到 **“创建资源”** 边栏选项卡。

1. 从 “创建资源”** 边栏选项卡，在 Azure 市场内搜索 **“存储帐户”**。

1. 使用搜索结果列表导航到 **“创建存储帐户”** 边栏选项卡。

1. 从 **“创建存储账户”** 边栏选项卡，使用以下设置创建新的存储帐户： 

    - 订阅：你在上一个任务中选择的订阅

    - 资源组：新资源组 **az1000203-RG** 的名称

    - 存储帐户名称：由小写字母和数字构成的一个长度在 3 到 24 个字符之间的唯一有效字符串

    - 位置：Azure 区域名称与你在创建第一个存储帐户时选择的名称不同

    - 性能：**标准**

    - 帐户类型：**StorageV2（通用 v2）**

    - 访问层：**热存储**

    - 复制：**异地冗余存储 (GRS)**

1. 点击 **“查看并创建”**，然后点击 **“创建”**。

1. 等待存储帐户预配完成。此操作持续不到一分钟。


#### 任务 2：查看 Azure 存储帐户的配置设置
  
1. 在 Azure 门户中，导航到你创建的第一个存储帐户的边栏选项卡。 

1. 打开存储帐户边栏选项卡后，请在 **“概述”** 部分查看存储帐户配置，包括性能、复制和帐户类型设置。

1. 显示 **“访问密钥”** 边栏选项卡。请注意，可以选择复制存储帐户名的值，以及 key1 和 key2 的值。你还可以选择重新生成每个密钥。

1. 显示存储帐户的 **“配置”** 边栏选项卡。

1. 在 **“配置”** 边栏选项卡，请注意你有以下选择：执行到 **“通用 v2”** 帐户的升级，强制执行安全传输，将复制设置更改为 **“异地冗余存储(GRS)”** 或 **“读取访问异地冗余存储 (RA-GRS)”**。但是，无法更改性能设置（此设置只能在创建存储帐户时分配）。

1. 显示存储帐户的 **“加密”** 边栏选项卡。请注意，默认启用加密，你可以选择使用自己的密钥。

   > **注意**：请勿更改存储帐户的配置。 

1. 在 Azure 门户中，导航到你创建的第二个存储帐户的边栏选项卡。 

1. 打开存储帐户边栏选项卡后，请在 **“概述”** 部分查看存储帐户配置，包括性能、复制和帐户类型设置。

1. 显示存储帐户的 **“配置”** 边栏选项卡。

1. 在 **“配置”** 边栏选项卡上，请注意你有以下选项：禁用安全传输要求，将默认访问层设置为 **“冷存储”**，将复制设置更改为 **“本地冗余存储(LRS)”** 或 **“读取访问异地冗余存储(RA-GRS)”**。这种情况下也无法更改性能设置。

1. 显示存储帐户的 **“加密”** 边栏选项卡。请注意，默认启用加密，你可以选择使用自己的密钥。


#### 任务 3：管理 Azure 存储 Blob 服务

1. 在Azure门户中，导航到你创建的第一个存储帐户的 **“Blob”** 边栏选项卡。 

1. 从第一个存储帐户的 **“Blob”** 边栏选项卡中，创建一个名为 **az1000202-container** 的新容器，其 **“公共访问级别”** 设置为 **“专用(无匿名访问)”**。 

1. 从 **az1000202-container** 边栏选项卡,将 **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json** 和 **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json** 上传到容器中.


#### 任务 4：在 Azure 存储帐户之间复制容器和 blob

1. 从 Azure 门户，在 Cloud Shell 窗格中启动 PowerShell 会话。 

   > **注意**：如果这是你第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求你创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```pwsh
   $containerName = 'az1000202-container'
   $storageAccount1Name = (Get-AzStorageAccount -ResourceGroupName 'az1000202-RG')[0].StorageAccountName
   $storageAccount2Name = (Get-AzStorageAccount -ResourceGroupName 'az1000203-RG')[0].StorageAccountName
   $storageAccount1Key1 = (Get-AzStorageAccountKey -ResourceGroupName 'az1000202-RG' -StorageAccountName $storageAccount1Name)[0].Value
   $storageAccount2Key1 = (Get-AzStorageAccountKey -ResourceGroupName 'az1000203-RG' -StorageAccountName $storageAccount2Name)[0].Value
   $context1 = New-AzStorageContext -StorageAccountName $storageAccount1Name -StorageAccountKey $storageAccount1Key1
   $context2 = New-AzStorageContext -StorageAccountName $storageAccount2Name -StorageAccountKey $storageAccount2Key1
   ```
   > **注意**：这些命令设置变量的值，这些变量表示 blob 容器的名称，包含你在上一任务中上传的 blob、两个存储帐户、它们对应的密钥及每个帐户对应的安全上下文。你将使用这些值来生成 SAS 令牌，以使用 AZCopy 命令行实用工具在存储帐户之间复制 blob。

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```pwsh
   New-AzStorageContainer -Name $containerName -Context $context2 -Permission Off
   ```
   > **注意**：此命令在第二个存储帐户中创建具有匹配名称的新容器
   
1. 在 Cloud Shell 窗格中，运行以下命令：

   ```pwsh
   $containerToken1 = New-AzStorageContainerSASToken -Context $context1 -ExpiryTime(get-date).AddHours(24) -FullUri -Name $containerName -Permission rwdl
   $containerToken2 = New-AzStorageContainerSASToken -Context $context2 -ExpiryTime(get-date).AddHours(24) -FullUri -Name $containerName -Permission rwdl
   ```
   > **注意**：这些命令生成 SAS 密钥，你将在下一步中使用这些密钥在两个容器之间复制 blob。
   
1. 在 Cloud Shell 窗格中，运行以下命令：

   ```pwsh
   azcopy cp $containerToken1 $containerToken2 --recursive=true
   ```

   > **注意**：此命令使用 AzCopy 实用工具在两个存储帐户之间复制容器的内容。 

1. 验证该命令是否已返回确认已传输这两个文件的结果。 

1. 导航到第二个存储帐户的 **“Blob”** 边栏选项卡，并验证它是否包含表示新建的 **az1000202-container** 的条目，并验证容器是否包含两个已复制的 blob。


#### 任务 5：使用共享访问签名 (SAS) 密钥访问 blob

1. 从第二个存储帐户的 **“Blob”** 边栏选项卡，导航到容器 **az1000202-container**，然后打开 **az-100-02_azuredeploy.json** 边栏选项卡。

1. 在 **az-100-02_azuredeploy.json** 边栏选项卡，复制 **“URL”** 属性的值。

1. 打开另一个 Microsoft Edge 窗口并导航到你在上一步中复制的 URL。 

   > **注意**：浏览器将显示 **“ResourceNotFound”**。这是预期的，因为容器使 **“公共访问级别”** 设置为 **“专用(无匿名访问)”**。

1. 在 **az-100-02_azuredeploy.json** 边栏选项卡上，使用以下设置生成共享访问签名 (SAS) 和相应的 URL：

    - 权限：**读取**

    - 开始日期/时间：指定你当前时区内的当前日期/时间

    - 到期日期/时间：指定当前时间前 24 小时的日期/时间

    - 允许的 IP 地址：留空

    - 允许的协议：**HTTP**

    - 签名密钥：**密钥 1**

1. 在 **az-100-02_azuredeploy.json** 边栏选项卡，复制 **“Blob SAS URL”**。

1. 从先前打开的 Microsoft Edge 窗口，导航到你在上一步中复制的 URL。 

   > **注意**：这次，系统将提示你是否要打开或保存 **az-100-02_azuredeploy.json**。这也是正常的，因为这次你不再匿名访问容器，而是使用新生成的 SAS 密钥，该密钥在接下来的 24 小时内有效。

1. 关闭显示提示的 Microsoft Edge 窗口。 

> **结果**：完成此练习后，你已创建了两个 Azure 存储帐户，检查了其配置设置，创建了 blob 容器，将 blob 上载到了容器中，在存储帐户之间复制了容器和 blob，并使用了 SAS 密钥访问其中一个 blob。 


### 练习 2：实施和使用 Azure 文件存储

本练习的主要任务具体如下：

1. 创建 Azure 文件服务共享

1. 将驱动器映射到 Azure VM 的 Azure 文件服务共享


#### 任务 1：创建 Azure 文件服务共享
  
1. 在 Azure 门户中，导航到显示你在上一个练习中创建的第二个存储帐户的属性的边栏选项卡。

1. 从存储帐户边栏选项卡，显示其文件服务的属性。

1. 从存储账户 **“文件”** 边栏选项卡，使用以下设置创建新文件共享：

    - 名称：**az10002share1**

    - 配额: **5 GB**


#### 任务 2：将驱动器映射到 Azure VM 的 Azure 文件服务共享

   > **注意**：在开始此任务之前，请确保已完成练习 0 中启动的模板部署。 

1. 导航到 **az10002share1** 边栏选项卡，显示 **“连接”** 边栏选项卡。

1. 从 **“连接”** 边栏选项卡，将从 Windows 计算机连接到文件共享的 PowerShell 命令复制到剪贴板中。

1. 在 Azure 门户中，请导航到 **az1000201-vm1** 边栏选项卡。

1. 从 **az1000201-vm1** 边栏选项卡，通过 RDP 协议连接到 Azure VM，并在提示登录时提供以下凭据：

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

1. 在 RDP 会话中，启动 Windows PowerShell ISE 会话。 

1. 在 Windows PowerShell ISE 会话中，打开脚本窗格并将该命令粘贴到本地剪贴板的内容中。

1. 将脚本粘贴到 PowerShell ISE 会话中，在脚本末尾添加 `` -Persist ``，执行脚本，并验证其输出是否确认 Z: 驱动器到 Azure 存储文件服务共享的映射成功。

1. 启动文件资源管理器，导航到 Z: 驱动器并创建一个名为 **Folder1** 的文件夹。

1. 在文件资源管理器窗口中，导航到 **Folder1** 并创建一个名为 **File1.txt** 的文本文档。 

   > **注意**：请确保考虑到未显示已知文件扩展名的文件资源管理器的默认配置，以避免创建名为 **File1.txt.txt** 的文件。

1. 在 PowerShell 提示符下，输入 **Z:**，将目录上下文更改为已映射的驱动器。 

1. 在 PowerShell 提示符下，输入 **dir**，列出驱动器的内容。应显示从文件资源管理器中创建的目录。

1. 在 PowerShell 提示符下，输入 **cd Folder1**，将目录更改为该文件夹。再次运行 **dir** 命令，列出文件内容。

> **结果**：完成本次练习后，你已创建 Azure 文件服务共享，将驱动器从 Azure VM 映射到文件共享，并使用 Azure VM 中的文件资源管理器在文件共享中创建文件夹和文件。

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **“Cloud Shell”** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **“Bash”**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键列出你在本实验室中创建的所有资源组：

   ```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv
   ```

1. 验证输出中是否仅包含你在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键以删除你在本实验室中创建的资源组

   ```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **结果**：在本练习中，你删除了本实验室中使用的资源。
