---
description: 有哪几类管理员
---

# 管理员和相关工具

我们不能孤立地谈Teams 平台的管理运营，实际上Teams作为Microsoft 365的一个组件，很多管理的思路和原则都是一致的，这既降低了IT部门管理的难度，也简化了Teams平台本身开发的难度。

既然谈IT管理运营，在具体讲解如何管理之前，我们还是要明确一下谁能进行管理。

## 管理员知多少 <a href="admins-role-list" id="admins-role-list"></a>

Microsoft 365 有一个拥有最高权限的“全局管理员”，另外其他各种各样的管理员就多达57个之多，这得益于Azure平台提供的RBAC（Role based Access Control ）架构，把角色和权限的粒度做的比较细，这样可以从源头避免滥用的管理员权限，另外用这一套机制，让每个具体的服务开发时都事半功倍。

[https://admin.microsoft.com/Adminportal/Home#/rbac/directory](https://admin.microsoft.com/Adminportal/Home#/rbac/directory)

![](<../.gitbook/assets/图片 (199).png>)

针对Teams，有如下五个不同的管理员角色。一个是全局的，另外四个是跟设备和通信有关。

![](<../.gitbook/assets/图片 (200).png>)

他们到底差异在什么地方呢？我们可以选择三个主要的管理员角色对照一下。

![](<../.gitbook/assets/图片 (201).png>)

本书主要侧重在Teams 作为应用平台这个角度来研究，所以在书中讲解到的管理员，除非特别说明，都是指上图中提到的 “ Teams 管理员” 这个角色，即对Teams完整功能都有权限。


在很多大型的跨国企业中，Teams 管理员通常是在总部，本地IT部门往往没有管理权限。


## 管理员使用的工具 <a href="admin-tools" id="admin-tools"></a>

Teams 管理员最常用的工具主要有两个，分别是：

1. **Teams 管理中心**，这个是最主要的工具，拥有所有的功能。它的访问地址是：[https://admin.teams.microsoft.com/](https://admin.teams.microsoft.com)。
2. **PowerShell** 。这个是高级工具，可以帮助管理员完成自动化任务，以及一些批量操作。建议安装最新的7.1.3或者更高版本，这是一个跨平台的版本。请通过 [https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell?view=powershell-7.1](https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell?view=powershell-7.1) 进行安装，并安装 `MicrosoftTeams` 这个模块，以及 `Microsoft.Graph` 和`Az` 这两个模块 （可选，但推荐）。

你可以通过下面的命令安装这些模块

```
Install-Module MicrosoftTeams,Microsoft.Graph,Az
```

我希望你已经对PowerShell有一定的了解，如果还没有，强烈推荐你尽快地学习一下。

<https://docs.microsoft.com/zh-cn/powershell/scripting/learn/tutorials/01-discover-powershell?view=powershell-7.1>

## 管理员认证考试

我还很推荐管理员经过系统学习后，争取能拿到下面这个官方认证。

<https://docs.microsoft.com/zh-cn/learn/certifications/m365-teams-administrator-associate/>

