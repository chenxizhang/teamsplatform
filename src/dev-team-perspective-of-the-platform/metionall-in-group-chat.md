---
description: '@所有人 （或@all） 的一个非官方实现'
---

# 在群聊中提醒所有人

这个案例是解决一个普遍需求，但目前标准版没有提供的功能：在群聊中提醒所有人。

## 需求说明

在Teams中，如果我们需要提醒某个人注意某个事情，会通过 `@xxxxx ` 这样的语法。同时，如果是在团队中聊天，我们还有一个特殊的快捷方式 `@team` 或 `@channel` 来提供整个团队的人，或者关注了当前频道的人，甚至还可以通过 `@tag` 来为特定的一批人进行提醒。

![@team，会自动解析当前团队](<../.gitbook/assets/图片 (329).png>)

![@channel，会自动解析当前频道 ](<../.gitbook/assets/图片 (330).png>)

![@tag, 会解析分配了某标签的用户](<../.gitbook/assets/图片 (331).png>)

看起来很不错，但是如果在群聊中，目前默认没有这个功能。对于广大的中国用户来说，其实我们很习惯这样操作，在需要时给所有人提醒某些信息。

这个例子给大家演示，如何用机器人这个能力，在群聊中实现一个非官方的“提醒所有人”的功能。这也算的是一个对机器人的另类用法，但毫无疑问，也是一个很强大的场景。

## 场景设计

我们需要在群里中，实现这样的场景：

1. 用户通过 `@所有人` 或 `@all` 这样的语法（这两个语法本质上是一样，我们会通过多语言来实现，本文仅演示中文版本），加上自己需要的文字，发出一个群公告。
2. 所有的人都能像在团队或频道里面一样，收到一个`@` 的通知，通常是在左侧工具栏的 `活动` 页面看到，客户端和手机都会收到推送。

![发出群通知](<../.gitbook/assets/图片 (333).png>)

![用户收到 的通知](<../.gitbook/assets/图片 (332).png>)

## 实现方案

本例我将采用.NET Core来编写，会用到一个机器人项目模板，然后也会用到Microsoft Graph 来发送推送消息。

### 安装.NET Core SDK

