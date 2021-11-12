---
description: 开发你自己的扫码功能
---

# 二维码扫描应用

本书第一个完整的开发案例，我选择了实现了一个二维码扫描的个人应用。这是一个“有用”的场景，扫码在中国乃至全世界都是一个刚需，除了手机本身自带的摄像头（相机功能）可以扫码外，各种应用厂商也设计了各种各样的扫码应用，以便针对不同的二维码进行不同的处理。

你扫的不是码，而是码背后带有的商业或管理逻辑。

## 场景介绍

很多公司现在都是移动办公的，相对于之前来说，员工不再有固定座位，而是随到随坐，先到先得。员工的个人物品（如果有），则提供了单独的储物柜来存放。这种储物柜一般都没有钥匙（因为容易掉，也容易坏），而是通过扫码开箱。

一个常见的储物柜是下面这样的。

![](<../.gitbook/assets/图片 (301).png>)

## **应用设计**

我认为首先这个场景是“有用”的，能解决用户真正的问题。那么，它应该如何跟用户交互呢？

1. 它是一个**个人应用（Personal App）**，它可以直接为单个用户安装，并且可以固定在Teams客户端的工具栏上面。
2. 它应该**包含（也仅包含）一个选项卡**，所有的功能都可以在这个界面实现，不需要复杂的跳转和页面刷新。
3. 这个应用**主要是在手机上使用**，需要申请手机上面的摄像头权限，其使用界面需要自动在手机端适应，方便用户用手指操作，而不是鼠标。

从界面设计的角度来说，微软也提供了一套专门针对Teams设计的UI框架可供使用，这就是FluentUI下面的React-Northstar（北极星)。


