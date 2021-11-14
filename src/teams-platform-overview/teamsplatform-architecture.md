# Microsoft Teams 平台架构

要理解Microsoft Teams 平台以及它给客户或合作伙伴提供的机会，我们仍然要先花一点时间看看这个应用和平台本身是怎么构建起来的。要讲清楚这个，可以另外写一本书，我这里引用在2019年11月份举行的Microsoft Ignite大会上，由 Teams 平台的架构师 Bill bliss所公开分享的内容。请参考下面的链接（英文）：

> 我很高兴跟 Bill 在他位于贝尔维尤的办公室进行过亲切的交流。

<https://techcommunity.microsoft.com/t5/microsoft-teams-events-blog/microsoft-ignite-live-blog-brk3215-microsoft-teams-architecture/ba-p/983346>

Microsoft Teams在全世界一百多个国家或地区同时提供服务，支持五十几种不同的语言，满足不同地区的安全合规要求，支持跨平台和终端设备。那么到底是什么样的架构才能支撑这种级别的应用服务呢？

## 基于Azure平台进行构建

Bill 在演讲中提到，“如果没有Azure，我们根本无法交付Microsoft Teams这个产品”。Microsoft Azure 是经过验证的成熟可靠的智能云平台，尤其在PaaS服务这个层面。Microsoft Teams在架构选型时优先考虑尽可能利用这些基础能力进行构建，使得它天生具备了安全合规，弹性可扩展，高可用性等特点，并且可以在极短的时间内就开发出来，投入市场。

![](<../.gitbook/assets/图片 3.png>)

## 重用Microsoft 365 核心服务

在 上一节 我已经提到了，Microsoft Teams的功能，大多都是基于SharePoint，OneDrive，Outlook，Skype for Business (或Skype）既有的能力实现的，它没有重新开发一套，而是直接将它们整合了起来，从下图可以清晰地看到这一点。

![](<../.gitbook/assets/图片 4.png>)

## 基于开源技术构建

这些年微软全面拥抱开源，在Microsoft Teams 的开发过程中得到了明显的体现。从下图看到的Teams 客户端的架构中，大量地使用了开源技术和组件。值得注意的是，在构建这个客户端时，走了一些弯路，一开始是用Angular编写的前端，但现在已经花了一两年的时间要重构为React版本。

Angular 和 React都很流行，但严格意义上他们并不是同一种东西，Angular是一个完整的框架，包括了MVC的全部实现，而React是一个视图库（View， UI Library），更加轻，更加具有灵活性吧。微软现在很多应用的前端（包括Office 365全系列），都大多使用React来编写，或者混合使用多种技术。这里 ([https://docs.microsoft.com/zh-cn/dotnet/architecture/modern-web-apps-azure/common-client-side-web-technologies](https://docs.microsoft.com/zh-cn/dotnet/architecture/modern-web-apps-azure/common-client-side-web-technologies) ) 有一篇介绍。

![](<../.gitbook/assets/图片 5.png>)

## 组件化的应用架构

从设计的源头，Microsoft Teams客户端就是由一个一个应用组合起来的，像搭积木一样。例如一般的用户登录Micrsooft Teams后默认能看到几个工具栏是“活动”，“聊天”，“团队”，“文件”，“日历”，“会议”等，

![](<../.gitbook/assets/图片 28.png>)

其实这些是因为每个租户配置了这几个应用，管理员可以根据需要增加或者修改这些默认的应用清单以及他们要显示的顺序，下图红色圈出的 “Workhub” 就是一个例子。除此之外，用户也可以随时自己安装喜爱的应用。

![](<../.gitbook/assets/图片 27.png>)

除了上面这些可以看到的应用，还有一些是不可见（或者说不可配置，默认就必须安装的）应用，如果有兴趣，可以参考下面这个列表了解一下。

[默认安装的Teams应用列表](../.gitbook/assets/teamsdefaultinstalledapps.json)


综上所述，在构建Microsoft Teams 平台时，产品组采取了明智的策略，即尽可能发挥自身的优势，利用现有的资产，使得这个应用乃至平台能够在冷启动的情况下，快速开发出来并进入市场，赢得了宝贵的时间。而拥抱开源则和组件化的应用架构也为平台后续的发展奠定了坚实的基础。这些都是很高层次的原则，接下来我们具体看一下Microsoft Teams平台提供的能力。