请参考 [https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#net-core-sdk](https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#net-core-sdk) 的说明，安装.NET Core SDK和项目模板。

### 创建项目

在命令行中运行下面的命令创建项目

```
dotnet new echobot -o teamsatallbot
```

安装一个专门用来实现Teams机器人的库，Microsoft Graph库，和Microsoft Identity Client库（请一行一行执行）如下命令

```
dotnet add package Microsoft.Bot.Builder.Teams --prerelease
dotnet add package Microsoft.Graph
dotnet add package Microsoft.Identity.Client
```

一顿操作后，你会得到下面这样一个项目

![](<../.gitbook/assets/图片 (334).png>)

这个模板实现的机器人非常简单，它就像它的名字表明的那样，你跟它说什么，它就简单地回复一句话，重复你说的。这当然没有什么令人振奋的，但很多时候机器人其实也就是这样一问一答嘛，所以具体取决于你要实现什么。

你需要在命令行将这个项目运行起来。通过 `dotnet watch run` ，默认情况下，你会看到一个浏览器被打开，这个服务已经在localhost:3978进行监听。

![](<../.gitbook/assets/图片 (343).png>)

### 注册机器人

先不要着急修改代码，让我们先把这个机器人配置起来，让它能在Teams中使用起来。你需要用到App Studio这个工具，你可以参考 [https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#app-studio](https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#app-studio) 这里的介绍安装起来。

在App Studio中创建一个新的应用，首先准备基本信息，大致如下

![](<../.gitbook/assets/图片 (335).png>)

然后，切换到 Bots 这个页面，让我们来添加一个新的机器人

![](<../.gitbook/assets/图片 (336).png>)

点击 ”Create bot“ 按钮后，你需要稍微等一会儿，然后会看到类似的界面。你需要点击下面的”Generate new password" 这个按钮（下图第二个红色框），生成一个密钥，并且立即复制保存起来，包括“群聊小助手”下方的这个id。后续代码会用到。

![](<../.gitbook/assets/图片 (337).png>)

上图中的第三个红色框的部分，你需要在你那边用下面的命令来获得。你需要先安装 `ngrok` 。

```
ngrok http 3978
```

![](<../.gitbook/assets/图片 (338).png>)

请用你那边的这个地址，替换掉上图中的 “Bot endpoint address" 中的 `/api/messages` 前面的部分。

回到VS Code中，修改appsettings.json文件，把上面保存的应用编号和密钥放在这里来，保存后项目会自动重新编译。

![](<../.gitbook/assets/图片 (342).png>)

### 安装机器人

通过下面的方式进行安装

![](<../.gitbook/assets/图片 (340).png>)

选择一个现有的聊天群，然后点击”设置机器人“按钮

![](<../.gitbook/assets/图片 (341).png>)

现在你可以尝试在群聊中，去@ 这个”所有人“机器人，然后你会看到如下的效果。

![](<../.gitbook/assets/图片 (344).png>)

这个机器人已经能正常工作了，但是这样显然不是我们想要的，只是把机器人运行起来了而已，下面看具体怎么实现提醒所有人的功能。

### 为机器人申请发送通知权限

上面的例子可以看出，机器人可以很容易地收到用户发送过来的消息（在群聊中，用户只需要通过 `@机器人名称` 这样的语法发出指令），那么接下来最重要的就是，机器人收到指令后，该怎么样提醒所有人注意到这个要提醒的消息？

Microsoft Graph 提供了一个接口，`sendActivityNotification` 可以实现这个目的，下面的接口的详细说明。

<https://docs.microsoft.com/zh-cn/graph/api/chat-sendactivitynotification>

在这一步中，你需要为机器人申请下面的权限。

![](<../.gitbook/assets/图片 (345).png>)

还记得你注册机器人完成后得到的机器人编号吗？你需要那个信息，然后访问如下的地址 ([https://portal.azure.com/#blade/Microsoft\_AAD\_IAM/ActiveDirectoryMenuBlade/RegisteredApps](https://portal.azure.com/#blade/Microsoft\_AAD\_IAM/ActiveDirectoryMenuBlade/RegisteredApps))，查找你的机器人应用。

![](<../.gitbook/assets/图片 (346).png>)

点击这个应用，进入它的详细配置页面。我们接下来需要为它配置一个使用的平台（就是用在什么场合），本例我们是一个Web应用。

![](<../.gitbook/assets/图片 (347).png>)

按照上述步骤操作后，点击 ”Web“ 这个选项，这里需要配置一个”重定向URI“，你可以随便放一个吧，本例我们是一个后台应用，实际上并不会真的做重定向，但是这个信息是必填的，而且一会儿代码中也要保持一致。

![](<../.gitbook/assets/图片 (348).png>)

单击 ”配置“ 按钮完成。接下来，为该应用申请必要的权限。

![](<../.gitbook/assets/图片 (349).png>)

按上图操作，按下图所示搜索权限，并且勾选。

![](<../.gitbook/assets/图片 (350).png>)

单击 ”添加权限“ 回到主界面。你需要以管理员身份对这个进行授权，否则你需要邀请你公司的管理员对其进行操作。

![](<../.gitbook/assets/图片 (351).png>)

到这里差不多该做的配置都完成了，你需要最后复制一下当前你这个应用所在的租户编号信息，以备使用。

![](<../.gitbook/assets/图片 (352).png>)



我建议把这个信息，放在项目的appsettings.json 文件中，如下图所示。



### 实现机器人功能

终于等到了最关键的部分，我们来真正实现机器人的功能。接下来，我们需要做的无非是两件事情

1. 获取当前群聊的人员列表
2. 给每个人发一个通知，告诉他们有一个消息需要引起注意

当前这个项目模板，引用了Bot.Builder等一系列SDK 库，所以编写机器人变得如此简单，就本例而言，你只需要关注一下EchoBot.cs 这个类就可以了。它继承自`ActivityHandler`， 提供了一系列可以重写的方法，例如下面这个就是最核心的、用来处理用户发送消息的事件（`OnMessageActivityAsync`）。

![](<../.gitbook/assets/图片 (354).png>)

在这个文件中，删除不用的代码. 下面这个方法很有用，它会在群聊或团队中添加新成员时被调用，但本例中暂时用不到

```csharp
protected override async Task OnMembersAddedAsync(IList<ChannelAccount> membersAdded, ITurnContext<IConversationUpdateActivity> turnContext, CancellationToken cancellationToken)
{
    var welcomeText = "Hello and welcome!";
    foreach (var member in membersAdded)
    {
        if (member.Id != turnContext.Activity.Recipient.Id)
        {
            await turnContext.SendActivityAsync(MessageFactory.Text(welcomeText, welcomeText), cancellationToken);
        }
    }
}
```

我们需要进一步地修改，例如使用一个专门的`TeamsActivityHandler` 来实现( 这个类特别有用，请参考 [https://docs.microsoft.com/zh-cn/microsoftteams/platform/bots/bot-basics?tabs=csharp](https://docs.microsoft.com/zh-cn/microsoftteams/platform/bots/bot-basics?tabs=csharp)），以及简单的Micrsooft Graph调用的代码。我这里就不逐一做介绍了，请参考修改后的代码。

```csharp
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Teams;
using Microsoft.Bot.Schema;
using Microsoft.Extensions.Configuration;
using Microsoft.Graph;
using Microsoft.Identity.Client;

namespace EchoBot.Bots
{
    /// <summary>
    /// 这个类用来进行身份验证
    /// </summary>
    public class SimpleAuth : IAuthenticationProvider
    {
        // 读取配置文件（或云平台的配置）
        private IConfiguration _configuration;
        public SimpleAuth(IConfiguration configuration)
        {
            this._configuration = configuration;
        }
        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            // 这个应用是指后台应用，不需要用户参与授权。请注意，使用密码的方式并不是很推荐的，在生产环境，尽量用证书。
            IConfidentialClientApplication app = ConfidentialClientApplicationBuilder
                .Create(_configuration.GetValue<string>("MicrosoftAppId"))
                .WithTenantId(_configuration.GetValue<string>("TenantId"))
                .WithClientSecret(_configuration.GetValue<string>("MicrosoftAppPassword"))
                .Build();

            // 获取访问凭据。请注意，生产环境下，这里需要考虑缓存。
            var token = await app.AcquireTokenForClient(new[] { "https://graph.microsoft.com/.default" }).ExecuteAsync();
            // 把AccessToken 附加的请求的头部中去
            request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token.AccessToken);
        }
    }
    public class EchoBot : TeamsActivityHandler
    {
        private IConfiguration _configuration;
        public EchoBot(IConfiguration config)
        {
            this._configuration = config;
        }

        protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
        {
            // 读取当前群聊中的用户列表
            var members = await TeamsInfo.GetMembersAsync(turnContext);
            // 准备用来访问Microsoft Graph的本地代理
            GraphServiceClient graphClient = new GraphServiceClient(new SimpleAuth(_configuration));
            // 为每个用户发送一个通知，这里解析得到他们的AadObjectId，用来发送
            members.Select(_ => _.AadObjectId).AsParallel().ForAll(async (_) =>
            {
                // 以下代码，其实你可以在官网找到，并且简单地做些修改即可 
                // https://docs.microsoft.com/zh-cn/graph/api/chat-sendactivitynotification?view=graph-rest-1.0&tabs=http#example-1-notify-a-user-about-a-task-created-in-a-chat
                var topic = new TeamworkActivityTopic
                {
                    Source = TeamworkActivityTopicSource.EntityUrl,
                    Value = $"https://graph.microsoft.com/beta/me/chats/{turnContext.Activity.Conversation.Id}/messages/{turnContext.Activity.Id}"
                };
                // 这个是通知的自定义模板（在manifest.json文件中要定义）
                var activityType = "metionall";
                // 预览文字
                var previewText = new ItemBody
                {
                    Content = "有人在群聊中提到你了，请点击查看"
                };
                // 收件人的id
                var recipient = new AadUserNotificationRecipient
                {
                    UserId = _
                };
                // 替换掉模板中的值
                var templateParameters = new List<Microsoft.Graph.KeyValuePair>()
                {
                    new Microsoft.Graph.KeyValuePair
                    {
                        Name = "from",
                        Value = turnContext.Activity.From.Name
                    },
                    new Microsoft.Graph.KeyValuePair
                    {
                        Name ="message",
                        Value =turnContext.Activity.RemoveMentionText(turnContext.Activity.Recipient.Id)
                    }
                };
                // 调用接口发送通知
                await graphClient.Chats[turnContext.Activity.Conversation.Id]
                    .SendActivityNotification(topic, activityType, null, previewText, templateParameters, recipient)
                    .Request()
                    .PostAsync();
            });

        }
    }
}

```

### 修改应用配置文件

代码是修改好了，但这个例子还不能工作，上面用到了Microsoft Graph以及推送通知的功能，还需要在应用的配置文件中做定义，而且这个定义目前不能通过App Studio直接定义，需要先下载下来，然后本地修改。请在App Studio中找到你这个应用，然后按下图操作，选择 ”Download“。

![](<../.gitbook/assets/图片 (355).png>)

下载下来是一个压缩包，建议你把它解压缩后，将有关文件复制到项目的根目录下面，创建一个Manifest文件夹来保存它们，如下图所示。

![](<../.gitbook/assets/图片 (356).png>)

在这个json文件的底部添加下面的定义

```javascript
  "webApplicationInfo": {
    "id": "978c16a7-70f4-44fb-92a6-813a84719017",
    "resource": "https://code365.xyz"
  },
  "activities": {
    "activityTypes": [
      {
        "type": "metionall",
        "description": "全员消息",
        "templateText": "{from} 提醒你: {message} "
      }
    ]
  }
```

完整的定义如下

```javascript
{
  "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.9/MicrosoftTeams.schema.json",
  "manifestVersion": "1.9",
  "version": "1.0.0",
  "id": "d90273f3-3f3a-496a-87f8-87bdd985fcb1",
  "packageName": "xyz.code365",
  "developer": {
    "name": "Code365.xyz",
    "websiteUrl": "https://code365.xyz",
    "privacyUrl": "https://code365.xyz",
    "termsOfUseUrl": "https://code365.xyz"
  },
  "icons": {
    "color": "color.png",
    "outline": "outline.png"
  },
  "name": {
    "short": "所有人",
    "full": "所有人"
  },
  "description": {
    "short": "在群里中@所有人",
    "full": "在群里中@所有人"
  },
  "accentColor": "#FFFFFF",
  "bots": [
    {
      "botId": "978c16a7-70f4-44fb-92a6-813a84719017",
      "scopes": ["groupchat"],
      "supportsFiles": false,
      "isNotificationOnly": false
    }
  ],
  "permissions": ["identity", "messageTeamMembers"],
  "validDomains": [],
  "webApplicationInfo": {
    "id": "978c16a7-70f4-44fb-92a6-813a84719017",
    "resource": "https://code365.xyz"
  },
  "activities": {
    "activityTypes": [
      {
        "type": "metionall",
        "description": "全员消息",
        "templateText": "{from} 提醒你: {message} "
      }
    ]
  }
}
```

我们接近大功告成了。你需要手工地把这三个文件（manifest.json, color.png, outline.png) 打包成一个zip文件，例如 `metionallbot.zip` 。然后在你的Teams客户端中进行上传。

![](<../.gitbook/assets/图片 (357).png>)

把这个机器人安装到你希望的群聊中，尝试去 `@所有人` 这样调用它，然后你很快可以看到相关的成员就收到通知了。

![](<../.gitbook/assets/图片 (358).png>)

## 完整源代码

这个案例的完整代码，请通过下面地址访问

<https://github.com/code365opensource/teamsapp-samples-metionallbot>


你可以把代码下载到本地，然后将有关的机器人配置信息，换成你自己的，然后在本地调试。


## 延申讨论

其实我还有一种方案，就是用机器人发一个新的消息到群聊中，并且把所有人都@一遍，这样做的好处是更加简单一些，不需要调用Microsoft Graph接口，不需要管理员账号。因为这里是直接@了某个人，所以他也一样会收到通知（通过Teams内置的机制）。

但可能不太好的地方是，多出来一个消息了。

![](<../.gitbook/assets/图片 (359).png>)

这个方案的代码很少，核心部分如下

```csharp
// 第一种方案：发送一条新的消息，直接在消息中提醒所有人。

var mentions = members.Select(_ => new Mention()
{
    Mentioned = new ChannelAccount(_.Id, _.Name, _.Role, _.AadObjectId),
    Text = $"<at>{XmlConvert.EncodeName(_.Name)}</at>"
});

var text = string.Join(',', mentions.Select(_ => _.Text));

var replyActivity = MessageFactory.Text($"大家好，{turnContext.Activity.From.Name} 提醒的消息：<br />{turnContext.Activity.RemoveMentionText(turnContext.Activity.Recipient.Id)} <br /> {text}.");
replyActivity.Entities = mentions.Cast<Entity>().ToArray();
await turnContext.SendActivityAsync(replyActivity, cancellationToken);
```

你觉得哪一种方式更好呢？有兴趣可以给我反馈


[feedback-and-follow-update.md](../feedback-and-follow-update.md)


