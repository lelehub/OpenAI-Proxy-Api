# OpenAI-Proxy-Api

## API Proxy

> ⚠️ 由于腾讯云自身规则，虽然代码本身支持SSE，但部署为云函数后可能无法正常工作
① 进入[云函数创建面板](https://console.cloud.tencent.com/scf/list-create?rid=5&ns=default&createType=empty)，选择日本/新加坡/美国(建议不要使用~~中国香港~~)、web函数、NodeJS 16。
![图 1](images/37604bf9e2e3a6c2d9bfb7295cc934f6fb2db4fae61422ac79096ef7f76f041f.png)  

② 在函数代码处点击`event.js`将本项目 [event.js](/event.js) 的代码粘贴进去。
![图 2](images/edbcec227ca630733bcf444593e4d191247ece4f56ae41553976a044fe5e0d75.png)  

其他不用改，点创建。

③ 创建完成后，点击「函数管理」→「函数代码」。等编辑器把函数代码加载完成后 CloudStudio → 终端 → 新终端，打开一个新终端。
![图 3](images/44ca1b937d365fe2923b7ee38a400e6d5ebf31f2b60b5fb1191188cd69e24f76.png)  

④ 在出现的终端中粘贴以下代码 

```bash
cd src && yarn add body-parser@1.20.2 cross-fetch@3.1.5 eventsource-parser@0.1.0 express@4.18.2 multer@1.4.5-lts.1 tencentcloud-sdk-nodejs@4.0.567 cors@2.8.5
```
![图 4](images/4e2d2ebbbfc956ef79e436d5d4ef91a6c8a204fad1bed0d545e534694270bb82.png)  

⑤ 点编辑器右上角的「部署」，等待部署完成。
![图 5](images/eb9c6c0921d9114542c21b88e58f75474af88b82c062d74c5a52bbe12c55c298.png)  

⑥ 下拉或者进入「触发管理」可以看到云函数的访问地址。
![图 7](images/d32f488da1869b30a2020687e8307a86d102b832dea3b7334ce94f57f5d7b692.png)  

⑦ 调整函数执行超时时间，默认的3s会经常超时，建议调整为30s；同时添加环境变量 `TIMEOUT`(单位为毫秒，如30000)
![图 8](images/2b12775ec3def000a1cb89cedbc49395cc4fe5e1b702125959d3de8937557a27.png)  
⑧ 如果你想绑定自己的域名，需要在「触发管理」中开启「标准API网关」，按腾讯云教程进行配置。
## Proxy的使用

使用时将 `https://api.openai.com/` 替换为该路径即可，如 `https://api.openai.com/v1/chat/completions` 替换为 `https://xxxxx.apigw.tencentcs.com/release/v1/chat/completions`

## Feishu-ChatGPT
### 1. 创建一个飞书开放平台应用，并获取到 APPID 和 Secret

访问 [开发者后台](https://open.feishu.cn/app?lang=zh-CN)，创建一个名为 **ChatGPT** 的应用，并上传应用头像。创建完成后，访问【凭证与基础信息】页面，复制 APPID 和 Secret 备用。
![图 9](images/667159c3535567448fef2b911affe8dc67b7cbcfb8785149778cbae0891fe11b.png)  

### 2. 开启机器人能力

打开应用的机器人应用功能

![图 10](images/7909aa88d14aaefe5ce12950f9393e1c994e4159b3974587c4b107d4aaa7b2ff.png)  

### 3. 访问 [AirCode](https://aircode.io/dashboard) ，创建一个新的项目

登录 [AirCode](https://aircode.io/dashboard) ，创建一个新的 Node.js v16 的项目，项目名可以根据你的需要填写，可以填写 ChatGPT

![图 11](images/664662d5922f9a502a5bf017b3724fa1a72293910998634f94fd3fcc6673f68a.png)  

### 4. 复制本项目下的 event.js 的源码内容，并粘贴到 Aircode 当中

访问[app.js](/app.js)，复制代码

![图 12](images/78cfa3b753c75117440182765d2f4cb9a33be98c59bcb30081424b9a008b36f8.png)  



并把代码粘贴到 AIrcode 默认创建的 hello.js 。然后点击顶部的 deploy ，完成第一次部署。

![图 13](images/9533ee53ff63c1c43f34d8919d3fac7496057e0af03bd41b0040c6685c05d201.png)  


部署成功后，可以在下方看到。

### 5. 安装所需依赖

这个开发过程中，我们使用了飞书开放平台官方提供的 SDK，以及 axios 来完成调用。点击页面左下角的包管理器，安装 `axios` 和 `@larksuiteoapi/node-sdk`。安装完成后，点击上方的部署，使其生效。

![图 14](images/fe095476d8e22c550c99c45af5b343aebdcb008afb2faeea4187339ab3c0b01f.png)  



### 6. 配置环境变量

接下来我们来配置环境变量，你需要配置三个环境变量 `APPID` 、`SECRET` 和 `BOTNAME`，APPID 填写你刚刚在飞书开放平台获取的 APPID，SECRET 填写你在飞书开放平台获取到的 SECRET，BOTNAME 填写你的机器人的名字。

> 配置环境变量可能会失败，可以多 deploy 几次，确保配置成功。

配置完成后，点击上方的 **Deploy** 按钮部署，使这些环境变量生效。

![图 15](images/832c74662b60bb56826763616c98ef4478f270089ccad8a9577fe010af33372f.png)  


### 7. 开启权限并配置事件

访问开放平台页面，开通如下 6 个权限：

- im:message
- im:message.group_at_msg
- im:message.group_at_msg:readonly
- im:message.p2p_msg
- im:message.p2p_msg:readonly
- im:message:send_as_bot

然后回到 AirCode ，复制函数的调用地址。
然后回到事件订阅界面，添加事件。

![图 16](images/2e44a4f3e6a53dbce106d75139c6f125e653b8c770c9d738937644d210b38c41.png)  

### 8. 发布版本，等待审核

上述这些都配置完成后，你的机器人就配置好了，接下来只需要在飞书开放平台后台找到应用发布，创建一个全新的版本并发布版本即可。

## 如何贡献？

欢迎通过 issue 提交你的想法，帮助我迭代这个项目 or 直接通过 Pull Request 来提交你的代码。发布成功后，你就可以在飞书当中体验 ChatGPT 了。


## 发布历史

### 1.0.0

- 初版发布。