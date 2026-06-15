# OpenAvatarChat-WebUI-Live2D 云服务器安装与使用说明（小白版）

## 一、项目介绍

`OpenAvatarChat-WebUI-Live2D` 是一个用于 **OpenAvatarChat** 的网页前端项目。你可以把它理解成：

- 一个运行在浏览器里的 **AI 虚拟老师 / 数字人界面**
- 页面上会显示一个 **Live2D 角色**
- 用户可以和这个角色进行文字或语音互动
- 它负责展示形象、聊天界面、音频播放，以及与后端服务通信

这个项目本身是前端，不是完整的大模型服务。根据仓库说明，它基于以下技术构建：

- Vue 3
- TypeScript
- Vite
- Electron（可打包桌面端）
- WebRTC
- WebSocket

它是 `OpenAvatarChat` 的官方 Web 前端分支版本，并额外加入了 **Live2D 渲染能力**。默认构建后就是 Live2D 模式，开箱即可显示虚拟角色。

---

## 二、这个项目能实现什么功能

结合你的使用场景：**有一台云服务器，希望尽量依赖外部 API，而不是本地重计算**，这个项目最适合做成下面这种系统：

### 1. AI 虚拟老师问答
- 学生在网页里输入问题
- 后端调用外部大模型 API 获取答案
- 页面把答案显示成“虚拟老师在回复”

### 2. Live2D 角色展示
- 页面显示一个 2D 动态老师形象
- 默认可直接使用仓库内置的 `Hiyori` 模型
- 后续也可以扩展更多模型

### 3. 语音播报
- AI 回答完成后
- 后端把文字交给外部 TTS（文字转语音）API
- 前端播放语音
- 用户会感觉是“老师在说话”

### 4. 语音提问（可选）
- 用户点击麦克风说话
- 后端调用外部 ASR（语音识别）API 转成文字
- 再交给大模型处理

### 5. 音视频 / 实时互动能力
项目本身支持：
- WebRTC 模式
- WebSocket 模式
- 数字人实时交互
- 会话管理
- 音频播放和媒体采集

### 6. 后续可扩展成专用教学助手
例如：
- 数学老师
- 英语口语老师
- 编程助教
- 校内答疑老师
- 教材知识库问答老师

---

## 三、为什么它适合你的云服务器

你之前说明了你的条件是：

- 云服务器部署
- 希望尽量使用 **外部 API**
- 避免本地跑大模型、重型语音、重型数字人推理

这种情况下，这个项目非常合适，因为它本质上是：

- **前端页面展示层**
- **和后端通信的交互层**
- **把最重的 AI 能力交给外部 API**

### 最适合放在你的云服务器上的内容
- Nginx
- OpenAvatarChat 后端
- OpenAvatarChat-WebUI-Live2D 前端
- HTTPS 证书
- WebSocket / WebRTC 信令服务
- 日志与配置文件

### 不建议一开始放在服务器上的内容
- 本地大模型推理
- 本地重型 TTS
- 本地重型 ASR
- 复杂 GPU 数字人推理系统

### 最省心的思路
把最吃资源的部分外包给第三方服务：

- 大模型：外部 LLM API
- 文字转语音：外部 TTS API
- 语音识别：外部 ASR API

这样你的服务器主要负责“页面、后端、转发和管理”，部署难度和资源压力都会小很多。

---

## 四、先理解它和后端的关系

这一点非常重要。

### 这个仓库是前端，不是完整系统
仓库 `README.md` 说明：

- 这个项目是 `OpenAvatarChat` 的 Web 前端
- 后端会把它的 `dist/` 静态文件挂载到网页路径下
- 前端启动时会调用后端接口 `/openavatarchat/initconfig` 获取初始化配置
- 前后端通过 HTTP、WebSocket、WebRTC 进行通信

也就是说：

**你不能只部署这个前端，就期待它自己完成 AI 问答。**

你还需要有：
- `OpenAvatarChat` 后端
- 外部 LLM API
- 如果要语音播报，还需要 TTS API
- 如果要语音输入，还需要 ASR API

---

## 五、Live2D 部分你需要知道什么

根据 `README.live2d.md`：

### 1. 这个分支默认已经开启 Live2D
直接执行：

```bash
pnpm run build
```

生成出来的 `dist/` 默认就是 **Live2D 模式**。

### 2. 默认模型是 Hiyori
仓库默认配置为：

- `VITE_AVATAR_TYPE=live2d`
- `VITE_LIVE2D_MODEL_URL=./live2d/hiyori/Hiyori.model3.json`

这意味着你构建完成后，默认就会看到一个可用的 Live2D 角色。

### 3. 可以增加更多 Live2D 模型
仓库提供了脚本：

```bash
scripts/download-live2d-models.sh
```

例如：

```bash
scripts/download-live2d-models.sh Haru Wanko
```

不过对于第一次部署的小白用户，建议先 **用默认 Hiyori 跑通整套流程**，不要一开始就折腾多模型。

---

## 六、推荐给小白的整体部署方案

最推荐的结构如下：

