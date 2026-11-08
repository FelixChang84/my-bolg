---
title: "五子棋对战"
date: 2026-04-14T00:00:00+08:00
draft: false
categories: ["生活"]
tags: ["五子棋", "小游戏"]
description: "一个可以直接在网页里下的五子棋小游戏，支持人机对战。"
aliases: ["/posts/qizi/"]
---

写博客的间隙摸鱼产物：一个跑在浏览器里的五子棋，不用装任何东西，点开就能下。

<!--more-->

<iframe src="/gomoku.html" width="100%" height="700" frameborder="0"></iframe>

棋盘逻辑全部在前端用 JavaScript 实现，胜负判断按四个方向（横、竖、两条斜线）各扫一遍，凑满五子即结束。
