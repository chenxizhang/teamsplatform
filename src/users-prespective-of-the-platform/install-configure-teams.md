---
description: 跨平台和设备支持
---

# 安装和配置 Teams

Microsoft Teams 本身并不是一个独立销售的产品，而是作为Microsoft 365 产品家族的一部分提供给用户使用的。你不需要单独的账号，而只是用Microsoft 365账号即可登录Teams。


个人版Teams 将支持直接用手机号登录。


## 管理账号

你的账号格式通常就是你在公司的邮箱地址，例如 **master@code365.xyz** 就是师傅的账号。任何时候，你可以通过 [https://myaccount.microsoft.com/](https://myaccount.microsoft.com) 这个地址查看账号并对其进行配置。

![](<../.gitbook/assets/图片 54.png>)

### 修改密码

截至目前为止，绝大部分人还是依赖密码来保护账号的安全。如果你依赖密码，养成定期修改的习惯是比较好的。点击上图的 ”更改密码“ 按钮随时可以修改密码。

![](<../.gitbook/assets/图片 56.png>)

### 启用多因子身份认证

但是密码其实是不可靠的，不仅仅容易丢失（或被人猜到），也容易被本人忘记。多因子身份认证在如今这个时代非常有其必要性，并且也完全具备可行性。你可以设置多一道验证的手段，例如手机、短信，或者通过Microsoft Authencator进行验证。

启用多因子身份认证并不难，请点击 "安全信息” 这个页面，然后点击 ”添加方法“ 即可。

![](<../.gitbook/assets/图片 55.png>)

微软和其他行业合作伙伴正在推进一个全新的方案，就是无代码身份验证，目前这个功能已经在Azure AD中可以预览，有兴趣的朋友可以参考[ https://aka.ms/gopasswordless ](https://aka.ms/gopasswordless)了解。

你还可以查看使用当前帐号的设备（如果有问题，可以将其强制注销），以及登录的轨迹等。请自行参考界面上的按钮，并且尝试进行点击了解。

## 安装Teams 客户端

### 查看订阅（是否能使用Teams）

在开始安装之前，你可以通过[查看订阅](https://portal.office.com/account/?ref=MeControl#subscriptions)来了解是否已被分配Teams的许可。请确保你已经至少分配了 `Microsoft Teams` 的订阅。

![](<../.gitbook/assets/image 1.png>)

### 通过Office 365套件一起安装

在上图中点击 左侧“应用和设备” 的菜单，或者直接导航到 [https://portal.office.com/account/?ref=MeControl#installs](https://portal.office.com/account/?ref=MeControl#installs) 可以看到下面的内容。点击红色按钮 “安装Office”即可。

![](<../.gitbook/assets/图片 57.png>)

### 独立安装Teams 客户端

如果你只想单独安装Teams客户端，请导航到 [https://www.microsoft.com/zh-cn/microsoft-teams/download-app](https://www.microsoft.com/zh-cn/microsoft-teams/download-app) 然后按需下载安装包。

值得一提的是，如果你想在安卓手机上安装官方的Teams应用，请务必参考网页底部的如下说明或者点击 [https://docs.microsoft.com/zh-cn/microsoftteams/get-teams-android-in-china](https://docs.microsoft.com/zh-cn/microsoftteams/get-teams-android-in-china) 进行扫码安装。

![](<../.gitbook/assets/图片 58.png>)

关于在不同平台安装的说明，请参考如下链接

<https://docs.microsoft.com/zh-cn/microsoftteams/get-clients#web-client>

### 通过IT 集中安装和部署

还有一些方法可以实现在公司内部的集中安装和部署，但超出了本章的范围。在第三章会有所介绍。



## 配置Teams 客户端

恭喜你！如果你已经准备好了账号，也安装好了Teams客户端，那么接下来就是探索Teams的精彩世界的时候了。常规的使用操作，请参考下面的官方培训小视频，强烈建议你用一小时左右的时间完成一次系统地学习。

<https://support.microsoft.com/zh-cn/office/microsoft-teams-%E8%A7%86%E9%A2%91%E5%9F%B9%E8%AE%AD-4f108e54-240b-4351-8084-b1089f0d21d7>



### 基本配置

通过点击Teams客户端右上角头像的下拉菜单中的“选项”按钮或者通过快捷键 `Ctrl+，`可以快速打开Teams客户端配置界面。


如果是在网页客户端中，快捷键是` Ctrl + Shift +,`&#x20;


![](<../.gitbook/assets/图片 62.png>)

这里经常会进行配置的有**语言**选项，**通知**选项，以及**设备**选项。例如我特别喜欢的一个开关是“噪声抑制”，很多时候我们远程办公，将这个选项设置为 “高” 可有效屏蔽掉开会期间周围的背景声音。

![](<../.gitbook/assets/图片 60.png>)

### 和外部联系人聊天

只需要知道对方的账号（通常就是邮箱地址），你可以很方便地跟外部联系人聊天，但你可能会遇到几种不同的情况，这里特别说明一下。

**第一种情况是，双方都已经在使用 Teams，并且允许相互进行沟通**。这是绝大部分时候默认的情况。双方可以愉快的聊天，包括发送表情，以及预约会议。出于安全考虑，双方不能直接分享文件。


这里提到的 “在使用Teams”，准确地说，是指该用户已经配置为 “Teams Only”这种模式。


![](<../.gitbook/assets/图片 61.png>)

**第二种情况是，对方还在使用Skype for Business。这种情况下，只有在对方主动发起聊天时你能进行回复，但是你无法主动地发送消息给他**。这种模式（Teams + Skype for Business），我们称之为混合模式。Skype for Business 会在2021年7月正式退役，现在绝大部分的客户，已经完成或即将完成从Skype for Business到Teams的迁移，相信以后类似的问题就不会遇到了。

>
中国区的Microsoft 365还没有包含Teams，所以在2021年7月之后，Skype for Business还是可以继续使用的。但中国区的Skype for Business用户，无法跟国际版的Teams用户进行聊天。

2021-8-2 更新：根据微软在7月底宣布的最新方案，目前已经支持中国区的Skype for Business用户直接跟国际版Teams用户进行聊天。


![](<../.gitbook/assets/图片 63.png>)

**第三种情况是，双方都已经在使用Teams，但是相互之间不允许沟通**（要么是你的公司进行了设置，要么是他们公司进行了设置）。这种情况下，你根本查找不到这个用户，更别说是发消息给他了。

![](<../.gitbook/assets/图片 64.png>)



作为最终用户，我希望你不会遇到第二种或第三种情况，这里也不准备对混合模式以及如何配置租户之间的信任做具体展开（这是下一个章节的内容），只是让你知道这几种可能性，然后如果你不巧遇到了，你需要跟外部联系人或者贵公司的IT部门进行必要的沟通来解决。

