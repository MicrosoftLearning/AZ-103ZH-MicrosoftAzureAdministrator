---
lab:
    title: “Azure 区域之间的 Azure Site Recovery”
    module: “模块 07 - 数据保护”
---

# 实验室：实现 Azure 区域之间的 Azure Site Recovery

本逻辑阵列块中的所有任务都是从Azure门户执行的

实验室文件： 

-  **Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.json**

-  **Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.parameters.json**

### 场景
  
Adatum Corporation希望实施Azure站点恢复，以便于在区域之间迁移和保护Azure VMs


### 目标
  
完成本实验室后，你将能够：

-  实现 Azure Site Recovery 保管库

-  使用 Azure Site Recovery 配置 Azure 区域之间的 Azure VM 复制


### 练习 1：使用 Azure Site Recovery 实现 Azure VM 迁移的前提条件 
  
本练习的主要任务如下：

1. 使用Azure资源管理器模板部署要迁移的Azure VM

1. 创建Azure恢复服务保管库
  

#### 任务 1：使用Azure资源管理器模板部署要迁移的Azure VM

1. 从实验室虚拟机启动 Microsoft Edge，浏览到 Azure 门户 ([**http://portal.azure.com**](http://portal.azure.com))，然后使用 Microsoft 帐户登录，该帐户具有要在本实验室中使用的 Azure 订阅的所有者角色。

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。

1. 在 **“创建资源”** 边栏选项卡中，在 Azure 市场中搜索 **“模板部署”**。

1. 使用搜索结果列表导航到 **部署自定义模板** 边栏选项卡。

1. 在 **“自定义部署”** 边栏选项卡中，单击 **“在编辑器中生成自己的模板”** 链接。如果未看到此链接，请改为单击 **“编辑模板”**。

1. 在 **“编辑模板”** 边栏选项卡中，加载模板文件 **Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.json**。 

   > **注意**：查看模板的内容，并注意它定义了托管Windows Server 2016 数据中心的Azure VM的部署。

1. 保存模板并返回到 **自定义部署** 边栏选项卡。 

1. 从 **“自定义部署”** 边栏选项卡中，导航到 **“编辑参数”** 边栏选项卡。

1. 在 **“编辑参数”** 边栏选项卡中，加载参数文件 **Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.parameters.json**。 

1. 保存参数并返回到 **“自定义部署”** 边栏选项卡。 

1. 在 **“自定义部署”** 边栏选项卡中，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1010101-RG** 的名称

    - 位置：最靠近实验室位置的 Azure 区域的名称以及可以在其中预配 Azure VM 的位置

    - VM 名称： **az1010101-vm**

    - 管理员用户名：**Student**

    - 管理员密码：**Pa55w.rd1234**

    - 映像发布者：**MicrosoftWindowsServer**

    - 图像提供者：**WindowsServer**

    - 图像 SKU： **2016-Datacenter-Server-Core-smalldisk**

    - VM 大小：**Standard_DS1_v2**

   > **注意**：若要标识可以预配 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注意**: 不要等待部署完成，而是继续执行下一个任务。您将在本逻辑阵列块的第二次练习中使用虚拟机 **az1010101-vm** 。


#### 任务 2：实现 Azure Site Recovery 保管库
 
1. 在 Azure 门户中，导航到 **“创建资源”** 边栏选项卡。

1. 在 **“创建资源”** 边栏选项卡中中，在 Azure 市场中搜索 **“备份和 Site Recovery”**。

1. 使用搜索结果列表导航到 **“恢复服务保管库”** 边栏选项卡。

1. 使用 **恢复服务保险库** 边栏选项卡，使用以下设置创建站点保险库：

    - 名称：**vaultaz1010102**

    - 订阅：在本练习的上一个任务中使用的同一 Azure 订阅

    - 资源组：新资源组 **az1010102-RG** 的名称

    - 位置：订阅中可用的Azure区域名称，该名称与您在本练习的上一个任务中部署的Azure VM区域不同

> **结果**: 完成本练习后，您已使用Azure资源管理器模板启动了Azure VM的部署，并创建了一个Azure站点恢复保险库，该保险库将用于复制Azure VM磁盘文件的内容。 


### 练习 2：使用 Azure Site Recovery 在 Azure 区域之间迁移 Azure VM 

本练习的主要任务如下：

1. 配置 Azure VM 复制

1. 查看 Azure VM 复制设置 


#### 任务 1：配置 Azure VM 复制

   > **注意**：在开始此任务之前，请确保已完成在第一个练习中启动的模板部署。 

1. 在Azure门户中，导航到新配置的Azure 恢复服务保险库的边栏选项卡 **vaultaz1010102**。

1. 从 **vaultaz1010102** 边栏选项卡，配置以下复制设置：

    - 源：**Azure**

    - 源位置：在本实验室的上一个练习中部署 Azure VM 的同一 Azure 区域

    - Azure 虚拟机部署模型：**资源管理器**

    - 您在本逻辑阵列块的上一个任务中使用过的相同Azure订阅

    - 源资源组：**az1010101-RG**

    - 虚拟机： **az1010101-vm**

    - 目标位置：订阅中可用的Azure区域的名称，该名称与您在上一个任务中部署Azure VM的区域不同。如果可能，请使用部署Azure站点恢复保险库的同一Azure区域。

    - 目标资源组：**(新) az1010101-RG-asr**

    - 目标虚拟网络：**(新) az1010101-vnet-asr**

    - 缓存存储帐户：接受默认设置

    - 副本托管磁盘：**（新）1 个高级磁盘，0 个标准磁盘**

    - 目标可用性集：**不适用**

    - 复制策略：新复制策略的名称 **12小时保留策略**

    - 恢复点保留：**12 小时**

    - 应用一致的快照频率：**6 小时**

    - 多 VM 一致性：**否**

1. 从 **配置设置** 边栏选项卡，启动目标资源的创建，并等到您被重定向到 **启用复制** 边栏选项卡。

1. 从 **启用复制** 边栏选项卡，启用复制。


#### 任务 2：查看 Azure VM 复制设置

1. 在Azure门户中，导航到 **vaultaz1010102 - 复制项** 边栏选项卡。

1. 在 **“vaultaz1010102 - 复制项”** 边栏选项卡上，确认是否存在代表 **az1010101-vm** Azure VM 的条目，并验证其 **“复制运行状况”** 是否 **“正常”**，其 **“状态”** 是否为 **“正在启用保护”**。

1. 从 **vaultaz1010102 - 复制项”** 边栏选项卡中，显示 **az1010101-vm** Azure VM 的复制项边栏选项卡。

1. 在 **az1010101-VM** 复制项边栏选项卡，审查 **健康和状态**， **故障转移就绪**， **最新恢复点**，和 **基础架构视图** 部分。请注意 **“故障转移”** 和 **“测试故障转移”** 工具栏图标。

   > **注意**: 此任务的其余步骤是可选的，不进行评分。 

1. 如果时间允许，请等待复制状态更改为 **“100％同步”**。该操作可能需要额外的90分钟。 

1. 检查 **RPO** 值， 以及 **崩溃一致** 和 **应用程序一致** 恢复点。 

1. 执行测试故障转移到 **az1010101-vnet-asr** 虚拟网络。

> **结果**: 完成本练习后，您已配置了Azure VM的复制并查看了Azure VM复制设置。

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **“Bash”**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter**，列出在本实验室中创建的所有资源组：

   ```sh
   az group list --query "[?starts_with(name,'az10101')].name" --output tsv
   ```

1. 验证输出中是否仅包含在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter**，删除在本实验室中创建的资源组

   ```sh
   az group list --query "[?starts_with(name,'az10101')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```
   > **注意**：如果你遇到类似于“...由于以下范围被锁定，无法执行删除操作...”的错误，那么需要运行以下步骤来删除阻止删除的资源锁定：
   > ```sh
   > lockedresource=$(az resource list --resource-group az1010101-RaG-asr --resource-type Microsoft.Compute/disks --query "[?starts_with(name,'az10101')].name" --output tsv)
   > az disk revoke-access -n $lockedresource --resource-group az1010101-RG-asr
   > lockid=$(az lock show --name ASR-Lock --resource-group az1010101-RG-asr --resource-type Microsoft.Compute/disks --resource-name $lockedresource --output tsv --query id)
   > az lock delete --ids $lockid
   > az group list --query "[?starts_with(name,'az10101')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   >```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **结果**：在本练习中，你删除了本实验室中使用的资源。
