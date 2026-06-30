# 面试 AI 助手

一个单文件 HTML 验证页，用来调用 Coze 面试准备智能体。

## 功能

- 上传或粘贴招聘 JD
- 上传或粘贴个人简历
- 填写最没底的面试环节
- 支持 JD / 简历截图上传
- 先调用 Coze 文件上传接口获取 `file_id`
- 再调用 Coze Chat API 生成面试准备内容
- 支持 Markdown 结果展示

## 使用方式

1. 打开 `面试AI助手.html`
2. 填入你的 Coze PAT
3. 确认 Bot ID
4. 上传 JD、简历和填写面试环节
5. 点击“生成面试准备”

## 本地运行

可以直接双击打开 HTML，也可以用本地服务运行：

```bash
python3 -m http.server 8031
```

然后访问：

```text
http://127.0.0.1:8031/面试AI助手.html
```

## 安全说明

公开仓库不内置任何 Coze PAT。页面会把你输入的 PAT 保存在当前浏览器的 `localStorage`，仅用于本地验证。

如果要正式发布成公开产品，请把 Coze PAT 放到后端代理或 serverless function，不要放在前端。
