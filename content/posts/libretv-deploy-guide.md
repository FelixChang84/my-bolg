---
title: "Cloudflare Pages 免费搭建 LibreTV 在线影视聚合、播放平台！"
date: 2026-04-13T00:00:00+13:00
draft: false
author: "零度博客"
categories: 
  - 教程
  - 自建服务
tags:
  - LibreTV
  - Cloudflare Pages
  - 影视聚合
  - 免费部署
---

# Cloudflare Pages 免费搭建 LibreTV 在线影视聚合、播放平台！

## 📺 项目简介

LibreTV 是一个轻量级、免费的在线视频搜索与观看平台，提供来自多个视频源的内容搜索与播放服务。无需注册，即开即用，支持多种设备访问。项目结合了前端技术和后端代理功能，可部署在支持服务端功能的各类网站托管服务上。

## 📋 详细部署指南

### Cloudflare Pages 【官网】

1. Fork 或克隆本仓库到您的 GitHub 账户（**LibreTV 仓库链接**：https://github.com/LibreSpark/LibreTV）

2. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)，进入 **Pages** 服务

3. 点击「创建项目」，连接您的 GitHub 仓库

4. 使用以下设置：
   - **构建命令**：留空（无需构建）
   - **输出目录**：留空（默认为根目录）

5. **⚠️ 重要**：在「设置」> 「环境变量」中添加 `PASSWORD` 变量（必须设置，用于后台管理密码）

6. **可选**：在「Settings」> 「Environment Variables」中添加 `ADMINPASSWORD` 变量

7. 点击「保存并部署」

8. 对接影视接口（推荐以下接口）：

| 资源名称   | 接口地址                                      |
|------------|-----------------------------------------------|
| 饭团影视   | https://www.fantuan.tv/api.php/provide/vod/  |
| 影视工厂   | https://cj.lziapi.com/api.php/provide/vod/   |
| 七七资源   | https://www.qiqidys.com/api.php/provide/vod/ |

---

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