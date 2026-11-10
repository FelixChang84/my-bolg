# 张佳轩的博客

> 把免费工具真正用起来，把想法做成能跑的东西，把踩过的坑如实写下来。

这是一个用 Hugo 搭建的个人博客，记录技术实践、自建服务教程、好用的工具，以及一些与技术无关的生活片段。

站点地址：<https://felixchang84.github.io/my-bolg/>

---

## 这个博客在写什么

大部分「关于」页都在自我介绍：我是谁、我会什么。但新访客真正想知道的往往是另一件事——这里有没有我想要的东西。所以这篇导览只回答三个问题：写什么、写给谁、从哪读起。

内容围绕三条主线：

| 主线 | 说明 |
| --- | --- |
| **把工具用起来** | 免费或开源的软件、服务，实际装一遍、跑一遍，把门槛和坑写清楚 |
| **把东西做出来** | 从一条命令到一个可交付产品的完整过程，含代码、配置和取舍 |
| **把过程记下来** | 职场、阅读、生活里值得记的事，不硬凑技术 |

这里不是资讯站，不追热点；也不是教材，不追求体系完整。每篇文章只解决一个具体问题。

## 写给谁看

- **想自建服务、但被费用和门槛劝退的人**：想有一个自己的影视站、图床或订阅提醒，看到「需要服务器、需要备案、需要绑卡」就放弃了。这里的教程基本都走免费额度路线。
- **正在学技术、需要一个完整项目练手的人**：比起只看语法，更需要一个从零到上线、能跑通的全流程。
- **被某个报错卡住、想找一条已经走通的路的人**：文章里会写清楚哪一步容易失败、失败时是什么现象。

如果你在找体系化教材或每日快讯，这里可能不太合适——这个博客更新不快，但每篇都尽量写透。

## 栏目

共五个分类，可在导航栏的「分类」页查看全部。

| 分类 | 写什么 | 代表文章 |
| --- | --- | --- |
| **技术** | 开发实践、原理拆解、完整项目 | [CAD 插件制作：从一条命令到可交付的产品](https://felixchang84.github.io/my-bolg/posts/cad-plugin-guide/) |
| **教程** | 零成本自建服务的分步操作 | [免服务器搭建个人网站：Hugo + GitHub + Cloudflare Pages](https://felixchang84.github.io/my-bolg/posts/hugo-cloudflare-deploy/) |
| **工具资源** | 工具评测、资源导航、使用边界 | [GitHub 上那 10 个免费 AI 工具：该装哪几个，会卡在哪一步](https://felixchang84.github.io/my-bolg/posts/github-free-ai-tools/) |
| **职场** | 劳动权益、职业选择 | [职场维权指南：被辞退时如何保护自己的合法权益](https://felixchang84.github.io/my-bolg/posts/workplace-rights-guide/) |
| **生活** | 阅读、随笔、小项目 | [庙堂之外：致那无名无状的月光](https://felixchang84.github.io/my-bolg/posts/miao-tang/) |

## 特色

**1. 先跑通，再动笔。**
教程里的命令、参数、配置都来自实际执行过的过程，不是从文档里抄来的片段。跑不通的地方会写明卡在哪、为什么，而不是假装一切顺利。

**2. 免费与自建优先。**
绝大多数方案基于 Cloudflare 的免费额度：不用买服务器、不用备案、不用绑卡。能白嫖的绝不劝你付费，需要花钱的地方会提前说清楚。

**3. 文章里藏着能直接玩的东西。**
有些文章不只是文字：[五子棋](https://felixchang84.github.io/my-bolg/posts/gomoku/) 可以直接下、汉字笔顺动画可以逐笔演示、家庭相册用密码保护。代码贴在文章里，也跑在页面上。

**4. 写清楚代价。**
每个工具都有边界：哪个功能要额外配置、哪一步会读你的文件、哪些数据其实不是实时的。只讲好处的推荐，等于没推荐。

## 从哪读起

- **想搭一个自己的站点** → [Hugo + GitHub + Cloudflare Pages](https://felixchang84.github.io/my-bolg/posts/hugo-cloudflare-deploy/)
- **想零成本跑点服务** → [免费图床](https://felixchang84.github.io/my-bolg/posts/cloudflare-image-hosting/) ／ [LibreTV 影视站](https://felixchang84.github.io/my-bolg/posts/libretv-deploy-guide/) ／ [SubsTracker 订阅提醒](https://felixchang84.github.io/my-bolg/posts/subs-tracker/)
- **想看一个完整项目怎么落地** → [CAD 插件制作](https://felixchang84.github.io/my-bolg/posts/cad-plugin-guide/)
- **只想随便翻翻** → [全部文章](https://felixchang84.github.io/my-bolg/posts/)

## 技术栈

- **生成器**：Hugo 0.160.1（extended）
- **主题**：hugo-coder
- **部署**：GitHub Actions 构建，发布到 GitHub Pages
- **内容**：Markdown，中文为主，`content.en/` 下提供少量英文页面

## 本地运行

```bash
# 克隆仓库
git clone https://github.com/FelixChang84/my-bolg.git
cd my-bolg

# 启动预览（默认 http://localhost:1313/my-bolg/）
hugo server

# 仅构建，产物输出到 public/
hugo
```

> `public/` 与 `resources/` 是构建产物，已在 `.gitignore` 中忽略；站点由 Actions 从源码重新构建，不需要提交它们。

## 目录结构

```
content/          中文内容（posts 文章、about 关于、projects 项目）
content.en/       英文内容
layouts/          自定义布局与短代码（video-card、secure-album）
static/           图片等静态资源
themes/           hugo-coder 主题
hugo.toml         站点配置：多语言、菜单、分类法
```

## 关于我

开发者，喜欢折腾各种工具，习惯把过程写下来。技术栈涉及 Python、C#、JavaScript，日常在 Windows 上写代码。

- GitHub：[@FelixChang84](https://github.com/FelixChang84)
- 邮箱：felix.chang84@gmail.com
- 关于页：<https://felixchang84.github.io/my-bolg/about/>

---

如果这里的内容帮你省下了一个下午，那这个博客就有存在的意义。
