---
description: 灵活并且强大
---

# 基于策略的管理

在Teams管理配置过程中，大量地使用了 “**策略（Policy）**”的概念，而且可能已经形成了一个设计范式。

1. 平台的不同服务，允许管理员以”策略“的形式定义管理规则
2. 这些策略具备多个层级，并且支持按具体用户分配，也支持按组分配
3. 相关的服务在运行期间，将首先检测当前用户所被分配的策略，以便决定哪些功能可用，哪些不可用
4. 平台可以基于策略对用户行为进行审核【目前还没有】


基于策略的管理，跟很多公司的管理理念是契合的，在英语中Policy的说法很常见，常被理解为策略或规则吧，例如“xxxxx的做法是公司Policy不允许的”。其实，任何公司其实都是有规则的小社会，区别在于这些规则如何被定义，如何被执行，以及执行之后能达到什么效果。

这也是我在Microsoft 365各个产品中越来越多看到的做法，尤其是Teams，从平台层面，将这种管理的文化无缝地融合进来了。


## 有哪些策略，分别解决什么问题

Teams 管理中心现在支持多达14种策略的定义和分发。

| 类别 | 策略     | 说明                                                                     | 组分配 |
| -- | ------ | ---------------------------------------------------------------------- | --- |
| 团队 | 团队策略   | 团队和频道策略用于控制在用户使用团队和频道时可使用哪些设置或功能。                                      | 是   |
| 团队 | 更新策略   | 更新策略用于管理 Teams 和 Office 预览用户，这些用户将在 Teams 应用中看到预发布版本或预览功能。             |     |
| 团队 | 模板策略   | 通过 Teams 模板策略，可以为组织内的人员创建和设置策略，以便他们只能看到某些模板。                           |     |
| 会议 | 会议策略   | 会议策略用于控制当用户加入 Microsoft Teams 会议后可以使用哪些功能。                             | 是   |
| 会议 | 实时事件策略 | Teams 实时事件策略用于打开或关闭功能，例如可加入实时事件的人员、是否为与会者提供听录稿，或安排和保存实时事件的人员是否可录制实时事件。 | 是   |
| 消息 | 消息传递策略 | 消息传递策略用于控制 Teams 用户可以使用的聊天和频道消息传递功能。                                   | 是   |
| 应用 | 权限策略   | 应用权限策略用于控制组织中的 Teams 用户可以使用的应用。                                        |     |
| 应用 | 设置策略   | 应用设置策略可控制如何通过 Teams 应用向用户提供应用。                                         |     |
| 语音 | 紧急策略   | 紧急呼叫策略用于控制组织内部用户可如何使用动态紧急呼叫功能。                                         |     |
| 语音 | 语音路由策略 | 语音路由策略将使用 PSTN 使用情况记录与语音路由关联。                                          |     |
| 语音 | 呼叫寄存策略 | 呼叫寄存让用户能够保持通话并将其转接给组织中的其他人员。                                           | 是   |
| 语音 | 呼叫策略   | 呼叫策略用于控制 Teams 用户可以使用的通话功能。                                            | 是   |
| 语音 | 来电显示策略 | 来电显示策略用于更改或阻止用户的来电显示(也称为主叫线路显示)。                                       |     |

## 策略的层级和优先规则

如果你看一下这些策略，就会发现他们的共性。每个策略，都有“**全局**”这个默认给所有用户的策略，同时都支持**自定义**（点击下图中的 “添加”按钮），并且**分配给用户**（点击下图的“管理用户”按钮），或**按组分配**（点击下图的 “组策略分配”按钮）。

>
部分策略，目前还不支持按组分配。管理员可以通过PowerShell 进行分配。本节末尾会介绍。


![](<../.gitbook/assets/图片 (202).png>)


大部分策略的分配都需要一定时间才能生效，官方文档说要几个小时，甚至24小时内才能完成。但实际生效时间也可能很快。


那么，既然策略允许自定义，并且支持针对用户或组进行分配，假设用户同时被分配了多个策略，那么到底以哪一个为准呢？Teams 平台将遵循下面的原则进行分配。

1. 如果某个用户被直接分配了一个策略，那么就以这个策略为准。否则进行下一步的判断。如果某个用户被多次分配同一类应用，则以最后分配的那次为准。
2. 如果某个用户是一个或多个组的成员，而这些组都被分配了某个策略，那么按照这些组被分配时的排名（Rank）最小的为准。
3. 如果用户既没有单独分配策略，也没有作为组成员被分配策略，则以全局策略为准。

![](<../.gitbook/assets/图片 (203).png>)

## 策略包

策略如此有用，且如此之多，以至于管理员可能无从下手，也不太好管理。最新推出的 “策略包（Policy Package）”的功能，可以有效地缓解这一问题。

