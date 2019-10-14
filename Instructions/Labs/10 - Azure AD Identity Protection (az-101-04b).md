---
lab:
    title: 'Azure AD 标识保护'
    module: '模块 10 - 保护身份'
---

# 实验室：Azure AD 标识保护

本实验室中的所有任务都是从 Azure 门户执行的，在与 Azure VM 的远程桌面会话中执行的练习 2 步骤除外。

实验室文件：

-  **Labfiles\\Module_10\\Azure_AD_Identity_Protection\\az-101-04b_azuredeploy.json**

-  **Labfiles\\Module_10\\Azure_AD_Identity_Protection\\az-101-04b_azuredeploy.parameters.json**

### 场景
  
Adatum Corporation 希望利用 Azure AD Premium 功能进行标识保护。


### 目标
  
完成本实验室后，你将能够：

-  使用 Azure 资源管理器模板部署 Azure VM

-  实现 Azure MFA

-  实施 Azure AD Identity Protection


### 练习 0：准备实验室环境
  
本练习的主要任务如下：

1. 使用 Azure 资源管理器模板部署 Azure VM


#### 任务 1：使用 Azure 资源管理器模板部署 Azure VM

1. 从实验室虚拟机启动 Microsoft Edge，浏览到 Azure 门户 ([**http://portal.azure.com**](http://portal.azure.com))，然后使用 Microsoft 帐户登录，该帐户具有要在本实验室中使用的 Azure 订阅的所有者角色。

1. 在 Azure 门户中，导航到 **新建**边栏选项卡。

1. 在 **新建** 边栏选项卡中，在 Azure 市场中搜索 **模板部署**。

1. 使用搜索结果列表导航到 **自定义部署** 卡。

1. 在 **自定义部署** 卡片，选择 **在编辑器中构建自己的模板**。

1. 在 **编辑模板** 边栏选项卡中，加载模板文件 **az-101-04b_azuredeploy.json**。 

   > **注意**：请查看模板的内容，并注意其定义了托管 Windows Server 2016 Datacenter 的 Azure VM 的部署。

1. 请保存模板并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署** 边栏选项卡，导航到 **编辑参数** 边栏选项卡

1. 在 **编辑参数** 边栏选项卡上，请加载参数文件 **az-101-04b_ Azure deploy.parameters.json**。 

1. 请保存参数并返回 **自定义部署** 边栏选项卡。 

1. 从 **自定义部署**边栏选项卡，使用以下设置启动模板部署：

    - 订阅：用于本实验室的订阅名称

    - 资源组：新资源组 **az1010401b-RG** 的名称

    - 位置：最靠近逻辑阵列模块位置的 Azure 区域的名称以及可以在其中配置 Azure VM 的位置

    - VM 大小：**Standard_DS1_v2**

    - VM 名称：**az1010401b-vm1**

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

    - “虚拟网络名称”：**az1010401b-vnet1**

   > **注**：若要标识可以预配 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

   > **注**：不必等待部署完成，请继续进行下一项练习。在本次逻辑阵列模块的最后一项练习中，您将使用该部署中所包含的虚拟机。

> **结果**：在完成本次练习后，您已经启动将在本次逻辑阵列模块的下一项练习中使用的 Azure VM 模板部署 **az1010401b-vm1**。


### 练习 1：实施 Azure MFA

本次练习的主要任务如下：

1. 请创建一个新 Azure AD 租户。

1. 请激活 Azure AD Premium v2 试用版

1. 请创建 Azure AD 用户和群组。

1. 请将 Azure AD Premium v2 许可证转让给 Azure AD 用户。

1. 请配置 Azure MFA 设置（包括欺诈警告、可信 IP 和应用程序密码）。

1. 验证 MFA 配置


#### 任务 1：请创建一个新 Azure AD 租户。

1. 在 Azure 门户中，请导航到 **新建** 边栏选项卡。 

1. 从 **新建** 边栏选项卡，搜索Azure Marketplace **Azure Active Directory**。

1. 请使用搜索结果列表，导航到 **创建库** 边栏选项卡。

1. 从 **创建** 目录边栏选项卡，创建一个新的“Azure AD 租户”，设置如下： 

  - 组织名称：**AdatumLab101-4b**

  - 初始域名：由字母和数字组合组成的唯一名称。 

  - 国家或地区：**美国**

   > **注意**：请记录初始域名。在本逻辑阵列模块中，您后续将需要该域名。 


#### 任务 2：请激活 Azure AD Premium v2 试用版

1. 在 Azure 门户中，将 **目录+订阅筛选器** 设置为新创建的“ Azure AD 租户”。

   > **注**：**目录 + 订阅** 筛选器显示在 Azure 门户工具栏中“Cloud Shell”图标右侧

   > **注意**：如果 **AdatumLab101-4b** 条目未出现在 **目录+订阅筛选器** 列表中，可能需要刷新浏览器窗口。

1. 在 Azure 门户中，导航到 **AdatumLab101-4b-概述** 边栏选项卡。

1. 从 **AdatumLab101-4b-概述** 边栏选项卡，导航到 **许可证-概述** 边栏选项卡。

1. 在 **许可证 - 概览** 边栏选项卡中，导航到 **许可证 - 所有产品** 边栏选项卡。 

1. 在 **许可证 - 所有产品** 边栏选项卡上，导航到 **激活** 边栏选项卡并激活 **Azure AD Premium P2** 免费试用版。


#### 任务 3：创建 Azure AD 用户和组。

1. 在 Azure 门户中，导航到 AdatumLab101-4b Azure AD 租户的 **用户-所有用户** 边栏选项卡。

1. 在 **用户 - 所有用户** 边栏选项卡中，使用以下设置创建新用户：

    - 名称：**aaduser1**

    - 用户名：**aaduser1@&lt;DNS-domain-name&gt;.onmicrosoft.com**，其中&lt;DNS-domain-name&gt;表示您在本次练习的第一个任务中指定的初始域名。 

   > **注意**：记下此用户名。本实验室稍后需要使用该用户名。

    - 个人资料：**默认**

    - 属性：**默认**

    - 组：**已选择 0 个组**

    - 目录角色：**用户**

    - 密码：选中 **显示密码** 复选框并记下 **密码** 文本框中出现的字符串。在本逻辑阵列模块中，您后续将需要该域名。

1. 在 **用户 - 所有用户** 边栏选项卡中，使用以下设置创建新用户：

    - 名称：**aaduser2**

    - 用户名：**aaduser2@&lt;DNS-domain-name&gt;.onmicrosoft.com**，其中&lt;DNS-domain-name&gt;表示您在本次练习的第一个任务中指定的初始域名。 

   > **注**：记下此用户名。本实验室稍后需要使用该用户名。

    - 个人资料：**默认**

    - 属性：**默认**

    - 组：**已选择 0 个组**

    - 目录角色：**用户**

    - 密码：选择 **显示密码** 复选框并注意 **密码** 文本框中出现的字符串。在本逻辑阵列模块中，您后续将需要该域名。


#### 任务 4：将 Azure AD Premium v2 许可证分配给 Azure AD 用户。

   > **注**：要将 Azure AD Premium v2 许可证分配给 Azure AD 用户，首先必须设置其位置属性。

1. 在 **用户 - 所有用户** 边栏选项卡上，导航到 **aaduser1 - 配置文件** 边栏选项卡并将 **使用位置** 设置为 **美国**。

1. 从 **aaduser1  - 个人资料** 边栏选项卡，导航到 **aaduser1  - 许可证** 边栏选项卡并为用户分配 Azure Active Directory Premium P2 许可证，并启用所有许可选项。

1. 回到 **用户 - 所有用户** 边栏选项卡，导航到 **aaduser2  - 个人资料** 边栏选项卡，并设置 **使用位置** 至 **美国**。

1. 从 **aaduser2  - 个人资料** 边栏选项卡，导航到 **aaduser2  - 许可证** 边栏选项卡并为用户分配 Azure Active Directory Premium P2 许可证，并启用所有许可选项。

1. 回到 **用户 - 所有用户** 边栏选项卡，导航到您的用户帐户的个人资料条目并设置 **使用位置** 至 **美国**。

1. 导航您的用户帐户的 **许可证** 边栏选项卡，并为其分配 Azure Active Directory Premium P2 许可证，并启用所有许可选项。

1. 从门户网站注销，然后使用您在本逻辑阵列模块中使用的相同帐户重新登录。 

   > **注意**：必须执行此步骤才能使许可证分配生效。 


#### 任务 5：配置 Azure MFA 设置。

1. 在 Azure 门户中，导航到 AdatumLab101-4b Azure AD 租户的 **用户-所有用户** 边栏选项卡。

1. 从 AdatumLab101-4b Azure AD 租户的 **用户- 所有用户** 边栏选项卡，使用 **多重身份验** 链接打开 **多重身份验** 门户。 

1. 在 **多重身份验证** 门户中，显示 **服务设置** 选项卡，查看其设置，确保已启用 **验证选项**（包括 **手机短信**,**通过移动应用发送通知**，以及 **来自移动应用或硬件标志的验证码**）。

1. 在 **多重身份验** 门户中，切换到 **用户** 选项卡，选择 **aaduser1** 条目，并启用其多重身份验状态。

1. 在 **多重身份验** 门户上，请注意 **aaduser1** 的多重身份验状态是否变为 **启用**，并且，再次选择用户条目后，您可以选择将其更改为 **强制**。

   > **注意**：将用户状态从启用更改为强制仅影响不支持 Azure MFA 的旧版 Azure AD 集成应用程序，并且一旦状态更改为强制，则需要使用应用程序密码。

1. 在选中 **aaduser1** 条目的 **多重身份验** 门户中，显示 **管理用户设置** 窗口并查看其选项，包括：

    - 要求所选用户再次提供联系方式

    - 删除所选用户生成的所有现有应用程序密码

    - 恢复所有记住的设备上的多重身份验

1. 不得对用户设置进行任何更改并切换回 Azure 门户。 

1. 从 AdatumLab101-4b Azure AD 租户的 **用户 - 所有用户** 边栏选项卡，导航到 **AdatumLab101-4b-概述** 边栏选项卡。

1. 从 **AdatumLab101-4b-概述** 边栏选项卡，导航到 **AdatumLab101-4b - MFA** 边栏选项卡。

1. 从 **AdatumLab101-4b-MFA** 边栏选项卡，导航到 **多重身份验- 欺诈警报** 边栏并配置以下设置：

    - 允许用户提交欺诈警报：**开**

    - 自动屏蔽举报欺诈的用户：**开**

    - 初次问候时报告欺诈的代码：**0**


#### 任务 6：验证 MFA 配置

1. 打开 InPrivate Microsoft Edge 窗口。

1. 在新的浏览器窗口中，导航到 Azure 门户并使用 **aaduser1** 用户帐户登录。出现提示时，将密码更改为新值。

   > **注意**：需要提供 **aaduser1** 用户帐户的完全限定名称，包括 Azure AD 租户 DNS 域名，如本实验室前面所述。

1. 当提示 **需要更多信息** 消息时，继续 **附加安全验证** 页面。

1. 在 **我们该如何与你联系？** 页面，请注意需要至少设置以下某个选项：

    - **身份验证电话**

    - **移动应用**

1. 选择 **身份验证电话** 选项以及 **通过短信向我发送验证码** 方法。

1. 完成验证并记下自动生成的应用密码。 

1. 出现提示时，更改在创建 **aaduser1** 帐户时生成的一个密码。

1. 验证您是否已成功登录 Azure 门户。

1. 注销 **aaduser1** 并关闭 InPrivate 浏览器窗口。


### 练习 2：实现 Azure AD 标识保护：
  
本次练习的主要任务如下：

1. 启用 Azure AD Identity Protection

1. 配置用户风险策略

1. 配置登录风险策略

1. 通过模拟风险事件来验证 Azure AD Identity Protection 配置


#### 任务 1：启用 Azure AD Identity Protection

1. 从逻辑阵列模块虚拟机启动 Microsoft Edge，浏览 Azure 门户，网址 [**http://portal.azure.com**](http://portal.azure.com) 并使用您用于创建 **AdatumLab101-4b** Azure AD 租户的 Microsoft 帐户登录。 

   > **注意**：确保您已登录 **AdatumLab101-4b** Azure AD 租户。您可以使用 **目录+订阅筛选器** 在 Azure AD 租户之间切换。 

1. 在 Azure 门户中，请导航到 **新建** 边栏选项卡。

1. 从 **新建** 边栏选项卡，搜索“Azure 市场”中的 **Azure AD Identity Protection**。

1. 选择搜索结果列表中的 **Azure AD Identity Protection**，继续创建与 **AdatumLab101-4b** Azure AD 租户相关联的 **Azure AD Identity Protection** 实例。

1. 在 Azure 门户中，导航到 **所有服务** 边栏并使用搜索筛选器显示 **Azure AD Identity Protection** 边栏选项卡。


#### 任务 2：配置用户风险策略

1. 从 **Azure AD Identity Protection** 边栏选项卡，导航到 **Azure AD Identity Protection-用户风险策略边栏**

1. 在 **Azure AD Identity Protection - 用户风险策略边栏** 中，配置 **用户风险补救策略**，设置如下：

    - 分配： 

        - 用户：**全部用户**（务必排除当前管理员帐户以避免锁定租户）

        - 条件：

            - 用户风险：**中等及以上**

    - 控制：

        - 访问：**允许访问**

        - **需要更改密码**

    - 强制策略：**开**


#### 任务 3：配置登录风险策略

1. 从 **Azure AD Identity Protection - 用户风险策略** 边栏选项卡，导航到 **Azure AD Identity Protection - 登录风险策略** 边栏

1. 在 **Azure AD Identity Protection - 登录风险策略边栏** 中，配置 **登录风险补救策略**，设置如下：

    - 分配： 

        - 用户：**所有用户**

        - 条件：

            - 用户风险：**中等及以上**

    - 控制：

        - 访问：**允许访问**

        - **需要多重身份验。**

    - 强制策略：**开**


#### 任务 4：通过模拟风险事件来验证 Azure AD Identity Protection 配置

   > **注**：开始此项任务之前，请确保您在练习 0 中启动的模板部署已经完成。 

1. 在 Azure 门户中，将 **目录 + 订阅** 筛选器设置为默认的 Azure AD 租户。

1. 在 Azure 门户中，导航到 **az1010401b-VM1** 边栏选项卡。

1. 从 **az1010401b-VM1** 边栏选项卡，通过“远程桌面”会话连接至 Azure VM，并在提示登录时提供以下凭据：

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

1. 在远程桌面会话中，在服务器管理器中单击 **本地服务器**，然后单击 **IE 增强的安全配置**。 

1. 在 **Internet Explorer 增强的安全配置** 对话框中，将两个选项都设置为 **关闭** 并单击 **确定**。

1. 在“远程桌面”会话中，打开 InPrivate Internet Explorer 窗口。

1. 在新的浏览器窗口中，导航到 ToR 浏览器项目，网址[**https://www.torproject.org/projects/torbrowser.html.en**](https://www.torproject.org/projects/torbrowser.html.en)，下载 ToR 浏览器，并使用默认选项进行安装。

1. 安装完成后，启动 ToR 浏览器，使用初始页面上的 **连接选项**，然后导航到应用程序访问面板，网址 [**https://myapps.microsoft.com**](https://myapps.microsoft.com)

1. 出现提示时，使用您在上一项练习中创建的 **aaduser2** 帐户登录。

1. 将会显示 **您的登录已被屏蔽的** 消息。由于该帐户未通过多重身份验进行配置，但是，与 ToR 浏览器使用相关的登录风险增加，要求必须进行该配置，预期会出现该消息。

1. 使用 **注销** 并通过其他帐户选项以您在上一项练习中为多重身份验创建和配置的 **aaduser1** 帐户登录。

1. 这一次，将会显示 **检测到可疑活动** 消息。同样地，由于此帐户通过多重身份验进行配置，预期会出现该消息。考虑到与 ToR 浏览器使用相关的登录风险增加，您必须根据您在上一个任务中配置的登录风险策略使用多重身份验。

1. 使用 **验证选项** 并指定是否要通过文本或致电来验证您的身份。 

1. 完成验证并确保您已成功登录到应用程序访问面板。

1. 以 **aaduser1** 注销，并关闭 ToR 浏览器窗口。

1. 启动 Internet Explorer，浏览 Azure 门户，网址：[**http://portal.azure.com**](http://portal.azure.com) 并使用您用于创建 **AdatumLab101-4b** Azure AD 租户的 Microsoft 帐户登录。 

1. 在 Azure 门户中，导航到 **Azure AD Identity Protection - 风险事件** 边栏并注意该条目代表 **从匿名 IP 地址登录**。

1. 从 **Azure AD Identity Protection-风险事件** 边栏选项卡，导航到 **Azure AD Identity Protection-标记为风险的用户** 边栏并注意代表 **aaduser2** 的条目。

> **结果**：完成本次练习后，您已通过模拟风险事件启用了“Azure AD Identity Protection”，配置用户风险策略和登录风险策略以及验证“Azure AD Identity Protection 配置”

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择 **Bash**。

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter**，列出在本实验室中创建的所有资源组：

   ```sh
   az group list --query "[?starts_with(name,'az1010')].name" --output tsv
   ```

1. 验证输出中是否仅包含在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，键入以下命令并按 **Enter**，删除在本实验室中创建的资源组

   ```sh
   az group list --query "[?starts_with(name,'az1010')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **注意**：若要删除在本实验室中创建的 Azure AD 租户，请按照 https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto 中说明操作  

> **结果**：在本练习中，你删除了本实验室中使用的资源。
