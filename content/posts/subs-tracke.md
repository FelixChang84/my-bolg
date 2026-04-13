---
title: "Cloudflare Pages  – SubsTracker订阅管理与提醒系统"
date: 2026-04-13T00:00:00+15:00
draft: false
author: "零度博客"
categories: ["免费资源", "工具推荐", "Cloudflare"]
tags: ["SubsTracker", "Cloudflare Workers", "订阅管理", "Telegram提醒", "KV存储"]
description: "基于Cloudflare Workers的轻量级订阅管理系统，帮助您轻松跟踪各类订阅服务的到期时间，并通过Telegram发送及时提醒。"
---

# SubsTracker – 订阅管理与提醒系统（Cloudflare Workers）

SubsTracker – 订阅管理与提醒系统是基于 **Cloudflare Workers** 的轻量级订阅管理系统，帮助您轻松跟踪各类订阅服务的到期时间，并通过 Telegram 发送及时提醒。

## ✨ 特性

- 🔔 **自动提醒**：在订阅到期前自动发送 Telegram 通知
- 📊 **订阅管理**：直观的 Web 界面管理所有订阅
- 🔄 **周期计算**：智能计算循环订阅的下一个周期
- 📱 **响应式设计**：完美适配移动端和桌面设备
- ☁️ **免服务器**：基于 Cloudflare Workers，无需自建服务器
- 🔒 **安全可靠**：数据存储在 Cloudflare KV 中，安全且高效

**注意！！！！！**：在开始之前，需在 Cloudflare 控制台左侧面板依次点击：**存储和数据库 → KV → 创建**，空间名称和变量都填写 `SUBSCRIPTIONS_KV`，保存后再部署，并给 Worker 绑定键值对以及设置定时执行时间！名称必须是 `SUBSCRIPTIONS_KV`，否则添加订阅将无法保存！

## 版本更新（参考 GitHub）

（原文中此部分可根据实际更新补充）

## 🚀 部署指南

### 前提条件
- Cloudflare 账户
- Telegram Bot（用于发送通知，可选其他渠道）

### 手动部署步骤（推荐从 GitHub 获取最新代码）

1. 登录 Cloudflare，创建 Worker，粘贴项目中的 JS 代码，点击部署。
2. 创建 KV 命名空间：**SUBSCRIPTIONS_KV**。
3. 给 Worker 绑定 KV 命名空间，并设置定时触发（Cron）。
4. 打开 Worker 提供的域名，输入默认账号密码：
   - 默认用户名：`admin`
   - 默认密码：`password`（或 `admin123`，具体以代码为准）
5. 进入系统配置，修改账号密码，并配置 Telegram Bot Token 和 Chat ID 等通知信息。
6. 点击「测试通知」验证是否正常，然后即可添加订阅使用。

**推荐方式**：使用 Wrangler CLI 本地部署（GitHub 项目提供 `wrangler.toml` 和脚本）。

项目 GitHub 地址：https://github.com/wangwangit/SubsTracker

## 更多功能亮点（摘自项目）

- **智能提醒**：自定义提前提醒天数，自动续订计算
- **农历显示**：支持农历日期显示（可开关）
- **多渠道通知**：Telegram、NotifyX、Webhook、企业微信、邮件、Bark 等
- **财务追踪**：记录订阅费用、支付历史、月度/年度统计
- **响应式 + 深色模式**：移动端友好
- **原文链接**：https://www.freedidi.com/19913.html  
- **项目开源地址**：https://github.com/wangwangit/SubsTracker

**打赏支持**（可选）

感谢您的支持！

<!-- 这里是样式代码，控制图片怎么显示 -->
<style>
  .hover-card {
    position: relative;
    display: inline-block;
    cursor: pointer;
    color: #d63384; /* 文字颜色，你可以改成你喜欢的颜色 */
    border-bottom: 1px dashed #d63384; /* 给文字加个虚线下划线，提示可以悬停 */
    font-weight: bold;
  }
  .hover-card .card-img {
    visibility: hidden;
    width: 200px; /* 这里控制弹窗图片的宽度 */
    background-color: #fff;
    color: #000;
    text-align: center;
    border-radius: 8px;
    padding: 5px;
    position: absolute;
    z-index: 1;
    bottom: 125%; /* 图片出现在文字上方 */
    left: 50%;
    margin-left: -100px; /* 居中修正（宽度的一半） */
    opacity: 0;
    transition: opacity 0.3s;
    box-shadow: 0 5px 15px rgba(0,0,0,0.3); /* 阴影效果 */
  }
  .hover-card .card-img img {
    width: 100%;
    border-radius: 4px;
    display: block;
  }
  /* 鼠标放上去时显示图片 */
  .hover-card:hover .card-img {
    visibility: visible;
    opacity: 1;
  }
</style>

<!-- 这里是内容列表 -->
<ul>
  <li>
    <span class="hover-card">
      支付宝
      <span class="card-img">
        <!-- 注意：src 里的路径对应 static/images 目录 -->
        <img src="/images/alipay.jpg" alt="支付宝收款码">
      </span>
    </span>
  </li>
  <li>
    <span class="hover-card">
      微信支付
      <span class="card-img">
        <!-- 注意：src 里的路径对应 static/images 目录 -->
        <img src="/images/weixinpay.jpg" alt="微信收款码">
      </span>
    </span>
  </li>
</ul>

---