### 云服务器上部署
- OpenAvatarChat 后端
- OpenAvatarChat-WebUI-Live2D 前端构建产物
- Nginx
- HTTPS

### 外部服务提供能力
- 大模型 API：负责回答问题
- TTS API：负责把答案转成语音
- ASR API：负责把用户语音转成文字（可选）

### 用户实际体验
- 打开网页
- 看到一个 Live2D 老师
- 输入问题或说话
- AI 给出答案
- 页面播放老师语音

这就是一个很适合教学、答疑、演示的 **AI 虚拟老师网页系统**。

---

## 七、部署前的准备工作

以下以常见 Linux 云服务器为例，假设系统是 Ubuntu 22.04 或 24.04。

你需要准备：

### 1. 一台云服务器
建议：
- 能联网
- 已开放 80 和 443 端口
- 如果后端直接暴露 8282，也要开放 8282 端口

### 2. 一个域名（强烈推荐）
例如：

- `ai.yourdomain.com`

为什么推荐域名：
- 更容易配置 HTTPS
- 浏览器对麦克风、音频、WebRTC 等功能更友好
- 更适合正式访问

### 3. 代码仓库
你现在使用的是：

- 前端：`sdtanyanlan/OpenAvatarChat-WebUI-Live2D`

你还需要准备对应的：
- `OpenAvatarChat` 后端项目

### 4. 外部 API
至少准备：

#### 必需
- 一个大模型 API，用于问答

#### 推荐
- 一个 TTS API，用于语音播报

#### 可选
- 一个 ASR API，用于语音识别

---

## 八、推荐的小白接入顺序

为了提高成功率，建议你按这个顺序搭建：

### 第一阶段：先跑通页面
目标：
- 网页能打开
- 能看到 Live2D 老师

### 第二阶段：再跑通文字问答
目标：
- 输入问题
- 得到文字答案

### 第三阶段：再接 TTS
目标：
- 答案可以朗读出来

### 第四阶段：最后再接 ASR
目标：
- 用户能说话提问
- 系统识别后回答

这样最稳，不容易卡在一堆问题里。

---

## 九、安装服务器基础环境

登录你的云服务器后，先安装基础环境。

### 1. 更新系统

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. 安装常用工具

```bash
sudo apt install -y git curl wget unzip nginx
```

### 3. 安装 Node.js

这个项目使用 Vite 和 pnpm，建议安装较新的 Node.js。

例如安装 Node.js 22：

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

检查版本：

```bash
node -v
npm -v
```

### 4. 安装 pnpm

```bash
sudo npm install -g pnpm
```

检查：

```bash
pnpm -v
```

---

## 十、下载前端项目

建议放到 `/opt` 目录：

```bash
cd /opt
sudo git clone https://github.com/sdtanyanlan/OpenAvatarChat-WebUI-Live2D.git
sudo chown -R $USER:$USER /opt/OpenAvatarChat-WebUI-Live2D
cd /opt/OpenAvatarChat-WebUI-Live2D
```

---

## 十一、安装依赖

```bash
pnpm install
```

如果安装成功，说明前端编译环境基本没问题。

---

## 十二、构建前端

直接执行：

```bash
pnpm run build
```

构建完成后会生成：

- `dist/`

这个目录就是最终部署用的网页文件。

根据仓库说明，这个 `dist/`：
- 默认已经是 Live2D 模式
- 默认使用 Hiyori 模型
- 默认会尽量通过浏览器地址自动推断后端地址

---

## 十三、最推荐的部署方式：跟随后端一起部署

这是仓库文档中最推荐、也最适合小白的方式。

### 原理
把这个前端项目构建出来的 `dist/`，复制到 `OpenAvatarChat` 后端的静态前端目录中。

后端会把这些前端页面挂载出来，浏览器访问后端地址时，就能直接打开网页。

### 假设后端目录是

```bash
/opt/OpenAvatarChat
```

执行：

```bash
rm -rf /opt/OpenAvatarChat/src/service/frontend_service/frontend/dist
cp -r /opt/OpenAvatarChat-WebUI-Live2D/dist /opt/OpenAvatarChat/src/service/frontend_service/frontend/dist
```

然后启动 `OpenAvatarChat` 后端。

启动后，通常访问：

```text
https://你的域名:8282
```

或者你已经用 Nginx 做了反向代理，就直接访问：

```text
https://你的域名
```

### 这种方式的优点
- 配置最少
- 前后端同源
- 不容易碰到跨域问题
- 更适合语音、WebSocket、WebRTC 这些功能
- 小白最容易成功

---

## 十四、前端单独部署方式（可选，不推荐新手优先使用）

如果你只是想临时把前端页面单独放出来，也可以把 `dist/` 当成静态网站部署。

### 1. 复制文件到网页目录

```bash
sudo mkdir -p /var/www/openavatarchat
sudo cp -r dist/* /var/www/openavatarchat/
```

### 2. 配置 Nginx

创建配置文件：

```bash
sudo nano /etc/nginx/sites-available/openavatarchat
```

写入：

