---
逻辑阵列模块：
    标题：‘配置 Azure DNS’
    模块：‘虚拟网络’
---

# 逻辑阵列模块：配置 Azure DNS
  
本逻辑阵列模块中的所有任务都是从 Azure 门户执行的（包括 PowerShell Cloud Shell 会话） 

   > **注**：不使用 Cloud Shell 时，逻辑阵列模块虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） https://docs.Microsoft.com/zh-cn/powershell/ Azure /install-az-ps?view=azps-1.2.0

逻辑阵列模块文件： 

-  **Allfiles/Labfiles/AZ-100.4/az-100-04b_01_ Azure deploy.json**

-  **Allfiles/Labfiles/AZ-100.4/az-100-04b_02_azuredeploy.json**

-  **Allfiles/Labfiles/AZ-100.4/az-100-04_ Azure deploy.parameters.json**


### 方案
  
Adatum Corporation 希望在 Azure 中实现公共和私人 DNS 服务，而无需部署其自身 DNS 服务器。 


### 目标
  
完成本逻辑阵列模块后，您将能够：

- 为公共域配置 Azure DNS

- 为私有域配置 Azure DNS


### 练习 1：为公共域配置 Azure DNS

本次练习的主要任务如下：

1. 创建公共 DNS 区域

1. 在公共 DNS 区域中创建 DNS 记录

1. 验证公共域的基于 Azure DNS 的名称解析


#### 任务 1：创建公共 DNS 区域
  
1. 从逻辑阵列模块虚拟机启动 Microsoft Edge，在 **http://portal.Azure.com** 上浏览至 Azure 门户，并使用在您计划用于本次逻辑阵列模块的 Azure 订阅中具有“所有者”角色的 Microsoft 帐户进行登录。

1. 在 Azure 门户中，请导航到“**新建**”边栏选项卡。

1. 从“**新建**”边栏选项卡，搜索 Azure 市场中的 **DNS 区域**。

1. 请使用搜索结果列表，导航到“**创建 DNS 区域**”边栏选项卡。

1. 从“**创建 DNS 区域**”边栏选项卡，创建新的 DNS 区域，设置如下： 

    - 名称：**com** 命名空间中任何唯一、有效的 DNS 域名

    - 订阅：在本次逻辑阵列模块中您所使用的 Azure 订阅名称

    - 资源组：新资源组 **az1000401b-RG** 的名称

    - 资源组位置：最靠近逻辑阵列模块位置的 Azure 区域名称并且您可以在其中设置 Azure DNS 区域


#### 任务 2：在公共 DNS 区域中创建 DNS 记录
  
1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

   > **注**：如果这是您第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求您创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令以标识逻辑阵列模块计算机的公共 IP 地址：

   ```
   Invoke-RestMethod http://ipinfo.io/json | Select-Object -ExpandProperty IP
   ```

   > **注**：记下该 IP 地址。在本任务中，您后续将需要用到该地址。

1. 在 Cloud Shell 窗格中，运行以下命令以创建公共 IP 地址资源：

   ```
   $rg=Get-AzResourceGroup -Name az1000401b-RG

   New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Sku Basic -AllocationMethod Static -Name az1000401b-pip -Location $rg.Location
   ```

1. 在 Azure 门户中，导航到 **az1000401b-RG** 资源组边栏选项卡。

1. 从 **az1000401b-RG** 资源组边栏选项卡，导航到显示新创建公共 DNS 区域的边栏选项卡。

1. 从 DNS 区域边栏选项卡，导航到“**添加记录集**”边栏并创建 DNS 记录，设置如下：

    - 名称： **mylabvmpip**

    - 类型： **A**

    - 别名记录集: **否**

    - TTL： **1**

    - TTL 单位： **小时**

    - IP 地址：您在此任务中先前确定的逻辑阵列模块计算机的公共 IP 地址

1. 从“**添加记录集**”边栏选项卡，创建另一条记录，设置如下：

    - 名称： **my Azure pip**

    - 类型： **A**

    - 别名记录集: **是**

    - 别名类型： **Azure 资源**

    - 选择订阅：您希望用于本逻辑阵列模块的 Azure 订阅名称。

    - Azure 资源： **az1000401b-pip**

    - TTL： **1**

    - TTL 单位： **小时**


#### 任务 3：验证公共域的基于 Azure DNS 的名称解析

1. 在 DNS 区域边栏选项卡上，记下承载您创建区域的名称服务器列表。您将使用在下一步中命名的第一个。

1. 从逻辑阵列模块虚拟机，启动命令提示符并运行以下命令以验证两个新创建 DNS 记录的名称解析（其中，&lt；custom_DNS_domain&gt；表示您在本次练习的第一个任务中创建的自定义 DNS 域，并且&lt；name_server&gt；表示您在上一步中确定的 DNS 名称服务器的名称）： 

   ```
   nslookup mylabvmpip.<custom_DNS_domain> <name_server>
   
   nslookup my Azure pip.<custom_DNS_domain> <name_server>
   ```

1. 验证返回的 IP 地址是否与您在此任务中先前确定的 IP 地址相匹配。

