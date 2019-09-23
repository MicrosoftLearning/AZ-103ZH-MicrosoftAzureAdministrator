---
lab：
    title：'实施和管理存储'
    module：'实施和管理存储'
---

# 逻辑阵列块：实施和管理存储

本练习中的所有任务都是从Azure门户（包括PowerShell Cloud Shell会话）执行的，但练习2中的任务2除外，包括从远程桌面会话到Azure VM执行的步骤

   > **注意**: 不使用Cloud Shell时，逻辑阵列块虚拟机必须安装Azure PowerShell 1.2.0模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps?view=azps-1.2.0](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps?view=azps-1.2.0)

逻辑阵列块文件： 

-  **Labfiles\\AZ100\\Mod02\\az-100-02_azuredeploy.json**

-  **Labfiles\\AZ100\\Mod02\\az-100-02_azuredeploy.parameters.json**

### 方案
  
Adatum Corporation希望利用Azure存储来托管其数据

### 目标
  
完成本逻辑阵列快后，您将能够：

-  使用 Azure资源管理器 模板部署Azure VM

-  实施和使用Azure Blob存储

-  实施和使用Azure File存储


### 练习 0：准备逻辑阵列块环境
  
本练习的主要任务如下：

1. 使用 Azure资源管理器 模板部署Azure VM


#### 任务 1：使用 Azure资源管理器 模板部署Azure VM

