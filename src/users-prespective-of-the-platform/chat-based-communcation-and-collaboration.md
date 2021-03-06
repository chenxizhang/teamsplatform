---
description: 聊天也有很多学问
---

# 基于聊天的沟通协作

我做了这么多年的计算机软件相关的工作，仍然忘不了当年选择这个行当时亲友们的忠言劝告：干点别的稳当点的工作吧，天天 “玩电脑” 那还能养活自己啊？！这个情境，就好比现在有人居然跟你说光聊天就能沟通协作，甚至不仅可以提高工作效率，还可以改善人类的生活？这...... 谁信啊？

![](<../.gitbook/assets/图片 80.png>)

## 聊天为何如此有价值

**信不信由你，聊着聊着就把事情办了，正成为一种风尚，认真说起来甚至可以说是回归到人类本原了。**试想一下，在没有信息化的时代，人们的很多沟通和协作不就是靠着电话啦，信笺啊，或者大量的线下会面来完成的吗？后来慢慢有了电子化的系统，我们开始依赖各种软件、邮件、流程来进行沟通及相互的约束。慢慢的，人和人的沟通其实逐渐变成人和电脑的沟通，人们甚至更愿意相信电脑，而不是在自己面前活生生的那个人。

但是，这种回归又不是简单的倒退。我先给大家讲一段故事。2013年左右，已经有多次创业经验的 **斯特尔特.巴特菲尔德 **虽然在游戏项目上失败了，却意外地发现内部开发的一个工具可能很有市场价值。和很多讲求效率和个性，并且随时都有一种 ”市面上的东西都很傻，我宁愿自己做一个“ 这种想法的软件开发团队一样，他们当时开发了一套用来内部沟通协作的工具，可以让大家不再淹没在来来回回的邮件中，可用更加灵活和及时的方式进行聊天，同时团队之间的沟通记录随时可以查到，并且这些历史记录慢慢地会沉淀出来有价值的、可利用的知识。

![](<../.gitbook/assets/图片 81.png>)

斯特尔特和他的团队决定发布这个软件，并且将公司名也改为同一个名字，它的全称就是由这句话的五个单词的首字母组成的：**“所有可搜索的会话和知识日志”（Searchable Log of All Conversation and Knowledge）——Slack.**

Slack 让人们通过对比重新认识了**对话**，并且通过 ”**可搜索**“ 这样的一个看似不起眼的改进，让这些对话和**知识**的**日志**变得更加有价值，一下子吸引了很多用户的关注，成为当年炙手可热的”独角兽“，很短时间内就完成上市，并最终在2020年被Salesforce 以大约270亿美金的天价收购。

2017正式发布的 Microsoft Teams 后来居上，从核心的理念是类似的，并且充分发挥微软深厚的技术能力和产品生态的优势，为广大的用户提供了更好的选择。

## 如何在Teams中进行搜索

不管是一对一的聊天、多人群聊、在会议中的讨论、以及针对某个话题的开放式讨论（在频道中完成），人们在Teams中进行沟通会留下很多有价值的日志，如何快速进行搜索呢？

### 搜索任意位置

你要搜索的内容可以分布在任意地方，不管是聊天里面，还是文件里面，抑或是你要搜索联系人名字等。任何时候，你可以在Teams客户端的顶部中间位置（命令框）中输入关键字，然后按下回车进行搜索。


也可以通过 `Ctrl +E` 这个快捷键


![](<../.gitbook/assets/图片 85.png>)

### 搜索特定位置

你可以通过 `Ctrl + F `这个快捷键，基于当前上下文进行搜索。例如，如果你当前是在打开某个团队的情况下，按下快捷键，可以立即在当前频道进行搜索。

![](<../.gitbook/assets/图片 82.png>)


你只能搜索到你自己或者参与过的会话和文件内容。


### 合规性审核和搜索

从技术上说，人们在Teams中的聊天都是存在Exchange Online的特定文件夹中的，共享的文档是保存在SharePoint Online或OneDrvie for Business的文件夹中的。如果对这一部分的细节有兴趣，请参考我此前的一个专门的文档。

[聊天消息到哪里去了](../.gitbook/assets/Microsoft Teams架构解析（2）——聊天消息到哪里去了-Ares Chen.pdf)


为什么要这么做呢？因为以上提到的这些Microsoft 365的核心组件，都已经得到了业界主流的安全和合规性认证，不管是从保护用户本身的数据权利，还是让组织有能力在必要时进行内容审查等等方面，形成了一套非常成熟的、广受客户认可的方案。

组织的部分人员，在得到明确授权的情况下，可以针对在Teams平台上的聊天历史进行合规性审查和搜索，这个部分的内容非常多，主要针对企业的管理层和IT部门。大家如果有兴趣，可适当地通过下面的链接了解一些概念。

<https://docs.microsoft.com/zh-cn/microsoft-365/compliance/ediscovery?view=o365-worldwide>

## 如何形成和利用知识？

什么是知识？如何形成和利用知识？ 我有如下的一点总结：


知识是指对一系列对话及其最终形成决策（或解决问题）过程所形成的见解。

1. 如果过程不能透明，则很难复制，也就很难形成知识。
2. 如果知识无法保存下来，也就无法继续利用。
3. 如果利用知识的方式不能智能化，那么它的价值也就很有限。


使用Microsoft Teams 平台，它用一种全新的方式，基于聊天展开沟通和协作，并且确保在安全透明的边界内能快捷地进行搜索，并且保存在了安全合规的地方。那么接下来就是如何有效率地进行利用了？

形成知识的前提是理解，或者用一种合适的形式对知识结构进行建模。人们在Teams上面每天的会议数以几十亿分钟计，文档那么多，相互之间的聊天和互动那么多，到底怎么提取里面有价值的信息，并且在人们需要时快速被找到，且能真的能进一步帮助到工作的改进呢？

针对这个话题，微软在行业内率先提出了一个员工体验平台（Employee Experience Platform ： EEP）的概念，并且定义和发布了Micrsooft Viva 套件，将于2021年晚些时候全面上市。

Microsoft Viva基于Microsoft Teams 这样一个入口，聚合了Microsoft 365的所有组件，并以人工智能技术，结合了员工发展的科学模型，有效地促进员工的沟通，学习，形成知识和见解。

![](<../.gitbook/assets/图片 86.png>)

有关Microsoft Viva的更多详情，请参考下面的官方介绍。

<https://www.microsoft.com/zh-cn/microsoft-viva>
