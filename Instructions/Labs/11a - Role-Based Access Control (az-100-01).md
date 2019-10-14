---
lab:
    title: '基于角色的访问控制'
    module: '模块 11 - 治理与符合性'
---

# 实验室：基于角色的访问控制 

本逻辑阵列模块中的所有任务都是从 Azure 门户执行的（包括 PowerShell Cloud Shell 会话）  

   > **注意**：不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件：无

### 方案
  
Adatum Corporation 希望使用 Azure 基于角色的访问控制和 Azure Policy 来控制其 Azure 资源的预配和管理。并且，它还希望能够自动化和跟踪配置和管理任务。

### 目标
  
完成本实验室后，你将能够：

-  通过使用内置的基于角色的访问控制（RBAC）角色和内置 Azure 策略，配置 Azure 资源的预配和管理委派

-  通过将 Azure 资源配置为委派管理和审核配置事件来验证委派


### 练习 1：通过使用内置的基于角色的访问控制（RBAC）角色和内置 Azure 策略，配置 Azure 资源的配置和管理委派

本次练习的主要任务如下：

1. 请创建 Azure AD 用户和群组。

1. 创建资源组

1. 通过内置 RBAC 角色委派 Azure 资源组的管理

1. 将内置 Azure 策略分配给 Azure 资源组


#### 任务 1：请创建 Azure AD 用户和群组。

