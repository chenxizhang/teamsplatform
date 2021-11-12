---
description: 任何人都可以是开发者
---

# 用Power Platform释放你的潜能

前面的小节，我给大家介绍了Teams这个本质上是由各种应用组装起来的平台，介绍了常用的内置应用，也提到了如何上传一个自定义应用。那么，作为普通用户，你是否可以根据业务需要定制自己想要的应用呢？

可以的，微软给出的答案就是Power Platform。这是另外一个强大的平台，关于该平台与Teams平台进行整合的一些背景和意义，请参考此前的小节。


[power-platform-integration.md](../teams-platform-overview/power-platform-integration.md)


如果你已经是Office 365 E3或A3，以及更高级别授权的用户，你已经可以免费使用Power Platform。如果不是，则可能需要单独购买，请咨询贵公司的管理员，或者相关的销售人员。

你可以这个链接（[https://portal.office.com/account/?ref=MeControl](https://portal.office.com/account/?ref=MeControl)）随时检查当前你的账号是否具备使用Power Platform，请在打开的页面中分别搜索如下 "Power" 这个关键字，查看是否有匹配到的条目。

如果你有相关的使用授权，这一节我将用一个完整的故事，带领大家体验一下Power Platform的四个核心组件，你不需要写一行代码，也能开发自己的自定义应用、自动化流程，并且基于业务数据进行分析，还可以定制一个问答机器人呢。

## 开发一个用于客户回访的自定义应用

阿波和团队小伙伴经常需要去拜访客户进行回访，他们需要把相关的客户反馈记录起来，并且在团队内部很好的共享。以前是用Excel文件来做这样的事情，每次都要邮件来回发送，合并文档也很麻烦，同时最要命的是，在手机上基本上编辑的体验真的太痛苦了。阿波想自己开发一个简单的应用来解决这个问题。他听说Power Apps很容易上手，只要定义一下所需要的业务数据，就可以快速生成应用。

基于Power Apps 开发新型应用，是一种无代码开发的新方式。**一般来说，在开始之前，你只要把数据保存在哪里，以及具体需要哪些数据想清楚就可以了。**至于到底怎么设计界面，以及如何实现数据的增删改查，工具可以帮你事半功倍。

在一些简单的业务场景，你可以利用SharePoint自带的列表来保存数据。例如阿波定义了下面这样一个列表来爆粗你客户回访记录。这个列表有五个字段：

1. **客户名称**。这个列从默认的“标题”列改名而来，必填项。
2. **回访日期**。日期类型，默认填写为当天日期。
3. **操作员**。人员类型，可以搜索来输入，确保是合法的员工信息。
4. **客户反馈**。文本类型。
5. **整体评价**。单选项，有高、中、低三个选项。


这个列表最好建立在某个团队所对应的SharePoint网站中，这样的话，所有团队中的用户就默认拥有了访问的权限，不需要额外的进行授权了。


![](<../.gitbook/assets/图片-132.png>)

接下来，回到Teams，阿波按照上一节的提示，在Teams里面安装 ”Power Apps“ 这个应用。

![](<../.gitbook/assets/图片-120.png>)

安装完成后，这个应用会自动打开，然后点击主界面上的 ”立即启动“ 按钮。

![](<../.gitbook/assets/图片-121.png>)

在弹出来的”创建应用“窗口中，阿波需要选择要为哪个团队进行开发。

>
如果需要为个人开发应用，而不是团队，需要在Power Apps的网页版本中进行，然后需要额外的方式部署到Teams。在第四章会介绍这方面的内容。


![](<../.gitbook/assets/图片-122.png>)

为应用起一个名字后，点击”保存“按钮，进入下一步，会是一个空白的应用界面。接下来可以按照下图所示的步骤添加SharePoint 列表作为数据源。

![](<../.gitbook/assets/图片-133.png>)

接下来把这个数据源跟PowerApps默认生成的界面连接起来。请按照下图所示进行操作。

![](<../.gitbook/assets/图片-134.png>)

接下来把字段放在界面上来，这将最终完成数据源的对接。如下图所示。

![](<../.gitbook/assets/图片-137.png>)

你已经看到自动生成的操作界面了，美中不足的是第一个字段，它的标题默认为Title。你可以通过下面的方法修改它为“客户名称”。

![](<../.gitbook/assets/图片-136.png>)

一个简单的应用就这样做成了。点击工具栏中的 ”预览“ 按钮，可以立即对这个应用进行访问，输入并查看数据，如下图所示。

![](<../.gitbook/assets/图片-126.png>)

一切看起来都挺不错，阿波已经等不及要分享给小伙伴们进行使用了，点击 ”发布到Teams“ 这个按钮。

![](<../.gitbook/assets/图片-127.png>)

在弹出的对话框中，选择”下一步“

![](<../.gitbook/assets/图片-128.png>)


这里的General，指的是每个团队默认的那个频道。在中文版本中，叫做常规（例如下图所示）。但在这个界面又没有完全翻译好。


PowerApps 会自动列出当前团队中所有的频道，阿波选择将这个应用作为第一个频道的选项卡添加进去。点击上图所指示的加号，并且点击”保存并关闭“后，阿波切换到对应的团队和频道，发现已经多出来一个选项卡”客户回访登记“，点击进入后，阿波和他的小伙伴可以很方便地进行数据填写和浏览了。

![](<../.gitbook/assets/图片-129.png>)

更加让人惊喜的是，小伙伴们立即就可以在手机端使用这个应用来填写和浏览数据了，而这个界面是完全自适应的。

![](<../.gitbook/assets/图片-130.png>)

开发并发布这个 ”客户回访登记“ 应用的过程，阿波一路点击按钮走下来，没有编写哪怕一行代码，前后大概用了十分钟的时间。默认生成的这个应用带有完整的新增数据，修改数据，浏览数据（列表），以及删除数据等功能，可谓是麻雀虽小，五脏俱全。

你可能会说，是不是还可以增加更多有意思的功能啊？例如自动用当前用户的名字填到”操作员“ 这个字段中去，以及控制用户能看所有人的数据，但只能改自己提交的数据？我能不能修改界面的布局？...... 完全没有问题，而且同样不难实现。但讲解这些细节就超出了本书的范围，你可以通过下面的官方文档稍作学习以便掌握。

<https://docs.microsoft.com/zh-cn/powerapps/>

## 在收到客户的差评时引起重视

师傅在看了阿波的演示后，感到非常高兴，并且提出一个小小的要求，如果某个客户的整体满意度是”低“的话，他希望得在团队频道中用消息形式发送一个通知给他，并且通过 @ 他提醒他。

利用Power Automate，可以监测小伙伴们提交回访记录的事件，并且通过条件判断（整体反馈是否为“低”），决定是否给师傅发送通知消息。

阿波在Teams中，搜索并安装 “Power Automate”这个应用。

![](<../.gitbook/assets/图片-131.png>)

安装成功后打开的主页，默认把列出了团队现有的流程，以及常见的Teams相关的流程模板。

![](<../.gitbook/assets/图片-139.png>)

点击上图中红色框出的这个模板，阿波开始第一个自己的流程设计。

![](<../.gitbook/assets/图片-140.png>)

输入一个易于理解的“流名称”，并确认登录后，点击“继续”按钮进入下一步。

![](<../.gitbook/assets/图片-141.png>)

在这个对话框中，选择对应的信息，然后点击 ”创建流“ 按钮完成操作。

接下来，阿波尝试通过之前创建的应用提交了一个客户回访记录，如下图所示。

![](<../.gitbook/assets/图片-143.png>)

提交了这个记录后，不一会儿阿波在常规频道中看到了一个消息，如下图所示。

![](<../.gitbook/assets/图片-144.png>)

看起来流程已经在工作了，但这并不是师傅所需要的。他希望通过一个更加直观的消息通知他，并且要能@他以便引起重视，这样的需求如何实现呢？阿波可以回到Power Automate 应用的主页，对这个流程进行定制。

![](<../.gitbook/assets/图片-145.png>)

点击”客户回访-差评通知师傅“ 这个流程，进入详情页面。

![](<../.gitbook/assets/图片-146.png>)

点击 ”编辑“ 按钮，进入编辑视图。

![](<../.gitbook/assets/图片-148.png>)

这里有两个设计好的组件，第一个是用来触发流程的，第二个则是用来给频道发送卡片消息的。在当前这个场景，我们可以把第二个组件删除掉，并且点击 ”新步骤“ 添加需要的步骤。下图是最终的版本，包含了一个条件判断（条件是“整体评价 value” 等于 “低”），然后在左侧分支中，用了一个“在聊天或频道中发布消息” 的步骤。

![](<../.gitbook/assets/图片-149.png>)

保存这个流程后，再次保存新的客户回访记录时，根据条件会触发如下的通知。

![](<../.gitbook/assets/图片-150.png>)

看起来不错吧，上面的例子基于HTML格式定义，还可以用一个自适应卡片来自定义消息。设计这个流程又花了阿波十分钟左右，如果你根据上面的截图，还无法完成自己的流程，那么请抽一点时间学习一些入门知识，你值得拥有。

<https://docs.microsoft.com/zh-cn/power-automate/getting-started>

## 透过数据发现信息，快速建模分析

阿波的工作取得了很好的进展，得到了师傅和小伙伴们的一致表扬，大家都逐渐习惯了直接在Teams中随时随地填写客户回访记录。很快这个列表的数据就越来越多了，阿波遇到一个新的挑战，就是如何快速在对这些数据进行分析，例如看看按月统计的回访量的变化，好中差评的分布，各个小伙伴们的工作负荷等。他想到了Power BI来进行这项工作。

>
对于一些简单的部门级应用，将数据直接放在SharePoint列表（甚至Excel文件）是一个非常好的方案，因为你不需要了解负责的数据库知识。但是要注意，Excel文件在多人并发时可能遇到一些限制，而SharePoint列表的条目数不建议超过5000，如果你预计数据量将持续增长会超过这个规模，建议提前做好相关数据库方面的考虑，或者直接用Power Platform的Dataverse这个服务。详情请参考 [https://powerplatform.microsoft.com/zh-cn/dataverse/](https://powerplatform.microsoft.com/zh-cn/dataverse/) 。


![](<../.gitbook/assets/图片-151.png>)

你可以有多种方式使用Power BI。目前在Teams中也有一个专门的Power BI的应用，但这个应用还在完善之中，不能直接连接到SharePoint数据源。所以本例中，我建议大家用 Power BI Desktop进行报表开发和设计，然后发布到Power BI Service，并最终在Teams中进行展现。

首先，请在这里（[https://powerbi.microsoft.com/zh-cn/desktop/](https://powerbi.microsoft.com/zh-cn/desktop/)）下载Power BI Desktop, 然后启动它，并用你的账号登录。

![](<../.gitbook/assets/图片-152.png>)

选择 ”获取数据“，在下拉菜单中选择 ”更多...“

![](<../.gitbook/assets/图片-153.png>)

选择”联机服务“中的 ”SharePoint Online 列表“，点击”连接“ 按钮进入下一步

![](<../.gitbook/assets/图片-154.png>)

输入你的SharePoint网站地址，然后点击 ”确定“按钮

![](<../.gitbook/assets/图片-155.png>)

选择 ”Microsoft 账号“，并且以你当前用户身份登录，然后点击 ”连接“按钮

![](<../.gitbook/assets/图片-156.png>)

选择 ”客户回访记录“ 这个列表，然后点击 ”转换数据“， 对数据源进行一定的加工。

![](<../.gitbook/assets/图片-157.png>)

在这个转换数据的窗口，我们会删除掉很多不相干的字段，并且对”回访日期“的数据类型进行设置，以及对操作员这个字段进行一个条件格式处理，因为默认提供的值是一个id，而不是用户的姓名。

这里的步骤很多，我很难一步一步地截图，如果你不知道如何操作，可以点击下图中的 ”高级编辑器“ 按钮

![](<../.gitbook/assets/图片-158.png>)

对照下面我提供的代码，你从第三行开始复制，粘贴到你的编辑器里面。而第八行涉及到人员信息的对应关系，你可能需要自己进行一定的修改。

```
let
    Source = SharePoint.Tables("https://chinateamscommunity.sharepoint.com/sites/msteams_7d1b2b/", [Implementation=null, ApiVersion=15]),
    #"37da01c3-068c-4177-b1fe-172751eaf4eb" = Source{[Id="37da01c3-068c-4177-b1fe-172751eaf4eb"]}[Items],
    #"Renamed Columns" = Table.RenameColumns(#"37da01c3-068c-4177-b1fe-172751eaf4eb",{{"ID", "ID.1"}}),
    #"Reordered Columns" = Table.ReorderColumns(#"Renamed Columns",{"FileSystemObjectType", "Id", "ContentTypeId", "ServerRedirectedEmbedUri", "ServerRedirectedEmbedUrl", "ID.1", "Title", "Modified", "Created", "AuthorId", "EditorId", "OData__UIVersionString", "Attachments", "GUID", "ComplianceAssetId", "OData_回访日期", "OData_操作员Id", "操作员StringId", "OData_客户反馈", "OData_整体评价", "FirstUniqueAncestorSecurableObject", "RoleAssignments", "AttachmentFiles", "ContentType", "GetDlpPolicyTip", "FieldValuesAsHtml", "FieldValuesAsText", "FieldValuesForEdit", "File", "Folder", "LikedByInformation", "ParentList", "Properties", "Versions", "Author", "Editor", "OData_操作员"}),
    #"Removed Columns" = Table.RemoveColumns(#"Reordered Columns",{"FileSystemObjectType", "Id", "ContentTypeId", "ServerRedirectedEmbedUri", "ServerRedirectedEmbedUrl", "ID.1", "Modified", "Created", "AuthorId", "EditorId", "OData__UIVersionString", "Attachments", "GUID", "ComplianceAssetId", "FirstUniqueAncestorSecurableObject", "RoleAssignments", "AttachmentFiles", "操作员StringId", "ContentType", "GetDlpPolicyTip", "FieldValuesAsHtml", "FieldValuesAsText", "FieldValuesForEdit", "File", "Folder", "LikedByInformation", "ParentList", "Properties", "Versions", "Author", "Editor", "OData_操作员"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns",{{"OData_回访日期", type date}}),
    #"Added Conditional Column" = Table.AddColumn(#"Changed Type", "操作员", each if [OData_操作员Id] = 10 then "师傅" else if [OData_操作员Id] = 12 then "阿波" else if [OData_操作员Id] = 13 then "大师" else if [OData_操作员Id] = 14 then "虎妞" else null),
    #"Renamed Columns1" = Table.RenameColumns(#"Added Conditional Column",{{"Title", "客户名称"}, {"OData_回访日期", "回访日期"}}),
    #"Removed Columns1" = Table.RemoveColumns(#"Renamed Columns1",{"OData_操作员Id"}),
    #"Renamed Columns2" = Table.RenameColumns(#"Removed Columns1",{{"OData_客户反馈", "客户反馈"}, {"OData_整体评价", "整体评价"}})
in
    #"Renamed Columns2"
```

修改完数据定义后，点击下图中的 ”关闭并应用“这个按钮以便回到报表设计界面。

![](<../.gitbook/assets/图片-159.png>)

通过可视化的界面，使用”堆积柱状图“ 和 ”饼图“ ，阿波设计了下面这样一个简单的报表，可以看到客户回访的数量，以及反馈的分布，还有不同人员的工作量等信息。

![](<../.gitbook/assets/图片-160.png>)

然后他决定将报表发布到Power BI Service，以便分享给师傅和小伙伴们进行查看。

![](<../.gitbook/assets/图片-162.png>)

发布成功后，点击下面的链接在Power BI  Service 中进行查看。

![](<../.gitbook/assets/图片-167.png>)

为了让团队成员能够访问，阿波还需要进行报表分享。

![](<../.gitbook/assets/图片-166.png>)

回到Teams客户端中来，阿波定位到对应的频道，然后进行添加选项卡操作，如下图所示。

![](<../.gitbook/assets/图片-164.png>)

在弹出的对话框中，选择对应的报表，点击”保存” 按钮

![](<../.gitbook/assets/图片-165.png>)

这个报表就可以直接在频道中快速访问到了。

![](<../.gitbook/assets/图片-163.png>)

你可以通过如下的链接学习Power BI的更多知识和技巧。

<https://powerbi.microsoft.com/zh-cn/learning/>

## 常见问题解答机器人

通过Power Apps 快速开发基于SharePoint列表的应用，Power Automate 实现智能化流程，Power BI 轻松对数据进行建模和分析，阿波和他的小伙伴们实实在在地感受到了Power Platform 带来的价值。

过了一段时间，在一次内部会议上，大家讨论拜访客户的心得，有一些新来的小伙伴特别想了解面对不同的客户，提出的不同问题应该如何解答，他们七嘴八舌地问师傅，问阿波...... 阿波转念一想，如果能把这些常见的问题整理起来，随时可以让新同事自己去查询，该多好呀。虽然以前也整理过类似的Word文档，但用文档来分享毕竟不方便，版本也很难控制。

阿波想到了虚拟机器人这个技术，Power Virtual Agents （简称PVA）可以很容易地实现这个需求，并且一键跟Teams结合起来。

PVA需要单独购买授权，目前部分用户可以申请试用，请尝试访问 [https://powerva.microsoft.com/](https://powerva.microsoft.com) 进行申请并开通。

>
虽然在Teams中也有Power Virutal Agents这个应用，但目前该应用功能还不完善。


![](<../.gitbook/assets/图片-168.png>)

选择国家/地区后，点击 “开始免费试用”按钮进入下一步

![](<../.gitbook/assets/图片-169.png>)

输入必要的信息，点击“创建” 按钮完成机器人的初始化过程

![](<../.gitbook/assets/图片-170.png>)

在PVA中，通过“主题（Topic）”的概念表示一系列用户可能会问的问题，针对某个主题，我们可以设计一系列的回复，可以是预先设置好的短语，也可以去后台系统进行查询。现在就可以点击上图中的“转到主题” 进行配置。

![](<../.gitbook/assets/图片-171.png>)

建议把默认创建好的四个自定义主题关闭掉，然后我们添加真正需要的主题。例如客户经常需要了解公司产品的下一步计划，这就是一个主题，他们可能会有多种问法，下图演示了如何新建主题，并且设置名称，以及常见问法（尽量不要少于五个）。

![](<../.gitbook/assets/图片-172.png>)

点击上图中的 “转到创作画布” ，可以用来定义机器人如何响应用户的问题。作为演示目的，在本例中我定义了一个固定的文字回答，然后结束会话。

![](<../.gitbook/assets/图片-173.png>)

作为机器人设计者，我随时可以在测试机器人窗口进行验证。

![](<../.gitbook/assets/图片-174.png>)


这里的提问，并不是要精确地匹配此前定义的短语，PVA是人工智能机器人，他会尝试对用户的提问进行理解。例如你也可以这样问 “能否介绍一下新产品的计划”，虽然这个短语并没有定义，但PVA可以理解你的意思其实是想要问产品路线图，所以也会看到同样的结果。


完成基本的测试后，要让外部能使用到这个机器人，你需要对齐进行发布。

![](<../.gitbook/assets/图片-175.png>)

PVA这个机器人真正能做到一次开发，处处运行。我们这里当然最关心的还是，如何让他能在Teams中进行集成。你可以通过下面的操作来启用Teams集成。

![](<../.gitbook/assets/图片-176.png>)

启用成功后，PVA会提供多种方式对这个机器人进行分发。

![](<../.gitbook/assets/图片-177.png>)

如果只是自己或者团队小范围使用，你可以把直接分享上图中的链接，或者直接点击 “打开机器人”即可。

![](<../.gitbook/assets/图片-178.png>)

点击 “添加” 按钮后，就可以跟机器人愉快地交谈了。

![](<../.gitbook/assets/图片-179.png>)

以上通过简单的例子介绍了如何创建机器人、定义问答主题、测试和调试、发布、集成到Teams的过程，相信可以一窥PVA的强大和灵活，更多细节功能，你可以通过下面的官方进行学习。

<https://docs.microsoft.com/zh-cn/power-virtual-agents/fundamentals-what-is-power-virtual-agents>

## 结语

Power Platform 是微软的新一代无代码/低代码平台，它可以让普通用户充分利用自己对于业务场景的理解，用所见即所得的工具，设计自己需要的应用程序，和流程，并且满怀信心地对数据进行分析，甚至还可以从零开始构建一个人工智能机器人。而与Teams平台的无缝整合，将更好地释放所有人的潜能。
