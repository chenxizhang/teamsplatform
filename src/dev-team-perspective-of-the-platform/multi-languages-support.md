---
description: 打造友好的用户界面，提供最佳的体验
---

# 多语言支持

在之前的例子中，我们看到的应用界面，都是中文的。作为演示开发流程的角度，这是不错的。但如果你的应用不仅仅针对中文市场发布，或者你的用户群中会有外国人，那么就需要考虑多语言支持。由于这个功能是基础性的，所以我单独用一个小节来做说明。

## 多语言支持的方案

多语言支持包含了两大方面

1. **应用定义的多语言支持**。这将影响到用户看到的应用的基本信息，尤其是名称信息。这个是通过为manifest（清单文件）提供多语言来实现。
2. **应用界面的多语言支持**。例如用户打开的选项卡应用，或者机器人的消息等。这个是需要开发人员自己进行实现，不同的开发平台有不同的实现方案。

另外，在实现多语言支持的具体方案上，建议设置英文为默认语言，然后再根据具体需要支持的其他语言列表定义资源文件（例如简体中文），这样的话，除了已定义的语言之外，其他所有没有定义资源文件的语言，都将以英文来显示，这样可以比较省力气。

## 如何检测用户的语言

Teams客户端本身就支持多语言，以上提到的两个方面，”应用定义“ 会自动根据当前的客户端语言进行切换，然后 ”应用界面“ 的部分，我也是建议你同样进行处理。

![](<../.gitbook/assets/图片-360.png>)

这个语言一旦修改，是会重启客户端的，所以对于选项卡应用来说，只需要在初始化时读取该设置即可，不需要每次都获取。通过Teams JS SDK，在 `getContext` 的回调中，检测 `locale` 这个属性即可。

![](<../.gitbook/assets/图片-361.png>)

对于机器人应用，它其实是一个远程服务，不在本地运行。你可以通过 `turnContext.Activity.Locale` 这个属性来得到当前发送消息的用户所使用的语言。

>
请注意，机器人的场景比较复杂，在不同的事件中，检测Locale的方式可能不一样，有些时候并没有提供这个信息，请做必要的异常处理。


详情请参考下面的文档介绍

<https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.bot.schema.imessageactivity?view=botbuilder-dotnet-stable>

## 应用定义的多语言支持

我们是在App Studio中定义Teams应用的，根据上述的原则，我建议你默认用英文定义，然后根据需要添加其他语言。

假设你先定义了如下这样一个应用，它包含了一个”个人选项卡“，一个”机器人“。

```javascript
{
  "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.9/MicrosoftTeams.schema.json",
  "manifestVersion": "1.9",
  "version": "1.0.0",
  "id": "a1b624ef-80f6-4dbe-9dd0-e3cf83248e8f",
  "packageName": "xyz.code365.teams",
  "developer": {
    "name": "https://code365.xyz",
    "websiteUrl": "https://code365.xyz",
    "privacyUrl": "https://code365.xyz",
    "termsOfUseUrl": "https://code365.xyz"
  },
  "icons": {
    "color": "color.png",
    "outline": "outline.png"
  },
  "name": {
    "short": "all",
    "full": "allbot"
  },
  "description": {
    "short": "metionallbot",
    "full": "Allow users to metion all people in a group chat."
  },
  "accentColor": "#FFFFFF",
  "staticTabs": [
    {
      "entityId": "homepage",
      "name": "Home",
      "contentUrl": "https://teamsplatform.xizhang.com",
      "scopes": [
        "personal"
      ]
    },
    {
      "entityId": "about",
      "scopes": [
        "personal"
      ]
    }
  ],
  "bots": [
    {
      "botId": "d7467536-9673-426c-8ea9-168fe9ba743a",
      "scopes": [
        "groupchat"
      ],
      "commandLists": [
        {
          "scopes": [
            "groupchat"
          ],
          "commands": [
            {
              "title": "help",
              "description": "Tell me about the bot"
            }
          ]
        }
      ],
      "supportsFiles": false,
      "isNotificationOnly": false
    }
  ],
  "permissions": [
    "identity",
    "messageTeamMembers"
  ],
  "validDomains": [
    "teamsplatform.code365.xyz"
  ]
}
```

