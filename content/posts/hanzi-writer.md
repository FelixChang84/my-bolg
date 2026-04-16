---
title: "✍️ 汉字笔顺动画演示"
date: 2026-04-16T08:00:00+08:00
draft: false
tags: ["JavaScript", "汉字", "动画"]
categories: ["技术分享"]
---

## 让汉字动起来

这是一款用于展示汉字笔画顺序和交互式书写练习的 JavaScript 库。

<div style="text-align:center;margin:20px">
  <input type="text" id="charInput" value="我" maxlength="1" style="padding:8px;font-size:16px;width:60px">
  <button onclick="updateChar()" style="padding:8px 16px;margin-left:10px">更新</button>
  <button onclick="playAnimation()" style="padding:8px 16px;margin-left:10px;background:#007bff;color:#fff;border:none">播放动画</button>
</div>

<div id="animationContainer" style="width:300px;height:300px;margin:20px auto;border:1px solid #ddd"></div>
<div id="quizContainer" style="width:300px;height:300px;margin:20px auto;border:1px solid #ddd"></div>

<div style="text-align:center">
  <button onclick="startQuiz()" style="padding:8px 16px">开始练习</button>
  <button onclick="resetQuiz()" style="padding:8px 16px;margin-left:10px">重置</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/hanzi-writer@3.5/dist/hanzi-writer.min.js"></script>
<script>
let animationWriter = null;
let quizWriter = null;
let currentChar = '我';
let totalMistakes = 0;
let correctStrokes = 0;

function initWriters(char) {
  if (animationWriter) animationWriter.destroy();
  if (quizWriter) quizWriter.destroy();
  
  animationWriter = HanziWriter.create('animationContainer', char, {
    width: 280, height: 280, padding: 10,
    strokeColor: '#007bff',
    outlineColor: '#ddd',
    showOutline: true,
  });
  
  quizWriter = HanziWriter.create('quizContainer', char, {
    width: 280, height: 280, padding: 10,
    strokeColor: '#28a745',
    outlineColor: '#ddd',
    showCharacter: false,
    showOutline: true,
  });
  
  currentChar = char;
  totalMistakes = 0;
  correctStrokes = 0;
  setTimeout(() => playAnimation(), 500);
}

function updateChar() {
  const char = document.getElementById('charInput').value;
  if (char && char.length === 1) {
    initWriters(char);
  } else {
    alert('请输入一个汉字！');
  }
}

function playAnimation() {
  if (animationWriter) animationWriter.animateCharacter();
}

function startQuiz() {
  if (quizWriter) {
    quizWriter.quiz({
      onCorrectStroke: () => correctStrokes++,
      onMistake: () => totalMistakes++,
    });
  }
}

function resetQuiz() {
  if (quizWriter) {
    quizWriter.destroy();
    initWriters(currentChar);
  }
}

window.addEventListener('load', () => initWriters('我'));
</script>