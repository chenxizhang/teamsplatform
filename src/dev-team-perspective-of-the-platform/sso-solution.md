---
description: 安全、无缝登录
---

# 单点登录实现方案

前面演示的一些例子，大多并不复杂。实际开发的应用中，有一个非常重要的环节就是身份认证。换言之，你首先需要有办法知道当前用户是谁，然后才可能有针对性地为其提供服务。

## 什么是单点登录

单点登录的意思就是说，你的应用既然放到了Teams里面了，那么Teams可以帮助你完成很自然的身份认证，用户可能都不需要输入账号和密码就能进入你的系统，甚至访问你的系统提供的数据。

这里的前提是，你的系统也是基于Azure AD进行身份认证的。

## 为什么需要单点登录

我们知道通过Teams JS SDK，我们可以获取到有限的一些信息，例如当前团队的id，用户的id，邮箱等。但一来这些信息并不充分，同时也不是足够的可靠。

信息不充分的问题，我在前面的 “二维码扫描应用” 章节中已经提到了。默认情况先，context可以给到应用程序用户的 `userPricinpalName` 和 `userObjectId` 这些信息。而没有更多信息，甚至连显示名称也没有。这是出于隐私方面的考虑。

为什么不可靠呢？以选项卡应用而已，Teams JS SDK提供的context对象本质上是一个浏览器的对象，而选项卡应用的页面是嵌入到一个iframe中的，那么这样一来，通过一定的技术手段，是完全可以模拟一个类似于Teams 的父容器，并且冒充一个假的context对象。

针对相关的页面，设置如下的Header可以“规定” 你的应用界面只允许嵌入到Teams客户端，这个看似是能解决上述问题的。但请注意，这里的“规定”是打引号的，也就是说，这种方法仍然是防君子，不能防小人。

```
"content-security-policy": "frame-ancestors teams.microsoft.com *.teams.microsoft.com *.skype.com",
"x-content-security-policy": "frame-ancestors teams.microsoft.com *.teams.microsoft.com *.skype.com",
"X-Frame-Options": "ALLOW-FROM https://teams.microsoft.com/"
```

另外，如果我们需要访问用户的更多信息，例如用户存放在OneDrive中的文件，我们需要得到相关的授权，获取有关的凭据，并且还要维护（保存起来，以及定时刷新）这些凭据。这可不是一个很简单的事情，如果实现了单点登录，这些工作都由Teams来帮你实现。

## 案例介绍

本例主要讲解如何实现**选项卡**应用的单点登录。我们至少需要实现下面的场景：

1. 获取到用户的显示名。
2. 读写用户OneDrive中的一个文件。

## 单点登录的原理

