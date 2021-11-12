---
description: 工欲善其事，必先利其器
---

# 开发环境和工具

这一节列出了基于Microsoft Teams平台进行应用开发所需要的环境和工具。

## Microsoft 365开发者账号

只要你有Microsoft 365的账号，哪怕是普通用户账号，其实就能进行Teams的开发。但是如果想要获得最完整的体验，包括管理员配置，服务接口调用等。你最好有一个用来做开发测试的账号或环境。

你可以通过下面的地址申请免费的Microsoft 365开发者账号，申请成功后，你将获得一个完整的环境，包含了25个Microsoft 365 E5 （目前最高等级的授权，价值大约 5000元/年/人，总价值约 125000元/年）。

<https://docs.microsoft.com/zh-cn/office/developer-program/microsoft-365-developer-program>

成功申请后，可以通过 [https://developer.microsoft.com/zh-cn/microsoft-365/profile](https://developer.microsoft.com/zh-cn/microsoft-365/profile) 随时查看你的开发环境的信息。

![](<../.gitbook/assets/图片 279.png>)

>
请注意，这个开发环境不是永久的，一般是半年一次授权，然后根据你在此环境中是否真的进行了相关的应用开发，决定是否能续期。你不能把它当作生产环境使用，你的重要资料也不建议保存在这个环境中。


## Microsoft Azure 试用版 【可选】


为了保证大家都能跟着本书做练习，本书所有例子并不依赖Azure，将全部能够在本地开发环境运行并调试。


在Microsoft Teams开发过程中，你可能会需要用到云服务器资源，用来部署你的应用。你可以完全自己的情况决定使用什么样的资源，但是如果使用Microsoft Azure可能会更加方便一些，尤其是在机器人开发这个部分。你可以尝试下面的链接获取免费的权益。

<https://azure.microsoft.com/zh-cn/free/>

## App Studio

作为Microsoft Teams 开发者，你可以通过官方提供的App Studio 进行应用程序定义，安装和发布。这个工具本身就是一个Teams应用，你可以在应用市场中搜索并安装它。

![](<../.gitbook/assets/图片 280.png>)

这个工具的界面是英文的，分别具有如下的功能

1. Manifest editor，这是用的最多的功能。你可以通过这里创建或导入应用，并且对其具体的功能进行定义。你还可以在这里管理机器人的信息。
2. Validation，这个功能用来对你定义的应用进行验证。
3. Card editor，在Teams应用中会经常用到自适应卡片，通过这个编辑器可以进行定义。
4. UI Tools，帮助开发者理解和使用官方提供的UI工具。

![](<../.gitbook/assets/图片 281.png>)

以上功能的详细操作，请参考

<https://docs.microsoft.com/zh-cn/microsoftteams/platform/concepts/build-and-test/app-studio-overview>

【2021/7/13更新】目前产品组推出了一个全新的网页版的开发者工具，大家可以通过如下地址访问，它可以实现跟App Studio这个应用同样的功能，同时还有一些新的功能。

<https://dev.teams.microsoft.com/home>

![](<../.gitbook/assets/image 18.png>)

## Node.js

Node.js® 是一个基于 Chrome V8 引擎 的 JavaScript 运行时，本书的部分案例会基于Node.js开发，你需要提前安装。

请通过 [https://nodejs.org/zh-cn/download/](https://nodejs.org/zh-cn/download/) 进行下载安装。

## .NET Core SDK

.NET Core是一个全新的开源框架，支持C#, VB.NET, F#三种语言进行开发，既能开发桌面应用，也能开发网页、物联网应用，甚至可以说是全栈开发平台。它运行速度极快，并且可以部署到多个平台上面运行。

你需要通过这里（[https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)）安装最新的.NET Core SDK。本书中的机器人范例，将基于.NET Core SDK来开发。所以请提前安装如下的项目模板。

```
dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
```

## Visual Studio Code

Visual Studio Code是新一代的代码编辑器，跨平台并且开源，轻量级，扩展性非常强，在全世界范围内都很受欢迎。它也是我们用来做Teams应用开发的主要工具。

你可以通过 [https://code.visualstudio.com/](https://code.visualstudio.com) 下载和安装。

### 安装中文语言包

VS code默认没有提供多语言的安装包，但你可以很容易地以插件的形式安装你需要的语言包。例如下面这个是中文简体语言包。

![](<../.gitbook/assets/图片 282.png>)

安装后需要重启VS Code生效。


你也可以通过命令行安装此插件&#x20;

```
code --install-extension MS-CEINTL.vscode-language-pack-zh-hans
```


### 安装 Microsoft Teams Toolkit for VS Code

你还可以安装Microsoft Teams Toolkit来加速Teams 应用开发，本书中的选项卡应用，我将采用这个Toolkit来生成和调试。你可以通过如下方式或 `code --install-extension TeamsDevApp.ms-teams-vscode-extension` 这一句命令安装。

![](<../.gitbook/assets/图片 283.png>)

### 安装 C# 插件

在本书演示项目中，C#用来开发机器人，默认情况下VS Code并没有安装C#，你可以通过如下方式安装或者 `code --install-extension ms-dotnettools.csharp` 这一句命令来安装它。

![](<../.gitbook/assets/图片 284.png>)

## Azure Functions Tool

Azure Functions 是一个非常有价值的无服务器（Serverless）解决方案，本书中会用它来实现选项卡项目中的服务API，简化开发。请注意，我将使用它的本地开发功能，并不需要部署到Azure。

首先，请安装Azure Functions Core Tools

<https://docs.microsoft.com/zh-cn/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#version-3x-and-2x>

然后，通过如下方式或`code --install-extension ms-azuretools.vscode-azurefunctions` 命令安装在VS Code中的Azure Functions 插件

![](<../.gitbook/assets/图片 285.png>)

## 安装PowerShell

作为开发人员，你经常需要访问Microsoft Teams的数据，以便进行验证和自动化。PowerShell将会让你事半功倍。

建议安装最新的7.1.3或者更高版本，这是一个跨平台的版本。请通过 [https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell?view=powershell-7.1](https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell?view=powershell-7.1) 进行安装，并安装 `MicrosoftTeams` 这个模块，以及 `Microsoft.Graph` 和`Az` 这两个模块 （可选，但推荐）。

```
Install-Module MicrosoftTeams,Microsoft.Graph,Az
```

你可能还需要一个PowerShell编辑器，我这里仍然推荐用VS Code，你可以通过下面一句命令安装PowerShell扩展。

```
code --install-extension ms-vscode.powershell
```

## 为本地开发提供外网https访问支持 <a href="localhost-https-support" id="localhost-https-support"></a>

这个说起来有点绕，其实很好理解。本地开发是指开发人员在自己的电脑上面开发，但并没有发布到真正的服务器或者云平台，所以这个应用是无法在外网访问到的。

Microsoft Teams 在定义应用包（Teams App Package）时，针对不同的应用会要求提供一个或者多个地址，而这些地址，有如下的要求

1. 该地址要能被外网访问到，而不是`http://localhost` 这样的地址。
2. 该地址要求用https来提供服务。

为了解决这样的问题，现在有一些第三方方案。我经常使用的有下面两个：ngrok （优先） 或 localhost.run，他们都有免费的服务，请按需使用。

### 使用 ngrok 提供本地隧道功能

在很多微软的官方文档中都提到了 `ngrok` 这个工具，这可能是目前用的最广泛的一个将本地网站发布到外网，并且默认提供https支持的服务。它的官方网站是 [https://ngrok.com/](https://ngrok.com) 。

ngrok 有免费版和收费版，也有多种安装方式。请参考这里的说明：[https://ngrok.com/download](https://ngrok.com/download)。我自己比较喜欢的方式是将其作为npm的全局模块安装，如下

```
npm install -g ngrok
```

这样做的好处就是可以直接用 ngrok 命令，最常见的用法是这样

```
ngrok http --host-header=rewrite 3000
```

这个工具会生成两个地址，可供外网访问。

![](<../.gitbook/assets/图片 287.png>)


请注意，你看到的界面可能跟我略有不同。我注册了一个免费账号。如果你没有注册账号，也可以直接使用，但是每次的会话只能维持8个小时，超过会强制终止。


另外，还有一个后台网站可以查看访问的情况，包括请求和响应的细节。请通过 http://127.0.0.1:4040 访问这个网站即可。

![](<../.gitbook/assets/图片 288.png>)

## Postman 【可选】

我还很喜欢用Postman来做API 的调试，如果你也有兴趣，请通过这里（[https://www.postman.com/downloads/](https://www.postman.com/downloads/)）进行安装。目前这个工具只有中文版。

![](<../.gitbook/assets/图片 286.png>)
