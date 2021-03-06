---
layout: post
title: HTML5 Canvas를 이용해서 주사위 게임 만들기
category: Javascript
tag: [javascript, html5]
---

## dicegame.html

<pre class="prettyprint">
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;title&gt;Dice Game&lt;/title&gt;

    &lt;script src="/js/dice.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;canvas id="canvas" width="400" height="200"&gt;
        이 브라우저는 HTML5 Canvas를 지원하지 않습니다.
    &lt;/canvas&gt;

    &lt;br&gt;

    &lt;button onClick="throwDices();"&gt;주사위 던지기&lt;/button&gt;

    &lt;script&gt;
        window.onload = function() {
            init();
        }
    &lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

<br>

## dice.js

<pre class="prettyprint">
var cwidth = 400;
var cheight = 300;
var dicex = 50;
var dicey = 50;
var diceWidth = 100;
var diceHeight = 100;
var dotrad = 6;
var ctx;

function init() {
    var ch = 1 + Math.floor(Math.random() * 6);
    drawFace(ch);
}

function drawFace(n) {
    ctx = document.getElementById("canvas").getContext("2d");
    ctx.lineWidth = 5;
    ctx.clearRect(dicex, dicey, diceWidth, diceHeight);
    ctx.strokeRect(dicex, dicey, diceWidth, diceHeight);
    ctx.fillStyle = "#009966";

    switch(n) {
        case 1:
            draw1();
            break;

        case 2:
            draw2();
            break;

        case 3:
            draw2();
            draw1();
            break;

        case 4:
            draw4();
            break;

        case 5:
            draw4();
            draw1();
            break;

        case 6:
            draw4();
            draw2mid();
            break;
    }
}

function draw1() {
    var dotx;
    var doty;

    ctx.beginPath();

    dotx = dicex + 0.5 * diceWidth;
    doty = dicey + 0.5 * diceHeight;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    ctx.closePath();
    ctx.fill();
}

function draw2() {
    var dotx;
    var doty;

    ctx.beginPath();

    dotx = dicex + 3 * dotrad;
    doty = dicey + 3 * dotrad;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    dotx = dicex + diceWidth - 3 * dotrad;
    doty = dicey + diceHeight - 3 * dotrad;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    ctx.closePath();
    ctx.fill();
}

function draw4() {
    var dotx;
    var doty;

    ctx.beginPath();

    dotx = dicex + 3 * dotrad;
    doty = dicey + 3 * dotrad;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    dotx = dicex + diceWidth - 3 * dotrad;
    doty = dicey + diceHeight - 3 * dotrad;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    ctx.closePath();
    ctx.fill();
    ctx.beginPath();

    dotx = dicex + 3 * dotrad;
    doty = dicey + diceHeight - 3 * dotrad;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    dotx = dicex + diceWidth - 3 * dotrad;
    doty = dicey + 3 * dotrad;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    ctx.closePath();
    ctx.fill();
}

function draw2mid() {
    var dotx;
    var doty;

    ctx.beginPath();

    dotx = dicex + 3 * dotrad;
    doty = dicey + 0.5 * diceHeight;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    dotx = dicex + diceWidth - 3 * dotrad;
    doty = dicey + 0.5 * diceHeight;
    ctx.arc(dotx, doty, dotrad, 0, Math.PI * 2, true);

    ctx.closePath();
    ctx.fill();
}

function throwDices() {
    var ch = 1 + Math.floor(Math.random() * 6);
    drawFace(ch);
}
</pre>