你可以通过[这里](https://docs.microsoft.com/zh-cn/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)的介绍了解更多详情，这个小节我会挑重点给大家介绍，并且通过实际的例子帮助大家理解。下面这个图大致解释了单点登录的原理。


此图并不是最新的，因为现在都不用Azure AD v1了，而是用v2。但原理没有差异。


![](<../.gitbook/assets/图片 400.png>)

简单地是这样的：

1. 你的应用尝试需要登录（或者获取当前用户身份）时，需要调用Teams SDK 提供的方法，`getAuthToken` 这个方法会返回一个专属的访问凭据。
2. 客户端决定是否需要弹出一个框，让用户自己接受或者授权。通常第一次是需要的。
3. 用户完全同意后，Teams客户端会拿着第一步得到的这个访问凭据，代表用户去请求Azure AD做身份认证。
4. 如果成功，则拿到一个真正的能访问通过Azure AD管理的资源（例如Microsoft Graph）的令牌。
5. 这个令牌，会返回到你的应用，然后你可以视情况地进行后续服务调用。

## 动手实验

下面通过实例来讲解这些过程。跟之前一样，我还是不准备使用复杂的模板，而是希望带领大家一步一步来实现，以便加深理解。

### 创建项目

通过下面的命令创建范例项目，并且安装必要的一些依赖项

```
npx create-react-app ssoapp --template typescript
cd ssoapp
yarn add @microsoft/teams-js @fluentui/react-northstar react-router-dom @microsoft/micros oft-graph-client crypto jwt-decode
yarn add @types/react-router-dom @types/microsoft-graph -D
```

### 设计主页

用如下代码替换掉`App.tsx` 的内容

```javascript
import { useState } from 'react';
import * as microsoftTeams from "@microsoft/teams-js";
import { Button, Flex, Segment, Provider, teamsTheme, Text } from "@fluentui/react-northstar";
import { BrowserRouter as Router, Route } from "react-router-dom";


function AuthStart() {
  return <Text content="身份认证开始..."></Text>
}

function AuthEnd() {
  return <Text content="身份认证结束..."></Text>

}

function Home() {

  const [authToken, setAuthToken] = useState<string>();
  const [graphToken, setGraphToken] = useState<string>();
  const [userName, setUserName] = useState<string>();
  const [fileContent, setFileContent] = useState<string>();

  return (
    <Flex column fill gap="gap.medium">
      <Button content="获取Auth Token"></Button>
      <Segment content={authToken} color="red"></Segment>
      <Button content="获取Graph Token"></Button>
      <Segment content={graphToken} color="blue"></Segment>
      <Button content="获取用户名"></Button>
      <Segment content={userName} color="green"></Segment>
      <Button content="获取文件内容"></Button>
      <Segment content={fileContent} color="black"></Segment>
    </Flex>

  );
}


function App() {
  //Teams客户端环境初始化
  microsoftTeams.initialize();

  return <Provider theme={teamsTheme}>
    <Router>
      <Route path="/" component={Home} exact></Route>
      <Route path="/auth-start" component={AuthStart} exact></Route>
      <Route path="/auth-end" component={AuthEnd} exact></Route>
    </Router>
  </Provider>

}

export default App;
```

通过上述这些代码，我简单的实现了一个主页，另外准备了两个后续会用的做登录用的页面。

### 启动应用

通过下面的指令把应用启动起来，它会默认打开本地的3000端口进行监听。

```
yarn start
```

用一个单独的命令行窗口，运行下面的命令为这个本地的端口做隧道访问。

```
ngrok http 3000 --host-header=rewrite
```

运行起来后，复制下面的红色地址

![](<../.gitbook/assets/图片 401.png>)

通过App Studio定义一个简单的测试应用，设置一个静态选项卡功能。注意，请先填写“App Details” 这个页面的信息。

![](<../.gitbook/assets/图片 402.png>)

然后通过下面的方式为自己进行安装

![](<../.gitbook/assets/图片 403.png>)

顺利完成安装后，该应用会打开，你会看到如下图所示的界面

![](<../.gitbook/assets/图片 404.png>)

### 读取客户端访问凭据

我们先来实现第一个按钮的功能，就是获取当前用户的客户端访问凭据。

```javascript
<Button content="获取Auth Token" onClick={() => {
  microsoftTeams.authentication.getAuthToken({
    successCallback: (token: string) => {
      setAuthToken(token);
    }
  })
}}></Button>
```

保存后，该应用会自动编译，你的页面也将会自动刷新。现在尝试点击一下该按钮，你会看到在其下方显示的一串文本信息。

![](<../.gitbook/assets/图片 405.png>)

这就是我们常说的JWT（Json web token），虽然你这样看不懂它什么意思，但其实很容易解析出来它包含的内容，例如你可以在 jwt.ms 这个网站中看到如下的解码效果。

![](<../.gitbook/assets/图片 406.png>)

这个token是由三个部分组成的，第一部分是声明，告诉应用程序这个token的类型，签名加密的算法。第二部分是主体，包含了令牌的基本信息，跟我们每个人的身份证也差不多，上面显示了谁颁发的这个token，以及相关的用户信息，权限声明等，第三部分无法解码，是签名信息。

所以说这个token并不神秘，也不是足够“安全”。这里所说的安全的意思是说，你不能直接根据这个文本，然后解码出来后，就相信提供这个token的人就是指定的用户。这个token只有拥有加密密钥的应用程序才能辨别真伪，这就好比说，你捡到了一个人的身份证，或者你干脆就伪造了一个身份证，你可能可以蒙骗不明真相的群众，但有关机构是能够通过一定的手段检测出来你是不是本人，或者这个身份证本身是不是合法的。

如果你只是想在该应用中显示一下用户的信息，并不会以该用户的信息去作为访问后台服务的依据，那么确实可以简单地读取这个token中的主体部分中的 `name` 字段来使用，首先安装导入这个库`import jwtdecode from "jwt-decode";`，并简单地修改代码如下

```javascript
<Button content="获取用户名" onClick={() => {
  if (authToken && !graphToken) {
    setUserName("本地读取到的用户名:" + (jwtdecode(authToken) as any).name);
  }
}}></Button>
```

保存代码，回到Teams中，点击“获取用户名”这个按钮，可以很容易地获取到用户的名字。如果仅仅用于显示，是非常不错的实现方案。

![](<../.gitbook/assets/图片 407.png>)

### 注册Azure AD应用程序

这个步骤很关键，而且也比较繁琐，请详细按照[这里](https://docs.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso#1-create-your-aad-application)的说明进行注册，后续我提供一个脚本。请检查核对下面的设置。

![](<../.gitbook/assets/图片 408.png>)

![这里权限申请设置可以为空，因为我们在前端访问时可以动态指定权限](<../.gitbook/assets/图片 409.png>)

![](<../.gitbook/assets/图片 410.png>)


> 【陈希章 更新于 2021/12/31】 脚本来了！ 请在PowerShell中安装 `code365scripts.teams`  这个模块，然后执行 `New-TeamsSSOAppliction -name myapp -url https://www.xizhang.com` 这样的命令即可。 请注意，这个命令根据了最新的文档 <https://docs.microsoft.com/zh-cn/microsoftteams/platform/m365-apps/extend-m365-teams-personal-tab?tabs=manifest-teams-toolkit> 的说明，不光把Teams客户端注册进去了，另外Outlook和Office客户端也通通注册了。关于这个脚本库的说明，请参考 <https://scripts.code365.xyz/code365scripts.teams/> 。



### 交换访问令牌

接下来我们要先把本地得到的SSO令牌，跟远端的AAD进行交换真正的服务访问令牌。这个在很多别的平台也是常见的做法，这个授权的流程叫 [on-behalf-of 流程](https://docs.microsoft.com/zh-cn/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。我们将参考[这个](https://docs.microsoft.com/zh-cn/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)文档来进行实现。

我们需要有一个中间层服务来执行这样的交换。当前这个项目，因为是用React模板创建的，所以它默认只是一个纯前端的应用。但我们可以通过一些有意思的技术实现在同一个项目中，既支持前端应用，也支持后端服务。

请按照 [https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#azure-functions-tool](https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#azure-functions-tool) 的说明安装Azure function tools.

在当前项目的根目录下面创建一个 `api` 的子目录，然后按`F1` 或 `Ctrl+Shift+P` 打开命令窗口，搜索`Azure Functions: Create New Project...` ，按照提示完成操作，请确认目录选择api， 语言选择 Typescript，函数名为 token。

![](<../.gitbook/assets/图片 411.png>)

切换到当前这个api目录后，执行下面的命令安装 `node-fetch` 这个模块。

```
yarn add node-fetch
```

使用如下代码替换这个index.ts 文件

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"
import jwt_decode, { JwtPayload } from "jwt-decode";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    const ssoToken = req.query.ssoToken as string;
    let tenantId = jwt_decode<JwtPayload>(ssoToken)['tid']; //获取租户的编号
    let accessTokenEndpoint = `https://login.microsoftonline.com/${tenantId}/oauth2/v2.0/token`;

    let accessTokenQueryParams = {
        grant_type: 'urn:ietf:params:oauth:grant-type:jwt-bearer',
        client_id: "692fb9c1-02ab-4bc0-bfaf-c270cedf85b8",
        client_secret: "W86QwNW8g~CP.v9dA-bKj_OuM~iJJ75yd3",
        assertion: req.query.ssoToken as string,
        scope: 'https://graph.microsoft.com/User.Read',
        requested_token_use: "on_behalf_of",
    };

    let body = new URLSearchParams(accessTokenQueryParams).toString();

    let accessTokenReqOptions = {
        method: 'POST',
        headers: {
            Accept: "application/json",
            "Content-Type": "application/x-www-form-urlencoded"
        },
        body: body
    };

    const fetch = require("node-fetch");

    let response = await fetch(accessTokenEndpoint, accessTokenReqOptions);
    let data = await response.json();
    if (!response.ok) {
        if ((data.error === 'invalid_grant') || (data.error === 'interaction_required')) {
            context.res = {
                status: "403",
                body: {
                    error: "要求授权"
                }
            }
        } else {
            context.res = {
                status: "500",
                body: {
                    error: "未知错误：无法交换令牌"
                }
            }
        }
    } else {
        context.res = {
            body: data
        }
    }

};

export default httpTrigger;
```

现在可以在当前这个api目录下面运行如下命令来启动这个项目

```
yarn start
```

你可以看到如下的运行效果

![](<../.gitbook/assets/图片 412.png>)

那么接下来，如何在前端应用中调用这个服务呢？回到咱们的选项卡应用的根目录，也就是api 目录所在的项目的根目录，找到package.json 这个文件，添加如下一行定义。

```
"proxy": "http://localhost:7071"
```

这个proxy定义的意思是，用户去访问选项卡应用时，除了我们通过react 路由定义的几个路径外，其他请求都转发到7071这个端口上面来（也就是function app在监听的端口），这样可以看起来像是同一个网站一样，避免还要实现跨域访问等问题。

### 完整实现用户信息读取

用如下代码替换掉App.tsx 文件

```javascript
import { useEffect, useState } from 'react';
import * as microsoftTeams from "@microsoft/teams-js";
import { Button, Flex, Segment, Provider, teamsTheme, Text } from "@fluentui/react-northstar";
import { BrowserRouter as Router, Route } from "react-router-dom";
import jwtdecode from "jwt-decode";
import crypto from "crypto";
import * as graph from "@microsoft/microsoft-graph-client";

// 这个组件用来在弹出的一个对话框中，去请求身份验证，这里会跳到Azure的登陆页面，并且在成功后跳回到对应的页面（用来接收access token），这里用到的是典型的 code-grant 的授权流。
function AuthStart() {
  useEffect(() => {
    microsoftTeams.initialize();
    microsoftTeams.getContext((context: microsoftTeams.Context) => {

      let tenant = context['tid'];
      let client_id = "692fb9c1-02ab-4bc0-bfaf-c270cedf85b8";
      let queryParams: any = {
        tenant: `${tenant}`,
        client_id: `${client_id}`,
        response_type: "token", //token_id in other samples is only needed if using open ID
        redirect_uri: window.location.origin + "/auth-end",
        scope: "https://graph.microsoft.com/User.Read",
        nonce: crypto.randomBytes(16).toString('base64')
      }

      let url = `https://login.microsoftonline.com/${tenant}/oauth2/v2.0/authorize?`;
      queryParams = new URLSearchParams(queryParams).toString();
      let authorizeEndpoint = url + queryParams;
      window.location.assign(authorizeEndpoint);

    });
  }, [])
  return <Text content="身份认证开始..."></Text>
}
// 这个组件用来接收身份验证的结果
function AuthEnd() {
  const getHashParameters = () => {
    let hashParams: any = {};
    window.location.hash.substr(1).split("&").forEach(function (item) {
      let [key, value] = item.split('=');
      hashParams[key] = decodeURIComponent(value);
    });
    return hashParams;
  }

  useEffect(() => {
    microsoftTeams.initialize();
    let hashParams = getHashParameters();
    if (hashParams["access_token"]) {
      // 这一句很关键，只有notifysuccess后，窗口会被关闭，然后继续后续的操作
      microsoftTeams.authentication.notifySuccess(hashParams["access_token"]);
    } else {
      microsoftTeams.authentication.notifyFailure("Consent failed");
    }
  }, [])

  return <Text content="身份认证结束..."></Text>

}

// 这个组件是主界面
function Home() {

  const [authToken, setAuthToken] = useState<string>();
  const [graphToken, setGraphToken] = useState<string>();
  const [userName, setUserName] = useState<string>();
  const [fileContent, setFileContent] = useState<string>();

  return (
    <Flex column fill gap="gap.medium">
      <Button content="获取Auth Token" onClick={() => {
        // 这个按钮用来获取本地的客户端凭据
        microsoftTeams.authentication.getAuthToken({
          successCallback: (token: string) => {
            setAuthToken(token);
          }
        })
      }}></Button>
      <Segment content={authToken} color="red"></Segment>


      <Button content="获取Graph Token" onClick={async () => {
        // 这个按钮用来获取交换得到的graph 令牌
        let serverURL = `api/token?ssoToken=${authToken}`;
        let response = await fetch(serverURL);
        if (response) {
          let data = await response.json();
          if (!response.ok && data.error === '要求授权') {
            microsoftTeams.authentication.authenticate({
              url: window.location.origin + "/auth-start",
              width: 600,
              height: 535,
              successCallback: (result) => { setGraphToken(result); },
              failureCallback: (reason) => { console.log(`交换token失败，原因是:${reason}`) }
            });

          } else if (!response.ok) {
            console.log(data.error);

          } else {
            setGraphToken(data["access_token"]);
          }
        }
      }}></Button>
      <Segment content={graphToken} color="blue"></Segment>


      <Button content="获取用户名" onClick={async () => {
        if (authToken && !graphToken) {
          setUserName("本地读取到的用户名:" + (jwtdecode(authToken) as any).name);
        }
        else if (graphToken) {
          // 这里拿到的graphToken，可以用来继续访问其他资源
          const client = graph.Client.init({
            authProvider: (done: any) => {
              done(null, graphToken);
            }
          })

          const user = await client.api("/me").get();
          console.log(user);
          setUserName(user.displayName);

        }
      }}></Button>
      <Segment content={userName} color="green"></Segment>
      <Button content="获取文件内容"></Button>
      <Segment content={fileContent} color="black"></Segment>
    </Flex>

  );
}


function App() {
  //Teams客户端环境初始化
  microsoftTeams.initialize();

  return <Provider theme={teamsTheme}>
    <Router>
      <Route path="/" component={Home} exact></Route>
      <Route path="/auth-start" component={AuthStart} exact></Route>
      <Route path="/auth-end" component={AuthEnd} exact></Route>
    </Router>
  </Provider>

}
export default App;

```

为了让大家易于理解，我把所有代码都写在了一个文件中，同时也尽量提供了注释。

下面可以测使一下这个应用的运行效果，在点击 “获取Auth Token”后，再点击 “获取 Graph Token” 这个按钮，因为是第一次使用，所以会弹出一个对话框，要求用户自己进行授权。

![](<../.gitbook/assets/图片 413.png>)

只有用户点击 “Accept”后，才能拿到真正的访问令牌。


如果不想每个用户都弹出这个界面，可以由管理员集中进行授权。后续会介绍如何操作。


![](<../.gitbook/assets/图片 414.png>)

然后点击“获取用户名” 的话，就能获取到真正可靠的用户信息了。

![](<../.gitbook/assets/图片 415.png>)

请注意，后续直接可以获取到Graph token，并且可以继续访问其他资源。这就是单点登录的意义。

### 实现文件读取

为了给大家展示一下单点登录方案的强大，除了获取到当前用户信息外，我们可以用一点代码来实现一下文件读取。

作为演示目的，我先在当前用户的OneDrive for Business的根目录下面放置了一个简单的文本文件，如下所示：

![](<../.gitbook/assets/图片 416.png>)

添加如下的一点代码来读取文件

```javascript
<Button content="获取文件内容" onClick={async () => {
  //实现文件读取
  if (graphToken) {
    const client = graph.Client.init({
      authProvider: (done: any) => {
        done(null, graphToken);
      }
    });

    const file = await client.api("/me/drive/root:/demo.txt").get();
    const url = file["@microsoft.graph.downloadUrl"];
    fetch(url).then(value => value.text()).then(text => setFileContent(text));
  }

}}></Button>
```

为了读取文件，我们还需要申请 Files.Read这个权限

```
const scope = "https://graph.microsoft.com/User.Read https://graph.microsoft.com/Files.Read";
```

用户再次尝试“获取Graph Token”的话，将弹出下面这样的对话框

![](<../.gitbook/assets/图片 417.png>)

“接受”后，点击 “获取文件内容” 按钮，你会看到我们可以将文本文件的内容读取出来。

![](<../.gitbook/assets/图片 418.png>)

本案例完整源代码，可以通过下面查看或者下载

<https://github.com/code365opensource/teamsapp-samples-tab-sso>


单点登录一方面简化了用户登录的过程，他们一般最多需要一次登录，然后就可以安全高效地访问到用户信息，以及其他授权的资源。这里的资源，还不仅仅限于Microsoft Graph这种由微软提供的资源，甚至可以是你自己的一个后端服务。但关于这一点，这里就先不展开了。


## 通过Microsoft Teams toolkit 实现单点登录

上面的步骤很有趣，如果你愿意，我强烈推荐你完整地跟着做动手实验，以便更好地理解单点登录实现的原理。一旦你熟悉后，你可能就想能否跳过一些步骤，或者你可能就是想更快地实现单点登录，那么我推荐你了解一下Microsoft Teams toolkit 这个插件，在Visual Studio Code中快速地创建和调试应用。

<https://developer.microsoft.com/en-us/office/blogs/how-to-create-single-sign-on-authentication-for-tab-apps-with-microsoft-teams-toolkit-for-visual-studio-code/>

这个工具还在不断迭代开发中，目前已经提供如下的功能，请参考

![](<../.gitbook/assets/图片 419.png>)

## 管理员授权

在上面的步骤中，大家看到在用户第一次使用我们这个应用时，他们可能会提醒有些权限需要他们进行授权，这在绝大部分时候都是很好的提醒，让用户意识到有某个应用程序可能会代表他们去访问他们在云端或后台中的资源。

但是，如果你连这一步都想省略掉，尽量给用户最流畅的体验。那么，你可以要求管理员集中地做一次授权，有两种做法实现这个目的：

通过Azure 门户操作，如下图所示。如果你是为自己公司开发应用，你可以把这个App，分享给你们公司的管理员，然后他们可以看到这个应用，然后可以点击类似下面这个按钮进行同意。

> 请注意，这里需要把所有需要的权限都定义清楚。


![](<../.gitbook/assets/图片 420.png>)

完成授权后，有关的状态会变成绿色，如下图所示。

![](<../.gitbook/assets/图片 421.png>)

另外也可以通过下面给管理员发送这个链接，他们即可通过网页完成授权 [https://login.microsoftonline.com/common/adminconsent?client\_id=\<AAD\_App\_ID>](https://login.microsoftonline.com/common/adminconsent?client\_id=%3CAAD\_App\_ID%3E)&#x20;

## 多租户应用开发 <a href="multi-tenant-apps" id="multi-tenant-apps"></a>

上面我们的例子，演示了如何在自己公司的租户中实现单点登录，这通常是比较容易的，也很安全可控。

对于广大的合作伙伴来说，需要考虑的另外一个问题是，你开发的Teams应用可能是一个通用的产品，不是仅仅给一个客户使用，那么你注册的AAD 应用程序，显然不可能跑到客户的AAD中去注册，而是在一个公用的地方（通常是你公司的AAD）中完成，并且要将这个应用程序设置为 “多租户应用”，例如下图所示：

![](<../.gitbook/assets/图片 422.png>)

但这里会有一个潜在的挑战，你很快就会看到。出于对于安全方面的考虑，产品组在2020年11月做了一些政策方面的调整，如果你注册的是一个“多租户应用”，那么该应用是所有者必须能证明是一个合法的微软合作伙伴，需要拥有MPN——微软合作伙伴编号，还需要进行域名验证，能力验证等。

![](<../.gitbook/assets/图片 423.png>)

点击上图中的链接，你需要完成必要的配置。

![](<../.gitbook/assets/图片 424.png>)

如果不能顺利完成这些配置和验证，你的应用还是能正常工作，但是普通用户将无法进行授权，而是必须由管理员来授权。这在某些时候，对于你的应用推广可能会带来一些麻烦。

下图是一个已经顺利完成验证的案例

![](<../.gitbook/assets/图片 425.png>)
