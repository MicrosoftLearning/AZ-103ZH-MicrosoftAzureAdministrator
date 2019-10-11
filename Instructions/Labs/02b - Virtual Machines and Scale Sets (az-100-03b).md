---
lab:
    title: '虚拟机规模集'
    module: '模块 02 - Azure 虚拟机'
---

# 实验室：虚拟机规模集

本实验室中的所有任务都是从 Azure 门户执行的（包括 PowerShell Cloud Shell 会话） 

   > **注意**：不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件： 

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03b_01_azuredeploy.json**

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03b_01_azuredeploy.parameters.json**

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03b_02_azuredeploy.json**

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03b_02_azuredeploy.parameters.json**

### 场景
  
Adatum Corporation 希望扩展在 Azure VM 和 Azure VM 规模集上运行的工作负荷的计算和存储资源


### 目标
  
完成本实验室后，你将能够：

-  使用 Azure Resource Manager 模板部署 Azure VM 和 Azure VM 规模集

-  配置 Azure VM 的计算和存储资源 

-  配置 Azure VM 规模集的计算和存储资源


### 练习 0：准备实验室环境
  
本练习的主要任务如下：

1. 使用 Azure 资源管理器模板部署 Azure VM

1. 使用 Azure Resource Manager 模板部署 Azure VM 规模集


#### 任务 1：使用 Azure 资源管理器模板部署 Azure VM

