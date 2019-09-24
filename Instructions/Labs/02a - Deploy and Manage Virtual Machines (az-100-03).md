---
lab:
    title: '部署和管理虚拟机'
    module: '部署和管理虚拟机'
---

# 逻辑阵列模块：部署和管理虚拟机

本逻辑阵列模块中的所有任务均在 Azure 门户（包括 PowerShell Cloud Shell 会话）中执行，但练习 2 任务 2 和 练习 2 任务 3 除外，其中包括从远程桌面会话至 Azure VM 的执行步骤

   > **注**：如果不使用 Cloud Shell ，则必须在逻辑阵列模块虚拟机上安装 Azure PowerShell 模块 [**https://docs.microsoft.com/zh-cn/powershell/azure/azurerm/install-azurerm-ps**](https://docs.microsoft.com/zh-cn/powershell/azure/azurerm/install-azurerm-ps)

逻辑阵列模块文件： 

-  **Labfiles\\AZ100\\Mod03\\az-100-03_deploy_azure_vm.ps1**

-  **Labfiles\\AZ100\\Mod03\\az-100-03_azuredeploy.json**

-  **Labfiles\\AZ100\\Mod03\\az-100-03_azuredeploy.parameters.json**

-  **Labfiles\\AZ100\\Mod03\\az-100-03_install_iis_vmss.zip**

### 方案
  
Adatum Corporation 希望通过使用 Azure 虚拟机（VM）和 Azure VM 规模集来实现其工作负荷


### 目标
  
完成本逻辑阵列模块后，您将能够：

-  使用 Azure 门户、Azure PowerShell 和 Azure 资源管理器模板部署 Azure VM。

-  为运行 Windows 和 Linux 操作系统，配置 Azure VM 的网络设置

-  部署和配置 Azure VM 规模集


### 练习 1：使用 Azure 门户、Azure PowerShell 和 Azure 资源管理器模板部署 Azure VM。
  
本次练习的主要任务如下：

1. 使用 Azure 门户将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至可用性集

1. 使用 Azure PowerShell 将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至现有可用性集

1. 使用 Azure 资源管理器模板将运行 Linux 的两个 Azure VM 部署至可用性集


#### 任务 1：使用 Azure 门户将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至可用性集

1. 从逻辑阵列模块虚拟机启动 Microsoft Edge，在 [**http://portal.azure.com**](http://portal.azure.com) 上浏览至 Azure 门户，并使用在您计划用于本次逻辑阵列模块的 Azure 订阅中具有“所有者”角色的 Microsoft 帐户进行登录。

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏搜索 Azure 市场 **Windows Server 2016 Datacenter**。

1. 使用搜索结果列表导航到 **创建虚拟机** 边栏用于部署 Windows Server 2016 Datacenter Azure 市场图像。

1. 使用 **创建虚拟机** 边栏部署具有以下设置的虚拟机

    - 订阅：在本次逻辑阵列模块中您所使用的订阅名称

    - 资源组：新资源组 **az1000301-RG** 的名称

    - 虚拟机名称： **az1000301-vm0**

    - 区域：最靠近逻辑阵列模块位置的 Azure 区域的名称以及可以在其中配置 Azure VM 的位置

    - 可用性选项：**可用性集**

    - 可用性集：新可用性集的名称 **az1000301-avset0** 与故障域 **2** 和更新域 5。

    - 图像： **Windows Server 2016 Datacenter**

    - 大小： **标准 DS1 v2**

    - 用户名称： **学生**

    - 密码： **Pa55w.rd1234**

    - 公共入站端口： **无**

    - 是否已拥有 Windows 许可证？： **否**

    - VM 磁盘类型： **标准 HDD**

    - 虚拟网络：新虚拟网络的名称 **az1000301-vnet0**，设置如下：

        - 地址空间：**10.103.0.0/16**

        - 子域名称：**subnet0**

        - 子网地址范围： **10.103.0.0/24**

    - 公共 IP：新公共 IP 地址的名称 **az1000301-vm0-ip**

    - 网络安全组： **基本**

    - 公共入站端口： **无**

    - 加速网络： **关**

    - 启动诊断: **关**

    - OS 来宾诊断： **关**

    - 系统分配的托管标识： **关**

    - 启用自动关闭： **关**

    - 启用备份： **关**

   > **注**：如需识别您可以提供 Azure VM 的 Azure 区域，请查阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)。

   > **注**：您将在本逻辑阵列模块的第二个练习中配置您在此任务中所创建的网络安全组

   > **注**：在继续执行下一个任务前，请等待部署完成。该操作需约 5 分钟。


#### 任务 2：使用 Azure PowerShell 将运行 Windows Server 2016 Datacenter 的 Azure VM 部署至现有可用性集

1. 从 Azure 门户，在 Cloud Shell 窗格中启动 PowerShell 会话。 

   > **注**：如果这是您第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求您创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $vmName = 'az1000301-vm1'
   $vmSize = 'Standard_DS1_v2'
   ```

   > **注**：此命令集指定 Azure VM 名称及其大小的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $resourceGroup = Get-AzResourceGroup -Name 'az1000301-RG'
   $location = $resourceGroup.Location
   ```

   > **注**：这些命令集指定目标资源组及其位置的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $availabilitySet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup.ResourceGroupName -Name 'az1000301-avset0'
   $vnet = Get-AzVirtualNetwork -Name 'az1000301-vnet0' -ResourceGroupName $resourceGroup.ResourceGroupName
   $subnetid = (Get-AzVirtualNetworkSubnetConfig -Name 'subnet0' -VirtualNetwork $vnet).Id
   ```

   > **注**：这些命令集指定可用性集，虚拟网络和子域网的变量值，您将在其中部署新的 Azure VM

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -Name "$vmName-nsg"
   $pip = New-AzPublicIpAddress -Name "$vmName-ip" -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -AllocationMethod Dynamic 
   $nic = New-AzNetworkInterface -Name "$($vmName)$(Get-Random)" -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -SubnetId $subnetid -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

   > **注**：这些命令创建新的网络安全组、公共 IP 地址和网络接口，且这些将被新的 Azure VM 所使用

   > **注**：您将在本逻辑阵列模块的第二个练习中配置您在此任务中所创建的网络安全组

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $adminUsername = 'Student'
   $adminPassword = 'Pa55w.rd1234'
   $adminCreds = New-Object PSCredential $adminUsername, ($adminPassword | ConvertTo-SecureString -AsPlainText -Force)
   ```

   > **注**：这些命令集指定新 Azure VM 的本地管理员帐户凭据的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $publisherName = 'MicrosoftWindowsServer'
   $offerName = 'WindowsServer'
   $skuName = '2016-Datacenter'
   ```

   > **注**：这些命令集指定将用于配置新 Azure VM 的 Azure 市场 图像属性的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $osDiskType = (Get-AzResource -ResourceGroupName $resourceGroup.ResourceGroupName -ResourceType Microsoft.Compute/disks)[0].Sku.name
   ```

   > **注**：此命令集变指定新 Azure VM 的操作系统磁盘类型的变量值

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $availabilitySet.Id
   Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
   Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $adminCreds 
   Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version 'latest'
   Set-AzVMOSDisk -VM $vmConfig -Name "$($vmName)_OsDisk_1_$(Get-Random)" -StorageAccountType $osDiskType -CreateOption fromImage
   Set-AzVMBootDiagnostic -VM $vmConfig -Disable
   ```

   > **注**：这些命令设置将用于配置新 Azure VM 的 Azure VM 配置对象的属性，其中包括 VM 大小、其可用性集、网络接口、计算机名称、本地管理员凭据、源图像、操作系统磁盘和启动诊断设置等。

1. 在 Cloud Shell 窗格中，运行以下命令：

   ```
   New-AzVM -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -VM $vmConfig
   ```

   > **注**：此命令启动新 Azure VM 的部署

   > **注**：请勿等待第一台虚拟机预配完成，而是继续进行下一项任务。

#### 任务 3：使用 Azure 资源管理器模板将运行 Linux 的两个 Azure VM 部署至可用性集

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡中搜索 Azure Marketplace 中的 **模板部署**。

1. 使用搜索结果列表导航到 **部署自定义模板** 边栏选项卡。

1. 在 **自定义部署** 边栏选项卡上，请选择 **在编辑器中构建自己的模板**。

1. 从 **编辑模板** 边栏选项卡，加载模板文件 **Labfiles\\AZ100\\Mod03\\az-100-03_azuredeploy.json**。 

   > **注**：查看模板内容，并注意其定义的将两个托管 Linux Ubuntu 的 Azure VM 部署至可用性集和现有虚拟网络 az1000301-vnet0 中。

1. 请保存模板并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡

1. 从 **编辑参数** 边栏中加载参数文件 **Labfiles\\AZ100\\Mod03\\az-100-03_azuredeploy.parameters.json**。 

1. 请保存参数并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：在本次逻辑阵列模块中您所使用的订阅名称

    - 资源组：新资源组 **az1000302-RG** 的名称

    - 位置：您先前在本次练习中选择的 Azure 区域

    - Vm 名称前缀： **az1000302-vm**

    - Nic 名称前缀： **az1000302-nic**

    - Pip 名称前缀： **az1000302-ip**

    - 管理员用户名： **学生**

    - 管理员密码： **Pa55w.rd1234**

    - 虚拟网络名称： **az1000301-vnet0**

    - 图像发布者： **Canonical**

    - 图像提供： **UbuntuServer**

    - 图像 SKU： **16.04.0-LTS**

    - VM 大小： **Standard_DS1_v2**

   > **注**：在继续执行下一个任务前，请等待部署完成。该操作需约 5 分钟。

> **结果**：完成本次练习后，您已使用 Azure 门户运行 Windows Server 2016 Datacenter 的 Azure VM 部署至可用性集，使用 Azure PowerShell 将另一台运行 Windows Server 2016 Datacenter 的 Azure VM 部署至同一可用性集，并通过使用 Azure 资源管理器模板部署两个 Azure 的运行 Linux Ubuntu 的 VM 转换为可用性集。

   > **注**：您当然可以在一个任务中使用模板来部署两个托管 Windows Server 2016 datacenter 的 Azure VM （如同使用托管 Linux Ubuntu 服务器的两个 Azure VM ）。在两个单独的任务中部署此类 Azure VM 的原因是：让您有机会熟悉 Azure 门户和基于 Azure PowerShell 的部署。


### 练习 2：为运行 Windows 和 Linux 操作系统，配置 Azure VM 的网络设置
  
本次练习的主要任务如下：

1. 配置 Azure VM 的静态专用和公共 IP 地址

1. 通过公共 IP 地址连接至运行 Windows Server 2016 Datacenter 的 Azure VM

1. 通过专用 IP 地址连接至运行 Linux Ubuntu Server 的 Azure VM


#### 任务 1：配置 Azure VM 的静态专用和公共 IP 地址

1. 在 Azure 门户中，请导航到 **az1000301-vm0** 边栏选项卡。

1. 从 **az1000301-vm0** 边栏选项卡，导航到 **az1000301-vm0-ip - Configuration** 边栏选项卡，显示公共 IP 地址的配置 **az1000301-vm0-ip**，分配给其网络接口。

1. 从 **az1000301-vm0-ip - Configuration** 边栏选项卡，将分配的公共 IP 地址更改 **为静态**。

   > **注**：记录分配给网络接口的公共 IP 地址 **az1000301-vm0**。在本次练习中，您后续将需要该域名。

1. 在 Azure 门户中，请导航到 **az1000302-vm0** 边栏选项卡。

1. 从 **az1000302-vm0** 边栏选项卡，显示 **az1000302-vm0 - Networking** 边栏选项卡。

1. 从 **az1000302-vm0 - Networking** 边栏选项卡，导航到显示其网络接口属性的边栏选项卡。

1. 边栏显示网络接口属性 **az1000302-vm0**，导航到其 **ipconfig1** 边栏选项卡。

1. 在 **ipconfig1** 边栏选项卡，将专用 IP 地址配置为静态并将其设置为 **10.103.0.100**。

   > **注**：更改专用 IP 地址分配需重新启动 Azure VM 。

   > **注**：可以通过静态或动态分配的公共和私有 IP 地址，连接到 Azure VM。选择静态 IP 分配通常在这些 IP 地址与 IP 筛选、路由选择结合使用的情况下进行，或者，如果将它们分配给充当 DNS 服务器的 Azure VM 的网络接口。


#### 任务 2：通过公共 IP 地址连接至运行 Windows Server 2016 Datacenter 的 Azure VM

1. 在 Azure 门户中，请导航到 **az1000301-vm0** 边栏选项卡。

1. 从 **az1000301-vm0** 边栏选项卡，导航到 **az1000301-vm0 - Networking** 边栏选项卡。

1. 在 **az1000301-vm0 - Networking** 边栏选项卡，查看分配给 **az1000301-vm0** 网络接口的网络安全组的入站端口规则。

   > **注**：由内置规则组成的默认配置阻止来自因特网的入站连接（包括经由 RDP 端口 TCP 3389 的连接）

1. 使用以下设置将入站安全规则添加到现有网络安全组：

    - 来源： **任何**

    - 源端口范围： **\***

    - 目的地： **任何**

    - 目标端口范围： **3389**

    - 协议： **TCP**

    - 动作： **允许**

    - 优先级： **100**

    - 名称： **AllowInternetRDPInBound**

1. 在 Azure 门户中，显示 **az1000301-vm0** 边栏的 **概述** 窗格。 

1. 来自 **概述** 窗格的 **az1000301-vm0** 边栏选项卡，生成 RDP 文件并使用它连接至 **az1000301-vm0**。

1. 出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称： **学生**

    - 密码： **Pa55w.rd1234**


#### 任务 3：通过专用 IP 地址连接至运行 Linux Ubuntu Server 的 Azure VM
 
1. 在 **az1000301-vm0** 的 RDP 会话中，启动 **命令提示符**。

1. 从命令提示符处，运行以下命令：

   ```
   nslookup az1000302-vm0
   ```

1. 检查输出，并记录该名称解析为您在本次练习的第一个任务中指定的 IP 地址（** 10.103.0.100 **）。

   > **注**：这是正常的。Azure 在虚拟网络中提供内置 DNS 名称解析。 

1. 在 **az1000301-vm0** 的 RDP 会话中，从服务器管理器暂时禁用 **IE 增强安全配置**。

1. 在 **az1000301-vm0** 的 RDP 会话中，启动 IE 浏览器，访问以下网址下载 **putty.exe**：[**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 

1. 使用 **putty.exe** 验证您是否可以通过 **SSH** 协议（TCP 22）成功连接到其专用 IP 地址上的 **az1000302-vm0**。

1. 出现提示时，通过指定以下值进行身份验证：

    - 用户名称： **学生**

    - 密码： **Pa55w.rd1234**

1. 成功通过身份验证后，终止到 **az1000301-vm0** 的 RDP 会话.

1. 在逻辑阵列模块虚拟机的 Azure 门户中，导航到 **az1000302-vm0** 边栏选项卡。

1. 从 **az1000302-vm0** 边栏选项卡，导航到 **az1000302-vm0 - Networking** 边栏选项卡。

1. 在 **az1000302-vm0 - Networking** 边栏选项卡上，查看分配给 **az1000301-vm0** 网络接口的网络安全组的入站端口规则，以确定通过专用 IP 地址进行 SSH 连接的成功原因。

   > **注**：由内置规则组成的默认配置允许 Azure 虚拟网络环境中的入站连接（包括经由 SSH 端口 TCP 22 的连接）。

> **结果**：完成本次练习后，您已配置 Azure VM 的静态专用和公有 IP 地址，通过公共 IP 地址连接到运行 Windows Server 2016 Datacenter 的 Azure VM，并通过专用 IP 地址连接到运行 Linux Ubuntu Server 的 Azure VM


### 练习 3：部署和配置 Azure VM 规模集

本次练习的主要任务如下：

1. 确定 Azure VM 规模集部署的可用 DNS 名称

1. 部署 Azure VM 规模集

1. 使用 DSC 扩展，在规模集 VM 上安装 IIS


#### 任务 1：确定 Azure VM 规模集部署的可用 DNS 名称

1. 从 Azure 门户，在 Cloud Shell 窗格中启动 PowerShell 会话。 

1. 在 Cloud Shell 窗格中，运行以下命令，将占位符 &lt;custom-label&gt; 替换为任何可能唯一的字符串，并将占位符 &lt;location-of-az1000301-RG&gt; 替换为您在其中创建 **az1000301-RG** 资源组的 Azure 区域的名称。

   ```
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location-of-az1000301-RG>'
   ```

1. 验证返回的命令是否为 **真**。如果没有，请使用 &lt;custom-label&gt; 不同的值重新运行相同的命令，直至命令返回 **真**。 

1. 请记录获得成功结果的 &lt;custom-label&gt; 的值。您将在下一个任务中使用它


#### 任务 2：部署 Azure VM 规模集

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，搜索 Azure 市场，以获取 **虚拟机规模集**。

1. 使用搜索结果列表导航到 **创建虚拟机规模集** 边栏选项卡。

1. 使用 **创建虚拟机规模集** 边栏选项卡，通过以下设置部署虚拟机规模集：

    - 虚拟机规模集名称： **az1000303vmss0**

    - 操作系统磁盘映像： **Windows Server 2016 Datacenter**

    - 订阅：在本次逻辑阵列模块中您所使用的订阅名称

    - 资源组：新资源组 **az1000303-RG** 的名称

    - 位置：您在本逻辑阵列模块的前几项练习中选择的 Azure 区域

    - 可用性区域： **无**

    - 用户名称： **学生**

    - 密码： **Pa55w.rd1234**

    - 实例数： **1**

    - 实例大小： **DS1 v2**

    - 部署为低优先级： **否**

    - 使用托管磁盘： **是**

    - 自动扩展： **禁用**

    - 选择负载均衡选项： **负载均衡器**

    - 公共 IP 地址名称： **az1000303vmss0-ip**

    - 域名标记：输入您在上一个任务中确定的 &lt;custom-label&gt; 的值

    - 虚拟网络：新虚拟网络的名称 **az1000303-vnet0**，设置如下：

        - 地址空间： **10.203.0.0/16**

        - 子域名称： **subnet0**

        - 子网地址范围： **10.203.0.0/24**

    - 每个实例的公共 IP 地址： **关**

   > **注**：在继续执行下一个任务前，请等待部署完成。该操作需约 5 分钟。


#### 任务 3：使用 DSC 扩展，在规模集 VM 上安装 IIS

1. 在 Azure 门户中，请导航到 **az1000303vmss0** 边栏选项卡。

1. 从 **az1000303vmss0** 边栏选项卡，显示其扩展边栏选项卡。

1. 从 **az1000303vmss0-扩展** 边栏选项卡，使用以下设置添加 **PowerShell Desired State Configuration** 扩展：

   > **注**：DSC 配置模块可从 **Labfiles\\AZ100\\Mod03\\az-100-03_install_iis_vmss.zip** 上传。该模块包含安装 Web 服务器（IIS）角色的 DSC 配置脚本。

    - 配置模块或脚本： **"az-100-03_install_iis_vmss.zip"**

    - 模块限定的配置名称： **az-100-03_install_iis_vmss.ps1\IISInstall**

    - 配置参数：留空

    - 配置数据 PSD1 文件：留空

    - WMF 版本： **最新**

    - 数据收集: **禁用**

    - 版本： **2.76**

    - 自动升级次要版本： **是**

1. 导航到 **az1000303vmss0-实例** 边栏选项卡，启动 **az1000303vmss0_0** 实例的升级。

   > **注**：更新将触发 DSC 配置脚本的应用。等待升级完成。该操作需约 5 分钟。您可以从 az1000303vmss0-实例边栏监控进度。 

1. 升级完成后，导航到 **az1000303vmss0-ip** 边栏选项卡。 

1. 在 **az1000303vmss0-ip** 边栏选项卡上，记下分配给 **az1000303vmss0** 的公共 IP 地址。

1. 启动 Microsoft Edge 并导航到您在上一步中确定的公共 IP 地址。

1. 验证浏览器是否显示默认的 IIS 主页。 

> **结果**：完成本次练习后，您已使用 DSC 扩展为 Azure VM 规模集部署确定了可用的 DNS 名称，部署了 Azure VM 规模集，并在规模集 VM 上安装了 IIS。