请在App Studio中，定位到 ”Languages“ 这个页面，然后选择 ”Download template“ 按钮。

![](<../.gitbook/assets/图片-362.png>)

你将得到一个json文件，它会自动根据当前的manifest中可以进行多语言化的字段生成一些信息。本例我得到下面这样的定义。

```
{
    "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.9/MicrosoftTeams.Localization.schema.json",
    "name.short": "",
    "name.full": "",
    "description.short": "",
    "description.full": "",
    "staticTabs[0].name": "",
    "staticTabs[1].name": "",
    "bots[0].commandLists[2].commands[0].title": "",
    "bots[0].commandLists[2].commands[0].description": ""
}
```

我用中文定义如下的内容

```
{
    "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.9/MicrosoftTeams.Localization.schema.json",
    "name.short": "所有人",
    "name.full": "所有人",
    "description.short": "在群聊中提醒所有人",
    "description.full": "这个功能类似于是群公告，任何人可以通过@的语法，给其他人发出提醒。",
    "staticTabs[0].name": "主页",
    "staticTabs[1].name": "关于",
    "bots[0].commandLists[2].commands[0].title": "帮助",
    "bots[0].commandLists[2].commands[0].description": "告诉我怎么用"
}
```

然后给该文件命令为 `zh.json` ，将其导入到应用定义中去。

![](<../.gitbook/assets/图片-363.png>)

点击”Import“ 按钮后选择上面提到的文件，然后稍等就完成导入了。

![](<../.gitbook/assets/图片-364.png>)

这样，我们如果在不同语言版本的Teams客户端中去尝试安装这个应用，看到的应用信息就是不一样的，例如下图是中文环境的效果。


你需要把应用上传到公司的应用商店，或者发布到微软的官方商店才会有效果。如果你直接为自己上传应用，还是会以默认语言显示。


![](<../.gitbook/assets/图片-367.png>)

点击应用弹出的安装界面也是完全中文的

![](<../.gitbook/assets/图片-365.png>)

下图是英文环境的效果。

![](<../.gitbook/assets/图片-368.png>)

![](<../.gitbook/assets/图片-366.png>)

## 应用界面的多语言支持

下面我们来看一下应用界面如何实现多语言支持。

### 选项卡应用（Node.js）

我用一个简单的例子来演示。假设你有如下这样一个简单的选项卡应用，它会在首页上跟用户打招呼，并且提供了应用的基本说明。下图3个数字标出来的部分是可以做多语言处理的。至于 `ares@code365.xyz` 这个部分是用户的信息，因为一个用户很难有多个不同的名称（此处读取的upn，更多的时候会读取显示名，请参考`单点登录实现方案` 中的介绍），所以这里不做多语言处理。

![](<../.gitbook/assets/图片-370.png>)

我们接下来看一下实现这个界面的核心代码

```javascript
  render() {
    const { t } = this.props;
    const userName =
      Object.keys(this.state.context).length > 0
        ? this.state.context["upn"]
        : "";

    return (
      <div>
        <h1>欢迎使用, {userName}!</h1>
        <h3>这是应用标题</h3>
        <p>这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，这是应用描述信息，</p>
      </div>
    );
  }
```

