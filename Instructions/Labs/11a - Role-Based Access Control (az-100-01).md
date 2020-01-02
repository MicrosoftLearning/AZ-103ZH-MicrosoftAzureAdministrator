---
lab:
    title: '基于角色的访问控制'
    module: '模块 11 - 治理与符合性'
---

# 实验：基于角色的访问控制 

本实验中的所有任务都在 Azure 门户中执行（包括 PowerShell Cloud Shell 会话）  

   > **注**：不使用 Cloud Shell 时，实验室虚拟机必须安装 Azure PowerShell 1.2.0 模块（或更新版本） [https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps](https://docs.microsoft.com/zh-cn/powershell/azure/install-az-ps)

实验室文件：无

### 场景
  
Adatum Corporation 希望使用 Azure 基于角色的访问控制和 Azure 策略来控制其 Azure 资源的预配和管理。它还希望能够自动完成预配和管理任务并进行跟踪。

### 目标
  
完成本实验室后，你将能够：

-  通过使用内置的基于角色的访问控制 (RBAC) 角色和内置的 Azure 策略来配置 Azure 资源的预配和管理委派

-  通过以委派的管理员身份预配 Azure 资源并审核预配事件来验证委派


### 练习 1：通过使用内置的基于角色的访问控制 (RBAC) 角色和内置的 Azure 策略来配置 Azure 资源的预配和管理委派

本练习的主要任务如下：

1. 创建 Azure Active Directory (AD) 用户和组。

1. 创建 Azure 资源组

1. 通过内置的 RBAC 角色委派 Azure 资源组的管理

1. 将内置的 Azure 策略分配给 Azure 资源组


#### 任务 1：创建 Azure AD 用户和组

1. 在实验虚拟机中，启动 Microsoft Edge，浏览到 Azure 门户 ([**http://portal.azure.com**](http://portal.azure.com))，并使用一个 Microsoft 帐户登录，该帐户应具备以下属性：在本实验将使用的 Azure 订阅中拥有所有者角色，并且是与该订阅关联的 Azure AD 租户的全局管理员。

1. 在 Azure 门户中，导航到**“Azure Active Directory”**边栏选项卡 

1. 在**“Azure Active Directory”**边栏选项卡中，导航到**“自定义域名”**边栏选项卡，并识别与 Azure AD 租户关联的主要 DNS 域名。记下其值，本任务的后续部分将会用到。

1. 在 Azure AD**“自定义域名”**边栏选项卡中，导航到**“用户 - 所有用户”**边栏选项卡。 

1. 在**“用户 - 所有用户”**边栏选项卡中，使用以下设置新建一个用户：

    - 用户名：**aaduser100011@&lt;DNS-domain-name&gt;**，其中 &lt;DNS-domain-name&gt; 表示在本任务前面步骤中记下的主要 DNS 域名。

    - 名称：**aaduser100011**
    
    - 名字：未设置
    
    - 姓氏：未设置

    - 自动生成密码
    
    - 密码：选中**显示密码**复选框并记下**密码**文本框中显示的字符串。您将在本实验室后面需要它。
   
    - 群组：**选定了 0 个组**

    - 角色：**用户**
    
    - 阻止登录：**否**
    
    - 使用位置：**美国**
    
    - 职位：未设置
    
    - 部门：未设置

1. 在**“用户 - 所有用户”** 边栏选项卡中，导航到**“组 - 所有组”**边栏选项卡。 

1. 在**“组 - 所有组”**边栏选项卡中，使用以下设置新建一个组：

    - 组类型：**安全**

    - 组名称：**az1001 Contributors**

    - 组描述：**az1001 Contributors**

    - 成员身份类型：**已分配**

    - 成员： **aaduser100011**


#### 任务 2：创建 Azure 资源组

1. 在 Azure 门户中，导航到**“资源组”**边栏选项卡。

1. 在**“资源组”**边栏选项卡中，使用以下设置创建第一个资源组：

    - 资源组名称：**az1000101-RG**

    - 订阅：你在此实验室中使用的订阅的名称

    - 资源组位置：最靠近实验位置并且可在其中预配 Azure VM 的 Azure 区域的名称。

   > **注**：要确定订阅中可用的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

1. 在**“资源组”**边栏选项卡中，使用以下设置创建第二个资源组：

    - 资源组名称：**az1000102-RG**

    - 订阅：在上一步中选择的订阅的名称

    - 资源组位置：在上一步中选择的 Azure 区域的名称


#### 任务 3：通过内置的 RBAC 角色委派 Azure 资源组的管理

1. 在 Azure 门户的**“资源组”**边栏选项卡中，导航到**“az1000101-RG”**边栏选项卡。

1. 在**“az1000101-RG”**边栏选项卡中，显示其**“访问控制 (IAM)”**边栏选项卡。

1. 在**“az1000101-RG  - 访问控制 (IAM)”** 边栏选项卡中，显示**“角色分配”**边栏选项卡。

1. 在**“角色分配”**边栏选项卡中，创建以下**“角色分配”**：

    - 角色：**参与者**

    - 访问权限分配对象：**Azure AD 用户、组或服务主体**

    - 选择：**“az1001 Contributors”**


#### 任务 4：将内置的 Azure 策略分配给 Azure 资源组

1. 在**“az1000101-RG”**边栏选项卡中，展示其**“策略”**边栏选项卡。

1. 在**“策略 - 合规”**边栏选项卡中，显示**“分配策略”**边栏选项卡。

1. 使用以下设置分配策略：

    - 范围： **az1000101-RG**

    - 排除项：将条目留空

    - 策略定义：**允许的虚拟机 SKU**

    - 分配名称：**允许的虚拟机 SKU**

    - 描述：**允许的选定虚拟机 SKU (Standard_DS1_v2)**

    - 分配者：将条目设置保留为其默认值
	
    - 允许的 SKU：**Standard_DS1_v2**

    - 创建托管身份：将条目留空

> **结果**：完成本练习后，你创建了一个 Azure AD 用户和一个 Azure AD 组，创建了两个 Azure 资源组，通过内置的 Azure VM 参与者 RBAC 角色委派了第一个 Azure 资源组的管理，并向同一个资源组分配了内置的 Azure 策略用于限制 Azure VM 可使用的 SKU。


### 练习 2：通过以委派的管理员身份预配 Azure 资源并审核预配事件来验证委派
  
本练习的主要任务如下：

1. 确定 Azure VM 部署的可用 DNS 名称

1. 尝试以委派的管理员身份自动部署不符合策略的 Azure VM

1. 以委派的管理员身份自动部署符合策略的 Azure VM

1. 查看与 Azure VM 部署相对应的 Azure 活动日志事件


#### 任务 1：识别 Azure VM 部署的可用 DNS 名称

1. 在 Azure 门户中，在 Cloud Shell 中启动 PowerShell 会话。 

   > **备注**：如果这是你第一次在当前 Azure 订阅中启动 Cloud Shell，则会要求你创建 Azure 文件共享以保留 Cloud Shell 文件。如果是，接受默认设置，这样会在自动生成的资源组中创建存储账户。

1. 在 Cloud Shell 窗格中，运行以下命令，将占位符 &lt;custom-label&gt; 替换为可能唯一的任何字符串，将占位符 &lt;location-of-az1000101-RG&gt; 替换为在其中创建了 **az1000101-RG** 资源组的 Azure 区域的名称。

```pwsh
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location-of-az1000101-RG>'
```

1. 验证命令是否返回**True**。如果不是，请使用不同的 &lt;custom-label&gt; 值重新运行相同的命令，直到命令返回**“True”**。 

1. 记下生成成功结果的 &lt;custom-label&gt; 值。你将在下一个任务中需要它

1. 运行以下命令：

```pwsh
   Register-AzResourceProvider –ProviderNamespace Microsoft.Network
```

```pwsh
   Register-AzResourceProvider –ProviderNamespace Microsoft.Compute
```
备注：这些 cmdlet 会注册 Azure 资源管理器 Microsoft.Network 和 Microsoft.Compute 资源提供程序。使用 Azure 资源管理器模板部署由这些资源提供程序（如果这些资源提供程序尚未注册）管理的资源时，每个订阅都需要执行一次此操作。

另请注意：如果在运行这些命令后遇到错误，提到令牌到期时间设置为当前时间之前的时间，请单击 Cloud Shell UI 上的电源按钮图标并重启 Cloud Shell 实例。  重启后，重试这些命令。

#### 任务2：尝试以委派的管理员身份自动部署不符合策略的 Azure VM

1. 在专用模式下启动另一个浏览器窗口。

1. 在新的浏览器窗口中，导航到 Azure 门户并使用在上一个练习中创建的用户帐户登录。出现提示时，将密码更改为新值。

1. 在 Azure 门户中，导航到**“资源组”**边栏选项卡，并注意只能查看资源组**“az1000101-RG”**。

1. 在 Azure 门户中，导航到**“创建资源”**边栏选项卡。 

1. 从 **创建资源** 边栏选项卡，在 Azure Marketplace 搜索 **模板部署**。

1. 使用搜索结果列表导航到**“部署自定义模板”**边栏选项卡。

1. 在**“自定义部署”**边栏选项卡上，在**“加载 GitHub 快速入门模板”**下拉列表中，选择**“101-vm-simple-linux”**条目，并导航到**“编辑模板”**边栏选项卡。

1. 在**“编辑模板”**边栏选项卡中，导航到**“变量”**部分并找到**“vmSize”**条目。

1. 请注意，模板使用硬编码的**“Standard_A1”**VM 大小。

1. 放弃可能对模板进行的任何更改，并导航到**“部署一个简单的 Ubuntu Linux VM”**边栏选项卡。

1. 在**“部署一个简单的 Ubuntu Linux VM”**边栏选项卡中，使用以下设置启动模板部署：

    - 订阅：在上一个练习中选择的相同订阅

    - 资源组： **az1000101-RG**

    - 位置：在上一个练习中选择的 Azure 区域的名称

    - 管理员用户名：**学生**

    - 管理员密码：**Pa55w.rd1234**

    - DNS 标签前缀：在上一个任务中记下的 &lt;custom-label&gt;

    - Ubuntu OS 版本：接受默认值

    - 位置：接受默认值
    
1. 请注意，启动部署失败。导航到**“错误”**边栏选项卡，并注意策略**“允许的虚拟机 SKU”**不允许部署资源。   


#### 任务 3：以委派的管理员身份自动部署符合策略的 Azure VM
 
1. 在**“部署一个简单的 Ubuntu Linux VM”**边栏选项卡中，导航到**“编辑模板”**边栏选项卡。

1. 在**“编辑模板”**边栏选项卡中，导航回到**“变量”**部分并找到**“vmSize”**条目。

1. 将值**Standard_A1**替换为**Standard_DS1_v2**并保存更改。

1. 再次启动部署。请注意，此次验证成功。 

1. 无需等待部署完成，可直接继续进行下一个任务。


#### 任务 4：查看与 Azure VM 部署相对应的 Azure 活动日志事件

1. 切换到上一个练习中使用的浏览器窗口。

1. 在 Azure 门户中，导航到**“az1000101-RG”**资源组边栏选项卡。

1. 在**“az1000101-RG”**资源组边栏选项卡中，显示其**“活动日志”**边栏选项卡。 

1. 在操作列表中，记下与失败和成功的验证事件相对应的操作。 

1. 刷新边栏选项卡视图并观察与 Azure VM 预配相对应的事件，包括表示成功部署的最后一个事件。

> **结果**：完成本练习后，你确定了 Azure VM 部署的可用 DNS 名称、尝试以委派的管理员身份自动部署了不符合策略的 Azure VM、以同一个委派的管理员身份自动部署了符合策略的 Azure VM，查看了与两个 Azure VM 部署相对应的 Azure 活动日志条目。

## 练习 3：移除实验室资源

#### 任务1：打开 Cloud Shell

1. 在门户顶部，单击**“Cloud Shell”**图标，打开 Cloud Shell 窗格。

1. 在 Cloud Shell 界面中，选择**“Bash”**。

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
