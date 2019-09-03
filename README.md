# AZ-103 Microsoft Azure 管理员

- **[下载最新的学生手册和 AllFiles 内容](../../releases/latest)**
- **您是 MCT 吗？** - 查看我们的 [适用于 MCT 的 GitHub 用户指南](https://microsoftlearning.github.io/MCT-User-Guide-ZH/)

> AZ-100 和 AZ-101 认证正在被新的 AZ-103 Microsoft Azure 管理员考试所取代！您可以在 Liberty Munson 的博客上阅读有关此公告的更多信息，网址为 https://www.microsoft.com/zh-cn/learning/community-blog-post.aspx?BlogId=8&Id=375217 
 
为了支持新的考试，我们从 2019 年 5 月 3 日开始推出一个新的 AZ-103 GitHub 存储库。那时，各自存储库中的所有 AZ-100 和 AZ-101 逻辑阵列模块都将被移动到此存储库中。这些逻辑阵列模块正在 AZ-103 中重复使用，我们只维护一个存储库。将保留 AZ-100 和 AZ-101 逻辑阵列模块编号系统，因此，如果您仍在教授 AZ-100 或 AZ-101 课程，您将能够轻松识别逻辑阵列模块。您还可以获得最新版本的逻辑阵列模块，并提交您发现的任何问题。

该存储库将包括以下逻辑阵列模块：

-  Azure 事件网格和 Azure 逻辑应用程序（az-101-02b）
-  Azure AD 身份保护（az-101-04b）
-  Azure 网络观察器（az-101-03b）
-  配置 Azure DNS（az-100-04b）
-  部署和管理虚拟机（az-100-03）
-  治理与合规（az-100-01b）
-  实施和管理 Azure Web 应用程序（az-101-02）
-  在区域之间实施 ASR（az-101-01）
-  实现目录同步（az-100-05）
-  实现文件同步（az-100-02b）
-  实施和管理存储（az-100-02）
-  负载均衡器和流量管理器（az-101-03）
-  将本地 Hyper-V VM 迁移到 Azure（az-101-01b）
-  特权身份管理（az-101-04）
-  基于角色的访问控制(az-100-01)
-  自助密码重置 (az-100-05b)
-  虚拟机和规模集(az-100-03b)
-  VNet 对等和服务链（az-100-04）

请注意，以下逻辑阵列模块不会成为 AZ-103 课程的一部分：

-  Azure 事件网格和 Azure 逻辑应用程序（az-101-02b）
-  实施和管理 Azure Web 应用程序（az-101-02）
-  将本地 Hyper-V VM 迁移到 Azure（az-101-01b）
-  Privileged Identity Management (az-101-04)

**我们在做什么？**

*	我们将在 GitHub 上发布逻辑阵列模块说明和逻辑阵列模块文件，以允许课程作者和 MCT 之间的互动。我们希望这有助于在 Azure 平台发生变化时保持最新内容。

*	这是 AZ-103，Microsoft Azure Administrator 课程的 GitHub 存储库。 

*	在每个存储库中，“指令”文件夹中都有 Markdown 格式的逻辑阵列模块指南。如果合适，还需要在 Allfiles \ Labfiles 文件夹中完成逻辑阵列模块所需的其他文件。并非每门课程都有相应的逻辑阵列模块文件 

*	对于每次交付，培训师应从 GitHub 下载最新文件。培训师还应检查“问题”选项卡，以查看其他 MCT 是否报告了任何错误。  

*	提供逻辑阵列模块时间估算，但培训师应根据受众进行检查以确保其准确性。

*	要进行逻辑阵列模块，您需要连接互联网和 Azure 订阅。有关使用 Cloud Shell 的更多信息，请阅读《讲师准备指南》。 

**我们怎么做？**

*	如果您在教授这些课程时，您需要确定需要改进的地方，请使用“问题”选项卡提供反馈。我们将定期创建新文件以合并更改。 

**关于 AZ-103 课程的一般评论**

* 所有逻辑阵列模块中的 PowerShell 脚本都使用当前版本的 Azure PowerShell Az 模块。

* 虽然不是必需的，但在完成每个逻辑阵列模块时，最好取消配置任何现有资源。这有助于降低超出默认 vCPU 配额限制的风险并最大限度地降低使用费用。

* 这些区域中 Azure 区域和资源的可用性在某种程度上取决于您使用的订阅类型。要识别订阅中可用的 Azure 区域，请参阅 https://azure.microsoft.com/zh-cn/regions/offers/。要确定这些地区的可用资源，请参阅 https://azure.microsoft.com/zh-cn/global-infrastructure/services/。这些限制可能会导致模板验证或模板部署期间出现故障，尤其是在配置 Azure VM 时。如果发生这种情况，请查看错误消息并使用不同的 VM 大小或不同的区域重试部署。

* 首次启动 Azure Cloud Shell 时，可能会提示您创建 Azure 文件共享以保留 Cloud Shell 文件。如果是这样，您通常可以接受默认值，这将导致在自动生成的资源组中创建存储帐户。请注意，如果删除该存储帐户，可能会再次发生这种情况。

* 在执行基于模板的部署之前，您可能需要注册处理器，以处理模板中引用的资源类型的供应。这是使用 Azure Resource Manager 模板部署由这些资源提供程序管理的资源时所需的一次性操作（每个订阅）（如果这些资源提供程序尚未注册）。您可以从 Azure 门户中的订阅资源提供程序刀片执行注册，也可以使用 Cloud Shell 运行 Register-AzResourceProvider PowerShell cmdlet 或 az provider Azure CLI 命令。

我们希望使用这个 GitHub 存储库可以为逻辑阵列模块带来协作感，并提高逻辑阵列模块体验的整体质量。 

问候，
*Microsoft Azure 管理员课件团队*