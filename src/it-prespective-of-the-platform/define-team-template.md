---
description: 快速创建团队
---

# 定义和分发团队模板

前面两节详细讲解的 “应用权限策略” 和 “ 应用设置策略” 都是针对用户个人的管理，而 在Microsoft Teams中，团队（Team）是很关键的一个场景，人们通过创建不同的团队进行协作，在团队中可以根据主题创建不同的频道，这些频道既是一个可以用来聊天的所在，也是可以通过安装不同的应用，快速完成很多工作任务。

就像Office一样，现在Teams也提供各种各样的模板，可以让用户快速创建团队。

![](<../.gitbook/assets/图片 223.png>)

上面列出的模板是Teams默认自带的，下面我们分别来看一下，管理员如何定义团队模板，并且设置团队模板策略。

## 创建自定义团队模板

管理员通过 [https://admin.teams.microsoft.com/teams/templates](https://admin.teams.microsoft.com/teams/templates) 查看并定义团队模板。

![](<../.gitbook/assets/图片 224.png>)

点击上图中的“添加” 按钮进入创建模板向导

![](<../.gitbook/assets/图片 225.png>)

点击 “下一步”

![](<../.gitbook/assets/图片 226.png>)

输入基本的信息后点击“下一步”，开始细节的设置，首先是针对频道的定义

![](<../.gitbook/assets/图片 227.png>)

你可以为这个模板添加多个频道，并且为频道添加选项卡应用。这些应用的属性甚至可以预先设置好，请点击应用标题行中的笔形编辑按钮。

![](<../.gitbook/assets/图片 228.png>)

除了为频道添加选项卡应用，还可以添加想为加入该团队的用户默认安装的其他应用，如下图所示

![](<../.gitbook/assets/图片 229.png>)

点击底部的“提交”按钮完成模板定义。

![](<../.gitbook/assets/图片 230.png>)

顺便说一下，你也可以通过PowerShell创建或修改团队模板

```
New-CsTeamTemplate
# 请参考 https://docs.microsoft.com/zh-cn/powershell/module/teams/new-csteamtemplate?view=teams-ps
```

## 分发团队模板

模板定义好了，默认情况下所有用户都能立即使用这些模板了。但管理员也能通过“模板策略” 来定义哪些用户能用哪些模板。

管理员访问 [https://admin.teams.microsoft.com/policies/template-policy](https://admin.teams.microsoft.com/policies/template-policy) 可以查看并定义模板策略。

和其他策略一样，“全局” 这个策略是对所有用户默认启用的，同时默认也包含了所有的模板。如果你想隐藏某些模板，可以点击该策略，然后进行编辑。

![](<../.gitbook/assets/图片 231.png>)

你可以选中某个模板，点击“隐藏”按钮。如果想要恢复也很简单，在下方的 ”隐藏的模板“ 中选中模板，然后点击”显示“按钮即可。

目前团队模板尽可能分配给单个用户（或者一个一个地添加用户），未来有可能会实现组分配。

![](<../.gitbook/assets/图片 232.png>)

## 用户基于模板创建团队

![](<../.gitbook/assets/图片 233.png>)

选择中意的模板后，进入详情页面

![](<../.gitbook/assets/图片 234.png>)

点击 ”下一条“（这里应该是下一步），进入团队类型设置

![](<../.gitbook/assets/图片 235.png>)

点击专用或公共，进入详情速览和频道自定义页面

![](<../.gitbook/assets/图片 236.png>)

点击“创建”按钮完成向导，在创建好的团队中我们能看到已经添加好的频道，以及应用。

![](<../.gitbook/assets/图片 237.png>)

