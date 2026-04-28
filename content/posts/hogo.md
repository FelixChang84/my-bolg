---
title: "免服务器 Hugo + Github + Cloudflare 个人网站（静态网页）部署"
date: 2026-04-28T08:00:00+08:00
draft: false
tags: ["Hugo", "Github", "Cloudflare"]
categories: ["技术分享"]
---

#### 摘要
+ 本地 hugo 搭建静态页面
+ 上传至 github 托管
+ 部署至 cloudflare page 或 cloudflare worker
+ （可选）自定义网页域名

#### 前置条件
+ Git
+ 已有 Github、Cloudflare 账号
+ 科学上网

#### 一、本地搭建静态页面
1. 安装 hugo：winget <font style="color:rgb(0, 81, 194);">install</font> Hugo.Hugo.Extended

> 手动安装：
>
> [https://github.com/gohugoio/hugo/releases/download/v0.157.0/hugo_extended_0.157.0_windows-amd64.zip](https://github.com/gohugoio/hugo/releases/download/v0.157.0/hugo_extended_0.157.0_windows-amd64.zip)，解压到合适位置，把解压后的 hugo.exe 所在路径添加到环境变量 path，用 hugo version 验证
>

2. 创建 hugo 项目：hugo new site pblog
3. 进入项目：cd pblog
4. 复制主题：git clone https://github.com/luizdepra/hugo-coder.git themes/hugo-coder
5. 配置 hugo.toml，可以使用主题作者提供的示例
6. 启动本地预览：hugo server 默认在  [http://localhost:1313/](http://localhost:1313/) 查看
7. 删掉主题库 .git：rmdir /s /q themes\hugo-coder\.git，避免影响后续推送
8. 运行 hugo 验证是否可以成功构建：hugo

#### 二、托管至 Github
1. git init
2. git add .
3. git commit -m "Initial"
4. 在 Github 上创建一个仓库，可以是私有仓库，设置本地项目 Git 远程地址到仓库：git remote add origin [https://github.com/<your-name>/<your-repo>.git](https://github.com/AlightSoulmate/hugo-demo.git)
5. git push -u origin master 或 git push -u origin main
6. 到仓库验证本地代码已推送

#### 三、部署至 Cloudflare Pages
1. 进入 Cloudflare - 构建 - Compute - Workers 和 Pages 页面，点击创建应用程序，选择 Pages。首次使用时，需要绑定和授权 Github 账号
2. 在创建应用程序页面，选择要部署的项目对应仓库，选择静态框架为 hugo，快速部署，完成后使用 Cloudflare Pages 提供的默认域名访问网页
3. 如构建失败，可能是 Hugo 版本差异，需要指定 HUGO_VERSION  环境变量

#### 四、自定义域名
1. 在 Cloudflare 或阿里云等国内云服务商购买域名，（不要购买 .cn 域名）若在 Cloudflare 外购买，需要在 Cloudflare 域名页面添加域名，按照提示修改 DNS 服务器，添加 DNS 记录，为顶级域名和 www 子域名添加两个 CNAME 记录
2. 在 Workers 和 Pages 页面刚刚部署的项目页添加自定义域名，需要等待较长时间


