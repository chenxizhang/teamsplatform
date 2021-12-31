---
description: 通过Teams让家长与老师互动，并了解孩子学习情况
---

# 家校通应用

这个案例是针对教育行业的场景。现在越来越多的学校（尤其是国际学校），使用Microsoft Teams作为教师和学生日常的沟通，协作，甚至教学的平台。但是，在教育场景中，还有一个角色也很重要，那就是家长。如何构建一个家校沟通的桥梁？这就是我设计本案例的出发点。

## 需求分析

作为案例分享，我尽可能把需求简化一些。

让我们来设想一下，老师、学生、家长这三个主要角色，他们有什么核心诉求？

1. 老师：看到家长留言，并且可以回复。可以发布消息给全体家长。
2. 学生：设置自己的家长信息，查看家长的留言。
3. 家长：了解自己孩子的作业完成情况，得分情况，老师评语等。除此之外，如果能给老师通过留言互动就更好了。

## 应用设计

我们需要考虑到的一个情况是：老师和学生拥有Microsoft Teams账号，他们也比较熟悉怎么使用。但家长是没有账号的，而且他们平常可能完全不用这个软件，他们需要一个特定的界面，并且通过他们熟悉的方式访问。

另外，在 Microsoft Teams for Education 的版本中，老师和学生是按照班级进行分组管理的，每个班级是一个团队，里面可能会有多位老师，和若干个学生—— 跟现实中的学校是一样的。在这个团队中，会根据需要建立多个频道进行日常的沟通和协作。

![](<../.gitbook/assets/图片 426.png>)

老师可以下发作业，学生可以提交作业，这都是Microsoft Teams内置的功能，本例并不实现这些场景。

![](<../.gitbook/assets/图片 427.png>)

所以，我们按下面的方式实现

1. 这个应用会有几个不同的界面，提供给老师、学生和家长使用。
2. 老师和学生，在对应的班级里面，通过频道选项卡的方式使用。
3. 学生可以决定是否开放信息给家长，并且设置家长信息（邮箱地址）。
4. 家长通过浏览器访问该应用，用他们的邮箱地址进行登录即可。

那么，就让我们开始吧。

## 实现方案

### 创建项目

我们将继续用Node.js来构建这个范例应用，而且不使用复杂的模板，尽可能用最原生态的方式，让大家了解细节。

> 请确保你认真阅读了本章第一节有关“ 开发环境和工具”的介绍。


```
npx create-react-app eduapp --template typescript
```

安装几个必要的模块

```
yarn add @microsoft/teams-js @fluentui/react-northstar react-router-dom
yarn add @types/react-router-dom -D
```

创建api目录，用Azure function core tools初始化一个服务项目，并且安装必要的模块

```
mkdir api
cd api
func init --language typescript --work-runtime node
func new -l typescript -n service -t "http trigger" -a anonymous
yarn add node-fetch lowdb uuid
yarn add @types/lowdbn @types/uuid -D
```

现在这个项目，在VS Code中看起来是下面这样的

![](<../.gitbook/assets/图片 428.png>)

配置前端项目和服务项目的连接，在项目根目录下面的package.json中添加一行配置

```
"proxy": "http://localhost:7071",
```

分别在根目录，和api 目录，运行下面的命令，将应用启动起来

```
yarn start
```

在VS Code中看起来是下面这样的

![](<../.gitbook/assets/图片 429.png>)

在浏览器中，默认打开了 http://localhost:3000 这个地址，你可以看到一个简单的应用界面

![](<../.gitbook/assets/图片 430.png>)

先不要着急。这只是React 应用的初始界面，我们很快就会进行修改。接下来你可以用 ngrok 为这个应用做外部隧道处理。

```
ngrok http 3000 --host-header=rewrite
```

![](<../.gitbook/assets/图片 431.png>)

通过App Studio创建一个范例应用，并为其定义一个“Team tab”

![](<../.gitbook/assets/图片 433.png>)

