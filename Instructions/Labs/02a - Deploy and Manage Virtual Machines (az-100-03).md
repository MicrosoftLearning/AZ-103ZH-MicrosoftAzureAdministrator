---
lab:
    title: '部署和管理虚拟机'
    module: '模块 02 - Azure 虚拟机'
---

# 实验室：部署和管理虚拟机

本实验室中的所有任务均从 Azure 门户（包括 PowerShell Cloud Shell 会话）执行，但练习 2 任务 2 和 练习 2 任务 3 除外，其中包括从远程桌面会话至 Azure VM 的执行步骤

   > **备注**：如果不使用 Cloud Shell，则必须在实验室虚拟机上安装 Azure PowerShell 模块 [**https://docs.microsoft.com/zh-cn/powershell/azure/install-Az-ps**](https://docs.microsoft.com/zh-cn/powershell/azure/install-Az-ps)

实验文件： 

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.json**

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.parameters.json**

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_install_iis_vmss.zip**

### 场景
  
Adatum 公司希望通过使用 Azure 虚拟机（VM）和 Azure VM 规模集来实现其工作负荷


### 目标
  
完成本实验室后，你将能够：

-  使用 Azure 门户、Azure PowerShell 和 Azure 资源管理器模板部署 Azure VM。

-  为运行 Windows 和 Linux 操作系统，配置 Azure VM 的网络设置

-  部署和配置 Azure VM 规模集


### 练习 1：使用 Azure 门户、Azure PowerShell 和 Azure 资源管理器模板部署 Azure VM
  
本次练习的主要任务如下：

1. 使用 Azure 门户将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至可用性集

1. 使用 Azure PowerShell 将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至现有可用性集

1. 使用 Azure 资源管理器模板将运行 Linux 的两个 Azure VM 部署至可用性集


#### 任务 1：使用 Azure 门户将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至可用性集

1. 从实验室虚拟机启动 Microsoft Edge，在[**http://portal.azure.com**](http://portal.azure.com)上浏览至 Azure 门户，并使用在你计划用于本逻辑阵列模块的 Azure 订阅中具有“所有者”角色的 Microsoft 帐户进行登录。

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在 Azure 市场搜索 **Windows Server**。从搜索结果列表选择 **Windows Server**。

1. 在 Windows Server 页面上，使用下拉菜单选择 **[smalldisk] Windows Server 2016 Datacenter**，然后单击 **创建**。

1. 使用**创建虚拟机**边栏选项卡部署具有以下设置的虚拟机：

    - 订阅：在本实验室中你所使用的订阅名称

    - 资源组：新资源组 **az1000301-RG** 的名称

    - 虚拟机名称：**az1000301-vm0**

    - 区域：**（美国）美国东部**（或离你更近的区域）

      > **备注**：若要标识可在其中配置 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

    - 可用性选项：**可用性集**

    - 可用性集：单击 **新建**，并为新可用性集 **az1000301-avset0**（具有 **2** 个容错域和 **5** 个更新域）命名。单击 **确定**。

    - 图片：**[smalldisk] Windows Server 2016 Datacenter**

    - 大小：**标准 DS2_v2**

    - 用户名称：**学生**

    - 密码：**Pa55w.rd1234**

    - 公共入站端口：**无**

    - 是否已拥有 Windows 许可？：**否**

1. 单击 **下一个:磁盘 >**。    

    - OS 磁盘类型：**标准 HDD**

1. 单击 **下一个:网络 >**。

1. 在“网络”选项卡上，在“虚拟网络”下单击 **新建**。使用默认分配的虚拟网络名称并指定以下内容：

    - 虚拟网络地址范围：**10.103.0.0/16**

    - 子网名称：**subnet0**

    - 子网地址范围：**10.103.0.0/24**

1. 单击 **“确定”**。

1. 单击**下一个:管理>**。

1. 在“管理”选项卡上，查看默认设置，并注意是通过自动预先配置的新诊断存储账户打开启动诊断。

1. 单击**下一个:高级>**。

1. 在“高级”选项卡上，查看可用设置。

1. 将所有设置保留为其默认值，然后单击**查看 + 创建**。

1. 单击 **创建**。

   > **备注**：你将在本实验室的第二个练习中配置你在此任务中所创建的网络安全组

   > **注**：在继续执行下一个任务前，请等待部署完成。该操作需约 5 分钟。


#### 任务 2：使用 Azure PowerShell 将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至现有可用性集

1. 从 Azure 门户，在“Cloud Shell”窗格中启动 PowerShell 会话。 

   > **备注**：如果这是你第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求你创建 Azure 文件共享以保留 Cloud Shell 文件。此时，请接受默认设置，这将在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   $vmName = 'az1000301-vm1'
   $vmSize = 'Standard_DS2_v2'
```

   > **备注**：这将设置指定 Azure VM 名称及其大小的变量的值

1. 在“Cloud Shell”窗格中，运行以下命令：

```pwsh
   $resourceGroup = Get-AzResourceGroup -Name 'az1000301-RG'
   $location = $resourceGroup.Location
```

   > **备注**：这些命令设置指定目标资源组及其位置的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

```pswh
   $availabilitySet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup.ResourceGroupName -Name 'az1000301-avset0'
   $vnet = Get-AzVirtualNetwork -Name 'az1000301-RG-vnet' -ResourceGroupName $resourceGroup.ResourceGroupName
   $subnetid = (Get-AzVirtualNetworkSubnetConfig -Name 'subnet0' -VirtualNetwork $vnet).Id
```

   > **备注**：这些命令设置指定你将新的Azure VM部署到其中的可用性集、虚拟网络和子网的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -Name "$vmName-nsg"
   $pip = New-AzPublicIpAddress -Name "$vmName-ip" -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -AllocationMethod Dynamic 
   $nic = New-AzNetworkInterface -Name "$($vmName)$(Get-Random)" -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -SubnetId $subnetid -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

   > **备注**：这些命令创建将被新的 Azure VM 使用的新的网络安全组、公共 IP 地址和网络接口

   > **注**：你将在本实验室的第二个练习中配置你在此任务中所创建的网络安全组

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   $adminUsername = 'Student'
   $adminPassword = 'Pa55w.rd1234'
   $adminCreds = New-Object PSCredential $adminUsername, ($adminPassword | ConvertTo-SecureString -AsPlainText -Force)
```

   > **备注**：这些命令设置指定新 Azure VM 的本地管理员帐户凭据的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   $publisherName = 'MicrosoftWindowsServer'
   $offerName = 'WindowsServer'
   $skuName = '2016-Datacenter'
```

   > **备注**：这些命令设置指定将用于预配新 Azure VM 的 Azure 市场图片属性的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   $osDiskType = (Get-AzDisk -ResourceGroupName $resourceGroup.ResourceGroupName)[0].Sku.Name
```

   > **备注**：此命令设置指定新 Azure VM 的操作系统磁盘类型的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $availabilitySet.Id
   Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
   Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $adminCreds 
   Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version 'latest'
   Set-AzVMOSDisk -VM $vmConfig -Name "$($vmName)_OsDisk_1_$(Get-Random)" -StorageAccountType $osDiskType -CreateOption fromImage
   Set-AzVMBootDiagnostic -VM $vmConfig -Disable
```

   > **备注**：这些命令设置将用于预配新 Azure VM 的 Azure VM 配置对象的属性，其中包括 VM 大小、其可用性集、网络接口、计算机名称、本地管理员凭据、源图像、操作系统磁盘和启动诊断设置等。

1. 在 Cloud Shell 窗格中，运行以下命令：

```pwsh
   New-AzVM -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -VM $vmConfig
```

   > **备注**：此命令启动新 Azure VM 的部署

   > **注**：请勿等待部署完成，而是继续进行下一项任务。


#### 任务 3：使用 Azure 资源管理器模板将运行 Linux 的两个 Azure VM 部署至可用性集

1. 在 Azure 门户中，导航到**创建资源**边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在 Azure 市场中搜索 **模板部署**，并选择 **模板部署(使用自定义模板部署)**。

1. 单击 **创建**。

1. 在 **自定义部署** 边栏选项卡中，单击 **在编辑器中生成自己的模板** 链接。如果未看到此链接，请改为单击 **编辑模板**。

1. 从 **编辑模板** 边栏选项卡，加载模板文件 **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.json**。 

   > **备注**：查看模板内容，并注意其定义将两个托管 Linux Ubuntu 的 Azure VM 部署至可用性集和现有虚拟网络 **az1000301-vnet0**中。此虚拟网络不在部署中。你将在以下参数中更改虚拟网络名称。

1. 保存模板并返回到**自定义部署**边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡。

1. 从“编辑参数” **Edit parameters** 边栏选项卡加载参数文件 **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.parameters.json**。 

1. 保存参数并返回到 **自定义部署** 边栏选项卡。 

1. 从**自定义部署**边栏选项卡，使用以下设置初始化模板部署：

    - 订阅：你在此实验室中使用的订阅的名称

    - 资源组：新资源组名称**az1000302-RG**

    - 位置：你先前在本次练习中选择的同一 Azure 区域

    - Vm 名称前缀： **az1000302-vm**

    - Nic 名称前缀：**az1000302-nic**

    - Pip 名称前缀：**az1000302-ip**

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

    - 图像发布者：**Canonical**

    - 图像提供：**UbuntuServer**

    - 图像 SKU：**16.04.0-LTS**

    - Vm 大小：根据讲师的建议，使用**Standard_DS1_v2**或**Standard_DS2_v2** 
   
    - 虚拟网络名称：**az1000301-RG-vnet**
    
    - 虚拟网络资源组：新资源组的名称 **az1000301-RG**
    
    - 子网名称：**subnet0**

   > **备注**：在继续执行下一个任务前，请等待部署完成。该操作需约 5 分钟。

> **结果**：完成本次练习后，你已通过使用 Azure 门户将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至可用性集，通过使用 Azure PowerShell 将另一运行 Windows Server 2016 Datacenter 的 Azure VM 部署至同一可用性集，并通过使用 Azure 资源管理器模板将两个运行 Linux Ubuntu 的 Azure VM 部署至可用性集。

   > **注**：你当然可以在单个任务中使用模板来部署两个托管 Windows Server 2016 datacenter 的 Azure VM （如同使用托管 Linux Ubuntu 服务器的两个 Azure VM ）。在两个单独的任务中部署此类 Azure VM 的原因是：让你有机会熟悉 Azure 门户和基于 Azure PowerShell 的部署。


### 练习 2：配置运行 Windows 和 Linux 操作系统的 Azure VM 的网络设置
  
本次练习的主要任务如下：

1. 配置 Azure VM 的静态专用和公共 IP 地址

1. 通过公共 IP 地址连接至运行 Windows Server 2016 Datacenter 的 Azure VM

1. 通过专用 IP 地址连接至运行 Linux Ubuntu Server 的 Azure VM


#### 任务 1：配置 Azure VM 的静态专用和公共 IP 地址

1. 在 Azure 门户中，请导航到 **az1000301-vm0** 边栏选项卡。

1. 从 **az1000301-vm0** 边栏选项卡，导航到 **“网络”** 边栏选项卡，显示公共 IP 地址的配置 **az1000301-vm0-ip**，分配给其网络接口。

1. 从 **“网络”** 边栏选项卡，单击代表公共 IP 地址的链接。

1. 在 az1000301-vm0-ip 边栏选项卡上，单击 **“配置”**。

1. 将公共 IP 地址的分配更改为 **“静态”**，然后单击 **“保存”**。

   > **备注**：记录分配给网络接口的公共 IP 地址**az1000301-vm0**。在本次练习中，你后续将需要该域名。

1. 在 Azure 门户中，请导航到 **az1000302-vm0** 边栏选项卡。

1. 从 **az1000302-vm0** 边栏选项卡，显示 **“网络”** 边栏选项卡。

1. 从 **“az1000302-vm0 - 网络”** 边栏选项卡，单击代表网络接口的链接。

1. 从显示 **az1000302-vm0** 网络接口属性的边栏选项卡，导航到其 **“IP 配置”** 边栏选项卡。

1. 在 **“IP 配置”** 边栏选项卡上，将 **ipconfig1** 专用 IP 地址配置为静态并将其设置为 **10.103.0.100**，然后单击 **“保存”**。

   > **备注**：更改专用 IP 地址分配需重新启动 Azure VM。

   > **注**：可以通过静态或动态分配的公共和专用 IP 地址连接到 Azure VM。选择静态 IP 分配通常在这些 IP 地址与 IP 筛选、路由选择结合使用的情况下进行，或者，如果将它们分配给充当 DNS 服务器的 Azure VM 的网络接口。


#### 任务 2：通过公共 IP 地址连接至运行 Windows Server 2016 Datacenter 的 Azure VM

1. 在 Azure 门户中，请导航到 **az1000301-vm0** 边栏选项卡。

1. 从 **az1000301-vm0** 边栏选项卡，导航到 **“网络”** 边栏选项卡。

1. 在**az1000301-vm0 - Networking**边栏选项卡，查看分配给**az1000301-vm0**网络接口的网络安全组的入站端口规则。

   > **注**：由内置规则组成的默认配置阻止来自因特网的入站连接（包括经由 RDP 端口 TCP 3389 的连接）

1. 使用以下设置将入站安全规则添加到现有网络安全组：

    - 来源：**任何**

    - 源端口范围：**\***

    - 目的地：**任何**

    - 目标端口范围： **3389**

    - 协议：**TCP**

    - 操作：**允许**

    - 优先级： **100**

    - 名称：**AllowInternetRDPInBound**

1. 在 Azure 门户中，显示**az1000301-vm0**边栏选项卡的**概述**窗格。 

1. 在**“概观”**窗格的 **az1000301-vm0** 边栏选项卡中，单击**“连接”**，生成 RDP 文件并使用它连接 **az1000301-vm0**。

1. 出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称：**学生**

    - 密码：**Pa55w.rd1234**


#### 任务 3：通过专用 IP 地址连接至运行 Linux Ubuntu Server 的 Azure VM
 
1. 在与**az1000301-vm0**的 RDP 会话中，启动**命令提示符**。

1. 从命令提示符处，运行以下命令：

```
   nslookup az1000302-vm0
```

1. 检查输出，并记录该名称解析为你在本次练习的第一个任务中分配的 IP 地址（**10.103.0.100**）。

   > **注**：这是正常的。Azure 在虚拟网络中提供内置 DNS 名称解析。 

1. 在 **az1000301-vm0** 的 RDP 会话中，从服务器管理器单击 **“本地服务器”**，然后禁用 **“IE 增强安全配置”**。

1. 在**az1000301-vm0**的 RDP 会话中，启动 IE 浏览器，并从[**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)下载**putty.exe** 

1. 使用 **putty.exe** 验证你是否可以通过 **SSH** 协议（TCP 22）成功连接到其专用 IP 地址上的 **az1000302-vm0**。

1. 出现提示时，通过指定以下值进行身份验证：

    - 用户名称：**学生**

    - 密码：**Pa55w.rd1234**

    > **备注**：用户名和密码都区分大小写。
 
1. 成功通过身份验证后，终止到 **az1000301-vm0** 的 RDP 会话。

1. 在实验室虚拟机的 Azure 门户中，导航到 **az1000302-vm0** 边栏选项卡。

1. 从 **az1000302-vm0** 边栏选项卡，导航到 **“网络”** 边栏选项卡。

1. 在**az1000302-vm0 - Networking**边栏选项卡上，查看分配给**az1000301-vm0**网络接口的网络安全组的入站端口规则，以确定通过专用 IP 地址进行 SSH 连接的成功原因。

   > **注**：由内置规则组成的默认配置允许 Azure 虚拟网络环境中的入站连接（包括经由 SSH 端口 TCP 22 的连接）。

> **结果**：完成本次练习后，你已配置 Azure VM 的静态专用和公共 IP 地址，通过公共 IP 地址连接到运行 Windows Server 2016 Datacenter 的 Azure VM，并通过专用 IP 地址连接到运行 Linux Ubuntu Server 的 Azure VM


### 练习 3：部署和配置 Azure VM 规模集

本次练习的主要任务如下：

1. 确定 Azure VM 规模集部署的可用 DNS 名称

1. 部署 Azure VM 规模集

1. 使用 DSC 扩展，在规模集 VM 上安装 IIS


#### 任务 1：确定 Azure VM 规模集部署的可用 DNS 名称

1. 从 Azure 门户，在 Cloud Shell 窗格中启动 PowerShell 会话。 

1. 在 Cloud Shell 窗格中，运行以下命令，将占位符 &lt;custom-label&gt; 替换为任何可能唯一的字符串。

```pwsh
   $rg = Get-AzResourceGroup -Name az1000301-RG
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location $rg.Location
```

1. 验证命令是否返回**True**。如果不是，请使用不同的 &lt;custom-label&gt; 值重新运行相同的命令，直到命令返回**“True”**。 

1. 记下生成成功结果的 &lt;custom-label&gt; 值。你将在下一个任务中使用它


#### 任务 2：部署 Azure VM 规模集

1. 在 Azure 门户中，导航到**创建资源**边栏选项卡。

1. 从**创建资源**边栏选项卡，搜索 Azure 市场，以获取**虚拟机规模集**。

1. 使用搜索结果列表导航到 **“创建虚拟机规模集”** 边栏选项卡。

1. 使用 **“创建虚拟机规模集”** 边栏选项卡，通过以下设置部署虚拟机规模集：

    - 虚拟机规模集名称：**az1000303vmss0**

    - 操作系统磁盘图像：**Windows Server 2016 Datacenter**

    - 订阅：你在本实验室中所使用的订阅名称

    - 资源组：新资源组 **az1000303-RG** 的名称

    - 位置：你在本实验室的之前练习中选择的同一 Azure 区域

    - 可用性区域：**无**

    - 用户名称：**学生**

    - 密码：**Pa55w.rd1234**

    - 实例数：**1**

    - 实例大小：**DS2 v2**

    - 部署为低优先级：**否**

    - 使用托管磁盘：**是**

    - 自动缩放：**禁用**

    - 选择负载均衡选项：**负载均衡器**

    - 公共 IP 地址名称：**az1000303vmss0-ip**

    - 域名标记：输入您在上一个任务中确定的&lt;custom-label&gt;的值

    - 虚拟网络：新虚拟网络**az1000303-vnet0**的名称，设置如下：

        - 地址范围：**10.203.0.0/16**

        - 子网名称：**subnet0**

        - 子网地址范围：**10.203.0.0/24**

    - 每个实例的公共 IP 地址：**关**
    
    - 加速网络：**关**
    
    - NIC 网络资源组：**基本**
    
    - 选择入站端口：**HTTP**
    
    - 启动诊断：**关**
    
    - 系统分配的托管标识：**关**

   > **备注**：在继续执行下一个任务前，请等待部署完成。该操作需约 5 分钟。


#### 任务 3：使用 DSC 扩展，在规模集 VM 上安装 IIS

1. 在 Azure 门户中，导航到**az1000303vmss0**边栏选项卡。

1. 从 **az1000303vmss0** 边栏选项卡，显示其扩展边栏选项卡。

1. 从**az1000303vmss0-扩展**边栏选项卡，使用以下设置添加**PowerShell 所需状态配置**扩展：

   > **注**：DSC 配置模块可从 **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_install_iis_vmss.zip** 上传。该模块包含安装 Web 服务器（IIS）角色的 DSC 配置脚本。

    - 配置模块或脚本：**"az-100-03_install_iis_vmss.zip"**

    - 模块限定的配置名称：**az-100-03_install_iis_vmss.ps1\IISInstall**

    - 配置参数：留空

    - 配置数据 PSD1 文件：留空

    - WMF 版本：**最新**

    - 数据收集。**禁用**

    - 版本：**2.76**

    - 自动升级次要版本：**是**

1. 导航到 **“az1000303vmss0 - 实例”** 边栏选项卡，启动 **az1000303vmss0_0** 实例的**升级**。

   > **备注**：更新将触发 DSC 配置脚本的应用程序。等待升级完成。该操作需约 5 分钟。你可以从**az1000303vmss0-实例**边栏选项卡监控进度。

1. 升级完成后，导航到 **“概述”** 边栏选项卡。 

1. 在 **az1000303vmss0-ip** 边栏选项卡上，记下分配给 **az1000303vmss0** 的公共 IP 地址。

1. 启动 Microsoft Edge 并导航到你在上一步中确定的公共 IP 地址。

1. 验证浏览器是否显示默认的 IIS 主页。 

> **结果**：完成本次练习后，你已为 Azure VM 规模集部署确定了可用的 DNS 名称，部署了 Azure VM 规模集，并通过使用 DSC 扩展在规模集 VM 上安装了 IIS。

## 练习 4：移除实验室资源

#### 任务1：打开 Cloud Shell

1. 在门户顶部，单击 **“Cloud Shell”** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **“Bash”**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键列出你在本实验室中创建的所有资源组：

```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv
```

1. 验证输出中是否仅包含在本练习中创建的资源组。将在下一个任务中删除这些资源组。

#### 任务2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键以删除你在本实验室中创建的资源组

```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **结果**：在本练习中，将删除本实验室中使用的资源。
