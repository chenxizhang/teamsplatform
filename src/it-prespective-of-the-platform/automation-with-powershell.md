---
description: 使用脚本实现完全自动化
---

# 使用PowerShell 创建和管理团队

## 使用PowerShell 创建和管理团队

作为管理员，你也可以通过PowerShell 创建团队、频道、添加成员、安装应用，以便进行自动化或批量操作。下面是一些简单的范例。


用PowerShell的方式可以实现比团队模板更加强大功能, 例如添加用户, 创建私有频道等.


### 连接到Microsoft Teams环境

```
Import-Module MicrosoftTeams #非必须，但这样写是最保险的
Connect-MicrosoftTeams
```

### 创建团队

```
New-Team -DisplayName "管理团队" -Description "通过PowerShell创建的团队" -OutVariable team
```

该命令会创建一个团队，并返回如下结果。请注意，默认团队是私有的。

![](<../.gitbook/assets/图片 (290).png>)

如果你想看这个团队的更多信息，可以用如下的命令。

![](<../.gitbook/assets/图片 (291).png>)

每个团队都有一个默认的频道，英文叫General，中文叫常规。

![](<../.gitbook/assets/图片 (292).png>)

### 创建频道

通过下面的命令可以为现有团队添加新的频道

```
$team | New-TeamChannel -DisplayName "高管"
```

如果知道团队的编号，也可以用下面的方式添加

```
New-TeamChannel -GroupId a2924e77-383a-4159-b231-0a3850f588eb -DisplayName "技术委员会"
```

而下面的命令是可以为团队添加私有频道

```
$team | New-TeamChannel -DisplayName "私有频道" -MembershipType Private
```

### 添加成员到频道

通过下面的命令可以为团队添加用户

```
$team | Add-TeamUser -User panda@code365.xyz
```

或者

```
Add-TeamUser -GroupId a2924e77-383a-4159-b231-0a3850f588eb -User tiger@code365.xyz
```

以上是把用户添加到团队, 如果需要将用户添加到私有频道, 你需要安装**预览版的PowerShell模块**

>
请注意, 安装多个版本可能会导致一些奇怪的问题. 我的做法是: 在PowerShell 7.1.x 这个最新版本PowerShell (黑色) 中安装正式版的模块, 在老的PowerShell (蓝色, 版本号为5.1) 中安装预览版. 在Windows中搜索“PowerShell”时你可以看到这两个PowerShell的版本。


```
# 安装预览版的模块
Install-Module MicrosoftTeams -AllowPrerelease -RequiredVersion "2.2.0-preview"
```

请看清楚下面这个是我的蓝色PowerShell窗口

![](<../.gitbook/assets/图片 (293).png>)

通过下面的命令为私有频道添加用户。请注意，该用户必须先添加到团队才能添加到私有频道。

```
Add-TeamChannelUser -GroupId a2924e77-383a-4159-b231-0a3850f588eb -DisplayName 私有频道 -User tiger@code365.xyz
```

### 添加来宾到团队

来宾指的是外部用户，可以是已经拥有Teams账号的用户，也可以不是，但你想要将他们到团队进行协作。添加来宾的过程分为两步，首先是要先邀请该账号加入你的公司，作为来宾账号。


邀请来宾并不需要管理员权限。


执行这个操作，需要安装一个额外的模块：AzureAD，需要注意的是这个模块，跟此前提到安装的Az在某些时候也是会有冲突的，所以请在老版本的PowerShell （蓝色的那个）中安装这个模块吧。

```
Install-Module AzureAD #安装模块
Import-Module AzureAD #导入模块
Connect-AzureAD # 连接到AzureAD
```

按照提示输入你的账号和密码后，可以用如下的命令发起邀请。

```
New-AzureADMSInvitation -InvitedUserDisplayName "陈希章" -InvitedUserEmailAddress "code365@xizhang.com" -SendInvitationMessage $true -InviteRedirectUrl "https://teams.microsoft.com/l/team/19%3a59d28969bcdc41c3b422cf91fd6fe94f%40thread.tacv2/conversations?groupId=a2924e77-383a-4159-b231-0a3850f588eb&tenantId=0aa03876-6fa6-4df8-9f90-a10f103dfd9a"
```

