---
lab:
    title: '实现目录同步'
    module: '模块 09 - Azure Active Directory'
---

# 实验室：实现目录同步
  
本逻辑阵列块中的所有任务都是从Azure门户（包括PowerShell Cloud Shell会话）执行的，但练习3任务1、练习3任务2和练习3任务3除外，其中包括从远程桌面会话到Azure VM执行的步骤

   > **注意**: 不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件：无

### 场景
  
Adatum Corporation希望将其活动目录与Azure Active Directory 集成


### 目标
  
 完成本实验室后，你将能够：

- 部署托管 Active Directory 域控制器的 Azure VM

- 创建和配置 Azure Active Directory 租户

- 将 Active Directory 林与 Azure Active Directory 租户同步


### 练习 1：部署托管 Active Directory 域控制器的 Azure VM

本练习的主要任务如下：

1. 确定 Azure VM 部署的可用 DNS 名称

1. 使用Azure资源管理器模板，部署可托管Active Directory域控制器的Azure VM


#### 任务 1：确定 Azure VM 部署的可用 DNS 名称

1. 从实验室虚拟机启动Microsoft Edge，浏览到Azure门户 [**http://portal.azure.com**](http://portal.azure.com) 并使用Microsoft帐户登录，该帐户在您打算在本逻辑阵列块中使用的Azure订阅中具有所有者角色，并且是与该订阅关联的Azure AD租户的全局管理员。

1. 从 Azure 门户，在 Cloud Shell 窗格中启动 PowerShell 会话。

   > **注意**: 如果这是你第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求你创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，请接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在Cloud Shell窗格中，运行以下命令，使用任何可能的唯一字符串和占位符&lt;location&gt;替换占位符&lt;custom-label&gt;  使用要在其中部署将承载Active Directory域控制器的Azure VM的Azure区域的名称。

   > **注意**: 若要标识可以预配 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   ```pwsh
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
   ```

1. 验证命令是否返回了 **True**。如果没有，请使用&lt;custom-label&gt; 的不同值重新运行相同的命令，直到命令返回 **True**。 

1. 请注意导致成功结果的&lt;custom-label&gt;的值。您将在下一个任务中使用它


#### 任务 2：使用Azure资源管理器模板，部署可托管Active Directory域控制器的Azure VM

1. 在实验虚拟机中，启动另一个 Microsoft Edge 实例，浏览到 GitHub Azure 快速启动模板页面 [**https://github.com/Azure/azure-quickstart-templates**](https://github.com/Azure/azure-quickstart-templates)。

1. 在“Azure 快速启动模板”页上，单击 **active-directory-new-domain**。

1. 在 **创建新的 Windows VM 并创建新的 AD 林、域和 DC** 页上，右键单击 **部署到 Azure**，然后单击 **在新的选项卡中打开**。

1. 在 **使用新的 AD 林创建 Azure VM** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1000501-RG** 的名称

    - 位置：您在上一个任务中使用的Azure区域名称

    - 管理员用户名：**Student**

    - 管理员密码：**Pa55w.rd1234**

    - 域名: **adatum.com**

    - Dns前缀：您在上一个任务中标识的&lt;custom-label&gt;
    
    - VM 大小：**Standard_D2s_v3**

    - _artifacts 位置：接受默认值

    - _artifacts 位置 SAS 令牌：留空

    - 位置：接受默认值

   > **注意**: 不必等待部署完成，请继续进行下一项练习。您将在本逻辑阵列块的第三个练习中使用此任务中部署的虚拟机。

> **结果:** 完成此练习后，您已使用Azure资源管理器模板启动了将托管Active Directory域控制器的Azure VM的部署


### 练习 2：创建和配置 Azure Active Directory 租户

本练习的主要任务如下：

1. 创建 Azure Active Directory (AD) 租户

1. 将自定义DNS名称添加到新的Azure AD租户

1. 使用全局管理员角色创建Azure AD用户


#### 任务 1：创建 Azure Active Directory (AD) 租户

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。 

1. 从 **创建资源** 边栏选项卡，在Azure应用市场内搜索 **Azure Active Directory**。

1. 使用搜索结果列表导航到 **创建目录** 边栏选项卡。

1. 在 **创建目录** 边栏选项卡中，使用以下设置创建新的 Azure AD 租户： 

  - 组织名称：**AdatumSync**

  - 初始域名：由字母和数字组合组成的唯一名称。 

  - 国家或地区：**美国**

   > **注意**：**初始域名** 文本框中的绿色复选标记将指示您键入的域名是否有效且唯一。 


#### 任务 2：将自定义DNS名称添加到新的Azure AD租户
  
1. 在Azure门户中，设置 **目录+订阅** 过滤器到新创建的Azure AD租户。

   > **注意**: 该 **目录+订阅** 过滤器显示在Azure门户工具栏的通知图标的左侧 

   > **注意**: 如果 **AdatumSync** 条目未出现在 **目录 +订阅 ** 筛选器列表中，则可能需要刷新浏览器窗口。

1. 在Azure门户中，导航到 **AdatumSync  - 概述** 边栏选项卡。

1. 从 **AdatumSync  - 概述** 边栏选项卡，显示 **AdatumSync  - 自定义域名** 边栏选项卡。 

1. 在 **AdatumSync  - 自定义域名** 边栏选项卡，标识与Azure AD租户关联的主要默认DNS域名。记住其数值-你将在下一个任务中需要它。

1. 从 **AdatumSync  - 自定义域名** 边栏选项卡，添加 **adatum.com** 自定义域名。

1. 在 **adatum.com** 边栏选项卡上，查看执行Azure AD域名验证所需的信息。

   > **注意**: 因为你没有 **adatum.com DNS** 域名，因此无法完成验证过程。这不会阻止您将 **adatum.com** Active Directory域与Azure AD租户同步。为此，您将使用Azure AD租户的默认主DNS名称（其名称以后缀 **onmicrosoft.com** 为结尾），您在此任务的前面已经确定了该名称。但是，请记住，Active Directory域的DNS域名和Azure AD租户的DNS名称将有所不同。这意味着 Adatum 用户在登录 Active Directory 域和登录 Azure AD 租户时需要使用不同的名称。


#### 任务 3：使用全局管理员角色创建Azure AD用户

1. 在Azure门户中，导航到 **AdatumSync** Azure AD租户的 **用户 - 所有用户** 边栏选项卡。

1. 在 **用户 - 所有用户** 边栏选项卡中，使用以下设置创建新用户：

    - 名称：**syncadmin**

    - 用户名称：**syncadmin@***<DNS-domain-name>* 其中 *<DNS-domain-name>* 表示您在上一个任务中标识的默认主DNS域名。记下此用户名。本实验室稍后需要使用该用户名。

    - 个人资料：**未配置**

    - 属性：**默认**

    - 组：**已选择 0 个组**

    - 目录角色：**全局管理员**

    - 密码：选中复选框 **显示密码** 并注意出现在 **密码** 文本框中的字符串。您将在此任务中稍后使用它。

   > **注意**: 若要实现 Azure AD Connect，需要具有全局管理员角色的 Azure AD 用户。

1. 打开 InPrivate Microsoft Edge 窗口。

1. 在新的浏览器窗口中，导航到Azure门户并使用 **syncadmin** 登录用户帐号。出现提示时，将密码更改为新值。

   > **注意**：您需要提供 **syncadmin** 用户帐户的完全限定名称，包括Azure AD租户DNS域名。 

1. 以 **syncadmin** 用户名签退并关闭InPrivate浏览器窗口。

> **结果:** 完成此练习后，您已创建了Azure AD租户，为新的Azure AD租户添加了自定义DNS名称，并创建了具有全局管理员角色的Azure AD用户。


### 练习 3：将 Active Directory 林与 Azure Active Directory 租户同步

本练习的主要任务如下：

1. 配置 Active Directory 以准备目录同步

1. 安装Azure AD Connect

1. 验证目录同步


#### 任务 1：配置 Active Directory 以准备目录同步

   > **注意**: 在开始此任务之前，请确保已完成练习 1 中启动的模板部署。
  
1. 在Azure门户中，将 **目录+订阅** 过滤器设置回与您在本逻辑阵列块的第一个练习中使用的Azure订阅关联的Azure AD租户。

   > **注意**: 该 **目录+订阅** 过滤器显示在Azure门户工具栏的通知图标的左侧 

1. 在Azure门户中，导航到 **adVM** 边栏选项卡，显示托管在本逻辑阵列块的第一个练习中部署的Active Directory域控制器的Azure VM属性。

1. 在 **adVM** 的边栏选项卡的 **概述** 窗格中，生成 RDP 文件并将其用于连接到 **adVM**。

1. 出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称：**Student**

    - 密码：**Pa55w.rd1234**

1. 在与 **adVM** 的远程桌面会话中，**打开Active Directory管理中心**。

1. 从 **Active Directory管理中心**，创建一个名为 **ToSync** 的根级组织单位。


1. 从 **Active Directory管理中心**，在组织单位 **ToSync** 内，使用以下设置创建新用户帐户：

    - 全称: **aduser1**

    - 用户UPN登录： **aduser1@adatum.com**

    - 用户SamAccountName登录：**adatum\aduser1**

    - 密码：**Pa55w.rd1234**

    - 其他密码选项：**密码永不过期**


#### 任务 2：安装Azure AD Connect

1. 在与 **adVM** 的RDP会话中，从服务器管理器暂时禁用 **IE增强安全配置**。

1. 在与 **adVM** 的 RDP 会话中，启动 Internet Explorer 并从 [**https://www.microsoft.com/zh-cn/download/details.aspx?id=47594**](https://www.microsoft.com/zh-cn/download/details.aspx?id=47594) 下载 **Azure AD Connect**

1. 启动 **Microsoft Azure Active Directory Connect** 向导，接受许可条款，然后在 **快速设置** 页面上，选择 **自定义** 选项。

1. 在 **安装所需组件** 页面上，取消选择所有可选配置选项并开始安装。

1. 在 **用户登录** 页面上，确保只有 **密码哈希同步** 已启用。

1. 当系统提示您连接到Azure AD时，请使用您在上一个练习中创建的 **syncadmin** 帐户凭据进行身份验证。

1. 当提示您连接目录时，请添加 **adatum.com** 森林，选择 **创建新的AD帐户** 选项，并使用以下凭据进行身份验证：

    - 用户名称：**ADATUM\\学员**

    - 密码：**Pa55w.rd1234**

1. 在 **Azure AD登录配置** 页面上，请注意警告说明 **如果UPN后缀与已验证的域名不匹配，则用户将无法使用内部部署凭据登录Azure AD，** 并启用该复选框 **继续，不将所有UPN后缀与已验证域匹配**。

   > **注意**: 如前所述，这是预期结果，因为无法验证自定义 Azure AD DNS 域 **adatum.com**。

1. 在 **域和OU过滤** 页面上，确保只有 **ToSync** OU已被选中。

1. 在 **独特地识别您的用户** 页面上，接受默认设置。

1. 在 **筛选用户和设备**页面上，接受默认设置。

1. 在 **可选功能**页面上，接受默认设置。

1. 在 **准备配置** 页面，确保 **配置完成后启动同步过程** 选中复选框并继续安装过程。 

   > **注意**: 安装需要约 2 分钟。

1. 配置完成后，关闭 Microsoft Microsoft Azure Active Directory Connect 窗口。


#### 任务 3：验证目录同步

1. 在与 **adVM** 的RDP会话中，启动Internet Explorer，浏览Azure门户网站 [**http://portal.azure.com**](http://portal.azure.com) ，然后使用您在上一个练习中创建的 **syncadmin** 账户登录。 

1. 在Azure门户中，导航到 **AdatumSync  - 概述** 边栏选项卡。

1. 在 **AdatumSync - Overview** 边栏选项卡中, 显示AdatumSync Azure AD租户的 **用户 - 所有用户** 边栏选项卡。

1. 在 **用户 - 所有用户** 边栏选项卡中，请注意用户对象列表包括 **aduser1** 帐户，与出现在 **资源** 柱中的 **Windows Server AD**。

1. 在 **用户 - 所有用户** 边栏选项卡中，显示 **aduser1  - 预置文件** 边栏选项卡。请注意，未设置 **部门** 属性。

1. 在与 **adVM** 的 RDP 会话中，切换到 **Active Directory 管理中心**，打开显示 **aduser1** 用户帐户属性的窗口，并将 **部门** 属性的值设置为 **销售**。

1. 在与 **adVM** 的 RDP 会话中，以管理员身份启动 **Windows PowerShell**。

1. 在 Windows PowerShell 提示符处，通过运行以下命令启动 Azure AD Connect 增量同步：

   ```pwsh
   Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'
   
   Start-ADSyncSyncCycle -PolicyType Delta
   ```

1. 在与 **adVM** 的 RDP 会话中，切换到显示 Azure 门户的 Internet Explorer 窗口。 

1. 在Azure门户中，导航到 **用户 - 所有用户** 边栏选项卡并刷新页面。 

1. 在 **用户 - 所有用户** 边栏选项卡中，显示 **aduser1 - 预置文件** 边栏选项卡。请注意， **部门** 属性设置为 **销售**。

   > **注意**: 您可能需要等待一分钟，然后再次刷新页面如 **部门** 属性仍未设置。

> **结果:** 完成本练习后，您已配置了Active Directory以准备目录同步，安装了Azure AD Connect并验证了目录同步。

## 练习 4：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **Bash**。

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

#### 任务 3：删除 Azure AD 租户。

1. 在实验室 VM 上以管理员身份启动 Windows PowerShell。 

1. 在实验室 VM 上的 Windows PowerShell 控制台中，通过运行以下命令安装 MsOnline PowerShell 模块（出现提示时，需要位于 NuGet 提供程序中才可继续使用对话框，单击 **是**）：

   ```pwsh
   Install-Module MsOnline -Force
   ```
   
1. 在实验室 VM 上的 Windows PowerShell 控制台中，通过运行以下命令连接到 AdatumSync Azure AD 租户（出现提示时，使用 SyncAdmin 凭据登录）：

   ```pwsh
   Connect-MsolService
   ```

1. 在实验室 VM 上的 Windows PowerShell 控制台中，通过运行以下命令禁用 Azure AD Connect 同步：

   ```pwsh
   Set-MsolDirSyncEnabled -EnableDirSync $false -Force
   ```

1. 在实验室 VM 上的 Windows PowerShell 控制台中，通过运行以下命令验证操作是否成功：

   ```pwsh
   (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled 
   ```   

1. 在实验室 VM 上，从 Azure 门户注销并关闭 Microsoft Edge 窗口。 

1. 在实验室 VM 中，启动 Microsoft Edge，导航到 Azure 门户，然后使用 SyncAdmin 凭据登录。 

1. 在 Azure 门户中，导航到 AdatumSync Azure AD 租户的 **用户 - 所有用户** 边栏选项卡，并删除除 AdatumSync 帐户之外的所有用户。

> **注意**：你可能需要等待几个小时才能完成此步骤。

1. 导航到AdatumSync - 概述”边栏选项卡，然后单击 **属性**。

1. 在 Azure Active Directory 的 **属性** 边栏选项卡上，在 **Azure 资源的访问管理** 部分中单击 **是**，然后单击 **保存**。

1. 从 Azure 门户注销，然后使用 SyncAdmin 凭据重新登录。 

1. 导航到 **AdatumSync - 概述** 边栏选项卡，并通过单击 **删除目录** 删除 Azure AD 租户。

1. 在 **删除目录 "AdatumSync"?** 边栏选项卡上，单击 **删除**。

> **注意**：有关此任务的任何其他信息，请参阅 https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto  

> **结果**：在本练习中，你删除了本实验室中使用的资源。