这个Team tab的详细页面中，将configuration URL 设置为 `你的网站根目录/config` ，类似下面这样

![](<../.gitbook/assets/图片 432.png>)

一切都准备就绪了，我们下面就专注于写代码实现吧。

### 实现可配置的选项卡

修改项目的App.tsx文件，根据需求，我们设计了三个不同的路由，分别代表家长端界面，老师和学生端界面，以及在Teams中的一个配置界面。

我们同时简单地实现了一个配置的功能。

```javascript
import { BrowserRouter as Router, Route } from "react-router-dom";
import { Provider, teamsTheme } from "@fluentui/react-northstar";
import * as microsoftTeams from "@microsoft/teams-js";

function Parent() {
  return <div>家校通,家长端</div>
}

function Tab() {
  return <div>家校通,老师和学生端，这里会根据不同的用户身份显示不同的内容</div>

}

function Configuration() {

  microsoftTeams.settings.setValidityState(true);
  microsoftTeams.settings.registerOnSaveHandler(evt => {
    microsoftTeams.settings.setSettings({
      entityId: "eduapp",//这个id，在真实的项目开发中，需要考虑设计成一个唯一性的字段，因为用户可以在任何团队和频道添加你这个选项卡，即便是同一个频道，也可以添加多次，那么如何进行区分呢？这个id 很重要。
      websiteUrl: "https://2adeb3f8ee53.ngrok.io",//在Teams中的选项卡，除了可以在Teams中打开外，还可以在浏览器中打开。如果在浏览器中打开，会调用这个地址。
      contentUrl: "https://2adeb3f8ee53.ngrok.io/tab",//这是在Teams里面打开的地址。如果要区分不同的选项卡实例，可以考虑在这个地址后面添加参数。
      suggestedDisplayName: "家校通"//这是推荐给用户的选项卡名称
    });


    evt.notifySuccess();
  })

  return (
    <>
      <h1>家校通应用</h1>
      <p>这个页面用来让用户进行配置，通常来说，这里会有一些可以配置的选项，然后根据选项的值，决定用户是否可以点击“保存”按钮，点击“保存”按钮时，会调用有关的接口，进行保存操作，并且通知客户端关闭当前的配置窗口。</p>
    </>
  )
}

function App() {
  microsoftTeams.initialize();

  return (
    <Provider theme={teamsTheme}>
      <Router>
        <Route exact path="/" component={Parent}></Route>
        <Route exact path="/config" component={Configuration}></Route>
        <Route exact path="/tab" component={Tab}></Route>
      </Router>
    </Provider>
  );
}

export default App;

```

你现在可以尝试在Teams中把这个应用安装起来，当你把它添加到一个团队（这意味着是一个班级），它会默认弹出来一个对话框，这里将加载我们设计好的配置页面，如下所示

![](<../.gitbook/assets/图片 434.png>)

点击 “保存”以后，这个选项卡就会显示出来。

![](<../.gitbook/assets/图片 435.png>)

而如果你直接访问当前这个网站的根目录，那么将显示下面的效果，也就是我们计划用来给家长提供功能的界面（目前还没有具体实现）。

![](<../.gitbook/assets/图片 436.png>)

看起来还不错，虽然界面比较简陋，但核心功能已经能看到了。下面我们就是逐一进行实现这三种角色的功能需求。

### 实现学生功能

这里会有一个关键问题，就是如何区分学生和老师？不同的学校做法可能不太一样，但通常都是把老师设置为团队的管理员，而学生只是一般的成员。

![](<../.gitbook/assets/图片 437.png>)

作为案例演示目的，简单起见，我就使用这个角色来判断老师和学生吧。但要注意，这样做其实不是很安全，后续请参考有关单点登录实现方案。


[sso-solution.md](sso-solution.md)


修改Tab 这个函数，这个界面会根据当前用户的角色来切换老师和学生这两个组件。