1. 从逻辑阵列模块虚拟机启动 Microsoft Edge，浏览到 Azure 门户 [**http://portal.azure.com**](http://portal.azure.com) 并使用 Microsoft 帐户登录，该帐户在您打算在本逻辑阵列模块中使用的 Azure 订阅中具有所有者角色，并且是与该订阅关联的 Azure AD 租户的全局管理员。

1. 在 Azure 门户中，导航到 **Azure Active Directory** 边栏选项卡 

1. 从 **Azure Active Directory** 边栏选项卡，导航到 **自定义域名** 边栏选项卡并标识与 Azure AD 租户关联的主 DNS 域名。记下它的值，此任务中稍后部分需要使用。

1. 从 Azure AD **自定义域名** 边栏选项卡，导航到 **用户 - 所有用户** 边栏选项卡。 

1. 在 **“用户 - 所有用户”** 边栏选项卡中，使用以下设置创建新用户：

    - 名称：**aaduser100011**

    - 用户名： **aaduser100011@&lt;DNS-domain-name&gt;** 其中 &lt;DNS-domain-name&gt; 表示您在此任务中先前确定的主 DNS 域名。

    - 个人资料：**未配置**

    - 属性：**默认**

    - 组：**已选择 0 个组**

    - 目录角色：**用户**

    - 密码：选中 **显示密码** 复选框并记下 **密码** 文本框中出现的字符串。在本逻辑阵列模块中，您后续将需要该域名。

1. 从 **用户 - 所有用户** 边栏选项卡，导航到 **群组 - 所有群组** 边栏选项卡。 

1. 从**组 - 所有组**边栏选项卡中，使用以下设置创建新组：

    - 组类型: **安全**

    - 组名称：**az1001 贡献者**

    - 小组介绍： **az1001 贡献者**

    - 成员身份类型：**已分配**

    - 成员：**aaduser100011**


#### 任务 2：创建资源组

1. 在 Azure 门户中，导航到 **资源组** 边栏选项卡。

1. 来自 **资源组** 边栏选项卡，使用以下设置创建第一个资源组：

    - 资源组名称：**az1000101-RG**

    - 订阅：您希望用于本逻辑阵列模块的订阅名称

    - 资源组位置：最靠近逻辑阵列模块位置的 Azure 区域的名称以及可以在其中设置 Azure VM 的位置。

   > **注意**：要识别订阅中可用的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

1. 从 **资源组** 边栏选项卡，使用以下设置创建第二个资源组：

    - 资源组名称：**az1000102-RG**

    - 订阅：您希望用于本逻辑阵列模块的订阅名称

    - 资源组位置：您在上一步中选择的 Azure 区域的名称


#### 任务 3：通过内置 RBAC 角色委派 Azure 资源组的管理

1. 在 Azure 门户中，从 **资源组** 边栏选项卡，导航到 **az1000101-RG** 边栏选项卡。

1. 从 **az1000101-RG** 边栏选项卡，展示它 **访问控制（IAM）** 边栏选项卡。

1. 从 **az1000101-RG  - 访问控制（IAM）** 边栏选项卡，显示 **角色分配**  边栏选项卡。

1. 从 **角色分配** 边栏选项卡，创建以下 **角色分配**：

    - 角色 **参与者**

    - 向下列对象分配访问权限：**Azure AD 用户，组或服务主体**

    - 选择 **az1001-1b**。


#### 任务 4：将内置 Azure 策略分配给 Azure 资源组

1. 从 **az1000101-RG** 边栏选项卡，显示其 **策略** 边栏选项卡。

1. 从 **策略 - 符合性** 边栏选项卡，显示 **分配策略边** 栏选项卡。

1. 根据以下设置分配策略：

    - 范围：**az1000101-RG**

    - 排除：将该项留空

    - 策略定义：**允许的虚拟机 SKU**

    - 作业名称：**允许的虚拟机 SKU**

    - 描述：**允许选择的虚拟机 SKU（Standard_DS1_v2）**

    - 分配方：将该项保留为其默认值
	
    - 允许的 SKU：**Standard_DS1_v2**

    - 创建托管标识：将该项留空

> **结果**：完成本次练习后，你已创建了 Azure AD 用户和 Azure AD 组，创建了两个 Azure 资源组，通过内置 Azure VM 参与者 RBAC 角色委派了第一个 Azure 资源组的管理，并向同一资源组分配了限制可用于 Azure VM 的 SKU 的内置 Azure 策略。


### 练习 2：通过将 Azure 资源预配为委派管理员和审核预配事件来验证委派
  
本次练习的主要任务如下：

1. 确定 Azure VM 部署的可用 DNS 名称

1. 尝试将策略不兼容的 Azure VM 自动部署为委派管理员

1. 作为委派管理员执行符合策略的 Azure VM 的自动部署

1. 查看与 Azure VM 部署相对应的 Azure 活动日志事件


#### 任务 1：确定 Azure VM 部署的可用 DNS 名称

1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

   > **注意**：如果这是您第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求您创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，请接受默认设置，这样会在自动生成的资源组中创建存储帐户。

1. 在 Cloud Shell 窗格中，运行以下命令，替换占位符&lt;custom-label&gt;任何可能是唯一的字符串和占位符&lt;location-of-az1000101-RG&gt;使用您在其中创建的 Azure 区域的名称 **az1000101-RG** 资源组。

   ```pwsh
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location-of-az1000101-RG>'
   ```

1. 验证命令是否返回了 **True**。如果没有，请使用&lt;custom-label&gt; 的不同值重新运行相同的命令，直到命令返回 **True**。 

1. 请注意导致成功结果的&lt;custom-label&gt;的值。您将在下一个任务中使用它

1. 运行以下命令：

   ```pwsh
   Register-AzResourceProvider –ProviderNamespace Microsoft.Network
   ```

   ```pwsh
   Register-AzResourceProvider –ProviderNamespace Microsoft.Compute
   ```
注意：这些 cmdlet 注册 Azure 资源管理器 Microsoft.Network 和 Microsoft.Compute 资源提供程序。这是使用 Azure 资源管理器模板部署由这些资源提供程序管理的资源时所需的一次性操作（每个订阅）（如果这些资源提供程序尚未注册）。

另请注意：如果在运行这些命令后遇到错误，提到令牌到期时间设置为当前时间之前的时间，请单击 Cloud Shell UI 上的电源按钮图标并重启 Cloud Shell 实例。  重启后，重试这些命令。

#### 任务 2：尝试将策略不兼容的 Azure VM 自动部署为委派管理员

1. 在专用模式下启动其他浏览器窗口。

1. 在新的浏览器窗口中，导航到 Azure 门户并使用您在上一个练习中创建的用户帐户登录。出现提示时，将密码更改为新值。

1. 在 Azure 门户中，导航到 **资源组** 边栏选项卡并注意您只能查看资源组 **az1000101-RG**。

1. 在 Azure 门户中，导航到 **创建资源** 边栏选项卡。 

1. 在 **创建资源** 边栏选项卡中，在 Azure 市场中搜索 **模板部署**。

1. 使用搜索结果列表导航到 **部署自定义模板** 边栏选项卡。

1. 在 **自定义部署** 边栏选项卡，在 **加载 GitHub 快速入门模板** 下拉列表，选择 **101-VM-简单 Linux 的** 进入并导航到 **编辑模板** 边栏选项卡。

1. 在 **编辑模板** 边栏选项卡，导航到 **变量** 部分并找到 **vmSize** 条目。

1. 请注意，模板使用硬编码 **Standard_A1** VM 大小。

1. 放弃您可能对模板所做的任何更改并导航到 **部署一个简单的 Ubuntu Linux VM** 边栏选项卡。

1. 从 **部署一个简单的 Ubuntu Linux VM** 边栏选项卡，使用以下设置启动模板部署：

    - 订阅：在上一个练习中选择的同一订阅

    - 资源组：**az1000101-RG**

    - 位置：您在上一练习中选择的 Azure 区域的名称

    - 管理员用户名：**Student**

    - 管理员密码：**Pa55w.rd1234**

    - Dns 标签前缀：您在上一个任务中标识的<custom-label>

    - Ubuntu OS 版：接受默认值

    - 位置：接受默认值
    
1. 请注意，部署启动失败。导航到 **错误** 边栏选项卡并注意策略不允许部署资源 **允许的虚拟机 SKU**。   


#### 任务 3：作为委派管理员执行符合策略的 Azure VM 的自动部署
 
1. 从 **部署一个简单的 Ubuntu Linux VM** 边栏选项卡，导航到 **编辑模板**边栏选项卡。

1. 在 **编辑模板**边栏选项卡，导航回到 **变量** 部分并找到 **vmSize** 条目。

1. 将值 **Standard_A1** 替换为 **Standard_DS1_v2** 并保存更改。

1. 再次启动部署。请注意，此次验证成功。 

1. 请勿等待第一台虚拟机预配完成，请继续进行下一个任务


#### 任务 4：查看与 Azure VM 部署相对应的 Azure 活动日志事件

1. 切换到上一练习中使用的浏览器窗口。

1. 在 Azure 门户中，导航到 **az1000101-RG** 资源组边栏选项卡。

1. 从 **az1000101-RG** 资源组边栏选项卡，显示它 **活动日志** 边栏选项卡。 

1. 在操作列表中，请注意与失败和成功验证事件对应的操作。 

1. 刷新刀片视图并观察与 Azure VM 配置相对应的事件，包括表示成功部署的最后一个事件。

> **结果**：完成本练习后，你已为 Azure VM 部署确定了可用的 DNS 名称，尝试了将与策略不兼容的 Azure VM 自动部署为委派管理员，执行了将符合策略的 Azure VM 自动部署为相同的委派管理员，并查看了与 Azure VM 部署都对应的 Azure 活动日志条目。

## 练习 3：删除实验室资源

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

> **结果**：在本练习中，你删除了本实验室中使用的资源。
