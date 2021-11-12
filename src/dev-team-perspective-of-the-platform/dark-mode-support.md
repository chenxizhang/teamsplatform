---
description: 支持多种不同界面风格
---

# 暗黑模式支持

我不记得什么时候开始流行起来的“暗黑模式/深色模式”（英文叫 Dark Mode），现在主流的应用乃至手机都默认内置支持暗黑模式，在iPhone手机上面，还可以自动根据时间切换呢。据说这个模式特别适合在夜晚时使用，对眼睛有些好处吧。

![](<../.gitbook/assets/图片 382.png>)

Teams的客户端也默认支持暗黑模式，并且还支持多一种，叫高对比度模式，这种模式对于视力不太好的用户来说会有很大的帮助。

![](<../.gitbook/assets/图片 383.png>)

## 如何支持多主题

那么，如何让你设计的应用，也能支持以上提到的三种模式呢？首先，这个要从不同的场景来介绍。

如果是机器人，或者消息扩展，或者连接器等开发场景，本质上它们与用户的交互，都是通过消息来实现的，这种消息可能是文本消息，也可能是卡片消息。而不管是哪一种，因为最终的呈现都是由Teams自己来实现，所以它默认就会根据当前客户端的主题色进行自动切换，你无需做任何的额外工作。例如下面几个卡片，在不同的主题模式下显示的效果。

![浅色模式](<../.gitbook/assets/图片 384.png>)

![深色模式](<../.gitbook/assets/图片 385.png>)

![高对比度模式](<../.gitbook/assets/图片 386.png>)

作为应用开发者，你真正需要关心的是选项卡应用，因为这些界面是由你自己实现的。那么到底怎么实现呢？下面是我总结的三个经验和常见做法：

1. 尽量用到官方提供的组件库来构建界面
2. 默认加载当前客户端的主题
3. 检测Teams客户端的主题切换

要实现上面的东西不难，因为官方已经提供了框架进行支持。下面通过一个实际的例子带领大家了解一下吧。

## 创建项目

本例使用 `Node.js` 来实现，请安装好必要的工具，并运行下面的命令来创建项目。

```
npx create-react-app themeapp --template typescript
```

然后继续运行命令

```
cd themeapp
yarn add @microsoft/teams-js @fluentui/react-northstar
```

这里添加的两个模块，都是微软官方提供的，第一个是Teams的客户端JS SDK，可以用来跟客户端打交道（本例用来检测客户端的主题，及其变化），第二个是官方提供的UI框架，它里面定义了很多我们需要用到的组件，可以直接用，并且它定义了以上提到的三套主题的样式。

## 使用当前主题

通过如下的方式，修改`App.tsx` 的代码

```javascript
import { Button, Flex, FlexItem, Provider, Segment, teamsTheme } from "@fluentui/react-northstar";

const App = () => {


  return (
    <Provider theme={teamsTheme}>
      <Flex column fill gap="gap.small">
        <Flex>
          <FlexItem push>
            <Segment content="用户名" color="blue"></Segment>
          </FlexItem>
        </Flex>

        <Segment content="第一行文字." color="red" />
        <Segment inverted content="第二行文字." color="red" />
        <Button content="按钮" primary></Button>
      </Flex>
    </Provider>
  );
};


export default App;
```

这个代码其实很简单，fluentui 这套框架提供了一个Provider的组件，可以从顶层往下层传递主题的信息，而teamsTheme是当前客户端的主题信息。其他一些代码都是基本的一些布局和组件，这里只是做范例演示。

![](<../.gitbook/assets/图片 387.png>)

看起来还不错，但是如果你现在去切换设置中的主题，你会发现界面并没有变化，例如即便我切换到暗黑模式，以上的界面还是没有变化，而在暗黑模式下，会很不协调。

![](<../.gitbook/assets/图片 388.png>)

## 检测主题的变化

我们需要检测到第一次加载时的主题，以及后续用户手动在进行主题的切换这个行为。这个可以通过 JS SDK来实现。下面是实现的代码。

```javascript
import { Button, Flex, FlexItem, Provider, Segment, teamsDarkTheme, teamsHighContrastTheme, teamsTheme, ThemePrepared } from "@fluentui/react-northstar";
import * as microsoftTeams from "@microsoft/teams-js";
import { useState, useEffect } from "react";


const App = () => {

  const [theme, setTheme] = useState<ThemePrepared>();

  useEffect(() => {
    microsoftTeams.initialize();
    microsoftTeams.getContext((ctx) => {
      switch (ctx.theme) {
        case "dark":
          setTheme(teamsDarkTheme);
          break;
        case "contrast":
          setTheme(teamsHighContrastTheme);
          break;
        default:
          setTheme(teamsTheme)
          break;
      }
    });

    microsoftTeams.registerOnThemeChangeHandler((theme) => {
      switch (theme) {
        case "dark":
          setTheme(teamsDarkTheme);
          break;
        case "contrast":
          setTheme(teamsHighContrastTheme);
          break;
        default:
          setTheme(teamsTheme)
          break;
      }
    })

  }, [])


  return (
    <Provider theme={theme || teamsTheme}>
      <Flex column fill gap="gap.small">
        <Flex>
          <FlexItem push>
            <Segment content="用户名" color="blue"></Segment>
          </FlexItem>
        </Flex>

        <Segment content="第一行文字." color="red" />
        <Segment inverted content="第二行文字." color="red" />
        <Button content="按钮" primary></Button>
      </Flex>
    </Provider>
  );
};


export default App;
```

现在，当用户切换到暗黑模式后，我们都界面也会自动更换颜色，这样看起来就很舒服，不是吗？

![](<../.gitbook/assets/图片 389.png>)

## 使用第三方库

上面的实现方式挺简单的，但是代码看起来还是有点繁琐。其实上面这么写出来，是想让大家理解这个原理，真正开发时，你可以通过一定第三方库来更加容易地实现。这个库叫 `msteams-react-base-component` , 你可以通过[这里](https://github.com/wictorwilen/msteams-react-base-component)了解更多它的信息。

```
yarn add msteams-react-base-component
```

用如下的代码覆盖掉`App.tsx` .

```javascript
import {
  Button,
  Flex,
  FlexItem,
  Provider,
  Segment
} from "@fluentui/react-northstar";
import { useTeams } from "msteams-react-base-component";



const App = () => {

  const [{ theme }] = useTeams();

  return (
    <Provider theme={theme}>
      <Flex column fill gap="gap.small">
        <Flex>
          <FlexItem push>
            <Segment content="用户名" color="blue"></Segment>
          </FlexItem>
        </Flex>

        <Segment content="第一行文字." color="red" />
        <Segment inverted content="第二行文字." color="red" />
        <Button content="按钮" primary></Button>
      </Flex>
    </Provider>
  );
};


export default App;
```

只要一行代码就可以搞定，看起来真不错。

这个案例的完整代码，请参考

<https://github.com/code365opensource/teamsapp-samples-tab-theming>

