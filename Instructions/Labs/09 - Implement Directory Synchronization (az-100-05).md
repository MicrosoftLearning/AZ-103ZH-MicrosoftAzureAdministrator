---
lab:
    title: '实现目录同步'
    module: '实施和管理混合身份'
---

# 逻辑阵列块：'实现目录同步'
  
本逻辑阵列块中的所有任务都是从Azure门户（包括PowerShell Cloud Shell会话）执行的，但练习3任务1、练习3任务2和练习3任务3除外，其中包括从远程桌面会话到Azure VM执行的步骤

   > **注意**: 不使用Cloud Shell时，实验室虚拟机必须安装Azure PowerShell 1.2.0模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps?view=azps-1.2.0](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps?view=azps-1.2.0)

逻辑阵列块文件：无

### 方案
  
Adatum Corporation希望将其活动目录与Azure Active Directory 集成


### 目标
  
 完成本实验后，您将能够：

- 部署托管Active Directory域控制器的Azure VM

- 创建和配置Azure Active Directory租户

- 将Active Directory林与Azure Active Directory租户同步


### 练习 1：部署托管Active Directory域控制器的Azure VM

本练习的主要任务如下：

1. 确定Azure VM部署的可用DNS名称

1. 使用Azure资源管理器模板，部署可托管Active Directory域控制器的Azure VM


#### 任务 1：确定Azure VM部署的可用DNS名称

