---
description: 在外部系统中集成Microsoft Teams
---

# Microsoft Graph

在前面的小节中，我们分别了解了Teams平台的五种能力（选项卡，机器人，消息扩展，连接器，通知），客户或合作伙伴可以利用这些能力，在Microsoft Teams客户端中实现各种各样的创新场景，扩展它，让它更好地帮助员工沟通协作，更快地完成任务。

## 典型场景

而Microsoft Graph则提供了另外一种能力，就是可以在你的外部系统中去整合Microsoft Teams的能力。考虑一下如下的场景：

1. 有一家著名的航空公司，他们全面使用Microsoft Teams,。不管是在办公室的员工，还是包括乘务组，机组等外勤人员（当然，他们会使用移动设备），都使用同一个平台进行沟通协作。
2.  每天晚上，根据排定好的航班信息，自动用航班号为名称创建一个团队，并且创建多个频道（有的是公开的，有的是私有的），例如机组，乘务组，安保组，地勤，调度，及客户服务等，并且把对应的员工添加到合适的频道。

    > &#x20;确保所有人都在一个团队里面，可以保证信息的透明和沟通的效率，通过不同的频道组织不同的事务，可以让沟通更加有序。
3. 为了让员工专注于为乘客服务，而不是学习怎么使用工具，这些动态创建好的团队和频道中，需要预先安装好所有必要的应用。不同的频道有对应不同的应用集合。
4. 在航班准备以及执飞过程中，重要的通知信息，会及时地推送到对应的团队或频道，甚至个人。紧急情况下，可以直接发起视频对话。
5. 在执飞完成后，需要快速地把该航班所有的沟通协作过程，以及产生的数据，按照航空行业的标准进行归档，以便进一步进行分析，或在法律规定的时间内还可以进行回溯。

以上提到这些场景，基本上都是可以通过Teams现有的界面手工地完成，但如果你站在每天要起降成百上千架飞机的航空公司的实际情况来看，就会发现人工来操作根本是不现实的。而通过 Microsoft Graph提供的接口，客户可以很方便地在现有的空管系统界面中，把上述的场景整合进去。通过这种无缝的整合，既可以发挥Microsoft Teams 作为终端用户日常沟通协作工具的优势，又可以把很多繁琐的创建、配置、维护、管理的工作自动化。

![](<../../.gitbook/assets/图片 (49).png>)

这个案例中主要用到的Microsoft Graph场景和接口对应关系如下，请参考

| 场景   | 接口                                                                                                                                                                                                     |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 创建团队 | [https://docs.microsoft.com/zh-cn/graph/api/team-post?view=graph-rest-1.0\&tabs=http](https://docs.microsoft.com/zh-cn/graph/api/team-post?view=graph-rest-1.0\&tabs=http)                             |
| 创建频道 | [https://docs.microsoft.com/zh-cn/graph/api/channel-post?view=graph-rest-1.0\&tabs=http](https://docs.microsoft.com/zh-cn/graph/api/channel-post?view=graph-rest-1.0\&tabs=http)                       |
| 应用安装 | [https://docs.microsoft.com/zh-cn/graph/api/team-post-installedapps?view=graph-rest-1.0\&tabs=http](https://docs.microsoft.com/zh-cn/graph/api/team-post-installedapps?view=graph-rest-1.0\&tabs=http) |
| 用户管理 | [https://docs.microsoft.com/zh-cn/graph/api/channel-post-members?view=graph-rest-1.0\&tabs=http](https://docs.microsoft.com/zh-cn/graph/api/channel-post-members?view=graph-rest-1.0\&tabs=http)       |
| 读取消息 | [https://docs.microsoft.com/zh-cn/graph/api/channel-list-messages?view=graph-rest-1.0](https://docs.microsoft.com/zh-cn/graph/api/channel-list-messages?view=graph-rest-1.0)                           |
| 发送通知 | [https://docs.microsoft.com/zh-cn/graph/api/chatmessage-post?view=graph-rest-1.0](https://docs.microsoft.com/zh-cn/graph/api/chatmessage-post?view=graph-rest-1.0)                                     |
| 团队归档 | [https://docs.microsoft.com/zh-cn/graph/api/team-archive?view=graph-rest-1.0\&tabs=http](https://docs.microsoft.com/zh-cn/graph/api/team-archive?view=graph-rest-1.0\&tabs=http)                       |

## 扩展阅读

Microsoft Graph是一个宝库，客户和合作伙伴如果能掌握这个能力，将可以极大地调动包括Microsoft Teams在内的微软云端服务的能力为你所用，实现更多有高附加值的业务场景。

本章不会对如何使用Microsoft Graph的细节进行展开，但提示如下的一些要点作为参考：

1.  Microsoft Graph 是一套统一的服务接口，不仅仅为 Microsoft Teams 服务。所有这些服务，都通过 https://graph.microsoft.com 这个地址对外提供服务。请参考[https://docs.microsoft.com/zh-cn/graph/overview](https://docs.microsoft.com/zh-cn/graph/overview)。&#x20;

    > 中国版本的Microsoft 365（目前还不包含Microsoft Teams）也支持 Microsoft Graph，但它的服务地址是 [https://microsoftgraph.chinacloudapi.cn](https://microsoftgraph.chinacloudapi.cn)。
2. Microsoft Graph 支持多种身份验证方式，它可以用当前登录的用户身份（以标准的OAuth认证流程实现），也可以用一个服务账号（用账号密码，或证书来做认证），不同的身份所对应的权限有细节的控制。请参考 [https://docs.microsoft.com/zh-cn/graph/auth/](https://docs.microsoft.com/zh-cn/graph/auth/)。
3. Microsoft Graph 是一个标准的REST API，支持任意的开发平台和语言访问，微软官方也提供了一系列的SDK 甚至标准组件库以便加速开发。请参考 [https://developer.microsoft.com/zh-cn/graph/gallery/?filterBy=Samples,SDKs](https://developer.microsoft.com/zh-cn/graph/gallery/?filterBy=Samples,SDKs) 。&#x20;
4. Microsoft Graph还支持批量数据访问，以便对数据进行集中的收集、建模和分析。这个可以跟Azure有关的服务相结合来实现。请参考 [https://docs.microsoft.com/zh-cn/graph/data-connect-overview](https://docs.microsoft.com/zh-cn/graph/data-connect-overview)。