你确实可以用任意的开发平台和语言来开发Teams应用（尤其是前端），但目前比较推荐的做法是使用React来实现，因为它提供了更加组件化的编程机制，和基于状态来呈现不同组件，很容易实现单页应用（Single Page Application, SPA），从运行性能和体验上也可能有一些优势。关于React的基础知识，请通过 [https://reactjs.org/tutorial/tutorial.html](https://reactjs.org/tutorial/tutorial.html) 进行学习。


你可以通过 [https://fluentsite.z22.web.core.windows.net/0.54.0](https://fluentsite.z22.web.core.windows.net/0.54.0) 了解和学习一下这套UI框架，如果你确实没有时间也没关系，当前这个项目尽量用到比较简单的部分。

这个框架甚至还提供了一个设计器，可以通过可视化的方式快速构建原型，请访问下面的地址

<https://fluentsite.z22.web.core.windows.net/0.54.0/builder>

![](<../.gitbook/assets/图片 (302).png>)


请注意，点击上图右上角第一个按钮 \</> 可以进入代码视图。下面我填出来的代码，你可以直接粘贴进去，快速看到界面效果。


## 状态设计

要开始做一个应用之前，我一般会习惯先思考当前应用对于用户来说，有哪几种不同的状态，而每个状态又需要用到哪些交互的元素。下面就以这个真实的案例来展开状态和组件设计。

### 状态1：用户当前没有已借用的箱子

这种状态下，除了顶部的问候语和底部的固定文字外，会在显目的位置显示“扫码开箱”的按钮，并且列出来此前用户已经借用箱子的记录。如果没有记录，那么这个部分可以是空白的。

![](<../.gitbook/assets/图片 (307).png>)

这个状态对应的代码

```
<div
  key="builder-root"
  data-builder-id="builder-root"
>
  <Flex
    column
  >
      <Text
      content="师傅，你好!"
      data-builder-id="wbczuqdcgp"
      important
      size="largest"
    />
      <Button
      content="扫码开箱"
      data-builder-id="6ykirazupyp"
      icon={<PopupIcon data-builder-id={undefined} />}
      iconPosition="before"
      primary
    />
  
    <List
      data-builder-id="i97jhhr8lnk"
      items={[
        {
          content: '归还时间：2020-1-2，使用时长3小时',
          header: '箱号25',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '2',
          media: <AcceptIcon key="gus233a02r8" data-builder-id="gus233a02r8"/>
        },
        {
          content: '取消操作',
          header: '箱号11',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '1',
          media: <BanIcon key="29gv0z4uap8" data-builder-id="29gv0z4uap8"/>
        }
      ]}
    />
    <Text
      align="center"
      content="版权所有@code365.xyz"
      data-builder-id="jfa83let6s"
    />
  </Flex>
</div>
```

### 状态2：用户当前还有未归还的箱子

根据规则，如果用户还有未归还的箱子，则不能继续借用其他箱子。所以，他将无法看到“扫码开箱”的按钮，而是在历史记录中，未归还的箱子这一行记录中会有一个“归还”按钮。

![](<../.gitbook/assets/图片 (306).png>)

这个状态对应的代码

```
<div
  key="builder-root"
  data-builder-id="builder-root"
>
  <Flex
    column
  >
      <Text
      content="师傅，你好!"
      data-builder-id="wbczuqdcgp"
      important
      size="largest"
    />
  
    <List
      data-builder-id="i97jhhr8lnk"
      items={[
        {
          content: <Button key="2v7dw1kbb4v" content="归还" data-builder-id="2v7dw1kbb4v"/>,
          header: '箱号12，正在使用',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '3',
          media: <ApprovalsAppbarIcon key="th08c34cjpp" data-builder-id="th08c34cjpp"/>
        },
        {
          content: '归还时间：2020-1-2，使用时长3小时',
          header: '箱号25',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '2',
          media: <AcceptIcon key="gus233a02r8" data-builder-id="gus233a02r8"/>
        },
        {
          content: '取消操作',
          header: '箱号11',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '1',
          media: <BanIcon key="29gv0z4uap8" data-builder-id="29gv0z4uap8"/>
        }
      ]}
    />
    <Text
      align="center"
      content="版权所有@code365.xyz"
      data-builder-id="jfa83let6s"
    />
  </Flex>
</div>
```

如果用户点击了“归还”按钮，箱子会被打开，再次关上的话，这个箱子就算归还了，然后就可以切换到**状态1**了。

### 状态3：用户扫码借用，但没有可用的箱子。

这种情况下，应用会提示有关的文字，并且在5秒后，自动回到状态1.

![](<../.gitbook/assets/图片 (305).png>)

这个状态对应的代码

```
<div
  key="builder-root"
  data-builder-id="builder-root"
>
  <Flex
    column
  >
    <Text
      content="师傅，你好!"
      data-builder-id="wbczuqdcgp"
      important
      size="largest"
    />

    <Text
      content="当前没有箱子可用，请稍后再试"
      data-builder-id="i9wragvzb9b"
    />

    <List
      data-builder-id="i97jhhr8lnk"
      items={[
        {
          content: '归还时间：2020-1-2，使用时长3小时',
          header: '箱号25',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '2',
          media: <AcceptIcon key="gus233a02r8" data-builder-id="gus233a02r8"/>
        },
        {
          content: '取消操作',
          header: '箱号11',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '1',
          media: <BanIcon key="29gv0z4uap8" data-builder-id="29gv0z4uap8"/>
        }
      ]}
    />
    <Text
      align="center"
      content="版权所有@code365.xyz"
      data-builder-id="jfa83let6s"
    />
  </Flex>
</div>
```

### 状态4：目前有可以借的箱子，用户可以完成借用操作

这个状态是大部分的情况，也是happy path。用户找到了可用的箱子，通过点击“马上打开” ，即可完成登记确认，然后切换到状态2. 如果用户在5秒内没有操作，则切换回状态1.

![](<../.gitbook/assets/图片 (308).png>)

这个状态对应的代码

```
<div
  key="builder-root"
  data-builder-id="builder-root"
>
  <Flex
    column
  >
    <Text
      content="师傅，你好!"
      data-builder-id="wbczuqdcgp"
      important
      size="largest"
    />

    <Text
      content="恭喜，当前7号箱子可用，请立即使用吧"
      data-builder-id="i9wragvzb9b"
    />
    <Button
      content="马上打开"
      data-builder-id="dpos63hz5bm"
      primary
    />
    <List
      data-builder-id="i97jhhr8lnk"
      items={[

        {
          content: '归还时间：2020-1-2，使用时长3小时',
          header: '箱号25',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '2',
          media: <AcceptIcon key="gus233a02r8" data-builder-id="gus233a02r8"/>
        },
        {
          content: '取消操作',
          header: '箱号11',
          headerMedia: '2020-1-2 11:30:17 PM',
          key: '1',
          media: <BanIcon key="29gv0z4uap8" data-builder-id="29gv0z4uap8"/>
        }
      ]}
    />
    <Text
      align="center"
      content="版权所有@code365.xyz"
      data-builder-id="jfa83let6s"
    />
  </Flex>
</div>
```

下图进一步展示了这四个状态及其他们之间的转换关系

![](<../.gitbook/assets/图片 (309).png>)

## 组件设计

仔细看看上面是四个状态，你当然也会发现，其实有些内容是共用的。这在React中，可以通过组件（Component）的概念将其抽象出来。简单地说，我们的这个应用，会有下面几个组件。

![](<../.gitbook/assets/图片 (310).png>)

1. 问候语，这里需要读取用户的基本信息（显示名称）
2. 扫码开箱按钮
3. 如果没有箱子可用的提示语
4. 如果有箱子可用的提示语，以及操作按钮
5. 用户现有借用记录，以时间降序排列
6. 底部固定文本

通过这样一番分解，我想接下来的开发就只是一个时间问题，随时都可以回来检查这个设计，并且可以据此进行测试和验收。

## 项目开发

为了给大家尽可能展示原汁原味的开发流程，我决定在这个项目的开发过程中，尽量不用复杂的模板，而是从零开始构建。你需要安装好VS Code和Node.js 即可。关于开发环境的准备，请参考&#x20;


[dev-env-and-tools.md](dev-env-and-tools.md)


### 通过 npx create-react-app 创建项目

在合适的目录，打开Powershell命令行，运行下面的命令，可以创建一个最简单的项目，我推荐用typescript来编程。

```
npx create-react-app scanit --template typescript
```

这个命令可能需要等一会儿才能完成。如果你发现确实很慢，请考虑优化一下npm的下载速度。请参考 [https://www.bing.com/search?form=MOZLBR\&pc=MOZI\&q=npm+%E4%B8%8B%E8%BD%BD%E9%80%9F%E5%BA%A6](https://www.bing.com/search?form=MOZLBR\&pc=MOZI\&q=npm+%E4%B8%8B%E8%BD%BD%E9%80%9F%E5%BA%A6) 这里的一些方法。【第三方提供，我不做任何保证哦】

![](<../.gitbook/assets/图片 (311).png>)

按照提示，你可以通过下面的命令快速验证一下该应用是否能正常运行。下面就是熟悉的React启动画面，默认情况下是在3000这个端口监听。

![](<../.gitbook/assets/图片 (312).png>)

下面我们安装几个Teams应用需要的模块，在当前的命令行窗口先用 `Ctrl+C` 终止运行，然后执行下面的命令

```
yarn add @microsoft/teams-js @fluentui/react-northstar
code .
```

用VS Code打开这个项目之后，目前的项目结构是这样的，请确认 react-northstar这个模块的版本至少是0.54.0， teams-js 这个模块的版本至少是1.9.0。

![](<../.gitbook/assets/图片 (313).png>)

### 修改入口文件

修改 index.tsx 这个应用入口文件。这里引入了northstar提供的Provider，以及为Teams应用准备的样式。

```javascript
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import { Provider, teamsTheme } from "@fluentui/react-northstar";

ReactDOM.render(
  <Provider theme={teamsTheme}>
    <App />
  </Provider>,
  document.getElementById("root")
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();

```

### 修改主程序组件

然后修改`app.tsx` 这个文件。如果大家稍微看一下代码，即可发现基本上就是按照上面我们谈到的状态和组件设计一步一步实现而已，所以前期的梳理很关键。


为了让大家看起来更加简单，我把组件（Greeting, Footer, History等）也都放在了这个文件。大家在实际项目上，如果代码量比较大，建议组件单独分文件存放。

从技术上说，Greeting，Footer等组件其实很简单，似乎都用不着用组件来拆分，我这里主要是给大家演示如何拆分组件。


```javascript
import React, { useEffect, useState } from "react";
import "./App.css";
// 导入UI组件
import {
  Text,
  Button,
  List,
  AcceptIcon,
  ApprovalsAppbarIcon,
  Flex,
  PopupIcon,
  Alert,
} from "@fluentui/react-northstar";
import * as microsoftTeams from "@microsoft/teams-js";

/* 顶部的问候组件 */
function Greeting(props: { UserName?: string }) {
  return (
    <Text content={props.UserName + ", 你好！"} important size="largest" />
  );
}

/* 扫码开箱按钮 */
function ActionBar(props: { click: any }) {
  return (
    <Button
      content="扫码开箱"
      icon={<PopupIcon />}
      iconPosition="before"
      primary
      onClick={()=>props.click()}
    />
  );
}

/* 底部的固定文字 */
function Footer() {
  return <Text align="center" content="版权所有@code365.xyz" />;
}

/* 用户的借用记录 */
function History(props: { Items?: IHistoryItem[]; click: any }) {
  if (props.Items === undefined || props.Items.length === 0) {
    return <></>;
  }

  var options: Intl.DateTimeFormatOptions = {
    dateStyle: "short",
    timeStyle: "short",
  };

  let fmt = new Intl.DateTimeFormat("zh-CN", options);

  let items = props.Items?.map((x) => {
    return {
      content: x.EndTime ? (
        "归还时间：" + fmt.format(x.EndTime)
      ) : (
        <Button content="归还" onClick={()=>props.click(x.BoxNumber)} />
      ),
      header: "箱号:" + x.BoxNumber.toString() + (x.EndTime ? "" : ",正在使用"),
      headerMedia: fmt.format(x.StartTime),
      key: x.BoxNumber.toString(),
      media: x.EndTime ? <AcceptIcon /> : <ApprovalsAppbarIcon />,
    };
  });

  return <List items={items} />;
}

// 如果当前没有可用箱子时显示的提示
function NotAvailable() {
  return <Text content="当前没有箱子可用，请稍后再试" />;
}

// 当前有可用的箱子时显示的提示和操作按钮
function Available(props: { id?: number; confirm: any }) {
  return (
    <Flex column>
      <Text content={"恭喜，当前" + props.id + "号箱子可用，请立即使用吧"} />
      <Button content="马上打开" primary onClick={()=>props.confirm(props.id)} />
    </Flex>
  );
}
// 历史记录类型定义
interface IHistoryItem {
  StartTime: Date;
  EndTime?: Date;
  BoxNumber: number;
}

// 应用的主组件
function App() {
  //定义几个状态
  const [state, setState] = useState<number>(0);
  //这个用来保存用户的名称
  const [userName, setUserName] = useState<string>();
  //这个用来保存用户的历史记录
  const [historyItems, setHistoryItems] = useState<IHistoryItem[]>();
  //这个用来记录当前用户所选择的箱子编号
  const [selectedBox, setSelectedBox] = useState<number>();
  //这个用来显示一些消息提醒
  const [message, setMessage] = useState<string>();

  //这个方法用来获取某个用户的历史记录
  const getHistoryItems = async (userName: string): Promise<IHistoryItem[]> => {
    // 目前使用硬编码实现，作为范例数据，第一个记录，可能返回有结束日期，也可能没有结束日期，这样来验证不同的显示
    // TODO 这里要改成读取数据的方式 
    return [
      {
        StartTime: new Date(),
        EndTime:
          Math.floor(Math.random() * 100) % 2 === 0 ? undefined : new Date(),
        BoxNumber: 1,
      },
      {
        StartTime: new Date(),
        EndTime: new Date(),
        BoxNumber: 23,
      },
      {
        StartTime: new Date(),
        EndTime: new Date(),
        BoxNumber: 11,
      },
    ];
  };

  // 这个方法会通过 “扫码开箱” 这个按钮来调用
  const scan = () => {};
  //归还某个箱子
  const returnBox = (id: number) => {};
  //确认借用某个箱子
  const confirm = (id: number) => {};

  // 这个只在初始化时调用，这里是读取用户信息和他历史记录信息
  useEffect(() => {
    // 初始化Teams，这个是必须调用的
    microsoftTeams.initialize();
    // 获取当前运行的环境上下文
    microsoftTeams.getContext(async (context) => {
      //告诉Teams当前应用已经初始化成功，通常会把“正在加载”的图标关闭掉
      microsoftTeams.appInitialization.notifySuccess();
      //获取用户的信息，为保护隐私，这里只能获取到userPrincipalName，通常就是用户的唯一的邮箱
      const user = context.userPrincipalName;
      // 设置当前的用户信息，以便通知界面更新
      setUserName(user);
      if (user) {
        // 获取当前用户的历史记录
        const items = await getHistoryItems(user);
        // 设置当前用户的历史记录，以便通知界面更新
        setHistoryItems(items);
        //检查是否有未归还的记录，如果有，则状态设置为2，否则为1
        let found = items.find((x) => x.EndTime === undefined);
        // 设置当前应用的状态
        setState(found ? 2 : 1);
      }
    });
  }, []);
  // 根据此前的设计，根据不同的状态，切换不同的组件，组成最合适的界面
  return (
    <Flex column>
      <Greeting UserName={userName} />
      {message && message?.length > 0 && <Alert content={message}></Alert>}
      {state === 0 && <Text content="这个网页只能在Teams中运行..." />}
      {state === 1 && <ActionBar click={scan} />}
      {state === 3 && <NotAvailable />}
      {state === 4 && <Available id={selectedBox} confirm={confirm} />}
      <History Items={historyItems} click={returnBox} />
      <Footer />
    </Flex>
  );
}
// 导出这个应用，传递给index.tsx
export default App;

```

另外为了格式化适当美观，我在`app.css` 这个文件底部添加了一点点定义。

```css
div.ui-flex {
  padding: 20px;
}

div.ui-flex > * {
  margin-top: 15px;
}

div.ui-flex > .ui-list {
  border: 1px solid rgb(243, 242, 242);
}
```

现在，如果你此前的网页没有关闭，你可以看到如下的效果

![](<../.gitbook/assets/图片 (314).png>)

没错，你要考虑这个网页是在Teams中运行的。如果用户尝试通过标准浏览器来加载，应该有所处理。那么到底怎么在Teams中加载这个应用并且进行调试呢？

### 通过网络隧道工具将本机地址发布到外网

在本章第一节中已经介绍了使用 `nrgok` 或 `localhost.run` 来实现网络隧道功能，把本地（localhost）的地址发布到外网，并且自动添加https支持，这是Teams应用的两个前提条件。请通过 [https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#localhost-https-support](https://teamsplatform.xizhang.com/dev-team-perspective-of-the-platform/dev-env-and-tools#localhost-https-support) 了解有关工具的安装和基本使用技巧。

本例中，我使用 `ngrok` 来实现。请打开命令行工具执行如下命令

```
ngrok http 3000
```

你会看到类似的界面。

![](<../.gitbook/assets/图片 (315).png>)

请注意复制上面的Forwarding中的https那个地址。我们接下来来定义Teams应用，并且看看在Teams中打开的效果，包括PC端和移动端。

### 通过App Studio定义Teams应用

在App Studio中创建一个新的应用，首先设置基本的信息，请认真填写。

![](<../.gitbook/assets/图片 (316).png>)

然后创建一个personal tab（个人选项卡）

![](<../.gitbook/assets/图片 (317).png>)

接下来还要为这个应用添加访问摄像头的权限，如下所示。这是为下一步做准备，毕竟你既然是要扫描二维码，那当然是需要申请必要的权限的。应用首先要申请权限，用户还需要确认接受权限，才能真正完成扫描操作。

![](<../.gitbook/assets/图片 (318).png>)

到目前为止，基本上打工告成了，你可以马上安装这个应用，并开始使用它。

![](<../.gitbook/assets/图片 (319).png>)

这个应用在PC端或网页端看起来像下面这样

![](<../.gitbook/assets/图片 (320).png>)

接下来，你可以打开你手机上的Teams App。与PC端操作有些不同，你需要上滑，看到应用列表，然后点击 “演示扫码” 这个应用，就可以看到在手机上的效果了。这个界面是会自动适应的。

![](<../.gitbook/assets/图片 (321).png>)

### 实现扫码功能

上面的步骤中，我们已经给这个应用申请了摄像头的权限，准确地说是 `media` 的权限，然后下面我们来看一下如何在代码中去执行扫码操作，我们需要将这个代码添加到 “扫码开箱” 这个按钮上。

使用下面的代码，替换原有代码中 131行 `const scan = () => {};` ，用来实现扫码功能。

```javascript
  // 这个方法会通过 “扫码开箱” 这个按钮来调用
  const scan = () => {
    //设置一些参数，这里是指30秒超时，如果没有操作
    const config: microsoftTeams.media.BarCodeConfig = {
      timeOutIntervalInSec: 30,
    };

    microsoftTeams.media.scanBarCode(
      (error: microsoftTeams.SdkError, decodedText: string) => {
        if (error) {
          let errorMessage;
          switch (error.errorCode) {
            case 100:
              errorMessage = "平台不支持";
              break;
            case 500:
              errorMessage = "内部错误";
              break;
            case 1000:
              errorMessage = "权限不足，用户没有接受";
              break;
            case 3000:
              errorMessage = "硬件不支持";
              break;
            case 4000:
              errorMessage = "错误的参数";
              break;
            case 8000:
              errorMessage = "用户取消操作";
              break;
            case 8001:
              errorMessage = "用户操作超时";
              break;
            case 9000:
              errorMessage = "平台太老";
              break;
            default:
              errorMessage = "未知错误";
              break;
          }
          setMessage("发生错误:" + errorMessage);
        } else if (decodedText) {
          // 这里是指扫描到了有关的二维码，本例不做具体的检测。通常会是一个网址，然后这个网址跟具体某个柜子是有关系的，然后通过这个网址发送请求，完成后续的开箱的操作。
          // 本例，只要扫描到了，我们就认为成功了，调用一个固定的API去获取可用的箱子。
          setMessage("扫码成功:" + decodedText);
        }
        //设置两秒后自动消息
        setTimeout(() => {
          setMessage("");
        }, 2000);
      },
      config
    );
  };
```

保存代码后，在Teams的移动App中刷新一下，如果看到 “扫码开箱” 这个按钮，你可以尝试点击一下了。不出意外的话，你会看到这个界面，要求你进行授权。

![](<../.gitbook/assets/图片 (322).png>)

你可以随便找一个二维码试一下效果，如果实在找不到，你可以用下面这个测试一下。

![](<../.gitbook/assets/图片 (323).png>)

正确完成扫描后，会把相关的网址显示在屏幕上，如下所示：

![](<../.gitbook/assets/图片 (324).png>)

当然，扫码过程中可能会有各种各样的情况，例如用户误操作，或者硬件问题等。出现那种情况，当前的代码也会将错误消息显示给用户看，例如：

![](<../.gitbook/assets/图片 (325).png>)

看起来不错，对吧？如果你不小心忘记授权，或者你之前授权了，现在想不再授权，你随时可以通过在 Teams的 “设置”中找到 “应用权限”，然后定位到对应的应用，然后决定哪些权限要授权等。

![](<../.gitbook/assets/图片 (326).png>)

### 实现数据读写

让我们来完成这个项目吧，现在我们把数据存取的功能做进去。为了简单期间，我没有计划真的写一个后台服务来实现数据读写，而是把数据存在 `localStorage` 里面，这样确保每个人都能在本地运行这个项目。

我们需要修改几个函数代码。首先是scan这个函数，我添加了当扫描成功后的代码，为了演示的更加有趣味性，我用随机数来生成可用箱子信息，并且还会判断是否为偶数。

```javascript
  // 这个方法会通过 “扫码开箱” 这个按钮来调用
  const scan = () => {
    //设置一些参数，这里是指30秒超时，如果没有操作
    const config: microsoftTeams.media.BarCodeConfig = {
      timeOutIntervalInSec: 30,
    };

    microsoftTeams.media.scanBarCode(
      (error: microsoftTeams.SdkError, decodedText: string) => {
        if (error) {
          let errorMessage;
          switch (error.errorCode) {
            case 100:
              errorMessage = "平台不支持";
              break;
            case 500:
              errorMessage = "内部错误";
              break;
            case 1000:
              errorMessage = "权限不足，用户没有接受";
              break;
            case 3000:
              errorMessage = "硬件不支持";
              break;
            case 4000:
              errorMessage = "错误的参数";
              break;
            case 8000:
              errorMessage = "用户取消操作";
              break;
            case 8001:
              errorMessage = "用户操作超时";
              break;
            case 9000:
              errorMessage = "平台太老";
              break;
            default:
              errorMessage = "未知错误";
              break;
          }
          setMessage("发生错误:" + errorMessage);
        } else if (decodedText) {
          // 这里是指扫描到了有关的二维码，本例不做具体的检测。通常会是一个网址，然后这个网址跟具体某个柜子是有关系的，然后通过这个网址发送请求，完成后续的开箱的操作。
          // 本例，只要扫描到了，就访问本地存储去尝试进行登记。
          setMessage("扫码成功:" + decodedText);
          // 随机生成一个箱子编号，也可能没有箱子的情况
          let id = Math.floor(Math.random() * 30);
          let available = id % 2 === 0;
          if (available) {
            let items = historyItems;
            items?.push({
              BoxNumber: id,
            });

            localStorage.setItem("history", JSON.stringify(items));
            setSelectedBox(id);
            setState(4);
          } else {
            setState(3);
            setTimeout(() => {
              setState(1);
            }, 5000);
          }
        }
        //设置两秒后自动消息
        setTimeout(() => {
          setMessage("");
        }, 2000);
      },
      config
    );
  };
```

然后我修改了“确认预定” 和 “归还”的函数，分别如下

```javascript
  //归还某个箱子
  const returnBox = (id: number) => {
    const found = localStorage.getItem("history");
    const items: IHistoryItem[] = found ? JSON.parse(found) : [];
    const item = items.find((x) => x.BoxNumber === id);
    const options: Intl.DateTimeFormatOptions = {
      dateStyle: "short",
      timeStyle: "short",
    };

    const fmt = new Intl.DateTimeFormat("zh-CN", options);
    if (item) {
      item.EndTime = fmt.format(new Date());
    }
    localStorage.setItem("history", JSON.stringify(items));
    setHistoryItems(items);
    setState(1);
  };
  //确认借用某个箱子
  const confirm = (id: number) => {
    const found = localStorage.getItem("history");
    const items: IHistoryItem[] = found ? JSON.parse(found) : [];
    const item = items.find((x) => x.BoxNumber === id);
    const options: Intl.DateTimeFormatOptions = {
      dateStyle: "short",
      timeStyle: "short",
    };

    const fmt = new Intl.DateTimeFormat("zh-CN", options);
    if (item) {
      item.StartTime = fmt.format(new Date());
    }
    localStorage.setItem("history", JSON.stringify(items));
    setHistoryItems(items);
    setState(2);
  };
```

还有”获取历史记录“的方法

```javascript
  //这个方法用来获取某个用户的历史记录
  const getHistoryItems = async (userName: string): Promise<IHistoryItem[]> => {
    // 此处作为演示目的，仅读取本地存储，所以其实用户名已经不重要。真实的开发中，可以把这个username的信息，传递给后台服务，进行搜索查询。
    const found = localStorage.getItem("history");
    return found ? JSON.parse(found) : [];
  };
```

看起来还不错，让我们来完整测试一下吧。

![](<../.gitbook/assets/图片 (327).png>)

你可能已经发现了，当前项目中显示的用户信息，是一个邮箱地址，而不是用户的显示名称。这是由于从隐私的角度，Teams默认没有把用户显示名称暴露出来。当然，我们有办法拿到这个信息。但这个需要进一步设置单点登录，涉及到的知识也比较多，而单点登录是一个通用性的功能，我会在后面专门的小节来介绍。

## 项目源代码

你可以在 [https://github.com/code365opensource/teamsapp-samples-scanit](https://github.com/code365opensource/teamsapp-samples-scanit) 下载完整项目源代码，希望你能在本地进行构建，并且尝试在你的Teams中运行起来。

## 可直接安装版本

为了让大家可以很方便测试，我把这个应用也发布到了云端，所以你可以用下面的安装包，在你的环境直接进行测试。

[安装包](../.gitbook/assets/scanit.zip)


下载这个安装包，然后在你的Teams客户端中为自己上传

![](<../.gitbook/assets/图片 (328).png>)