```javascript
function Tab() {

  const [context, setContext] = useState<microsoftTeams.Context>();//当前Teams上下文

  useEffect(() => {
    microsoftTeams.getContext(ctx => {
      setContext(ctx);
    })
  }, [])

  return (
    <>
      {context && context.userObjectId && context.groupId && context.userPrincipalName ?
        (context.userTeamRole ?
          <Student studentId={context.userObjectId} studentName={context.userPrincipalName.split('@')[0]} classId={context.groupId} /> :
          <Teacher teacherName={context.userPrincipalName.split('@')[0]} teacherId={context.userObjectId} classId={context.groupId}></Teacher>)
        : <div>正在加载...</div>
      }
    </>
  )

}
```

使用下面的代码实现Student这个组件，用来展示学生的页面

```javascript
function Student(props: { studentId: string, studentName: string, classId: string }) {

  const [settings, setSettings] = useState<[any]>();
  const [inputName, setInputName] = useState<string>();
  const [selectedType, setSelectedType] = useState<string>();

  useEffect(() => {
    fetch(`api/service?call=getParentInfoById&id=${props.studentId}`).then(x => x.json()).then(x => setSettings(x));
  }, [])

  return (
    <Flex column fill gap="gap.medium">
      <Segment color="brand" inverted>
        <Flex column fill gap="gap.small">
          <Text content={`你好,${props.studentName}`} size="larger"></Text>
          <Text content="在这里你可以设置家长信息，你需要输入邮箱地址，和选择称谓。你设置的家长，可以通过邮箱登录到家校通（网页版），然后可以查看你所在班级的作业及老师批改情况，并且可以跟你所在班级的老师进行留言互动。" size="small"></Text>
        </Flex>
      </Segment>

      {settings && settings.length > 0 &&
        <Segment>
          <h2>已经添加的家长</h2>
          <List items={settings?.map((x: any) => {
            return {
              key: x.email,
              header: x.type,
              content: x.email
            }
          })}></List>
        </Segment>
      }
      <Segment>

        <h2>添加新的家长</h2>
        <Form
          style={{ marginLeft: 20 }}
          onSubmit={() => {
            const data = {
              id: props.studentId,
              student: props.studentName,
              type: selectedType,
              email: inputName,
              class: props.classId
            };
            //调用接口提交数据
            fetch("api/service?call=addParentInfo", {
              method: "post",
              body: JSON.stringify(data)
            }).then(x => {
              let temp = settings;
              temp?.push(data);
              setSettings(temp);
            })
          }}
        >

          <FormInput
            label="家长邮箱"
            name="firstName"
            id="first-name"
            required
            defaultValue={inputName}
            onChange={(evt, data) => {
              setInputName(data?.value)
            }}
          />
          <FormDropdown
            items={['爸爸', '妈妈', '爷爷', '奶奶', '外公', '外婆', '其他']}
            search={true}
            autoSize={false}
            placeholder="选择家长称谓"
            defaultValue={selectedType}
            onChange={(evt, data) => {
              if (data.value) {
                setSelectedType(data.value as string);
              }
            }}
          />
          <FormButton content="提交" />
        </Form>
      </Segment>
    </Flex>)
}
```

我们需要提供两个API方法来实现学生配置信息的读写。为了简单起见，我将所有的API方法都写在api目录下面的 service目录下面的index.ts文件中，并且根据传入参数的call来判断是要调用什么方法。另外，这里使用了一个简单的json文件来保存数据。有兴趣请参考 `lowdb` 这个模块的官方说明。

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";