```nginx
server {
    listen 80;
    server_name your-domain.com;

    root /var/www/openavatarchat;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

启用：

```bash
sudo ln -s /etc/nginx/sites-available/openavatarchat /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### 这种方式要注意
如果前端和后端不是同一个域名、同一个来源，你通常需要自己处理：

- 前端如何找到后端
- 跨域
- WebSocket 路径
- HTTPS
- 音视频权限

所以小白阶段不建议优先选这个方式。

---

## 十五、什么时候需要写 .env

仓库说明里提到：

### 如果前端跟随后端同源部署
一般 **不需要手动写 `.env`**。

因为前端会自动根据浏览器当前访问地址推断后端地址：

- `VITE_SERVER_IP` 默认从 `location.hostname` 获取
- `VITE_SERVER_PORT` 默认从 `location.port` 获取
- `VITE_USE_SSL` 默认从 `location.protocol` 推导

### 只有这些情况才需要手动写 `.env`
- 前端独立部署
- 后端在另外一个域名或 IP
- 你要做前后端分离

示例：

```env
VITE_SERVER_IP=你的后端IP或域名
VITE_SERVER_PORT=8282
VITE_USE_SSL=true
```

### 一个非常重要的限制
仓库里明确说明：

如果 `VITE_USE_SSL=false`，出于浏览器安全限制，主机名必须是：

- `127.0.0.1`
- `localhost`

这意味着：

**远程云服务器正式访问时，基本应该使用 HTTPS。**

---

## 十六、如何让“老师把答案念出来”

这是很多小白用户最关心的部分。

完整链路通常是：

1. 用户在网页里提问
2. 后端调用大模型 API 获取文字答案
3. 后端把答案发送给 TTS API
4. TTS API 返回音频
5. 前端播放音频
6. 用户感觉像“虚拟老师在说话”

### 重点理解
这个前端项目负责的是：
- 显示老师形象
- 显示聊天界面
- 播放音频
- 与后端通信

真正做“AI 生成答案”和“生成语音”的，通常是：
- 后端
- 外部 API

所以如果你发现：
- 页面能打开
- 老师形象也有
- 但不会自动念答案

大多数情况下不是前端坏了，而是：
- TTS API 没接好
- 后端没把音频正确返回给前端
- 音频播放链路没接通

---

## 十七、如何实现“用户说话提问”

如果你还想让学生直接对着麦克风说话，通常需要：

1. 前端获取麦克风输入
2. 后端接收音频
3. 后端调用 ASR API 识别文字
4. 把识别结果发给大模型
5. 大模型返回答案
6. 再用 TTS API 生成播报音频

### 注意事项
要想让麦克风更稳定地工作，通常需要：
- HTTPS
- 浏览器授权麦克风权限
- 正确的后端配置
- 可用的 ASR 服务

---

## 十八、这个项目的主要运行模式

根据仓库文档，前端支持两种主要对话模式：

### 1. WebRTC 模式
适合更实时的音视频互动。

### 2. WebSocket 模式
适合音频/文本通信。

具体使用哪种模式，前端启动时会从后端的初始化配置接口中获取。

对于小白来说，不必一开始深究这个区别。你只需要知道：

- 前端不是自己决定全部逻辑
- 很多行为由后端返回的配置控制

---

## 十九、常见问题排查

### 1. 页面打开了，但没有老师形象
可能原因：
- `dist/` 没有正确复制到后端目录
- Live2D 资源没有一起部署
- 静态资源路径出错

### 2. 页面有老师，但不能回答问题
可能原因：
- 后端没启动
- 前端没有连上后端
- 大模型 API 没配置好

### 3. 能回答文字，但不会说话
可能原因：
- TTS API 没接
- 后端没有把 TTS 音频返回给前端
- 前端没有收到可播放音频

### 4. 麦克风不能用
可能原因：
- 没有 HTTPS
- 浏览器没有授权麦克风
- ASR 服务没配置

### 5. 前端能启动，但访问异常
可能原因：
- `.env` 地址配错
- 端口没开放
- 域名没解析好
- Nginx 代理配置不对

---

## 二十、非常适合你的推荐上线方案

如果你是第一次搭这个系统，最稳妥的路线是：

### 第一步
- 使用默认 `Hiyori` 模型
- 跑通前端页面
- 能看到 Live2D 老师

### 第二步
- 接好 OpenAvatarChat 后端
- 跑通文字聊天

### 第三步
- 接入外部 TTS API
- 实现老师语音播报

### 第四步
- 再接外部 ASR API
- 实现语音提问

### 第五步
- 后续再加知识库
- 做成课程问答老师 / 校园老师 / 助教系统

---

## 二十一、一句话总结

`OpenAvatarChat-WebUI-Live2D` 非常适合做一个“**网页里的 AI 虚拟老师**”。

如果你的限制条件是：
- 用云服务器部署
- 尽量省资源
- 尽量依赖外部 API

那么最推荐的做法就是：

- 服务器负责前端、后端、网页访问和转发
- 大模型、TTS、ASR 交给外部 API
- 先跑通默认 Hiyori 模型和文字问答
- 再逐步增加语音播报和语音提问

这样最适合小白，成功率也最高。
