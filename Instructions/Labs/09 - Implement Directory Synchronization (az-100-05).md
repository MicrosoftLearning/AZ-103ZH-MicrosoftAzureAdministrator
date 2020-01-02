---
lab:
    title: '实现目录同步'
    module: '模块 09 - Azure Active Directory'
---

# 实验：“实现目录同步”
  
本实验室中的所有任务均从 Azure 门户（包括 PowerShell Cloud Shell 会话）执行，任务 1 练习 3、任务 2 练习 3 和任务 3 练习 3 除外，其中包括从远程桌面会话到 Azure VM 执行的步骤

   > **注意**：不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件：无

### 场景
  
Adatum 公司希望将其活动目录 （Active Directory）与 Azure Active Directory 集成


### 目标
  
 完成本实验室后，你将能够：

- 部署托管 Active Directory 域控制器的 Azure VM

- 创建和配置 Azure Active Directory 租户

- 将 Active Directory 林与 Azure Active Directory 租户同步


### 练习 1：部署托管 Active Directory 域控制器的 Azure VM

本次练习的主要任务如下：

1. 确定 Azure VM 部署的可用 DNS 名称

1. 使用 Azure 资源管理器模板，部署可托管 Active Directory 域控制器的 Azure VM


#### 任务 1：识别 Azure VM 部署的可用 DNS 名称

