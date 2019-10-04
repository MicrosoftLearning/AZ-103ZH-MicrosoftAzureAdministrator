---
lab:
    title: “配置 Azure DNS”
    module: “模块 04 - 虚拟网络”
---

# 实验室：配置 Azure DNS
  
本实验室中的所有任务都是在 Azure 门户中执行的（包括 PowerShell Cloud Shell 会话） 

   > **注意**：不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本）[https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件： 

-  **Labfiles\\Module_04\\Configure_Azure_DNS\\az-100-04b_01_azuredeploy.json**

-  **Labfiles\\Module_04\\Configure_Azure_DNS\\az-100-04b_02_azuredeploy.json**

-  **Labfiles\\Module_04\\Configure_Azure_DNS\\az-100-04_azuredeploy.parameters.json**


### 场景
  
Adatum Corporation 希望在 Azure 中实现公共和专用 DNS 服务，而无需部署自己的 DNS 服务器。 


### 目标
  
完成本实验室后，你将能够：

- 为公共域配置 Azure DNS

- 为专用域配置 Azure DNS


### 练习 1：为公共域配置 Azure DNS

本练习的主要任务如下：

1. 创建公共 DNS 区域

1. 在公共 DNS 区域中创建 DNS 记录

1. 验证公共域的基于 Azure DNS 的名称解析


#### 任务 1：创建公共 DNS 区域
  
1. 从实验室虚拟机启动 Microsoft Edge，浏览到 Azure 门户 ([**http://portal.azure.com**](http://portal.azure.com))，然后使用 Microsoft 帐户登录，该帐户具有要在本实验室中使用的 Azure 订阅的所有者角色。

1. 在 Azure 门户中，导航到 **“创建资源”** 边栏选项卡。

1. 在 **“创建资源”** 边栏选项卡中，在 Azure 市场中搜索 **“DNS 区域”**。

1. 选择 **“DNS 区域”**，然后单击 **“创建”**。

1. 在 **“创建 DNS 区域”** 边栏选项卡中，使用以下设置创建新的 **DNS 区域**： 

    - 订阅：用于本实验室的 Azure 订阅名称

    - 资源组：新资源组 **az1000401b-RG** 的名称

    - 名称：**com** 命名空间中任意唯一且有效的 DNS 域名

    - 资源组位置：**美国东部**（或你附近受支持的区域）


#### 任务 2：在公共 DNS 区域中创建 DNS 记录
  
1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

   > **注意**：如果这是你第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求你创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，请接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令以确定实验计算机的公共 IP 地址：

   ```pwsh
   Invoke-RestMethod http://ipinfo.io/json | Select-Object -ExpandProperty IP
   ```

   > **注意**：记下该 IP 地址。本任务稍后会用到该地址。

1. 在 Cloud Shell 窗格中，运行以下命令以创建公共 IP 地址资源：

   ```pwsh
   $rg = Get-AzResourceGroup -Name az1000401b-RG

   New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Sku Basic -AllocationMethod Static -Name az1000401b-pip -Location $rg.Location
   ```

1. 在 Azure 门户中，导航到 **az1000401b-RG** 资源组边栏选项卡。

1. 从 **az1000401b-RG** 资源组边栏选项卡，导航到显示新创建公共 DNS 区域的边栏选项卡。

1. 在“DNS 区域”边栏选项卡中，单击 **“记录集”** 以导航到 **“添加记录集”** 边栏选项卡

1. 使用以下设置创建 DNS 记录：

    - 名称：**mylabvmpip**

    - 类型：**A**

    - 别名记录集：**否**

    - TTL： **1**

    - TTL 单位：**小时**

    - IP 地址：先前在此任务中确定的实验室计算机的公共 IP 地址

1. 在“概述”边栏选项卡中，单击 **“记录集”**，然后使用以下设置创建其他记录：

    - 名称：**myazurepip**

    - 类型：**A**

    - 别名记录集：**是**

    - 别名类型：**Azure 资源**

    - 选择订阅：用于本实验室中的 Azure 订阅名称

    - Azure 资源：**az1000401b-pip**

    - TTL：**1**

    - TTL 单位：**小时**


#### 任务 3：验证公共域的基于 Azure DNS 的名称解析

1. 在“DNS 区域”边栏选项卡上，记下托管所创建的区域的名称服务器列表。你将使用在下一步中命名的第一个名称服务器。

1. 在实验虚拟机中，启动命令提示符并运行以下命令，验证两个新创建 DNS 记录的名称解析（其中，&lt;custom_DNS_domain&gt; 表示在本练习的第一个任务中创建的自定义 DNS 域，而 &lt;name_server&gt; 表示在上一步中确定的 DNS 名称服务器的名称）： 

   ```
   nslookup mylabvmpip.<custom_DNS_domain> <name_server>
   
   nslookup myazurepip.<custom_DNS_domain> <name_server>
   ```

1. 验证返回的 IP 地址是否与你之前在此任务中确定的 IP 地址相匹配。

> **结果**：完成本练习后，你已创建了公共 DNS 区域，在公共 DNS 区域中创建了 DNS 记录，并验证了公共域的基于 Azure DNS 的名称解析。


### 练习 2：为专用域配置 Azure DNS
  
本练习的主要任务如下：

1. 预配多虚拟网络环境

1. 创建专用 DNS 区域

1. 将 Azure VM 部署至虚拟网络中

1. 验证私有域的基于 Azure DNS 的名称预留和解析


#### 任务 1：预配多虚拟网络环境
  
1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

1. 在 Cloud Shell 窗格中，运行以下命令以创建资源组：

   ```pwsh
   $rg1 = Get-AzResourceGroup -Name 'az1000401b-RG'

   $rg2 = New-AzResourceGroup -Name 'az1000402b-RG' -Location $rg1.Location
   ```

1. 在 Cloud Shell 窗格中，运行以下命令以创建两个 Azure 虚拟网络：

   ```pwsh
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name subnet1 -AddressPrefix '10.104.0.0/24'

   $vnet1 = New-AzVirtualNetwork -ResourceGroupName $rg2.ResourceGroupName -Location $rg2.Location -Name az1000402b-vnet1 -AddressPrefix 10.104.0.0/16 -Subnet $subnet1

   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name subnet1 -AddressPrefix '10.204.0.0/24'

   $vnet2 = New-AzVirtualNetwork -ResourceGroupName $rg2.ResourceGroupName -Location $rg2.Location -Name az1000402b-vnet2 -AddressPrefix 10.204.0.0/16 -Subnet $subnet2
   ```

#### 任务 2：创建专用 DNS 区域

1. 在 Cloud Shell 窗格中，运行以下命令以创建专用 DNS 区域，其中第一个虚拟网络支持注册，而第二个虚拟网络支持解析：

   ```pwsh
   $vnet1 = Get-AzVirtualNetwork -Name az1000402b-vnet1

   $vnet2 = Get-AzVirtualNetwork -name az1000402b-vnet2

   New-AzDnsZone -Name adatum.local -ResourceGroupName $rg2.ResourceGroupName -ZoneType Private -RegistrationVirtualNetworkId @($vnet1.Id) -ResolutionVirtualNetworkId @($vnet2.Id)
   ```

   > **注意**：分配给 Azure DNS 区域的虚拟网络不能包含任何资源。

1. 在 Cloud Shell 窗格中，运行以下命令以验证是否已成功创建专用 DNS 区域：

   ```pwsh
   Get-AzDnsZone -ResourceGroupName $rg2.ResourceGroupName
   ```


#### 任务 3：将 Azure VM 部署至虚拟网络中

1. 在 Cloud Shell 窗格中，上传 **az-100-04b_01_azuredeploy.json**、**az-100-04b_02_azuredeploy.json** 和 **az-100-04_azuredeploy.parameters.json 文件**。

1. 在 Cloud Shell 窗格中，运行以下命令，以将 Azure VM 部署至第一个虚拟网络中：

   ```pwsh
   cd $home

   New-AzResourceGroupDeployment -ResourceGroupName $rg2.ResourceGroupName -TemplateFile "./az-100-04b_01_azuredeploy.json" -TemplateParameterFile "./az-100-04_azuredeploy.parameters.json" -AsJob
   ```

1. 在 Cloud Shell 窗格中，运行以下命令，以将 Azure VM 部署至第二个虚拟网络中：

   ```pwsh
   New-AzResourceGroupDeployment -ResourceGroupName $rg2.ResourceGroupName -TemplateFile "./az-100-04b_02_azuredeploy.json" -TemplateParameterFile "./az-100-04_azuredeploy.parameters.json" -AsJob
   ```

   > **注意**：请等待两个部署完成，再继续执行下一个任务。你可以通过在 Cloud Shell 窗格中运行 `Get-Job` cmdlet 来确认作业状态。


#### 任务 4：验证专用域的基于 Azure DNS 的名称保留和解析

1. 在 Azure 门户中，导航到 **az1000402b-vm2** Azure VM 边栏选项卡。 

1. 在 **“概览”** 窗格的 **“az1000402b-vm2”** 边栏选项卡中，生成 RDP 文件并使用它连接至 **“az1000402b-vm2”**。

1. 系统出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称：**Student**

    - 密码： **Pa55w.rd1234**

1. 在 **az1000402b-vm2** 远程桌面会话中，启动命令提示符窗口并运行以下命令： 

   ```
   nslookup az1000402b-vm1.adatum.local
   ```

1. 验证名称是否已成功解析。

1. 切换回实验虚拟机，并在 Azure 门户窗口的 Cloud Shell 窗格中，运行以下命令以在专用 DNS 区域中创建其他 DNS 记录：

   ```pwsh
   New-AzDnsRecordSet -ResourceGroupName $rg2.ResourceGroupName -Name www -RecordType A -ZoneName adatum.local -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.104.0.4")
   ```

1. 再次切换至 **az1000402b-vm2** 远程桌面会话，并在命令提示符窗口中运行以下命令： 

   ```
   nslookup www.adatum.local
   ```

1. 验证名称是否已成功解析。

> **结果**：完成本练习后，你已配置了多虚拟网络环境，创建了专用 DNS 区域，将 Azure VM 部署至虚拟网络中，并验证了专用域的基于 Azure DNS 的名称保留和解析

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **“Cloud Shell”** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **“Bash”**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter**，列出在本实验室中创建的所有资源组：

   ```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv
   ```

1. 验证输出中是否仅包含在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter**，删除在本实验室中创建的资源组

   ```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **结果**：在本练习中，你删除了本实验室中使用的资源。
