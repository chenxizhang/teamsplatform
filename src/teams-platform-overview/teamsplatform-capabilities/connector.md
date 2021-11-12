---
description: 让外部系统与Teams无缝地整合
---

# 连接器（Connector）

Microsoft Teams 是设计用来作为现代办公的一个枢纽（Hub），这不仅体现在它把微软现有的各种应用或服务很好地整合起来，也意味着它可以与公司现有的各种业务系统无缝地整合。此前我们已经介绍过利用选项卡可以把企业应用整合到客户端界面中来（可点击下面的链接了解），这一节则要谈一谈另外一种层面的整合：数据和逻辑层面。


[teams-tab-overview.md](teams-tab-overview.md)


连接器适用的典型场景是：

1. **在客户端中接收来自外部系统的通知消息**。你可以先进行订阅然后定期接收，也可以在具体事件发生时接收推送。
2. **在客户端中发送消息给外部系统**。你可以以一种与机器人交互的类似体验，给外部系统进行整合，但这里不要求你的外部系统实现机器人的框架，也不要求部署到微软的机器人平台。

连接器是专属频道的扩展功能，目前不支持针对个人，或者群聊，会议设置连接器。如果需要配置连接器，需要选择某个频道，然后在快捷菜单中选择 `连接器`。

![](<../../.gitbook/assets/图片 (36).png>)

## 订阅外部系统的通知

其实连接器这个技术，早在2016年就在Office 365中引入了，当时是主要应用于Outlook客户端，如果有兴趣，你可以参考下面这个官方的文档介绍（英文）。

<https://www.microsoft.com/en-us/microsoft-365/blog/2016/03/25/introducing-office-365-connectors/>

Office 365的连接器，是基于 `订阅/发布` 这种架构来实现的，即外部事件源需要预先定义一个订阅配置页面，Outlook用户主动地发起订阅某些事件，设置希望接收的频率（或条件），然后这些配置保存在外部事件源的系统中，在特定的时间到来时或特定事件发生时，它会通过一个特定的地址给Outlook客户端推送消息。

同样的逻辑，现在也可以用到Microsoft Teams 里面来，目前已经有上百个按照Office 365 连接器规划做好的应用直接可以使用。例如下面这个例子是我们使用RSS 这个标准连接器，为某个频道订阅主题为 `Teams 平台` 的博客推送，并且要求它每6个小时推送一次。

![](<../../.gitbook/assets/图片 (37).png>)

完成配置后，用户可以在频道中看到相关的信息，并立即接收到第一次的推送（通常是10条内容），然后每隔一段时间（这里设置为6小时），用户又会收到最新的10条内容。

![](<../../.gitbook/assets/图片 (38).png>)

类似的场景还有非常多，例如研发团队希望接收Github或者DevOps系统中的事件通知，或者经理希望接收办公自动化（OA）系统的审批通知等。

这种针对某个特定事件源的订阅，当你很清楚事件源的情况下，是很有效的方法。但是，如果你只是希望在一个频道中接收各种通知消息，不管事件源到底是具体什么系统，且这些系统不想做大的改造的话，Microsoft Teams还提供一种更加灵活的、也是Teams原生的实现方式。

## 按需接收外部系统的通知

Office 365 连接器的实现方案中，有一个关键的技术细节，就是用户完成配置后，客户端会负责把一个内部的 **Webhook地址 **提交给外部事件源，以便事件源在需要时可以通过给这个地址发送消息来实现推送。

那么，假设我们能为每个频道生成一个唯一的 Webhook 地址，然后把这些地址分发给受信任的外部应用系统，让他们自己决定在什么时候给这个地址发送消息即可，是不是也是一种不错的方案呢？

这个实现方式，在Microsoft Teams中叫做 `Incoming webhook`, 中文也可以说 `传入钩子` 吧。

![](<../../.gitbook/assets/图片 (39).png>)

配置 Incoming Webhook 很容易，只需要提供一个名称和图标即可。

![](<../../.gitbook/assets/图片 (40).png>)

这样你可以得到了一个URL, 你可以用任何熟悉的语言用极少量的代码即可实现消息推送。例如下面我用PowerShell 做一个演示。请打开你电脑上面的PowerShell，修改下面代码中第5行的https开头的这个地址（改成你自己的Webhook地址），然后运行它。

