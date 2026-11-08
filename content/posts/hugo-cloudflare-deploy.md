---
title: "免服务器搭建个人网站：Hugo + GitHub + Cloudflare Pages"
date: 2026-04-28T08:00:00+08:00
draft: false
tags: ["Hugo", "GitHub", "Cloudflare"]
categories: ["教程"]
description: "用 Hugo 生成静态页面、GitHub 托管源码、Cloudflare Pages 自动构建部署，零服务器成本上线一个个人网站。"
aliases: ["/posts/hogo/"]
---

## 摘要

- 本地用 Hugo 生成静态页面
- 源码上传至 GitHub 托管
- 由 Cloudflare Pages 自动构建并部署
- （可选）绑定自定义域名

## 前置条件

- 已安装 Git
- 已注册 GitHub、Cloudflare 账号
- 本机可正常访问 GitHub 与 Cloudflare（国内网络环境需自备代理）

## 一、本地搭建静态页面

1. 安装 Hugo：`winget install Hugo.Hugo.Extended`。也可以手动下载 [hugo_extended_0.157.0_windows-amd64.zip](https://github.com/gohugoio/hugo/releases/download/v0.157.0/hugo_extended_0.157.0_windows-amd64.zip)，解压到合适位置，把 `hugo.exe` 所在目录加入环境变量 `PATH`，再用 `hugo version` 验证
2. 创建项目：`hugo new site pblog`
3. 进入项目目录：`cd pblog`
4. 安装主题：`git clone https://github.com/luizdepra/hugo-coder.git themes/hugo-coder`
5. 配置 `hugo.toml`，可以从主题作者提供的示例配置改起
6. 启动本地预览：`hugo server`，默认在 [http://localhost:1313/](http://localhost:1313/) 查看
7. 删掉主题自带的 Git 仓库，避免影响主仓库推送：`rmdir /s /q themes\hugo-coder\.git`
8. 运行 `hugo` 验证能否成功构建

把前几步的命令连起来：

```bash
winget install Hugo.Hugo.Extended
hugo new site pblog
cd pblog
git clone https://github.com/luizdepra/hugo-coder.git themes/hugo-coder
hugo server          # 本地预览，Ctrl+C 停止
hugo                 # 正式构建，产物在 public/
```

## 二、托管至 GitHub

1. 初始化仓库：`git init`
2. 添加文件：`git add .`
3. 首次提交：`git commit -m "Initial"`
4. 在 GitHub 上创建仓库（私有仓库也可以），关联远程地址：`git remote add origin https://github.com/<your-name>/<your-repo>.git`

5. 推送代码：`git push -u origin main`（老仓库默认分支是 master 时改用 `master`）
6. 回到仓库页面确认代码已推送成功

## 三、部署至 Cloudflare Pages

1. 进入 Cloudflare 控制台 → 构建 → Compute → Workers 和 Pages，点击创建应用程序并选择 Pages。首次使用需要绑定并授权 GitHub 账号
2. 选择要部署的仓库，框架预设选 Hugo，直接快速部署，完成后即可用 Cloudflare 提供的默认域名访问
3. 若构建失败，多半是 Hugo 版本差异，需要在项目设置里指定 `HUGO_VERSION` 环境变量

## 四、自定义域名

1. 在 Cloudflare 或阿里云等云服务商购买域名（不建议购买 .cn 域名）。若域名在 Cloudflare 之外购买，需先在 Cloudflare 添加站点，按提示修改 DNS 服务器，并为顶级域名与 www 子域名各添加一条 CNAME 记录
2. 在 Workers 和 Pages 中刚部署的项目里添加自定义域名，生效可能需要等待较长时间

## 小结

整套流程的成本是零：Hugo 负责把 Markdown 编译成静态文件，GitHub 负责存代码，Cloudflare Pages 负责在每次 push 后自动构建发布。之后写文章只需要新建一个 Markdown 文件、push 一次，站点就会自动更新。
