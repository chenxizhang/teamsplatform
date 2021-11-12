# Power Platform的管理

上一章，我们通过完整的实例介绍了Power Platform与Teams Platform的完美结合，可以释放所有人（毫无疑问，也包括IT 部门的员工）的潜能，你可以通过下面的链接进行回顾。


[empower-every-person-with-power-platform.md](../users-prespective-of-the-platform/empower-every-person-with-power-platform.md)


那么，作为IT管理部门，需要掌握哪些基本的管理运营的知识呢？

## Power Platform是可以单独购买的

你可能需要首先花一点点时间了解Power Platform的授权体系。简单的说，Microsoft 365 E3, A3, E5, A5 或者Dynamics 365 的用户将默认就拥有一定的Power Platform的授权，能够立即开始使用基本服务。但所有的组件都是可以单独购买的，升级后原有的应用和流程，报表和机器人都继续使用，不需要改变任何东西。

<https://docs.microsoft.com/zh-cn/power-platform/admin/pricing-billing-skus>

## Power Platform 管理中心

Microsoft 365全局管理员，或者指定的Power Platform管理员有权访问 "Power Platform管理中心“。

![](<../.gitbook/assets/图片-262.png>)

Power Platform 管理中心访问地址是 [https://admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com)。

![](<../.gitbook/assets/图片-264.png>)

在Power Platform中，有一个重要的概念是环境，在环境中，可以定义数据库（一个或多个），这些数据在应用和流程中共享。 PowerApps 和 PowerAutomate ，Power Virtual Agents 都是以环境作为边界的。例如用户需要创建一个PowerApps的应用，它必须属于且只能属于一个环境。

![](<../.gitbook/assets/图片-263.png>)

针对环境的配置很关键，请你一定要花一些时间进行研究和学习。

![](<../.gitbook/assets/图片-265.png>)

作为管理员，你还需要特别关注容量的使用量，你可能需要考虑单独购买容量来满足员工的使用需求。

![](<../.gitbook/assets/图片-266.png>)

## Power Platform for Teams

Power  Platform 免费为Teams提供了一种新的环境类型，如下图所示。

![](<../.gitbook/assets/图片-267.png>)

当您第一次在 Microsoft Teams 中创建[应用](https://docs.microsoft.com/zh-cn/powerapps/teams/create-first-app)或[机器人](https://docs.microsoft.com/zh-cn/power-virtual-agents/teams/authoring-first-bot-teams#create-a-bot)或第一次从应用目录安装 Power Apps 应用时，将自动为所选团队创建 Dataverse for Teams 环境。 Dataverse for Teams 环境用于存储、管理和共享特定于团队的数据、应用和流。 **每个团队可以有一个环境，**团队内使用 Power Apps 应用创建的所有数据、应用、机器人和流通过该团队的 Dataverse for Teams 数据库提供。

目前一个租户，最多支持10000个Dataverse for Teams 环境。客户不需要为此付费，在管理中心看到的容量也是一直为零。但如果需要更多环境，则可能需要升级到生产版的Dataverse。

更多详情，你可以参考如下的官方文档。

<https://docs.microsoft.com/zh-cn/power-platform/admin/about-teams-environment>