```
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Content-Type", "application/json")
$body = "{`"text`": `"<b>你好</b>，这是来自外部系统的一个推送消息, 你可以用HTML格式定义消息。<br /><br /><img src='https://plusrew.com/wp-content/uploads/2020/06/hero_msteams.jpg' /><br /> <p> <a href='https://teamsplatform.code365.xyz'>详情请参考 https://teamsplatform.code365.xyz/</a></p>`"}"
$body = [System.Text.Encoding]::UTF8.GetBytes($body)
Invoke-RestMethod 'https://chinateamscommunity.webhook.office.com/webhookb2/7e79a292-3721-466b-af2d-e99af414393e@0aa03876-6fa6-4df8-9f90-a10f103dfd9a/IncomingWebhook/05ffa5f83d9b41a589bd1a1e97480b8a/f4883f2a-c2c4-47f5-b590-28a12cf053f8' -Method 'POST' -Headers $headers -Body $body
```

你可以很快看到一个新的消息出现在频道中。

![](<../../.gitbook/assets/图片 (41).png>)

以上使用的消息格式是HTML的，灵活性很大，除了不能用脚本之外，一切都很好。如果你推送的消息，希望能让用户进行输入，并且提交数据，就可以利用到自适应卡片的技术了。下面是另外一个例子：

![](<../../.gitbook/assets/图片 (43).png>)

如果你想尝试这个例子，请下载下面这个范例文件

[自适应卡片消息范例文件](../../.gitbook/assets/message.json)


然后在PowerShell运行下面的命令

```
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Content-Type", "application/json")
Invoke-RestMethod 'https://chinateamscommunity.webhook.office.com/webhookb2/7e79a292-3721-466b-af2d-e99af414393e@0aa03876-6fa6-4df8-9f90-a10f103dfd9a/IncomingWebhook/05ffa5f83d9b41a589bd1a1e97480b8a/f4883f2a-c2c4-47f5-b590-28a12cf053f8' -Method 'POST' -Headers $headers -InFile message.json
```

很有趣并且也很简单，对吗？你可能已经发现，在使用Incoming webhook的方案中，没有做身份验证，也就是说，只要有这个地址，谁都可以推送消息。如果你想要做身份验证，则需要考虑这一节开头提到的订阅连接器的方案。

## **在客户端中发送消息给外部系统**

前面我们介绍了两种连接器，他们都可以从外部系统中接收推送信息。那么，当我们在Teams中有些信息要发送给外部系统时，又该如何实现呢？

Microsoft Teams 平台提供了一种叫做 Outgoing webhook （传出webhook） 的能力，与上面的Incoming webhook正好相反，它就是指把消息往外传的一种方案。外部系统只需要给出一个公开的地址，然后按照标准的方法解析请求，并且回复规定格式的消息即可。

Outgoing webhook必须在团队级别配置，如下图所示

![](<../../.gitbook/assets/图片 (44).png>)

你也可以用 [https://teamsplatform.free.beeceptor.com/api/cardmessage](https://teamsplatform.free.beeceptor.com/api/cardmessage) 这个由我创建的模拟的API进行测试。点击“创建” 按钮会出现下面的提示。这个是用来做安全验证的，目前我们暂时用不着，你直接点击“关闭”即可。

> 如果你想测试返回标准文本消息，则用  [https://teamsplatform.free.beeceptor.com/api/textmessage](https://teamsplatform.free.beeceptor.com/api/textmessage) 这个地址进行配置即可。
>
> 请注意，这个地址是免费的，每天只允许调用50次，如果你发现不能调用，请隔天再试。

![](<../../.gitbook/assets/图片 (45).png>)

接下来在频道聊天中，可以用和机器人很类似的交互方式，发送消息给这个连接器，并且从它这里接收回复。例如下图所示

![](<../../.gitbook/assets/图片 (46).png>)

用这种方式实现的连接器，可以实现跟机器人很类似的场景，即根据用户的输入，动态地进行回复。区别在于，这种方式仅适合于频道，同时它不要求在微软的机器人平台上注册，在某些情况下可能会简单一些。
