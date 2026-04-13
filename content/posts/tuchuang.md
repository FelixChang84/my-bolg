---
title: "Cloudflare Pages 搭建免费图床！享受 Telegram 的无限空间"
date: 2026-04-13T00:00:00+08:00
draft: false
author: "零度博客"
categories: ["教程"]
tags: ["Cloudflare", "图床", "Telegraph", "Telegram", "免费图床"]
description: "免费图片托管解决方案，Flickr/imgur 替代品。使用 Cloudflare Pages 和 Telegraph。"
---

# Cloudflare Pages 搭建免费图床！享受 Telegram 的无限空间

**免费图片托管解决方案**，Flickr/imgur 替代品。使用 Cloudflare Pages 和 Telegraph。

> 由于原有的 Telegraph API 接口被官方关闭，需要将上传渠道切换至 Telegram Channel，请按照文档中的部署要求设置 `TG_Bot_Token` 和 `TG_Chat_ID`，否则将无法正常使用上传功能。

## 如何获取 Telegram 的 `Bot_Token` 和 `Chat_ID`

如果您还没有 Telegram 账户，请先创建一个。接着，按照以下步骤操作以获取 `BOT_TOKEN` 和 `CHAT_ID`：

1. **创建 Bot 并获取 BOT_TOKEN**  
   打开 Telegram，搜索 `@BotFather`，发送 `/newbot`，按照提示创建一个新的 Bot，BotFather 会给你一个 `BOT_TOKEN`（格式类似 `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`）。

2. **创建 Channel 并获取 CHAT_ID**  
   创建一个公开或私有的 Channel（频道），把你的 Bot 添加为管理员。  
   然后向你的 Channel 发送一条消息，打开浏览器访问以下链接（把 `BOT_TOKEN` 替换成你自己的）：

   ```
   https://api.telegram.org/bot<你的BOT_TOKEN>/getUpdates
   ```

   在返回的 JSON 中找到 `"chat":{"id":xxxxxxxxx}`，这个数字就是你的 `CHAT_ID`。

## 提前准备

你唯一需要提前准备的就是一个 **Cloudflare 账户**。

## 部署教程（简单 3 步）

1. **下载或 Fork 本仓库**（注意：目前请使用 Fork）  
   仓库地址：https://github.com/cf-pages/Telegraph-Image （或其他对应仓库）

2. 打开 **Cloudflare Dashboard** → **Pages** 管理页面 → **创建项目**  
   - 如果 Fork 了仓库，选择 **连接到 Git 提供程序**  
   - 如果下载了仓库，选择 **直接上传**

3. 按照页面提示输入项目名称，选择需要连接的 Git 仓库（或上传文件），点击 **部署站点** 即可完成部署。

### 重要环境变量配置（必须设置）

在 Cloudflare Pages 的 **设置 → 环境变量** 中添加以下变量：

- `TG_Bot_Token`：你的 Telegram Bot Token
- `TG_Chat_ID`：你的 Telegram Channel ID

部署完成后，访问你的 Pages 地址即可使用图床。

## 功能特点

- 无限图片存储（依托 Telegram 无限空间）
- 完全免费（Cloudflare 免费额度内）
- 支持自定义域名
- 简单快捷部署
- 可用于 Markdown 写作、博客配图等

---