1. 从逻辑阵列块虚拟机启动Microsoft Edge，浏览到Azure门户 [**http://portal.azure.com**](http://portal.azure.com) 并使用您打算在本逻辑阵列块中使用的Azure订阅中具有所有者角色的Microsoft帐户登录。

1. 在Azure门户中，导航到 **订阅** 边栏选项卡。

1. 从 **订阅** 边栏选项卡，导航到显示Azure订阅属性的边栏选项卡。

1. 从显示订阅属性的边栏选项卡中，导航到 **资源提供程序** 边栏选项卡。

1. 在 **资源提供程序** 边栏选项卡上，注册以下资源提供程序（如果这些资源提供程序尚未注册）：
- Microsoft.Network
- Microsoft.Compute
- Microsoft.Storage

**注意:** 此步骤注册Azure资源管理器Microsoft.Network，Microsoft.Compute和Microsoft.Storage资源提供程序。这是使用Azure 资源管理器模板部署由这些资源提供程序管理的资源时所需的一次性操作（每个订阅）（如果这些资源提供程序尚未注册）。

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在Azure应用市场内搜索 **模板部署**。

1. 使用搜索结果列表导航到 **部署自定义模板** 边栏选项卡。

1. 在 **自定义部署** 边栏选项卡上，选择 **在编辑器中构建自己的模板**。

1. 从 **编辑模板** 边栏选项卡，加载模板文件 **Labfiles\\AZ100\\Mod02\\az-100-02_azuredeploy.json**. 

   > **注意**: 查看模板的内容，并注意它定义了托管Windows Server 2016 数据中心的Azure VM的部署。

1. 保存模板并返回到 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡。

1. 从 **编辑模板** 边栏选项卡，加载模板文件 **Labfiles\\AZ100\\Mod02\\az-100-02_azuredeploy.parameters.json**. 

1. 保存参数并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，使用以下设置初始化模板部署：

    - 订阅：您用于本逻辑阵列块的订阅名称

    - 资源组：新资源组 **az1000201-RG** 的名称

    - 位置：最靠近逻辑阵列块位置的Azure区域名称，可在其中配置Azure VMs的位置

    - VM 大小： **Standard_DS1_v2**

    - Vm名称： **az1000201-vm1**

    - 管理员用户名： **学员**

    - 管理员密码： **Pa55w.rd1234**

    - 虚拟网络名称: **az1000201-vnet1**

   > **注意**: 标识可以配置Azure VM的Azure区域，请登陆：[**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注意**: 不要等待第二台虚拟机部署完成，请继续进行下一个练习您将使用虚拟机 **az1000201-vm1** 在本逻辑阵列块的第二次练习中。

> **结果**: 完成此练习后，您已启动Azure VM **az1000201-VM1** 的模板部署，您将在本逻辑阵列块的第二次练习中使用它。


### 练习 1：实施和使用Azure Blob存储

本练习的主要任务如下：

1. 创建Azure存储帐户

1. 查看Azure存储帐户的配置设置

1. 管理Azure存储Blob服务

1. 在Azure存储帐户之间复制容器和Blobs

1. 使用共享访问签名（SAS）密钥访问blob


#### 任务 1：创建Azure存储帐户

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在Azure应用市场内搜索 **存储帐户**。

1. 使用搜索结果列表导航到 **创建存储帐户** 边栏选项卡。

1. 从 **创建存储账户** 边栏选项卡，使用以下设置创建新的存储帐户： 

    - 订阅：您在上一个任务中选择的订阅

    - 资源组：新资源组 **az1000202-RG** 的名称

    - 存储帐户名称：由小写字母和数字构成的一个长度在 3 到 24 个字符之间的唯一有效字符串

    - 位置：您在上一个任务中选择的Azure区域名称

    - 性能： **标准**

    - 帐户类型： **存储（通用v1）**

    - 复制： **本地冗余存储（LRS）**

    - 需要安全传输： **已禁用**

    - 允许访问： **所有网络**

    - **Data Lake Storage Gen2** 分层名称空间：**已禁用**

1. 不要等待配置存储帐户，而是继续执行下一步。

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在Azure应用市场内搜索 **存储帐户**。

1. 使用搜索结果列表导航到 **创建存储帐户** 边栏选项卡。

1. 从 **创建存储账户** 边栏选项卡，使用以下设置创建新的存储帐户： 

    - 订阅：您在上一个任务中选择的订阅

    - 资源组：新资源组 **az1000203-RG** 的名称

    - 存储帐户名称：由小写字母和数字构成的一个长度在 3 到 24 个字符之间的唯一有效字符串

    - 位置：Azure区域名称与您在创建第一个存储帐户时选择的名称不同

    - 性能： **标准**

    - 帐户类型： **存储（通用v2）**

    - 访问层： **热**

    - 复制： **异地冗余存储 (GRS)**

    - 需要安全传输： **已禁用**

    - 允许访问： **所有网络**
    
    - **Data Lake Storage Gen2** 分层名称空间：**已禁用**

1. 等待配置存储帐户。此操作持续不到一分钟。


#### 任务 2：查看Azure存储帐户的配置设置
  
1. 在Azure门户中，导航到您创建的第一个存储帐户的边栏选项卡。 

1. 打开存储帐户边栏选项卡后，请在 **概观** 部分查看存储帐户配置 ，包括性能、复制和帐户类型设置。

1. 显示 **访问密钥** 边栏选项卡。请注意，您可以选择复制存储帐户名的值，以及key1和key2的值。您还可以选择重新生成每个密钥。

1. 显示存储帐户的 **配置** 边栏选项卡。

1. 在 **组态** 边栏选项卡，请注意您可以执行升级至 **通用v2** 帐户，强制执行安全传输以及将复制设置更改为 **异地冗余存储（GRS）** 或 **读访问地理冗余存储（RA-GRS）**。但是，您无法更改性能设置（此设置只能在创建存储帐户时分配）。

1. 显示存储帐户的 **加密** 边栏选项卡。请注意，默认情况下启用加密，您可以选择使用自己的密钥。

   > **注意**: 请勿更改存储帐户的配置。 

1. 在Azure门户中，导航到您创建的第二个存储帐户的边栏选项卡。 

1. 打开存储帐户边栏选项卡后，请在 **概观** 部分查看存储帐户配置 ，包括性能、复制和帐户类型设置。

1. 显示存储帐户的 **配置** 边栏选项卡。

1. 在 **配置** 边栏选项卡上，请注意您可以选择禁用安全传输要求，将默认访问层设置为 **冷却**，以及将复制设置更改为 **本地冗余存储（LRS）** 或 **读取访问地理冗余存储（RA）-GRS）。** 在这种情况下，您也无法更改性能设置。

1. 显示存储帐户的 **加密** 边栏选项卡。请注意，默认情况下启用加密，您可以选择使用自己的密钥。


#### 任务 3：管理Azure存储Blob服务

1. 在Azure门户中，导航到您创建的第一个存储帐户的 **Blobs** 边栏选项卡。 

1. 从第一个存储帐户的 **Blobs** 边栏选项卡中，Blobs创建一个名为 **az1000202-container** 的新容器，其 **公用访问级别** 设置为 **Private（无匿名访问）**。 

1. 从 **az1000202-container** 边栏选项卡, 将 **Labfiles\\AZ100\\Mod02\\az-100-02_azuredeploy.json** 和 **Labfiles\\AZ100\\Mod02\\az-100-02_azuredeploy.parameters.json** 上传到容器中。


#### 任务 4：在Azure存储帐户之间复制容器和Blobs

1. 从Azure门户，在Cloud Shell窗格中启动PowerShell会话。 

   > **注意**: 如果这是您第一次在当前Azure订阅中启动Cloud Shell，则会要求您创建Azure文件共享以保留Cloud Shell文件。如果是，请接受默认值，这将导致在自动生成的资源组中创建存储帐户。

1. 在“Cloud Shell”窗格中，运行以下命令：

   ```
   $storageAccount1Name = (Get-AzStorageAccount -ResourceGroupName 'az1000202-RG')[0].StorageAccountName
   $storageAccount2Name = (Get-AzStorageAccount -ResourceGroupName 'az1000203-RG')[0].StorageAccountName
   $storageAccount1Key1 = (Get-AzStorageAccountKey -ResourceGroupName 'az1000202-RG' -StorageAccountName $storageAccount1Name)[0].Value
   $storageAccount2Key1 = (Get-AzStorageAccountKey -ResourceGroupName 'az1000203-RG' -StorageAccountName $storageAccount2Name)[0].Value
   ```

   > **注意**: 这些命令设置表示每个存储帐户名称及其对应键的变量值。您将使用这些值在下一步中使用AZCopy命令行实用程序在存储帐户之间复制blobs。

1. 在“Cloud Shell”窗格中，运行以下命令：

   ```
   azcopy --source https://$storageAccount1Name.blob.core.windows.net/az1000202-container/ --destination https://$storageAccount2Name.blob.core.windows.net/az1000302-container/ --source-key $storageAccount1Key1 --dest-key $storageAccount2Key1 --include "az" --sync-copy --recursive
   ```

   > **注意**: 此命令使用AzCopy实用程序在两个存储帐户之间复制容器的内容。 

1. 验证该命令是否返回了确认已传输这两个文件的结果。 

1. 导航到 第二个存储帐户的 **Blobs** 边栏选项卡并验证它是否包含表示新创建的 **az1000202容器** 条目并且容器是否包含两个复制的blobs。


#### 任务 5：使用共享访问签名（SAS）密钥访问blob

1. 从第二个存储帐户的 **Blobs** 边栏选项卡，导航到容器 **az1000202-container**，然后打开 **az-100-02_azuredeploy.json** 边栏选项卡。

1. 在 **az-100-02_azuredeploy.json** 边栏选项卡，复制 **URL** 属性值。

1. 打开另一个Microsoft Edge窗口并导航到您在上一步中复制的URL处。 

   > **注意**: 浏览器将显示 **没有发现资源文件**。这是预期的，因为容器由 **公共访问级别** 调成 **私人（无匿名访问）**。

1. 在 **az-100-02_azuredeploy.json** 边栏选项卡上，使用以下设置生成共享访问签名（SAS）和相应的URL：

    - 权限： **读取**

    - 开始日期/时间：指定您当前时区内的当前日期/时间

    - 到期日期/时间：指定当前时间提前24小时的日期/时间

    - 允许的IP地址：留空

    - 允许的协议： **HTTP**

    - 签名密钥： **密钥 1**

1. 在 **az-100-02_azuredeploy.json** 边栏选项卡，复制 **Blob SAS URL**。

1. 从先前打开的Microsoft Edge窗口，导航到您在上一步中复制的URL。 

   > **注意**: 这次，系统将提示您是否要打开或保存 **AZ-100-02_azuredeploy.json**。这也是预期的，因为这次您不再匿名访问容器，而是使用新生成的SAS密钥，该密钥在接下来的24小时内有效。

1. 关闭显示提示的Microsoft Edge窗口。 

> **结果**: 完成此练习后，您已创建了两个Azure存储帐户，检查了其配置设置，创建了blob容器，将Blob上载到了容器中，在存储帐户之间复制了容器和blob，并使用了SAS密钥访问其中一个blobs。 


### 练习 2：实施和使用Azure File存储

本练习的主要任务具体如下：

1. 创建Azure文件服务共享

1. 将驱动器映射到Azure VM的Azure文件服务共享


#### 任务 1：创建Azure文件服务共享
  
1. 在Azure门户中，导航到显示您在上一个练习中创建的第二个存储帐户属性的边栏选项卡。

1. 从存储帐户边栏选项卡，显示其文件服务的属性。

1. 从存储账户 **文件** 边栏选项卡，使用以下设置创建新文件共享：

    - 名称： **az10002share1**

    - 配额: **5 GB**


#### 任务 2：将驱动器映射到Azure VM的Azure文件服务共享

   > **注意**: 在开始此任务之前，请确保您在练习0中启动的模板部署已完成。 

1. 导航到 **az10002share1** 边栏选项卡，显示 **连接** 边栏选项卡。

1. 从 **连接** 边栏选项卡，将从Windows计算机连接到文件共享的PowerShell命令复制到剪贴板中。

1. 在Azure门户中，导航到 **az1000201-vm1** 边栏选项卡。

1. 从 **az1000201-vm1** 边栏选项卡，通过RDP协议连接到Azure VM，并在提示登录时提供以下凭据：

    - 管理员用户名： **学员**

    - 管理员密码： **Pa55w.rd1234**

1. 在RDP会话中，启动Windows PowerShell ISE会话。 

1. 在Windows PowerShell ISE会话中，打开脚本窗格并将其粘贴到本地剪贴板的内容中。

1. 执行脚本并验证其输出确认Z的成功映射: 驱动器已成功映射到 Azure 存储文件服务共享。

1. 启动文件资源管理器，导航到Z：驱动器并创建一个名为 **文件夹1** 的文件夹。

1. 在文件资源管理器窗口中，导航到 **文件夹1** 并创建一个名为 **FILE1.TXT** 的文本文档 。 

   > **注意**: 请确保考虑到未显示已知文件扩展名的文件资源管理器的默认配置，以避免创建名为 **File1.txt.txt** 的文件。

> **结果**: 完成本练习后，您已创建了Azure文件服务共享，将驱动器从Azure VM映射到了文件共享，并使用了Azure VM中的文件资源管理器在文件共享中创建文件夹和文件。
