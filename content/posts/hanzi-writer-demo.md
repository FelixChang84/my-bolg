---
title: "✍️ 汉字笔顺动画演示"
date: 2026-04-16T08:00:00+08:00
draft: false
tags: ["JavaScript", "汉字"]
categories: ["技术分享"]
---

<div style="text-align:center;margin:20px">
  <input type="text" id="charInput" value="我" maxlength="1" style="width:60px;padding:8px;font-size:16px">
  <button onclick="updateChar()" style="padding:8px 16px;margin-left:10px">更新</button>
  <button onclick="playAnim()" style="padding:8px 16px;margin-left:10px;background:#007bff;color:#fff;border:none">播放动画</button>
</div>

<div id="animBox" style="width:300px;height:300px;margin:20px auto;border:1px solid #ddd"></div>
<div id="quizBox" style="width:300px;height:300px;margin:20px auto;border:1px solid #ddd"></div>
<div style="text-align:center">
  <button onclick="startQuiz()" style="padding:8px 16px">开始练习</button>
  <button onclick="resetQuiz()" style="padding:8px 16px;margin-left:10px">重置</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/hanziwriter@3.0.0/dist/hanziwriter.min.js"></script>
<script>
let aw,qw;
function init(c){
  if(aw)aw.destroy();if(qw)qw.destroy();
  aw=HanziWriter.create('animBox',c,{width:280,height:280,padding:10,strokeColor:'#007bff'});
  qw=HanziWriter.create('quizBox',c,{width:280,height:280,padding:10,strokeColor:'#28a745',showCharacter:false});
  setTimeout(()=>aw.animateCharacter(),300);
}
function updateChar(){
  const v=document.getElementById('charInput').value;
  if(v.length===1)init(v);else alert('输入一个汉字');
}
function playAnim(){if(aw)aw.animateCharacter();}
function startQuiz(){if(qw)qw.quiz();}
function resetQuiz(){if(qw){qw.destroy();init(document.getElementById('charInput').value);}}
window.onload=()=>init('我');
</script>