const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    const FileSync = require("lowdb/adapters/FileSync");
    const lowdb = require("lowdb");
    const adapter = new FileSync("./data.json");
    const db = lowdb(adapter);
    db.defaults({ studentSettings: [] }).write();



    //这个接口用来模拟服务调用
    let responseMessage = {};
    // 学生相关
    //1. 读取家长信息（姓名，邮箱，称谓） GET, getParentInfoById
    if (req.method.toLowerCase() === "get" && req.query.call === "getParentInfoById" && req.query.id) {
        responseMessage = db.get("studentSettings").filter({ id: req.query.id }).value()
    }

    //2. 保存家长信息（姓名，邮箱，称谓） POST,addParentInfo
    else if (req.method.toLowerCase() === "post" && req.query.call === "addParentInfo") {
        db.get("studentSettings").push(JSON.parse(req.rawBody)).write();
        responseMessage = {
            body: "保存成功"
        }
    }

    // 老师相关


    // 家长相关

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };

};

export default httpTrigger;
```

保存以上代码，如果你之前已经运行了项目，它将自动再次编译，此时尝试用一个学生的账号去打开应用，如下图所示。

![](<../.gitbook/assets/图片 438.png>)

用户可以添加一个或多个家长信息，如下所示

![](<../.gitbook/assets/图片 439.png>)

后台数据大致是下面这样的

```javascript
"studentSettings": [
  {
    "id": "4e0ea706-6806-4cd3-ad63-e42daab30b5d",
    "student": "AshleyK",
    "type": "爸爸",
    "email": "dad@code365.xyz",
    "class": "64612a03-d205-4045-ae76-7c9f6e5006b1"
  },
  {
    "id": "4e0ea706-6806-4cd3-ad63-e42daab30b5d",
    "student": "AshleyK",
    "type": "妈妈",
    "email": "mon@code365.xyz",
    "class": "64612a03-d205-4045-ae76-7c9f6e5006b1"
  }
],
```

我们这里不准备实现更加复杂的验证逻辑，以及细节的管理功能，例如删除或修改家长信息等。下面我们尝试来实现老师端的功能。

### 实现老师功能

我们为老师实现的核心功能，主要包括

1. 老师可以给班级所有家长发公告消息
2. 老师可以看到家长回复的消息（针对某个公告消息）

首先，为了能执行这些操作，我添加了几个后台服务方法，如下面所示

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { v4 } from "uuid";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    const FileSync = require("lowdb/adapters/FileSync");
    const lowdb = require("lowdb");
    const adapter = new FileSync("./data.json");
    const db = lowdb(adapter);

    db.defaults({ studentSettings: [], posts: [], comments: [] }).write();

    //这个接口用来模拟服务调用
    let responseMessage = {};
    // 学生相关
    //1. 读取家长信息（姓名，邮箱，称谓） GET, getParentInfoById
    if (req.method.toLowerCase() === "get" && req.query.call === "getParentInfoById" && req.query.id) {
        responseMessage = db.get("studentSettings").filter({ id: req.query.id }).value()
    }

    //2. 保存家长信息（姓名，邮箱，称谓） POST,addParentInfo
    else if (req.method.toLowerCase() === "post" && req.query.call === "addParentInfo") {
        db.get("studentSettings").push(JSON.parse(req.rawBody)).write();
        responseMessage = {
            body: "保存成功"
        }
    }

    // 老师相关
    //1. 发送公告消息
    else if (req.method.toLowerCase() === "post" && req.query.call === "addpost") {
        const data = JSON.parse(req.rawBody);
        db.get("posts").push({
            id: v4(),
            teacherId: data.teacherId,
            content: data.content,
            time: new Date().toLocaleString()
        }).write();

    }
    //2. 读取某个老师发布的公告列表
    else if (req.method.toLowerCase() === "get" && req.query.call === "getmyposts" && req.query.id) {
        responseMessage = db.get("posts").filter({ teacherId: req.query.id }).value();
    }

    //3. 获取某个公告的评论
    else if (req.method.toLowerCase() === "get" && req.query.call === "getcommentsbypost" && req.query.id) {
        responseMessage = db.get("comments").filter({ postId: req.query.id }).value();
    }

    // 家长相关

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };

};

export default httpTrigger;
```

在前端应用中，我为老师组件设计了如下的界面和逻辑