受你邀请的用户会收到一封邮件通知。如果他已经有Teams账号，直接点击邮件中的“Accept invitation”按钮就可以进入对应的团队（你需要尽快用脚本将其添加到团队），否则的话，他会被引导一个页面，要求他创建一个Microsoft Account（用同样的邮箱地址），然后加入该团队。

![](<../.gitbook/assets/图片 (294).png>)

无论如何，只要创建了这个邀请，就可以像正常的用户那样添加到团队了。例如：

```
Add-TeamUser -GroupId a2924e77-383a-4159-b231-0a3850f588eb -User code365@xizhang.com
```

>
来宾不能加入私有频道。


### 为频道安装选项卡应用

目前无法直接通过MicrosoftTeams这个模块提供的命令为频道安装应用，但是可以通过Microsoft Graph接口来实现，你可以先看一下这个文档，了解一下所需要的权限，以及典型用法。

<https://docs.microsoft.com/zh-cn/graph/api/channel-post-tabs?view=graph-rest-1.0>

下面是一个范例，我希望在 “技术委员会”这个频道中添加一个 “Excel”的选项卡, 并且把某个文档通过这个选项卡打开。

我们首先要知道Excel这个应用的编号是什么。

```
$appId = (Get-TeamsApp -DisplayName Excel).Id
```

通过查询得到这个应用的Id 是`com.microsoft.teamspace.tab.file.staticviewer.excel`

另外还需要查询得到你要操作的团队和频道的编号。

```
$teamId = (Get-Team -DisplayName 管理团队).GroupId
$channelId = (Get-TeamChannel -GroupId $teamId | Where-Object {$_.DisplayName -eq "技术委员会"}).Id
```

请确保安装了Microsoft.Graph这个模块，接下来我们连接到Microsoft Graph

```
Connect-Graph -Scope "TeamsTab.Read.All,TeamsTab.Create"
```

请按照提示完成身份认证和授权

![](<../.gitbook/assets/图片 (297).png>)

接下来是核心步骤了，通过Invoke-GraphReqeust 来完成请求。

```
# 准备要提交给服务器的数据，请注意下面列出的entityId不能随便写，据观察每个文件都有一个唯一的id，至于如何获得这个id，请参考本文后续说明
$body =@"                                                                               
{
	"displayName":"销售报表",
	"teamsApp@odata.bind":"https://graph.microsoft.com/v1.0/appCatalogs/teamsApps/$appId",
	"configuration":{
		"entityId":"B8D9D4E1-78DD-4231-B6E8-7D923EA732CD",			
		"contentUrl":"https://chinateamscommunity.sharepoint.com/sites/msteams_b5a289/Shared Documents/General/Sales.xlsx",
		"websiteUrl":null,
		"removeUrl":null
}}
"@

# 构建要请求的服务器API地址
$url = "https://graph.microsoft.com/v1.0/teams/$teamId/channels/$channelId/tabs"

# 发起请求
Invoke-GraphRequest -Method POST -Uri $url -Body $body -ContentType "application/json; charset=utf-8"
```

![](<../.gitbook/assets/图片 (298).png>)

回到团队中我们可以看到这个Excel文件已经添加到当前的频道。

![](<../.gitbook/assets/图片 (299).png>)

>
不同的选项卡的配置选项是不一样的，我认为最好的办法是，你先在一个频道把相关的选项卡配置好，然后通过下面命令读取选项卡信息，并且据此修改你要自动化批量创建时用的选项。

```
 Invoke-GraphRequest -Uri $url -OutputType Json
```

这个命令会返回一段Json文本，据我观察，Excel 这个选项卡的EntityId，不能随便设置，每个文件都有一个对应的Id。切记！


### 其他操作

我不准备对一个操作都进行详细的讲解，有兴趣的朋友可以参考其他命令，例如修改团队和频道设置等

```
Get-Command -Module microsoftteams | Where-Object {$_.Name -notlike "*-Cs*"}
```

![](<../.gitbook/assets/图片 (295).png>)

如果对某个命令感兴趣，但不知道怎么使用，请通过如下的方式查看帮助

```
Get-Help Set-Team -Full
```

