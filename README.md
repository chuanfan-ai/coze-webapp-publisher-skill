# Coze Web App Publisher Skill

把 Coze 上已经搭好的智能体或工作流，快速包装成一个可用的小应用、单页网站或个人网页工具。

这个仓库不是某一个具体智能体项目，而是一套可复用的 Codex skill：当用户想把 Coze bot / workflow 做成网页给别人使用时，按固定流程收集信息、学习 Playground 接口、设计页面、实现调用、验证并发布。

## 解决什么问题

- 已经在 Coze 里搭好了智能体，但别人使用不方便
- 想把 Coze 工作流做成一个网页工具
- 想先用单文件 HTML 快速验证产品体验
- 想后续发布到 GitHub Pages、Vercel 或个人网站
- 不想每次都重新摸索 Coze Playground、文件上传、流式输出、Markdown 渲染

## 开工前三件套

使用这个 skill 时，先让用户提供：

1. **Coze Playground 地址**
   - 最好是带 `bot_id` 或 `workflow_id` 的地址
   - 没有地址时，提供原始 Bot ID / Workflow ID

2. **Coze API Key / PAT**
   - 本地单 HTML 验证可以按用户要求写进前端
   - 公开发布时不能提交真实令牌

3. **智能体或工作流作用说明**
   - 可以是文字描述
   - 可以是截图
   - 可以是提示词片段
   - 可以是输出样例
   - 可以是已有页面草稿

Playground 只用来学习“接口怎么调”，不负责说明“智能体是干嘛的”。

## 工作流程

1. 理解 Coze 智能体/工作流的用途
2. 在 Playground 里确认接口调用方式
3. 判断是否需要文件上传、图片上传、流式输出、会话查询或工作流参数
4. 设计适合该能力的小应用页面
5. 先做单文件 HTML 验证
6. 校验 token、上传、调用、Markdown 展示和浏览器效果
7. 需要发布时再拆分后端代理、环境变量或部署结构

## Markdown 与富媒体渲染

Coze 输出通常是 Markdown。这个 skill 默认要求把返回内容渲染成真正的页面 UI，而不是把 `###`、`**加粗**`、表格语法或流式事件 JSON 原样显示。

推荐方案：

- 用 `marked` 解析 GFM 风格 Markdown
- 用 `DOMPurify` 清洗生成的 HTML
- 支持标题、列表、任务列表、表格、引用、代码块、链接、图片
- 对视频/音频增加媒体层：支持 `@[video](url)`、`![video](url)`，也可以把 `.mp4` / `.webm` / `.mp3` 等链接自动转成播放器
- 公开发布时仍然不能把真实 PAT 写进前端

## 仓库结构

```text
.
├── SKILL.md
├── references/
│   └── playground-checklist.md
├── assets/
│   └── single-html-template.html
└── agents/
    └── openai.yaml
```

## 安装位置

本地已安装到：

```text
/Users/chuanfan/.agents/skills/coze-html-app-builder
```

Codex 入口软链：

```text
/Users/chuanfan/.codex/skills/coze-html-app-builder
```

## 安全原则

本地验证可以使用用户提供的 PAT。公开仓库、公开网页和线上产品不能提交真实 PAT。

如果要让别人无需填 token 就能使用，需要增加后端代理或 serverless function，把 Coze token 放到服务端环境变量里。