```jsx
function Teacher(props: { teacherId: string, teacherName: string, classId: string }) {
  const [posts, setPosts] = useState<[any]>();

  useEffect(() => {
    fetch(`api/service?call=getmyposts&id=${props.teacherId}`).then(x => x.json()).then(x => setPosts(x));
  }, [])

  // 评论组件
  const Comment = (props: { postId: string }) => {
    const [comments, setComments] = useState<[any]>();
    useEffect(() => {
      fetch(`api/service?call=getcommentsbypost&id=${props.postId}`).then(x => x.json()).then(x => setComments(x));

    }, [])

    return (<>
      {
        comments ?
          <List
            items={
              comments?.map(x => {
                return {
                  key: x.id,
                  header: x.comment,
                  content: `${x.author} 发表于 ${x.time}`
                }
              })
            }></List >
          : ""}
    </>)
  }

  return (
    <Flex column fill gap="gap.medium">
      <Segment color="brand" inverted>
        <Flex column fill gap="gap.small">
          <Text content={`你好,${props.teacherName}`} size="larger"></Text>
          <Text content="这是家校通的老师界面，在这里你可以发布公告，并且也可以查看家长的回复" size="small"></Text>
        </Flex>
      </Segment>

      <Segment>
        <h2>发布公告</h2>
        <Form
          style={{ marginLeft: 20 }}
          onSubmit={(evt) => {
            const text = (evt.target as any).txtcomment.value;
            fetch("api/service?call=addpost", {
              method: "post",
              body: JSON.stringify({
                teacherId: props.teacherId,
                content: text
              })
            }).then(_ => {
              (evt.target as any).txtcomment.value = "";
            })
          }}
        >
          <FormTextArea style={{ width: 600 }} name="txtcomment" />
          <FormButton content="发布"></FormButton>
        </Form>
      </Segment>

      <Segment>
        <h2>公告列表</h2>
        <List items={posts?.map((x: any) => {
          return {
            key: x.id,
            header: <>
              <h3>{x.content}</h3>
              <p>发布于: {x.time}</p>
            </>,
            content: <Comment postId={x.id} />
          }
        })}></List>
      </Segment>
    </Flex>
  )
}
```

这个界面效果，用一个老师的帐号登录可以看到，大致如下

![](<../.gitbook/assets/图片 441.png>)

### 实现家长功能

终于到了这个案例最有意思，也最有挑战的一个部分了，家校通主要是为了打通学校和家庭，让家长能了解到必要的信息，并且可以跟老师进行互动，而且最好是让家长用他们最方便的形式来进行操作。

目前这一版的设计，我们是假定家长没有Microsoft Teams账号，他们将使用自己的手机或电脑，通过网页的形式打开“家校通” 的主页，在主页上可以方便地了解子女的作业完成情况，还可以收到老师发出的公告，并且进行互动留言。


未来也可以考虑让家长使用免费的Microsoft Teams参与家校互动。


家长需要了解子女的作业完成情况，这个可以通过Microsoft Graph的接口实现。但这里还是要请大家注意，因为这些信息是学校和学生自己的数据，所以必须由学校授权才能开放给家长，我在本案例中，特别添加的一个环节——让学生主动添加自己家长（意味着授权家长可以查看自己的作业），则是从尊重每个用户的隐私和权利这个角度的考量。

如果我们知道一个班级的编号，我们就能查询到对应的作业信息，这个接口的文档如下

<https://docs.microsoft.com/en-us/graph/api/resources/educationassignment?view=graph-rest-beta>

> 目前这些接口出于beta阶段。


如果通过Graph Explorer这个工具，你可以用如下的查询语法来验证一下数据

```
https://graph.microsoft.com/beta/education/classes/64612a03-d205-4045-ae76-7c9f6e5006b1/assignments?$filter=status eq 'assigned'&$select=displayname,duedatetime,assigneddatetime,id,grading,instructions
```

