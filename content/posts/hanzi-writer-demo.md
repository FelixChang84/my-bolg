---
title: "✍️ 汉字笔顺动画演示 - HanziWriter"
date: 2026-04-16T08:30:00+08:00
draft: false
tags: ["JavaScript", "汉字", "动画", "开源库"]
categories: ["技术分享"]
description: "使用 HanziWriter 实现可自定义汉字的笔顺动画和书写练习"
---

## Hanzi Writer 案例

<div style="text-align: center; margin: 20px 0;">
  <label style="margin-right: 10px;">汉字</label>
  <input type="text" id="char-input" value="我" style="padding: 8px 12px; font-size: 16px; border: 1px solid #ddd; border-radius: 4px; width: 60px; text-align: center;" maxlength="1">
  <button onclick="updateDemo()" style="margin-left: 10px; padding: 8px 16px; font-size: 14px; background: #f0f0f0; border: 1px solid #ddd; border-radius: 4px; cursor: pointer;">更新案例</button>
</div>

<hr style="border: none; border-top: 1px solid #eee; margin: 30px 0;">

### 汉字动画

<div id="animation-container" style="width: 300px; height: 300px; margin: 20px auto; border: 1px solid #ddd; padding: 10px;"></div>

<div style="text-align: center; margin: 20px 0; padding: 15px; background: #f9f9f9; border-radius: 5px; display: inline-block; width: 100%; max-width: 320px;">
  <label style="margin-right: 10px;">
    <input type="checkbox" id="show-outline-anim" checked onchange="toggleOutline()"> 展示轮廓
  </label>
  <button onclick="playAnimation()" style="margin-left: 10px; padding: 8px 20px; background: #2c7be5; color: white; border: none; border-radius: 4px; cursor: pointer;">动画!</button>
</div>

<p style="text-align: center; margin: 20px 0;">
  <a href="https://hanziwriter.org/cn/docs/animation.html" target="_blank" style="color: #2c7be5; text-decoration: none;">了解更多关于动画知识</a>
</p>

<hr style="border: none; border-top: 1px solid #eee; margin: 30px 0;">

### 描画测试

<p style="text-align: center; color: #666; margin: 20px 0;">
  通过用鼠标或手指绘制汉字 <strong>我</strong> 来测验自己的书写正确错误
</p>

<div id="quiz-container" style="width: 300px; height: 300px; margin: 20px auto; border: 1px solid #ddd; padding: 10px;"></div>

<div style="text-align: center; margin: 20px 0; padding: 15px; background: #f9f9f9; border-radius: 5px; display: inline-block; width: 100%; max-width: 320px;">
  <label style="margin-right: 10px;">
    <input type="checkbox" id="show-outline-quiz" checked onchange="toggleQuizOutline()"> 展示轮廓
  </label>
  <button onclick="resetQuiz()" style="margin-left: 10px; padding: 8px 20px; background: #2c7be5; color: white; border: none; border-radius: 4px; cursor: pointer;">重置</button>
</div>

<p style="text-align: center; margin: 20px 0;">
  <a href="https://hanziwriter.org" target="_blank" style="color: #2c7be5; text-decoration: none;">学习更多测试</a>
</p>

<!-- 引入 HanziWriter -->
<script src="https://cdn.jsdelivr.net/npm/hanziwriter@3.0.0/dist/hanziwriter.min.js"></script>

<script>
let animationWriter = null;
let quizWriter = null;
let currentChar = '我';

// 初始化
function initWriters(char) {
  currentChar = char;
  
  // 销毁旧的实例
  if (animationWriter) {
    animationWriter.destroy();
  }
  if (quizWriter) {
    quizWriter.destroy();
  }
  
  // 创建动画实例
  animationWriter = HanziWriter.create('animation-container', char, {
    width: 280,
    height: 280,
    padding: 5,
    strokeColor: '#2c7be5',
    outlineColor: '#ddd',
    showCharacter: true,
    showOutline: document.getElementById('show-outline-anim').checked,
  });
  
  // 创建测试实例
  quizWriter = HanziWriter.create('quiz-container', char, {
    width: 280,
    height: 280,
    padding: 5,
    strokeColor: '#2c7be5',
    outlineColor: '#ddd',
    showCharacter: false,
    showOutline: document.getElementById('show-outline-quiz').checked,
  });
  
  // 自动播放动画
  setTimeout(() => {
    animationWriter.animateCharacter();
  }, 500);
}

// 更新案例
function updateDemo() {
  const char = document.getElementById('char-input').value;
  if (char && char.length === 1) {
    initWriters(char);
    // 更新测试区域的提示文字
    document.querySelector('#quiz-container').previousElementSibling.innerHTML = 
      `通过用鼠标或手指绘制汉字 <strong>${char}</strong> 来测验自己的书写正确错误`;
  } else {
    alert('请输入一个汉字！');
  }
}

// 播放动画
function playAnimation() {
  if (animationWriter) {
    animationWriter.animateCharacter();
  }
}

// 切换轮廓（动画区）
function toggleOutline() {
  if (animationWriter) {
    const show = document.getElementById('show-outline-anim').checked;
    if (show) {
      animationWriter.showOutline();
    } else {
      animationWriter.hideOutline();
    }
  }
}

// 重置测试
function resetQuiz() {
  if (quizWriter) {
    quizWriter.quiz();
  }
}

// 切换轮廓（测试区）
function toggleQuizOutline() {
  if (quizWriter) {
    const show = document.getElementById('show-outline-quiz').checked;
    if (show) {
      quizWriter.showOutline();
    } else {
      quizWriter.hideOutline();
    }
  }
}

// 页面加载后初始化
window.addEventListener('load', () => {
  initWriters('我');
});
</script>