1. 在实验室虚拟机中启动 Microsoft Edge，浏览位于 [**http://portal.azure.com**](http://portal.azure.com) 的到 Azure 门户并使用 Microsoft 帐户登录。该帐户在您打算在此实验室中使用的 Azure 订阅中具有所有者角色，并且是与该订阅关联的 Azure AD 租户的全局管理员。

1. 从 Azure 门户，在“Cloud Shell”窗格中启动 PowerShell 会话。

   > **备注**：如果这是您第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求您创建 Azure 文件共享以保留 Cloud Shell 文件。此时，请接受默认设置，这将在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令，将占位符 &lt; custom-label &gt; 替换为任何可能唯一的字符串，将占位符 &lt; location &gt; 替换为要在其中部署将承载 Active Directory 域控制器的 Azure VM 的 Azure 区域的名称。

   > **注意**：若要标识可在其中配置 Azure VM 的 Azure 区域，请参阅[**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

```pwsh
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
```

1. 验证命令是否返回**True**。如果不是，请使用不同的 &lt;custom-label&gt; 值重新运行相同的命令，直到命令返回**“True”**。 

1. 记下生成成功结果的 &lt;custom-label&gt; 值。你将在下一个任务中需要它


#### 任务 2：使用 Azure 资源管理器模板，部署可托管 Active Directory 域控制器的 Azure VM

1. 在实验虚拟机中，启动另一个 Microsoft Edge 实例，浏览到 GitHub Azure 快速启动模板页面 [**https://github.com/Azure/azure-quickstart-templates**](https://github.com/Azure/azure-quickstart-templates)。

1. 在“Azure 快速启动模板”页上，单击**“active-directory-new-domain”**。

1. 在**“创建新的 Windows VM 并创建新的 AD 林、域和 DC”**页上，右键单击**“部署到 Azure”**，然后单击**“在新的选项卡中打开”**。

1. 在 **使用新的 AD 林创建 Azure VM** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：你在此实验室中使用的订阅的名称

    - 资源组：新资源组名称**az1000501-RG**

    - 位置：您在上一个任务中使用的 Azure 区域名称

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

    - 域名: **adatum.com**

    - Dns 前缀：您在上一个任务中标识的&lt;custom-label&gt;
    
    - VM 大小：**Standard_D2s_v3**

    - _artifacts 位置：接受默认值

    - _artifacts 位置 Sas Token：留空

    - 位置：接受默认值

   > **注意**：无需等待部署完成即可继续进行下一项练习。在本实验室的第三个练习中，您将使用此任务中部署的虚拟机。

> **结果**：完成此练习后，您已经启动了Azure VM 的部署，该 Azure VM 将使用 Azure 资源管理器模板来托管Active Directory 域控制器


### 练习 2：创建和配置 Azure Active Directory 租户

本练习的主要任务如下：

1. 创建 Azure Active Directory（AD）租户

1. 将自定义 DNS 名称添加到新的 Azure AD 租户

1. 使用全局管理员角色创建 Azure AD 用户


#### 任务 1：创建 Azure Active Directory（AD）租户

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。 

1. 从 **创建资源** 边栏选项卡，在 Azure Marketplace 搜索 **Azure Active Directory**。

1. 使用搜索结果列表导航到 **创建目录** 边栏选项卡。

1. 从 **创建目录**边栏选项卡，创建一个新 Azure AD 租户，设置如下： 

  - 组织名称：**AdatumSync**

  - 初始域名：由字母和数字组合组成的唯一名称。 

  - 国家或地区：**美国**

   > **备注**：**“初始域名”**文本框中的绿色复选标记将指示您键入的域名是否有效且唯一。 


#### 任务 2：将自定义 DNS 名称添加到新的 Azure AD 租户
  
1. 在 Azure 门户中，将 **目录+订阅**筛选器设置为新创建的 Azure AD 租户。

   > **注意**：该 **目录+订阅** 筛选器显示在 Azure 门户工具栏的通知图标的左侧 

   > **注意**：如果 **AdatumSync**条目未出现在**目录 +订阅** 筛选器列表中，则需刷新浏览器窗口。

1. 在 Azure 门户中，导航到 **AdatumSync - 概述** 边栏选项卡。

1. 从 **AdatumSync - 概述** 边栏选项卡，显示 **AdatumSync - 自定义域名** 边栏选项卡。 

1. 在 **AdatumSync - 自定义域名** 边栏选项卡，识别与 Azure AD 租户关联的主要默认 DNS 域名。注意其值 - 在下一个任务中将需要它。

1. 从 **AdatumSync - 自定义域名** 边栏选项卡，添加 **adatum.com** 自定义域名。

1. 在 **adatum.com** 边栏选项卡上，查看执行 Azure AD 域名验证所需的信息。

   > **注意**：因无 **adatum.com** DNS 域名，您将无法完成验证过程。这不会阻止您将**adatum.com** Active Directory 域与 Azure AD 租户同步。为此，您将使用 Azure AD 租户的默认主 DNS 名称（其名称以 **onmicrosoft.com** 后缀结尾），该名称在此任务的前期已确定。但是，请记住，因此，Active Directory 域的 DNS 域名和 Azure AD 租户的 DNS 名称将有所不同。这意味着 Adatum 用户在登录 Active Directory 域和登录 Azure AD 租户时需要使用不同的名称。


#### 任务 3：使用全局管理员角色创建 Azure AD 用户

1. 在 Azure 门户中，导航到 **AdatumSync** Azure AD 租户的 **用户 - 所有用户** 边栏选项卡。

1. 在**用户 - 所有用户**边栏选项卡中，使用以下设置创建新用户：

     - 用户名称：**syncadmin @***<DNS-domain-name>*其中*<DNS-domain-name>*则表示您在上一个任务中确定的默认主 DNS 域名。记下此用户名。您将在本实验室的后面需要它。
    
    - 名称： **syncadmin**
    
    - 密码：选中复选框 **显示密码**，并注意出现在 **密码** 文本框中的字符串。您将在此任务中稍后使用它。
    
    - 群组：**选定了 0 个组**

    - 目录角色：单击“用户”并选择“**全局管理员**”

   > **备注**：为实现 Azure AD Connect，需要具有全局管理员角色的 Azure AD 用户。

1. 打开 InPrivate Microsoft Edge 窗口。

1. 在新的浏览器窗口中，导航到 Azure 门户并使用 **syncadmin** 登录用户帐户。出现提示时，将密码更改为新值。

   > **备注**：您需要提供 **syncadmin** 用户帐户的完全限定名称，包括 Azure AD 租户 DNS 域名。 

1. 以**syncadmin**用户名注销并关闭 InPrivate 浏览器窗口。

> **结果**：完成此练习后，你已创建 Azure AD 租户，为新的 Azure AD 租户添加了自定义 DNS 名称，并创建了具有全局管理员角色的 Azure AD 用户。


### 练习 3：将 Active Directory 林与 Azure Active Directory 租户同步

本练习的主要任务如下：

1. 配置 Active Directory 以准备目录同步

1. 安装 Azure AD Connect

1. 验证目录同步


#### 任务 1：配置 Active Directory 以准备目录同步

   > **注意**：开始此项任务之前，请确保您在练习 1 中启动的模板部署已经完成。
  
1. 在 Azure 门户中，将 **目录+订阅** 筛选器设置回与本实验室第一个练习中使用的 Azure 订阅关联的 Azure AD 租户。

   > **注意**：该 **目录+订阅** 筛选器显示在 Azure 门户工具栏的通知图标的左侧 

1. 在 Azure 门户中，导航到 **adVM**边栏选项卡，显示托管在本实验室第一个练习中部署的Active Directory 域控制器的 Azure VM 属性。

1. 在“**adVM**”边栏选项卡的“**概述**”窗格上，单击“连接”。 

1. 在“**连接到虚拟机**”边栏选项卡上，在“**IP 地址**”下拉列表中选择负载均衡器公共 IP 地址，下载相应 RDP 文件，然后使用它连接到“**adVM**”。

1. 出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称：**学生**

    - 密码：**Pa55w.rd1234**

1. 在与**adVM**的远程桌面会话中，**打开 Active Directory 管理中心**。

1. 在 **Active Directory 管理中心**，创建一个名为**ToSync**的根级组织单位。


1. 在 **Active Directory 管理中心**组织单元 **ToSync**内，使用以下设置创建新用户帐户：

    - 全称： **aduser1**

    - 用户 UPN 登录： **aduser1@adatum.com**

    - 用户 SamAccountName 登录：**adatum\aduser1**

    - 密码：**Pa55w.rd1234**

    - 其他密码选项：**密码永不过期**


#### 任务 2：安装 Azure AD Connect

1. 在与**adVM**的 RDP 会话中，从服务器管理器暂时禁用 **IE 增强安全配置**。

1. 在与**adVM**的 RDP 会话中，启动 Internet Explorer 并在 [**https://www.microsoft.com/zh-cn/download/details.aspx?id=47594**](https://www.microsoft.com/zh-cn/download/details.aspx?id=47594)下载 **Azure AD Connect**

1. 启动**Microsoft Azure Active Directory Connect**向导，接受许可条款，然后在**快速设置**页面上，选择**自定义**选项。

1. 在 **安装所需组件** 页面上，取消选择所有可选配置选项并开始安装。

1. 在 **用户登录** 页面上，确保仅启用 **密码哈希同步**。

1. 当系统提示连接到 Azure AD 时，请使用您在上一个练习中创建的**syncadmin**帐户凭据进行身份验证。

1. 当提示您连接目录时，请添加 **adatum.com** 林，选择 **创建新的 AD 帐户**选项，并使用以下凭据进行身份验证：

    - 用户名称：**ADATUM\\学员**

    - 密码：**Pa55w.rd1234**

1. 在 **Azure AD 登录配置** 页面上，请注意警告说明。**如果 UPN 后缀与已验证的域名不匹配，则用户将无法使用内部部署凭据登录 Azure AD，**并启用该复选框**继续，不将所有 UPN 后缀与已验证域匹配**。

   > **注意**：如前所述，这是正常现象，因为您无法验证自定义 Azure AD DNS 域 **adatum.com**。

1. 在 **域和 OU 筛选** 页面上，确保仅选中 **ToSync** OU。

1. 在 **独特地识别您的用户** 页面上，接受默认设置。

1. 在 **筛选用户和设备**页面上，接受默认设置。

1. 在 **可选功能**页面上，接受默认设置。

1. 在 **准备配置** 页面，确保 **配置完成后启动同步过程** 选中复选框并继续安装过程。 

   > **注意**：安装需要约 2 分钟。

1. 配置完成后，关闭 Microsoft Azure Active Directory Connect 窗口。


#### 任务 3：验证目录同步

1. 在与 **adVM**的 RDP 会话中，启动 Internet Explorer，浏览 Azure 门户网站 [**http://portal.azure.com**](http://portal.azure.com)，然后使用您在上一个练习中创建的 **syncadmin**帐户登录。 

1. 在 Azure 门户中，导航到 **AdatumSync - 概述** 边栏选项卡。

1. 在**AdatumSync - Overview**边栏选项卡中, 显示 AdatumSync Azure AD 租户的**用户 - 所有用户**边栏选项卡。

1. 在 **用户 - 所有用户** 边栏选项卡中，请注意用户对象列表包括 **aduser1** 帐户，与出现在 **资源** 柱中的 **Windows Server AD**。

1. 在 **用户 - 所有用户** 边栏选项卡中，显示 **aduser1 - 预置文件** 边栏选项卡。请注意 **部门** 属性未设置。

1. 在与**adVM**的RDP会话中，切换到 **Active Directory 管理中心**，打开显示 **aduser1** 用户帐户属性的窗口，并设置其值 **部门** 属性为 **销售**。

1. 在与**adVM**的 RDP 会话中，启动 **Windows PowerShell** 作为管理员。

1. 在 Windows PowerShell 提示符下，通过运行以下命令启动 Azure AD Connect delta 同步：

```pwsh
   Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'
   
   Start-ADSyncSyncCycle -PolicyType Delta
```

1. 在与**adVM**的 RDP 会话中，切换到显示 Azure 门户的 Internet Explorer 窗口。 

1. 在 Azure 门户中，导航到 **用户 - 所有用户** 边栏选项卡并刷新页面。 

1. 在 **用户 - 所有用户** 边栏选项卡中，显示 **aduser1 - 预置文件** 边栏选项卡。请注意， **部门**属性设置为**销售**。

   > **注意**：可能需要等待一分钟，如 **部门** 属性仍未设置请再次刷新页面。

> **结果**：完成本次练习后，你已配置 Active Directory 以准备目录同步，已安装 Azure AD Connect，并已验证目录同步。

## 练习 4：移除实验室资源

#### 任务1：删除 Azure AD 租户。

1. 在与**adVM**的 RDP 会话中，启动 Windows PowerShell 作为管理员。 

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装 MsOnline PowerShell 模块（在出现提示时，在“需要 NuGet 提供程序才能继续”对话框中，单击**“是”**）：

```pwsh
   Install-Module MsOnline -Force
```
   
1. 从 Windows PowerShell 控制台，通过运行以下命令连接到 AdatumSync Azure AD 租户（在出现提示时，使用 SyncAdmin 凭据登录）：

```pwsh
   Connect-MsolService
```

1. 从 Windows PowerShell 控制台中，通过运行以下命令禁用 Azure AD Connect 同步：

```pwsh
   Set-MsolDirSyncEnabled -EnableDirSync $false -Force
```

1. 从 Windows PowerShell 控制台中，通过运行以下命令来验证操作是否成功：

```pwsh
   (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled 
```   

1. 从 Azure 门户注销并关闭 Internet Explorer 窗口。 

1. 启动 Internet Explorer，导航到 Azure 门户，然后使用 SyncAdmin 凭据登录。 

1. 在 Azure 门户中，导航到 AdatumSync Azure AD 租户的**“用户 - 所有用户”**边栏选项卡，并删除除 AdatumSync 帐户之外的所有用户。

> **备注**：你可能需要等待几个小时才能完成此步骤。

1. 导航到“AdatumSync - 概述”边栏选项卡，然后单击**“属性”**。

1. 在 Azure Active Directory 的**“属性”**边栏选项卡上，在**“Azure 资源的访问管理”**部分中单击**“是”**，然后单击**“保存”**。

1. 从 Azure 门户注销，然后使用 SyncAdmin 凭据重新登录。 

1. 导航到**“AdatumSync - 概述”**边栏选项卡，并通过单击**“删除目录”**删除 Azure AD 租户。

1. 在**“删除目录 "AdatumSync"?”**边栏选项卡上，单击**“删除”**。

> **备注**：有关此任务的任何其他信息，请参阅 https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto  

#### 任务2：打开 Cloud Shell

1. 在门户顶部，单击**“Cloud Shell”**图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择**“Bash”**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键列出你在本实验室中创建的所有资源组：

```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv
```

1. 验证输出中是否仅包含在本练习中创建的资源组。将在下一个任务中删除这些资源组。

#### 任务3：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter** 键以删除你在本实验室中创建的资源组

```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **结果**：在本练习中，将删除本实验室中使用的资源。