![](<../.gitbook/assets/图片 445.png>)

那么，如何获取某个作业的已交作业呢？请参考下面这样的查询语法

```
https://graph.microsoft.com/beta/education/classes/64612a03-d205-4045-ae76-7c9f6e5006b1/assignments/b7293bdf-9534-487b-b04c-e235830fb4ce/submissions?$select=id,status,submittedBy
```

![](<../.gitbook/assets/图片 443.png>)

如果要查看某个作业，老师批改之后的打分，以及评语，请参考下面这样的查询语法

```
https://graph.microsoft.com/beta/education/classes/64612a03-d205-4045-ae76-7c9f6e5006b1/assignments/b7293bdf-9534-487b-b04c-e235830fb4ce/submissions/b8da4b62-5aba-baa4-6f50-8814fd29faea/outcomes
```

![](<../.gitbook/assets/图片 444.png>)

以上我们通过Graph Explorer来查询，是很方便的，而在家校通应用中，如果需要访问这些数据，还需要先在Azure AD中注册一个应用程序，并且申请必要的的权限。

![](<../.gitbook/assets/图片 446.png>)

点击“注册”后，为该应用程序添加一个密码

![](<../.gitbook/assets/图片 447.png>)

请注意，你需要把这个密码复制保存起来，因为你只有一次机会看到这个密码。接下来为该应用申请权限。请注意，这里需要申请“应用程序” 这个类型的权限。

![](<../.gitbook/assets/图片 448.png>)

作为管理员，你必须授予管理员同意，这样一会儿我们自己的那个服务（api/service）才可以直接访问这些接口。

最后，你需要复制应用程序编号，租户编号等信息，如下所示

![](<../.gitbook/assets/图片 449.png>)

回到VS Code中来，把上面三个信息（应用程序编号，租户编号，密码），保存在 api目录下面的.env文件中。如果该文件不存在，则新建这个文件。

![](<../.gitbook/assets/图片 450.png>)

为api 这个项目，添加如下的模块, 用来进行身份验证并且调用Graph的接口。

```
yarn add @azure/msal-node @microsoft/microsoft-graph-client isomorphic-fetch dotenv
```

在 `index.ts` 文件中，导入了如下对象

```typescript
import * as msal from "@azure/msal-node";
import * as graph from "@microsoft/microsoft-graph-client";
import "isomorphic-fetch";
```

添加了如下的一个方法

```typescript
const getGraphClient = async (): Promise<graph.Client> => {
    require("dotenv").config();
    const cca = new msal.ConfidentialClientApplication({
        auth: {
            clientId: process.env.clientId,
            clientSecret: process.env.clientSecret,
            authority: `https://login.microsoftonline.com/${process.env.tenantId}`
        }
    });

    const tokenResponse = await cca.acquireTokenByClientCredential({
        scopes: ["https://graph.microsoft.com/.default"]
    });


    const client = graph.Client.init({
        authProvider: (done) => {
            done(null, tokenResponse.accessToken);
        }
    });
    return client;
}

