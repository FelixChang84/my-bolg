---
title: "loudflare + MoonTVPlus：零成本搭建个人影视站终极指南"
date: 2026-05-07T00:00:00+13:00
draft: false
author: "自建服务"
categories: 
  - 教程
  - 自建服务
tags:
  - MoonTV
  - Cloudflare Pages
  - 影视聚合
  - 免费部署
---

# 🎬 Cloudflare + MoonTVPlus：零成本搭建个人影视站终极指南

> 📺 视频预览：
> <iframe src="//player.bilibili.com/player.html?isOutside=true&aid=116389872538913&bvid=BV1DpDSBEErp&cid=37438686916&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>
> 
> 📝 原文参考：[skilladd.org](https://skilladd.org/2026/04/11/49.Cloudflare%E6%90%AD%E5%BB%BAMoontvplus/)

---

## 📋 视频内容速览

本视频由UP主「技能加点」发布，时长约7分19秒，详细介绍了如何使用 **Cloudflare Workers + MoonTVPlus** 一键部署一个功能强大的个人影视聚合站。核心亮点包括：

✅ **零成本**：完全利用Cloudflare免费额度  
✅ **零维护**：无需服务器，部署后自动运行  
✅ **全平台**：支持网页、电视、手机多端播放  
✅ **功能丰富**：弹幕、评论、外部播放器、视频超分等增强功能

---

## 🔍 MoonTVPlus 是什么？

MoonTVPlus 是基于 MoonTV v100 二次开发的**增强版影视聚合播放器**，在原版基础上新增了以下实用功能：

| 功能 | 说明 |
|------|------|
| 🎞️ 外部播放器支持 | 可调用本地或第三方播放器，提升兼容性 |
| ✨ 视频超分 | AI增强画质，低清资源也能高清观看 |
| 💬 弹幕系统 | 支持实时弹幕互动，观影不孤单 |
| 📝 评论抓取 | 聚合多平台评论，一站式查看观影反馈 |
| 🔍 资源聚合 | 整合全网影视源，搜索即得 |

---

## 🛠️ 部署前准备（3个账号搞定）

1. **Cloudflare 账号**  
   → 用于部署 Workers 边缘服务，提供全球CDN加速

2. **GitHub 账号**  
   → 用于托管代码、配置自动化部署流程

3. **Upstash 账号**（免费256MB Redis）  
   → 用于存储用户数据、播放记录等

> ⚠️ **重要提醒**：视频作者特别建议「部署完就删库」，因为频繁API调用可能被GitHub机器人误判为异常行为，导致账号被封禁，而解封需手机号验证（不支持中国大陆及港澳台地区）😅

---

## 🚀 一键部署四步走

### 第一步：获取 Cloudflare API Token 和 Account ID
- 登录 Cloudflare 仪表板 → 头像 → **My Profile** → **API Tokens**
- 选择模板：`Edit Cloudflare Workers`
- 权限配置：
  - `Account > Cloudflare Workers Scripts > Edit`
  - `Account > D1 > Edit`（如使用D1数据库）
- 创建后**立即复制Token**（仅显示一次！）
- 在 Dashboard 首页右侧查看 **Account ID**

### 第二步：创建 Upstash Redis 数据库
- 注册登录 [Upstash](https://upstash.com)
- 创建免费 Redis 实例（256MB足够个人使用）
- 复制生成的 **REST API URL** 和 **Token**

### 第三步：配置 GitHub Secrets
进入你 Fork 的 MoonTVPlus 仓库，依次添加以下环境变量：

| Secret 名称 | 作用 | 示例 |
|-------------|------|------|
| `CLOUDFLARE_API_TOKEN` | CF API认证 | `your_api_token_here` |
| `CLOUDFLARE_ACCOUNT_ID` | CF账户标识 | `abc123def456` |
| `USERNAME` | 后台管理账号 | `admin` |
| `PASSWORD` | 后台管理密码 | `your_secure_password` |
| `NEXT_PUBLIC_STORAGE_TYPE` | 存储后端类型 | `upstash` |
| `UPSTASH_URL` | Redis连接地址 | `https://xxx.upstash.io` |
| `UPSTASH_TOKEN` | Redis访问令牌 | `your_upstash_token` |

### 第四步：触发 GitHub Actions 自动部署
- 确保仓库已启用 Actions
- 推送任意提交或手动触发 `Deploy` 工作流
- 等待约2-5分钟，部署成功后可在 Cloudflare Workers 面板查看访问域名

---

## 📱 如何使用？

### 🔗 访问你的影视站
部署成功后，你将获得一个 `*.workers.dev` 域名（或绑定自定义域名），浏览器打开即可使用。

### 📺 多端播放方案
- **网页端**：直接访问域名，支持弹幕、搜索、收藏
- **电视端**：推荐使用 **OrionTV** 或 **TiviMate** 等支持Web源的播放器，填入你的站点API地址
- **手机端**：浏览器访问或添加到主屏幕，体验近似原生App

### 🎯 视频源配置
默认使用 `test.json` 作为测试源，实际使用时可：
- 自行维护 JSON 源列表
- 接入开源影视源项目（注意版权合规）
- 通过后台动态管理资源接口

---

## ⚠️ 注意事项 & 风险提示

1. **合规使用**：请确保所聚合内容符合当地法律法规，建议仅用于学习或个人测试
2. **账号安全**：妥善保管 API Token，避免泄露；部署后建议删除敏感 Secrets
3. **流量限制**：Cloudflare 免费计划有请求次数限制，高并发可能触发限流
4. **项目维护**：开源项目可能更新或停止维护，建议定期关注上游仓库动态

---

## 💡 延伸思考：为什么这类方案火了？

随着流媒体平台会员费用上涨、内容分区限制增多，越来越多技术爱好者开始探索「自建影视站」方案。Cloudflare 的边缘计算能力 + 开源前端项目，让「零成本、零运维」成为可能。

但我们也需理性看待：
- 🔐 技术无罪，使用有责
- 🌐 尊重版权，支持正版
- 🛡️ 注意隐私，保护数据

---

## 📚 资源汇总

| 名称 | 链接 |
|------|------|
| MoonTVPlus 项目 | [GitHub 仓库](https://github.com)（搜索 MoonTVPlus） |
| Cloudflare Workers | [workers.cloudflare.com](https://workers.cloudflare.com) |
| Upstash Redis | [upstash.com](https://upstash.com) |
| 原视频教程 | [B站 BV1DpDSBEErp](https://www.bilibili.com/video/BV1DpDSBEErp) |
| 图文教程 | [skilladd.org 博客](https://skilladd.org/2026/04/11/49.Cloudflare搭建Moontvplus/) |

---

> 🎯 **小结**：Cloudflare + MoonTVPlus 是一套极具创意的前端工程实践，适合对 Web 开发、边缘计算感兴趣的朋友动手尝试。技术探索的乐趣在于过程，而合法合规、尊重创作者，才是长久享受数字生活的基石。

---
*本文内容基于公开教程整理，仅用于技术学习交流。*