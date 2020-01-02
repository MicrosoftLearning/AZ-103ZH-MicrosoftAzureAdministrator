# AZ-103 Microsoft Azure 管理员

- **你是 MCT？** - 请查看我们的[适用于 MCT 的 GitHub 用户指南](https://microsoftlearning.github.io/MCT-User-Guide/)
- **需要手动构建实验室说明？** -  可在 [MicrosoftLearning/Docker-Build](https://github.com/MicrosoftLearning/Docker-Build) 存储库获取相关说明
- 查看 AZ-103 模块提供的实验室列表 - https://microsoftlearning.github.io/AZ-103-MicrosoftAzureAdministrator/

> AZ-100 和 AZ-101 认证正替换为新的 AZ-103 Microsoft Azure 管理员考试！可在 Liberty Munson 的博客 (https://www.microsoft.com/zh-cn/learning/community-blog-post.aspx?BlogId=8&Id=375217) 上阅读有关此公告的更多信息 

> 若要获取有关课程内容的建议或一般评论，请务必使用 [MCT 课件论坛](https://www.microsoft.com/zh-cn/learning/mct-central.aspx)。此外，可在[课件支持论坛](https://trainingsupport.microsoft.com/zh-cn)上报告 bug 和课程错误。
 
为了支持新的考试，我们从 2019 年 5 月 3 日开始推出一个新的 AZ-103 GitHub 存储库。自此，AZ-100 和 AZ-101 课程各自存储库中的所有实验都将移到此存储库中。AZ-103 中也重复使用这些实验，因此我们只需维护一个存储库。AZ-100 和 AZ-101 实验编号系统保留，因此，如果你仍讲授 AZ-100 或 AZ-101 课程，将能够轻松识别这些实验。你还可以获得最新版实验并提交发现的任何问题。

此存储库中将包含以下实验：

-  Azure 事件网格和 Azure 逻辑应用 (az-101-02b)
-  Azure AD 身份保护 (az-101-04b)
-  Azure 网络观察程序 (az-101-03b)
-  配置 Azure DNS (az-100-04b)
-  部署和管理虚拟机 (az-100-03)
-  治理与合规 (az-100-01b)
-  实施和管理 Azure Web 应用 (az-101-02)
-  在区域之间实施 ASR (az-101-01)
-  实施目录同步 (az-100-05)
-  实施文件同步 (az-100-02b)
-  实施和管理存储 (az-100-02)
-  负载均衡器和流量管理器 (az-101-03)
-  将本地 Hyper-V VM 迁移到 Azure (az-101-01b)
-  基于角色的访问控制 (az-100-01)
-  自助密码重置 (az-100-05b)
-  虚拟机和规模集 (az-100-03b)
-  VNet 对等互连和服务链接 (az-100-04)

请注意，AZ-103 课程中不包含以下实验：

-  Azure 事件网格和 Azure 逻辑应用 (az-101-02b)
-  实施和管理 Azure Web 应用 (az-101-02)
-  将本地 Hyper-V VM 迁移到 Azure (az-101-01b)
-  特权身份管理 (az-101-04)

**我们会做些什么？**

*	我们会在 GitHub 上发布实验说明和实验文件，方便课程作者和 MCT 进行互动。我们希望这可以帮助确保在 Azure 平台发生变化时随时更新内容。

*	这是 AZ-103“Microsoft Azure 管理员”课程的 GitHub 存储库。 

*	在每个存储库中，Instructions 文件夹中都提供了 Markdown 格式的实验指南。适当情况下，还会在 Allfiles\Labfiles 文件夹中提供完成实验所需的其他文件。并非每个课程都有相应的实验文件。 

*	每次讲授课程时，培训师都应从 GitHub 下载最新文件。培训师还应检查“问题”选项卡，查看其他 MCT 是否报告了任何错误。  

*	虽然提供了估计的实验用时，但培训师应根据受众进行检查，以确保其准确性。

*	要进行实验，需要连接 Internet 和 Azure 订阅。有关使用 Cloud Shell 的更多信息，请阅读《讲师准备指南》。 

**我们应该怎么做？**

*	如果你在教授这些课程的过程中发现了需要改进的方面，请使用“问题”选项卡提供反馈。我们会定期创建新文件以合并更改。 

**关于 AZ-103 课程的一般评论**

* 所有逻辑阵列模块中的 PowerShell 脚本都使用当前版本的 Azure PowerShell Az 模块。

* 完成每个实验后，最好取消预配任何现有资源，但不是强制要求。这有助于降低超出默认 vCPU 配额限制的风险并最大限度地降低使用费用。

* 这些区域中 Azure 区域和资源的可用性在某种程度上取决于您使用的订阅类型。要识别订阅中可用的 Azure 区域，请参阅 https://azure.microsoft.com/zh-cn/regions/offers/ 。 要确定这些地区的可用资源，请参阅 https://azure.microsoft.com/zh-cn/global-infrastructure/services/ 。这些限制可能会导致模板验证或模板部署失败，尤其是在预配 Azure VM 时。如果发生这种情况，请查看错误消息并使用不同的 VM 大小或不同的区域重试部署。

* 首次启动 Azure Cloud Shell 时，可能会提示创建 Azure 文件共享以保存 Cloud Shell 文件。如果是这样，通常可以接受默认值，这会在自动生成的资源组中创建存储帐户。请注意，如果删除该存储帐户，可能会再次发生这种情况。

* 在执行基于模板的部署之前，可能需要注册提供程序，以处理模板中所引用资源类型的预配。这是使用 Azure 资源管理器模板部署由这些资源提供程序管理的资源时所需的一次性操作（每个订阅）（如果这些资源提供程序尚未注册）。可在 Azure 门户的“订阅资源提供程序”边栏选项卡中进行注册，也可使用 Cloud Shell 运行 Register-AzResourceProvider PowerShell cmdlet 或 az provider Azure CLI 命令。

我们希望借助此 GitHub 存储库实现实验协作，并改善实验体验的整体质量。 

此致 
*Azure 管理员课件团队*