```

然后分别定义了三个函数来对应上面我们提到过的三个接口请求（获取作业，获取作业提交，获取作业批改数据），分别如下

```typescript
//2. 获取某个班级的所有已下发的作业
else if (req.method.toLowerCase() === "get" && req.query.call === "getassignmentsbyclass" && req.query.id) {
    const client = await getGraphClient();
    responseMessage = await client
        .api(`/education/classes/${req.query.id}/assignments?$filter=status eq 'assigned'&$select=displayname,duedatetime,assigneddatetime,id,grading,instructions`)
        .version("beta")
        .get()
}
```

这个代码分支，通过浏览器直接进行测试，可以得到如下的效果

![](<../.gitbook/assets/图片 451.png>)

下面的获取作业的提交数据

```typescript
//3. 获取某个作业的提交情况
else if (req.method.toLowerCase() === "get" && req.query.call === "getsubmissionsbyassignment" && req.query.id && req.query.class) {
    const client = await getGraphClient();
    responseMessage = await client
        .api(`/education/classes/${req.query.class}/assignments/${req.query.id}/submissions?$select=id,status,submittedBy`)
        .version("beta")
        .get();
}
```

这个代码分支，通过浏览器直接进行测试，可以得到如下的效果

![](<../.gitbook/assets/图片 452.png>)

最后是获取某个学生提交后，老师的批改信息

```typescript
//4. 获取某个学生提交后，老师的批改信息
else if (req.method.toLowerCase() === "get" && req.query.call === "getsubmissionoutcome" && req.query.id && req.query.class && req.query.assignment) {
    const client = await getGraphClient();
    responseMessage = await client
        .api(`/education/classes/${req.query.class}/assignments/${req.query.assignment}/submissions/${req.query.id}/outcomes`)
        .version("beta")
        .get();
}
```

这个代码分支，通过浏览器直接进行测试，可以得到如下的效果

![](<../.gitbook/assets/图片 453.png>)

最后，我还添加了一个方法，用来读取班级公告。

```typescript
//5. 获取某个班级的公告
else if (req.method.toLowerCase() === "get" && req.query.call === "getpostsbyclass" && req.query.id) {
    responseMessage = db.get("posts").filter({ class: req.query.id }).value();
}
```

以及根据家长邮箱获取相关孩子和班级信息

```typescript
// 1. 根据email获取孩子和班级信息
else if (req.method.toLowerCase() === "get" && req.query.call === "getParentInfoByEmail" && req.query.email) {
    responseMessage = db.get("studentSettings").filter({ email: req.query.email }).value()
}
```

现在后台的服务就全部写好了，我们来看看前端的实现吧。下面是完整实现的Parent这个组件的代码，里面包含了几个子组件，如作业列表，作业状态，公告列表，评论等。为了简单起见，我全部放在一个大的组件里面了，正常开发时，可以分多个文件来保存。

```typescript
function Parent() {
  const [parentInfo, setParentInfo] = useState<[any]>();//家长信息



  useEffect(() => {
    //默认尝试去加载localstorage里面的数据，并且从服务器刷新一次数据
    const email = localStorage.getItem("parentEmail");
    if (email) {
      fetch(`api/service?call=getParentInfoByEmail&email=${email}`).then(x => x.json()).then(x => {
        setParentInfo(x);
      });
    }
  }, []);


  // 作业列表组件
  const AssignmentList = (props: { class: string, student: string }) => {
    const [assignments, setAssignments] = useState<[any]>();//作业列表


    const AssignmentStatus = (props: { id: string, class: string, student: string }) => {
      const [status, setStatus] = useState<string>("正在检索状态");

      useEffect(() => {
        fetch(`api/service?call=getsubmissionsbyassignment&class=${props.class}&id=${props.id}&student=${props.student}`)
          .then(x => x.json())
          .then(x => {
            if (x.value && x.value.length > 0) {
              //这个学生有提交作业
              const submissionId = x.value[0].id;
              fetch(`api/service?call=getsubmissionoutcome&class=${props.class}&assignment=${props.id}&id=${submissionId}`).then(x => x.json()).then(x => {
                if (x.value && x.value.length > 0) {
                  let feedback, point;

                  x.value.forEach((item: any) => {
                    if (item.feedback) {
                      feedback = item.feedback.text.content;
                    }

                    if (item.points) {
                      point = item.points.points;
                    }
                  });

                  setStatus(`作业已提交,得分:${point},老师评语:${feedback ?? '无'}`);
                }
                else
                  setStatus("作业已提交，但老师还没有批改");
              });
            }
            else
              setStatus("当前还没有提交作业");
          })
      }, []);

      return (<div>{status}</div>)
    }
    useEffect(() => {
      fetch(`api/service?call=getassignmentsbyclass&id=${props.class}`).then(x => x.json()).then(x => setAssignments(x.value));
    }, [])
    return (<List items={
      assignments?.map(x => {
        return {
          key: x.id,
          header: <h3>{x.displayName}</h3>,
          content: <>
            <p>{`发布日期:${x.assignedDateTime},截止日期:${x.dueDateTime}, 总分:${x.grading.maxPoints}`}</p>
            <AssignmentStatus class={props.class} id={x.id} student={props.student} />
            <hr />
          </>
        }
      })
    }></List>)
  }

  //公告列表组件
  const PostList = (props: { class: string }) => {
    const [posts, setPosts] = useState<[any]>();//公告

    const Comment = (props: { postId: string }) => {
      const [comments, setComments] = useState<[any]>();
      useEffect(() => {
        fetch(`api/service?call=getcommentsbypost&id=${props.postId}`).then(x => x.json()).then(x => setComments(x));
      }, [])

      return (<>
        {
          comments ?
            <List
              items={
                comments?.map(x => {
                  return {
                    key: x.id,
                    header: x.comment,
                    content: `${x.author} 发表于 ${x.time}`
                  }
                })
              }></List >
            : ""}
        <hr /></>)
    }

    useEffect(() => {
      fetch(`api/service?call=getpostsbyclass&id=${props.class}`).then(x => x.json()).then(x => setPosts(x));
    }, [])
    return (<List items={
      posts?.map(x => {
        return {
          key: x.id,
          header: <h3>{x.content}</h3>,
          content: <Comment postId={x.id}></Comment>
        }
      })
    }></List>)
  }

  return (
    <Flex column fill gap="gap.medium">
      <Segment color="brand" inverted>
        <Flex column fill gap="gap.small">
          <Text content={`你好,家长`} size="larger"></Text>
          <Text content="这是家校通的家长界面，在这里你看到孩子的作业完成情况，以及老师下发的通知公告等" size="small"></Text>
        </Flex>
      </Segment>

      {parentInfo && <Segment>
        <List items={
          parentInfo.map(x => {
            return {
              key: x.class,
              header: `作为${x.student}的${x.type}, 班级编号:${x.class}`,
              content: <>
                <h2>作业列表</h2>
                <AssignmentList class={x.class} student={x.id}></AssignmentList>
                <h2>班级公告</h2>
                <PostList class={x.class}></PostList>
              </>
            }
          })
        }>
        </List>
      </Segment>}


      {!parentInfo && <Segment>
        <Form
          onSubmit={(evt) => {
            const email = (evt.target as any).txtEmail.value;
            //作为范例，这里目前没有做真正的验证
            fetch(`api/service?call=getParentInfoByEmail&email=${email}`).then(x => x.json()).then(x => {
              setParentInfo(x);
            }).then(_ => {
              localStorage.setItem("parentEmail", email);
            });
          }}
        >
          <FormInput name="txtEmail" placeholder="请使用邮箱登录"></FormInput>
          <FormButton content="登录" primary></FormButton>
        </Form>
      </Segment>}
    </Flex>
  )
}

```

这个页面看起来的效果是这样的，默认情况下，家长需要先用邮件进行登录，然后会根据邮箱找到子女（可能会有多个）在不同班级中的作业列表，老师发送的公告信息，并且进行展示。

![](<../.gitbook/assets/图片 454.png>)

到这里为止，我们能看到一个基本的原型，通过家校通这个应用，老师和学生、家长可以无缝地合作，共享信息等，他们用各自习惯用的工具，安全可控地分享信息，并且进行互动。

## 完整代码

这个案例的完整代码，请通过下面地址获取

<https://github.com/code365opensource/teamsapp-samples-tab-education>

这个案例主要目的是展示如何基于Teams 平台实现内部沟通，目前实现的功能很有限。但如果你的学校对这个方案感兴趣，请通过下面方式和我取得联系


[feedback-and-follow-update.md](../feedback-and-follow-update.md)