> **结果**：完成此练习后，您已创建公共 DNS 区域，在公共 DNS 区域中创建 DNS 记录，并验证公共域的基于 Azure DNS 的名称解析。


### 练习 2：为私有域配置 Azure DNS
  
本次练习的主要任务如下：

1. 提供多虚拟网络环境

1. 创建私人 DNS 区域

1. 将 Azure VM 部署至虚拟网络中

1. 验证专用域的基于 Azure DNS 的名称保留和解析


#### 任务 1：提供多虚拟网络环境
  
1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

1. 在 Cloud Shell 窗格中，运行以下命令以创建资源组：

   ```
   $rg1=Get-AzResourceGroup -Name 'az1000401b-RG'

   $rg2=New-AzResourceGroup -Name 'az1000402b-RG' -Location $rg1.Location
   ```

1. 在 Cloud Shell 窗格中，运行以下命令以创建两个 Azure 虚拟网络：

   ```
   $subnet1=New-AzVirtualNetworkSubnetConfig -Name subnet1-AddressPrefix'10.104.0.0/24'

   $vnet1=New-AzVirtualNetwork -ResourceGroupName $rg2.ResourceGroupName -Location $rg2.Location -Name az1000402b-vnet1 -AddressPrefix 10.104.0.0/16 -Subnet $subnet1

   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name subnet1 -AddressPrefix '10.204.0.0/24'

   $vnet2=New-AzVirtualNetwork -ResourceGroupName $rg2.ResourceGroupName -Location $rg2.Location -Name az1000402b-vnet2 -AddressPrefix 10.204.0.0/16 -Subnet $subnet2
   ```

#### 任务 2：创建私人 DNS 区域

1. 在 Cloud Shell 窗格中，运行以下命令以创建私人 DNS 区域，其中第一个虚拟网络支持注册，第二个虚拟网络支持解析：

   ```
   New-Az DNS Zone -Name adatum.local -ResourceGroupName $rg2.ResourceGroupName -ZoneType Private -RegistrationVirtualNetworkId @（$vnet1.Id）-ResolutionVirtualNetworkId @（$vnet2.Id）
   ```

   > **注**：分配给 Azure DNS 区域的虚拟网络不能包含任何资源。

1. 在 Cloud Shell 窗格中，运行以下命令以验证是否已成功创建私人 DNS 区域：

   ```
   Get-Az DNS Zone-ResourceGroupName $rg2.ResourceGroupName
   ```


#### 任务 3：将 Azure VM 部署至虚拟网络中

1. 在 Cloud Shell 窗格中，上传 **az-100-04b_01_Azure deploy.json**、**az-100-04b_02_Azure deploy.json** 和 **az-100-04_Azure deploy.parameters.json** 文件

1. 在 Cloud Shell 窗格中，运行以下命令，以将 Azure VM 部署至第一个虚拟网络中：

   ```
   New-AzResourceGroupDeployment -ResourceGroupName $rg2.ResourceGroupName -TemplateFile "$home/az-100-04b_01_Azure deploy.json" -TemplateParameterFile "$home/az-100-04_Azure deploy.parameters.json" -AsJob
   ```

1. 在 Cloud Shell 窗格中，运行以下命令，以将 Azure VM 部署至第二个虚拟网络中：

   ```
   New-AzResourceGroupDeployment -ResourceGroupName $rg2.ResourceGroupName -TemplateFile "$home/az-100-04b_02_azuredeploy.json" -TemplateParameterFile "$home/az-100-04_azuredeploy.parameters.json" -AsJob
   ```

   > **注**：在继续执行下一个任务前，请等待两个部署完成。您可通过在 Cloud Shell 窗格中运行`Get-Job` cmdlet 来识别工作的状态。


#### 任务 4：验证专用域的基于 Azure DNS 的名称保留和解析

1. 在 Azure 门户中，导航到该边栏的 **az1000402b-vm2** Azure VM . 

1. 来自“**概述**”窗格的 **az1000402b-vm2** 边栏选项卡，生成 RDP 文件并使用它连接至 **az1000402b-vm2**。

1. 出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称： **学生**

    - 密码： **Pa55w.rd1234**

1. 在 **az1000402b-vm2** 远程桌面会话中，启动命令提示符窗口并运行以下命令： 

   ```
   nslookup az1000402b-vm1.adatum.local
   ```

1. 验证该名称是否已成功被解析。

1. 切换至逻辑阵列模块虚拟机，并在 Azure 门户窗口的 Cloud Shell 窗格中，在私人 DNS 区域中运行以下命令，以创建其他 DNS 记录：

   ```
   New-Az DNS RecordSet-ResourceGroupName $rg2.ResourceGroupName -Name www -RecordType A -ZoneName adatum.local -Ttl 3600 - DNS Records（New-Az DNS RecordConfig -IPv4Address "10.104.0.4"）
   ```

1. 再次切换至 **az1000402b-vm2** 远程桌面会话，并在命令提示符窗口运行以下命令： 

   ```
   nslookup www.adatum.local
   ```

1. 验证该名称是否已成功被解析。

> **结果**：完成此操作后，您已配置多虚拟网络环境，创建私人 DNS 区域，将 Azure VM 部署至虚拟网络中，并验证基于 Azure DNS 的名称保留和专用域解析
