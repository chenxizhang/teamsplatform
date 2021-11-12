---
description: 通过数据，掌握规律
---

# 使用量分析

作为管理员，你随时可以查看整个公司使用Teams的统计数据，并且对其进行分析，以便掌握规律，帮助公司做出更好的决策。

## 通过管理中心查看并下载报告 <a href="admincenter" id="admincenter"></a>

通过登录到 [https://admin.teams.microsoft.com/analytics/reports](https://admin.teams.microsoft.com/analytics/reports) ，你可以进入“分析和报告“中心，在这里列出了常见的报表，如下图所示

![](<../.gitbook/assets/图片 (181).png>)

你还可以针对报表选择不同的日期范围，目前在管理中心支持：最近7天，最近30天，过去90天的数据。

>
不同的报表对应的日期范围选项可能不一样。


![](<../.gitbook/assets/图片 (182).png>)

点击“运行报表”按钮后，即可在线查看到所选报表的数据。

![](<../.gitbook/assets/图片 (183).png>)

如果你希望保存原始数据以便进一步分析（例如通过的图表展示），请点击上图中所示的 ”导出到Excel“ 按钮完成下载。

![](<../.gitbook/assets/图片 (184).png>)

点击上图中的 “下载” 按钮，即可得到一个CSV文件，其中就包含了当前报表的原始数据。

![](<../.gitbook/assets/图片 (185).png>)

## 使用Microsoft Graph 读取数据 <a href="msgraph" id="msgraph"></a>

通过管理中心查看报表进行分析非常方便，与此同时这些报表也可以通过Microsoft Graph接口进行访问

<https://docs.microsoft.com/zh-cn/graph/api/resources/report?view=graph-rest-1.0>

针对Teams相关的报表，主要有如下两类：设备使用情况 和 用户活动。

![](<../.gitbook/assets/图片 (187).png>)

具体来说，每个接口都有一个访问地址，用户需要得到授权（要么是委派权限，要么是应用权限）才可以访问对应的数据。例如下面 [这个接口](https://docs.microsoft.com/zh-cn/graph/api/reportroot-getteamsuseractivityuserdetail?view=graph-rest-1.0) 用来访问 Teams 用户活动的详细信息。

![](<../.gitbook/assets/图片 (188).png>)



这些接口既是面向开发者的，也是面向管理员的。作为开发者来说，只要熟悉一些HTTP的基本知识，并且了解如何进行OAuth身份认证，就可以很方便地通过编程的方式访问这些接口，将其整合到自己的应用系统中。那么，对于管理员来说，这些接口到底怎么用呢？简单地说，你可以用PowerShell来实现。

## 通过PowerShell脚本查看和下载报表 <a href="powershell" id="powershell"></a>

你可以有多种方式运行PowerShell，但我这里统一推荐你使用最新的7.1这个版本，该版本不仅仅可以在Windows上面运行，而且还可以在Mac或者Linux桌面版运行。

![](<../.gitbook/assets/图片 (186).png>)

请参考下面的链接进行安装

<https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell?view=powershell-7.1>

安装好PowerShell后，你需要安装用来访问Microsoft Graph的模块, 请在PowerShell窗口中输入并执行如下命令：

```
Install-Module Microsoft.Graph
```

要访问Microsoft Graph的API，你首先需要登录，并且申请有关的权限，请查阅对应的接口文档中的权限说明，本例的Reports.Read.All这个权限是对应报表接口的。

```
Connect-Graph -Scopes "Reports.Read.All"
```

执行此命令后会得到一个提示，需要你在浏览器中进行登录

![](<../.gitbook/assets/图片 (190).png>)

你必须在两分钟内完成登录和授权

![](<../.gitbook/assets/图片 (189).png>)

点击下一步，使用管理员账号进行登录并且进行授权。如果是第一次登录，你将看到如下的提示。

![](<../.gitbook/assets/图片 (191).png>)

点击“接受”按钮即可完成授权，此时可以回到PowerShell 窗口。执行下面的命令，可以获取到过去30天的用户活动统计。

```
Invoke-GraphRequest -Uri "v1.0/reports/getTeamsUserActivityUserDetail(period='D30')" -OutputFilePath c:\temp\teamsuseractivity_d30.csv
```

默认导出的这个文件是CSV格式的，请参考下面的结果。

![](<../.gitbook/assets/图片 (192).png>)


period可以有四个选项，分别是 D7,D30,D90,D180， 代表7天，30天，90天，180天的数据聚合, 请注意，目前在管理中心只能下载最多90天的数据聚合，但通过接口或PowerShell可以最多获取180天。


这一节介绍了三种不同的方法，让Teams管理员可以通过报表公司或者员工使用Teams的情况，包括聊天的活跃度，使用设备的情况，以及应用活跃度等信息。这几种方式中，通过管理员中心的方式是最简单的，也最直观的，目前还是报表最完整的。另外，有一部分报表（例如设备统计，用户活动统计）也可以通过Microsoft Graph的接口来访问，这对于需要定期导出数据的场景来说非常有用。