1. 从实验室虚拟机启动 Microsoft Edge，在 [**http://portal.azure.com**](http://portal.azure.com) 上浏览至 Azure 门户，并使用在你计划用于本实验室的 Azure 订阅中具有“所有者”角色的 Microsoft 帐户进行登录。

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在 Azure 市场中搜索 **模板部署**，然后选择 **模板部署(使用自定义模板部署)**。

1. 单击 **创建**。

1. 在 **自定义部署** 边栏选项卡中，单击 **在编辑器中构建自己的模板** 链接。如果不显示此链接，请改为单击 **编辑模板**。

1. 在 **编辑模板** 边栏选项卡上，请加载模板文件 **az-100-03b_01_azuredeploy.json**。 

   > **注意**：请查看模板的内容，并注意其定义了托管 Windows Server 2016 Datacenter 的 Azure VM 的部署。

1. 请保存模板并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡。

1. 从 **编辑参数** 边栏选项卡，加载参数文件 **az-100-03b_01_azuredeploy.parameters.json**。 

1. 保存参数并返回到 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1000301b-RG** 的名称

    - 位置：**美国东部**（或离你更近的支持区域）

    - VM 名称：**az1000301b-vm1**

    - VM 大小：**Standard_DS2_v2**

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

   > **注意**：要标识可以配置 Azure VM 的 Azure 区域，请参阅 [https://azure.microsoft.com/zh-cn/regions/offers/](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注意**：你不必等到还原完成，可继续执行该项练习中的下一项任务。你将在本实验室的下一项练习中使用此部署中包含的虚拟机。

#### 任务 2：使用 Azure Resource Manager 模板部署 Azure VM 规模集

1. 在实验室虚拟机上的 Azure 门户中，请导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在 Azure 市场中搜索 **模板部署**，然后选择 **模板部署(使用自定义模板部署)**。

1. 单击 **创建**。

1. 在 **自定义部署** 边栏选项卡中，单击 **在编辑器中构建自己的模板** 链接。如果不显示此链接，请改为单击 **编辑模板**。

1. 在 **编辑模板** 边栏选项卡上，请加载模板文件 **az-100-03b_02_azuredeploy.json**。 

   > **注意**：查看模板的内容，并注意其定义了托管 Windows Server 2016 Datacenter 的 Azure VM 规模集的部署。

1. 请保存模板并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡。

1. 从 **编辑参数** 边栏选项卡，加载参数文件 **az-100-03b_02_azuredeploy.parameters.json**。 

1. 保存参数并返回到 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1000302b-RG** 的名称

    - 位置：最靠近实验室位置的 Azure 区域的名称以及可以在其中预配 Azure VM 的位置

    - Vmss 名称：**az1000302bvmss1**

    - VM 大小：**Standard_DS2_v2**

    - 管理员用户名：**Student**

    - 管理员密码：**Pa55w.rd1234**

    - 实例数：**1**

   > **注意**：不必等待部署完成，请继续进行下一项练习。你将在本实验室的最后一个练习中使用此部署中包含的虚拟机规模集。

> **结果**：完成本次练习后，你已启动 **Azure VM az1000301b-VM1** 和 Azure VM 规模集 **az1000302bvmss1** 的模板部署，你将在本实验室的下一项练习中使用这些部署。


### 练习 1：配置 Azure VM 的计算和存储资源 
  
本次练习的主要任务如下：

1. 使用 Azure 门户垂直扩展计算 Azure VM 的资源

1. 使用 ARM 模板垂直扩展计算 Azure VM 的资源

1. 将数据磁盘附加到 Azure VM。

1. 在 Azure VM 中配置数据卷


#### 任务 1：使用 Azure 门户垂直扩展计算 Azure VM 的资源

1. 在实验室虚拟机的 Azure 门户中，导航到 **az1000301b-RG** 资源组边栏选项卡。

1. 从 **az1000301b-RG** 资源组边栏选项卡，导航到 **az1000301b-VM1** 虚拟机边栏选项卡。

1. 从 **az1000301b-vm1** 虚拟机边栏选项卡，导航到 **大小** 虚拟机边栏选项卡。

1. 从 **az1000301b-vm1 - 大小** 虚拟机边栏选项卡，增加虚拟机的大小为 **Ds3_v2** **标准**，然后单击 **调整大小**。

   > **注意**：如果此大小不可用，请选择其他大小。要确定可供选择的 Azure VM 大小，请参阅 [https://azure.microsoft.com/zh-cn/global-infrastructure/services/](https://azure.microsoft.com/zh-cn/global-infrastructure/services/)

   > **注意**：请记住，调整 Azure VM 的大小需要重新启动其操作系统。 


#### 任务 2：使用 ARM 模板垂直扩展计算 Azure VM 的资源

1. 在实验室虚拟机的 Azure 门户中，导航到 **az1000301b-RG** 资源组边栏选项卡。

1. 从 **az1000301b-RG** 资源组边栏选项卡，导航到 **az1000301b-RG - 部署** 边栏选项卡。

1. 从 **az1000301b-RG - 部署** 边栏选项卡，导航到 **Microsoft.Template - 概述** 边栏选项卡显示最近成功部署。

1. 在 **az1000301b-RG  - 部署** 边栏选项卡，使用 **重新部署** 选项导航到 **自定义部署** 边栏选项卡。

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡。

1. 在 **编辑参数** 边栏选项卡，查看用于最近成功部署的原始参数的值。 

   > **注意**： **adminPassword** 参数的值为 null，因为该参数具有 **securestring** 的数据类型。 

1. 保存参数并返回到 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：**az1000301b-RG**

    - 位置：你之前选择的相同 Azure 区域的名称

    - VM 名称：**az1000301b-vm1**

    - VM 大小：**Standard_DS2_v2**

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

   > **注意**：在继续执行本次练习的下一个任务之前，请等待部署完成。部署将 Azure VM 的大小更改回其原始值，从而有效地将其缩小。


#### 任务 3：将数据磁盘附加到 Azure VM。

1. 在 Azure 门户中，导航到 **az1000301b-vm1** 虚拟机边栏选项卡。

1. 从 **az1000301b-vm1** 虚拟机边栏选项卡，导航到 **磁盘** 边栏选项卡。

1. 从 **az1000301b-vm1 - 磁盘** 边栏选项卡，使用 **“+ 添加数据磁盘”**，然后在 **名称** 下拉列表，点击 **创建磁盘**。

1. 在 **创建托管磁盘** 边栏选项卡中，使用以下设置创建新数据磁盘：

    - 名称：**az1000301b-vm1-DataDisk0**

    - 资源组：**az1000301b-RG**

    - 帐户类型：**标准 HDD**

    - 来源类型：**无（空磁盘）**

    - 大小 (GiB)： **1023**

1. 回到 **az1000301b-vm1 - 磁盘** 边栏选项卡，为新创建的磁盘配置以下设置：

    - LUN：**0**

    - 主机缓存 **无**

1. 从 **az1000301b-vm1 - 磁盘** 边栏选项卡，使用 **“+ 添加数据磁盘** 选项，然后在 **名称** 下拉列表，点击 **创建磁盘”**。

1. 在 **创建托管磁盘** 边栏选项卡中，使用以下设置创建新数据磁盘：

    - 名称：**az1000301b-vm1-DataDisk1**

    - 资源组：**az1000301b-RG**

    - 帐户类型：**标准 HDD**

    - 来源类型：**无（空磁盘）**

    - 大小 (GiB)：**1023**

1. 回到 **az1000301b-vm1 - 磁盘** 边栏选项卡，为新创建的磁盘配置以下设置：

    - LUN：**1**

    - 主机缓存 **无**

1. 单击 **保存**。

#### 任务 4：在 Azure VM 中配置数据卷

1. 在 Azure 门户中，请导航到 **az1000301b-vm1** 边栏选项卡。

1. 从 **az1000301b-vm1** 边栏选项卡，通过 RDP 协议连接到 Azure VM，并在提示登录时提供以下凭据：

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

1. 在 RDP 会话中，在服务器管理器中，导航到 **文件和存储服务**。

1. 单击 **存储池**，然后右键单击 **原始** 存储，单击 **新建存储池**。

1. 使用 “新建存储池向导”创建名为 **StoragePool1** 的新存储池，该存储池由你在上一任务中附加的两个磁盘组成

1. 在 **结果** 页面，在 **此向导关闭时创建虚拟磁盘** 旁边放置复选标记，然后单击 **关闭**。

1. 使用“新建虚拟磁盘向导”创建名为 **VirtualDisk1** 的新虚拟磁盘，设置如下：
    - 机箱感知：**禁用**

    - 存储布局：**简单**

    - 预配：**固定**

    - 大小：最大大小

1. 在 **结果** 页面，在 **此向导关闭时创建卷** 旁边放置复选标记。

1. 

    - 驱动器号：**F**

    - 文件系统：**NTFS**

    - 分配单元大小：**默认**

    - 卷标：**数据**

> **结果**：完成此练习后，你可以使用 Azure 门户以及使用 ARM 模板垂直扩展 Azure VM 的计算资源，将附加数据磁盘连接到 Azure VM 以及在 Azure VM 中配置数据卷。


### 练习 2：配置 Azure VM 规模集的计算和存储资源
  
本次练习的主要任务如下：

1. 使用 Azure 资源管理器模板垂直扩展计算 Azure VM 规模集的资源

1. 将数据磁盘附加到 Azure VM 规模集

1. 在 Azure VM 规模集中配置数据卷

1. 水平扩展计算 Azure VM 规模集的资源


#### 任务 1：使用 Azure 资源管理器模板垂直扩展计算 Azure VM 规模集的资源

1. 在实验室虚拟机的 Azure 门户中，导航到 **az1000302b-RG** 资源组边栏选项卡。

1. 从 **az1000302b-RG** 资源组边栏选项卡，导航到 **az1000302b-RG - 部署** 边栏选项卡。

1. 从 **az1000302b-RG - 部署** 边栏选项卡，导航到 **Microsoft.Template - 概述** 边栏选项卡显示最近成功部署。

1. 在 **az1000302b-RG  - 部署** 边栏选项卡，使用 **重新部署** 选项导航到 **自定义部署** 边栏选项卡。

1. 从 **自定义部署** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组： **az1000302b-RG**

    - 位置：你之前选择的相同 Azure 区域的名称

    - VM 名称：**az1000302bvmss1**

    - VM 大小：在本次练习的上一个练习中用于扩展 Azure VM 的 VM 大小的名称

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

   > **注意**：在继续执行本次练习的下一个任务之前，请等待部署完成。部署将增加 Azure VM 规模集的 Azure VM 实例的大小。 

1. 在 Azure 门户中，导航到 **az1000302bvmss1** 边栏选项卡并验证 VM 规模集的 VM 实例的大小是否已更改。


#### 任务 2：将数据磁盘附加到 Azure VM 规模集

1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

   > **注意**：如果这是你第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求你创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在 Clould Shell 窗格中，运行以下命令以将一个 128 GB 磁盘附加到 VM 规模集中的每个 VM 实例：

   ```pwsh
   $vmss = Get-AzVmss -ResourceGroupName 'az1000302b-RG' -VMScaleSetName 'az1000302bvmss1'

   Add-AzVmssDataDisk -VirtualMachineScaleSet $vmss -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType 'Standard_LRS'

   Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name 
   ```

1. 在 Azure 门户中，导航到 az1000302bvmss1 规模集的 **存储** 边栏选项卡并验证是否已添加数据磁盘。 

#### 任务 3：在 Azure VM 规模集中配置数据卷
 
1. 在“Cloud Shell”窗格中，运行以下命令以使用“自定义脚本扩展”在新添加的磁盘上配置简单卷：

   ```pwsh
   $vmss = Get-AzVmss -ResourceGroupName 'az1000302b-RG' -VMScaleSetName 'az1000302bvmss1'

   $publicSettings = @{"fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"}

   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "customScript" -Publisher "Microsoft.Compute" -Type "CustomScriptExtension" -TypeHandlerVersion 1.8 -Setting $publicSettings   
   
   Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name 
   ```

   > **注意**：要确认已配置卷，你将通过 RDP 连接到 VM 规模集中的 VM 实例。

1. 要在 VM 规模集前面标识 Azure 负载均衡器的公共 IP 地址，请在 Azure 门户中导航到 **az1000302bvmss1** 边栏选项卡并注意 **公共 IP 地址** 条目的值。

1. 要标识要连接的端口，请导航到 **az1000302b-RG** 资源组边栏选项卡。

1. 从 **az1000302b-RG** 资源组边栏选项卡，导航到 **az1000302bvmss1-lb** 负载均衡器边栏选项卡。

1. 在 **az1000302bvmss1-lb** 负载均衡器边栏选项卡，请注意 **公共 IP 地址** 设置的值匹配你在此任务中先前确定的 VM 规模集所使用的 IP 地址的值。

1. 从 **az1000302bvmss1-lb** 负载均衡器边栏选项卡，导航到 **az1000302bvmss1-lb - 入站 NAT 规则** 边栏选项卡。

1. 从 **az1000302bvmss1-lb - 入站 NAT 规则** 边栏选项卡，导航到 **natpool.0** 边栏选项卡并注意端口映射以 **50000** 开头。 

1. 在实验室计算机上，通过从 **开始 - >运行** 文本框运行以下命令，启动远程桌面连接到 VM 规模集中的第一个 VM 实例（其中 &lt;public_IP_address&gt; 表示你在此任务中先前确定的公共 IP 地址）：

   ```
   mstsc /f /v:<public_IP_address>:50000
   ```

   > **注意**：确保将占位符<public_IP_address>替换为您在此任务中先前标识的公共 IP 地址的值。

1. 当提示你通过 RDP 登录 VM 规模集的 VM 实例时，请提供以下凭据：

    - 管理员用户名： **Student**

    - 管理员密码：**Pa55w.rd1234**

   > **注意**：忽略有关使用临时个人资料登录的任何消息。 

1. 在 RDP 会话中，启动文件资源管理器并验证 VM 是否具有 127 GB 的额外卷，并为其分配了 F：驱动器号。 

1. 从 RDP 会话退出。


#### 任务 4：水平扩展计算 Azure VM 规模集的资源

1. 在实验室虚拟机的 Azure 门户中，导航到 **az1000302bvmss1** VM 规模集边栏选项卡。

1. 从 **az1000302bvmss1** VM 规模集边栏选项卡，导航到 **缩放** 边栏选项卡。

1. 在 **az1000302bvmss1 - 缩放** 边栏选项卡，使用 **覆盖条件** 设置将实例计数增加到 2。

1. 单击 **保存**。

1. 导航到 **az1000302bvmss1 - 实例** 边栏选项卡并验证实例数已增加到 2。

   > **注意**：你可能需要刷新 **az1000302bvmss1 - 实例** 边栏选项卡的显示，查看预配其他实例的过程。


> **结果**：完成此练习后，你已使用 Azure 资源管理器模板垂直扩展 Azure VM 规模集的计算资源，将数据磁盘附加到 Azure VM 规模集，配置 Azure VM 规模集中的数据卷，以及水平扩展 Azure VM 规模集的计算资源。

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **Bash**。

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