我这里介绍的一个方案用到了一个比较流行的库 `react-i18next` ，它在Github上有多达6300个star，请参考 [https://github.com/i18next/react-i18next](https://github.com/i18next/react-i18next) 。

#### 添加 i18next

```
npm install react-i18next i18next
```

#### 创建语言资源文件

让我们在components目录下面创建一个子目录（i18n），然后按照语言再建立单独的目录，例如（en, zh），然后每个目录下面定义一个translation.json 文件，然后在里面定义我们需要的资源。

![](<../.gitbook/assets/图片-371.png>)

#### 创建多语言配置

在 `i18n` 目录下面创建一个 config.ts 文件，这里会设置默认的语言。

```javascript
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';

// 定义资源
const resources = {
    "en-us": {
        translation: require("./en/translation.json")
    },
    "zh-cn": {
        translation: require("./zh/translation.json")
    }
} as const;

// 初始化
i18n
    .use(initReactI18next).init({
        fallbackLng: "en-us",
        lowerCaseLng: true,
        resources: resources
    });

export default i18n;
```

#### 在应用入口处导入上面的配置

通常是在 index.tsx 文件中import即可。例如

![](<../.gitbook/assets/图片-372.png>)

#### 在应用组件中添加初始化和语言设置的逻辑

在App.tsx中，添加下面的第2行，第11行，第14-16行，在Teams完成初始化时，修改语言设置。

```javascript
import * as microsoftTeams from "@microsoft/teams-js";
import { useTranslation } from "react-i18next";
import { BrowserRouter as Router, Route } from "react-router-dom";
import "./App.css";
import Privacy from "./Privacy";
import Tab from "./Tab";
import TabConfig from "./TabConfig";
import TermsOfUse from "./TermsOfUse";

function App() {
  const { t, i18n } = useTranslation();
  microsoftTeams.initialize();

  microsoftTeams.getContext((context) => {
    i18n.changeLanguage(context.locale);
  })

  return (
    <Router>
      <Route exact path="/privacy" component={Privacy} />
      <Route exact path="/termsofuse" component={TermsOfUse} />
      <Route exact path="/tab" component={Tab} />
      <Route exact path="/config" component={TabConfig} />
    </Router>
  );
}

export default App;
```

#### 在具体的Tab组件中引入资源定义

请注意下面的第5行，第27行，第36-38行，以及第43行。这里的关键语法就是 `{t("xxxxxxx")}` ，它可以读取到对应资源文件中的文本定义。

```javascript
import React from "react";
import "./App.css";
import * as microsoftTeams from "@microsoft/teams-js";

import { withTranslation } from "react-i18next";

class Tab extends React.Component<any, any> {
  constructor(props: any) {
    super(props);


    this.state = {
      context: {},
    };
  }

  componentDidMount() {
    microsoftTeams.getContext((context: microsoftTeams.Context) => {
      this.setState({
        context: context,
      });
    });
  }

  render() {

    const { t } = this.props;

    const userName =
      Object.keys(this.state.context).length > 0
        ? this.state.context["upn"]
        : "";

    return (
      <div>
        <h1>{t("greeting")},{userName}!</h1>
        <h3>{t("title")}</h3>
        <p>{t("description")}</p>
      </div>
    );
  }
}
export default withTranslation()(Tab);

```

下面是中文环境效果

![](<../.gitbook/assets/图片-373.png>)

下面是英文环境效果

![](<../.gitbook/assets/图片-374.png>)

这个案例的完整代码，你可以通过 [https://github.com/code365opensource/teamsapp-samples-tab-multilanguages](https://github.com/code365opensource/teamsapp-samples-tab-multilanguages) 获取到。

### 机器人应用（C#)

下面来看看机器人的场景。这里的范例，请大家同样用C#来开发。请通过 `dotnet new echobot -o echobotsample` 这样的命令来创建范例项目。默认情况下生成的项目，关键的代码如下。

![](<../.gitbook/assets/图片-375.png>)

我们准备对上面的两个方法进行改造，以使得其支持多语言。其实实现的方式有很多，我这里分别介绍三种做法。

#### 直接定义在代码文件中

首先是最简单的一种。大家试想一下，每个机器人真正需要跟用户交互的消息不会太多的，那么我可以把这些资源直接定义在代码文件中，以便实现更加高效的编程，以及加快运行速度。

```csharp
private Dictionary<string, string> en = new Dictionary<string, string>()
{
    {"Echo","Echo:{0}"},
    {"Welcome","Hello and welcome!"}
};

private Dictionary<string, string> zh = new Dictionary<string, string>()
{
    {"Echo","回音:{0}"},
    {"Welcome","欢迎!"}
};

private string GetLocalizedText(string key, string locale)
{
    if (locale.ToLower().StartsWith("zh"))
    {
        return zh[key];
    }
    else
        return en[key];
}
```

我们分别用一个字典定义了两种语言的字符串定义，然后写了一个简单的方法来读取它们。接下来尝试修改一下原有的两个方法。

```csharp
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    var replyText = string.Format(GetLocalizedText("Echo", turnContext.Activity.Locale), turnContext.Activity.Text);
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
}

protected override async Task OnMembersAddedAsync(IList<ChannelAccount> membersAdded, ITurnContext<IConversationUpdateActivity> turnContext, CancellationToken cancellationToken)
{
    var welcomeText = GetLocalizedText("Welcome", turnContext.Activity.GetLocale());
    foreach (var member in membersAdded)
    {
        if (member.Id != turnContext.Activity.Recipient.Id)
        {
            await turnContext.SendActivityAsync(MessageFactory.Text(welcomeText, welcomeText), cancellationToken);
        }
    }
}
```

#### 定义在单独的配置文件中

如果你所需要的语言资源比较多，或者你不喜欢在代码中用字典的方式来定义它们，这完全可以理解。你可以利用.NET Core支持的基于文件的配置系统，单独定义一个（或多个）资源文件，然后在程序启动时将其加载进来，这样做的好处还在于，这些资源的定义，是只加载一次，而不是每次机器人初始化时都构建一次，有时候可能会占用额外的内存。

你可以将这些资源定义在一个文件，也可以在多个文件中。

![](<../.gitbook/assets/图片-376.png>)

然后，我们需要在这个项目启动时就把这些定义作为配置信息加载进来，所以你需要修改一下 `Program.cs` 这个文件，增加如下的几行代码。

![](<../.gitbook/assets/图片-377.png>)

最后，在机器人的代码中，你可以默认读取到这些配置（因为运行时已经把配置信息注入到机器人的构造函数中），你通过下面的方式接收即可。

```csharp
private IConfiguration Configuration;
public EchoBot(IConfiguration configuration)
{
    this.Configuration = configuration;
}
```

顺便将 `GetLocalizedText` 这个方法稍作修改即可。

```csharp
private string GetLocalizedText(string key, string locale)
{
    var lng = locale.ToLower().StartsWith("zh") ? "zh" : "en";
    return Configuration.GetValue<string>($"{lng}:{key}");
}
```

下图演示的是分别用中文和英文环境交互的效果。

![](<../.gitbook/assets/图片-378.png>)

#### 用模板处理多语言卡片

在机器人与用户的交互过程中，你可以充分利用最新的“自适应卡片”的技术。你可以通过[ 这里](https://docs.microsoft.com/zh-cn/adaptive-cards/) 学习一些基本知识，并且在下面的网站设计卡片消息。

<https://adaptivecards.io/designer/>

![](<../.gitbook/assets/图片-380.png>)

请在完成卡片设计后，点击“Copy card payload” 按钮把卡片的定义复制到单独的文件中，并且根据不同语言，定义不同的版本。

![](<../.gitbook/assets/图片-381.png>)

本例中，我定义了一个卡片的两种语言版本，分别用一个json文件保存起来，放在了Resources目录下面的Cards目录。接下来我们需要添加一个包来解析和生成卡片。

```
dotnet add package AdaptiveCards
```

然后，用如下几行代码即可读取卡片定义，并且根据当前语言生成卡片，并发送给用户。

```csharp
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    // 回复卡片消息
    var lng = turnContext.Activity.Locale.ToLower().StartsWith("zh") ? "zh" : "en";
    var carddef = System.IO.File.ReadAllText($"Resources/cards/samplecard.{lng}.json");
    var cardParseResult = AdaptiveCard.FromJson(carddef);
    var message = MessageFactory.Attachment(new Attachment() { Content = cardParseResult.Card, ContentType = AdaptiveCard.ContentType });
    await turnContext.SendActivityAsync(message);
}
```

就是这么简单，测试的效果如下，请参考。

![](<../.gitbook/assets/图片-379.png>)

本例的完整代码，请通过下面的地址访问。

<https://github.com/code365opensource/teamsapp-samples-bot-multilanguages>