管理员可以通过 [https://admin.teams.microsoft.com/policy-packages](https://admin.teams.microsoft.com/policy-packages) 访问策略包。

![](<../.gitbook/assets/图片 (204).png>)

和策略一样，这里也有默认定义好的很多现成的包，可以直接分配给用户（点击下图的“管理用户”按钮）或组使用（点击下图的 “组包分配”按钮）。

![](<../.gitbook/assets/图片 (205).png>)

点击上图中的 “添加”按钮，可以创建新的策略包，你可以选择一种或多种策略，对他们进行组合使用。

![](<../.gitbook/assets/图片 (206).png>)

## 通过PowerShell 分配和管理策略

通过管理员中心，你可以完成绝大部分跟策略管理的工作，但有些功能还不完善（尤其是针对组进行策略分配），此时你可以通过PowerShell来实现。下面我将展示主要的操作流程，和常见的命令。

### 安装和准备

请参考 [https://teamsplatform.xizhang.com/it-prespective-of-the-platform/admin-and-tools#admin-tools](https://teamsplatform.xizhang.com/it-prespective-of-the-platform/admin-and-tools#admin-tools) 的说明安装PowerShell，和所有的三个模块。

### 连接到MicrosoftTeams

你有多种方式连接到MicrosoftTeams, 下面的方式会跳出浏览器窗口让你输入用户名和密码，这种方式是最简单也是最安全的。但这种方式是需要用户干预的，无法实现自动化。你还可以通过证书或者保存在本地的凭据等方式来连接。详情可以参考 [https://docs.microsoft.com/zh-cn/powershell/module/teams/connect-microsoftteams?view=teams-ps](https://docs.microsoft.com/zh-cn/powershell/module/teams/connect-microsoftteams?view=teams-ps) 。

>
普通用户也能通过这个命令连接到MicrosoftTeams，但他们在执行某些命令时会被拒绝。


```
Import-Module MicrosoftTeams #可选，但加上这一行比较保险
Connect-MicrosoftTeams
```

### 创建和更新策略

通过PowerShell来创建、更新、删除策略，针对不同的策略，有对应不同的命令，你可以通过下面这一行代码查看所有可用的命令列表

```
Get-Command -Module microsoftteams -Name *Cs*Policy* | Where-Object {$_.Verb -in @("New","Set","Remove")}
```

![](<../.gitbook/assets/图片 (220).png>)

### 给用户分配策略、策略包

不同的策略，对应不同的命令，请用下面的查询得到可用的命令列表。

```
Get-Command -Module microsoftteams -Name *Cs*Policy* | Where-Object {$_.Verb -eq "Grant"}
```

下面的例子是给虎妞（tiger@code365.xyz）分配一个名称为“一线工作者”的应用权限策略。

```
Grant-CsTeamsAppPermissionPolicy -Identity tiger@code365.xyz -PolicyName "一线工作者"
```

而这个例子是给虎妞分配一个名称为“一线工作者”的策略包。

```
Grant-CsUserPolicyPackage -Identity tiger@code365.xyz -PackageName "一线工作者"
```



### 给组分配策略、策略包

如果希望通过PowerShell 将某个策略分配给某个组，可以使用下面这样的命令语法。

```
New-CsGroupPolicyAssignment -PolicyType TeamsAppSetupPolicy -PolicyName "技术委员会" -GroupId 3a61586b-fa48-441a-99e2-787574bb2dad
```

值得注意的是，目前只有下面的策略类型是受支持的。

* TeamsAppSetupPolicy
* TeamsCallingPolicy
* TeamsCallParkPolicy
* TeamsChannelsPolicy
* TeamsComplianceRecordingPolicy
* TenantDialPlan
* TeamsEducationAssignmentsAppPolicy
* TeamsMeetingBroadcastPolicy
* TeamsMeetingPolicy
* TeamsMessagingPolicy
* TeamsShiftsPolicy
* TeamsUpdateManagementPolicy

这里的GroupId是指组的唯一编号，你可以通过在Azure Portal中查看，或者通过下面的PowerShell命令查看，假设你知道组名称的话。

```
Import-Module Az.Resources # 可选，但加上这一行比较保险
Get-AzADGroup -DisplayNameStartsWith "技术委员会"
```

下面的命令是将“一线工作者”这个策略包，分配给某个用户组。

```
Grant-CsGroupPolicyPackageAssignment -GroupId 3a61586b-fa48-441a-99e2-787574bb2dad -PackageName "一线工作者"
```

### 批量给用户分配策略

上面提到，并非所有的策略类型都支持按组分配，例如我们常用的App Permission Policy（应用权限策略）目前就不支持。下面这个脚本可以通过“比较笨”的方法，枚举一个组中所有用户，并且一个一个地给他们分配。

```
<#

概述：这个脚本用来为一个组的所有成员设置AppPermissionPolicy. 
目前这个Policy还无法直接按组分配，所以需要自定义脚本来实现。
请参考对应的文章说明：https://teamsplatform.code365.xyz/it-prespective-of-the-platform/policy-based-managment

作者：陈希章 （code365@xizhang.com)
网站：https://teamsplatform.code365.xyz 

#>
[CmdletBinding()]
param (
    [Parameter(Mandatory = $true)]
    [string]
    $GroupName,
    [Parameter(Mandatory = $true)]
    [string]
    $PolicyName
)

# 连接
Import-Module MicrosoftTeams
Import-Module Az.Resources

Connect-MicrosoftTeams
Connect-AzAccount

# 查找组成员 
Function Get-RecursiveAzureAdGroupMemberUsers {
    [cmdletbinding()]
    param(
        [parameter(Mandatory = $True, ValueFromPipeline = $true)]
        $AzureGroup
    )
    Begin {
        
    }
    Process {
        Write-Verbose -Message "枚举组成员 $($AzureGroup.DisplayName)"
        $Members = Get-AzADGroupMember -GroupObjectId $AzureGroup.Id
            
        $UserMembers = $Members | Where-Object { $_.ObjectType -eq 'User' }
        If ($Members | Where-Object { $_.ObjectType -eq 'Group' }) {
            [array]$UserMembers += $Members | Where-Object { $_.ObjectType -eq 'Group' } | ForEach-Object { Get-RecursiveAzureAdGroupMemberUsers -AzureGroup $_ }
        }
    }
    end {
        Return $UserMembers
    }
}

# 设置组成员权限策略
$group = Get-AzADGroup -DisplayName $GroupName
$members = Get-RecursiveAzureAdGroupMemberUsers -AzureGroup $group | Where-Object { $_.ObjectType -eq "User" }
$batchId = New-CsBatchPolicyAssignmentOperation -Identity $members.UserPrincipalName -PolicyName $PolicyName -PolicyType TeamsAppPermissionPolicy

Write-Host "已经开始批量处理，你可以通过 Get-CsBatchPolicyAssignmentOperation -OperationId $batchId 随时看到进度"
```

你也可以下载这个脚本到你的本地，解压缩后得到里面的ps1文件。

[批量分配策略脚本](../.gitbook/assets/AssignPermissionPolicyToGroup (1).zip)


这个脚本已经参数化，你可以按照下面的格式执行它，记得替换GroupName和PolicyName参数的值即可。

```
 .\AssignPermissionPolicyToGroup.ps1 -GroupName  技术委员会 -PolicyName 技术委员会
```

这个脚本是通过批量处理（Batch）的方式提交给后台 `New-CsBatchPolicyAssignmentOperation`，它会返回一个操作编号，你随时可以通过编号查看操作进度。

```
Get-CsBatchPolicyAssignmentOperation -OperationId 16849ef9-2017-4973-a9ba-528437a73329
```

你可能需要等待一会儿才能看到Completed的状态。

![](<../.gitbook/assets/图片 (208).png>)

>
给用户直接分配的策略将拥有最高的优先级，请确保你充分理解这个原理。在能直接用组分配时，**我建议优先用组的方式分配。**


### 获取一个用户或组被分配的所有策略

如果你需要查看某个用户当前被最终分配使用的策略信息，可以用如下的命令

```
Get-CsUserPolicyAssignment -Identity tiger@code365.xyz
```

你会看到的结果类似如下

![](<../.gitbook/assets/图片 (221).png>)

你可以通过如下的命令查看某个组所分配的所有策略

```
Get-CsGroupPolicyAssignment -GroupId 3a61586b-fa48-441a-99e2-787574bb2dad
```

正常情况下返回的结果如下

![](<../.gitbook/assets/图片 (222).png>)

### 取消某个用户或组的策略分配


目前没有命令直接可以撤销某个用户的某个策略分配，一个可行的办法，是建立一个其他的策略，然后将其分配给用户。


你可以通过如下命令取消某个组的策略分配。请注意，你无法单独取消某个策略，而是针对某个策略类型全部取消。

```
Remove-CsGroupPolicyAssignment -GroupId 3a61586b-fa48-441a-99e2-787574bb2dad -PolicyType TeamsAppSetupPolicy
```

### 其他跟策略相关的命令

你随时可以通过下面的命令查看所有跟策略相关的命令

```
Get-Command -Module MicrosoftTeams -Name *Policy*
```

初略看一下就有几十个，如果有兴趣的，请逐一进行了解。请注意，这些命令无法直接通过 `Get-Help` 查看帮助，你可能需要打开浏览器进行查询，或者通过[https://docs.microsoft.com/zh-cn/powershell/module/skype/?view=skype-ps](https://docs.microsoft.com/zh-cn/powershell/module/skype/?view=skype-ps) 进行查阅。

![](<../.gitbook/assets/图片 (210).png>)

###



这一节介绍了基于策略的管理机制，并且介绍了针对策略、策略包的分配，不管是从管理中心，还是通过PowerShell。下面我将专门针对 应用权限策略 和 应用设置策略 进行讲解。