1. 从实验室虚拟机启动Microsoft Edge，浏览到Azure门户 [**http://portal.azure.com**](http://portal.azure.com) 并使用Microsoft帐户登录，该帐户在您打算在本逻辑阵列块中使用的Azure订阅中具有所有者角色，并且是与该订阅关联的Azure AD租户的全局管理员。

1. 从Azure门户，在Cloud Shell窗格中启动PowerShell会话。

   > **注意**: 如果这是您第一次在当前Azure订阅中启动Cloud Shell，则会要求您创建Azure文件共享以保留Cloud Shell文件。如果是，请接受默认值，这将导致在自动生成的资源组中创建存储帐户。

1. 在Cloud Shell窗格中，运行以下命令，使用任何可能的唯一字符串和占位符&lt;location&gt;替换占位符&lt;custom-label&gt;  使用要在其中部署将承载Active Directory域控制器的Azure VM的Azure区域的名称。

   > **注意**: 要标识可以配置Azure VM的Azure区域，请登陆： [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   ```
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
   ```

1. 验证返回命令 **True**。如果没有，请使用&lt;custom-label&gt; 的不同值重新运行相同的命令，直到命令返回 **True**。 

1. 请注意导致成功结果的&lt;custom-label&gt;的值。您将在下一个任务中使用它


#### 任务 2：使用Azure资源管理器模板，部署可托管Active Directory域控制器的Azure VM

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。

1. 从 **创建资源** 边栏选项卡，在Azure应用市场搜索 **模板部署**。

1. 使用搜索结果列表导航到 **部署自定义模板** 边栏选项卡。

1. 在 **自定义部署** 边栏选项卡，在 **加载GitHub快速入门模板** 下拉列表并选择 **主动目录新域** 条目。

1. 在 **使用新的AD林创建Azure VM** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：您用于本实验室的订阅名称

    - 资源组：新资源组 **az1000501-RG** 的名称

    - 位置：您在上一个任务中使用的Azure区域名称

    - 管理员用户名： **学员**

    - 管理员密码： **Pa55w.rd1234**

    - 域名: **adatum.com**

    - Dns前缀：您在上一个任务中标识的&lt;custom-label&gt; 

    - _artifacts 位置：接受默认值

    - _artifacts 位置Sas Token：留空

    - 位置：接受默认值

   > **注意**: 不要等待部署完成，而是继续下一个练习。您将在本逻辑阵列块的第三个练习中使用此任务中部署的虚拟机。

> **结果:** 完成此练习后，您已使用Azure资源管理器模板启动了将托管Active Directory域控制器的Azure VM的部署


### 练习 2：创建和配置Azure Active Directory租户

本练习的主要任务如下：

1. 创建Azure Active Directory（AD）租户

1. 将自定义DNS名称添加到新的Azure AD租户

1. 使用全局管理员角色创建Azure AD用户


#### 任务 1：创建Azure Active Directory（AD）租户

1. 在Azure门户中，导航到 **创建资源** 边栏选项卡。 

1. 从 **创建资源** 边栏选项卡，在Azure应用市场内搜索 **Azure Active Directory**。

1. 使用搜索结果列表导航到 **创建目录** 边栏选项卡。

1. 从 **创建目录** 边栏选项卡，  创建一个新Azure AD租户，设置如下： 

  - 组织名称： **AdatumSync**

  - 初始域名：由字母和数字组合组成的唯一名称。 

  - 国家或地区： **美国**

   > **注意**: **初始域名** 文本框中的绿色复选标记将指示您键入的域名是否有效且唯一。 


#### 任务 2：将自定义DNS名称添加到新的Azure AD租户
  
1. 在Azure门户中，设置 **目录+订阅** 过滤器到新创建的Azure AD租户。

   > **注意**: 该 **目录+订阅** 过滤器显示在Azure门户工具栏的通知图标的左侧 

   > **注意**: 如果 **AdatumSync** 条目未出现在 **目录 +订阅** 筛选器列表中，则可能需要刷新浏览器窗口。

1. 在Azure门户中，导航到 **AdatumSync - 概述** 边栏选项卡。

1. 从 **AdatumSync - 概述** 边栏选项卡，显示 **AdatumSync - 自定义域名** 边栏选项卡。 

1. 在 **AdatumSync - 自定义域名** 边栏选项卡，标识与Azure AD租户关联的主要默认DNS域名。记住其数值-你将在下一个任务中需要它。

1. 从 **AdatumSync - 自定义域名** 边栏选项卡，添加 **adatum.com** 自定义域名。

1. 在 **adatum.com** 边栏选项卡上，查看执行Azure AD域名验证所需的信息。

   > **注意**: 您将无法完成验证过程，因为您不拥有 **adatum.com** DNS域名。这不会阻止您将 **adatum.com** Active Directory域与Azure AD租户同步。为此，您将使用Azure AD租户的默认主DNS名称（其名称以后缀 **onmicrosoft.com** 为结尾），您在此任务的前面已经确定了该名称。但是，请记住，Active Directory域的DNS域名和Azure AD租户的DNS名称将有所不同。这意味着Adatum用户在登录Active Directory域和登录Azure AD租户时需要使用不同的名称。


#### 任务 3：使用全局管理员角色创建Azure AD用户

1. 在Azure门户中，导航到 **AdatumSync** Azure AD租户的 **用户 - 所有用户** 边栏选项卡。

1. 在 **用户 - 所有用户** 边栏选项卡中，使用以下设置创建新用户：

    - 名称： **syncadmin**

    - 用户名称： **syncadmin@***<DNS-domain-name>* 其中 *<DNS-domain-name>* 表示您在上一个任务中标识的默认主DNS域名。记下此用户名。您将在本逻辑阵列块后期需要它。

    - 配置文件： **未配置**

    - 属性： **默认**

    - 组： **选定了 0 个组**

    - 目录角色： **全局管理员**

    - 密码：选中复选框 **显示密码** 并注意出现在 **密码** 文本框中的字符串。您将在此任务中稍后使用它。

   > **注意**: 为实现Azure AD Connect，需要具有全局管理员角色的Azure AD用户。

1. 打开InPrivate Microsoft Edge窗口。

1. 在新的浏览器窗口中，导航到Azure门户并使用 **syncadmin** 登录用户帐号。出现提示时，将密码更改为新值。

   > **注意**: 您需要提供 **syncadmin** 用户帐户的完全限定名称，包括Azure AD租户DNS域名。 

1. 以 **syncadmin** 用户名签退并关闭InPrivate浏览器窗口。

> **结果:** 完成此练习后，您已创建了Azure AD租户，为新的Azure AD租户添加了自定义DNS名称，并创建了具有全局管理员角色的Azure AD用户。


### 练习 3：将Active Directory林与Azure Active Directory租户同步

本练习的主要任务如下：

1. 配置Active Directory以准备目录同步

1. 安装Azure AD Connect

1. 验证目录同步


#### 任务 1：配置Active Directory以准备目录同步

   > **注意**: 在开始此任务之前，请确保您在练习1中启动的模板部署已完成。
  
1. 在Azure门户中，将 **目录+订阅** 过滤器设置回与您在本逻辑阵列块的第一个练习中使用的Azure订阅关联的Azure AD租户。

   > **注意**: 该 **目录+订阅** 过滤器显示在Azure门户工具栏的通知图标的左侧 

1. 在Azure门户中，导航到 **adVM** 边栏选项卡，显示托管在本逻辑阵列块的第一个练习中部署的Active Directory域控制器的Azure VM属性。

1. 从 **adVM** 的 **概观** 窗格边栏选项卡，生成RDP文件并使用它连接 **adVM**。

1. 出现提示时，通过指定以下凭据进行身份验证：

    - 用户名称： **学员**

    - 密码： **Pa55w.rd1234**

1. 在与 **adVM** 的远程桌面会话中，**打开Active Directory管理中心**。

1. 从 **Active Directory管理中心**，创建一个名为 **ToSync** 的根级组织单位。


1. 从 **Active Directory管理中心**，在组织单位 **ToSync** 内，使用以下设置创建新用户帐户：

    - 全称: **aduser1**

    - 用户UPN登录： **aduser1@adatum.com**

    - 用户SamAccountName登录： **adatum\aduser1**

    - 密码： **Pa55w.rd1234**

    - 其他密码选项： **密码永不过期**


#### 任务 2：安装Azure AD Connect

1. 在与 **adVM** 的RDP会话中，从服务器管理器暂时禁用 **IE增强安全配置**。

1. 在与 **adVM** 的RDP会话中，启动Internet Explorer并从 [**https://www.microsoft.com/zh-cn/download/details.aspx?id=47594**](https://www.microsoft.com/zh-cn/download/details.aspx?id=47594)下载 **Azure AD Connect** 

1. 启动 **Microsoft Azure Active Directory Connect** 向导，接受许可条款，然后在 **快速设置** 页面上，选择 **自定义** 选项。

1. 在 **安装所需组件** 页面上，取消选择所有可选配置选项并开始安装。

1. 在 **用户登录** 页面上，确保只有 **密码哈希同步** 已启用。

1. 当系统提示您连接到Azure AD时，请使用您在上一个练习中创建的 **syncadmin** 帐户凭据进行身份验证。

1. 当提示您连接目录时，请添加 **adatum.com** 森林，选择 **创建新的AD帐户** 选项，并使用以下凭据进行身份验证：

    - 用户名称： **ADATUM\\学员**

    - 密码： **Pa55w.rd1234**

1. 在 **Azure AD登录配置** 页面上，请注意警告说明 **如果UPN后缀与已验证的域名不匹配，则用户将无法使用内部部署凭据登录Azure AD，** 并启用该复选框 **继续，不将所有UPN后缀与已验证域匹配**。

   > **注意**: 如前所述，这是预期的，因为您无法验证自定义Azure AD DNS域 **adatum.com**。

1. 在 **域和OU过滤** 页面上，确保只有 **ToSync** OU已被选中。

1. 在 **独特地识别您的用户** 页面上，接受默认设置。

1. 在 **筛选用户和设备** 页面上，接受默认设置。

1. 在 **可选功能** 页面上，接受默认设置。

1. 在 **准备配置** 页面，确保 **配置完成后启动同步过程** 选中复选框并继续安装过程。 

   > **注意**: 安装需要约 2 分钟。

1. 配置完成后，关闭 Microsoft Microsoft Azure Active Directory Connect 窗口。


#### 任务 3：验证目录同步

1. 在与 **adVM** 的RDP会话中，启动Internet Explorer，浏览Azure门户网站 [**http://portal.azure.com**](http://portal.azure.com) ，然后使用您在上一个练习中创建的 **syncadmin** 账户登录。 

1. 在Azure门户中，导航到 **AdatumSync - 概述** 边栏选项卡。

1. 在 **AdatumSync - Overview** 边栏选项卡中, 显示AdatumSync Azure AD租户的 **用户 - 所有用户** 边栏选项卡。

1. 在 **用户 - 所有用户** 边栏选项卡中，请注意用户对象列表包括 **aduser1** 帐户，与出现在 **资源** 柱中的 **Windows Server AD** 。

1. 在 **用户 - 所有用户** 边栏选项卡中，显示 **aduser1  - 预置文件** 边栏选项卡。请注意 **部门** 属性未设置。

1. 在与 **adVM** 的RDP会话中，切换到 **Active Directory管理中心**，打开显示 **aduser1** 用户帐户属性的窗口，并设置其值 **部门** 属性为 **销售**。

1. 在与 **adVM** 的RDP会话中，启动 **Windows PowerShell** 作为管理员。

1. 在Windows PowerShell提示符下，通过运行以下命令启动Azure AD Connect delta同步：

   ```
   Start-ADSyncSyncCycle -PolicyType Delta
   ```

1. 在与 **adVM** 的RDP会话中，切换到显示Azure门户的Internet Explorer窗口。 

1. 在Azure门户中，导航到 **用户 - 所有用户** 边栏选项卡并刷新页面。 

1. 在 **用户 - 所有用户** 边栏选项卡中，显示 **aduser1 - 预置文件** 边栏选项卡。请注意， **部门** 属性设置为 **销售**。

   > **注意**: 您可能需要等待一分钟，然后再次刷新页面如 **部门** 属性仍未设置。

> **结果:** 完成本练习后，您已配置了Active Directory以准备目录同步，安装了Azure AD Connect并验证了目录同步。
